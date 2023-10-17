# helm-secret-env
helm create secret environment

## clone

```
git clone https://github.com/wachira90/helm-secret-env.git mychart
```

## test

```
helm template mychart
```

## result

D:\test\example-helm>helm template mychart

```yaml
---
# Source: mychart/templates/secret.yaml     
apiVersion: v1
kind: Secret
metadata:
  name: release-name-auth
data:
  password: cGFzc3dvcmQ=
  username: cm9vdA==
---
# Source: mychart/templates/service.yaml    
apiVersion: v1
kind: Service
metadata:
  name: release-name-mychart
  labels:
    helm.sh/chart: mychart-0.1.0
    app.kubernetes.io/name: mychart
    app.kubernetes.io/instance: release-name
    app.kubernetes.io/version: "1.16.0"     
    app.kubernetes.io/managed-by: Helm      
spec:
  type: ClusterIP
  ports:
    - port: 80
      targetPort: http
      protocol: TCP
      name: http
  selector:
    app.kubernetes.io/name: mychart
    app.kubernetes.io/instance: release-name
---
# Source: mychart/templates/deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: release-name-mychart
  labels:
    helm.sh/chart: mychart-0.1.0
    app.kubernetes.io/name: mychart
    app.kubernetes.io/instance: release-name
    app.kubernetes.io/version: "1.16.0"
    app.kubernetes.io/managed-by: Helm
spec:
  replicas: 1
  selector:
    matchLabels:
      app.kubernetes.io/name: mychart
      app.kubernetes.io/instance: release-name
  template:
    metadata:
      labels:
        app.kubernetes.io/name: mychart
        app.kubernetes.io/instance: release-name
    spec:
      serviceAccountName: default
      securityContext:
        {}
      containers:
        - name: mychart
          securityContext:
            {}
          image: "nginx:1.16.0"
          imagePullPolicy: IfNotPresent
          env:
            - name: "USERNAME"
              valueFrom:
                secretKeyRef:
                  key:  username
                  name: release-name-auth
            - name: "PASSWORD"
              valueFrom:
                secretKeyRef:
                  key:  password
                  name: release-name-auth
          ports:
            - name: http
              containerPort: 80
              protocol: TCP
          livenessProbe:
            httpGet:
              path: /
              port: http
          readinessProbe:
            httpGet:
              path: /
              port: http
          resources:
            {}
```
