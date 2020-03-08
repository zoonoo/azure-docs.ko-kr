---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 02/27/2020
ms.author: ccompy
ms.openlocfilehash: da43e639779dd4946cb014be777e3a6db0366e24
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78671479"
---
* 격리 계층을 제외한 모든 범위의 요금제를 지원하는 다중 테넌트 시스템
* VNet에 배포 되 고 격리 된 가격 책정 계획 앱을 지 원하는 ASE (App Service Environment)

이 문서는 다중 테 넌 트 앱에서 사용 하기 위한 VNet 통합 기능을 통해 진행 됩니다. 앱이 [App Service Environment][ASEintro]에 있는 경우 이미 vnet에 있으며 vnet 통합 기능을 사용 하 여 동일한 vnet의 리소스에 연결할 필요가 없습니다. 모든 네트워킹 기능에 대 한 자세한 내용은 [App Service 네트워킹 기능][Networkingfeatures] 을 참조 하세요.

VNet 통합은 가상 네트워크의 리소스에 대 한 앱 액세스 권한을 앱에 부여 하지만 VNet에서 앱에 대 한 인바운드 개인 액세스를 부여 하지 않습니다. 개인 사이트 액세스는 Azure virtual network 내에서와 같은 개인 네트워크에서 앱에만 액세스할 수 있도록 하는 것을 말합니다. VNet 통합은 앱에서 VNet으로의 아웃 바운드 호출을 수행 하는 경우에만 사용할 수 있습니다. VNet 통합 기능은 동일한 지역에서 Vnet와 함께 사용 하거나 다른 지역에서 Vnet를 사용 하는 경우 다르게 동작 합니다. VNet 통합 기능에는 두 가지 변형이 있습니다.

* 지역 VNet 통합-동일한 지역에서 리소스 관리자 Vnet에 연결 하는 경우 통합 하는 VNet에 전용 서브넷이 있어야 합니다. 
* 게이트웨이는 VNet 통합이 필요 합니다. Vnet 다른 지역에서 또는 동일한 지역의 클래식 VNet에 연결 하는 경우 대상 VNet에서 프로 비전 된 Virtual Network 게이트웨이가 필요 합니다.

VNet 통합 기능:

* Standard, Premium, PremiumV2 또는 탄력적 프리미엄 요금제 필요 
* TCP 및 UDP 지원
* App Service 앱 및 함수 앱 작업

VNet 통합에서 지원하지 않는 다음 몇 가지 항목이 있습니다.

* 드라이브 탑재
* AD 통합 
* NetBios

게이트웨이 필요 VNet 통합은 대상 VNet 또는 피어 링 또는 Vpn을 사용 하 여 대상 VNet에 연결 된 네트워크에 있는 리소스에 대 한 액세스만 제공 합니다. 게이트웨이 필요 VNet 통합은 Express 경로 연결에서 사용할 수 있는 리소스에 대 한 액세스를 허용 하지 않으며 서비스 끝점과 연동 됩니다. 

사용 된 버전에 관계 없이 VNet 통합은 가상 네트워크의 리소스에 대 한 앱 액세스 권한을 앱에 부여 하지만 가상 네트워크에서 앱에 대 한 인바운드 개인 액세스를 부여 하지는 않습니다. 프라이빗 사이트 액세스는 Azure Virtual Network와 같은 프라이빗 네트워크에서만 앱에 액세스할 수 있도록 합니다. VNet 통합은 앱에서 VNet으로의 아웃 바운드 호출을 수행 하는 경우에만 사용할 수 있습니다. 

<!--Links-->
[ASEintro]: https://docs.microsoft.com/azure/app-service/environment/intro
[Networkingfeatures]: https://docs.microsoft.com/azure/app-service/networking-features