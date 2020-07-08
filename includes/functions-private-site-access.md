---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/06/2020
ms.author: glenga
ms.openlocfilehash: 5e0cff7bde6e80a776d694820ca7b69dafa7c0d9
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83648830"
---
프라이빗 사이트 액세스는 Azure Virtual Network와 같은 프라이빗 네트워크에서만 앱에 액세스할 수 있도록 합니다.

* 프라이빗 사이트 액세스는 서비스 엔드포인트가 구성될 때 [Premium](../articles/azure-functions/functions-premium-plan.md), [Consumption](../articles/azure-functions/functions-scale.md#consumption-plan) 및 [App Service](../articles/azure-functions/functions-scale.md#app-service-plan) 계획에서 사용할 수 있습니다.
    * 서비스 엔드포인트는 **플랫폼 기능** > **네트워킹** > **액세스 제한 구성** > **규칙 추가**에 따라 앱별로 구성할 수 있습니다. 이제 가상 네트워크를 규칙 유형으로 선택할 수 있습니다.
    * 자세한 내용은 [가상 네트워크 서비스 엔드포인트](../articles/virtual-network/virtual-network-service-endpoints-overview.md)를 참조하세요.
    * 서비스 엔드포인트를 사용하는 경우 가상 네트워크 통합이 구성된 경우에도 함수에서 인터넷에 대한 전체 아웃바운드 액세스를 계속 유지합니다.
* 프라이빗 사이트 액세스는 ILB(내부 부하 분산 장치)를 사용하여 구성된 App Service Environment 내에서도 사용할 수 있습니다. 자세한 내용은 [App Service Environment에서 내부 부하 분산 장치 만들기 및 사용](../articles/app-service/environment/create-ilb-ase.md)을 참조하세요.

프라이빗 사이트 액세스를 설정하는 방법을 알아보려면 [Azure Functions 프라이빗 사이트 액세스 설정](../articles/azure-functions/functions-create-private-site-access.md)을 참조하세요.