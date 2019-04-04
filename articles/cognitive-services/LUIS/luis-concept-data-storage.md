---
title: 데이터 저장소
titleSuffix: Language Understanding - Azure Cognitive Services
description: LUIS는 키를 통해 지정된 지역에 해당하는 Azure 데이터 저장소에 암호화된 데이터를 저장합니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: diberry
ms.openlocfilehash: a1093c2a6303b453a17a52058303913de5ecfa8d
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2019
ms.locfileid: "58893200"
---
# <a name="data-storage-and-removal-in-language-understanding-luis-cognitive-services"></a>Language Understanding(LUIS) Cognitive Services의 데이터 저장 및 제거
LUIS는 키를 통해 지정된 지역에 해당하는 Azure 데이터 저장소에 암호화된 데이터를 저장합니다. 이 데이터는 30일 동안 저장됩니다. 

## <a name="export-and-delete-app"></a>앱 내보내기 및 삭제
사용자는 앱 [내보내기](luis-how-to-start-new-app.md#export-app) 및 [삭제](luis-how-to-start-new-app.md#delete-app) 작업을 완전히 제어할 수 있습니다. 

## <a name="utterances"></a>발언

서로 다른 두 위치에서 길이 발언을 저장할 수 있습니다. 

* 하는 동안 **제작 과정**, 길이 발언 생성 되 고 의도에 저장 합니다. 길이 발언 의도에 성공적인 LUIS 앱에 대 한 필요 합니다. 앱을 게시 하 고 끝점, 끝점 요청의 querystring에서 쿼리를 받은 후 `log=false`, 끝점 utterance 저장 되는 경우를 결정 합니다. 있는 활성 학습 길이 발언 포함 되는 끝점에 저장 된 경우는 **빌드** 포털의 섹션에서 합니다 **끝점 길이 발언을 검토** 섹션입니다. 
* 경우 없습니다 **끝점 길이 발언 검토**, 의도 utterance 추가할를 utterance 검토 끝점 대상이 눈에 띄도록 일환으로 더 이상 저장 됩니다. 응용 프로그램의 의도에 추가 됩니다. 

<a name="utterances-in-an-intent"></a>

### <a name="delete-example-utterances-from-an-intent"></a>예제에서는 길이 발언 의도에서 삭제
[LUIS](luis-reference-regions.md) 학습에 사용되는 예제 발언을 삭제합니다. LUIS 앱에서 예제 발화를 삭제하면 예제 발화는 LUIS 웹 서비스에서 제거되고 내보낼 수 없습니다.

<a name="utterances-in-review"></a>

### <a name="delete-utterances-in-review-from-active-learning"></a>활성 학습에서 길이 발언 검토 삭제

**[엔드포인트 발화 검토 페이지](luis-how-to-review-endpoint-utterances.md)** 에서 LUIS가 제안하는 사용자 발화 목록에 있는 발화를 삭제할 수 있습니다. 이 목록에서 발화를 삭제하면 제안되지 않지만 로그에서 삭제되지는 않습니다.

활성 학습 길이 발언을 사용 하지 않으려는 경우 있습니다 [활성 학습을 사용 하지 않도록 설정](luis-how-to-review-endpoint-utterances.md#disable-active-learning)합니다. 로깅을 사용 하지 않도록 설정도 활성 학습을 사용 하지 않도록 설정 합니다.

### <a name="disable-logging-utterances"></a>로깅 길이 발언을 사용 하지 않도록 설정
[활성 학습을 사용 하지 않도록 설정](luis-how-to-review-endpoint-utterances.md#disable-active-learning) 로깅을 사용 하지 않도록 설정 됩니다.


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
> [내보내기 및 앱을 삭제 하는 방법에 대 한 설명](luis-how-to-start-new-app.md)
