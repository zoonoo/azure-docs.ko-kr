---
title: Azure에서 LUIS 앱의 버전 관리 | Microsoft Docs
description: LUIS(Language Understanding) 응용 프로그램의 버전을 관리하는 방법을 알아봅니다.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/29/2017
ms.author: diberry
ms.openlocfilehash: 4941cf533f1b860ead07a416d5af6f62a1978305
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/24/2018
ms.locfileid: "39226597"
---
# <a name="manage-versions"></a>버전 관리

모델에서 작업할 때마다 앱의 다른 [버전](luis-concept-version.md)을 만듭니다. 

## <a name="set-active-version"></a>활성 버전 설정
버전을 사용하려면 **내 앱** 페이지에서 해당 이름을 선택하여 앱을 연 다음, 위쪽 표시줄에서 **설정**을 선택합니다.

![버전 페이지](./media/luis-how-to-manage-versions/settings.png)

**설정** 페이지를 사용하면 버전 및 공동 작업자를 비롯한 전체 앱에 대한 설정을 구성할 수 있습니다. 

## <a name="clone-a-version"></a>버전 복제
1. **설정** 페이지에서 앱 설정 및 공동 작업자 섹션 다음에 복제하려는 버전의 행을 찾습니다. 맨 오른쪽에 있는 줄임표(***...***) 단추를 선택합니다. 

    ![버전 행 속성](./media/luis-how-to-manage-versions/version-section.png)

2. 목록에서 **복제**를 선택합니다.

    ![버전 행 속성 선택](./media/luis-how-to-manage-versions/version-three-dots-modal.png)

3. **복제 버전** 대화 상자에서 “0.2”와 같은 새 버전에 사용할 이름을 입력합니다.

   ![버전 복제 대화 상자](./media/luis-how-to-manage-versions/version-clone-version-dialog.png)
 
 > [!NOTE]
 > 버전 ID는 문자, 숫자 또는 ‘.’으로만 구성할 수 있으며 10자 이하여야 합니다.
 
 지정한 이름으로 새 버전이 생성되고 활성 버전으로 설정됩니다.
 
  ![버전이 생성되고 목록에 추가됨](./media/luis-how-to-manage-versions/new-version.png)

 > [!NOTE]
 > 앞에 나온 이미지에 표시된 대로 게시된 버전은 게시된 슬롯의 유형(프로덕션(녹색), 준비(빨강) 및 둘 다(검정색))을 나타내는 색으로 표시된 항목으로 연결됩니다. 교육 및 게시 날짜는 각 버전에 대해 표시됩니다.

## <a name="set-active-version"></a>활성 버전 설정
1. **설정** 페이지의 **버전** 목록에서 맨 오른쪽에 줄임표(***...***) 단추를 선택합니다.

2. 팝업 목록에서 **활성 상태로 설정**을 선택합니다.

    ![활성 버전 설정](./media/luis-how-to-manage-versions/set-active-version.png)

    다음 스크린샷에 표시된 것처럼 활성 버전은 밝은 파란색으로 강조 표시됩니다.

    ![활성 버전](./media/luis-how-to-manage-versions/set-active-version-done.png) 


## <a name="import-version"></a>버전 가져오기
JSON 파일에서 버전을 가져올 수 있습니다. 버전을 가져오면 새 버전이 활성 버전이 됩니다.

**버전을 가져오려면 다음을 수행합니다.**

1. **설정** 페이지에서 **새 버전 가져오기** 단추를 선택합니다.

    ![가져오기 단추](./media/luis-how-to-manage-versions/import-version.png)

2. **찾아보기**를 선택하고 JSON 파일을 선택합니다.

    ![버전 가져오기 대화 상자](./media/luis-how-to-manage-versions/import-version-dialog.png)

JSON 파일에 있는 버전이 이미 앱에 있는 경우 버전 ID를 설정하기만 하면 됩니다.

## <a name="export-version"></a>버전 내보내기
JSON 파일로 버전을 내보낼 수 있습니다.

**버전을 내보내려면 다음을 수행합니다.**

1. **설정** 페이지의 **버전** 목록에서 맨 오른쪽에 줄임표(***...***) 단추를 선택합니다.

2. 작업의 팝업 목록에서 **내보내기**를 선택하고 파일을 저장하려는 위치를 선택합니다.

## <a name="delete-a-version"></a>버전 삭제
버전을 삭제할 수 있지만 앱의 버전을 하나 이상 유지해야 합니다. 활성 버전을 제외한 모든 버전을 삭제할 수 있습니다. 

1. **설정** 페이지의 **버전** 목록에서 맨 오른쪽에 줄임표(***...***) 단추를 선택합니다.

2. 작업의 팝업 목록에서 **삭제**를 선택하고 파일을 저장하려는 위치를 선택합니다.

    ![버전 삭제 확인](./media/luis-how-to-manage-versions/delete-menu.png) 


## <a name="rename-a-version"></a>버전 이름 바꾸기
사용 중이 아닌 이름으로 버전 이름을 바꿀 수 있습니다.  

1. **설정** 페이지의 **버전** 목록에서 맨 오른쪽에 줄임표(***...***) 단추를 선택합니다.

2. 작업의 팝업 목록에서 **이름 바꾸기**를 선택합니다.

3. 새 버전 이름을 입력하고 **완료**를 선택합니다.

    ![버전 이름 바꾸기 확인](./media/luis-how-to-manage-versions/rename-popup.png) 
