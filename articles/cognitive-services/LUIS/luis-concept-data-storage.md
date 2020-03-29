---
title: 데이터 저장 - LUIS
titleSuffix: Azure Cognitive Services
description: LUIS는 키를 통해 지정된 지역에 해당하는 Azure 데이터 저장소에 암호화된 데이터를 저장합니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: e3028a9f046edb4013d8a2d6ec88d8c3cb2ca489
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79221334"
---
# <a name="data-storage-and-removal-in-language-understanding-luis-cognitive-services"></a>Language Understanding(LUIS) Cognitive Services의 데이터 스토리지 및 제거
LUIS는 키를 통해 지정된 지역에 해당하는 Azure 데이터 저장소에 암호화된 데이터를 저장합니다. 이 데이터는 30일 동안 저장됩니다. 

## <a name="export-and-delete-app"></a>앱 내보내기 및 삭제
사용자는 앱 [내보내기](luis-how-to-start-new-app.md#export-app) 및 [삭제](luis-how-to-start-new-app.md#delete-app) 작업을 완전히 제어할 수 있습니다. 

## <a name="utterances"></a>발언

발언은 두 개의 서로 다른 위치에 저장할 수 있습니다. 

* **작성 프로세스**중에 발언이 만들어지고 의도에 저장됩니다. 성공적인 LUIS 앱에 대한 의도의 발광이 필요합니다. 앱이 게시되고 끝점에서 쿼리를 수신하면 끝점 요청의 쿼리 `log=false`문자열이 종료점 발언이 저장되는지 여부를 결정합니다. 끝점이 저장되면 포털의 **빌드** 섹션에서 **끝점 발언 검토** 섹션에서 활성 학습 발언의 일부가 됩니다. 
* **끝점 발언을 검토하고**의도에 발언을 추가하면 발언은 검토할 끝점 발언의 일부로 더 이상 저장되지 않습니다. 앱의 의도에 추가됩니다. 

<a name="utterances-in-an-intent"></a>

### <a name="delete-example-utterances-from-an-intent"></a>의도에서 예제 발언 삭제

[LUIS](luis-reference-regions.md) 학습에 사용되는 예제 발언을 삭제합니다. LUIS 앱에서 예제 발화를 삭제하면 예제 발화는 LUIS 웹 서비스에서 제거되고 내보낼 수 없습니다.

<a name="utterances-in-review"></a>

### <a name="delete-utterances-in-review-from-active-learning"></a>활성 학습에서 검토 중인 발언 삭제

**[엔드포인트 발화 검토 페이지](luis-how-to-review-endpoint-utterances.md)** 에서 LUIS가 제안하는 사용자 발화 목록에 있는 발화를 삭제할 수 있습니다. 이 목록에서 발화를 삭제하면 제안되지 않지만 로그에서 삭제되지는 않습니다.

활성 학습 발언을 원하지 않는 경우 활성 [학습을 비활성화할](luis-how-to-review-endpoint-utterances.md#disable-active-learning)수 있습니다. 활성 학습을 사용하지 않도록 설정하면 로깅도 비활성화됩니다.

### <a name="disable-logging-utterances"></a>로깅 발언 사용 안 함
[활성 학습을 사용하지 않도록 설정하면](luis-how-to-review-endpoint-utterances.md#disable-active-learning) 로깅이 비활성화됩니다.


<a name="accounts"></a>

## <a name="delete-an-account"></a>계정 삭제
계정을 삭제하면 모든 앱과 예제 발화 및 로그가 함께 삭제됩니다. 데이터는 계정과 데이터가 영구 삭제되기 전에 60일 동안 보존됩니다.

계정은 **설정** 페이지에서 삭제할 수 있습니다. 오른쪽 위 탐색 모음에서 계정 이름을 선택하여 **설정** 페이지로 이동합니다.

## <a name="data-inactivity-as-an-expired-subscription"></a>만료된 구독으로 데이터 비활성
데이터 보존 및 삭제를 위해 비활성 LUIS 앱은 _Microsoft의 재량_으로 만료된 구독으로 처리될 수 있습니다. 지난 90일 동안 다음 기준을 충족하는 경우, 앱을 비활성으로 간주합니다. 

* 앱이 호출되지 **않은** 경우
* 수정되지 않은 경우
* 현재 키가 할당되지 않은 경우
* 사용자가 로그인하지 않은 경우

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [앱 내보내기 및 삭제에 대해 알아보기](luis-how-to-start-new-app.md)
