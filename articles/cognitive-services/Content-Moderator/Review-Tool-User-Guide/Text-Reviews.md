---
title: Azure Content Moderator에서 텍스트 검토 - Content Moderator
description: Content Moderator에서 텍스트를 검토하여 점수 및 검색된 태그를 살펴보는 방법을 알아봅니다. 정보를 사용하여 콘텐츠가 적절한지 확인합니다.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: article
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 896956653ed924b7e30e63c1ae5cfa90dd0cc94d
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55219790"
---
# <a name="let-human-reviewers-review-text"></a>사용자 검토자의 텍스트 검토 허용

Azure Content Moderator에서 점수 및 검색된 태그를 사용하여 텍스트를 검토할 수 있습니다. 정보를 사용하여 콘텐츠가 적절한지 확인합니다. 

## <a name="select-or-enter-the-text-to-review"></a>검토할 텍스트를 선택하거나 입력합니다.

Content Moderator에서 **시도** 탭을 선택합니다. 그런 다음, **텍스트** 옵션을 선택하여 그 다음 조정 시작 화면으로 이동합니다. 아무 텍스트를 입력하거나 기본 샘플 텍스트를 제출하면 자동으로 텍스트가 조정됩니다. 최대 1,024자를 입력할 수 있습니다.

## <a name="get-ready-to-review-results"></a>결과 검토 준비

검토 도구는 먼저 Text Moderation API를 호출합니다. 그런 다음, 검색된 태그를 사용하여 텍스트 검토를 생성합니다. 검토 도구는 팀의 주의를 끌기 위해 점수 결과를 일치시킵니다.

## <a name="review-text-results"></a>텍스트 결과 검토

자세한 결과가 창에 표시됩니다. 결과에는 Text Moderation API가 반환한 검색된 태그 및 용어가 포함됩니다. 태그의 선택 상태를 전환하려면 태그를 선택합니다. 직접 만든 사용자 지정 태그를 사용해도 됩니다.

![Chrome 브라우저 창에서 플래그가 지정된 텍스트를 보여주는 검토 도구의 스크린샷](../images/reviewresults_text.png)
