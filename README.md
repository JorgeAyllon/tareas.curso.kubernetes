# Respuestas de la Tarea 1 (Jorge Ayllon Carrasco)
1. gh repo clone jgenso/NodeExpressMongoDBDockerApp   
2. kubectl create namespace jorge-ayllon-carrasco 
3. docker build -f node.dockerfile -t jorge-ayllon/example-node-express-mongodb-app .
4. secretos-para-mongo.yaml                                                                                           
apiVersion: v1
data:
  password: TWkuUGFzc3dvcmQuNjdkZg== #Mi.Password.67df
  username: Sm9yZ2VBeWxsb25BZG1pbg== #JorgeAyllonAdmin
kind: Secret
metadata:
  creationTimestamp: null
  name: mongo-creds

5.
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: mongo
  name: mongo
spec:
  replicas: 1
  selector:
    matchLabels:
      app: mongo
  strategy: {}
  template:
    metadata:
      labels:
        app: mongo
    spec:
      containers:
      - image: mongo
        name: mongo
        args: ["--dbpath","/data/db"]
        livenessProbe:
          exec:
            command:
              - mongo
              - --disableImplicitSessions
              - --eval
              - "db.adminCommand('ping')"
          initialDelaySeconds: 30
          periodSeconds: 10
          timeoutSeconds: 5
          successThreshold: 1
          failureThreshold: 6
        readinessProbe:
          exec:
            command:
              - mongo
              - --disableImplicitSessions
              - --eval
              - "db.adminCommand('ping')"
          initialDelaySeconds: 30
          periodSeconds: 10
          timeoutSeconds: 5
          successThreshold: 1
          failureThreshold: 6
        env:
        - name: MONGO_INITDB_ROOT_USERNAME
          valueFrom:
            secretKeyRef:
              name: mongo-creds
              key: username
        - name: MONGO_INITDB_ROOT_PASSWORD
          valueFrom:
            secretKeyRef:
              name: mongo-creds
              key: password

6.
apiVersion: v1
kind: ConfigMap
metadata:
  name: scoutconfig
data:
  config.py: |
    MONGO_HOST="mongodb-service"
    MONGO_PORT= 27017
    MONGO_DBNAME= "scout-demo"
    MONGO_USERNAME= "scoutUser"
    MONGO_PASSWORD= "scoutPassword"
    
7. Escribir un Service para que Mongo esté disponible vía DNS para otros pods
apiVersion: v1
kind: Service
metadata:
  labels:
    app: mongo
  name: mongo-nodeport-svc
spec:
  ports:
  - port: 27017
    protocol: TCP
    targetPort: 27017
    nodePort: 32000
  selector:
    app: mongo
  type: NodePort

El acceso desde los otros pods se realiza mediante:
mongo.default.svc.cluster.local
8.
9.
