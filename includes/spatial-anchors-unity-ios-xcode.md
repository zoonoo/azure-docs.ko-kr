---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 1/29/2019
ms.author: rgarcia
ms.openlocfilehash: b802c9dbd0cef65325cb03538b68b49c57b85bb3
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/26/2019
ms.locfileid: "56890931"
---
**빌드**를 선택하여 대화 상자를 엽니다. 그런 다음, Xcode 프로젝트를 내보낼 폴더를 선택합니다.

내보내기가 완료되면 내보낸 Xcode 프로젝트가 포함된 폴더가 표시됩니다.

### <a name="open-the-xcode-project"></a>Xcode 프로젝트 열기

내보낸 Xcode 프로젝트 폴더에서 다음 명령을 실행하여 프로젝트에 필요한 CocoaPods를 설치합니다.

```bash
pod install --repo-update
```

이제 `Unity-iPhone.xcworkspace`를 열어서 Xcode에서 프로젝트를 열 수 있습니다.

```bash
open ./Unity-iPhone.xcworkspace
```

> [!NOTE]
> `library not found for -lPods-Unity-iPhone` 오류가 표시되는 경우 `.xcworkspace` 대신 `.xcodeproj` 파일을 열었을 수 있습니다. `.xcworkspace`를 열고 다시 시도하세요.

루트 **Unity-iPhone** 노드를 선택하여 프로젝트 설정을 살펴보고 **일반** 탭을 선택합니다.

**서명**에서 **Automatically manage signing**(서명 자동으로 관리)을 선택합니다. 표시 되는 대화 상자에서 **Enable Automatic**(자동 사용)을 선택하여 빌드 설정을 다시 설정합니다.

**배포 정보**에서 **배포 대상**이 `11.0`으로 설정되어 있는지 확인합니다.

### <a name="deploy-the-app-to-your-ios-device"></a>iOS 디바이스에 앱 배포

iOS 디바이스를 Mac에 연결하고 **활성 스키마**를 iOS 디바이스로 설정합니다.

![디바이스 선택](./media/spatial-anchors-unity/select-device.png)

**빌드를 선택한 다음, 현재 스키마를 실행**합니다.

![배포 및 실행](./media/spatial-anchors-unity/deploy-run.png)