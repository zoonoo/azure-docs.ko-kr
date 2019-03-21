---
title: Azure Content Moderator에서 자격 증명 관리 - Content Moderator
titlesuffix: Azure Cognitive Services
description: API를 사용하려면 필요한 Content Moderator 자격 증명을 관리하는 방법을 알아봅니다.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: article
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 0da6b6b0fef0f998e20789253b2a65c54121532c
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2019
ms.locfileid: "58260012"
---
# <a name="manage-content-moderator-service-credentials"></a>Content Moderator 서비스 자격 증명 관리

Content Moderator 자격 증명은 다음 위치에 만들어집니다.

- [Azure 포털](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator)
- [Content Moderator 검토 도구](https://contentmoderator.cognitive.microsoft.com/)

이 문서에서는 자격 증명을 찾는 위치 및 상관 관계를 설명합니다.

## <a name="the-azure-portal"></a>Azure 포털

Azure Portal 대시보드에서 Content Moderator 계정을 선택합니다. **리소스 관리** 아래에서 **키**를 선택합니다. 키를 복사하려면 키 오른쪽에 있는 아이콘을 선택합니다.

![Azure Portal의 Content Moderator 키](images/credentials-azure-portal-keys.PNG)

### <a name="use-the-azure-account-with-the-review-tool-and-review-api"></a>검토 도구 및 검토 API와 함께 Azure 계정 사용
review API에 Azure 키를 사용하려면 다음 스크린샷의 **속성** 화면에 나열된 리소스 ID를 복사한 후, 다음 **리소스 ID** 섹션에 표시된 것처럼 **허용되는 리소스 ID** 필드의 검토 도구 자격 증명 화면에 입력합니다. 

> [!NOTE]
> Content Moderator 구독의 지역이 검토 팀의 지역과 일치해야만 팀을 인식하고 팀 데이터에 액세스할 수 있습니다. 예를 들어 이 페이지의 이미지에서 **미국 서부** 지역 **(4)** 에는 Content Moderator Azure 구독 및 검토 팀이 포함됩니다.
>
> 검토 도구의 두 장소를 Azure 구독의 키와 리소스 ID로 바꾼 후에는 [자격 증명] 화면에 표시된 **평가판 Ocp-Apim 구독-키**가 더 이상 사용되지 않지만, 항상 제공되기는 합니다.
> 평가판 키는 1RPS(초당 요청 수)에서 매월 최대 5,000개 트랜잭션으로 제한됩니다.

![Azure Portal의 Content Moderator 리소스 ID](images/credentials-azure-portal-resourceid.PNG)

### <a name="use-the-azure-account-with-the-workflows-in-the-review-tool"></a>검토 도구의 워크플로와 함께 Azure 계정 사용

Content Moderator 내에서 제공되는 워크플로에 Azure 키를 사용하려면 다음 **워크플로** 섹션에 표시된 것처럼 **워크플로 설정** 섹션의 **Ocp-Apim-Subscription-Key** 필드에 키를 입력합니다. **'+'** 기호를 눌러 리소스 ID를 저장합니다.

![검토 도구의 Content Moderator 워크플로 자격 증명](images/credentials-workflow.PNG)

## <a name="the-review-tool"></a>검토 도구

검토 도구 대시보드의 **설정** 탭에서 **자격 증명**을 선택합니다.

![검토 도구의 Content Moderator 자격 증명](images/credentials-trial-resource-workflow.PNG)

다음 섹션에서는 이전 이미지를 자세히 살펴봅니다.

### <a name="api"></a>API

첫 번째 부분에는 검토 팀 생성의 일부로 **review API 엔드포인트**, **팀 ID** 및 **Ocp-Apim-Subscription-Key(Content Moderator 평가판 키)** 가 나열됩니다. 이러한 것들을 사용하여 review API를 포함한 모든 Content Moderator API를 호출하세요.

또한 API 엔드포인트의 지역 식별자를 살펴보세요. 예를 들어 **westus** 은 지역에 "https:\//westus.api.cognitive.microsoft.com/contentmoderator/review/v1.0"

![검토 도구의 Content Moderator 키](images/credentials-trialkey.PNG)

### <a name="resource-id"></a>리소스 ID

이 필드 집합은 이전 섹션 [검토 도구 및 API와 함께 Azure 계정 사용](credentials.md#use-the-azure-account-with-the-review-tool-and-review-api)에서 다루었습니다. 이 필드는 이전 섹션에서 설명한 대로 Azure Resource ID를 이 필드에 추가하지 않는 한 대개 비어 있습니다.

### <a name="workflows"></a>워크플로

이 필드 집합은 이전 섹션 [검토 도구의 워크플로와 함께 Azure 계정 사용](credentials.md#use-the-azure-account-with-the-workflows-in-the-review-tool)에서 다루었습니다. 기본적으로 검토 도구는 워크플로우를 실행하기 위해 자동 생성된 평가판 키를 사용하며 이 키는 처음에 표시됩니다. 다른 두 필드에는 각각 [화면 텍스트] 및 [이미지 평가 작업]에 나열된 용어와 이미지를 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [워크플로](workflows.md)에서 Content Moderator 자격 증명을 사용하는 방법을 알아봅니다.
