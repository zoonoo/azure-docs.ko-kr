---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 04/15/2020
ms.author: ccompy
ms.openlocfilehash: c31a5aaa9866a4ce97cd3cd59a8e363834f70587
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81312819"
---
* 격리를 제외한 모든 가격 책정 계획을 지 원하는 다중 테 넌 트 시스템
* App Service Environment는 VNet에 배포 되 고 격리 된 가격 책정 계획 앱을 지원 합니다.

VNet 통합 기능은 다중 테 넌 트 앱에서 사용 됩니다. 앱이 [App Service Environment][ASEintro]에 있는 경우 이미 VNet에 있으므로 VNet 통합 기능을 사용하여 동일한 VNet의 리소스에 연결할 필요가 없습니다. 모든 네트워킹 기능에 대 한 자세한 내용은 [App Service 네트워킹 기능][Networkingfeatures]을 참조 하세요.

VNet 통합은 vnet의 리소스에 대 한 앱 액세스를 제공 하지만 VNet에서 앱에 대 한 인바운드 개인 액세스를 부여 하지 않습니다. 개인 사이트 액세스는 Azure virtual network 내에서와 같은 개인 네트워크 에서만 앱에 액세스할 수 있도록 하는 것을 말합니다. VNet 통합은 앱에서 VNet으로의 아웃 바운드 호출을 수행 하는 데만 사용 됩니다. Vnet 통합 기능은 동일한 지역에서 vnet과 다른 지역의 VNet에서 사용 되는 경우 다르게 동작 합니다. VNet 통합 기능에는 두 가지 변형이 있습니다.

* **지역 VNet 통합**: 동일한 지역에서 Azure Resource Manager 가상 네트워크에 연결 하는 경우 통합 하는 VNet에 전용 서브넷이 있어야 합니다.
* **게이트웨이-필수 Vnet 통합**: 다른 지역의 VNet 또는 동일한 지역의 클래식 가상 네트워크에 연결 하는 경우 대상 vnet에서 프로 비전 된 Azure Virtual Network Gateway가 필요 합니다.

VNet 통합 기능:

* Standard, Premium, PremiumV2 또는 탄력적 프리미엄 요금제가 필요 합니다.
* TCP 및 UDP를 지원 합니다.
* Azure App Service 앱 및 함수 앱을 사용 합니다.

VNet 통합에서 지원 하지 않는 몇 가지 사항은 다음과 같습니다.

* 드라이브 탑재
* Active Directory 통합.
* NetBIOS.

게이트웨이-필요한 VNet 통합은 대상 VNet 또는 피어 링 또는 Vpn을 사용 하 여 대상 VNet에 연결 된 네트워크에 있는 리소스에 대 한 액세스를 제공 합니다. 게이트웨이-필요한 VNet 통합은 Azure Express 경로 연결에서 사용할 수 있는 리소스에 대 한 액세스를 허용 하지 않으며 서비스 끝점에서 작동 합니다.

사용 된 버전에 관계 없이 vnet 통합은 vnet의 리소스에 대 한 앱 액세스를 제공 하지만 VNet에서 앱에 대 한 인바운드 개인 액세스를 부여 하지 않습니다. 개인 사이트 액세스는 Azure VNet 내에서와 같이 개인 네트워크 에서만 앱에 액세스할 수 있도록 하는 것을 말합니다. VNet 통합은 앱에서 VNet으로의 아웃 바운드 호출을 수행 하는 경우에만 사용할 수 있습니다.

<!--Links-->
[ASEintro]: https://docs.microsoft.com/azure/app-service/environment/intro
[Networkingfeatures]: https://docs.microsoft.com/azure/app-service/networking-features
