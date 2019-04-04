---
title: 정의 및-Content Moderator 검토 도구를 통해 콘텐츠 워크플로 사용 합니다.
titlesuffix: Azure Cognitive Services
description: 사용자 지정 워크플로 및 콘텐츠 정책에 따라 임계값을 정의 하 여 Azure Content Moderator 워크플로 디자이너를 사용할 수 있습니다.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: article
ms.date: 03/14/2019
ms.author: sajagtap
ms.openlocfilehash: 76990fb3b6ed1815ada724f28f8276bac1cf28d4
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/01/2019
ms.locfileid: "58757407"
---
# <a name="define-and-use-moderation-workflows"></a>정의 및 조정 워크플로 사용 합니다.

이 가이드에서는 설정 및 사용 하는 방법을 알아봅니다 [워크플로](../review-api.md#workflows) 에 [검토 도구](https://contentmoderator.cognitive.microsoft.com) 웹 사이트입니다. 워크플로 콘텐츠를 보다 효율적으로 처리 하는 데 사용할 수 있는 클라우드 기반 사용자 지정 된 필터입니다. 워크플로 다양 한 가지 방법으로 콘텐츠를 필터링 하 고 적절 한 조치를 취할를 서비스에 연결할 수 있습니다. 이 가이드에서는 콘텐츠를 필터링 하 고 일반적인 조정 시나리오에서 사용자 검토를 설정 하는 Content Moderator 커넥터 (기본적으로 포함 됨)를 사용 하는 방법을 보여 줍니다.

## <a name="create-a-new-workflow"></a>새 워크플로 만들기

로 이동 합니다 [Content Moderator 검토 도구](https://contentmoderator.cognitive.microsoft.com/) 에 로그인 합니다. **설정** 탭에서 **워크플로**를 선택합니다.

![워크플로 설정](images/2-workflows-0.png)

다음 화면에서 선택 **워크플로 추가**합니다.

![워크플로 추가](images/2-workflows-1.png)

### <a name="assign-a-name-and-description"></a>이름 및 설명 할당

워크플로 이름과 설명을 입력 한 다음 워크플로 이미지 또는 텍스트를 처리 하는지 여부를 선택 합니다.

![워크플로 이름 및 설명](images/image-workflow-create.PNG)

### <a name="define-evaluation-criteria"></a>평가 기준 정의

다음 화면으로 이동 합니다 **경우** 섹션입니다. 맨 위 드롭다운 메뉴에서 선택 **조건을**합니다. 이렇게 하면 워크플로 작업을 수행 하는 데는 조건을 구성할 수 있습니다. 여러 조건을 사용 하려는 경우 선택 **조합** 대신 합니다. 

다음으로 커넥터를 선택 합니다. 이 예제에서는 **Content Moderator**를 사용합니다. 선택 하면 커넥터에 따라 데이터 출력에 대 한 다른 옵션이 표시 됩니다. 참조 된 [커넥터](./configure.md#connectors) 다른 커넥터를 설정 하는 방법을 알아보려면 검토 도구 설정 가이드의 섹션입니다.

![워크플로 커넥터 선택](images/image-workflow-connect-to.PNG)

원하는 출력을 사용 하 여 및에 대해 확인할 조건을 설정 선택 합니다.

![워크플로 조건 정의](images/image-workflow-condition.PNG)

### <a name="define-the-action"></a>작업 정의

로 이동 합니다 **다음** 섹션에서는 작업을 선택 하는 위치입니다. 다음 예제에서는 이미지 검토를 만들고 태그를 할당 합니다. 필요에 따라 대체 (Else) 경로 추가 하 고도 대 한 작업을 설정할 수 있습니다.

![워크플로 작업 정의](images/image-workflow-action.PNG)

### <a name="save-the-workflow"></a>워크플로 저장 합니다.

워크플로 이름은; 워크플로 API (아래 참조)를 사용 하 여 조정 작업을 시작 하는 이름이 필요 합니다. 마지막으로 사용 하 여 워크플로 저장 합니다 **저장할** 페이지의 맨 위에 있는 단추입니다.

## <a name="test-the-workflow"></a>워크플로 테스트

사용자 지정 워크플로 정의한 했으므로 샘플 콘텐츠를 사용 하 여 테스트 합니다. 로 이동 **워크플로** 하 고 해당 선택 **워크플로 실행** 단추입니다.

![워크플로 테스트](images/image-workflow-execute.PNG)

이 저장할 [샘플 이미지](https://moderatorsampleimages.blob.core.windows.net/samples/sample3.png) 로컬 드라이브에 있습니다. 선택한 **파일 선택** 워크플로에 이미지를 업로드 합니다.

![bathing 세트에서 여자](images/sample-racy.PNG)

### <a name="track-progress"></a>진행률 추적

다음 팝업 창에서 워크플로의 진행률을 볼 수 있습니다.

![워크플로 실행 추적](images/image-workflow-job.PNG)

### <a name="verify-workflow-action"></a>워크플로 작업 확인

로 이동 합니다 **이미지** 탭에서 **검토** 새로 만든된 이미지 검토 인지 확인 합니다.

![이미지 검토](images/image-workflow-review.PNG)

## <a name="next-steps"></a>다음 단계

이 가이드에서는 설정 하 고 Content Moderator에서 조정 워크플로 사용 하는 방법을 배웠습니다 [검토 도구](https://contentmoderator.cognitive.microsoft.com)합니다. 다음으로, 참조를 [REST API 가이드](../try-review-api-workflow.md) 를 프로그래밍 방식으로 워크플로 만드는 방법을 알아봅니다.
