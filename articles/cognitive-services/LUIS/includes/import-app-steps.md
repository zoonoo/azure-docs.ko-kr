---
title: 앱 단계 가져오기
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 12/03/2019
ms.author: diberry
ms.openlocfilehash: b0d1735df7a3ac36e20af771939aec6b8013f2df
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74840819"
---
1. [미리 보기 LUIS 포털](https://preview.luis.ai)의 **내 앱** 페이지에서 **가져오기**를 선택한 다음, **JSON으로 가져오기**를 선택합니다. 이전 단계에서 저장한 JSON 파일을 찾습니다. 앱 이름은 변경할 필요가 없습니다. **완료**를 선택합니다.

1. **관리** 섹션의 **버전** 탭에서 버전을 선택한 다음, **복제**를 선택하여 버전을 복제하고 새 10자 이름을 지정한 다음, **완료**를 선택하여 복제 프로세스를 마칩니다. 버전 이름이 URL 경로의 일부로 사용되므로 이름에는 URL에 유효하지 않은 문자가 포함될 수 없습니다.

    > [!TIP]
    > 앱을 수정하기 전에 새 버전으로 복제하는 것이 가장 좋습니다. 버전을 완료한 후에는 버전을 .json 또는 .lu 파일로 내보내고, 파일을 소스 제어 시스템에 체크 인합니다.

1. **빌드**, **의도**를 차례로 선택하여 LUIS 앱의 기본 구성 요소인 의도를 확인합니다.

    ![[버전] 페이지에서 [의도] 페이지로 변경합니다.](../media/tutorial-machine-learned-entity/new-version-imported-app.png)