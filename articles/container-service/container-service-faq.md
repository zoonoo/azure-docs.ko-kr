---
title: Azure Container Service - FAQ | Microsoft Docs
description: "Azure Container Service 즉, Docker 컨테이너 앱을 실행하도록 가상 컴퓨터 클러스터 만들기, 구성 및 관리를 간소화하는 서비스에 대한 질문과 대답을 제공합니다."
services: container-service
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: "Docker, 컨테이너, 마이크로 서비스, Mesos, Azure, Kubernetes"
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/21/2017
ms.author: danlep
translationtype: Human Translation
ms.sourcegitcommit: 2a381431acb6436ddd8e13c69b05423a33cd4fa6
ms.openlocfilehash: c28391b752c071161796421aee63402899d2a0a4
ms.lasthandoff: 02/22/2017


---
# <a name="frequently-asked-questions-azure-container-service"></a>질문과 대답: Azure Container Service


## <a name="orchestrators"></a>오케스트레이터

### <a name="which-container-orchestrators-do-you-support-on-azure-container-service"></a>Azure Container Service에 어떤 컨테이너 오케스트레이터가 지원되나요? 

오픈 소스 DC/OS, Docker Swarm 및 Kubernetes가 지원됩니다. 자세한 내용은 [개요](container-service-intro.md)를 참조하세요.
 
### <a name="do-you-support-docker-swarm-mode"></a>Docker Swarm 모드가 지원되나요? 

현재 Swarm 모드는 지원되지 않지만 서비스 로드맵에 포함되어 있습니다. 

### <a name="does-azure-container-service-support-windows-containers"></a>Azure Container Service에 Windows 컨테이너가 지원되나요?  

현재 Linux 컨테이너는 모든 오케스트레이터에 지원됩니다. Kubernetes를 통한 Windows 컨테이너에 대한 지원은 미리 보기 상태입니다.

### <a name="do-you-recommend-a-specific-orchestrator-in-azure-container-service"></a>Azure Container Service에 권장되는 특정 오케스트레이터가 있나요? 
일반적으로 특정 오케스트레이터가 권장되지는 않습니다. 지원되는 오케스트레이터 중 하나에 경험이 있다면 그러한 경험을 Azure Container Service에 적용할 수 있습니다. 하지만 데이터 경향에 따르면 DC/OS는 빅 데이터 및 IoT 워크로드에 대해 성능이 입증되었고 Kubernetes는 클라우드 네이티브 워크로드에 적합하며 Docker Swarm은 Docker 도구와의 통합 및 학습이 쉬운 것으로 알려져 있습니다.

시나리오에 따라 다른 Azure 서비스를 사용하여 사용자 지정 컨테이너 솔루션을 구축하고 관리할 수도 있습니다. 이러한 서비스에는 [Virtual Machines](../virtual-machines/virtual-machines-linux-azure-overview.md), [Service Fabric](../service-fabric/service-fabric-overview.md), [Web Apps](../app-service-web/app-service-web-overview.md) 및 [Batch](../batch/batch-technical-overview.md)가 있습니다.  

### <a name="what-is-the-difference-between-azure-container-service-and-acs-engine"></a>Azure Container Service와 ACS Engine의 차이는 무엇인가요? 
Azure Container Service는 Azure Portal, Azure 명령줄 도구 및 Azure API의 기능이 포함된 SLA 기반 Azure 서비스입니다. 이 서비스를 사용하면 비교적 적은 수의 구성 옵션을 통해 표준 컨테이너 오케스트레이션 도구를 실행하는 클러스터를 신속하게 구현하고 관리할 수 있습니다. 

[ACS Engine](http://github.com/Azure/acs-engine)은 power user가 모든 수준의 클러스터 구성을 사용자 지정하는 데 사용할 있는 오픈 소스 프로젝트입니다. 인프라 및 소프트웨어 양쪽의 구성을 변경하는 기능이 있기 때문에 ACS Engine용 SLA는 제공되지 않습니다. 지원은 공식적인 Microsoft 채널이 아닌 GitHub의 오픈 소스 프로젝트를 통해 처리됩니다. 

## <a name="cluster-management"></a>클러스터 관리

### <a name="how-do-i-create-ssh-keys-for-my-cluster"></a>클러스터의 SSH 키는 어떻게 만드나요?

클러스터의 Linux 가상 컴퓨터에 대한 인증을 위해 운영 체제에서 표준 도구를 사용하여 SSH RSA 공용 및 개인 키 쌍을 생성할 수 있습니다. 단계는 [OS X 및 Linux](../virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md) 또는 [Windows](../virtual-machines/virtual-machines-linux-ssh-from-windows.md) 지침을 참조하세요. 

[Azure CLI 2.0 명령](container-service-create-acs-cluster-cli.md)을 사용하여 컨테이너 서비스 클러스터를 배포하는 경우 클러스터용 SSH 키가 자동으로 생성될 수 있습니다.

### <a name="how-do-i-create-a-service-principal-for-my-kubernetes-cluster"></a>Kubernetes 클러스터에 대한 서비스 주체는 어떻게 만드나요?

Azure Container Service에서 Kubernetes 클러스터를 만들려면 Azure Active Directory 서비스 주체 ID 및 암호도 필요합니다. 자세한 내용은 [Kubernetes 클러스터의 서비스 주체 정보](container-service-kubernetes-service-principal.md)를 참조하세요.


[Azure CLI 2.0 명령](container-service-create-acs-cluster-cli.md)을 사용하여 Kubernetes 클러스터를 배포하는 경우 서비스 주체 자격 증명이 클러스터에 대해 자동으로 생성될 수 있습니다.


### <a name="how-do-i-increase-the-number-of-masters-after-a-cluster-is-created"></a>클러스터를 만든 후 마스터 수는 어떻게 늘리나요? 
클러스터를 만들고 나면 마스터 수가 고정되어 변경할 수 없습니다. 클러스터를 만드는 동안 고가용성을 위해 다수의 마스터를 적당히 선택해야 합니다.


### <a name="how-do-i-increase-the-number-of-agents-after-a-cluster-is-created"></a>클러스터를 만든 후 에이전트 수는 어떻게 늘리나요? 
클러스터의 에이전트 수는 Azure Portal 또는 명령줄 도구를 사용하여 조정할 수 있습니다. [Azure Container Service 클러스터 규모 조정](container-service-scale.md)을 참조하세요.


### <a name="what-are-the-urls-of-my-masters-and-agents"></a>마스터와 에이전트의 URL은 무언인가요? 
Azure Container Service에 있는 클러스터 리소스의 URL은 사용자가 제공한 DNS 이름 접두사 및 배포를 위해 사용자가 선택한 Azure 지역의 이름을 기반으로 합니다. 예를 들어 마스터 노드의 FQDN(정규화된 도메인 이름)은 다음과 같은 형식입니다.

``` 
DNSnamePrefix.AzureRegion.cloudapp.azure.net
```

클러스터에 일반적으로 사용되는 URL은 Azure Portal, Azure 리소스 탐색기 또는 기타 Azure 도구에서 찾을 수 있습니다.
 
### <a name="where-do-i-find-the-ssh-connection-string-to-my-cluster"></a>클러스터에 대한 SSH 연결 문자열은 어디에서 찾을 수 있나요?

연결 문자열은 Azure Portal에서 찾거나 Azure 명령줄 도구를 사용하여 찾을 수 있습니다. 

1. 포털에서 클러스터 배포를 위한 리소스 그룹으로 이동합니다.  

2. **개요**를 클릭하고 **필수** 아래에서 **배포**에 대한 링크를 클릭합니다. 

3. **배포 기록** 블레이드에서 이름이 **microsoft-acs**로 시작하고 배포 날짜가 다음에 나오는 배포를 클릭합니다. 예: microsoft-acs-201701310000.  

4. **요약** 페이지의 **출력** 아래에 여러 개의 클러스터 링크(<provided></provided>)가 제공됩니다. **SSHMaster0**은 컨테이너 서비스 클러스터의 첫 번째 마스트에 SSH 연결 문자열을 제공합니다. 

이전에 언급했듯이 Azure 도구를 사용하여 마스터의 FQDN을 찾을 수도 있습니다. 마스터의 FQDN 및 클러스터를 만들 때 지정한 사용자 이름을 사용하여 마스터에 SSH 연결을 만듭니다. 예:

```bash
ssh userName@masterFQDN –A –p 22 
```

자세한 내용은 [Azure Container Service 클러스터에 연결](container-service-connect.md)을 참조하세요.




## <a name="next-steps"></a>다음 단계

* Azure Container Service에 대해 [자세히 알아보세요](container-service-intro.md).
* [포털](container-service-deployment.md) 또는 [Azure CLI 2.0](container-service-create-acs-cluster-cli.md)을 사용하여 컨테이너 서비스 클러스터 배포.
