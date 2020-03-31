---
title: Azure 데이터 팩터리의 Azure 함수 활동
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
ms.openlocfilehash: ffb610634399594788afcb9b600ba00c6803dfdd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77207028"
---
# <a name="azure-function-activity-in-azure-data-factory"></a>Azure Data Factory의 Azure 함수 작업

Azure 함수 작업을 사용하면 Data Factory 파이프라인에서 [Azure Functions](../azure-functions/functions-overview.md)를 실행할 수 있습니다. Azure 함수를 실행하려면 연결된 서비스 연결과 실행하려는 Azure 함수를 지정하는 작업을 만들어야 합니다.

이 기능에 대한 소개 및 데모는 다음 비디오(8분)를 시청하세요.

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Run-Azure-Functions-from-Azure-Data-Factory-pipelines/player]

## <a name="azure-function-linked-service"></a>Azure 함수의 연결된 서비스

Azure 함수의 반환 형식은 유효한 `JObject`여야 합니다. [(JArray는](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_Linq_JArray.htm) *not* `JObject`.) 실패 하 고 `JObject` 사용자 오류 *응답 콘텐츠가 유효한 JObject*.

| **속성** | **설명** | **필수** |
| --- | --- | --- |
| type   | 형식 속성을 설정 해야 **합니다.: AzureFunction** | 예 |
| 함수 앱 URL | Azure 함수 앱의 URL입니다. 형식은 `https://<accountname>.azurewebsites.net`입니다. 이 URL은 Azure Portal에서 함수 앱을 볼 때 **URL** 섹션 아래에 표시되는 값입니다.  | 예 |
| 기능 키 | Azure 함수의 액세스 키입니다. 해당 함수의 **관리** 섹션을 클릭하고 **기능 키** 또는 **호스트 키**를 복사합니다. 여기에서 자세히 알아보기: [Azure Functions HTTP 트리거 및 바인딩](../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys) | 예 |
|   |   |   |

## <a name="azure-function-activity"></a>Azure 함수 작업

| **속성**  | **설명** | **허용된 값** | **필수** |
| --- | --- | --- | --- |
| name  | 파이프라인의 작업 이름입니다.  | String | 예 |
| type  | 작업의 형식은 'AzureFunctionActivity'입니다. | String | 예 |
| 연결된 서비스 | 해당하는 Azure 함수 앱에 대한 Azure Function 연결된 서비스입니다.  | 연결된 서비스 참조 | 예 |
| 함수 이름  | Azure 함수 앱에서 이 작업이 호출하는 함수의 이름입니다. | String | 예 |
| method  | 함수 호출에 대한 REST API 메서드입니다. | 문자열 지원 형식: "GET", "POST", "PUT"   | 예 |
| 머리글  | 요청에 전송되는 헤더입니다. 예를 들어 요청에 언어 및 형식을 설정하려면 다음과 같이 씁니다. "headers": { "Accept-Language": "en-us", "Content-Type": "application/json" } | 문자열(또는 resultType 문자열이 있는 식) | 예 |
| 본문  | 함수 API 메서드에 대한 요청과 함께 전송되는 본문입니다.  | 문자열(또는 resultType 문자열이 있는 식) 또는 개체   | PUT/POST 메서드에 필요합니다. |
|   |   |   | |

요청 페이로드 스키마 [Request payload schema](control-flow-web-activity.md#request-payload-schema) 섹션에서 요청 페이로드의 스키마를 참조하십시오.

## <a name="routing-and-queries"></a>라우팅 및 쿼리

Azure 함수 작업은 **라우팅**을 지원합니다. 예를 들어 Azure 함수에 끝점이 `https://functionAPP.azurewebsites.net/api/<functionName>/<value>?code=<secret>`있는 `functionName` 경우 Azure Function 활동에 `<functionName>/<value>`사용할 수 있는 것은 입니다. 이 함수를 매개 변수화하여 `functionName` 런타임에 원하는 함수를 제공할 수 있습니다.

또한 Azure 함수 작업은 **쿼리**를 지원합니다. 질의의 일부로 포함되어야 `functionName`합니다. 예를 들어 함수 이름이 `HttpTriggerCSharp` 있고 포함할 쿼리가 `name=hello`있는 경우 은 Azure `functionName` Function Activity에서 `HttpTriggerCSharp?name=hello`로 생성할 수 있습니다. 이 함수는 매개 변수화될 수 있으므로 런타임시 값을 결정할 수 있습니다.

## <a name="timeout-and-long-running-functions"></a>시간 시간 및 장기 실행 함수

Azure 함수는 설정에서 구성한 설정에 `functionTimeout` 관계없이 230초 후에 시간 배분됩니다. 자세한 내용은 [이 문서를](../azure-functions/functions-versions.md#timeout)참조하십시오. 이 동작을 해결하려면 비동기 패턴을 따르거나 지속형 함수를 사용합니다. 내구성 함수의 장점은 자체 상태 추적 메커니즘을 제공하므로 직접 구현할 필요가 없다는 것입니다.

[이 문서에서](../azure-functions/durable/durable-functions-overview.md)지속 적 기능에 대 한 자세한 내용은 입니다. [이 예제와](../azure-functions/durable/durable-functions-http-features.md#http-api-url-discovery)같이 다른 URI를 사용하여 응답을 반환하는 지속형 함수를 호출하도록 Azure Function 활동을 설정할 수 있습니다. 함수가 실행되는 동안 HTTP 상태 202를 반환하므로 `statusQueryGetUri` 웹 활동을 사용하여 함수의 상태를 폴링할 수 있습니다. `url` 필드가 로 설정된 웹 활동을 `@activity('<AzureFunctionActivityName>').output.statusQueryGetUri`설정하기만 하면 됩니다. 지속 형 함수가 완료되면 함수의 출력은 웹 활동의 출력이 됩니다.


## <a name="sample"></a>예제

Azure 함수를 사용하여 tar 파일의 내용을 추출하는 데이터 팩터리의 샘플을 여기에서 찾을 수 [있습니다.](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV2/UntarAzureFilesWithAzureFunction)

## <a name="next-steps"></a>다음 단계

[Azure Data Factory의 파이프라인 및 작업](concepts-pipelines-activities.md)에서 Data Factory의 작업에 대해 자세히 알아보세요.
