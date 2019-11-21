# Kubecon2019

demo of post upgrade helm hook that will update an environment 


run :
> helm3 install --set CF.API_KEY=$CF_API_TOKEN demo ./demo
> helm3 upgrade--set CF.API_KEY=$CF_API_TOKEN  demo ./demo

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
