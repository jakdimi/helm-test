# Test argo faliure

- argo is installed on minikube like we usually do in [this repo](https://zivgitlab.uni-muenster.de/wwuit-sys/sciebo/kubernetes/-/tree/master/argocd), only replace `harbor.uni-muenster.de/proxy-docker/library/redis:6.2.6-alpine` with `redis:6.2.14-alpine` to install locally.
- install application via `kubectl apply -f application.yml`


## Argo dies

- Made argo die by deleting all deployments
- No deployments left, most pods in namespace  `argocd` are gone 
- `argocd-test-deployment` pod still running and working
    - shell into the alpine instance still works

```
helm-test on  main [?] 
❯ kubectl -n argocd get deployments
NAME                               READY   UP-TO-DATE   AVAILABLE   AGE
argocd-applicationset-controller   1/1     1            1           9m32s
argocd-dex-server                  1/1     1            1           59m
argocd-notifications-controller    1/1     1            1           59m
argocd-redis                       1/1     1            1           59m
argocd-repo-server                 1/1     1            1           59m
argocd-server                      1/1     1            1           9m32s

helm-test on  main [?] 
❯ kubectl -n argocd delete deployment argocd-applicationset-controller argocd-dex-server argocd-notifications-controller argocd-redis argocd-repo-server argocd-repo-server
deployment.apps "argocd-applicationset-controller" deleted
deployment.apps "argocd-dex-server" deleted
deployment.apps "argocd-notifications-controller" deleted
deployment.apps "argocd-redis" deleted
deployment.apps "argocd-repo-server" deleted
Error from server (NotFound): deployments.apps "argocd-repo-server" not found

helm-test on  main [?] 
❯ kubectl -n argocd get deployment
NAME            READY   UP-TO-DATE   AVAILABLE   AGE
argocd-server   1/1     1            1           10m

helm-test on  main [?] 
❯ kubectl -n argocd delete deployment argocd-server
deployment.apps "argocd-server" deleted

helm-test on  main [?] 
❯ kubectl -n argocd get deployments
No resources found in argocd namespace.

helm-test on  main [?] 
❯ kubectl get pods   
NAME                                      READY   STATUS    RESTARTS   AGE
argocd-test-deployment-7d9599d8bb-6vgph   1/1     Running   0          22m

helm-test on  main [?] 
❯ kubectl exec argocd-test-deployment-7d9599d8bb-6vgph -it --tty -- /bin/sh
/ # ls
bin    dev    etc    home   lib    media  mnt    opt    proc   root   run    sbin   srv    sys    tmp    usr    var
/ # exit
```

- *reinstall argo as above*


## The Application resource is deleted

- delete application via `kubectl -n argocd delete application argo-test-application`
- `argocd-test-deployment` pod still running and working
    - shell into the alpine instance still works

```
helm-test on  main [?] 
❯ kubectl get deployment
NAME                     READY   UP-TO-DATE   AVAILABLE   AGE
argocd-test-deployment   1/1     1            1           34s

helm-test on  main [?] 
❯ kubectl -n argocd get application
NAME                    SYNC STATUS   HEALTH STATUS
argo-test-application   Synced        Healthy

helm-test on  main [?] 
❯ kubectl -n argocd delete application argo-test-application
application.argoproj.io "argo-test-application" deleted

helm-test on  main [?] 
❯ kubectl get pod 
NAME                                      READY   STATUS    RESTARTS   AGE
argocd-test-deployment-7d9599d8bb-jwbhh   1/1     Running   0          2m

helm-test on  main [?] 
❯ kubectl exec argocd-test-deployment-7d9599d8bb-jwbhh -it --tty -- /bin/sh
/ # ls
bin    dev    etc    home   lib    media  mnt    opt    proc   root   run    sbin   srv    sys    tmp    usr    var
/ # exit
```


