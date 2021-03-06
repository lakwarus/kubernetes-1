## Sample3: Ballerina program with multiple services with different ports

- This sample deploy ballerina program 2 services with single endpoint.
- The ingress is configured so that two APIs can be accessed as following.
    http://pizzashack.com/customer
    http://order.com/orders
- Following artifacts will be generated from this sample.
    ``` 
    $> docker image
    ballerina.com/pizzashack:2.1.0 
    
    $> tree
        ├── README.md
        ├── kubernetes
        │   ├── docker
        │   │   └── Dockerfile
        │   ├── pizzashack_deployment.yaml
        │   ├── pizzashack_hpa.yaml
        │   ├── pizzashack_ingress.yaml
        │   └── pizzashack_svc.yaml
        ├── pizzashack.bal
        └── pizzashack.balx
    ```
### How to run:

1. Compile the  pizzashack.bal file. Command to run kubernetes artifacts will be printed on success:
```bash
$> ballerina build pizzashack.bal
@docker 			 - complete 3/3 
@kubernetes:HPA 		 - complete 1/1
@kubernetes:Deployment 		 - complete 1/1
@kubernetes:Service 		 - complete 1/1
@kubernetes:Ingress 		 - complete 2/2
Run following command to deploy kubernetes artifacts: 
kubectl apply -f /Users/lakmal/ballerina/kubernetes/samples/sample3/kubernetes/

```

2. pizzashack.balx, Dockerfile, docker image and kubernetes artifacts will be generated: 
```bash
$> tree
.
├── README.md
├── kubernetes
│   ├── docker
│   │   └── Dockerfile
│   ├── pizzashack_deployment.yaml
│   ├── pizzashack_hpa.yaml
│   ├── pizzashack_ingress.yaml
│   └── pizzashack_svc.yaml
├── pizzashack.bal
└── pizzashack.balx
```

3. Verify the docker image is created:
```bash
$> docker images
REPOSITORY                   TAG                 IMAGE ID            CREATED             SIZE
ballerina.com/pizzashack     2.1.0              df83ae43f69b        2 minutes ago        102MB

```

4. Run kubectl command to deploy artifacts (Use the command printed on screen in step 1):
```bash
$> kubectl apply -f /Users/lakmal/ballerina/kubernetes/samples/sample3/kubernetes/
deployment "pizzashack-deployment" created
horizontalpodautoscaler "pizzashack-hpa" created
ingress "customer-ingress" created
ingress "order-ingress" created
service "pizzaep-svc" created

```

5. Verify kubernetes deployment,service and ingress is running:
```bash
$> kubectl get pods
NAME                                    READY     STATUS    RESTARTS   AGE
pizzashack-deployment-d6747b8b9-64n7d   1/1       Running   0          39m


$> kubectl get svc
NAME                                              TYPE           CLUSTER-IP       EXTERNAL-IP   PORT(S)                      AGE
pizzaep-svc                                       ClusterIP      10.99.151.55     <none>        9090/TCP                     1m



$> kubectl get ingress
NAME               HOSTS            ADDRESS   PORTS     AGE
customer-ingress   pizzashack.com             80, 443   2m
order-ingress      order.com                  80, 443   2m
```

6. Access the hello world service with curl command:

- **Using ingress**

Add /etc/host entry to match hostname. 
_(127.0.0.1 is only applicable to docker for mac users. Other users should map the hostname with correct ip address 
from `kubectl get ingress` command.)_
 ```
 127.0.0.1 order.com
 127.0.0.1 pizzashack.com
 ```
Use curl command with hostname to access the service.
```bash
$> curl http://order.com/orders
Get order resource !!!!

$>curl http://pizzashack.com/customer
Get Customer resource !!!!
```

7. Undeploy sample:
```bash
$> kubectl delete -f /Users/lakmal/ballerina/kubernetes/samples/sample3/kubernetes/
```
