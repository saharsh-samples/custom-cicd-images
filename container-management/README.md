# Container Management image

This image contains following tools used for managing containers and container images.

- [`buildah`](https://github.com/containers/buildah)
- [`skopeo`](https://github.com/containers/skopeo)

## Build

        docker build -t $IMAGE .

## Run

### `buildah`

        docker run --rm $IMAGE buildah [options]

### `skopeo`

        docker run --rm $IMAGE skopeo [options]

## Jenkins pipeline

Use the following Pod template for Jenkins agent (replacing $IMAGE with actual image URL)

```
apiVersion: v1
kind: Pod
spec:
  containers:
      - name: container-management
        image: '$IMAGE'
        imagePullPolicy: IfNotPresent
        command:
          - /bin/cat
        tty: true
        securityContext:
          privileged: true
        volumeMounts:
          - mountPath: /var/lib/containers
            name: container-storage
    volumes:
      - name: container-storage
        emptyDir: {}
```

Example pipeline

```
pipeline {
    agent none
    stages {
        stage('Build container image') {
            agent {
                kubernetes {
                    cloud 'openshift'
                    label 'buildah'
                    yaml """
apiVersion: v1
kind: Pod
spec:
  containers:
      - name: buildah
        image: '$IMAGE'
        imagePullPolicy: IfNotPresent
        command:
          - /bin/cat
        tty: true
        securityContext:
          privileged: true
        volumeMounts:
          - mountPath: /var/lib/containers
            name: buildah-storage
    volumes:
      - name: buildah-storage
        emptyDir: {}
"""
                }
            }
            steps {
                container('buildah') {
                    sh 'buildah bud -t myimage:tag source-dir'
                }
            }
        }
    }
}
```
