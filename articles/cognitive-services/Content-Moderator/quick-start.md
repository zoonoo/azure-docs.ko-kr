---
title: Azure Content Moderator 시작 | Microsoft Docs
description: Azure Content Moderator를 시작하는 방법입니다.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 01/15/2018
ms.author: sajagtap
ms.openlocfilehash: ae4333047ebd95733c7baaed0323a0c2c477d323
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35374318"
---
# <a name="get-started-with-content-moderator"></a>Content Moderator 시작

다음과 같은 방법으로 Content Moderator API와 검토 도구를 시작합니다.

- [검토 도구로 시작](#start-with-the-review-tool)하여 API 키와 검토 팀을 둘 다 만듭니다. 검토 도구를 탐색하고 Content Moderator API를 사용하여 통합하는 방법을 알아봅니다.
- Azure Portal에서 [Content Moderator를 구독](#start-with-the-apis)합니다. 검토 팀을 만들려면 온라인에서 등록해야 합니다.
- [Flow 커넥터 및 템플릿을 사용](https://flow.microsoft.com/connectors/shared_cognitiveservicescontentmoderator/content-moderator/)하여 사용하기 쉬운 디자이너와의 광범위한 통합을 확인합니다.

선택한 옵션에 관계없이 API 자격 증명을 찾으려면 [자격 증명 관리](review-tool-user-guide/credentials.md) 문서를 참조하세요.

## <a name="start-with-the-review-tool"></a>검토 도구로 시작
Content Moderator 검토 도구 웹 사이트에서 [등록](http://contentmoderator.cognitive.microsoft.com/)합니다.

![Content Moderator 홈페이지](images/homepage.PNG)

### <a name="create-a-review-team"></a>검토 팀 만들기
팀에 이름을 지정합니다. 동료를 초대하려면 해당 메일 주소를 입력하면 됩니다.

![팀 구성원 초대](images/QuickStart-2-small.png)

### <a name="upload-images-or-enter-text"></a>이미지 업로드 또는 텍스트 입력
**시도 > 이미지** 또는 **시도 > 텍스트**를 클릭합니다. 최대 5개의 샘플 이미지를 업로드하거나, 조정할 샘플 텍스트를 입력합니다.

![이미지 또는 텍스트 조정 시도](images/tryimagesortext.png)

### <a name="submit-for-automated-moderation"></a>자동화된 조정을 위해 제출
자동화된 조정을 위해 콘텐츠를 제출합니다. 내부적으로, 검토 도구는 조정 API를 호출하여 콘텐츠를 검사합니다. 검사가 완료되면 검토 대기 중인 결과를 알리는 메시지가 표시됩니다.

![파일 조정](images/submitted.png)

### <a name="review-and-confirm-results"></a>결과 검토 및 확인
자동 조정된 태그를 검토하고 필요한 경우 변경한 후 **다음** 단추를 사용하여 제출합니다. 비즈니스 응용 프로그램이 Moderator API를 호출하면 태그가 지정된 콘텐츠가 큐에 대기되어 검토 팀이 검토할 수 있게 됩니다. 이 접근 방식을 사용하면 대량 콘텐츠를 빠르게 검토할 수 있습니다.

![결과 검토](images/reviewresults.png)

모든 [검토 도구 기능](Review-Tool-User-Guide/human-in-the-loop.md)을 사용하는 방법을 알아보거나, 다음 섹션을 계속 진행하여 API에 대해 알아봅니다. [자격 증명 관리](review-tool-user-guide/credentials.md) 문서에 표시된 대로, 검토 도구에 API 키가 프로비전되어 있으므로 등록 단계는 건너뜁니다.

### <a name="use-the-apis"></a>API 사용

이제 콘텐츠 조정 및 검토 도구 환경을 살펴보았으므로, Content Moderator를 비즈니스 응용 프로그램과 통합하는 방법을 알아봅니다. 다음 섹션을 사용하여 SDK 및 샘플을 통해 자세히 알아보고 빠르게 파악합니다.

## <a name="start-with-the-apis"></a>API로 시작

Azure Portal에서 [Content Moderator를 구독](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator)합니다. 다음 API 중 하나로 시작합니다.

### <a name="image-moderation"></a>이미지 조정

[API 콘솔](try-image-api.md)로 시작하거나, [.NET 빠른 시작](image-moderation-quickstart-dotnet.md)을 사용하여 이미지를 검사하고 태그, 신뢰도 점수, 기타 추출된 정보를 사용하여 성인/외설 콘텐츠를 검색합니다.

### <a name="text-moderation"></a>텍스트 조정

[API 콘솔](try-text-api.md)로 시작하거나, [.NET 빠른 시작](text-moderation-quickstart-dotnet.md)을 사용하여 텍스트 콘텐츠에 욕설, 원치 않는 기계 지원 텍스트 분류(미리 보기) 및 PII(개인 식별 정보)가 있는지 검사합니다. 


### <a name="video-moderation"></a>비디오 조정

[.NET 빠른 시작](video-moderation-api.md)으로 시작하여 비디오를 검사하고 성인/외설 콘텐츠를 검색합니다. 


### <a name="review-apis"></a>API 검토

여기서 작업, 검토, 워크플로 API 중 하나를 선택하여 시작합니다.

- [작업 API](try-review-api-job.md)는 조정 API를 사용하여 콘텐츠를 검사하고 검토 도구에서 검토를 생성합니다. 
- [검토 API](try-review-api-review.md)는 먼저 콘텐츠를 검사하지 않고 사용자 조정자를 위해 이미지, 텍스트 또는 비디오 검토를 직접 만듭니다. 
- [워크플로 API](try-review-api-workflow.md)는 팀이 만드는 사용자 지정 워크플로에 대한 세부 정보를 만들고, 업데이트하고, 가져옵니다.

## <a name="next-steps"></a>다음 단계

[이미지 조정 API](image-moderation-api.md)로 시작하는 콘텐츠 조정에 대해 자세히 알아봅니다.
