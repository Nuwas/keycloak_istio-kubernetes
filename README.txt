

kubectl create namespace jwt-keycloak

kubectl apply -f postgres.yaml
watch kubectl get all -n jwt-keycloak

kubectl apply -f keycloak_deploy_service.yaml
watch kubectl get all -n jwt-keycloak


