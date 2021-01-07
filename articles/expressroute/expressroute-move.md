---
title: 'Express 경로: 클래식에서 Azure Resource Manager로 회로 이동'
description: 클래식에서 Azure Resource Manager 배포 모델로 Azure Express 경로 회로를 이동할 때 발생 하는 상황에 대해 알아봅니다.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 12/15/2020
ms.author: duau
ms.openlocfilehash: dcba2e9de2b37e8c432f94781b3c4c369ad52395
ms.sourcegitcommit: 02ed9acd4390b86c8432cad29075e2204f6b1bc3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/29/2020
ms.locfileid: "97807944"
---
# <a name="moving-expressroute-circuits-from-the-classic-to-the-resource-manager-deployment-model"></a>클래식에서 Resource Manager 배포 모델로 ExpressRoute 회로 이동
이 문서에서는 Azure Express 경로 회로를 클래식에서 Azure Resource Manager 배포 모델로 이동할 때 발생 하는 상황에 대 한 개요를 제공 합니다.

단일 Express 경로 회로를 사용 하 여 클래식 및 리소스 관리자 배포 모델 둘 다에서 배포 된 가상 네트워크를 연결할 수 있습니다.

![두 가지 배포 모델에 걸쳐 가상 네트워크에 연결하는 ExpressRoute 회로](./media/expressroute-move/expressroute-move-1.png)

## <a name="expressroute-circuits-that-are-created-in-the-classic-deployment-model"></a>클래식 배포 모델에서 만든 ExpressRoute 회로
클래식 배포 모델에서 만든 Express 경로 회로는 먼저 리소스 관리자 배포 모델로 마이그레이션해야 합니다. 그런 다음에만 클래식 및 리소스 관리자 배포 모델에 대 한 연결을 설정할 수 있습니다. 연결이 이동 하는 동안 연결이 끊어지거나 중단 되지 않습니다. 동일한 구독 및 교차 구독 내에서 클래식 배포 모델의 모든 회로 간 네트워크 연결이 유지 됩니다.

이동이 성공적으로 완료 되 면 Express 경로 회로는 리소스 관리자 배포 모델에서 만든 Express 경로 회로와 정확히 동일 하 게 동작 합니다. 이제 리소스 관리자 배포 모델에 가상 네트워크에 대한 연결을 만들 수 있습니다.

Express 경로 회로를 리소스 관리자 배포 모델로 이동한 후에는 리소스 관리자 배포 모델 에서만 관리할 수 있습니다. 피어 링을 관리 하 고, 회로 속성을 업데이트 하 고, 회로를 삭제 하는 작업은 리소스 관리자 배포 모델을 통해서만 사용할 수 있습니다. 두 배포 모델에 대 한 액세스를 관리 하는 방법에 대 한 자세한 내용은 다음 섹션을 참조 하십시오.

회로를 리소스 관리자 배포 모델로 이동 하기 위해 연결 공급자를 포함할 필요가 없습니다.

## <a name="expressroute-circuits-that-are-created-in-the-resource-manager-deployment-model"></a>Resource Manager 배포 모델에서 만든 ExpressRoute 회로
두 배포 모델에서 모두 액세스할 수 있도록 Resource Manager 배포 모델에서 만든 ExpressRoute 회로를 설정할 수 있습니다. 구독에서 모든 Express 경로 회로는 두 배포 모델에서 모두 액세스할 수 있도록 구성할 수 있습니다.

* 리소스 관리자 배포 모델에서 만든 Express 경로 회로는 기본적으로 클래식 배포 모델에 대 한 액세스 권한이 없습니다.
* 클래식 배포 모델에서 리소스 관리자 배포 모델로 이동한 Express 경로 회로는 기본적으로 두 배포 모델에서 모두 액세스할 수 있습니다.
* Express 경로 회로는 리소스 관리자 또는 클래식 배포 모델에서 만들어졌는지에 관계 없이 항상 리소스 관리자 배포 모델에 액세스할 수 있습니다. [가상 네트워크를 연결 하는 방법](expressroute-howto-linkvnet-arm.md)에 대 한 지침에 따라 가상 네트워크에 대 한 연결을 만들 수 있습니다.
* 클래식 배포 모델에 대한 액세스는 ExpressRoute 회로의 **allowClassicOperations** 매개 변수에 의해 제어됩니다.

> [!IMPORTANT]
> [서비스 제한](../azure-resource-manager/management/azure-subscription-service-limits.md) 페이지에 문서화된 모든 할당량이 적용됩니다. 예를 들어 표준 회로는 클래식 및 리소스 관리자 배포 모델 간에 최대 10개의 가상 네트워크 링크/연결을 포함할 수 있습니다.
> 
> 

## <a name="controlling-access-to-the-classic-deployment-model"></a>클래식 배포 모델에 대한 액세스 제어
Express 경로 회로를 사용 하 여 두 배포 모델의 가상 네트워크에 연결할 수 있습니다. 이렇게 하려면 Express 경로 회로에서 **allowClassicOperations** 매개 변수를 설정 합니다.

**allowClassicOperations** 를 TRUE로 설정하면 가상 네트워크를 두 배포 모델에서 ExpressRoute 회로로 연결할 수 있습니다. 
* 클래식 배포 모델에서 가상 네트워크를 연결 하려면 [클래식 배포 모델의 가상 네트워크를 연결 하는 방법](expressroute-howto-linkvnet-classic.md)을 참조 하세요.
* 리소스 관리자 배포 모델에서 가상 네트워크를 연결 하려면 [리소스 관리자 배포 모델에서 가상 네트워크를 연결 하는 방법](expressroute-howto-linkvnet-arm.md)을 참조 하세요.

**allowClassicOperations** 를 FALSE로 설정하면 클래식 배포 모델에서 회로에 대한 액세스가 차단됩니다. 그러나 클래식 배포 모델에 연결 된 모든 가상 네트워크는 계속 유지 됩니다. Express 경로 회로는 클래식 배포 모델에 표시 되지 않습니다.

## <a name="supported-operations-in-the-classic-deployment-model"></a>클래식 배포 모델에서 지원되는 작업
**allowClassicOperations** 가 TRUE로 설정된 경우 ExpressRoute 회로에서 다음 클래식 작업이 지원됩니다.

* ExpressRoute 회로 정보 가져오기
* 클래식 가상 네트워크에 대한 가상 네트워크 링크 만들기/업데이트/가져오기/삭제
* 구독 간 연결에 대한 가상 네트워크 연결 권한 부여 만들기/업데이트/가져오기/삭제

그러나 **allowClassicOperations** 을 TRUE로 설정 하면 다음과 같은 클래식 작업을 실행할 수 없습니다.

* Azure 프라이빗, Azure 공용 및 Microsoft 피어링에 대한 BGP(Border Gateway Protocol) 피어링 만들기/업데이트/가져오기/삭제
* ExpressRoute 회로 삭제

## <a name="communication-between-the-classic-and-the-resource-manager-deployment-models"></a>클래식과 리소스 관리자 배포 모델 간의 통신
ExpressRoute 회로는 클래식 및 리소스 관리자 배포 모델 간의 브리지 역할을 합니다. 두 가상 네트워크가 동일한 회로에 연결 된 경우 두 배포 모델의 가상 네트워크 간 트래픽은 Express 경로 회로를 통해 전달 될 수 있습니다.

집계 처리량은 가상 네트워크 게이트웨이 처리 용량으로 제한됩니다. 이 경우 트래픽이 연결 공급자의 네트워크 또는 네트워크에 들어가지 않습니다. 가상 네트워크 간의 트래픽 흐름은 완전히 Microsoft 네트워크 내에서만 일어납니다.

## <a name="access-to-azure-public-and-microsoft-peering-resources"></a>Azure 공용 및 Microsoft 피어링 리소스 액세스
작업 중단 없이 Azure 공용 피어링 및 Microsoft 피어링을 통해 일반적으로 액세스할 수 있는 리소스에 계속해서 액세스할 수 있습니다.  

## <a name="whats-supported"></a>지원되는 내용
이 섹션에서는 ExpressRoute 회로에 지원되는 내용을 설명합니다.

* 단일 ExpressRoute 회로를 사용하여 클래식 및 Resource Manager 배포 모델에 배포된 가상 네트워크에 액세스할 수 있습니다.
* 클래식에서 Resource Manager 배포 모델로 ExpressRoute 회로를 이동할 수 있습니다. 이동이 완료 되 면 Express 경로 회로는 리소스 관리자 배포 모델에서 만든 다른 모든 Express 경로 회로 처럼 계속 작동 합니다.
* ExpressRoute 회로만 이동시킬 수 있습니다. 회로 링크, 가상 네트워크 및 VPN 게이트웨이는 이 작업을 통해 이동될 수 없습니다.
* ExpressRoute 회로가 리소스 관리자 배포 모델로 이동한 후에는 리소스 관리자 배포 모델만 사용하여 ExpressRoute의 수명 주기를 관리할 수 있습니다. 즉, 피어 링 추가/업데이트/삭제와 같은 작업을 실행 하 고, 회로 속성 (예: 대역폭, SKU 및 청구 유형)을 업데이트 하 고, 리소스 관리자 배포 모델 에서만 회로를 삭제할 수 있습니다.
* ExpressRoute 회로는 클래식 및 리소스 관리자 배포 모델 간의 브리지 역할을 합니다. 두 가상 네트워크가 동일한 Express 경로 회로에 연결 된 경우 클래식 가상 네트워크의 가상 머신과 리소스 관리자 가상 네트워크의 가상 머신 간 트래픽은 Express 경로를 통해 통신할 수 있습니다.
* 구독 간 연결은 클래식 및 리소스 관리자 배포 모델에서 지원됩니다.
* ExpressRoute 회로를 클래식 모델에서 Azure Resource Manager 모델로 이동한 후 [ExpressRoute 회로에 연결된 가상 네트워크를 마이그레이션](expressroute-migration-classic-resource-manager.md)할 수 있습니다.

## <a name="whats-not-supported"></a>지원 되지않는 사항
이 섹션에서는 ExpressRoute 회로에 지원되지 않는 내용을 설명합니다.

* 클래식 배포 모델에서 ExpressRoute 회로의 수명 주기를 관리합니다.
* 클래식 배포 모델에 대 한 azure RBAC (역할 기반 액세스 제어) 지원. 클래식 배포 모델에서 회로에 대 한 Azure RBAC 컨트롤을 실행할 수 없습니다. 구독의 모든 관리자/공동 관리자는 가상 네트워크를 회로에 연결하거나 연결을 끊을 수 있습니다.

## <a name="configuration"></a>Configuration
[클래식에서 Resource Manager 배포 모델로 ExpressRoute 회로 이동](expressroute-howto-move-arm.md)에서 설명한 지침을 수행합니다.

## <a name="next-steps"></a>다음 단계
* [ExpressRoute 회로에 연결된 가상 네트워크를 클래식 모델에서 Azure Resource Manager 모델로 마이그레이션](expressroute-migration-classic-resource-manager.md)
* 워크플로 정보는 [ExpressRoute 회로 프로비전 워크플로 및 회로 상태](expressroute-workflows.md)를 참조하세요.
* ExpressRoute 연결을 구성하려면:
  
  * [ExpressRoute 회로 만들기](expressroute-howto-circuit-arm.md)
  * [라우팅 구성](expressroute-howto-routing-arm.md)
  * [Express 경로 회로에 가상 네트워크 연결](expressroute-howto-linkvnet-arm.md)

