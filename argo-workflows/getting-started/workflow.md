A workflow is defined as a **Kubernetes resource**. Each workflow consists of one or more templates, one of which is
defined as the entrypoint. Each template can be one of several types, in this example we have one template that is a
container.

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Workflow
metadata:
  name: hello
spec:
  serviceAccountName: argo # this is the service account that the workflow will run with
  entrypoint: main # the first template to run in the workflows
  templates:
  - name: main
    container: # this is a container template
      image: busybox # this is the image to run
      command: ["echo"]
      args: ["hello world"]

```

There are several other types of templates, and we'll come to more of them soon.

Because a workflow is just a Kubernetes resource, you can use `kubectl` with them.

> There is an issue in Argo Workflows 3.6.x releases where some permissions are missing in **argo-cluster-role** ClusterRole. If you have installed one of those affected versions, you must modify the **argo-cluster-role** ClusterRole, ensuring the role allows both `create` and `patch` verbs over the `workflowtaskresults` resources:

`kubectl edit clusterrole argo-cluster-role`

```
- apiGroups:
  - argoproj.io
  resources:
  - workflowtaskresults
  verbs:
  - list
  - watch
  - deletecollection
  - create
  - patch
```

Create a workflow:

`kubectl -n argo apply -f hello-workflow.yaml`{{execute}}

Then you can wait for it to complete (around 1m):

`kubectl -n argo wait workflows/hello --for condition=Completed --timeout 2m`{{execute}}
