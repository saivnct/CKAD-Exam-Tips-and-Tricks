# CKAD Tips & Tricks

## Create an alias for kubectl

No need to type **kubectl** every single time.

    alias k=kubectl

    export out="--dry-run=client -o yaml"
    
    export n="-n my-namespace"

    alias runbox="kubectl run busybox --image=busybox --restart=Never --rm -it --command --"

    alias runnginx="kubectl run nginx --image=nginx:alpine --restart=Never --rm -it --command --"

Ex:
    
    k get po $n

    k get po my-pod $n $out > my-pod.yaml

    runnginx curl -L my-service:8080

    runbox nc -z -v -w 3 my-service 8080

## Kubectl run/create is your friend

There's no way you'll have time to type all of these up in the exam, so use a base template.

**Pod yaml**

    k run p1 --image=nginx --dry-run=client -o yaml > p1.yaml

    k run p1 --iamge=nginx $out > p1.yaml

    k $do echo "Hello World"

**Deploy yaml**

    k create deployment d1 --image=nginx --replicas=3 --dry-run=client -o yaml > d1.yaml

**Job yaml**

    k create job my-job --image=nginx --dry-run=client -o yaml > j1.yaml

**CronJob yaml**

    k create cj my-cj --image=nginx --schedule="*/1 * * * *" --dry-run=client -o yaml > cj1.yaml

## Kubectl get
**Get deployments, replicasets, pods all-in-one command**

    k get deploy,rs,po
    k get po --show-labels



## Grep for events to describe pod details

    k describe pod <pod-name> | grep -i events -A 10

The -A option essentially means 'after,' so you're saying give me the search results that start with 'events' and then the next 10 lines too. Piping through with **tail** works fine as well, but this option is a bit more precise

## Find all the kubectl shortcuts

    k api-resources | grep -i persistentvolumeclaim

This gets you the following response

    persistentvolumeclaims     pvc   true    PersistentVolumeClaim

Api-resources gives you the supported api resources (duh), but it also gives you the shortcuts for each resource. Which would you rather do?

    kubectl get persistentvolumeclaim

    k get pvc

## Find the fields for supported resources

Need to know about the NodeSelector field in a pod? Check the specs with **explain**. 

    k explain pod.spec.nodeSelector

Note that it is cap sensitive, so your best bet is to first pipe it through to a case insensitive grep and see if you're in the right spot

    k explain pod.spec | grep -i nodeselector

Inside these explanations, there's generally a link to the documentation that is necessary for greater understanding.

Strictly speaking, it may be faster to just use the k8s docs over this method if you're less familiar with some of the more niche specs.

## Cut out the (sometimes) crap in —help arguments options

When typing in -h or —help, you get A LOT of options info populated, where generally you may just want to see some examples of the object being used. To mitigate this, use **head** to only see the examples. Generally, the first 25-30 lines are strictly example based.

    k annotate --help | head -30

## Specificity matters

It's possible to declare details at both the pod level and then the container level. For instance, securityContext can be set in both. More granularity will override other specifics declared in the YAML.

Here is an example with securityContext:

    k explain pod.spec.securityContext

    k explain pod.spec.containers.securityContext

In this example, if I set runAsUser in both the Pod specs and container specs, the container classification would override the pod specs. 


## Check if a service is reachable from within a pod by nc command
    k run busybox --image=busybox --restart=Never --rm -it --command -- wget -O- <service-name>:<service-port>
    k run bysybox --image=busybox --restart=Never --rm -it --command -- nc -zv <service-name> <service-port>

## Search keywords
    pv, pvc
    volumes
    volumeMounts
    configmap
    configmapref
    configmapkeyref
    secret
    deployment
    ingress
    securityContext
    runasuser

## Get to know Vim

The test is command line based, so you'll need to know to use Vim or another text editor. Without it, you'll be very strapped for time.

Here are some potential keys in Vim that will prove useful (I'm gonna assume you at least know how to exit the editor and navigate without the arrows ;) Note, these are **case sensitive**

`Shift + A` - go to the end of the current row (insert mode)

`Shift + I` - go to the first letter on the current row (insert mode)

`Shift + G` - go to the last row of data in the file

`Shift + H` - go to top of screen

`Shift + S` - delete line and substitute text (insert mode)

`/Pod` - find any instances of `Pod` in the file

`e` - jump to the end of the next word

`w` - jump to the start of the next word

`b` - jump backwards to the start of a word



`:25` - go to the 25th row in the file


`:w` - write (save) the file, but don't exit

`:wq` - write (save) and quit

`:q`- quit (fails if there are unsaved changes)

`:q!`  - quit and throw away unsaved changes

### Copy & Paste
    Mark lines: Esc+V (then arrow keys) or Shift+V (then arrow keys)
    Copy marked lines: y
    Cut marked lines: d
    Past lines: p or P

### Indent multiple lines
    To indent multiple lines press Esc and type :set shiftwidth=2. 
    First mark multiple lines using Shift v and the up/down keys. 
    Then to indent the marked lines press > or <.You can then press . to repeat the action.    

If you REALLY hate Vim, change the editor stat!

```
export KUBECTL_EDITOR="nano"

```


