---
title: Object Anchors 모델을 시각화하는 방법
description: 변환된 모델을 시각화하는 방법을 설명합니다.
author: rgarcia
manager: vrivera
ms.author: rgarcia
ms.date: 05/28/2021
ms.topic: overview
ms.service: azure-object-anchors
ms.openlocfilehash: a9140004d9f4d79cabb9890851ba5c5cb7b815f2
ms.sourcegitcommit: e39ad7e8db27c97c8fb0d6afa322d4d135fd2066
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111987919"
---
# <a name="how-to-visualize-an-object-anchors-model"></a>Object Anchors 모델을 시각화하는 방법

변환된 모델은 시각화하지 않아도 사용할 수 있습니다. 그러나 원하는 경우 사용하기 전에 해당 메시를 쉽게 볼 수 있는 방법이 있습니다.

[Unity 앱 빠른 시작](quickstarts/get-started-unity-hololens.md)의 단계를 수행하고 한 가지만 약간 변경합니다. 샘플 장면을 빌드할 때 **AOASampleScene** 을 여는 대신 **VisualizeScene** 을 열고 장면 빌드 목록에 추가합니다. 그런 다음, **빌드 설정** 에서 **VisualizeScene** 옆에‘만’ 확인 표시가 있는지 확인합니다. 다른 모든 장면은 포함해서는 안 됩니다.

:::image type="content" source="../../includes/media/object-anchors-quickstarts-unity/aoa-unity-build-settings-visualize.png" alt-text="빌드 설정 시각화":::

**빌드** 단추를 선택하는 ‘대신’ **빌드 설정** 대화 상자를 닫습니다.

**계층 구조** 패널에서 **시각화 도우미** GameObject를 선택합니다.

:::image type="content" source="../../includes/media/object-anchors-quickstarts-unity/aoa-unity-hierarchy.png" alt-text="계층 구조":::

**검사기** 패널의 **메시 로더(스크립트)** 섹션에서 **모델 경로** 속성을 찾고 파일 확장명을 포함하여 Object Anchors 모델 파일의 경로를 입력합니다.

:::image type="content" source="../../includes/media/object-anchors-quickstarts-unity/aoa-unity-inspector.png" alt-text="검사기":::

Unity 편집기 위쪽에서 **재생** 단추를 선택한 다음, **장면** 보기가 선택되어 있는지 확인합니다.

:::image type="content" source="../../includes/media/object-anchors-quickstarts-unity/aoa-unity-editor.png" alt-text="재생 및 장면 보기":::

이제 [Unity의 장면 보기 탐색 컨트롤](https://docs.unity3d.com/Manual/SceneViewNavigation.html)을 사용하여 Object Anchors 모델을 검사할 수 있습니다.

:::image type="content" source="./media/object-anchors-visualize.png" alt-text="모델 시각화":::
