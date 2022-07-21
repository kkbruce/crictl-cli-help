# crictl.exe CLI Help

由於 <a href="https://github.com/kubernetes-sigs/cri-tools/blob/master/docs/crictl.md" target="_blank">crictl</a> 的說明文件過於簡單，一邊查詢一邊輸入指令實在沒有效率，因此整理此crictl CLI說明文件並提供一些範例指令碼，以方便查詢與使用。

## crictl.exe

```dos
crictl.exe [global options] command [command options] [arguments...]

COMMANDS:
   attach              Attach to a running container
   create              Create a new container
   exec                Run a command in a running container
   version             Display runtime version information
   images, image, img  List images
   inspect             Display the status of one or more containers
   inspecti            Return the status of one or more images
   imagefsinfo         Return image filesystem info
   inspectp            Display the status of one or more pods
   logs                Fetch the logs of a container
   port-forward        Forward local port to a pod
   ps                  List containers
   pull                Pull an image from a registry
   run                 Run a new container inside a sandbox
   runp                Run a new pod
   rm                  Remove one or more containers
   rmi                 Remove one or more images
   rmp                 Remove one or more pods
   pods                List pods
   start               Start one or more created containers
   info                Display information of the container runtime
   stop                Stop one or more running containers
   stopp               Stop one or more running pods
   update              Update one or more running containers
   config              Get and set crictl client configuration options
   stats               List container(s) resource usage statistics
   statsp              List pod resource usage statistics
   completion          Output shell completion code
   help, h             Shows a list of commands or help for one command

```
   
### `crictl.exe attach` - Attach to a running container

```dos
crictl.exe attach [command options] CONTAINER-ID
```

### `crictl.exe create` - Create a new container

```dos
crictl.exe create [command options] POD container-config.[json|yaml] pod-config.[json|yaml]
```

CLI範例

```dos
crictl create $POD_ID container-config.json pod-config.json
```

### `crictl.exe exec` - Run a command in a running container

```dos
crictl.exe exec [command options] CONTAINER-ID COMMAND [ARG...]
```

CLI範例

```dos
crictl exec $CONTAINER_ID ipconfig
crictl exec -it $CONTAINER_ID cmd
```

### `crictl.exe version` - Display runtime version information

```dos
crictl.exe version [command options] [arguments...]
```

CLI範例

```dos
PS C:\> crictl.exe version
Version:  0.1.0
RuntimeName:  containerd
RuntimeVersion:  v1.6.6
RuntimeApiVersion:  v1
```

### `crictl.exe images` - List images

```dos
crictl.exe images [command options] [REPOSITORY[:TAG]]
```

CLI範例

```dos
crictl.exe ima
crictl.exe image
crictl.exe images
```

> 前二個是縮寫。

### `crictl.exe inspect` - Display the status of one or more containers

```dos
crictl.exe inspect [command options] CONTAINER-ID [CONTAINER-ID...]
```

CLI範例

```dos
crictl.exe inspect $CONTAINER-ID
```

### `crictl.exe inspecti` - Return the status of one or more images

```dos
crictl.exe inspecti [command options] IMAGE-ID [IMAGE-ID...]
```

CLI範例

```dos
crictl.exe img
crictl.exe inspecti 2a67292b6e8ba e3acc2fa6fe12
```

### `crictl.exe imagefsinfo` - Return image filesystem info

```dos
crictl.exe imagefsinfo [command options] [arguments...]
```

CLI範例

```dos
crictl.exe imagefsinfo e3acc2fa6fe12
```

> `inspecti`顯示的資訊與`imagefsinfo`不同哦。

### `crictl.exe inspectp` - Display the status of one or more pods

```dos
crictl.exe inspectp [command options] POD-ID [POD-ID...]
```

CLI範例

```dos
crictl.exe inspectp $POD_ID
```

### `crictl.exe logs` - Fetch the logs of a container

```dos
crictl.exe logs [command options] CONTAINER-ID
```

CLI範例

```dos
crictl.exe logs $CONTAINER_ID
```

### `crictl.exe port-forward` - Forward local port to a pod

```dos
crictl.exe port-forward [command options] POD-ID [LOCAL_PORT:]REMOTE_PORT
```

CLI範例

```dos
crictl.exe port-forward $POD_ID 8080:80
```

### `crictl.exe ps` - List containers

```dos
crictl.exe ps [command options] [arguments...]
```

CLI範例

```dos
crictl.exe ps
crictl.exe ps -a
```

> 還有許多過濾用的參數。當容器多的時候，再查詢使用即可。

### `crictl.exe pull` - Pull an image from a registry

```dos
crictl.exe pull [command options] NAME[:TAG|@DIGEST]

OPTIONS:
   --auth AUTH_STRING                   Use AUTH_STRING for accessing the registry. AUTH_STRING is a base64 encoded 'USERNAME[:PASSWORD]' [%CRICTL_AUTH%]
   --creds USERNAME[:PASSWORD]          Use USERNAME[:PASSWORD] for accessing the registry [%CRICTL_CREDS%]
   --pod-config pod-config.[json|yaml]  Use pod-config.[json|yaml] to override the the pull context
```

CLI範例

```dos
crictl.exe pull k8s.gcr.io/pause:3.7
```

> 如果像存取Azure ACR需要憑證，就要帶認證參數。<br>
> `crictl.exe pull --creds USERNAME[:PASSWORD] youregistry.azurecr.io/blabla:tlsc2022`

Note：

在網路獨立環境，有時需要透過`ctr.exe`來匯出與匯入`.tar`，在匯入時需要加上`-n="k8s.io"`命名空間，這樣 `crictl.exe` 才看得到匯入的映象檔。

```dos
ctr image pull k8s.gcr.io/pause:3.7
ctr images export pause37.tar k8s.gcr.io/pause:3.7
ctr -n="k8s.io" images import .\pause37.tar
crictl images
```

### `crictl.exe run` - Run a new container inside a sandbox

```dos
crictl.exe run [command options] container-config.[json|yaml] pod-config.[json|yaml]
```

CLI範例

```dos
crictl.exe run .\container.json .\pod.json
crictl.exe pods
crictl.exe ps
```

* `run` 執行之後顯示的是 CONTAINER ID。
* `run` 等於把 `runp`、`create`、`start` 整合在一起。

### `crictl.exe runp` - Run a new pod

```dos
crictl.exe runp [command options] pod-config.[json|yaml]
```

CLI範例

```dos
$POD_ID=(crictl runp .\pod-config.json)
$CONTAINER_ID=(crictl create $POD_ID .\container-config.json .\pod-config.json)
crictl start $CONTAINER_ID
```

> 一般會分開執行`runp`、`create`、`start` 是為了取得各自的ID，方便後續 CLI 操作執行。

### `crictl.exe rm` - Remove one or more containers

```dos
crictl.exe rm [command options] CONTAINER-ID [CONTAINER-ID...]

OPTIONS:
   --all, -a    Remove all containers (default: false)
   --force, -f  Force removal of the container, disregarding if running (default: false)
```

CLI範例

```dos
crictl.exe img
crictl.exe stop 61837f3d17b3b
crictl.exe rm 61837f3d17b3b
```

### `crictl.exe rmi` - Remove one or more images

```dos
crictl.exe rmi [command options] IMAGE-ID [IMAGE-ID...]

OPTIONS:
   --all, -a    Remove all images (default: false)
   --prune, -q  Remove all unused images (default: false)
```

CLI範例

```dos
crictl.exe rmi c764b8ebd8645
crictl.exe rmi --prune
```

> 如果系統目前沒在執行中的容器，那麼`--prune`等於清除所有映像檔。

### `crictl.exe rmp` - Remove one or more pods

```dos
crictl.exe rmp [command options] POD-ID [POD-ID...]

OPTIONS:
   --all, -a    Remove all pods (default: false)
   --force, -f  Force removal of the pod sandbox, disregarding if running (default: false)
```

CLI範例

```dos
PS C:\> crictl.exe rmp -a
pod sandbox "46c169974805cb12f3cfedc34e48444c6710e118af93b42ce303ae3cbbd8d304" is running, please stop it first
PS C:\> crictl.exe pods
POD ID              CREATED             STATE               NAME                    NAMESPACE           ATTEMPT             RUNTIME
46c169974805c       23 minutes ago      Ready               dotnet-sample-sandbox   default             1                   (default)
PS C:\> crictl.exe stopp 46c169974805c
Stopped sandbox 46c169974805c
PS C:\> crictl.exe rmp -a
Removed sandbox 46c169974805cb12f3cfedc34e48444c6710e118af93b42ce303ae3cbbd8d304
```

### `crictl.exe pods` - List pods

```dos
crictl.exe pods [command options] [arguments...]
```

CLI範例

```dos
crictl.exe pods
```

### `crictl.exe start` - Start one or more created containers

```dos
crictl.exe start [command options] CONTAINER-ID [CONTAINER-ID...]
```

CLI範例

```dos
crictl.exe start $CONTAINER_ID
```

### `crictl.exe info` - Display information of the container runtime

```dos
crictl.exe info [command options] [arguments...]
```

CLI範例

```dos
crictl.exe info
```

### `crictl.exe stop` - Stop one or more running containers

```dos
crictl.exe stop [command options] CONTAINER-ID [CONTAINER-ID...]
```

CLI範例

```dos
crictl.exe stop $CONTAINER_ID
```

### `crictl.exe stopp` - Stop one or more running pods

```dos
crictl.exe stopp [command options] POD-ID [POD-ID...]
```

CLI範例

```dos
crictl.exe stopp $POD_ID
```

### `crictl.exe update` - Update one or more running containers

```dos
crictl.exe update [command options] CONTAINER-ID [CONTAINER-ID...]

OPTIONS:
   --cpu-count value    (Windows only) Number of CPUs available to the container (default: 0)
   --cpu-maximum value  (Windows only) Portion of CPU cycles specified as a percentage * 100 (default: 0)
   --cpu-period value   CPU CFS period to be used for hardcapping (in usecs). 0 to use system default (default: 0)
   --cpu-quota value    CPU CFS hardcap limit (in usecs). Allowed cpu time in a given period (default: 0)
   --cpu-share value    CPU shares (relative weight vs. other containers) (default: 0)
   --cpuset-cpus value  CPU(s) to use
   --cpuset-mems value  Memory node(s) to use
   --memory value       Memory limit (in bytes) (default: 0)
```

CLI範例

```dos
crictl.exe update --cpu-count 1 $CONTAINER_ID
```

### `crictl.exe config` - Get and set crictl client configuration options

```dos
crictl.exe config [command options] [<crictl options>]
```

通常會設定在 `~\.crictl\crictl.yaml`。

```dos
mkdir -Force "$home\.crictl"

@"
runtime-endpoint: npipe://./pipe/containerd-containerd
image-endpoint: npipe://./pipe/containerd-containerd
timeout: 10
#debug: true
"@ | Set-Content "$home\.crictl\crictl.yaml" -Force
```

> 如果你是第一次在`crictl.exe`執行建立Pod的工作，它會在背景下載`k8s.gcr.io/pause`，而這個下載作業會受到上面的`timeout`參數影響。因此建議第一次執行可以加大至`90`~`120`。等正常下載完成後再縮減回來即可。

### `crictl.exe stats` - List container(s) resource usage statistics

```dos
crictl.exe stats [command options] [ID]
```

CLI範例 

```dos
crictl.exe stats
crictl.exe stats -a
crictl.exe stats $CONTAINER_ID
```

### `crictl.exe statsp` - List pod resource usage statistics

```dos
crictl.exe statsp [command options] [ID]
```

CLI範例

```dos
crictl.exe statsp
crictl.exe statsp $POD_ID
```

### `crictl.exe completion` - Output shell completion code

```dos
crictl.exe completion [command options] SHELL
```

> 提供Shell自動完成，支援bash、zsh、fish，Windows不支援。
