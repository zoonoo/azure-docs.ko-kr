---
title: "Linux에서 Azure Service Fabric 컨테이너 응용 프로그램 만들기 | Microsoft Docs"
description: "이 빠른 시작에서는 Azure Service Fabric에서 첫 번째 Linux 컨테이너 응용 프로그램을 만듭니다.  응용 프로그램을 사용하여 Docker 이미지를 빌드하고, 이미지를 컨테이너 레지스트리로 푸시하고, Service Fabric 컨테이너 응용 프로그램을 빌드하고 배포합니다."
services: service-fabric
documentationcenter: linux
author: suhuruli
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: python
ms.topic: quickstart
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/05/2017
ms.author: suhuruli
ms.custom: mvc
ms.openlocfilehash: f568bdf6ce40ff2d437f3566b66f6dd1478a74fa
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/24/2018
---
# <a name="quickstart-deploy-an-azure-service-fabric-linux-container-application-on-azure"></a>빠른 시작: Azure에서 Azure Service Fabric Linux 컨테이너 응용 프로그램 배포
Azure Service Fabric은 확장성 있고 안정성이 뛰어난 마이크로 서비스 및 컨테이너를 배포 및 관리하기 위한 분산 시스템 플랫폼입니다. 

이 빠른 시작은 Service Fabric 클러스터에 Linux 컨테이너를 배포하는 방법을 보여줍니다. 작업이 완료되면 Python 웹 프런트 엔드 및 Redis 백 엔드로 구성된 선택 응용 프로그램이 Service Fabric 클러스터에서 실행됩니다. 

![quickstartpic][quickstartpic]

이 빠른 시작에서 다음을 수행하는 방법을 알아봅니다.
> [!div class="checklist"]
> * Azure Linux Service Fabric 클러스터에 컨테이너 배포
> * Service Fabric에서 컨테이너 크기 조정 및 장애 조치

## <a name="prerequisite"></a>필수 요소
1. Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/)을 만듭니다.

2. CLI(명령줄 인터페이스)를 로컬로 설치하고 사용하도록 선택한 경우 Azure CLI 버전 2.0.4 이상을 실행하도록 합니다. 버전을 찾으려면 az --version을 실행합니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 2.0 설치](https://docs.microsoft.com/cli/azure/install-azure-cli)를 참조하세요.

## <a name="get-application-package"></a>응용 프로그램 패키지 가져오기
Service Fabric에 컨테이너를 배포하려면 개별 컨테이너 및 응용 프로그램에 대해 설명하는 매니페스트 파일(응용 프로그램 정의)의 집합이 필요합니다.

클라우드 셸에서 git을 사용하여 응용 프로그램 정의의 복사본을 복제합니다.

```bash
git clone https://github.com/Azure-Samples/service-fabric-containers.git

cd service-fabric-containers/Linux/container-tutorial/Voting
```
## <a name="deploy-the-application-to-azure"></a>Azure에 응용 프로그램 배포

### <a name="set-up-your-azure-service-fabric-cluster"></a>Azure Service Fabric Cluster 설정
응용 프로그램을 Azure의 클러스터에 배포하려면 고유한 클러스터를 만듭니다.

파티 클러스터는 Azure에서 호스팅되는 시간이 제한된 체험용 Service Fabric 클러스터이며 누구든지 응용 프로그램을 배포하고 플랫폼에 대해 알아볼 수 있는 Service Fabric 팀에서 실행합니다. 파티 클러스터에 대한 액세스 권한을 얻으려면 [지침에 따릅니다](http://aka.ms/tryservicefabric). 

보안 파티 클러스터에서 관리 작업을 수행하기 위해 Service Fabric Explorer, CLI 또는 Powershell을 사용할 수 있습니다. Service Fabric Explorer를 사용하려면 파티 클러스터 웹 사이트에서 PFX 파일을 다운로드하고, 인증서 저장소(Windows 또는 Mac) 또는 브라우저 자체(Ubuntu)로 인증서를 가져와야 합니다. 파티 클러스터의 자체 서명된 인증서에는 암호가 없습니다. 

Powershell 또는 CLI를 사용하여 관리 작업을 수행하려면 PFX(Powershell) 또는 PEM(CLI)이 필요합니다. PFX를 PEM 파일로 변환하려면 다음 명령을 실행합니다.  

```bash
openssl pkcs12 -in party-cluster-1277863181-client-cert.pfx -out party-cluster-1277863181-client-cert.pem -nodes -passin pass:
```

자체 클러스터를 만드는 방법은 [Azure에서 Service Fabric 클러스터 만들기](service-fabric-tutorial-create-vnet-and-linux-cluster.md)를 참조하세요.

> [!Note]
> 웹 프런트 엔드 서비스는 들어오는 트래픽에 대해 80 포트에서 수신 대기하도록 구성됩니다. 클러스터에 대해 포트가 열려 있는지 확인합니다. Party 클러스터를 사용하는 경우 이 포트가 열려 있습니다.
>

### <a name="install-service-fabric-command-line-interface-and-connect-to-your-cluster"></a>Service Fabric 명령줄 인터페이스 설치 및 클러스터에 연결

Azure CLI를 사용하여 Azure에서 Service Fabric 클러스터에 연결합니다. 끝점은 클러스터의 관리 끝점입니다. 예: `https://linh1x87d1d.westus.cloudapp.azure.com:19080`

```bash
sfctl cluster select --endpoint https://linh1x87d1d.westus.cloudapp.azure.com:19080 --pem party-cluster-1277863181-client-cert.pem --no-verify
```

### <a name="deploy-the-service-fabric-application"></a>Service Fabric 응용 프로그램 배포 
Service Fabric 컨테이너 응용 프로그램은 설명하는 Service Fabric 응용 프로그램 패키지나 Docker Compose를 사용하여 배포할 수 있습니다. 

#### <a name="deploy-using-service-fabric-application-package"></a>Service Fabric 응용 프로그램 패키지를 사용하여 배포
제공된 설치 스크립트를 사용하여 클러스터에 선택 응용 프로그램 정의를 복사하고, 응용 프로그램 유형을 등록하며, 응용 프로그램의 인스턴스를 만듭니다.

```bash
./install.sh
```

#### <a name="deploy-the-application-using-docker-compose"></a>Docker Compose를 사용하여 응용 프로그램 배포
다음 명령을 통해 Docker Compose를 사용하여 Service Fabric 클러스터에 응용 프로그램을 배포 및 설치합니다.
```bash
sfctl compose create --deployment-name TestApp --file-path docker-compose.yml
```

브라우저를 열고 http://\<my-azure-service-fabric-cluster-url>:19080/Explorer에서 Service Fabric Explorer로 이동합니다. 예: `http://linh1x87d1d.westus.cloudapp.azure.com:19080/Explorer` 응용 프로그램 노드를 확장하여 만든 선택 응용 프로그램 유형 및 인스턴스에 대한 항목이 표시되는지 확인합니다.

![Service Fabric Explorer][sfx]

실행 중인 컨테이너에 연결합니다.  `http://linh1x87d1d.westus.cloudapp.azure.com:80`처럼 클러스터의 URL을 가리키는 웹 브라우저를 엽니다. 브라우저에서 선택 응용 프로그램이 표시됩니다.

![quickstartpic][quickstartpic]

## <a name="fail-over-a-container-in-a-cluster"></a>클러스터에서 컨테이너 장애 조치(failover)
Service Fabric은 컨테이너 인스턴스가 오류가 발생해야 하는 클러스터의 다른 노드로 자동으로 이동하는지 확인합니다. 또한 수동으로 컨테이너의 노드를 드레이닝하고 클러스터의 다른 노드로 정상적으로 옮길 수 있습니다. 서비스의 크기를 조정하는 여러 방법이 있습니다. 이 예제에서는 Service Fabric Explorer를 사용합니다.

프런트 엔드 컨테이너를 장애 조치(failover)하려면 다음 단계를 수행합니다.

1. 클러스터에서 Service Fabric Explorer를 엽니다. 예: `http://linh1x87d1d.westus.cloudapp.azure.com:19080/Explorer`
2. 트리 뷰에서 **fabric:/Voting/azurevotefront** 노드를 클릭하고 파티션 노드(GUID로 표현됨)를 확장합니다. 트리 뷰에서 노드 이름을 확인합니다. 컨테이너에서 현재 실행 중인 노드를 보여 줍니다. 예: `_nodetype_4`
3. 트리 뷰에서 **노드** 노드를 확장합니다. 컨테이너를 실행 중인 노드 옆에 있는 줄임표(...)를 클릭합니다.
4. **다시 시작**을 선택하여 해당 노드를 다시 시작하고 다시 시작 작업을 확인합니다. 다시 시작하면 컨테이너를 클러스터의 다른 노드로 장애 조치(failover)합니다.

![sfxquickstartshownodetype][sfxquickstartshownodetype]

## <a name="scale-applications-and-services-in-a-cluster"></a>클러스터에서 응용 프로그램 및 서비스 크기 조정
Service Fabric 서비스는 해당 서비스에 대한 부하를 수용하도록 클러스터 간에 쉽게 크기를 조정할 수 있습니다. 클러스터에서 실행되는 인스턴스 수를 변경하여 서비스 크기를 조정합니다.

웹 프런트 엔드 서비스의 크기를 조정하려면 다음 단계를 수행합니다.

1. 클러스터에서 Service Fabric Explorer를 엽니다(예: `http://linh1x87d1d.westus.cloudapp.azure.com:19080`).
2. 트리 뷰에서 **fabric:/Voting/azurevotefront** 노드 옆에 있는 줄임표(...)를 클릭하고 **서비스 크기 조정**을 선택합니다.

    ![containersquickstartscale][containersquickstartscale]

  이제 웹 프런트 엔드 서비스의 인스턴스 수를 조정하도록 선택할 수 있습니다.

3. 이 수를 **2**로 변경하고 **Scale Service**를 클릭합니다.
4. 트리 뷰에서 **fabric:/Voting/azurevotefront** 노드를 클릭하고 파티션 노드(GUID로 표현됨)를 확장합니다.

    ![containersquickstartscaledone][containersquickstartscaledone]

    이제 서비스에 두 인스턴스가 있는 것을 볼 수 있습니다. 트리 뷰에서 인스턴스가 실행하는 노드를 볼 수 있습니다.

이 간단한 관리 작업으로 사용자 로드를 처리하는 프런트 엔드 서비스에 사용 가능한 리소스를 두 배로 증가시킬 수 있습니다. 서비스를 안정적으로 실행하기 위해 서비스의 여러 인스턴스가 필요하지 않다는 것을 이해하는 것이 중요합니다. 서비스가 실패하면 Service Fabric은 클러스터에서 새 서비스 인스턴스가 실행되는지 확인합니다.

## <a name="clean-up"></a>정리
템플릿에 제공된 제거 스크립트를 사용하여 클러스터에서 응용 프로그램 인스턴스를 삭제하고 응용 프로그램 유형을 등록 해제합니다. 이 명령은 인스턴스를 정리하는 데 시간이 걸리고 'install'sh' 명령은 이 스크립트 직후에 실행해서는 안됩니다. 

```bash
./uninstall.sh
```

## <a name="next-steps"></a>다음 단계
이 빠른 시작에서는 다음을 수행하는 방법을 알아보았습니다.
> [!div class="checklist"]
> * Azure에 Linux 컨테이너 응용 프로그램 배포
> * Service Fabric 클러스터에서 컨테이너 장애 조치(failover)
> * Service Fabric 클러스터에서 컨테이너 크기 조정

* [Service Fabric의 컨테이너](service-fabric-containers-overview.md)를 실행하는 방법에 대해 자세히 알아봅니다.
* Service Fabric [응용 프로그램 수명 주기](service-fabric-application-lifecycle.md)에 대해 자세히 알아봅니다.
* GitHub에서 [Service Fabric 컨테이너 코드 샘플](https://github.com/Azure-Samples/service-fabric-containers)을 확인합니다.

[sfx]: ./media/service-fabric-quickstart-containers-linux/containersquickstartappinstance.png
[quickstartpic]: ./media/service-fabric-quickstart-containers-linux/votingapp.png
[sfxquickstartshownodetype]:  ./media/service-fabric-quickstart-containers-linux/containersquickstartrestart.png
[containersquickstartscale]: ./media/service-fabric-quickstart-containers-linux/containersquickstartscale.png
[containersquickstartscaledone]: ./media/service-fabric-quickstart-containers-linux/containersquickstartscaledone.png
