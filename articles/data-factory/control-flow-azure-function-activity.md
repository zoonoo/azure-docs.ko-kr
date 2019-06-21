---
title: Azure Data Factory의Azure 함수 작업 | Microsoft Docs
description: Data Factory 파이프라인에서 Azure Function 작업을 사용하여 Azure 함수를 실행하는 방법 알아보기
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/09/2019
author: sharonlo101
ms.author: shlo
manager: craigg
ms.openlocfilehash: dfdfb9e38f16d0077175587933b0800b87cc1931
ms.sourcegitcommit: 22c97298aa0e8bd848ff949f2886c8ad538c1473
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/14/2019
ms.locfileid: "67144124"
---
# <a name="azure-function-activity-in-azure-data-factory"></a>Azure Data Factory의 Azure 함수 작업

Azure 함수 작업을 사용하면 Data Factory 파이프라인에서 [Azure Functions](../azure-functions/functions-overview.md)를 실행할 수 있습니다. Azure 함수를 실행하려면 연결된 서비스 연결과 실행하려는 Azure 함수를 지정하는 작업을 만들어야 합니다.

이 기능에 대한 소개 및 데모는 다음 비디오(8분)를 시청하세요.

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Run-Azure-Functions-from-Azure-Data-Factory-pipelines/player]

## <a name="azure-function-linked-service"></a>Azure 함수의 연결된 서비스

Azure 함수의 반환 형식은 유효한 `JObject`여야 합니다. ([JArray](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_Linq_JArray.htm)는 `JObject`가 *아님*에 유의) 이외의 형식을 반환 `JObject` 실패 하 고 사용자 오류를 발생 시킵니다 *응답 콘텐츠 없는 유효한 JObject*합니다.

| **속성** | **설명** | **필수** |
| --- | --- | --- |
| type   | type 속성을 다음으로 설정해야 합니다. **AzureFunction** | 예 |
| 함수 앱 URL | Azure Function App의 URL입니다. 형식은 `https://<accountname>.azurewebsites.net`입니다. 이 URL은 Azure Portal에서 Function App을 볼 때 **URL** 섹션 아래에 표시되는 값입니다.  | 예 |
| 기능 키 | Azure 함수의 액세스 키입니다. 해당 함수의 **관리** 섹션을 클릭하고 **기능 키** 또는 **호스트 키**를 복사합니다. 자세한 내용은 다음을 참조하세요. [Azure Functions HTTP 트리거 및 바인딩](../azure-functions/functions-bindings-http-webhook.md#authorization-keys) | 예 |
|   |   |   |

## <a name="azure-function-activity"></a>Azure 함수 작업

| **속성**  | **설명** | **허용되는 값** | **필수** |
| --- | --- | --- | --- |
| name  | 파이프라인의 작업 이름입니다.  | String | 예 |
| 형식  | 작업의 형식은 'AzureFunctionActivity'입니다. | String | 예 |
| 연결된 서비스 | 해당하는 Azure Function App에 대한 Azure Function 연결된 서비스입니다.  | 연결된 서비스 참조 | 예 |
| 함수 이름  | Azure Function App에서 이 작업이 호출하는 함수의 이름입니다. | String | 예 |
| 메서드  | 함수 호출에 대한 REST API 메서드입니다. | 문자열 지원 형식: "GET", "POST", "PUT"   | 예 |
| 머리글  | 요청에 전송되는 헤더입니다. 예를 들어 요청에 언어 및 형식을 설정하려면 다음과 같이 씁니다. "headers": { "Accept-Language": "en-us", "Content-Type": "application/json" } | 문자열(또는 resultType 문자열이 있는 식) | 아닙니다. |
| 본문  | 함수 API 메서드에 대한 요청과 함께 전송되는 본문입니다.  | 문자열(또는 resultType 문자열이 있는 식) 또는 개체   | PUT/POST 메서드에 필요합니다. |
|   |   |   | |

 [요청 페이로드 스키마](control-flow-web-activity.md#request-payload-schema) 섹션에서 요청 페이로드의 스키마를 참조하세요.

## <a name="routing-and-queries"></a>라우팅 및 쿼리

Azure 함수 작업은 **라우팅**을 지원합니다. 예를 들어 Azure Function에는 끝점이 `https://functionAPP.azurewebsites.net/api/<functionName>/<value>?code=<secret>`, 해당 `functionName` Azure 함수 작업에서 사용 하는 `<functionName>/<value>`합니다. 원하는 제공 하려면이 함수를 매개 변수화 할 수 있습니다 `functionName` 런타임 시.

또한 Azure 함수 작업은 **쿼리**를 지원합니다. 일부로 포함 될 경우에 쿼리가 `functionName`합니다. 예를 들어 경우 함수 이름은 `HttpTriggerCSharp` 포함 하려는 쿼리가 `name=hello`를 생성할 수 있습니다 다음는 `functionName` 으로 Azure 함수 작업에서 `HttpTriggerCSharp?name=hello`합니다. 이 함수는 값은 런타임 시 결정 될 수 있습니다 매개 변수화 할 수 있습니다.

## <a name="timeout-and-long-running-functions"></a>시간 제한 및 장기 실행 함수

에 관계 없이 230 초 이후에 azure 함수 시간 초과 `functionTimeout` 설정에서 구성한 설정 합니다. 자세한 내용은 [이 문서](../azure-functions/functions-versions.md#timeout)(영문)를 읽어보세요. 이 문제를 해결 하려면 비동기 패턴을 따르거나 Durable Functions를 사용 합니다. Durable Functions의 장점은 자체적으로 구현 하지 않아도 되므로 자신의 상태 추적 메커니즘을 제공 합니다.

에 지 속성 함수에 자세히 알아보려면 [이 문서에서는](../azure-functions/durable/durable-functions-overview.md)합니다. 와 같은 다른 URI 사용 하 여 응답을 반환 하는 지 속성 함수를 호출 하는 Azure 함수 작업을 설정할 수 있습니다 [이 예제에서는](../azure-functions/durable/durable-functions-http-api.md#http-api-url-discovery)합니다. 때문에 `statusQueryGetUri` HTTP 상태 202 함수 하는 동안 실행 되 고, 웹 작업을 사용 하 여 함수의 상태를 폴링할 수를 반환 합니다. 웹 작업을 사용 하 여 간단히 설정 합니다 `url` 필드 설정 `@activity('<AzureFunctionActivityName>').output.statusQueryGetUri`합니다. 지 속성 함수에는 다음이 완료 되 면 함수는 출력 웹 작업의 출력이 됩니다.


## <a name="sample"></a>샘플

Tar 파일의 콘텐츠를 추출 하는 Azure 함수를 사용 하는 Data Factory의 샘플을 찾을 수 있습니다 [여기](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV2/UntarAzureFilesWithAzureFunction)합니다.

## <a name="next-steps"></a>다음 단계

[Azure Data Factory의 파이프라인 및 작업](concepts-pipelines-activities.md)에서 Data Factory의 작업에 대해 자세히 알아보세요.
