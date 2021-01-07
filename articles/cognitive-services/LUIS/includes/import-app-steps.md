---
title: 앱 단계 가져오기
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: include
ms.date: 10/16/2020
ms.openlocfilehash: b70a22e502de5441b5c91a8f33357375834f54e1
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93130728"
---
1. [LUIS 포털](https://www.luis.ai)의 **내 앱** 페이지에서 **+ 대화용 새 앱** 을 선택한 다음, **JSON으로 가져오기** 를 선택합니다. 이전 단계에서 저장한 JSON 파일을 찾습니다. 앱 이름은 변경할 필요가 없습니다. **완료** 를 선택합니다.

1. **관리** 섹션의 **버전** 탭에서 `0.1` 버전을 선택한 다음, **복제** 를 선택하여 버전을 복제하고 `ml-entity`의 새 이름을 지정한 다음, **완료** 를 선택하여 복제 프로세스를 마칩니다. 버전 이름이 URL 경로의 일부로 사용되므로 이름에는 URL에 유효하지 않은 문자가 포함될 수 없습니다.

    > [!TIP]
    > 앱을 수정하기 전에 새 버전으로 복제하는 것이 가장 좋습니다. 버전 변경을 완료하면 버전을 .json 또는 .lu 파일로 내보내고, 파일을 소스 제어 시스템에 체크 인합니다.

1. 화면 위쪽에서 **빌드** 를 선택한 다음, 왼쪽 탐색 메뉴에서 **의도** 를 클릭합니다. LUIS 앱의 기본 구성 요소인 앱의 의도가 표시됩니다.
