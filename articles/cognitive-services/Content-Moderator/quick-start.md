---
title: '빠른 시작: 웹에서 Content Moderator 사용해 보기 - Content Moderator'
titlesuffix: Azure Cognitive Services
description: 이 빠른 시작에서는 코드를 작성할 필요 없이 온라인 Content Moderator 검토 도구를 사용하여 Content Moderator의 기본 기능을 테스트합니다.
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 52c69aee4a8fdf8ad08590113c95ff3de3b0883b
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55205800"
---
# <a name="quickstart-try-content-moderator-on-the-web"></a>빠른 시작: 웹에서 Content Moderator 사용해 보기

이 빠른 시작에서는 코드를 작성할 필요 없이 온라인 Content Moderator 검토 도구를 사용하여 Content Moderator의 기본 기능을 테스트합니다. 이 서비스를 앱에 보다 신속하게 통합하려면 [다음 단계](#next-steps) 섹션에서 다른 빠른 시작을 참조하세요.

## <a name="prerequisites"></a>필수 조건

- 웹 브라우저

## <a name="set-up-the-review-tool"></a>검토 도구 설정
Content Moderator 검토 도구는 사람 검토자가 의사를 결정할 때 인식 서비스를 지원할 수 있는 웹 기반 도구입니다. 이 가이드에서는 검토 도구를 설정하는 간단한 프로세스를 통해 Content Moderator 서비스의 작동 원리를 알아보겠습니다. [Content Moderator 검토 도구](https://contentmoderator.cognitive.microsoft.com/) 사이트로 이동하여 가입합니다.

![Content Moderator 홈페이지](images/homepage.PNG)

## <a name="create-a-review-team"></a>검토 팀 만들기

다음으로, 검토 팀을 만듭니다. 작업 시나리오에서는 서비스의 조정 결정을 수동으로 검토할 사람들의 그룹이 됩니다. 지금은 팀 이름만 만들면 됩니다. 동료를 팀에 초대하려면 여기에 이메일 주소를 입력하면 됩니다.

![팀 구성원 초대](images/QuickStart-2-small.png)

## <a name="upload-sample-content"></a>샘플 콘텐츠 업로드

이제 샘플 콘텐츠를 업로드할 준비가 완료되었습니다. **시도 > 이미지**, **시도 > 텍스트** 또는 **시도 > 비디오**를 선택합니다.

![이미지 또는 텍스트 조정 시도](images/tryimagesortext.png)

조정할 콘텐츠를 제출합니다. 내부적으로, 검토 도구는 조정 API를 호출하여 콘텐츠를 검사합니다. 검사가 완료되면 검토 대기 중인 결과가 있음을 알리는 메시지가 표시됩니다.

![파일 조정](images/submitted.png)

## <a name="review-moderation-tags"></a>조정 태그 검토

적용된 조정 태그를 검토합니다. 콘텐츠에 적용된 태그와 각 범주의 점수를 볼 수 있습니다. [이미지](image-moderation-api.md), [텍스트](text-moderation-api.md) 및 [비디오](video-moderation-api.md) 조정 토픽을 참조하여 다른 콘텐츠 태그가 무엇을 나타내는지 자세히 알아보세요.

![결과 검토](images/reviewresults_text.png)

검토 팀은 프로젝트에서 이러한 태그를 필요한 대로 변경하거나 추가할 수 있습니다. **다음** 단추를 사용하여 변경 내용을 제출합니다. 비즈니스 애플리케이션이 Moderator API를 호출하면 태그가 지정된 콘텐츠가 큐에 추가되고, 사람 검토 팀이 검토할 수 있게 됩니다. 이 방법을 사용하면 대량의 콘텐츠를 빠르게 검토할 수 있습니다.

여기서는 Content Moderator 검토 도구를 사용하여 Content Moderator 서비스로 할 수 있는 작업의 예제를 살펴보았습니다. 다음으로, 검토 도구에 대한 자세한 내용 및 검토 API를 사용하여 검토 도구를 소프트웨어 프로젝트에 통합하는 방법을 알아볼 수도 있고, 앱에서 조정 API 자체를 사용하는 방법을 알아보는 [다음 단계](#next-steps) 섹션으로 건너뛸 수도 있습니다.

## <a name="learn-more-about-the-review-tool"></a>검토 도구에 대한 자세한 정보

Content Moderator 검토 도구를 사용하는 방법에 대해 자세히 알아보려면 [검토 도구](Review-Tool-User-Guide/human-in-the-loop.md) 가이드를 살펴보고, 검토 도구 API를 통해 사람 검토 환경을 미세 조정하는 방법을 알아보세요.
- [작업 API](try-review-api-job.md)는 조정 API를 사용하여 콘텐츠를 검사하고 검토 도구에서 검토를 생성합니다. 
- [검토 API](try-review-api-review.md)는 먼저 콘텐츠를 검사하지 않고 사용자 조정자를 위해 이미지, 텍스트 또는 비디오 검토를 직접 만듭니다. 
- [워크플로 API](try-review-api-workflow.md)는 팀이 만드는 사용자 지정 워크플로에 대한 세부 정보를 만들고, 업데이트하고, 가져옵니다.

또는 다음 단계를 계속 진행하여 코드에서 Moderation API 사용을 시작하세요.

## <a name="next-steps"></a>다음 단계

앱에서 Moderation API 자체를 사용하는 방법을 알아봅니다.
- 이미지 조정을 구현합니다. [API 콘솔](try-image-api.md) 또는 [C# 빠른 시작](image-moderation-quickstart-dotnet.md)을 사용하여 이미지를 검사하고 태그, 신뢰도 점수, 기타 추출된 정보를 사용하여 성인/외설 콘텐츠를 검색합니다.
- 텍스트 조정을 구현합니다. [API 콘솔](try-text-api.md) 또는 [C# 빠른 시작](text-moderation-quickstart-dotnet.md)을 사용하여 텍스트 콘텐츠에 욕설, 원치 않는 기계 지원 텍스트 분류(미리 보기) 및 PII(개인 식별 정보)가 있는지 검사합니다. 
- 비디오 조정을 구현합니다. [C#의 비디오 조정 방법 가이드](video-moderation-api.md)에 따라 비디오를 검사하고 성인/외설 콘텐츠를 검색합니다. 
