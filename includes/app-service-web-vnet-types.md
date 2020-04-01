---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 02/27/2020
ms.author: ccompy
ms.openlocfilehash: 41b7c7163ff56c848b1b66e606c06ba45d36e610
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80419517"
---
* 격리를 제외한 모든 범위의 가격 책정 계획을 지원하는 다중 테넌트 시스템입니다.
* 가상 네트워크에 배포되고 격리된 가격 책정 계획 앱을 지원하는 앱 서비스 환경입니다.

VNet 통합 기능은 다중 테넌트 앱에서 사용됩니다. 앱이 앱 [서비스 환경에][ASEintro]있는 경우 이미 가상 네트워크에 있으며 동일한 가상 네트워크의 리소스에 도달하기 위해 VNet 통합 기능을 사용할 필요가 없습니다. 모든 네트워킹 기능에 대한 자세한 내용은 [App Service 네트워킹 기능을][Networkingfeatures]참조하십시오.

VNet 통합은 가상 네트워크의 리소스에 대한 앱 액세스 권한을 제공하지만 가상 네트워크에서 앱에 대한 인바운드 개인 액세스 권한을 부여하지는 않습니다. 개인 사이트 액세스란 Azure 가상 네트워크 내에서와 같은 개인 네트워크에서만 앱에 액세스할 수 있도록 하는 것을 말합니다. VNet 통합은 앱에서 가상 네트워크로 아웃바운드 호출을 수행하는 데만 사용됩니다. VNet 통합 기능은 동일한 리전의 가상 네트워크와 다른 지역의 가상 네트워크에서 사용될 때 다르게 작동합니다. VNet 통합 기능에는 두 가지 변형이 있습니다.

* **지역 별 VNet 통합**: 동일한 리전의 Azure Resource Manager 가상 네트워크에 연결할 때 통합하는 가상 네트워크에 전용 서브넷이 있어야 합니다.
* **게이트웨이 필수 VNet 통합**: 다른 지역의 가상 네트워크 또는 동일한 리전의 클래식 가상 네트워크에 연결하는 경우 대상 가상 네트워크에 프로비전된 Azure 가상 네트워크 게이트웨이가 필요합니다.

VNet 통합 기능은 다음과 같은 기능입니다.

* 표준, 프리미엄, PremiumV2 또는 탄력적 프리미엄 요금제가 필요합니다.
* TCP 및 UDP를 지원합니다.
* Azure 앱 서비스 앱 및 기능 앱과 함께 작업합니다.

VNet 통합이 지원하지 않는 몇 가지 사항이 있습니다.

* 드라이브 탑재
* 활성 디렉터리 통합.
* Netbios.

게이트웨이에 필요한 VNet 통합은 대상 가상 네트워크 또는 피어링 또는 VPN을 사용하여 대상 가상 네트워크에 연결된 네트워크의 리소스에만 액세스를 제공합니다. 게이트웨이에 필요한 VNet 통합은 Azure ExpressRoute 연결에서 사용할 수 있는 리소스에 대한 액세스를 사용하거나 서비스 끝점에서 작동하도록 설정하지 않습니다.

사용된 버전에 관계없이 VNet Integration는 가상 네트워크의 리소스에 대한 앱 액세스 권한을 제공하지만 가상 네트워크에서 앱에 대한 인바운드 개인 액세스 권한을 부여하지는 않습니다. 개인 사이트 액세스란 Azure 가상 네트워크 내에서와 같은 개인 네트워크에서만 앱에 액세스할 수 있도록 하는 것을 말합니다. VNet 통합은 앱에서 가상 네트워크로 아웃바운드 호출을 하기 위한 것입니다.

<!--Links-->
[ASEintro]: https://docs.microsoft.com/azure/app-service/environment/intro
[Networkingfeatures]: https://docs.microsoft.com/azure/app-service/networking-features
