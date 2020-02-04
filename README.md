# Example App to show how labeling (NS) works in a simple way.
## Prepared as workshops helpers

## This app relays on availability of rhscl/httpd-24-rhel7 locally or accesible regitry

* ```git clone git@github.com:gmontalvoy/label-app.git```
* Authenticate as you consider.
* oc create -f dc1.yaml -n $ProjectNameHere

## Scenarios
### 1. App working as expected.

```oc get pods``` will show the expected behaviour when you deployed without any preferences on which node has to be used for the app without dealing with nodeSelector.

```
label-app-7bcb475c4-596p7   1/1     Running   0          4m29s   10.128.2.14   ip-10-0-140-174.us-east-2.compute.internal   <none>           <none>
label-app-7bcb475c4-ktrqd   1/1     Running   0          4m29s   10.131.0.17   ip-10-0-166-231.us-east-2.compute.internal   <none>           <none>
label-app-7bcb475c4-scm62   1/1     Running   0          4m29s   10.129.2.11   ip-10-0-158-110.us-east-2.compute.internal   <none>           <none>
```

### 2. Attaching app to a specific node.

if you want to make your application binded to a node (or a labelling set of ones):

```oc label node $nodeNameHere type=GPU```

ip-10-0-166-231.us-east-2.compute.internal   Ready    worker   109m   v1.16.2   [...]/os_id=rhcos,**type=GPU**

```oc patch dc label-app -p '{"spec":{"template":{"spec":{"nodeSelector":{"type":"GPU"}}}}}'```

Then we can see our application running on the labelled node exclusively
```oc get pods -o wide```

```
label-app-c4dddd765-5bc29   1/1     Running   0          111s   10.131.0.18   ip-10-0-166-231.us-east-2.compute.internal   <none>           <none>
label-app-c4dddd765-cldk7   1/1     Running   0          92s    10.131.0.20   ip-10-0-166-231.us-east-2.compute.internal   <none>           <none>
label-app-c4dddd765-t8s8x   1/1     Running   0          101s   10.131.0.19   ip-10-0-166-231.us-east-2.compute.internal   <none>           <none>
```
### 3. Labelling incorrectly and consequences.

Failing in labelling the node incorrectly will left the resources pending until it matches on valid label:

```
<unknown>   Warning   FailedScheduling         pod/test-app-3-deploy              0/6 nodes are available: 6 node(s) didn't match node selector.
<unknown>   Warning   FailedScheduling         pod/test-app-3-deploy              0/6 nodes are available: 6 node(s) didn't match node selector.
```
