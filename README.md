# kubernetes-nvidia-gpu-workstation
Install kubernetes and Nvidia Cloud Container on Centos7.6 workstation

Follow these steps to install docker and kubernetes

https://www.howtoforge.com/tutorial/centos-kubernetes-docker-cluster/

to onnect your newly installed Kubernetes cluster to the NVIDIA GPU Cloud and use an optimized GPU-enabled container

visit: 
https://devblogs.nvidia.com/maximizing-nvidia-dgx-kubernetes/
```
cat ~/.docker/config.json
```
will display your password for nvcr.io. Now you can using your NGC API Key to create a Kubernetes “secret” on the master node, this secret enables Kubernetes to log into NGC and download an optimized GPU-enabled container when specified:
```
kubectl create secret docker-registry ngc --docker-server=nvcr.io --docker-username='$oauthtoken' --docker-password=JG9hdXxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxV4 --docker-email=xxxxxxxxxxx@xxx.xxx
```
Verify that the secret creation has completes:
```
kubectl get secrets
```
You should see output similar to the lines below

```
(base) [thomas@localhost ~]$ kubectl get secrets
NAME                  TYPE                                  DATA   AGE
default-token-nhb5q   kubernetes.io/service-account-token   3      15h
ngc                   kubernetes.io/dockerconfigjson        1      2m7s
(base) [thomas@localhost ~]$ 
```

Now you can use your very first optimized GPU-enabled container. Kubernetes uses the “.yaml” file format.
Create a file names pod.yaml

Now issue this command
```
kubectl create --validate=false -f pod.yml
kubectl get pods --show-all
NAME       READY   STATUS               RESTARTS   AGE
gpu-pod   1/1     Running   0          118s
```
Pods can be deleted with 
```
kubectl delete -f pod.yml
```



```
apiVersion: v1
kind: Pod
metadata:
  name: gpu-pod
spec:
  containers:
    - name: cuda-container
      image: nvidia/cuda:9.0-base
      command: ["sleep"]
      args: ["100000"]
      computeResourceRequests: ["nvidia-gpu"]
  computeResources:
    - name: "nvidia-gpu"
      resources:
        limits:
          nvidia.com/gpu: 1
      affinity:
        required:
          - key: "nvidia.com/gpu-memory"
            operator: "Gt"
            values: ["8000"] # change value to appropriate mem for GPU

```
Now run 

```
kubectl  describe po/gpu-pod
```
You can open a bash session inside the container

```
kubectl exec -it gpu-pod bash
```

## Solve DNS issues you might have


## LIBRARY_PATH


      
