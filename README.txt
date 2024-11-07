Istio

    istioctl install --set profile=default
    kubectl label namespace default istio-injection=enabled

Certificate
    export DOMAIN_NAME=nuwas.nn
    openssl req -x509 -sha256 -nodes -days 365 -newkey rsa:2048 -subj '/O=$DOMAIN_NAME Inc./CN=$DOMAIN_NAME' -keyout $DOMAIN_NAME.key -out $DOMAIN_NAME.crt
    openssl req -out tt.$DOMAIN_NAME.csr -newkey rsa:2048 -nodes -keyout tt.$DOMAIN_NAME.key -subj "/CN=tt.$DOMAIN_NAME/O=tt from $DOMAIN_NAME"
    openssl x509 -req -days 365 -CA $DOMAIN_NAME.crt -CAkey $DOMAIN_NAME.key -set_serial 0 -in tt.$DOMAIN_NAME.csr -out tt.$DOMAIN_NAME.crt

    kubectl create secret tls global-istio-tls --key tt.$DOMAIN_NAME.key --cert tt.$DOMAIN_NAME.crt -n istio-system

Gateway-Crd
    kubectl apply -f gateway_crd.yaml

Keycloak

kubectl create namespace jwt-keycloak
kubectl apply -f postgres.yaml
watch kubectl get all -n jwt-keycloak

kubectl apply -f keycloak_deploy_service.yaml
watch kubectl get all -n jwt-keycloak

Create Realm, Client (Client and Client-Roles), Realm-roles (admin and user) and User (User, Credential and Role Mapping)

  Test Jwt token
  https://tt.nuwas.nn/keycloak/realms/nuwas-auth/protocol/openid-connect/token

  Body
    grant_type = password
    client_id = nuwas-rest-api
    username = nuwas
    password = nuwas


    For istio
    get the Jwks-uri details from Realm Settings/Endpoints/OpenID Endpoint Configuration

Deploy Authentication and Authurization CRDs

kubectl apply -f request_authentication_crd.yaml
kubectl apply -f authorization_policy_crd.yaml
kubectl apply -f authorization_policy_allow_crd.yaml

Later get the jwt-token and try out the service end-point using the keycloak_k8s.postman_collection.json



