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

ado-token-7k45r

# para configurar mi secret para azdo, con este comando obtenemos el json asociado a la cuenta para luego configurar la service connection del AzDO

kubectl get secret ado-token-7k45r -n kube-system -o json

# json de la service account ado

{
    "apiVersion": "v1",
    "data": {
        "ca.crt": "LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUM1ekNDQWMrZ0F3SUJBZ0lCQURBTkJna3Foa2lHOXcwQkFRc0ZBREFWTVJNd0VRWURWUVFERXdwcmRXSmwKY201bGRHVnpNQjRYRFRJeU1EVXlNREU0TURJMU4xb1hEVE15TURVeE56RTRNREkxTjFvd0ZURVRNQkVHQTFVRQpBeE1LYTNWaVpYSnVaWFJsY3pDQ0FTSXdEUVlKS29aSWh2Y05BUUVCQlFBRGdnRVBBRENDQVFvQ2dnRUJBTU9XCkw4Z2xqT09pelJvVzFYV3VKR3BJTnAzdzcwelJXREdraGxBMkM3K3hEQlBoRTRmTFh3ajYvWHN4ekI2cHYrZmYKaWhxN0hXQVVORFVVNjM3UlJQWXpMeWNmdGhPOE9lTnRCNTdMVFp2MkZNZTBWaHR5ZHVydGJkZy9uTEVCczd3RApNNWZSWkI1SmNOMHZUNjZqMC9rYU1WTURMR0JYbHBGUlB2Z3hFa2lrVkh0MkdKNVZrS1BPT0dGMHNNK1A1N28vCllNSHJXWGNoWW1QdVRDeVZpUGkwRDdSaHhSNlFVdEp0YVl2L3dUZm42T0Q4VGQ2cDlkeU1sTlZnbHFvMEw2UFoKOXpSWWI3QTRkYTBOc1QycXltenRBR2cxMXUybzRBT05Md1ZCNXpyZDJFM2dPWXlrT1ljemdWdExqMElZNFJmTwpVcElvWHJGckY1cU4yMDdQRkVzQ0F3RUFBYU5DTUVBd0RnWURWUjBQQVFIL0JBUURBZ0trTUE4R0ExVWRFd0VCCi93UUZNQU1CQWY4d0hRWURWUjBPQkJZRUZLR1Q2RDlNdWppUFpHd1JwblZXZUhRb0xwdmdNQTBHQ1NxR1NJYjMKRFFFQkN3VUFBNElCQVFBTzhTS0ZTNFR1V2JzblM3aTlxNHEzTFpWZkkzY09qUFFqVkg3bUh6VEhyVE1UOENHQwpKVzBNNTlDbWE3aFA0dUg0OGJiVzN4aXZxVXErNEJEb3VNNDJhTFFjYXI1SDNCMUZocDBsbHRoc2xJNGh3U0FUCjRhTWFaeG1HQ2NVRUV2Q1lWellxVTV4M0tVUW5uaEpEdGozaWgxN0htRGZXRnV4Qmp6MmhTYk0zdUVua1F0V1oKeW9oSm9KUTQwRzBaUFE1ZjBwQTBFOUZUUG8zbmlyaVRBNDJMekhzZk45WnJkVjBWNGJtWW5jbFJzOHNpbDl1Twp4SDRpK2FQQVArdGY0Mzh4dzNuclJsM2FmKzQ1OUlwT3ZaZS81eTFvdXptckwraWJQMEVsSUo1MWVNM3ZFNU1CClQ3SHEybjhxTFBJaElwSERUbXBPdU0xa2oxNDdvNlBCWnc3NwotLS0tLUVORCBDRVJUSUZJQ0FURS0tLS0tCg==",
        "namespace": "a3ViZS1zeXN0ZW0=",
        "token": "ZXlKaGJHY2lPaUpTVXpJMU5pSXNJbXRwWkNJNklqbE5PVlp3ZUV0VlMybFJRMHBOZUdKMlVGRm5OWGd6UlcwMFRUaEpPRGh3YkhKT1IxUjRkVlJJZERRaWZRLmV5SnBjM01pT2lKcmRXSmxjbTVsZEdWekwzTmxjblpwWTJWaFkyTnZkVzUwSWl3aWEzVmlaWEp1WlhSbGN5NXBieTl6WlhKMmFXTmxZV05qYjNWdWRDOXVZVzFsYzNCaFkyVWlPaUpyZFdKbExYTjVjM1JsYlNJc0ltdDFZbVZ5Ym1WMFpYTXVhVzh2YzJWeWRtbGpaV0ZqWTI5MWJuUXZjMlZqY21WMExtNWhiV1VpT2lKaFpHOHRkRzlyWlc0dE4yczBOWElpTENKcmRXSmxjbTVsZEdWekxtbHZMM05sY25acFkyVmhZMk52ZFc1MEwzTmxjblpwWTJVdFlXTmpiM1Z1ZEM1dVlXMWxJam9pWVdSdklpd2lhM1ZpWlhKdVpYUmxjeTVwYnk5elpYSjJhV05sWVdOamIzVnVkQzl6WlhKMmFXTmxMV0ZqWTI5MWJuUXVkV2xrSWpvaVpqWXhZVE0zTldZdE9XTTBPUzAwWTJKakxXRXhNemt0TXpoa1lXWXdOMlUwWmpkaUlpd2ljM1ZpSWpvaWMzbHpkR1Z0T25ObGNuWnBZMlZoWTJOdmRXNTBPbXQxWW1VdGMzbHpkR1Z0T21Ga2J5SjkucTJjbWhXaXRmZk9xZUlua0FBdmdmeXdoVkdGcmZ0ZkZaelh2VVdra0lBbWZJMEFaaGNXbExIYS00dGtJR1g3c0VZbUZBNl9rZzQ1U2dxZlJITUpxajBPMUdZTkM4REVzUDBzVW51cHo3dHFQZEJMZnEyVFVlSEwtTkFsVGluTDFRTThOb1BnWi1ZcmtXQ3BVQVpfaDlhZVdnenlMR2FRNXl0SG1HdFlWTS1SNDI4MFZDRTlwRGRGUkRrUHBYS2JLRVdGNWdWVkZBQVlQejk0cUhrb2MzTE5YOWoyQzBPZU5zMmYtRFBFQ1lKeVhzYTBYcm9sUEhHalRjODZHVUptWTN2Vlo5Z0RRZ0ZRYWM5eG5hWFJxRk1kdHFCOERMSUJEZm93NlExLThOMmVIeDFhRExMR3lFdVBtZU1CQnhjb1NBbzJaVFVBTnBrc2d5T1ptdFBqc0dB"
    },
    "kind": "Secret",
    "metadata": {
        "annotations": {
            "kubernetes.io/service-account.name": "ado",
            "kubernetes.io/service-account.uid": "f61a375f-9c49-4cbc-a139-38daf07e4f7b"
        },
        "creationTimestamp": "2022-05-20T18:21:40Z",
        "name": "ado-token-7k45r",
        "namespace": "kube-system",
        "resourceVersion": "2980",
        "uid": "a0c52b18-6bc6-4a99-b618-84304e3fa096"
    },
    "type": "kubernetes.io/service-account-token"
}


# Obtener API URL para la service account

# Desde la consola de la instancia de EC2

kubectl cluster-info | grep -E 'Kubernetes master|Kubernetes control plane' | awk '/http/ {print $NF}'

https://97D61FA6AE5E0972A132D3B25FB0960C.yl4.us-east-2.eks.amazonaws.com


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

export ES_DOMAIN_PASSWORD="zjugGfK1Wucoz+dJ_Ek1$"

ES_DOMAIN_PASSWORD="zjugGfK1Wucoz+dJ_Ek1$"


# Configurar OpenID Connect

eksctl utils associate-iam-oidc-provider --cluster eks-mundos-e --approve

# Crear policy

aws iam create-policy --policy-name fluent-bit-policy --policy-document file://~/environment/logging/fluent-bit-policy.json

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

Kibana URL: https://search-eksworkshop-logging-wxy7apc3i252g2chpzmfuuzr6q.us-east-2.es.amazonaws.com/\_plugin/kibana/
Kibana user: eksworkshop
Kibana password: zjugGfK1Wucoz+dJ_Ek1$


# Instalar Prometheus y Grafana usnado Helm (Manejador de paquetes para kubernetes)

# Agregar repo de prometheus
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts

# Agregar repo de grafana
helm repo add grafana https://grafana.github.io/helm-charts

# Crear el namespace prometheus
kubectl create namespace prometheus

# Desplegar prometheus en EKS

helm install prometheus prometheus-community/prometheus \
--namespace prometheus \
--set alertmanager.persistentVolume.storageClass="gp2" \
--set server.persistentVolume.storageClass="gp2"

# Verificar la instalación
kubectl get all -n prometheus

# Exponer prometheus en la instancia de EC2 en el puerto 8080
kubectl port-forward -n prometheus deploy/prometheus-server 8080:9090 --address 0.0.0.0

# Desplegar Graffana



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










export AWS_REGION='us-east-2'
export ACCOUNT_ID=279445133194
export ES_DOMAIN_NAME="eksworkshop-logging"
export ES_VERSION="7.4"
export ES_DOMAIN_USER="eksworkshop"
export ES_DOMAIN_PASSWORD="zjugGfK1Wucoz+dJ_Ek1$"