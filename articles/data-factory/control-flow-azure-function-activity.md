---
title: Azure Data Factory의 Azure 함수 활동
description: Data Factory 파이프라인에서 Azure Function 작업을 사용하여 Azure 함수를 실행하는 방법 알아보기
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/09/2019
ms.openlocfilehash: ee2e59e794cf34a8fd5043a56867a81c2537f1ae
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "81415317"
---
# <a name="azure-function-activity-in-azure-data-factory"></a>Azure Data Factory의 Azure 함수 작업
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]
Azure 함수 작업을 사용하면 Data Factory 파이프라인에서 [Azure Functions](../azure-functions/functions-overview.md)를 실행할 수 있습니다. Azure 함수를 실행하려면 연결된 서비스 연결과 실행하려는 Azure 함수를 지정하는 작업을 만들어야 합니다.

이 기능에 대한 소개 및 데모는 다음 비디오(8분)를 시청하세요.

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Run-Azure-Functions-from-Azure-Data-Factory-pipelines/player]

## <a name="azure-function-linked-service"></a>Azure 함수의 연결된 서비스

Azure 함수의 반환 형식은 유효한 `JObject`여야 합니다. ([JArray](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_Linq_JArray.htm)는 `JObject`가 *아님*에 유의) `JObject` 이외의 모든 반환 형식이 실패하고 *응답 콘텐츠가 유효한 JObject가 아닙니다* 사용자 오류가 발생합니다.

| **속성** | **설명** | **필수** |
| --- | --- | --- |
| type   | type 속성을 다음으로 설정해야 합니다. **AzureFunction** | 예 |
| 함수 앱 URL | Azure 함수 앱의 URL입니다. 형식은 `https://<accountname>.azurewebsites.net`입니다. 이 URL은 Azure Portal에서 함수 앱을 볼 때 **URL** 섹션 아래에 표시되는 값입니다.  | 예 |
| 기능 키 | Azure 함수의 액세스 키입니다. 해당 함수의 **관리** 섹션을 클릭하고 **기능 키** 또는 **호스트 키**를 복사합니다. 자세한 내용은 다음을 참조하세요. [Azure Functions HTTP 트리거 및 바인딩](../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys) | 예 |
|   |   |   |

## <a name="azure-function-activity"></a>Azure 함수 작업

| **속성**  | **설명** | **허용된 값** | **필수** |
| --- | --- | --- | --- |
| name  | 파이프라인의 작업 이름입니다.  | 문자열 | 예 |
| type  | 작업의 형식은 'AzureFunctionActivity'입니다. | 문자열 | 예 |
| 연결된 서비스 | 해당하는 Azure 함수 앱에 대한 Azure Function 연결된 서비스입니다.  | 연결된 서비스 참조 | 예 |
| 함수 이름  | Azure 함수 앱에서 이 작업이 호출하는 함수의 이름입니다. | 문자열 | 예 |
| 메서드  | 함수 호출에 대한 REST API 메서드입니다. | 지원 되는 문자열 형식: "GET", "POST", "PUT"   | 예 |
| header  | 요청에 전송되는 헤더입니다. 예를 들어 요청에 언어 및 형식을 설정하려면 다음과 같이 씁니다. "headers": { "Accept-Language": "en-us", "Content-Type": "application/json" } | 문자열(또는 resultType 문자열이 있는 식) | 아니요 |
| 본문  | 함수 API 메서드에 대한 요청과 함께 전송되는 본문입니다.  | 문자열(또는 resultType 문자열이 있는 식) 또는 개체   | PUT/POST 메서드에 필요합니다. |
|   |   |   | |

 [요청 페이로드 스키마](control-flow-web-activity.md#request-payload-schema)섹션에서 요청 페이로드의 스키마를 참조 하세요   .

## <a name="routing-and-queries"></a>라우팅 및 쿼리

Azure 함수 작업은 **라우팅**을 지원합니다. 예를 들어 Azure 함수에 끝점이 있는 경우  `https://functionAPP.azurewebsites.net/api/<functionName>/<value>?code=<secret>` `functionName` Azure function 활동에서 사용할는 `<functionName>/<value>` 입니다. 이 함수를 매개 변수화 하 여 런타임에 원하는를 제공할 수 있습니다 `functionName` .

또한 Azure 함수 작업은 **쿼리**를 지원합니다. 쿼리는의 일부로 포함 되어야 `functionName` 합니다. 예를 들어 함수 이름이이 `HttpTriggerCSharp` 고 포함 하려는 쿼리가 인 경우 `name=hello` `functionName` Azure function 활동에서로를 생성할 수 있습니다 `HttpTriggerCSharp?name=hello` . 이 함수는 매개 변수화 할 수 있으므로 런타임에 값을 결정할 수 있습니다.

## <a name="timeout-and-long-running-functions"></a>시간 제한 및 장기 실행 함수

설정에서 구성한 설정에 관계 없이 230 초 후에 Azure Functions 시간이 초과 `functionTimeout` 됩니다. 자세한 내용은 [이 문서](../azure-functions/functions-versions.md#timeout)를 참조하세요. 이 동작을 해결 하려면 비동기 패턴을 따르거나 Durable Functions를 사용 합니다. Durable Functions의 혜택은 자신의 상태 추적 메커니즘을 제공 하므로 사용자가 직접 구현할 필요가 없다는 것입니다.

[이 문서](../azure-functions/durable/durable-functions-overview.md)에서 Durable Functions에 대해 자세히 알아보세요. Azure Function 활동을 설정 하 여 지 속성 함수를 호출할 수 있습니다 .이 함수는 [이 예와](../azure-functions/durable/durable-functions-http-features.md#http-api-url-discovery)같이 다른 URI를 사용 하는 응답을 반환 합니다. 는 `statusQueryGetUri` 함수가 실행 되는 동안 HTTP 상태 202을 반환 하므로 웹 작업을 사용 하 여 함수의 상태를 폴링할 수 있습니다. 필드를로 설정 하 여 웹 활동을 설정 하기만 하면 됩니다 `url` `@activity('<AzureFunctionActivityName>').output.statusQueryGetUri` . 지 속성 함수가 완료 되 면 함수의 출력이 웹 활동의 출력이 됩니다.


## <a name="sample"></a>예제

Azure 함수를 사용 하 여 tar 파일의 콘텐츠를 추출 하는 Data Factory 샘플은 [여기](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV2/UntarAzureFilesWithAzureFunction)에서 찾을 수 있습니다.

## <a name="next-steps"></a>다음 단계

[Azure Data Factory의 파이프라인 및 작업](concepts-pipelines-activities.md)에서 Data Factory의 작업에 대해 자세히 알아보세요.
