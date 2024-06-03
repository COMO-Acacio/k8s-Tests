### 1) Install the Gateway APIs
Install the Gateway API CRDs before installing Kong Ingress Controller.

        kubectl apply -f https://github.com/kubernetes-sigs/gateway-api/releases/download/v1.0.0/standard-install.yaml

### 2) Create a Gateway and GatewayClass instance to use.

        kubectl apply -f kong-gateway.yaml

### 3) Install Kong using Help
Adding Kong to Helm charts

        helm repo add kong https://charts.konghq.com
        helm repo update

Installing Kong Ingress Controller and Kong Gateway using Helm

        helm install kong kong/ingress -n kong --create-namespace 

### 4) Deploying a test echo service
To proxy requests, you need an upstream application to send a request to. Deploying this echo server provides a simple application that returns information about the Pod it’s running in:

        kubectl apply -f echo-depl.yaml
        
### 5)  Create routing configuration to proxy /echo requests to the echo server:

        kubectl apply -f echo-gateway.yaml

        kubectl apply -f echo-ingress.yaml

### 6) Port-forward to visualize the response
Do a port-forward to port 1027 for testing the response of the service call:

        kubectl port-forward svc/echo 1027:1027

### 7) Adding a Rate Limiting to the app
Plugins can be linked to a service or a route. Adding a rate limit plugin to a service shares the limit across all routes contained within that service.

        kubectl annotate service echo konghq.com/plugins=rate-limit-5-min

OR 

        kubectl annotate httproute echo konghq.com/plugins=rate-limit-5-min

        kubectl annotate ingress echo konghq.com/plugins=rate-limit-5-min

### 8) Proxy Caching
This configuration caches all HTTP 200 responses to GET and HEAD requests for 300 seconds:

        kubectl apply -f kong-cluster-plugin.yaml
    
### 9) Key Authentication
Add authentication to the echo service:

        kubectl apply -f kong-auth.yaml

        kubectl annotate service echo konghq.com/plugins=rate-limit-5-min,key-auth --overwrite

        kubectl apply -f kong-secret.yaml

        kubectl apply -f kong-consumer.yaml
