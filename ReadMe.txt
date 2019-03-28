
Helm installation:

kubectl create -f rbac-config.yaml

helm init --service-account tiller

helm delete helm-nginx --purge

Manual deployment:
kubectl create -f deployment.yml
kubectl create -f service.yml


Chart development:

helm create nginx-helm

rm -rf nginx-helm/templates/*.*
rm -rf nginx-helm/templates/tests
delete contents of values and chart.yaml files

helm install --debug --dry-run ./nginx-helm/
helm install --debug --dry-run ./nginx-helm/ --name mynginx-helm

helm delete mynginx-helm --purge

Copy below into values.yaml

replicaCount: 1
ports:
  internal: 80
  external: 8080
image:
  name: nginx
  tag: 1.7.9

modify deployment.yaml

from:
  replicas: 2
to:
  replicas: {{ .Values.replicaCount }}

from:
- name: nginx
  image: nginx:1.7.9
  ports:
  - containerPort: 80
to:
- name: {{ .Values.image.name }}
  image: {{ .Values.image.name }}:{{ .Values.image.tag }}
  ports:
  - containerPort: {{ .Values.ports.internal }}

modify service.yaml

from:

port: {{ .Values.ports.external }}
targetPort: {{ .Values.ports.internal }}

to:

port: {{ .Values.ports.external }}
targetPort: {{ .Values.ports.internal }}
