---
author: craigktreasure
ms.service: azure-object-anchors
ms.topic: include
ms.date: 03/02/2021
ms.author: crtreasu
ms.openlocfilehash: d8dfc3d4b7a8447250481b98c1adadc865a29da1
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102532659"
---
### <a name="upload-your-model"></a>모델 업로드

Object Anchors 모델이 아직 없는 경우 [모델 만들기](/azure/object-anchors/quickstarts/get-started-model-conversion)의 지침에 따라 모델을 만듭니다. 그런 다음, 여기로 돌아옵니다.

HoloLens가 Windows Device Portal에 연결된 상태에서 다음 단계에 따라 앱에서 사용할 모델을 업로드합니다.

1. Windows Device Portal에서 **시스템 > 파일 탐색기 > LocalAppData** 로 이동합니다. 여기의 앱 목록에 애플리케이션이 표시됩니다.

    :::image type="content" source="./media/object-anchors-quickstarts-unity/portal-localappdata.png" alt-text="파일 탐색기":::

2. 애플리케이션을 열고 `LocalState` 폴더를 클릭합니다.

    :::image type="content" source="./media/object-anchors-quickstarts-unity/portal-localstate.png" alt-text="LocalState 폴더 열기":::

3. 모델 파일을 `LocalState` 폴더에 업로드합니다.

    :::image type="content" source="./media/object-anchors-quickstarts/portal-upload-model.png" alt-text="포털에서 모델 업로드":::

    HoloLens에서 애플리케이션을 다시 시작합니다. 이제 모델과 일치하는 개체를 감지할 수 있습니다.
