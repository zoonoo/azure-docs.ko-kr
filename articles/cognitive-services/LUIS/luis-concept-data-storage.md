---
title: 데이터 스토리지 - LUIS
titleSuffix: Azure Cognitive Services
description: LUIS는 키를 통해 지정된 지역에 해당하는 Azure 데이터 저장소에 암호화된 데이터를 저장합니다.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 12/07/2020
ms.openlocfilehash: a58bcff4e39c4a4a907cd8567b47b074ff299bd5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "97008455"
---
# <a name="data-storage-and-removal-in-language-understanding-luis-cognitive-services"></a>Language Understanding(LUIS) Cognitive Services의 데이터 스토리지 및 제거

LUIS는 키를 통해 지정된 [지역](luis-reference-regions.md)에 해당하는 Azure 데이터 저장소에 암호화된 데이터를 저장합니다. 

* 엔터티, 의도 및 발화 등의 모델을 학습하는 데 사용되는 데이터는 애플리케이션의 수명 동안 LUIS에 저장됩니다. 소유자 또는 기여자가 앱을 삭제하는 경우 이 데이터도 함께 삭제됩니다. 애플리케이션이 90일 이내에 사용되지 않은 경우 데이터가 삭제됩니다. 

* 애플리케이션 작성자는 게시된 애플리케이션으로 전송되는 발화에 대해 [로깅을 사용](luis-how-to-review-endpoint-utterances.md#log-user-queries-to-enable-active-learning)하도록 선택할 수 있습니다. 사용하도록 설정된 경우 발화는 30일 동안 저장되며 애플리케이션 작성자가 볼 수 있습니다. 애플리케이션을 게시할 때 로깅을 사용하도록 설정하지 않은 경우 이 데이터는 저장되지 않습니다.

## <a name="export-and-delete-app"></a>앱 내보내기 및 삭제
사용자는 앱 [내보내기](luis-how-to-start-new-app.md#export-app) 및 [삭제](luis-how-to-start-new-app.md#delete-app) 작업을 완전히 제어할 수 있습니다. 

## <a name="utterances"></a>발언

발화는 서로 다른 두 위치에 저장할 수 있습니다. 

* **작성 프로세스** 중에 발화가 만들어지고 의도에 저장됩니다. 의도의 발화는 성공적인 LUIS 앱에 필요합니다. 앱이 게시되고 엔드포인트에서 쿼리를 수신하면 엔드포인트 요청의 쿼리 문자열 `log=false`가 엔드포인트 발화가 저장되었는지 확인합니다. 엔드포인트는 저장되면 **엔드포인트 발화 검토** 섹션에서 포털의 **빌드** 섹션에 있는 활성 학습 발화의 일부가 됩니다. 
* **엔드포인트 발화를 검토** 하고 발화를 의도에 추가하면 발화가 검토할 엔드포인트 발화의 일부로 더 이상 저장되지 않습니다. 앱의 의도에 추가됩니다. 

<a name="utterances-in-an-intent"></a>

### <a name="delete-example-utterances-from-an-intent"></a>의도에서 예제 발화 삭제

[LUIS](luis-reference-regions.md) 학습에 사용되는 예제 발언을 삭제합니다. LUIS 앱에서 예제 발화를 삭제하면 예제 발화는 LUIS 웹 서비스에서 제거되고 내보낼 수 없습니다.

<a name="utterances-in-review"></a>

### <a name="delete-utterances-in-review-from-active-learning"></a>활성 학습에서 검토 중인 발화 삭제

**[엔드포인트 발화 검토 페이지](luis-how-to-review-endpoint-utterances.md)** 에서 LUIS가 제안하는 사용자 발화 목록에 있는 발화를 삭제할 수 있습니다. 이 목록에서 발화를 삭제하면 제안되지 않지만 로그에서 삭제되지는 않습니다.

활성 학습 발화를 원하지 않는 경우 [활성 학습을 사용하지 않도록 설정](luis-how-to-review-endpoint-utterances.md#disable-active-learning)할 수 있습니다. 활성 학습을 사용하지 않도록 설정하여 로깅을 사용하지 않습니다.

### <a name="disable-logging-utterances"></a>발화 로깅 사용 안 함
[활성 학습을 사용하지 않도록 설정](luis-how-to-review-endpoint-utterances.md#disable-active-learning)하여 로깅을 사용하지 않습니다.


<a name="accounts"></a>

## <a name="delete-an-account"></a>계정 삭제
마이그레이션되지 않은 경우 계정을 삭제할 수 있으며 모든 앱은 해당 예제 발화 및 로그와 함께 삭제됩니다. 데이터는 계정과 데이터가 영구 삭제되기 전에 90일 동안 보존됩니다.

계정은 **설정** 페이지에서 삭제할 수 있습니다. 오른쪽 위 탐색 모음에서 계정 이름을 선택하여 **설정** 페이지로 이동합니다.

## <a name="delete-an-authoring-resource"></a>작성 리소스 삭제
[작성 리소스로 마이그레이션](./luis-migration-authoring.md)한 경우 Azure Portal에서 리소스 자체를 삭제하면 해당 리소스와 연결된 모든 애플리케이션이 해당 예제 발화 및 로그와 함께 삭제됩니다. 데이터는 90일 동안 유지된 후에 영구적으로 삭제됩니다.    

리소스를 삭제하려면 [Azure Portal](https://ms.portal.azure.com/#home)로 이동하여 LUIS 작성 리소스를 선택합니다. **개요** 탭으로 이동하여 페이지 맨 위에 있는 **삭제** 단추를 클릭합니다. 그런 다음 리소스가 삭제되었는지 확인합니다. 

## <a name="data-inactivity-as-an-expired-subscription"></a>만료된 구독으로 데이터 비활성
데이터 보존 및 삭제를 위해 비활성 LUIS 앱은 _Microsoft의 재량_ 으로 만료된 구독으로 처리될 수 있습니다. 지난 90일 동안 다음 기준을 충족하는 경우, 앱을 비활성으로 간주합니다. 

* 앱이 호출되지 **않은** 경우
* 수정되지 않은 경우
* 현재 키가 할당되지 않은 경우
* 사용자가 로그인하지 않은 경우

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [앱 내보내기 및 삭제에 대해 알아보기](luis-how-to-start-new-app.md)