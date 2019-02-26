# Helm image

This image contains both, [`helm`](https://docs.helm.sh/) and [`kubectl`](https://kubernetes.io/docs/reference/kubectl/kubectl/), tools.

## Build

        docker build -t $IMAGE .

## Run

### `helm`

        docker run --rm $IMAGE [options]

### `kubectl`

        docker run --rm --entrypoint "" $IMAGE kubectl [options]

## Jenkins pipeline

Use the following Pod template for Jenkins agent (replacing $IMAGE with actual image URL)

```
apiVersion: v1
kind: Pod
spec:
  containers:
    - name: helm
      image: '$IMAGE'
      imagePullPolicy: IfNotPresent
      command:
        - /bin/cat
      tty: true
```

Example pipeline

```
pipeline {
    agent none
    stages {
        stage('Deploy using Helm') {
            agent {
                kubernetes {
                    cloud 'openshift'
                    label 'helm'
                    yaml """
apiVersion: v1
kind: Pod
spec:
  containers:
    - name: helm
      image: '$IMAGE'
      imagePullPolicy: IfNotPresent
      command:
        - /bin/cat
      tty: true
"""
                }
            }
            steps {
                container('helm') {
                    sh 'helm version'
                }
            }
        }
    }
}
```
