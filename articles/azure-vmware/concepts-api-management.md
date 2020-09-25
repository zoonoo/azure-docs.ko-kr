---
title: 개념-API Management
description: API Management Azure VMware 솔루션 Vm (가상 머신)에서 실행 되는 Api를 보호 하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: 346d0f795c3d19b115ced771991263cce2104217
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91262980"
---
# <a name="api-management-to-publish-and-protect-apis-running-on-azure-vmware-solution-based-vms"></a>Azure VMware 솔루션 기반 Vm에서 실행 되는 Api를 게시 및 보호 API Management

Microsoft Azure [API Management](https://azure.microsoft.com/services/api-management/) 를 통해 개발자와 devops 팀이 내부 또는 외부 소비자에 게 안전 하 게 게시할 수 있습니다.

여러 Sku에서 제공 되기는 하지만 개발자 및 프리미엄 Sku만 azure Virtual Network 통합을 통해 Azure VMware 솔루션 작업에서 실행 되는 Api를 게시할 수 있습니다. 이러한 두 Sku는 API Management 서비스와 백 엔드 간의 연결을 안전 하 게 사용할 수 있도록 합니다. 개발자 SKU는 개발 및 테스트를 위한 것이 고, 프리미엄 SKU는 프로덕션 배포를 위한 것입니다.

Azure VMware 솔루션 Vm (가상 머신)을 기반으로 실행 되는 백 엔드 서비스의 경우 기본적으로 API Management의 구성이 온-프레미스 백 엔드 서비스와 동일 합니다. 내부 및 외부 배포 모두에 대해 API Management는 Azure VMware 솔루션 쪽에서 백 엔드 서버가 NSX Load Balancer 뒤에 배치 될 때 부하 분산 장치의 VIP (가상 IP)를 백 엔드 끝점으로 구성 합니다.

## <a name="external-deployment"></a>외부 배포

외부 배포는 공용 끝점을 사용 하 여 외부 사용자가 사용 하는 Api를 게시 합니다. 개발자 및 DevOps 엔지니어는 Azure Portal 또는 PowerShell 및 API Management 개발자 포털을 통해 Api를 관리할 수 있습니다.

외부 배포 다이어그램은 전체 프로세스와 관련 된 행위자 (맨 위에 표시 됨)를 보여 줍니다. 행위자는 다음과 같습니다.

- **관리자:** PowerShell 또는 Azure DevOps와 같은 Azure Portal 및 자동화 메커니즘을 통해 Azure VMware 솔루션을 관리 하는 관리 또는 DevOps 팀을 나타냅니다.

- **사용자:**  표시 되는 Api의 소비자를 나타내며 Api를 사용 하는 사용자와 서비스를 모두 나타냅니다.

트래픽 흐름은 허브 가상 네트워크에 연결 된 백 엔드 서비스를 추상화 하는 API Management 인스턴스를 통해 이동 합니다. Express 경로 게이트웨이는 Express 경로 Global Reach 채널로 트래픽을 라우팅하고, 들어오는 트래픽을 다른 백 엔드 서비스 인스턴스로 배포 하 Load Balancer NSX에 도달 합니다.

API Management Azure 공용 API가 있고 Azure DDOS Protection 서비스를 활성화 하는 것이 좋습니다. 

:::image type="content" source="media/api-management/external-deployment.png" alt-text="외부 배포-Azure VMware 솔루션에 대 한 API Management":::


## <a name="internal-deployment"></a>내부 배포

내부 배포는 내부 사용자 또는 시스템에서 사용 하는 Api를 게시 합니다. DevOps 팀 및 API 개발자는 외부 배포와 동일한 관리 도구 및 개발자 포털을 사용 합니다.

내부 배포는 [Azure 애플리케이션 Gateway를 사용](../api-management/api-management-howto-integrate-internal-vnet-appgateway.md) 하 여 API의 기능을 활용 하 고 다양 한 시나리오를 지 원하는 하이브리드 배포를 만드는 데 사용 되는 공용 및 보안 끝점을 만들 수 있습니다.  API는 해당 기능을 활용 하 고 다양 한 시나리오를 가능 하 게 하는 하이브리드 배포를 만듭니다.

* 내부 및 외부 소비자가 사용 하는 동일한 API Management 리소스를 사용 합니다.

* 외부 소비자에 대해 정의 되 고 사용 가능한 Api의 하위 집합이 포함 된 단일 API Management 리소스를 사용 합니다.

* 공용 인터넷에서 API Management에 대 한 액세스를 전환 하는 쉬운 방법을 제공 합니다.

아래 배포 다이어그램은 내부 또는 외부에 있을 수 있는 소비자를 보여 줍니다. 각 형식은 동일한 Api 또는 다른 Api에 액세스 합니다.

내부 배포에서 Api는 동일한 API Management 인스턴스에 노출 됩니다. API Management 앞에는 azure WAF (웹 응용 프로그램 방화벽) 기능을 활성화 하 고, 트래픽을 필터링 하는 HTTP 수신기와 규칙 집합을 배포 하 고, Azure VMware 솔루션에서 실행 되는 백 엔드 서비스의 하위 집합만 노출 하 여 Application Gateway.

* 내부 트래픽은 Express 경로 게이트웨이를 통해 Azure 방화벽으로 라우팅되고 트래픽 규칙이 설정 되거나 API Management에 직접 API Management 됩니다.  

* 외부 트래픽은 API Management의 외부 보호 계층을 사용 하는 Application Gateway를 통해 Azure에 들어갑니다.


:::image type="content" source="media/api-management/internal-deployment.png" alt-text="Azure VMware 솔루션에 대 한 내부 배포-API Management":::