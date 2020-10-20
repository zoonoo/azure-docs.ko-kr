---
author: craigktreasure
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 12/13/2018
ms.author: crtreasu
ms.openlocfilehash: b83951cd743afc7e249e3ee37182007bbe40cb5c
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/13/2020
ms.locfileid: "91971398"
---
## <a name="create-a-spatial-anchors-resource"></a>Spatial Anchors 리소스 만들기

<a href="https://portal.azure.com" target="_blank">Azure 포털</a>로 이동합니다.

왼쪽 창에서 **리소스 만들기**를 선택합니다.

검색 상자를 사용하여 **Spatial Anchors**를 검색합니다.

![Spatial Anchors 검색 결과를 보여주는 스크린샷.](./media/spatial-anchors-get-started-create-resource/portal-search.png)

**Spatial Anchors**를 선택한 다음, **만들기**를 선택합니다.

**Spatial Anchors 계정** 창에서 다음을 수행합니다.

* 일반 영숫자 문자를 사용하여 고유한 리소스 이름을 입력합니다.  
* 리소스를 연결할 구독을 선택합니다.  
* **새로 만들기**를 선택하여 리소스 그룹을 만듭니다. 이름을 **myResourceGroup**으로 지정한 다음, **확인**을 선택합니다.  

  [!INCLUDE [resource group intro text](resource-group.md)]
  
* 리소스를 배치할 위치(Azure 지역)를 선택합니다.  
* **새로 만들기**를 선택하여 리소스 만들기를 시작합니다.

![리소스를 만들기 위한 Spatial Anchors 창의 스크린샷.](./media/spatial-anchors-get-started-create-resource/create-resource-form.png)

리소스를 만든 후 Azure Portal은 배포가 완료되었음을 표시합니다. 
   
![리소스 배포가 완료되었음을 보여주는 스크린샷입니다.](./media/spatial-anchors-get-started-create-resource/deployment-complete.png)

**리소스로 이동**을 선택합니다. 이제 리소스 속성을 볼 수 있습니다. 
   
나중에 사용하기 위해 리소스의 **계정 ID** 값을 텍스트 편집기에 복사합니다.

![리소스 속성 창의 스크린샷.](./media/spatial-anchors-get-started-create-resource/view-resource-properties.png)

또한 리소스의 **계정 도메인** 값을 텍스트 편집기에 복사합니다.

![리소스의 계정 도메인 값을 보여주는 스크린샷.](./media/spatial-anchors-get-started-create-resource/view-resource-domain.png)

**설정** 아래에서 **키**를 선택합니다. 나중에 사용하기 위해 **기본 키** 값, **계정 키**를 텍스트 편집기에 복사합니다.

![계정에 대한 키 창의 스크린샷.](./media/spatial-anchors-get-started-create-resource/view-account-key.png)
