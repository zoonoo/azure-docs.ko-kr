---
title: 버전 관리
titleSuffix: Language Understanding - Azure Cognitive Services
description: 버전을 통해 다양한 모델을 빌드하고 게시할 수 있습니다. 모델에 변경 사항을 적용하기 전에 현재 활성 모델을 앱의 다른 버전에 을 복제하는 것이 좋습니다.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 01/23/2019
ms.author: diberry
ms.openlocfilehash: ac12ec34ce9799163dc815af3bc602a2e63923d8
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55210165"
---
# <a name="use-versions-to-edit-and-test-without-impacting-staging-or-production-apps"></a>스테이징 또는 프로덕션 앱에 영향을 주지 않고 버전을 사용하여 편집 및 테스트

버전을 통해 다양한 모델을 빌드하고 게시할 수 있습니다. 모델에 변경 사항을 적용하기 전에 현재 활성 모델을 앱의 다른 [버전](luis-concept-version.md)에 복제하는 것이 좋습니다. 

버전을 사용하려면 **내 앱** 페이지에서 해당 이름을 선택하여 앱을 연 다음, 위쪽 표시줄에서 **관리**를 선택하고 왼쪽 탐색에서 **버전**을 선택합니다. 

버전 목록은 게시된 버전, 게시 위치 및 현재 활성 버전을 보여 줍니다. 

[![관리 섹션, 버전 페이지](./media/luis-how-to-manage-versions/versions-import.png "관리 섹션, 버전 페이지")](./media/luis-how-to-manage-versions/versions-import.png#lightbox)

## <a name="clone-a-version"></a>버전 복제

1. 복제할 버전을 선택한 다음, 도구 모음에서 **복제**를 선택합니다. 

2. **복제 버전** 대화 상자에서 “0.2”와 같은 새 버전에 사용할 이름을 입력합니다.

   ![버전 복제 대화 상자](./media/luis-how-to-manage-versions/version-clone-version-dialog.png)
 
     > [!NOTE]
     > 버전 ID는 문자, 숫자 또는 ‘.’으로만 구성할 수 있으며 10자 이하여야 합니다.
 
 지정한 이름으로 새 버전이 생성되고 활성 버전으로 설정됩니다.

## <a name="set-active-version"></a>활성 버전 설정

목록에서 버전을 선택한 다음, 도구 모음에서 **활성화**를 선택합니다. 

[![관리 섹션, 버전 페이지, 버전 작업 수행](./media/luis-how-to-manage-versions/versions-other.png "관리 섹션, 버전 페이지, 버전 작업 수행")](./media/luis-how-to-manage-versions/versions-other.png#lightbox)

## <a name="import-version"></a>버전 가져오기

1. 도구 모음에서 **버전 가져오기**를 선택합니다. 

2. **새 버전 가져오기** 팝업 창에서 10자의 새 버전 이름을 입력합니다. JSON 파일에 있는 버전이 이미 앱에 있는 경우 버전 ID를 설정하기만 하면 됩니다.

    ![관리 섹션, 버전 페이지, 새 버전 가져오기](./media/luis-how-to-manage-versions/versions-import-pop-up.png)

    버전을 가져오면 새 버전이 활성 버전이 됩니다.

<a name = "export-version"></a>

## <a name="other-actions"></a>다른 작업

* 버전을 **삭제**하려면 목록에서 버전을 선택한 다음, 도구 모음에서 **삭제**를 선택합니다. **확인**을 선택합니다. 
* 버전의 **이름을 바꾸려면** 목록에서 버전을 선택한 다음, 도구 모음에서 **이름 바꾸기**를 선택합니다. 새 이름을 입력하고 **완료**를 선택합니다. 
* 버전을 **내보내려면** 목록에서 버전을 선택한 다음, 도구 모음에서 **앱 내보내기**를 선택합니다. 파일은 로컬 머신에 다운로드됩니다. 

