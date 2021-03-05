---
author: craigktreasure
ms.service: azure-object-anchors
ms.topic: include
ms.date: 04/03/2020
ms.author: crtreasu
ms.openlocfilehash: 43c8c578587c65b6e0317caf77ff2d0604cb4fa3
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/03/2021
ms.locfileid: "102044669"
---
### <a name="build-and-deploy-the-app"></a>앱 빌드 및 배포

Unity에서 생성한 `.sln` 파일을 엽니다. 빌드 구성을 다음과 같이 변경합니다.

:::image type="content" source="./media/object-anchors-quickstarts-unity/aoa-unity-vs-build-config.png" alt-text="빌드 구성":::

다음으로, 앱을 배포하고 디버그하기 위해 **원격 컴퓨터 IP 주소** 를 구성해야 합니다.

앱 프로젝트를 마우스 오른쪽 단추로 클릭하고 **속성** 을 선택합니다. 속성 페이지에서 **구성 속성 -> 디버깅** 을 선택합니다. **컴퓨터 이름** 값을 HoloLens 디바이스의 IP 주소로 변경하고 **적용** 을 클릭합니다.

:::image type="content" source="./media/object-anchors-quickstarts-unity/aoa-unity-vs-remote-debug.png" alt-text="원격 디버그":::

속성 페이지를 닫습니다. **원격 컴퓨터** 를 클릭합니다. 앱이 빌드되고 원격 디바이스에 배포되기 시작합니다. 디바이스가 활성 상태인지 확인합니다.
