---
title: Content Moderator 검토 도구 개념-알아보기
titlesuffix: Azure Cognitive Services
description: Content Moderator 검토 도구를 결합 된 AI 및 사용자 검토 중재 활동을 조정 하는 웹 사이트에 알아봅니다.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.date: 03/15/2019
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.author: sajagtap
ms.openlocfilehash: b7ec997fd3e9bfe294050893d80fd57a96a47aae
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "61270007"
---
# <a name="content-moderator-review-tool"></a>Content Moderator 검토 도구

Azure Content Moderator는 사용자 검토를 사용 하 여 machine learning 콘텐츠 조정을 결합 하는 서비스를 제공 하며 [검토 도구](https://contentmoderator.cognitive.microsoft.com) 웹 사이트는 이러한 서비스에 세부적인된 액세스를 제공 하는 프런트 엔드를 사용자에 게 친숙 합니다.

![브라우저에서의 검토 도구 대시보드](./images/0-dashboard.png)

## <a name="what-it-does"></a>기능

합니다 [검토 도구](https://contentmoderator.cognitive.microsoft.com)콘텐츠 조정 프로세스의 다음 작업을 수행할 수 있습니다 기계 지원 조정 Api와 함께 사용 하는 경우:

- 일련의 도구를 사용 하 여 여러 형식 (텍스트, 이미지 및 비디오) 콘텐츠를 조정 합니다.
- 사용자 생성을 자동화할 [검토](../review-api.md#reviews) 조정 API 제공을 생성 하는 경우에 합니다.
- 콘텐츠 범주 또는 경험 수준에 따라 구성 하는 여러 검토 팀, 콘텐츠 검토 에스컬레이션 하거나 할당 됩니다.
- 기본 또는 사용자 지정 논리 필터를 사용 하 여 ([워크플로](../review-api.md#workflows))를 정렬 및 코드를 작성 하지 않고 콘텐츠를 추적 합니다.
- 사용 하 여 [커넥터](./configure.md#connectors) Microsoft PhotoDNA, 텍스트 분석 및 콘텐츠 Moderator Api 외에도 Face Api를 사용 하 여 콘텐츠를 처리 합니다.
- 모든 API에 대 한 워크플로 만드는 데 사용자 고유의 커넥터를 빌드 또는 비즈니스 프로세스입니다.
- 콘텐츠 수정 프로세스에 대한 주요 성능 메트릭을 가져옵니다.

## <a name="review-tool-dashboard"></a>검토 도구 대시보드

에 **대시보드** 탭, 도구 내에서 수행 하는 콘텐츠 검토에 대 한 주요 메트릭을 볼 수 있습니다. 총, 완료 및 이미지, 텍스트 및 비디오 콘텐츠 검토 보류 중인 번호를 참조 하십시오. 사용자 및 적용 된 조정 태그 뿐만 아니라 검토를 완료 하는 팀의 분석을 볼 수도 있습니다.

![대시보드 보기](images/0-dashboard.png)

## <a name="review-tool-credentials"></a>검토 도구 자격 증명

등록 하면와 합니다 [검토 도구](https://contentmoderator.cognitive.microsoft.com), 계정에 대 한 Azure 지역을 선택 하 라는 메시지가 나타납니다. 때문에 이것이 합니다 [검토 도구](https://contentmoderator.cognitive.microsoft.com) Azure Content Moderator 서비스에 대 한 무료 평가판 키 생성 REST 호출 또는 SDK 클라이언트에서 서비스에 액세스 하려면이 키가 필요 합니다. 선택 하 여 키 및 API 끝점 URL을 볼 수 있습니다 **설정을** > **자격 증명**합니다.

![Content Moderator 자격 증명](images/settings-6-credentials.png)

## <a name="next-steps"></a>다음 단계

참조 [검토 도구 구성](./configure.md) 검토 도구 리소스에 액세스 하 고 설정을 변경 하는 방법을 알아보려면.