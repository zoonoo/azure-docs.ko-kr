---
author: craigktreasure
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 12/13/2018
ms.author: crtreasu
ms.openlocfilehash: dc8d3c2d400204f53b05bb5536af95679541f3f6
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2019
ms.locfileid: "58305200"
---
## <a name="create-a-spatial-anchors-resource"></a>Spatial Anchors 리소스 만들기

<a href="https://portal.azure.com" target="_blank">Azure 포털</a>로 이동합니다.

Azure Portal의 왼쪽 탐색 창에서 **리소스 만들기**를 선택합니다.

검색 상자를 사용하여 **Spatial Anchors**를 검색합니다.

   ![Spatial Anchors 검색](./media/spatial-anchors-get-started-create-resource/portal-search.png)

**Spatial Anchors**를 선택합니다. 대화 상자에서 **만들기**를 선택합니다.

**Spatial Anchors 계정** 대화 상자에서 다음을 수행합니다.

- 고유한 리소스 이름을 입력합니다.
- 리소스를 연결할 구독을 선택합니다.
- **새로 만들기**를 선택하여 리소스 그룹을 만듭니다. 이름을 **myResourceGroup**으로 지정하고 **확인**을 선택합니다.
      [!INCLUDE [resource group intro text](resource-group.md)]
- 리소스를 배치할 위치(Azure 지역)를 선택합니다.
- **새로 만들기**를 선택하여 리소스 만들기를 시작합니다.

   ![리소스 만들기](./media/spatial-anchors-get-started-create-resource/create-resource-form.png)

리소스가 만들어지면 리소스 속성을 볼 수 있습니다. 리소스의 **계정 ID** 값을 텍스트 편집기에 복사합니다. 나중에 필요합니다.

   ![리소스 속성](./media/spatial-anchors-get-started-create-resource/view-resource-properties.png)

**설정** 아래에서 **키**를 선택합니다. **기본 키** 값을 텍스트 편집기에 복사합니다. 이 값은 `Account Key`입니다. 나중에 필요합니다.

   ![계정 키](./media/spatial-anchors-get-started-create-resource/view-account-key.png)
