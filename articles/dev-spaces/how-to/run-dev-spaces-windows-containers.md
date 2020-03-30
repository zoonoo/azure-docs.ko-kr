---
title: Windows 컨테이너와 상호 작용
services: azure-dev-spaces
ms.date: 01/16/2020
ms.topic: conceptual
description: Windows 컨테이너를 사용하여 기존 클러스터에서 Azure 개발자 공간을 실행하는 방법에 대해 알아봅니다.
keywords: Azure 개발자 공간, 개발자 공간, 도커, 쿠버넷, Azure, AKS, Azure Kubernetes 서비스, 컨테이너, Windows 컨테이너
ms.openlocfilehash: 0b3f221c9e62343a02ba8742e4cf988c7cf26c12
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240478"
---
# <a name="interact-with-windows-containers-using-azure-dev-spaces"></a>Azure 개발자 공간을 사용하여 Windows 컨테이너와 상호 작용

신규 및 기존 Kubernetes 네임스페이스에서 Azure 개발자 공간을 활성화할 수 있습니다. Azure 개발자 공간 실행 하 고 리눅스 컨테이너에서 실행 되는 악기 서비스. 이러한 서비스는 동일한 네임스페이스의 Windows 컨테이너에서 실행되는 응용 프로그램과상호 작용할 수도 있습니다. 이 문서에서는 기존 Windows 컨테이너가 있는 네임스페이스에서 서비스를 실행하기 위해 개발자 공간을 사용하는 방법을 보여 주며 있습니다. 현재 Azure 개발자 공간을 사용하여 Windows 컨테이너를 디버깅하거나 연결할 수 없습니다.

## <a name="set-up-your-cluster"></a>클러스터 설정

이 문서에서는 Linux 및 Windows 노드 풀이 모두 있는 클러스터가 이미 있다고 가정합니다. Linux 및 Windows 노드 풀로 클러스터를 만들어야 하는 경우 [여기에서][windows-container-cli]지침을 따를 수 있습니다.

Kubernetes 명령줄 클라이언트인 [kubectl을][kubectl]사용하여 클러스터에 연결합니다. Kubernetes 클러스터에 연결하도록 `kubectl`을 구성하려면 [az aks get-credentials][az-aks-get-credentials] 명령을 사용합니다. 이 명령은 자격 증명을 다운로드하고 Kubernetes CLI가 해당 자격 증명을 사용하도록 구성합니다.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

클러스터에 대한 연결을 확인하려면 [kubectl get][kubectl-get] 명령을 사용하여 클러스터 노드 목록을 반환합니다.

```azurecli-interactive
kubectl get nodes
```

다음 예제 출력은 Windows 및 Linux 노드가 모두 있는 클러스터를 보여 주며, 계속하기 전에 각 노드에 대한 상태가 *준비되었는지* 확인합니다.

```console
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmss000000   Ready    agent   13m    v1.14.8
aks-nodepool1-12345678-vmss000001   Ready    agent   13m    v1.14.8
aksnpwin000000                      Ready    agent   108s   v1.14.8
```

Windows 노드에 [오염을][using-taints] 적용합니다. Windows 노드의 오염으로 인해 개발자 스페이스가 Windows 노드에서 실행되도록 Linux 컨테이너를 예약할 수 없습니다. 다음 명령 예제 명령은 이전 예제의 *aksnpwin987654* Windows 노드에 오염을 적용합니다.

```azurecli-interactive
kubectl taint node aksnpwin987654 sku=win-node:NoSchedule
```

> [!IMPORTANT]
> 노드에 taint를 적용하는 경우 해당 노드에서 서비스를 실행하도록 서비스의 배포 템플릿에서 일치하는 관용을 구성해야 합니다. 샘플 응용 프로그램은 이미 이전 명령에서 구성한 오염에 [일치하는 내성으로][sample-application-toleration-example] 구성되어 있습니다.

## <a name="run-your-windows-service"></a>Windows 서비스 실행

AKS 클러스터에서 Windows 서비스를 실행하고 *실행 중인* 상태인지 확인합니다. 이 문서에서는 [샘플 응용 프로그램을][sample-application] 사용하여 클러스터에서 실행되는 Windows 및 Linux 서비스를 보여 줍니다.

GitHub에서 샘플 응용 프로그램을 복제하고 `dev-spaces/samples/existingWindowsBackend/mywebapi-windows` 디렉터리로 이동합니다.

```console
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/existingWindowsBackend/mywebapi-windows
```

샘플 응용 프로그램은 [Helm 3을][helm-installed] 사용하여 클러스터에서 Windows 서비스를 실행합니다. 디렉터리로 `charts` 이동하여 Windows 서비스를 실행하는 Helm을 사용합니다.

```console
cd charts/
kubectl create ns dev
helm install windows-service . --namespace dev
```

위의 명령은 Helm을 사용하여 *개발* 네임스페이스에서 Windows 서비스를 실행합니다. *dev라는*네임스페이스가 없는 경우 생성됩니다.

`kubectl get pods` 이 명령을 사용하여 Windows 서비스가 클러스터에서 실행되고 있는지 확인합니다. 

```console
$ kubectl get pods --namespace dev --watch
NAME                     READY   STATUS              RESTARTS   AGE
myapi-4b9667d123-1a2b3   0/1     ContainerCreating   0          47s
...
myapi-4b9667d123-1a2b3   1/1     Running             0          98s
```

## <a name="enable-azure-dev-spaces"></a>Azure 개발자 공간 사용

Windows 서비스를 실행하는 데 사용한 것과 동일한 네임스페이스에서 개발자 공간을 사용하도록 설정합니다. 다음 명령을 사용하면 *개발자* 네임스페이스에서 개발자 공백을 사용할 수 있습니다.

```console
az aks use-dev-spaces -g myResourceGroup -n myAKSCluster --space dev --yes
```

## <a name="update-your-windows-service-for-dev-spaces"></a>개발자 공간에 대한 Windows 서비스 업데이트

기존 네임스페이스에서 이미 실행 중인 컨테이너를 사용하여 개발자 공간을 사용하도록 설정하면 기본적으로 개발자 공간은 해당 네임스페이스에서 실행되는 새 컨테이너를 시도하고 계측합니다. 또한 Dev Spaces는 네임스페이스에서 이미 실행 중인 서비스를 위해 생성된 새 컨테이너를 시도하고 계측합니다. Dev Spaces가 네임스페이스에서 실행중인 컨테이너를 계측하지 못하도록 하려면 `deployment.yaml`에 프록시 없음 *헤더를* 추가합니다.

`existingWindowsBackend/mywebapi-windows/charts/templates/deployment.yaml` 파일에 추가: `azds.io/no-proxy: "true"`

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  ...
spec:
  replicas: {{ .Values.replicaCount }}
  selector:
    ...
  template:
    metadata:
      labels:
        app.kubernetes.io/name: {{ include "mywebapi.name" . }}
        app.kubernetes.io/instance: {{ .Release.Name }}
        azds.io/no-proxy: "true"
```

Windows `helm list` 서비스에 대한 배포를 나열하는 데 사용합니다.

```cmd
$ helm list --namespace dev
NAME              REVISION  UPDATED                     STATUS      CHART           APP VERSION NAMESPACE
windows-service 1           Wed Jul 24 15:45:59 2019    DEPLOYED    mywebapi-0.1.0  1.0         dev  
```

위의 예에서 배포 이름은 Windows *서비스*입니다. 다음을 사용하여 `helm upgrade`새 구성으로 Windows 서비스를 업데이트합니다.

```cmd
helm upgrade windows-service . --namespace dev
```

당신이 당신의 `deployment.yaml`업데이트 이후 , 개발자 공간시도하고 서비스를 계측하지 않습니다.

## <a name="run-your-linux-application-with-azure-dev-spaces"></a>Azure 개발자 공간으로 Linux 응용 프로그램 실행

디렉터리로 `webfrontend` 이동하여 `azds prep` 및 `azds up` 명령을 사용하여 클러스터에서 Linux 응용 프로그램을 실행합니다.

```console
cd ../../webfrontend-linux/
azds prep --enable-ingress
azds up
```

명령은 `azds prep --enable-ingress` 응용 프로그램에 대한 Helm 차트 및 Dockerfiles를 생성합니다.

> [!TIP]
> 프로젝트의 [Dockerfile 및 Helm 차트](../how-dev-spaces-works-prep.md#prepare-your-code)는 Azure Dev Spaces에서 코드를 빌드하고 실행하는 데 사용되지만 프로젝트를 빌드하고 실행하는 방법을 변경하려면 이러한 파일을 수정할 수 있습니다.

`azds up` 명령은 네임스페이스에서 서비스를 실행합니다.

```console
$ azds up
Using dev space 'dev' with target 'myAKSCluster'
Synchronizing files...4s
Installing Helm chart...11s
Waiting for container image build...6s
Building container image...
Step 1/12 : FROM mcr.microsoft.com/dotnet/core/sdk:2.2
...
Step 12/12 : ENTRYPOINT ["/bin/bash", "/entrypoint.sh"]
Built container image in 36s
Waiting for container...2s
Service 'webfrontend' port 'http' is available at http://dev.webfrontend.abcdef0123.eus.azds.io/
Service 'webfrontend' port 80 (http) is available via port forwarding at http://localhost:57648
```

azds up 명령의 출력에 표시되는 공용 URL을 열어 서비스가 실행되는 것을 볼 수 있습니다. 이 예제에서 공용 URL은 `http://dev.webfrontend.abcdef0123.eus.azds.io/`입니다. 브라우저에서 서비스로 이동하여 상단의 *정보* 보기를 클릭합니다. 컨테이너가 사용 중이면 Windows 버전이 포함된 *mywebapi* 서비스의 메시지가 표시되는지 확인합니다.

![mywebapi에서 Windows 버전을 보여주는 샘플 응용 프로그램](../media/run-dev-spaces-windows-containers/sample-app.png)

## <a name="next-steps"></a>다음 단계

Azure Dev Spaces를 통해 여러 컨테이너에서 더 복잡한 애플리케이션을 개발할 수 있는 방법 및 사용자가 다양한 환경에서 다양한 코드 버전이나 분기로 작업하여 공동 개발을 간소화하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [Azure Dev Spaces에서 팀 개발][team-development-qs]

[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[helm-installed]: https://helm.sh/docs/intro/install/
[sample-application]: https://github.com/Azure/dev-spaces/tree/master/samples/existingWindowsBackend
[sample-application-toleration-example]: https://github.com/Azure/dev-spaces/blob/master/samples/existingWindowsBackend/mywebapi-windows/charts/templates/deployment.yaml#L24-L27
[team-development-qs]: ../quickstart-team-development.md
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[team-development]: ../team-development-netcore.md
[using-taints]: ../../aks/use-multiple-node-pools.md#schedule-pods-using-taints-and-tolerations
[windows-container-cli]: ../../aks/windows-container-cli.md
