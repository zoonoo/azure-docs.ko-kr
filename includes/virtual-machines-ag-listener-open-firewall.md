---
author: rockboyfor
ms.service: virtual-machines
ms.topic: include
origin.date: 10/26/2018
ms.date: 11/26/2018
ms.author: v-yeche
ms.openlocfilehash: e128f3c67a41322d9c25a8d6941e937729760bf4
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62097696"
---
이 단계에서는 부하 분산된 엔드포인트에 대한 프로브 포트(이전에 지정한 대로 59999)를 여는 방화벽 규칙과 가용성 그룹 수신기 포트를 여는 다른 규칙을 만듭니다. 가용성 그룹 복제본이 포함된 VM에 부하 분산된 엔드포인트를 만들었으므로 해당 VM에서 수신기 포트와 프로브 포트를 열어야 합니다.

1. 복제본을 호스팅하는 VM에서 **고급 보안이 포함된 Windows 방화벽**을 시작합니다.

2. **인바운드 규칙**을 마우스 오른쪽 단추로 클릭한 다음 **새 규칙**을 클릭합니다.

3. **규칙 유형** 페이지에서 **포트**를 선택한 후 **다음**을 클릭합니다.

4. **프로토콜 및 포트** 페이지에서 **TCP**를 선택하고 **특정 로컬 포트** 상자에서 **59999**를 입력한 후 **다음**을 클릭합니다.

5. **작업** 페이지에서 **연결 허용**을 선택한 상태로 유지하고 **다음**을 클릭합니다.

6. **프로필** 페이지에서 기본 설정을 그대로 적용하고 **다음**을 클릭합니다.

7. **이름** 페이지의 **이름** 텍스트 상자에서 **AlwaysOn 수신기 프로브 포트**와 같은 규칙 이름을 지정한 다음 **마침**을 클릭합니다.

8. 가용성 그룹 수신기 포트(이전에 스크립트의 $EndpointPort 매개 변수에서 지정된 대로)에 대해 위의 단계를 반복한 다음 **AlwaysOn 수신기 포트**와 같은 적절한 규칙 이름을 지정합니다.

<!-- Update_Description: update meta properties -->