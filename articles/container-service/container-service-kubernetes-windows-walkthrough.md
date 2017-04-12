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
ms.date: 03/20/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: eb3af43b8a13eaaebfa9147848383ff889119d97
ms.lasthandoff: 04/03/2017


---

# <a name="get-started-with-kubernetes-and-windows-containers-in-container-service"></a>Container Service에서 Kubernetes 및 Windows 컨테이너 시작


이 문서는 Windows 컨테이너를 실행하기 위해 Windows 노드를 포함하는 Azure Container Service에서 Kubernetes 클러스터를 만드는 방법을 보여줍니다. 

> [!NOTE]
> Azure Container Service에서 Kubernetes를 통한 Windows 컨테이너에 대한 지원은 미리 보기 상태입니다. Azure Portal 또는 Resource Manager 템플릿을 사용하여 Windows 노드가 있는 Kubernetes 클러스터를 만듭니다. 이 기능은 현재 Azure CLI 2.0에서 지원되지 않습니다.
>



다음 이미지는 Linux 마스터 노드 한 개와 Windows 에이전트 노드가 두 개 있는 Azure Container Service의 Kubernetes 클러스터 아키텍처를 보여줍니다. 

* Linux 마스터는 Kubernetes REST API 역할을 하며 포트 22에서 SSH로 또는 포트 443에서 `kubectl`로 액세스할 수 있습니다. 
* Windows 에이전트 노드는 Azure 가용성 집합에서 그룹화되고 컨테이너를 실행합니다. Windows 노드는 마스터 노드를 경유하여 RDP SSH 터널을 통해 액세스할 수 있습니다. Azure Load Balancer 규칙은 노출된 서비스에 따라 클러스터에 동적으로 추가됩니다.


![Azure의 Kubernetes 클러스터 이미지](media/container-service-kubernetes-windows-walkthrough/kubernetes-windows.png)

모든 VM은 동일한 개인 가상 네트워크에 있으며 서로 완벽하게 액세스할 수 있습니다. 모든 VM에서 kubelet, Docker 및 프록시가 실행됩니다.

## <a name="prerequisites"></a>필수 조건


* **SSH RSA 공개 키**: 포털 또는 Azure 빠른 시작 템플릿 중 하나를 통해 배포하는 경우 Azure Container Service 가상 컴퓨터에 대한 인증을 위해 SSH RSA 공개 키를 제공해야 합니다. SSH(보안 셸) RSA 키를 만들려면 [OS X 및 Linux](../virtual-machines/linux/mac-create-ssh-keys.md) 또는 [Windows](../virtual-machines/linux/ssh-from-windows.md) 지침을 참조하세요. 

* **서비스 주체 클라이언트 ID 및 비밀**: 자세한 내용 및 지침은 [Kubernetes 클러스터의 서비스 주체 정보](container-service-kubernetes-service-principal.md)를 참조하세요.




## <a name="create-the-cluster"></a>클러스터 만들기

Azure Portal을 사용하여 Windows 에이전트 노드가 있는 [Kubernetes 클러스터를 만들](container-service-deployment.md#create-a-cluster-by-using-the-azure-portal) 수 있습니다. 클러스터를 만드는 경우 다음 설정에 유의합니다.

* **기본** 블레이드의 **Orchestrator**에서 **Kubernetes**를 선택합니다. 

  ![Kubernetes Orchestrator 선택](media/container-service-kubernetes-windows-walkthrough/portal-select-kubernetes.png)

* **마스터 구성** 블레이드에서 Linux 마스터 노드에 대한 서비스 주체 자격 증명 및 사용자 자격 증명을 입력합니다. 1, 3 또는 5개의 마스터를 선택합니다.

* **에이전트 구성** 블레이드의 **운영 체제**에서 **Windows(미리 보기)**를 선택합니다. Windows 에이전트 노드에 대한 관리자 자격 증명을 입력합니다.

  ![Windows 에이전트 선택](media/container-service-kubernetes-windows-walkthrough/portal-select-windows.png)

자세한 내용은 [Azure Container Service 클러스터 배포](container-service-deployment.md)를 참조하세요.

## <a name="connect-to-the-cluster"></a>클러스터에 연결

`kubectl` 명령줄 도구를 사용하여 로컬 컴퓨터에서 Kubernetes 클러스터의 마스터 노드에 연결합니다. `kubectl`을 설치하고 설정하는 단계는 [Azure Container Service 클러스터에 연결](container-service-connect.md#connect-to-a-kubernetes-cluster)을 참조하세요. `kubectl` 명령을 사용하여 Kubernetes 웹 UI에 액세스하고 Windows 컨테이너 워크로드를 만들고 관리할 수 있습니다.

## <a name="create-your-first-kubernetes-service"></a>첫 번째 Kubernetes 서비스 만들기

`kubectl`을 사용하여 클러스터를 만들고 연결한 후 기본 Windows 웹앱을 시작해 보고 인터넷에 노출할 수 있습니다. 이 예제에서는 YAML 파일을 사용하여 컨테이너 리소스를 지정한 다음 `kubctl apply`를 사용하여 만듭니다.

1. 노드 목록을 보려면 `kubectl get nodes`를 입력합니다. 노드의 전체 정보를 원하는 경우 다음을 입력합니다.  

    ```
    kubectl get nodes -o yaml
    ```

2. `simpleweb.yaml`라는 파일을 만들고 다음을 복사합니다. 이 파일은 [Docker 허브](https://hub.docker.com/r/microsoft/windowsservercore/)의 Windows Server 2016 서버 코어 기본 OS 이미지를 사용하여 웹앱을 설정합니다.  

```yaml
  apiVersion: v1
  kind: Service
  metadata:
    name: win-webserver
    labels:
      app: win-webserver
  spec:
    ports:
      # the port that this service should serve on
    - port: 80
      targetPort: 80
    selector:
      app: win-webserver
    type: LoadBalancer
  ---
  apiVersion: extensions/v1beta1
  kind: Deployment
  metadata:
    labels:
      app: win-webserver
    name: win-webserver
  spec:
    replicas: 1
    template:
      metadata:
        labels:
          app: win-webserver
        name: win-webserver
      spec:
        containers:
        - name: windowswebserver
          image: microsoft/windowsservercore
          command:
          - powershell.exe
          - -command
          - "<#code used from https://gist.github.com/wagnerandrade/5424431#> ; $$listener = New-Object System.Net.HttpListener ; $$listener.Prefixes.Add('http://*:80/') ; $$listener.Start() ; $$callerCounts = @{} ; Write-Host('Listening at http://*:80/') ; while ($$listener.IsListening) { ;$$context = $$listener.GetContext() ;$$requestUrl = $$context.Request.Url ;$$clientIP = $$context.Request.RemoteEndPoint.Address ;$$response = $$context.Response ;Write-Host '' ;Write-Host('> {0}' -f $$requestUrl) ;  ;$$count = 1 ;$$k=$$callerCounts.Get_Item($$clientIP) ;if ($$k -ne $$null) { $$count += $$k } ;$$callerCounts.Set_Item($$clientIP, $$count) ;$$header='<html><body><H1>Windows Container Web Server</H1>' ;$$callerCountsString='' ;$$callerCounts.Keys | % { $$callerCountsString+='<p>IP {0} callerCount {1} ' -f $$_,$$callerCounts.Item($$_) } ;$$footer='</body></html>' ;$$content='{0}{1}{2}' -f $$header,$$callerCountsString,$$footer ;Write-Output $$content ;$$buffer = [System.Text.Encoding]::UTF8.GetBytes($$content) ;$$response.ContentLength64 = $$buffer.Length ;$$response.OutputStream.Write($$buffer, 0, $$buffer.Length) ;$$response.Close() ;$$responseStatus = $$response.StatusCode ;Write-Host('< {0}' -f $$responseStatus)  } ; "
        nodeSelector:
          beta.kubernetes.io/os: windows
  ```

      
> [!NOTE] 
> 구성에 `type: LoadBalancer`가 포함됩니다. 이 설정으로 인해 서비스가 Azure Load Balancer를 통해 인터넷에 노출됩니다. 자세한 내용은 [Azure Container Service의 Kubernetes 클러스터에서 컨테이너 부하 분산](container-service-kubernetes-load-balancing.md)을 참조하세요.
>

## <a name="start-the-application"></a>응용 프로그램 시작

1. 응용 프로그램을 시작하려면 다음을 입력합니다.  

    ```
    kubectl apply -f simpleweb.yaml
    ```  
  
  
2. 서비스 배포(약 30초 소요됨)를 확인하려면 다음을 입력합니다.  

    ```
    kubectl get pods
    ```

3. 서비스가 실행된 후 서비스의 내부 및 외부 IP 주소를 보려면 다음을 입력합니다.

    ```
    kubectl get svc
    ``` 
  
    ![Windows 서비스의 IP 주소](media/container-service-kubernetes-windows-walkthrough/externalipa.png)

    외부 IP 주소 추가에는 몇 분이 소요됩니다. 부하 분산 장치가 외부 주소를 구성하기 전에는 `<pending>`으로 표시됩니다.

4. 외부 IP 주소를 사용할 수 있게 되면 서비스를 웹 브라우저에서 열 수 있습니다.

    ![브라우저의 Windows 서버 앱](media/container-service-kubernetes-windows-walkthrough/wincontainerwebserver.png)


## <a name="access-the-windows-nodes"></a>Windows 노드 액세스
Windows 노드는 원격 데스크톱 연결을 통해 로컬 Windows 컴퓨터에서 액세스할 수 있습니다. 마스터 노드를 통해 RDP SSH 터널을 사용하는 것이 좋습니다. 

Windows에서 SSH 터널을 만드는 방법은 여러 가지가 있습니다. 이 섹션에서는 PuTTY를 사용하여 터널을 만드는 방법을 설명합니다.

1. Windows 시스템으로 [PuTTY를 다운로드합니다](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

2. 응용 프로그램을 실행합니다.

3. 클러스터 관리 사용자 이름 및 클러스터에서 첫 번째 마스터의 공용 DNS 이름으로 구성된 호스트 이름을 입력합니다. **호스트 이름**은 `adminuser@PublicDNSName`과 유사합니다. **포트**에 22를 입력합니다.

  ![PuTTY 구성 1](media/container-service-kubernetes-windows-walkthrough/putty1.png)

4. **SSH > 인증**을 선택합니다. 인증을 위한 개인 키 파일(.ppk 형식)에 경로를 추가합니다. [PuTTYgen](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)과 같은 도구를 사용하여 클러스터를 만드는 데 사용되는 SSH 키에서 이 파일을 생성할 수 있습니다.

  ![PuTTY 구성 2](media/container-service-kubernetes-windows-walkthrough/putty2.png)

5. **SSH > 터널**을 선택하고 전달된 포트를 구성합니다. 로컬 Windows 컴퓨터에서 포트 3389를 이미 사용 중이기 때문에 다음 설정을 사용하여 Windows 노드 0 및 Windows 노드 1에 도달하는 것이 좋습니다. (추가 Windows 노드에 대해 이런 패턴을 반복합니다.)

    **Windows 노드 0**

    * **원본 포트:** 3390
    * **대상:** 10.240.245.5:3389

    **Windows 노드 1**

    * **원본 포트:** 3391
    * **대상:** 10.240.245.6:3389

    ![Windows RDP 터널 이미지](media/container-service-kubernetes-windows-walkthrough/rdptunnels.png)

6. 완료하면 **세션 > 저장**을 클릭하여 연결 구성을 저장합니다.

7. PuTTY 세션에 연결하려면 **열기**를 클릭합니다. 마스터 노드에 대한 연결을 완료합니다.

8. 원격 데스크톱 연결을 시작합니다. 첫 번째 Windows 노드에 연결하려면, **컴퓨터**에 대해 `localhost:3390`을 지정하고 **연결**을 클릭합니다. (두 번째 노드에 연결하려면 `localhost:3390`을 지정하고 나머지를 수행합니다.) 연결을 완료하려면 배포 중 구성한 로컬 Windows 관리자 암호를 제공합니다.


## <a name="next-steps"></a>다음 단계

여기에는 Kubernetes에 대한 자세한 내용을 제공하는 권장 링크가 나와 있습니다.

* [Kubernetes Bootcamp](https://kubernetesbootcamp.github.io/kubernetes-bootcamp/index.html) - 컨테이너화된 응용프로그램을 배포, 크기 조정, 업데이트 및 디버그하는 방법을 보여 줍니다.
* [Kubernetes 사용자 가이드](http://kubernetes.io/docs/user-guide/) - 기존 Kubernetes 클러스터에서 실행되는 프로그램에 대한 정보를 제공합니다.
* [Kubernetes 예제](https://github.com/kubernetes/kubernetes/tree/master/examples) - Kubernetes로 실제 응용 프로그램을 실행하는 방법에 대한 예제를 제공합니다.
