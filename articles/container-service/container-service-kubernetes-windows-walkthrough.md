---
title: "Windows용 Azure Kubernetes 클러스터 | Microsoft Docs"
description: "Azure Container Service에서 Windows 컨테이너용 Kubernetes 클러스터 배포 및 시작"
services: container-service
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: acs, azure-container-service, kubernetes
keywords: 
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/04/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 18d4994f303a11e9ce2d07bc1124aaedf570fc82
ms.openlocfilehash: 4e730b65a98af05ea00c5f8ebd9914e3367b66a7
ms.contentlocale: ko-kr
ms.lasthandoff: 05/09/2017


---

# <a name="get-started-with-kubernetes-and-windows-containers-in-container-service"></a>Container Service에서 Kubernetes 및 Windows 컨테이너 시작


이 문서에서는 Windows 컨테이너를 실행하기 위해 Windows 노드를 포함하는 Azure Container Service에서 Kubernetes 클러스터를 만드는 방법을 보여줍니다. `az acs` Azure CLI 2.0 명령을 시작하여 Azure Container Service에서 Kubernetes 클러스터를 만듭니다. 그런 다음 Kubernetes `kubectl` 명령줄 도구를 사용하여 Docker 이미지에서 빌드된 Windows 컨테이너로 작업합니다. 

> [!NOTE]
> Azure Container Service에서 Kubernetes를 통한 Windows 컨테이너에 대한 지원은 미리 보기 상태입니다. 
>



다음 이미지는 Linux 마스터 노드 한 개와 Windows 에이전트 노드가 두 개 있는 Azure Container Service의 Kubernetes 클러스터 아키텍처를 보여줍니다. 

![Azure의 Kubernetes 클러스터 이미지](media/container-service-kubernetes-windows-walkthrough/kubernetes-windows.png)

* Linux 마스터는 Kubernetes REST API 역할을 하며 포트 22에서 SSH로 또는 포트 443에서 `kubectl`로 액세스할 수 있습니다. 
* Windows 에이전트 노드는 Azure 가용성 집합에서 그룹화되고 컨테이너를 실행합니다. Windows 노드는 마스터 노드를 경유하여 RDP SSH 터널을 통해 액세스할 수 있습니다. Azure Load Balancer 규칙은 노출된 서비스에 따라 클러스터에 동적으로 추가됩니다.



모든 VM은 동일한 개인 가상 네트워크에 있으며 서로 완벽하게 액세스할 수 있습니다. 모든 VM에서 kubelet, Docker 및 프록시가 실행됩니다.

자세한 배경 정보는 [Azure Container Service 소개](container-service-intro.md) 및 [Kubernetes 설명서](https://kubernetes.io/docs/home/)를 참조하세요.

## <a name="prerequisites"></a>필수 조건
Azure CLI 2.0을 사용하여 Azure Container Service 클러스터를 만들려면 다음을 수행해야 합니다.
* Azure 계정([무료 평가판 받기](https://azure.microsoft.com/pricing/free-trial/))이 있어야 합니다.
* [Azure CLI 2.0](/cli/azure/install-az-cli2)을 설치하고 로그인해야 합니다.

Kubernetes 클러스터에는 다음 사항도 필요합니다. 이러한 항목을 미리 준비하거나 `az acs create` 명령 옵션을 사용하여 클러스터 배포 시 자동으로 생성할 수 있습니다. 

* **SSH RSA 공용 키**: SSH(보안 셸) RSA 키를 만들려면 [macOS 및 Linux](../virtual-machines/linux/mac-create-ssh-keys.md) 또는 [Windows](../virtual-machines/linux/ssh-from-windows.md) 지침을 참조하세요. 

* **서비스 주체 클라이언트 ID 및 비밀**: Azure Active Directory 서비스 주체를 만드는 단계 및 추가 정보는 [Kubernetes 클러스터에 대한 서비스 주체 정보](container-service-kubernetes-service-principal.md)를 참조하세요.

이 문서의 명령 예제에서는 SSH 키 및 서비스 주체를 자동으로 생성합니다.
  
## <a name="create-your-kubernetes-cluster"></a>Kubernetes 클러스터 만들기

클러스터를 만드는 Azure CLI 2.0 명령은 다음과 같습니다. 

### <a name="create-a-resource-group"></a>리소스 그룹 만들기
Azure Container Service를 [사용할 수 있는](https://azure.microsoft.com/regions/services/) 위치에 리소스 그룹을 만듭니다. 다음 명령은 *westus* 위치에 *myKubernetesResourceGroup*이라는 리소스 그룹을 만듭니다.

```azurecli
az group create --name=myKubernetesResourceGroup --location=westus
```

### <a name="create-a-kubernetes-cluster-with-windows-agent-nodes"></a>Windows 에이전트 노드가 있는 Kubernetes 클러스터 만들기

`--orchestrator-type=kubernetes`에서 `az acs create` 명령 및 `--windows` 에이전트 옵션을 사용하여 리소스 그룹에서 Kubernetes 클러스터를 만듭니다. 명령 구문의 경우 `az acs create` [도움말](/cli/azure/acs#create)을 참조하세요.

다음 명령은 관리 노드 및 지정된 인증서의 *myPrefix*를 DNS 접두사로 포함하는 *myKubernetesClusterName*이라는 컨테이너 서비스 클러스터를 만들어서 Windows 노드에 도달합니다. 이 버전의 명령은 Kubernetes 클러스터에 대한 SSH RSA 키 및 서비스 주체를 자동으로 생성합니다.


```azurecli
az acs create --orchestrator-type=kubernetes \
    --resource-group myKubernetesResourceGroup \
    --name=myKubernetesClusterName \
    --dns-prefix=myPrefix \
    --agent-count=2 \
    --generate-ssh-keys \
    --windows --admin-username myWindowsAdminName \
    --admin-password myWindowsAdminPassword
```

몇 분 후 명령이 완료되면 Kubernetes 클러스터가 작동해야 합니다.

> [!IMPORTANT]
> 계정에 Azure AD 서비스 주체를 만들 수 있는 권한이 없으면 명령은 `Insufficient privileges to complete the operation.`과 유사한 오류를 생성합니다. 자세한 내용은 [Kubernetes 클러스터의 서비스 주체 정보](container-service-kubernetes-service-principal.md)를 참조하세요. 
> 

## <a name="connect-to-the-cluster-with-kubectl"></a>kubectl을 사용하여 클러스터에 연결

클라이언트 컴퓨터에서 Kubernetes 클러스터에 연결하려면 Kubernetes 명령줄 클라이언트인 [`kubectl`](https://kubernetes.io/docs/user-guide/kubectl/)을 사용합니다. 

`kubectl`이 로컬로 설치되지 않은 경우 `az acs kubernetes install-cli`을 사용하여 설치할 수 있습니다. ([Kubernetes 사이트](https://kubernetes.io/docs/tasks/kubectl/install/)에서 다운로드할 수도 있습니다.)

**Linux 또는 macOS**

```azurecli
sudo az acs kubernetes install-cli
```

**Windows**
```azurecli
az acs kubernetes install-cli
```

> [!TIP]
> 기본적으로 이 명령은 `kubectl` 이진 파일을 Linux 또는 macOS 시스템의 `/usr/local/bin/kubectl`이나 Windows의 `C:\Program Files (x86)\kubectl.exe`에 설치합니다. 다른 설치 경로를 지정하려면 `--install-location` 매개 변수를 사용합니다.
>
> `kubectl`이 설치된 후에는 해당 디렉터리가 시스템 경로에 있는지를 확인하거나 해당 경로에 추가합니다. 


그런 후 다음 명령을 실행하여 마스터 Kubernetes 클러스터 구성을 로컬 `~/.kube/config` 파일에 다운로드합니다.

```azurecli
az acs kubernetes get-credentials --resource-group=myKubernetesResourceGroup --name=myKubernetesClusterName
```

이제 컴퓨터의 클러스터에 액세스할 준비가 되었습니다. 다음을 실행해 보세요.

```bash
kubectl get nodes
```

클러스터의 컴퓨터 목록이 표시되는지 확인하세요.

![Kubernetes 클러스터에서 실행 중인 노드](media/container-service-kubernetes-windows-walkthrough/kubectl-get-nodes.png)

## <a name="create-your-first-kubernetes-service"></a>첫 번째 Kubernetes 서비스 만들기

클러스터를 만들고 `kubectl`과 연결한 후에 Docker 컨테이너에서 Windows 웹앱을 시작해 보고 인터넷에 노출시킵니다. 이 기본 예제에서는 JSON 파일을 사용하여 Microsoft IIS(인터넷 정보 서버) 컨테이너를 지정하고 `kubctl apply`를 사용하여 만듭니다. 

1. `iis.json`라는 로컬 파일을 만들고 다음을 복사합니다. 이 파일은 Kubernetes가 [Docker Hub](https://hub.docker.com/r/microsoft/iis/)의 공용 이미지를 사용하여 Windows Server 2016 Server Core에서 IIS를 실행하도록 지시합니다. 컨테이너는 포트 80을 사용하지만, 처음에 클러스터 네트워크 내에서만 액세스할 수 있습니다.

  ```JSON
  {
    "apiVersion": "v1",
    "kind": "Pod",
    "metadata": {
      "name": "iis",
      "labels": {
        "name": "iis"
      }
    },
    "spec": {
      "containers": [
        {
          "name": "iis",
          "image": "microsoft/iis",
          "ports": [
            {
            "containerPort": 80
            }
          ]
        }
      ],
      "nodeSelector": {
        "beta.kubernetes.io/os": "windows"
      }
    }
  }
  ```
2. 응용 프로그램을 시작하려면 다음을 입력합니다.  
  
  ```bash
  kubectl apply -f iis.json
  ```  
3. 컨테이너의 배포를 추적하려면 다음을 입력합니다.  
  ```bash
  kubectl get pods
  ```
  컨테이너를 배포하는 동안 상태는 `ContainerCreating`입니다. 

  ![ContainerCreating 상태인 IIS 컨테이너](media/container-service-kubernetes-windows-walkthrough/iis-pod-creating.png)   

  IIS 이미지의 크기 때문에 컨테이너에 `Running` 상태를 입력하는 데 몇 분 정도가 걸릴 수 있습니다.

  ![실행 중인 IIS 컨테이너](media/container-service-kubernetes-windows-walkthrough/iis-pod-running.png)

4. 전 세계에 컨테이너를 노출하려면 다음 명령을 입력합니다.

  ```bash
  kubectl expose pods iis --port=80 --type=LoadBalancer
  ```

  Kubernetes는 이 명령을 통해 공용 IP 주소를 사용하여 Azure Load Balancer 규칙을 생성합니다. 변경 내용이 부하 분산 장치로 전파되는 데 몇 분 정도 걸립니다. 자세한 내용은 [Azure Container Service의 Kubernetes 클러스터에서 컨테이너 부하 분산](container-service-kubernetes-load-balancing.md)을 참조하세요.

5. 다음 명령을 실행하여 서비스의 상태를 확인합니다.

  ```bash
  kubectl get svc
  ```

  처음에는 IP 주소가 `pending`으로 표시됩니다.

  ![보류 중인 외부 IP 주소](media/container-service-kubernetes-windows-walkthrough/iis-svc-expose.png)

  몇 분 후에는 IP 주소가 다음과 같이 설정됩니다.
  
  ![IIS의 외부 IP 주소](media/container-service-kubernetes-windows-walkthrough/iis-svc-expose-public.png)


6. 외부 IP 주소가 제공된 후에 브라우저에서 해당 IP로 이동할 수 있습니다.

  ![IIS로 이동하는 이미지](media/container-service-kubernetes-windows-walkthrough/kubernetes-iis.png)  

7. IIS 포드를 삭제하려면 다음을 입력합니다.

  ```bash
  kubectl delete pods iis
  ```

## <a name="next-steps"></a>다음 단계

* Kubernetes UI를 사용하려면 `kubectl proxy` 명령을 실행합니다. 그런 다음 http://localhost:8001/ui로 이동합니다.

* 사용자 지정 IIS 웹 사이트를 빌드하고 Windows 컨테이너에서 실행하는 단계는 [Docker Hub](https://hub.docker.com/r/microsoft/iis/)의 지침을 참조하세요.

* PuTTy를 사용하여 마스터에 대한 RDP SSH 터널을 통해 Windows 노드에 액세스하려면 [ACS 엔진 설명서](https://github.com/Azure/acs-engine/blob/master/docs/ssh.md#create-port-80-tunnel-to-the-master)를 참조하세요. 

