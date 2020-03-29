---
title: 검토 도구 - 콘텐츠 중재자를 통해 콘텐츠 워크플로 정의 및 사용
titleSuffix: Azure Cognitive Services
description: Azure 콘텐츠 중재자 워크플로 디자이너를 사용하여 콘텐츠 정책에 따라 사용자 지정 워크플로 및 임계값을 정의할 수 있습니다.
services: cognitive-services
author: PatrickFarley
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: pafarley
ms.openlocfilehash: 0be77dc1ac249c37f9b59078451d8fbe35f17458
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "72754247"
---
# <a name="define-and-use-moderation-workflows"></a>중재 워크플로정의 및 사용

이 가이드에서는 [검토 도구](https://contentmoderator.cognitive.microsoft.com) 웹 사이트에서 [워크플로를](../review-api.md#workflows) 설정하고 사용하는 방법을 알아봅니다. 워크플로는 콘텐츠를 보다 효율적으로 처리하는 데 사용할 수 있는 클라우드 기반 사용자 지정 필터입니다. 워크플로는 다양한 서비스에 연결하여 다양한 방식으로 콘텐츠를 필터링한 다음 적절한 조치를 취할 수 있습니다. 이 가이드에서는 콘텐츠 중재자 커넥터(기본적으로 포함)를 사용하여 콘텐츠를 필터링하고 일반적인 조정 시나리오에서 휴먼 리뷰를 설정하는 방법을 보여 주며 이 가이드를 참조합니다.

## <a name="create-a-new-workflow"></a>새 워크플로 만들기

콘텐츠 운영자 검토 도구로 이동하여 [로그인합니다.](https://contentmoderator.cognitive.microsoft.com/) **설정** 탭에서 **워크플로**를 선택합니다.

![워크플로 설정](images/2-workflows-0.png)

다음 화면에서 **워크플로 추가를**선택합니다.

![워크플로 추가](images/2-workflows-1.png)

### <a name="assign-a-name-and-description"></a>이름 및 설명 할당

워크플로의 이름을 지정하고 설명을 입력하고 워크플로가 이미지 또는 텍스트를 처리할지 여부를 선택합니다.

![워크플로 이름 및 설명](images/image-workflow-create.PNG)

### <a name="define-evaluation-criteria"></a>평가 기준 정의

다음 화면에서 **If** 섹션으로 이동합니다. 상단 드롭다운 메뉴에서 **조건을**선택합니다. 이렇게 하면 워크플로가 작업을 수행할 조건을 구성할 수 있습니다. 여러 조건을 사용하려면 대신 **조합을** 선택합니다. 

다음으로 커넥터를 선택합니다. 이 예제에서는 **Content Moderator**를 사용합니다. 선택한 커넥터에 따라 데이터 출력에 대해 다양한 옵션을 선택할 수 있습니다. 다른 [커넥터를](./configure.md#connectors) 설정하는 방법에 대해 알아보려면 검토 도구 설정 가이드의 커넥터 섹션을 참조하십시오.

![워크플로우 커넥터 선택](images/image-workflow-connect-to.PNG)

사용할 원하는 출력을 선택하고 조건을 설정하여 확인합니다.

![워크플로 조건 정의](images/image-workflow-condition.PNG)

### <a name="define-the-action"></a>작업 정의

**다음** 섹션으로 이동하여 작업을 선택합니다. 다음 예제는 이미지 검토를 만들고 태그를 할당합니다. 선택적으로 대체(Else) 경로를 추가하고 그에 대한 작업도 설정할 수 있습니다.

![워크플로 작업 정의](images/image-workflow-action.PNG)

### <a name="save-the-workflow"></a>워크플로 저장

워크플로 이름을 적어 둡니다. 워크플로 API를 사용하여 중재 작업을 시작하려면 이름이 필요합니다(아래 참조). 마지막으로 페이지 상단에 있는 **저장** 단추를 사용하여 워크플로를 저장합니다.

## <a name="test-the-workflow"></a>워크플로 테스트

사용자 지정 워크플로를 정의한 후 샘플 콘텐츠로 테스트합니다. 워크플로로 이동하여 해당 **워크플로 실행** **단추를 선택합니다.**

![워크플로 테스트](images/image-workflow-execute.PNG)

이 [샘플 이미지를](https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg) 로컬 드라이브에 저장합니다. 그런 다음 **파일 선택을** 선택하고 워크플로에 이미지를 업로드합니다.

![이미지에 따옴표가 겹쳐진 러너](images/sample-text.jpg)

### <a name="track-progress"></a>진행률 추적

다음 팝업 창에서 워크플로진행 상황을 볼 수 있습니다.

![워크플로 실행 추적](images/image-workflow-job.PNG)

### <a name="verify-workflow-action"></a>워크플로 작업 확인

**검토** 아래의 **이미지** 탭으로 이동하여 새로 만든 이미지 검토가 있는지 확인합니다.

![이미지 검토](images/image-workflow-review.PNG)

## <a name="next-steps"></a>다음 단계

이 가이드에서는 콘텐츠 중재자 [검토 도구에서](https://contentmoderator.cognitive.microsoft.com)중재 워크플로를 설정하고 사용하는 방법을 배웠습니다. 다음으로 REST [API 가이드를](../try-review-api-workflow.md) 참조하여 프로그래밍 방식으로 워크플로를 만드는 방법을 알아봅니다.
