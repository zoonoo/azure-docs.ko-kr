---
title: 도구 개념 검토 - 콘텐츠 중재자 알아보기
titleSuffix: Azure Cognitive Services
description: AI와 인적 검토 검토를 결합한 조정을 조정하는 웹 사이트인 콘텐츠 중재자 검토 도구에 대해 알아봅니다.
services: cognitive-services
author: PatrickFarley
manager: mikemcca
ms.date: 03/15/2019
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.author: pafarley
ms.openlocfilehash: a23e6d46ee6e79fd7a5cabf4434c561f7d83b31b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "76169513"
---
# <a name="content-moderator-review-tool"></a>콘텐츠 진행자 검토 도구

Azure 콘텐츠 중재자는 기계 학습 콘텐츠 중재를 사용자 리뷰와 결합하는 서비스를 제공하며 [검토 도구](https://contentmoderator.cognitive.microsoft.com) 웹 사이트는 이러한 서비스에 대한 자세한 액세스를 제공하는 사용자 친화적인 프런트 엔드입니다.

![브라우저의 검토 도구 대시보드](./images/0-dashboard.png)

## <a name="what-it-does"></a>수행하는 작업

[검토 도구는](https://contentmoderator.cognitive.microsoft.com)기계 지원 중재 API와 함께 사용할 때 콘텐츠 조정 프로세스에서 다음 작업을 수행할 수 있습니다.

- 한 도구 세트를 사용하여 여러 형식(텍스트, 이미지 및 비디오)으로 콘텐츠를 조정합니다.
- 중재 API 결과가 나올 때 휴먼 [리뷰 작성을 자동화합니다.](../review-api.md#reviews)
- 콘텐츠 카테고리 또는 환경 수준별로 구성된 여러 검토 팀에 콘텐츠 검토를 할당하거나 에스컬레이션합니다.
- 기본 또는 사용자 지정 논리[필터(워크플로)를](../review-api.md#workflows)사용하여 코드를 작성하지 않고 콘텐츠를 정렬하고 추적합니다.
- [커넥터를](./configure.md#connectors) 사용하여 콘텐츠 중재자 API 외에 Microsoft PhotoDNA, 텍스트 분석 및 얼굴 서비스를 사용하여 콘텐츠를 처리합니다.
- 자체 커넥터를 구축하여 모든 API 또는 비즈니스 프로세스에 대한 워크플로를 만듭니다.
- 콘텐츠 수정 프로세스에 대한 주요 성능 메트릭을 가져옵니다.

## <a name="review-tool-dashboard"></a>도구 대시보드 검토

**대시보드** 탭에서 도구 내에서 수행된 콘텐츠 검토에 대한 주요 메트릭을 볼 수 있습니다. 이미지, 텍스트 및 비디오 콘텐츠에 대한 총 리뷰, 완료 및 보류 중인 리뷰 수를 확인합니다. 또한 검토를 완료한 사용자 및 팀의 분석 결과와 적용된 중재 태그를 확인할 수도 있습니다.

![대시보드 보기](images/0-dashboard.png)

## <a name="review-tool-credentials"></a>도구 자격 증명 검토

[검토 도구로](https://contentmoderator.cognitive.microsoft.com)등록하면 계정에 대한 Azure 지역을 선택하라는 메시지가 표시됩니다. 이는 검토 [도구가](https://contentmoderator.cognitive.microsoft.com) Azure 콘텐츠 중재자 서비스에 대한 무료 평가판 키를 생성하기 때문입니다. REST 호출 또는 클라이언트 SDK의 서비스에 액세스하려면 이 키가 필요합니다. **설정** > **자격 증명을**선택하여 키 및 API 끝점 URL을 볼 수 있습니다.

![Content Moderator 자격 증명](images/settings-6-credentials.png)

## <a name="next-steps"></a>다음 단계

[검토 도구 리소스에](./configure.md) 액세스하고 설정을 변경하는 방법을 알아보려면 검토 구성 도구를 참조하십시오.