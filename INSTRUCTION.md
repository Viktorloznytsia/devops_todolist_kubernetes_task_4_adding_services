# Task 4: Adding services

## 1. Deploy the application

Apply the manifests in this order (the namespace must exist first):

```
kubectl apply -f .infrastructure/namespace.yml
kubectl apply -f .infrastructure/busybox.yml
kubectl apply -f .infrastructure/todoapp-pod.yml
kubectl apply -f .infrastructure/todoapp-pod-2.yml
kubectl apply -f .infrastructure/clusterip-service.yml
kubectl apply -f .infrastructure/nodeport-service.yml
```

Check that all pods are `Running` and that each service has two endpoints
(both pods have the label `app: todolist`):

```
kubectl get pods -n todoapp
kubectl get svc -n todoapp
kubectl get endpoints -n todoapp
```

## 2. Test the ClusterIP service from the busybox pod (DNS)

```
kubectl exec -it busybox -n todoapp -- sh
curl -i http://todoapp-clusterip.todoapp/api/health
exit
```

The service DNS name is `<service>.<namespace>`
(full form: `todoapp-clusterip.todoapp.svc.cluster.local`).
Expected result: `HTTP/1.1 200 OK` and the body `Health OK`.

## 3. Test the service with port-forward

```
kubectl port-forward service/todoapp-clusterip 8080:80 -n todoapp
```

Then open `http://localhost:8080/api/health` in the browser.
Expected result: `Health OK`. Stop the forwarding with `Ctrl + C`.

## 4. Access the application through the NodePort service

The NodePort service `todoapp-nodeport` exposes the app on port `30080` of the node.
With Docker Desktop Kubernetes the node is the local machine, so open:

```
http://localhost:30080/api/health
```

Expected result: `Health OK`.