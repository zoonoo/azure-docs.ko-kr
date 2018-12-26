---
title: LUIS 앱에서 버전 관리
titleSuffix: Azure Cognitive Services
description: 버전을 통해 다양한 모델을 빌드하고 게시할 수 있습니다. 모델에 변경 사항을 적용하기 전에 현재 활성 모델을 앱의 다른 버전에 을 복제하는 것이 좋습니다.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: db461191b70aabc322e570ecc814a076c21206f2
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47033790"
---
# <a name="manage-versions"></a>버전 관리

버전을 통해 다양한 모델을 빌드하고 게시할 수 있습니다. 모델에 변경 사항을 적용하기 전에 현재 활성 모델을 앱의 다른 [버전](luis-concept-version.md)에 복제하는 것이 좋습니다. 

버전을 사용하려면 **내 앱** 페이지에서 해당 이름을 선택하여 앱을 연 다음, 위쪽 표시줄에서 **관리**를 선택하고 왼쪽 탐색에서 **버전**을 선택합니다. 

버전 목록은 게시된 버전, 게시 위치 및 현재 활성 버전을 보여 줍니다. 

[![](./media/luis-how-to-manage-versions/versions-import.png "섹션, 버전 페이지 관리")](./media/luis-how-to-manage-versions/versions-import.png#lightbox)

## <a name="clone-a-version"></a>버전 복제

1. 복제할 버전을 선택한 다음, 도구 모음에서 **복제**를 선택합니다. 

2. **복제 버전** 대화 상자에서 “0.2”와 같은 새 버전에 사용할 이름을 입력합니다.

   ![버전 복제 대화 상자](./media/luis-how-to-manage-versions/version-clone-version-dialog.png)
 
     > [!NOTE]
     > 버전 ID는 문자, 숫자 또는 ‘.’으로만 구성할 수 있으며 10자 이하여야 합니다.
 
 지정한 이름으로 새 버전이 생성되고 활성 버전으로 설정됩니다.

## <a name="set-active-version"></a>활성 버전 설정

목록에서 버전을 선택한 다음, 도구 모음에서 **활성화**를 선택합니다. 

[![](./media/luis-how-to-manage-versions/versions-other.png "섹션, 버전 페이지 관리")](./media/luis-how-to-manage-versions/versions-other.png#lightbox)

## <a name="import-version"></a>버전 가져오기

1. 도구 모음에서 **버전 가져오기**를 선택합니다. 

2. **새 버전 가져오기** 팝업 창에서 10자의 새 버전 이름을 입력합니다. JSON 파일에 있는 버전이 이미 앱에 있는 경우 버전 ID를 설정하기만 하면 됩니다.

    ![섹션, 버전 페이지 관리](./media/luis-how-to-manage-versions/versions-import-pop-up.png)

    버전을 가져오면 새 버전이 활성 버전이 됩니다.

<a name = "export-version"></a>

## <a name="other-actions"></a>다른 작업

* 버전을 **삭제**하려면 목록에서 버전을 선택한 다음, 도구 모음에서 **삭제**를 선택합니다. **확인**을 선택합니다. 
* 버전의 **이름을 바꾸려면** 목록에서 버전을 선택한 다음, 도구 모음에서 **이름 바꾸기**를 선택합니다. 새 이름을 입력하고 **완료**를 선택합니다. 
* 버전을 **내보내려면** 목록에서 버전을 선택한 다음, 도구 모음에서 **앱 내보내기**를 선택합니다. 파일은 로컬 머신에 다운로드됩니다. 

