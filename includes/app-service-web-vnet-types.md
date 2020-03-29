---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 02/27/2020
ms.author: ccompy
ms.openlocfilehash: da43e639779dd4946cb014be777e3a6db0366e24
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78671479"
---
* 격리 계층을 제외한 모든 범위의 요금제를 지원하는 다중 테넌트 시스템
* VNet에 배포되고 격리된 가격 책정 계획 앱을 지원하는 ASE(앱 서비스 환경)

이 문서는 다중 테넌트 앱에서 사용할 VNet 통합 기능을 거칩니다. 앱이 [App Service Environment][ASEintro]에 있는 경우 이미 VNet에 있으므로 VNet 통합 기능을 사용하여 동일한 VNet의 리소스에 연결할 필요가 없습니다. 모든 네트워킹 기능에 대한 자세한 내용은 [앱 서비스 네트워킹 기능을][Networkingfeatures] 참조하십시오.

VNet 통합은 가상 네트워크의 리소스에 대한 앱 액세스 권한을 제공하지만 VNet에서 앱에 대한 인바운드 개인 액세스 권한을 부여하지는 않습니다. 개인 사이트 액세스란 Azure 가상 네트워크 내에서와 같은 개인 네트워크에서만 앱에 액세스할 수 있도록 하는 것을 말합니다. VNet 통합은 앱에서 VNet으로 아웃바운드 호출을 하기 위한 것입니다. VNet 통합 기능은 동일한 리전의 VNet과 다른 지역의 VNet에서 사용할 때 다르게 행동합니다. VNet 통합 기능에는 두 가지 변형이 있습니다.

* 지역 별 VNet 통합 - 동일한 리전의 리소스 관리자 VNet에 연결할 때 통합중인 VNet에 전용 서브넷이 있어야 합니다. 
* 게이트웨이 필수 VNet 통합 - 다른 지역의 VNet 또는 동일한 리전의 클래식 VNet에 연결할 때 대상 VNet에 프로비전된 가상 네트워크 게이트웨이가 필요합니다.

VNet 통합 기능은 다음과 같은 기능입니다.

* 표준, 프리미엄, 프리미엄V2 또는 탄력적 프리미엄 가격 계획이 필요합니다. 
* TCP 및 UDP 지원
* 앱 서비스 앱 및 기능 앱과 함께 작업

VNet 통합에서 지원하지 않는 다음 몇 가지 항목이 있습니다.

* 드라이브 탑재
* AD 통합 
* NetBios

게이트웨이 필수 VNet 통합은 피어링 또는 VPN을 사용하여 대상 VNet 또는 대상 VNet에 연결된 네트워크의 리소스에만 액세스를 제공합니다. 게이트웨이 필수 VNet 통합은 ExpressRoute 연결에서 사용할 수 있는 리소스에 대한 액세스를 사용하거나 서비스 끝점에서 작동하도록 설정하지 않습니다. 

사용된 버전에 관계없이 VNet Integration는 앱에 가상 네트워크의 리소스에 대한 액세스 권한을 제공하지만 가상 네트워크에서 앱에 대한 인바운드 개인 액세스 권한을 부여하지는 않습니다. 프라이빗 사이트 액세스는 Azure Virtual Network와 같은 프라이빗 네트워크에서만 앱에 액세스할 수 있도록 합니다. VNet 통합은 앱에서 VNet으로 아웃바운드 호출을 하기 위한 것입니다. 

<!--Links-->
[ASEintro]: https://docs.microsoft.com/azure/app-service/environment/intro
[Networkingfeatures]: https://docs.microsoft.com/azure/app-service/networking-features