---
author: craigktreasure
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 12/13/2018
ms.author: crtreasu
ms.openlocfilehash: 9e232c5a8f1c2e38c04ea2236691554952f443e8
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/24/2019
ms.locfileid: "56752853"
---
## <a name="create-a-spatial-anchors-resource"></a>Spatial Anchors 리소스 만들기

1. <a href="https://portal.azure.com" target="_blank">Azure Portal</a>로 이동합니다.

2. Azure Portal의 왼쪽 메뉴에서 **리소스 만들기**를 선택합니다.

3. 검색 창에서 "Spatial Anchors"를 검색합니다.

   ![Spatial Anchors 검색](./media/spatial-anchors-get-started-create-resource/portal-search.png)

4. **Spatial Anchors(미리 보기)** 를 선택하면 대화 상자가 열리고 **만들기**를 선택합니다.

5. **Spatial Anchors 계정** 양식에서:

   1. 고유한 리소스 이름을 지정합니다.
   2. 리소스를 연결할 구독을 선택합니다.
   3. **새로 만들기**를 선택하여 리소스 그룹을 만들고 리소스 그룹의 이름을 **myResourceGroup**으로 지정하고 **확인**을 선택합니다.
      [!INCLUDE [resource group intro text](resource-group.md)]
   4. 리소스를 배치할 위치(지역)를 선택합니다.
   5. **새로 만들기**를 선택하여 리소스 만들기를 시작합니다.

   ![리소스 만들기](./media/spatial-anchors-get-started-create-resource/create-resource-form.png)

6. 리소스가 성공적으로 만들어졌으면 리소스 속성을 볼 수 있습니다. 나중에 필요하므로 리소스의 **계정 ID** 값을 적어둡니다.

   ![리소스 속성 보기](./media/spatial-anchors-get-started-create-resource/view-resource-properties.png)

7. **설정**에서 **키**를 선택하고 **기본 키** 값을 적어둡니다. 이 값은 `Account Key`이며 나중에 사용됩니다.

   ![계정 키 보기](./media/spatial-anchors-get-started-create-resource/view-account-key.png)
