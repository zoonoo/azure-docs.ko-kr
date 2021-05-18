---
title: 디바이스 번들 및 간접 연결된 디바이스 인증
titleSuffix: Azure Certified
description: 인증을 위해 간접 연결된 디바이스를 제출하는 방법을 참조하세요.
author: cbroad
ms.author: cbroad
ms.date: 02/23/2021
ms.topic: how-to
ms.service: certification
ms.openlocfilehash: 3a4fd2838c0ddf6d7d03d68f105fc59471b77dea
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107304480"
---
# <a name="device-bundles-and-indirectly-connected-devices"></a>디바이스 번들 및 간접 연결된 디바이스

디바이스를 통해 Azure와 상호 작용하는 디바이스를 지원하려면 SaaS 또는 PaaS 제품, 제출 포털(https://certify.azure.com/) 및 디바이스 범주(https://devicecatalog.azure.com) 를 통해 번들링 및 종속성 개념을 사용하도록 설정하여 이러한 디바이스 조합 액세스를 촉진하고 Azure Certified Device 프로그램에 액세스합니다.

제공되는 제품군 및 서비스에 따라 다음 단계를 함께 수행해야 할 수도 있습니다.


![프로젝트 종속성 만들기](./media/indirect-connected-device/picture-1.png )
## <a name="sensors-and-indirect-devices"></a>센서 및 간접 디바이스
많은 센서에는 Azure에 연결하기 위한 디바이스가 필요합니다. 또한 센서 디바이스와 함께 작동할 여러 호환 디바이스가 있을 수 있습니다. **이러한 시나리오에 맞추려면 해당 디바이스를 통해 정보를 전달하는 센서를 인증하기 전에 디바이스를 먼저 인증해야 합니다.**

제출 조합의 예제 매트릭스 ![제출 예제](./media/indirect-connected-device/picture-2.png )

별도의 디바이스가 필요한 센서를 인증하려면 다음을 수행합니다.
1.  먼저 [디바이스를 인증](https://certify.azure.com)하고 Azure Certified Device 카탈로그에 게시합니다.
    - 위의 예에서와 같이 호환되는 통과 디바이스가 여러 개인 경우 인증을 위해 별도로 제출하고 카탈로그에도 게시합니다.
2.  디바이스를 통해 연결된 센서를 사용하여 인증받을 센서를 제출합니다.
    * “디바이스 세부 정보” 섹션의 “종속성” 탭에서 다음 값을 설정합니다.
        * 종속성 형식 = “하드웨어 게이트웨이”
        * 종속성 URL = “디바이스 카탈로그의 디바이스로 연결되는 URL 링크”
        * 테스트 중에 사용됨 = “예”
        * 디바이스 카탈로그에서 제품 설명을 보는 사용자에게 제공해야 하는 고객 맞춤 설명을 추가합니다. (예: “Azure에 연결하려면 센서에 시리즈 100 디바이스가 필요합니다.”)

3.  이 디바이스에 대해 다른 디바이스를 선택 항목으로 추가하려는 경우 “+ 추가 종속성 추가”를 선택할 수 있습니다. 그런 다음 동일한 지침을 따르고 테스트 중에 사용되지 않았음에 유의합니다. 고객 맞춤 설명에서 테스트 중에 사용된 디바이스에 대한 대안으로 다른 디바이스가 이 센서와 연결되어 있다는 것을 고객이 알고 있는지 확인합니다.

![대체 텍스트](./media/indirect-connected-device/picture-3.png "하드웨어 종속성 형식")

## <a name="paas-and-saas-offerings"></a>PaaS 및 SaaS 제품
제품 포트폴리오의 일부로 인증하는 디바이스가 있을 수 있지만 디바이스에는 회사 또는 타사의 다른 서비스도 필요합니다. 이 종속성을 추가하려면 다음 단계를 수행합니다.
1. 디바이스의 제출 프로세스를 시작합니다.
2. “종속성” 탭에서 다음 값을 설정합니다.
    - 종속성 형식 = “소프트웨어 서비스”
    - 서비스 이름 = “[사용자 제품 이름]”
    - 종속성 URL = “서비스를 설명하는 제품 페이지로 연결되는 URL 링크”
    - Azure Certified Device에서 제품 설명을 보는 사용자에게 제공해야 하는 고객 맞춤 설명을 추가합니다.
3. 이 디바이스에 대한 선택 사항으로 추가하려는 다른 소프트웨어, 서비스 또는 하드웨어 종속성이 있는 경우 “+ 추가 종속성 추가”를 선택하고 동일한 지침을 따를 수 있습니다.

![소프트웨어 종속성 형식](./media/indirect-connected-device/picture-4.png )

## <a name="bundled-products"></a>번들 제품
번들 제품 목록은 단순히 하나의 제품 목록에서 번들의 일부로 판매되는 다른 구성 요소가 있는 디바이스를 성공적으로 인증하는 것입니다. 온도 센서 및 카메라 센서(#1)와 같은 추가 구성 요소가 포함된 디바이스를 제출하거나 통과 디바이스(#2)가 포함된 터치 센서를 제출할 수 있습니다. “구성 요소” 기능을 통해 목록에 여러 구성 요소를 추가할 수 있습니다.

이 작업을 수행하려면 제품 목록 이미지의 형식을 지정하여 이 제품이 다른 구성 요소와 함께 제공된다는 것을 표시합니다.  또한 인증을 위해 번들에 추가 서비스가 필요한 경우 서비스 종속성을 통해 해당 서비스를 식별해야 합니다.
번들 제품의 예제 매트릭스

![번들 제출 예제](./media/indirect-connected-device/picture-5.png )

Azure Certified Device 포털에서 구성 요소 기능을 사용하는 방법에 대한 자세한 설명은 [도움말 설명서](./how-to-using-the-components-feature.md)를 참조하세요. 

디바이스가 동일한 제품에 별도 센서를 사용하는 통과 디바이스인 경우 통과 디바이스를 반영하는 하나의 구성 요소와 센서를 반영하는 또 하나의 구성 요소를 만듭니다. 디바이스 세부 정보 섹션의 제품 정보 탭에서 구성 요소를 프로젝트에 추가할 수 있습니다.

![구성 요소 추가](./media/indirect-connected-device/picture-6.png )

통과 디바이스의 경우, 구성 요소 형식을 고객에게 보낼 제품으로 설정하고, 제품과 관련된 다른 필드를 채웁니다. 예:

![구성 요소 세부 정보](./media/indirect-connected-device/picture-7.png )

센서의 경우, 두 번째 구성 요소를 추가한 다음, 구성 요소 형식은 주변 디바이스로 설정하고 연결 메서드는 개별로 설정합니다. 예:

![두 번째 구성 요소 세부 정보](./media/indirect-connected-device/picture-8.png )

센서 구성 요소가 만들어지면 세부 정보를 편집하고 센서 탭으로 이동한 다음 센서 세부 정보를 추가합니다. 예:

![센서 세부 정보](./media/indirect-connected-device/picture-9.png )

프로젝트 세부 정보를 모두 추가하고 일반적인 절차에 따라 인증받을 디바이스를 제출합니다.

