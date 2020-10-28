---
title: 개념-API Management
description: API Management Azure VMware 솔루션 Vm (가상 머신)에서 실행 되는 Api를 보호 하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: f412ee81fc77435f2586a31c1bf6f6bdf22c66e2
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92670329"
---
# <a name="api-management-to-publish-and-protect-apis-running-on-azure-vmware-solution-based-vms"></a>Azure VMware 솔루션 기반 Vm에서 실행 되는 Api를 게시 및 보호 API Management

Microsoft Azure [API Management](https://azure.microsoft.com/services/api-management/) 를 사용 하면 내부 또는 외부 소비자에 게 안전 하 게 게시할 수 있습니다.  Azure Virtual Network 통합을 통해 azure VMware 솔루션 작업에서 실행 되는 Api를 게시할 수 있습니다.  두 Sku 모두 API Management 서비스와 백 엔드 간에 안전 하 게 연결할 수 있습니다. 

>[!NOTE]
>개발자 SKU는 개발 및 테스트를 위한 것이 고, 프리미엄 SKU는 프로덕션 배포를 위한 것입니다.

API Management 구성은 Azure VMware 솔루션 Vm (가상 머신) 및 온-프레미스에서 실행 되는 백 엔드 서비스에 대해 동일 합니다. 두 배포 모두 API Management 백 엔드 서버가 Azure VMware 솔루션의 NSX Load Balancer 뒤에 배치 되는 경우 부하 분산 장치에서 VIP (가상 IP)를 백 엔드 끝점으로 구성 합니다. 


## <a name="external-deployment"></a>외부 배포

외부 배포는 공용 끝점을 사용 하 여 외부 사용자가 사용 하는 Api를 게시 합니다. 개발자 및 DevOps 엔지니어는 Azure Portal 또는 PowerShell 및 API Management 개발자 포털을 통해 Api를 관리할 수 있습니다.

외부 배포 다이어그램은 전체 프로세스와 관련 된 행위자 (맨 위에 표시 됨)를 보여 줍니다. 행위자는 다음과 같습니다.

- **관리자:** PowerShell 또는 Azure DevOps와 같은 Azure Portal 및 자동화 메커니즘을 통해 Azure VMware 솔루션을 관리 하는 관리 또는 DevOps 팀을 나타냅니다.

- **사용자:**  Api를 사용 하는 사용자와 서비스를 모두 표시 하 고 노출 된 Api의 소비자를 나타냅니다.

트래픽 흐름은 허브 가상 네트워크에 연결 된 백 엔드 서비스를 추상화 하는 API Management 인스턴스로 이동 합니다. Express 경로 게이트웨이는 트래픽을 Express 경로 Global Reach 채널로 라우팅하고 들어오는 트래픽을 다른 백 엔드 서비스 인스턴스로 배포 하 Load Balancer NSX에 도달 합니다.

API Management Azure 공용 API가 있고 Azure DDOS Protection 서비스를 활성화 하는 것이 좋습니다. 

:::image type="content" source="media/api-management/external-deployment.png" alt-text="외부 배포-Azure VMware 솔루션에 대 한 API Management":::


## <a name="internal-deployment"></a>내부 배포

내부 배포는 내부 사용자 또는 시스템에서 사용 하는 Api를 게시 합니다. DevOps 팀 및 API 개발자는 외부 배포와 동일한 관리 도구 및 개발자 포털을 사용 합니다.

[Azure 애플리케이션 Gateway를 사용 하 여](../api-management/api-management-howto-integrate-internal-vnet-appgateway.md) 내부 배포를 수행 하 여 API에 대 한 공용 및 보안 끝점을 만들 수 있습니다.  게이트웨이의 기능은 다양 한 시나리오를 가능 하 게 하는 하이브리드 배포를 만드는 데 사용 됩니다.  

* 내부 및 외부 소비자가 사용 하는 동일한 API Management 리소스를 사용 합니다.

* 외부 소비자에 대해 정의 되 고 사용 가능한 Api의 하위 집합이 포함 된 단일 API Management 리소스를 사용 합니다.

* 공용 인터넷에서 API Management에 대 한 액세스를 전환 하는 쉬운 방법을 제공 합니다.

아래 배포 다이어그램은 내부 또는 외부에 있을 수 있는 소비자를 보여 줍니다. 각 형식은 동일한 Api 또는 다른 Api에 액세스 합니다.

내부 배포에서 Api는 동일한 API Management 인스턴스에 노출 됩니다. API Management 앞에는 Azure WAF (웹 응용 프로그램 방화벽) 기능이 활성화 된 상태로 Application Gateway 배포 됩니다. 또한 Azure VMware 솔루션에서 실행 되는 백 엔드 서비스의 하위 집합만 노출 하 여 트래픽을 필터링 하는 일련의 HTTP 수신기와 규칙을 배포 합니다.


* 내부 트래픽은 Express 경로 게이트웨이를 통해 Azure 방화벽으로 이동한 다음 직접 또는 트래픽 규칙을 통해 API Management 합니다.   

* 외부 트래픽은 API Management의 외부 보호 계층을 사용 하는 Application Gateway를 통해 Azure에 들어갑니다.


:::image type="content" source="media/api-management/internal-deployment.png" alt-text="외부 배포-Azure VMware 솔루션에 대 한 API Management" lightbox="media/api-management/internal-deployment.png":::