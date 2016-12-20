---
title: "Kubernetes를 사용하여 Azure Container Service 클러스터 배포 | Microsoft Docs"
description: "Kubernetes를 사용하여 Azure Container Service 배포"
services: container-service
documentationcenter: 
author: anhowe
manager: timlt
editor: 
tags: acs, azure-container-service, kubernetes
keywords: 
ms.assetid: 8da267e8-2aeb-4c24-9a7a-65bdca3a82d6
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2016
ms.author: anhowe
translationtype: Human Translation
ms.sourcegitcommit: 0989c85e4b7c73a8b7b335254e4af90ef34ddc01
ms.openlocfilehash: 8c10da9d2363f0938d821b59cc4fd58b53f9ed17


---

# <a name="microsoft-azure-container-service-engine---kubernetes-walkthrough"></a>Microsoft Azure Container Service 엔진 - Kubernetes 연습

## <a name="deployment"></a>배포

다음은 간단한 Kubernetes 클러스터를 배포하는 단계입니다.

1. [ssh 키 생성](https://github.com/Azure/azure-quickstart-templates/blob/master/101-acs-dcos/docs/SSHKeyManagement.md#ssh-key-generation)
3. [서비스 주체 생성](https://github.com/Azure/acs-engine/blob/master/docs/serviceprincipal.md)
4. [추가 정보에서 Azure에 배포 단추](https://github.com/Azure/azure-quickstart-templates/blob/master/101-acs-kubernetes/README.md)를 클릭하고 필드 입력

## <a name="walkthrough"></a>연습

Kubernetes 클러스터가 만들어지면 다음을 포함하는 리소스 그룹이 생성됩니다.

1. 포트 22의 SSH 또는 포트 443의 kubectl에서 액세스할 수 있는 1개 마스터

2. 가용성 집합의 노드 집합.  노드는 마스터를 통해 액세스할 수 있습니다.  이 작업을 수행하는 방법의 예는 [에이전트 전달](https://github.com/Azure/azure-quickstart-templates/blob/master/101-acs-dcos/docs/SSHKeyManagement.md#key-management-and-agent-forwarding-with-windows-pageant)을 참조하세요.

다음 이미지는 1개의 마스터와 2개의 에이전트가 있는 컨테이너 서비스 클러스터 아키텍처를 보여 줍니다.

![Azure의 Kubernetes 클러스터 이미지](media/container-service-kubernetes-walkthrough/kubernetes.png)

위의 그림에서 다음과 같은 부분을 확인할 수 있습니다.

1. **마스터 구성 요소** - 마스터는 Kubernetes 스케줄러, api 서버 및 컨트롤러 관리자를 실행합니다.  포트 443은 kubectl cli를 사용한 원격 관리를 위해 노출됩니다.
2. **노드** - Kubernetes 노드는 가용성 집합에서 실행됩니다.  Azure 부하 분산 장치는 노출된 서비스에 따라 클러스터에 동적으로 추가됩니다. 
3. **공통 구성 요소** - 모든 VM에서 kubelet, Docker 및 Proxy가 실행됩니다.
4. **네트워킹** - 모든 VM에 10.240.0.0/16 네트워크의 IP 주소가 할당됩니다.  각 VM에는 pod당 IP를 사용하도록 설정하는 해당 pod CIDR에 대해 /24 서브넷이 할당됩니다.  각 VM에서 실행되는 프록시는 서비스 네트워크 10.0.0.0/16을 구현합니다.

모든 VM은 동일한 개인 VNET에 있으며 서로 완벽하게 액세스할 수 있습니다.

## <a name="create-your-first-kubernetes-service"></a>첫 번째 Kubernetes 서비스 만들기

이 연습을 완료하면 다음 방법을 알 수 있게 됩니다.
 * SSH 통해 Kubernetes 클러스터에 액세스
 * 간단한 Docker 응용 프로그램 배포 및 전 세계에 공개
 * Kube 구성 파일의 위치 및 Kubernetes 클러스터에 원격으로 액세스하는 방법
 * `kubectl exec`를 사용하여 컨테이너에서 명령 실행 
 * Kubernetes 대시보드에 최종적으로 액세스

1. 템플릿을 배포한 후 마스터 FQDN(정규화된 도메인 이름)을 기록해 둡니다.
   1. Powershell 또는 CLI를 사용하는 경우 출력 매개 변수가 'masterFQDN'이라는 OutputsString 섹션에 있습니다.
   2. 포털을 사용하는 경우 ContainerService 리소스의 개요 블레이드로 이동하여 "마스터 FQDN"을 복사합니다.
     
   ![Docker 크기 조정 이미지](media/container-service-kubernetes-walkthrough/portal-kubernetes-outputs.png)

2. 1단계에서 얻은 마스터 FQDN에 대한 SSH

3. 노드 및 실행 중인 pod를 탐색합니다.
  1. 노드 목록을 보려면 `kubectl get nodes`를 입력합니다.  노드의 전체 정보를 원하는 경우 `-o yaml`을 추가하여 `kubectl get nodes -o yaml`을 입력합니다.
  2. 실행 중인 pod 목록을 보려면 `kubectl get pods --all-namespaces`를 입력합니다.

4. `kubectl run nginx --image nginx`를 입력하여 첫 번째 Docker 이미지를 시작합니다.  이렇게 하면 노드 중 하나에 있는 pod에서 nginx Docker 컨테이너가 시작됩니다.

5. nginx 배포의 전체 정보를 보려면 `kubectl get pods -o yaml`을 입력합니다. 호스트 IP 및는 podIP 볼 수 있습니다.  호스트의 포드 CIDR에서 pod IP가 할당됩니다.  nginx 출력을 보려면 pod ip에 대해 curl을 실행합니다. 예를 들면 다음과 같습니다. `curl 10.244.1.4`

  ![podIP에 대한 curl 이미지](media/container-service-kubernetes-walkthrough/kubernetes-nginx1.png)

6. 다음 단계는 개인 서비스 네트워크 10.0.0.0/16에서 Kubernetes 서비스로 nginx 배포를 노출하는 것입니다.
  1. 명령 `kubectl expose deployment nginx --port=80`을 실행하여 서비스를 노출합니다.
  2. 명령 `kubectl get service`를 실행하여 서비스 IP를 얻습니다.
  3. IP에 대해 curl을 실행합니다. 예를 들면 다음과 같습니다. `curl 10.0.105.199`

  ![서비스 IP에 대한 curl 이미지](media/container-service-kubernetes-walkthrough/kubernetes-nginx2.png)

7. 마지막 단계는 전 세계에 서비스를 노출하는 것입니다.  이 작업을 위해서는 서비스 유형을 `ClusterIP`에서 `LoadBalancer`로 변경합니다.
  1. 명령 `kubectl edit svc/nginx`를 실행하여 서비스를 편집합니다.
  2. `type`을 `ClusterIP`에서 `LoadBalancer`로 변경한 후 저장합니다.  이제 Kubernetes는 공용 IP를 사용하여 Azure 부하 분산 장치를 만듭니다.
  3. 이렇게 변경하는 데는 2~3분 정도가 소요됩니다.  "pending"에서 외부 IP로의 서비스 변경을 확인하려면 `watch 'kubectl get svc'`를 입력합니다.

  ![pending에서 외부 IP로의 전환을 확인하는 이미지](media/container-service-kubernetes-walkthrough/kubernetes-nginx3.png)

  4. 외부 IP가 표시되면 브라우저에서 해당 IP로 이동할 수 있습니다.

  ![nginx 찾아보기 이미지](media/container-service-kubernetes-walkthrough/kubernetes-nginx4.png)  

8. 이 연습의 다음 단계에서는 Kubernetes 클러스터를 원격으로 관리하는 방법을 보여 줍니다.  먼저 컴퓨터에 Kubectl을 다운로드하고 경로에 추가합니다.
  * [Windows Kubectl](https://storage.googleapis.com/kubernetes-release/release/v1.4.5/bin/windows/amd64/kubectl.exe)
  * [OSX Kubectl](https://storage.googleapis.com/kubernetes-release/release/v1.4.5/bin/darwin/amd64/kubectl)
  * [Linux](https://storage.googleapis.com/kubernetes-release/release/v1.4.5/bin/linux/amd64/kubectl)

9. Kubernetes 마스터의 홈 디렉터리 ~/.kube/config 아래에는 원격 액세스를 위한 kube 구성 파일이 포함되어 있습니다.  컴퓨터에 이 파일을 다운로드하고, KUBECONFIG 환경 변수를 설정하고 kubectl을 실행하여 클러스터에 연결할 수 있는지 확인합니다.
  * Windows에서는 [putty](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)의 pscp를 사용합니다.  [pageant](https://github.com/Azure/acs-engine/blob/master/docs/ssh.md#key-management-and-agent-forwarding-with-windows-pageant)를 통해 인증서가 노출되었는지 확인합니다.
  ```
  # MASTERFQDN is obtained in step1
  pscp azureuser@MASTERFQDN:.kube/config .
  SET KUBECONFIG=%CD%\config
  kubectl get nodes
  ```
  * OS X 또는 Linux:
  ```
  # MASTERFQDN is obtained in step1
  scp azureuser@MASTERFQDN:.kube/config .
  export KUBECONFIG=`pwd`/config
  kubectl get nodes
  ```
10. 다음 단계에서는 원격 Docker 컨테이너에서 원격으로 명령을 실행하는 방법을 보여 줍니다.
  1. `kubectl get pods`를 실행하여 nginx pod의 이름을 표시합니다.
  2. pod 이름을 사용하여 pod에 대해 원격 명령을 실행할 수 있습니다.  예: `kubectl exec nginx-701339712-retbj date`
  3. 원격 bash 세션을 실행해 봅니다. 예: `kubectl exec nginx-701339712-retbj -it bash`.  다음 스크린샷에서는 다음 명령을 보여 줍니다.

  ![podIP에 대한 curl 이미지](media/container-service-kubernetes-walkthrough/kubernetes-remote.png)

11. 이 자습서의 마지막 단계에서는 대시보드를 보여 줍니다.
  1. 프록시에 직접 연결하려면 `kubectl proxy`를 실행합니다.
  2. 브라우저에서 [대시보드](http://127.0.0.1:8001/api/v1/proxy/namespaces/kube-system/services/kubernetes-dashboard/#/workload?namespace=_all)로 이동합니다.
  3. pod 및 서비스를 탐색합니다.
  ![Kubernetes 대시보드 이미지](media/container-service-kubernetes-walkthrough/kubernetes-dashboard.png)

# <a name="learning-more"></a>자세히 알아보기

여기에는 Kubernetes에 대한 자세한 내용을 제공하는 권장 링크가 나와 있습니다.

1. [Azure Kubernetes 설명서](https://azure.microsoft.com/en-us/documentation/services/container-service/)

## <a name="kubernetes-community-documentation"></a>Kubernetes 커뮤니티 설명서

1. [Kubernetes Bootcamp](https://kubernetesbootcamp.github.io/kubernetes-bootcamp/index.html) - 컨테이너화된 응용프로그램을 배포, 크기 조정, 업데이트 및 디버그하는 방법을 보여 줍니다.
2. [Kubernetes 사용자 안내서](http://kubernetes.io/docs/user-guide/) - 기존 Kubernetes 클러스터에서 실행되는 프로그램에 대한 정보를 제공합니다.
3. [Kubernetes 예제](https://github.com/kubernetes/kubernetes/tree/master/examples) - Kubernetes로 실제 응용 프로그램을 실행하는 방법에 대한 여러 가지 예제를 제공합니다.



<!--HONumber=Nov16_HO3-->


