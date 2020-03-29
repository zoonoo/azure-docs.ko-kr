---
title: 검토 도구를 통해 콘텐츠 검토 사용 - 콘텐츠 중재자
titleSuffix: Azure Cognitive Services
description: 웹 포털에서 검토 도구를 통해 중재자가 이미지를 검토하도록 하는 방법을 알아봅니다.
services: cognitive-services
author: PatrickFarley
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/15/2019
ms.author: pafarley
ms.openlocfilehash: b02324923e3f004395105b8e04165390cb950fe7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "73044124"
---
# <a name="create-human-reviews"></a>인간 리뷰 만들기

이 가이드에서는 리뷰 도구 웹 사이트에서 [리뷰를](../review-api.md#reviews) 설정하는 방법을 알아봅니다. 검토는 인간 운영자가 평가할 콘텐츠를 저장하고 표시합니다. 중재자는 적용된 태그를 변경하고 자신의 사용자 지정 태그를 적절하게 적용할 수 있습니다. 사용자가 검토를 완료하면 결과가 지정된 콜백 끝점으로 전송되고 콘텐츠가 사이트에서 제거됩니다.

## <a name="prerequisites"></a>사전 요구 사항

- 콘텐츠 운영자 [검토 도구](https://contentmoderator.cognitive.microsoft.com/) 사이트에서 로그인하거나 계정을 만듭니다.

## <a name="image-reviews"></a>이미지 검토

1. [검토 도구로](https://contentmoderator.cognitive.microsoft.com/)이동하여 **시도** 탭을 선택하고 검토할 이미지를 업로드합니다.
1. 업로드된 이미지 처리가 완료되면 **검토** 탭으로 이동하여 **이미지를**선택합니다.

    ![검토 이미지 옵션이 강조 표시된 검토 도구를 보여주는 Chrome 브라우저](images/review-images-1.png)

    이미지는 자동 조정 프로세스에 의해 할당된 모든 레이블과 함께 표시됩니다. 검토 도구를 통해 제출한 이미지는 다른 검토자에게 표시되지 않습니다.

1. 선택적으로 **리뷰를 이동하여** 슬라이더(1)를 표시하여 화면에 표시되는 이미지 수를 조정합니다. **태그가 지정되거나** **태그가 지정되지 않은** 단추(2)를 클릭하여 그에 따라 이미지를 정렬합니다. 태그 패널(3)을 클릭하여 켜거나 끌 수 있습니다.

    ![검토에 대한 태그가 지정된 이미지를 사용하는 검토 도구를 보여주는 Chrome 브라우저](images/review-images-2.png)

1. 이미지에 대한 자세한 내용을 보려면 축소판 그림의 타원을 클릭하고 **세부 정보 보기를**선택합니다. **이동** 옵션을 사용하여 하위 팀에 이미지를 할당할 수 있습니다(하위 팀에 대해 자세히 알아보려면 [팀](./configure.md#manage-team-and-subteams) 섹션 참조).

    ![보기 세부 정보 옵션이 강조 표시된 이미지](images/review-images-3.png)

1. 세부 정보 페이지에서 이미지 조정 정보를 찾아봅니다.

    ![별도 창에서 나열된 조정 세부 정보를 사용하는 이미지](images/review-images-4.png)

1. 태그 할당을 검토하여 필요에 따라 업데이트한 후에는 **다음**을 클릭하여 검토를 제출합니다. 제출 후에는 약 5초 이내에 **이전** 단추를 클릭하여 이전 화면으로 돌아가 이미지를 다시 검토합니다. 그 후에는 이미지가 제출 큐에 더 이상 없으며 **이전** 단추를 더 이상 사용할 수 없습니다.

## <a name="text-reviews"></a>텍스트 검토

텍스트 검토는 이미지 리뷰와 유사하게 작동합니다. 콘텐츠를 업로드하는 대신 텍스트로 작성하거나 붙여넣기만 하면 됩니다(최대 1,024자). 그런 다음 콘텐츠 중재자는 텍스트를 분석하고 태그를 적용합니다(욕설 및 개인 데이터와 같은 기타 중재 정보 이외에). 텍스트 검토에서 검토를 제출하기 전에 적용된 태그를 전환하거나 사용자 지정 태그를 적용할 수 있습니다.

![Chrome 브라우저 창에서 플래그가 지정된 텍스트를 보여주는 검토 도구의 스크린샷](../images/reviewresults_text.png)

## <a name="next-steps"></a>다음 단계

이 가이드에서는 콘텐츠 중재자 [검토 도구에서](https://contentmoderator.cognitive.microsoft.com)리뷰를 설정하고 사용하는 방법을 배웠습니다. 다음으로 REST [API 가이드](../try-review-api-review.md) 또는 [.NET SDK 퀵스타트를](../dotnet-sdk-quickstart.md) 참조하여 프로그래밍 방식으로 리뷰를 만드는 방법을 알아봅니다.