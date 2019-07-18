---
title: -Content Moderator 검토 도구를 통해 사용 하 여 콘텐츠 검토
titlesuffix: Azure Cognitive Services
description: 웹 포털에서 검토 도구를 통해 중재자가 이미지를 검토하도록 하는 방법을 알아봅니다.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: article
ms.date: 03/15/2019
ms.author: sajagtap
ms.openlocfilehash: a482ecf4a0d321525ab7e392695d2c4c0eebeadc
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60629044"
---
# <a name="create-human-reviews"></a>사용자 검토를 만들려면

이 가이드에서는 설정 하는 방법을 알아봅니다 [검토](../review-api.md#reviews) 검토 도구 웹 사이트입니다. 검토 저장 하 고 평가 하는 조정자에 대 한 콘텐츠를 표시 합니다. 중재자는 적용 된 태그를 변경 하 고 적절 하 게 자신의 사용자 지정 태그를 적용할 수 있습니다. 사용자 검토를 완료 되 면 결과 지정 된 콜백 끝점으로 전송 됩니다 하 고 콘텐츠는 사이트에서 제거 됩니다.

## <a name="prerequisites"></a>필수 조건

- 로그인 또는 Content Moderator에서 계정을 만듭니다 [검토 도구](https://contentmoderator.cognitive.microsoft.com/) 사이트입니다.

## <a name="image-reviews"></a>이미지 검토

1. 로 [검토 도구](https://contentmoderator.cognitive.microsoft.com/)를 선택 합니다 **시도** 탭을 검토 하려면 일부 이미지를 업로드 합니다.
1. 업로드 된 이미지 처리를 완료 한 후로 이동 합니다 **검토** 탭을 선택한 **이미지**합니다.

    ![검토 이미지 옵션이 강조 표시된 검토 도구를 보여주는 Chrome 브라우저](images/review-images-1.png)

    자동 조정 프로세스에 의해 할당 된 모든 레이블을 사용 하 여 이미지를 표시 합니다. 검토 도구를 통해 사용자가 제출한 이미지 다른 검토자에 게 표시 되지 않습니다.

1. 필요에 따라 이동 합니다 **표시할 검토** 화면에 표시 되는 이미지의 수를 조정 하려면 슬라이더 (1). 클릭 합니다 **태그가 지정 된** 또는 **태그가 지정 되지 않은** 이미지를 적절 하 게 정렬 단추 (2). 켜기 또는 끄기로 전환 하는 태그 패널 (3)를 클릭 합니다.

    ![검토에 대한 태그가 지정된 이미지를 사용하는 검토 도구를 보여주는 Chrome 브라우저](images/review-images-2.png)

1. 이미지에서 자세한 정보를 보려면 선택한 미리 보기에서 줄임표를 클릭 **세부 정보를 보려면**합니다. 사용 하 여 치중에 이미지를 지정할 수 있습니다는 **이동할** 옵션 (참조는 [팀](./configure.md#manage-team-and-subteams) 하위 팀에 자세히 알아보려면 섹션).

    ![보기 세부 정보 옵션이 강조 표시된 이미지](images/review-images-3.png)

1. 세부 정보 페이지에서 이미지 조정 정보를 찾아봅니다.

    ![별도 창에서 나열된 조정 세부 정보를 사용하는 이미지](images/review-images-4.png)

1. 태그 할당을 검토하여 필요에 따라 업데이트한 후에는 **다음**을 클릭하여 검토를 제출합니다. 제출 후에는 약 5초 이내에 **이전** 단추를 클릭하여 이전 화면으로 돌아가 이미지를 다시 검토합니다. 그 후에는 이미지가 제출 큐에 더 이상 없으며 **이전** 단추를 더 이상 사용할 수 없습니다.

## <a name="text-reviews"></a>텍스트 검토

텍스트 검토 이미지 검토 유사 하 게 작동 합니다. 콘텐츠를 업로드 하는 대신 간단히 쓰거나 텍스트 (최대 1,024 개의 문자)에 붙여 넣습니다. 그런 다음 Content Moderator는 텍스트를 분석 하 고 태그 (불경 한 언어 및 개인 데이터와 같은 기타 조정 정보) 외에도 적용 됩니다. 텍스트 검토에 적용 된 태그를 설정/해제 하거나 검토를 제출 하기 전에 사용자 지정 태그를 적용할 수 있습니다.

![Chrome 브라우저 창에서 플래그가 지정된 텍스트를 보여주는 검토 도구의 스크린샷](../images/reviewresults_text.png)

## <a name="next-steps"></a>다음 단계

이 가이드에서는 설정 하 고 Content Moderator의 검토를 사용 하는 방법을 배웠습니다 [검토 도구](https://contentmoderator.cognitive.microsoft.com)합니다. 다음으로, 참조를 [REST API 가이드](../try-review-api-review.md) 또는 [.NET SDK 가이드](../moderation-reviews-quickstart-dotnet.md) 검토를 프로그래밍 방식으로 만드는 방법에 알아보려면 합니다.