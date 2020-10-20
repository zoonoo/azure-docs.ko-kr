---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 08/14/2020
ms.author: rgarcia
ms.openlocfilehash: 9c20726ce412e651aba0fdd202031d342350c922
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/13/2020
ms.locfileid: "91971490"
---
**빌드**를 선택합니다. 열리는 창에서 Xcode 프로젝트를 내보낼 폴더를 선택합니다.

   내보내기가 완료되면 내보낸 Xcode 프로젝트가 포함된 폴더가 표시됩니다.

   > [!NOTE]
   > 바꿀지 추가할지 묻는 메시지와 함께 창이 나타나면 **추가**를 선택하는 것이 더 빠릅니다. 장면의 자산을 변경하려는 경우에는 **바꾸기**를 선택해야 합니다. 예를 들어, 부모/자식 관계를 추가, 제거 또는 변경하거나 속성을 추가, 제거 또는 변경할 수 있습니다. 소스 코드만 변경하려는 경우에는 **추가**를 선택하는 것으로 충분합니다.

## <a name="open-the-xcode-project"></a>Xcode 프로젝트 열기

이제 Xcode에서 `Unity-iPhone.xcodeproj` 프로젝트를 열 수 있습니다. 

Xcode를 시작하고 내보낸 `Unity-iPhone.xcodeproj` 프로젝트를 열거나 프로젝트를 내보낸 위치에서 다음 명령을 실행하여 Xcode에서 프로젝트를 시작할 수 있습니다.

 ```bash
open ./Unity-iPhone.xcodeproj
```

루트 **Unity-iPhone** 노드를 선택하여 프로젝트 설정을 살펴본 다음, **일반** 탭을 선택합니다.

**서명** 아래에서 **서명 자동 관리**를 사용하는지 확인합니다. 그렇지 않은 경우, 표시되는 창에서 **자동 사용**을 선택하여 이 옵션을 사용하도록 설정한 다음, 빌드 설정을 재설정합니다.

**배포 정보**에서 **배포 대상**이 **11.0**으로 설정되어 있는지 확인합니다.

## <a name="deploy-the-app-to-your-ios-device"></a>iOS 디바이스에 앱 배포

iOS 디바이스를 Mac에 연결하고 **활성 스키마**를 iOS 디바이스로 설정합니다.

   ![디바이스를 선택하는 내 iPhone 단추의 스크린샷.](./media/spatial-anchors-unity/select-device.png)

**빌드를 선택한 다음, 현재 스키마를 실행**합니다.

   !["배포 및 실행" 화살표 단추의 스크린샷.](./media/spatial-anchors-unity/deploy-run.png)
