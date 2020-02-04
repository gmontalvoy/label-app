# Example App to show how labeling (NS) works in a simple way.
## Prepared as workshops helpers

## This app relays on availability of rhscl/httpd-24-rhel7 locally or accesible regitry

* git clone THISREPO.
* Authenticate as you consider.
* oc create -f dc1.yaml -n $ProjectNameHere

## Scenarios
### 1. App working as expected.

```oc get pods``` will show the expected behaviour when you deployed without any preferences on which node has to be used for the app without dealing with nodeSelector.

### 2. Attaching app to a specific node.

if you want to make your application binded to a node (or a labelling set of ones):

```oc label node $nodeNameHere type=GPU```

```oc patch dc label-app -p '{"spec":{"template":{"spec":{"nodeSelector":{"type":"GPU"}}}}}'```

Then we can see our application running on the labelled node
```oc get pods -o wide```

### 3. Labelling incorrectly and consequences.

Failing in labelling the node incorrectly will left the resources pending until it matches on valid label:

```
<unknown>   Warning   FailedScheduling         pod/test-app-3-deploy              0/6 nodes are available: 6 node(s) didn't match node selector.
<unknown>   Warning   FailedScheduling         pod/test-app-3-deploy              0/6 nodes are available: 6 node(s) didn't match node selector.
```
