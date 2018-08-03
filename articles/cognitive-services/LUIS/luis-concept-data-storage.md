---
title: LUIS의 데이터 저장 이해 - Azure | Microsoft Docs
description: 데이터를 Language Understanding(LUIS)에 저장하는 방법 알아보기
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/08/2018
ms.author: diberry
ms.openlocfilehash: e4e289db0803a127bdbff5122095ae2125cdbb38
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/24/2018
ms.locfileid: "39226195"
---
# <a name="data-storage-and-removal"></a>데이터 저장 및 제거
LUIS는 키를 통해 지정된 지역에 해당하는 Azure 데이터 저장소에 암호화된 데이터를 저장합니다. 이 데이터는 30일 동안 저장됩니다. 

## <a name="export-and-delete-app"></a>앱 내보내기 및 삭제
사용자는 앱 [내보내기](luis-how-to-start-new-app.md#export-app) 및 [삭제](luis-how-to-start-new-app.md#delete-app) 작업을 완전히 제어할 수 있습니다. 

## <a name="utterances-in-an-intent"></a>의도의 발화
[LUIS](luis-reference-regions.md) 학습에 사용되는 예제 발언을 삭제합니다. LUIS 앱에서 예제 발화를 삭제하면 예제 발화는 LUIS 웹 서비스에서 제거되고 내보낼 수 없습니다.

## <a name="utterances-in-review"></a>검토 중인 발화
**[끝점 발화 검토 페이지](luis-how-to-review-endoint-utt.md)** 에서 LUIS가 제안하는 사용자 발화 목록에 있는 발화를 삭제할 수 있습니다. 이 목록에서 발화를 삭제하면 제안되지 않지만 로그에서 삭제되지는 않습니다.

## <a name="accounts"></a>계정
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