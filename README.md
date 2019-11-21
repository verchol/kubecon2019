# Kubecon2019

demo of post upgrade helm hook that will update an environment 


run :
helm3 install --set CF.API_KEY=$CF_API_TOKEN demo ./demo

helm3 upgrade--set CF.API_KEY=$CF_API_TOKEN  demo ./demo

in ./demo/test/


## Hook
This will execute this logic on POST_UPGRADE 

```
apiVersion: v1
kind: Pod
metadata:
  name: "{{ include "demo.fullname" . }}-codefresh-pipeline"
  labels:
{{ include "demo.labels" . | nindent 4 }}
  annotations:
    "helm.sh/hook": post-upgrade
    "helm.sh/hook-delete-policy": hook-failed
     
spec:
  containers:
   - name: cf
     env:
       - name: CF_API_KEY
         value : {{.Values.CF.API_KEY}}
     image: codefresh/cli
      #command: ['wget']
     args: ['run', 'kubecon2019/helm3posthook']
  restartPolicy: Never
```

Codefresh Pipeline 
[Codfresh-inc/kubecon2019/helm3posthook]: (https://g.codefresh.io/pipelines/edit/workflow?id=5dd5d49a7ede02556adef526&pipeline=helm3posthook&projects=kubecon2019&projectId=5dd5d4757ede02fe2edef525&rightbar=steps)
```
version: '1.0'
stages:
  - stage1
steps:
  hello_world_helm3:
    title: free style step
    stage: stage1
    image: verchol/helm:v3
    commands:
      - helm version
    env:
      name: kubecon-env-staging
      endpoints:
      - name: login
        url: https://g.codefresh.io
      type: helm-release
      change: Updated
      filters:
      - cluster: cf-load@codefresh-load
        releaseName: istio
  ```
