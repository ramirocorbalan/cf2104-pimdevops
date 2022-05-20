# creacion de cluster eks

eksctl create cluster --name eks-mundos-e --region us-east-2 --node-type t2.small --with-oidc --ssh-access --ssh-public-key devops-workstation --managed --full-ecr-access --zones us-east-2a,us-east-2b,us-east-2c

# usuario para atregar al kubectl

mapUsers: - userarn: arn:aws:iam::279445133194:user/ramiromundose
username: ramiromundose
groups: - system:masters

# para ver la configuracion actual

kubectl describe configmap -n kube-system aws-auth

# para editar la configuracion Agregar usuario IAM de AWS

eksctl get iamidentitymapping --cluster eks-mundos-e --region=us-east-2

eksctl create iamidentitymapping --cluster eks-mundos-e --region=us-east-2 --arn arn:aws:iam::279445133194:user/ramiromundose --group system:masters --no-duplicate-arns

# Ahora se trabaja con AzDO, se configura una service account para poder conectarnos al cluster de aws

# para crear una cuenta nos movemos dentro de la carpeta del proyecto que tiene el yaml asociado

kubectl apply -f ado-admin-service-account.yaml

# Obtener secret asociado

kubectl get serviceAccounts ado -n kube-system -o=jsonpath={.secrets[*].name}

# secret para azdo

ado-token-v24bj

# para configurar mi secret para azdo, con este comando obtenemos el json asociado a la cuenta para luego configurar la service connection del AzDO

kubectl get secret ado-token-v24bj -n kube-system -o json

# json de la service account ado

{
"apiVersion": "v1",
"data": {
"ca.crt": "LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUM1ekNDQWMrZ0F3SUJBZ0lCQURBTkJna3Foa2lHOXcwQkFRc0ZBREFWTVJNd0VRWURWUVFERXdwcmRXSmwKY201bGRHVnpNQjRYRFRJeU1EVXhPVEUwTXpnek5Wb1hEVE15TURVeE5qRTBNemd6TlZvd0ZURVRNQkVHQTFVRQpBeE1LYTNWaVpYSnVaWFJsY3pDQ0FTSXdEUVlKS29aSWh2Y05BUUVCQlFBRGdnRVBBRENDQVFvQ2dnRUJBTU96CnhTSG1VVEU3aHhsUGYraEhBT1g4V0VqYVBUbWtsUVZIZXlUenZWQ2tWZ20vSm4yZVp0YnNHMXFnL3RudDFxd3gKazhrRlBVTUNxbS9teHJCZy9xT2VoTU9nRFF4TGtRckx0ZGx0TzVRWWJESDBkRGFaUS94OExiQkJob205UkM0bApqTkNubjBveWRqQ1pmWnlNV01id0pFaCtXWnF2NnFxNHhDdUUvL1hYUnExQ1h0Ym1tOXN2alJKS0V1RmdzZUllClpITmd4QmxEby96SW13b1A1MTFkMDlUQ1p5NDZCMFJML2orbnBCWGp4SXhVRWRZZVpNdU5RYlluK3g2RWg5ckYKMTd4bGtKQkNPa1R5SXRHa3lISFp5Rzc3NHBTa0V3ajdkeUtIWkFmK0NjQzVRU0pNZHRiNU9hVWJoc1Eya1FCVwpxMktNWWNaS2R4OFhEaXZLa3NjQ0F3RUFBYU5DTUVBd0RnWURWUjBQQVFIL0JBUURBZ0trTUE4R0ExVWRFd0VCCi93UUZNQU1CQWY4d0hRWURWUjBPQkJZRUZHTFkrN2hTU2k4N2Fod2JNVjEvNlVUREh0M2JNQTBHQ1NxR1NJYjMKRFFFQkN3VUFBNElCQVFDQmpaWU42bXozRlBsdTd0YmVyb3ZwOFRNSGRPVmg5NnpPemtuZEdNSXROMDlGbWlxbQplM1pVb2d0cmZRM0xoNUluaGo2enM3bXFvNmNRM2F2SHYyZS9OL0xZSGN5eDhuSVhESGNpT0QvUlJxUTNuMGIzCnF3RUgxNVdrSGJXN0txRmoyaVRlbFhuL0htN0hTMDJwKzhGc0F4ZGtWOU9QV3k4VHB1dVQxQmlOUzlXU3J6bWoKdWpMTmhWaC9PNnNVbXJ3TTJRL0txNGtSai9GbGEwVnNnS3VpTnl5cVNRK2orSEtNUGhRVHFpVXJ0bnF6eTdDawo0SnVoTjNaZjRsSVhMTVhkbnZTRmh6VitHT3BNbjlUUXdEUWlnVTh4ME02ZzMvOWs1RlQ5dG9CTm04ajhkL1BvClFzblFzcDRFbVB6L0QraG5mb2NseDVmek1WTnNMaFV2c0VCbAotLS0tLUVORCBDRVJUSUZJQ0FURS0tLS0tCg==",
"namespace": "a3ViZS1zeXN0ZW0=",
"token": "ZXlKaGJHY2lPaUpTVXpJMU5pSXNJbXRwWkNJNklqQnFiMnhhZWpSUmRtWjZjalkyVEVsVFltMUViRmRyTUZOMFEwaG9hVlpmT1RsSGJrbFhaV2d5TFhjaWZRLmV5SnBjM01pT2lKcmRXSmxjbTVsZEdWekwzTmxjblpwWTJWaFkyTnZkVzUwSWl3aWEzVmlaWEp1WlhSbGN5NXBieTl6WlhKMmFXTmxZV05qYjNWdWRDOXVZVzFsYzNCaFkyVWlPaUpyZFdKbExYTjVjM1JsYlNJc0ltdDFZbVZ5Ym1WMFpYTXVhVzh2YzJWeWRtbGpaV0ZqWTI5MWJuUXZjMlZqY21WMExtNWhiV1VpT2lKaFpHOHRkRzlyWlc0dGRqSTBZbW9pTENKcmRXSmxjbTVsZEdWekxtbHZMM05sY25acFkyVmhZMk52ZFc1MEwzTmxjblpwWTJVdFlXTmpiM1Z1ZEM1dVlXMWxJam9pWVdSdklpd2lhM1ZpWlhKdVpYUmxjeTVwYnk5elpYSjJhV05sWVdOamIzVnVkQzl6WlhKMmFXTmxMV0ZqWTI5MWJuUXVkV2xrSWpvaU9HWXdNVGcxTXpjdE16VmpOQzAwTmpKbExUa3pPV0V0T1RBek9EYzVOR0poTVRjNElpd2ljM1ZpSWpvaWMzbHpkR1Z0T25ObGNuWnBZMlZoWTJOdmRXNTBPbXQxWW1VdGMzbHpkR1Z0T21Ga2J5SjkuUmpYYmpXSDZHMGlzM2Q5TGRLZHNUcnhQV2h6VDFjMDM1bEttbTNldU5VUXp6WjJZY2dZdW84dWVfaThQQldyUTJ2SGFoVWNCNzRpZml1S0tROF9CMVZrN1dDVW9KSFQzc2VJa29SU0YzY1RYOHpJcUFLZmNmN2dSTEZRSTJSZnRPTDA2ZzJLS2trc1NkNGhsVXVNRWdQSVBsOHh3Y18tbGpEMExEMWR6T1htWEwzMGtBS2l1ejRETmo3QkQ2cEtVQXVzQTRFNVBCOTlmbDh2LTFjWHl4ZlJOUG42clkyMnRPRkdScjZJazZWREtLNWZrelhRTDUxMl81UnJMOUF3QXUwYU5fenN6N0M0WW80VThUQTdWVG5saVlhSTR6d29qVnBkZ1dMRjZwSFlSTVMwS1QwQXRzdDNKV3FCemgwZzRJZUtWY3lDOVp6bHpqR1N4RkNYM0dB"
},
"kind": "Secret",
"metadata": {
"annotations": {
"kubernetes.io/service-account.name": "ado",
"kubernetes.io/service-account.uid": "8f018537-35c4-462e-939a-9038794ba178"
},
"creationTimestamp": "2022-05-19T15:15:38Z",
"name": "ado-token-v24bj",
"namespace": "kube-system",
"resourceVersion": "5481",
"uid": "2b69e0aa-c4fa-4ecf-8e3d-e70ae2d845d3"
},
"type": "kubernetes.io/service-account-token"
}

# Obtener API URL para la service account

# Desde la consola de la instancia de EC2

kubectl cluster-info | grep -E 'Kubernetes master|Kubernetes control plane' | awk '/http/ {print $NF}'

https://A8E8B99E0831C191A5D0108A67ABE98D.gr7.us-east-2.eks.amazonaws.com

# deploy del contenedor nginx
# Se realiza desde AzDO mediante un pipeline

http://a9c8a75593b18428a861050508f2812b-1628979674.us-east-2.elb.amazonaws.com

###### Configurar variables de entorno

export AWS_REGION='us-east-2'
export ACCOUNT_ID=279445133194
export ES_DOMAIN_NAME="eksworkshop-logging"

# Definir el nombre de dominio para el cluster de Elasticsearch

export ES_DOMAIN_NAME="eksworkshop-logging"

# Elasticsearch version

export ES_VERSION="7.4"

# kibana admin user

export ES_DOMAIN_USER="eksworkshop"

# kibana admin password

export ES_DOMAIN_PASSWORD="$(openssl rand -base64 12)_Ek1$"

# Configurar OpenID Connect

eksctl utils associate-iam-oidc-provider --cluster eks-mundos-e --approve

# Crear policy

aws iam create-policy --policy-name fluent-bit-policy --policy-document file:fluent-bit-policy.json

# Crear namespace de logging

kubectl create namespace logging

# Crear cuenta de servicio

eksctl create iamserviceaccount --name fluent-bit --namespace logging --cluster eks-mundos-e --attach-policy-arn "arn:aws:iam::279445133194:policy/fluent-bit-policy" --approve --override-existing-serviceaccounts

# Crear dominio de elasticsearch

# ojo que el json que baja tiene algunos parametros mal

curl -sS https://www.eksworkshop.com/intermediate/230_logging/deploy.files/es_domain.json | envsubst > ~/environment/logging/es_domain.json

aws es create-elasticsearch-domain --cli-input-json file://~/environment/logging/es_domain.json

# para crear el domain de elastic se tuvieron que cambiar algunas opciones del archivo es_domain.json

# para ver si termino de crearse el dominio

if [ $(aws es describe-elasticsearch-domain --domain-name eksworkshop-logging --query 'DomainStatus.Processing') == "false" ]
then
tput setaf 2; echo "The Elasticsearch cluster is ready"
else
tput setaf 1;echo "The Elasticsearch cluster is NOT ready"
fi

# We need to retrieve the Fluent Bit Role ARN

export FLUENTBIT_ROLE=$(eksctl get iamserviceaccount --cluster eks-mundos-e --namespace logging -o json | jq '.[].status.roleARN' -r)

# Get the Elasticsearch Endpoint

export ES_ENDPOINT=$(aws es describe-elasticsearch-domain --domain-name ${ES_DOMAIN_NAME} --output text --query "DomainStatus.Endpoint")

# Update the Elasticsearch internal database

curl -sS -u "${ES_DOMAIN_USER}:${ES_DOMAIN_PASSWORD}" \
    -X PATCH \
    https://${ES_ENDPOINT}/_opendistro/_security/api/rolesmapping/all_access?pretty \
    -H 'Content-Type: application/json' \
    -d'
[
  {
    "op": "add", "path": "/backend_roles", "value": ["'${FLUENTBIT_ROLE}'"]
  }
]
'

# Desplegamos contenedor de fluentbit

kubectl apply -f ~/environment/logging/fluentbit.yaml

# Obtenemos la informacion de kibana

echo "Kibana URL: https://${ES_ENDPOINT}/\_plugin/kibana/
Kibana user: ${ES_DOMAIN_USER}
Kibana password: ${ES_DOMAIN_PASSWORD}"

Kibana URL: https://search-eksworkshop-logging-wxy7apc3i252g2chpzmfuuzr6q.us-east-2.es.amazonaws.com/_plugin/kibana/
Kibana user: eksworkshop
Kibana password: 7KR20Z6kUEHE2F96_Ek1$

# Borrado de ambiente

# Borrar fluentbit y elastic

kubectl delete -f ~/environment/logging/fluentbit.yaml

aws es delete-elasticsearch-domain --domain-name ${ES_DOMAIN_NAME}

eksctl delete iamserviceaccount --name fluent-bit --namespace logging --cluster eks-mundos-e --wait

aws iam delete-policy --policy-arn "arn:aws:iam::${ACCOUNT_ID}:policy/fluent-bit-policy"

kubectl delete namespace logging

unset ES_DOMAIN_NAME
unset ES_VERSION
unset ES_DOMAIN_USER
unset ES_DOMAIN_PASSWORD
unset FLUENTBIT_ROLE
unset ES_ENDPOINT

# Borrar Prometheus y Grafana

helm uninstall prometheus --namespace prometheus

kubectl delete ns prometheus
helm uninstall grafana --namespace grafana
kubectl delete ns grafana
rm -rf ${HOME}/environment/grafana

# Borrar Cluster EKS

eksctl delete cluster --name eks-mundos-e
