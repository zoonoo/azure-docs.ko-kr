---
title: Azure Data Factory의Azure 함수 작업 | Microsoft Docs
description: Data Factory 파이프라인에서 Azure Function 작업을 사용하여 Azure 함수를 실행하는 방법 알아보기
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: douglasl
ms.openlocfilehash: ee99733440d74424f98a2ed16de83c88bae53ff1
ms.sourcegitcommit: 3ba9bb78e35c3c3c3c8991b64282f5001fd0a67b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/15/2019
ms.locfileid: "54321792"
---
# <a name="azure-function-activity-in-azure-data-factory"></a>Azure Data Factory의 Azure 함수 작업

Azure 함수 작업을 사용하면 Data Factory 파이프라인에서 [Azure Functions](../azure-functions/functions-overview.md)를 실행할 수 있습니다. Azure 함수를 실행하려면 연결된 서비스 연결과 실행하려는 Azure 함수를 지정하는 작업을 만들어야 합니다.

이 기능에 대한 소개 및 데모는 다음 비디오(8분)를 시청하세요.

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Run-Azure-Functions-from-Azure-Data-Factory-pipelines/player]

## <a name="azure-function-linked-service"></a>Azure 함수의 연결된 서비스

Azure 함수의 반환 형식은 유효한 `JObject`여야 합니다. ([JArray](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_Linq_JArray.htm)는 `JObject`가 *아님*에 유의) `JObject` 이외의 반환 형식은 실패하고 일반 사용자 오류 *엔드포인트를 호출하는 오류*가 발생합니다.

| **속성** | **설명** | **필수** |
| --- | --- | --- |
| 형식   | type 속성을 다음으로 설정해야 합니다. **AzureFunction** | 예 |
| 함수 앱 URL | Azure Function App의 URL입니다. 형식은 `https://<accountname>.azurewebsites.net`입니다. 이 URL은 Azure Portal에서 Function App을 볼 때 **URL** 섹션 아래에 표시되는 값입니다.  | 예 |
| 기능 키 | Azure 함수의 액세스 키입니다. 해당 함수의 **관리** 섹션을 클릭하고 **기능 키** 또는 **호스트 키**를 복사합니다. 자세한 내용은 다음을 참조하세요. [Azure Functions HTTP 트리거 및 바인딩](../azure-functions/functions-bindings-http-webhook.md#authorization-keys) | 예 |
|   |   |   |

## <a name="azure-function-activity"></a>Azure 함수 작업

| **속성**  | **설명** | **허용되는 값** | **필수** |
| --- | --- | --- | --- |
| 이름  | 파이프라인의 작업 이름입니다.  | 문자열 | 예 |
| 형식  | 작업의 형식은 'AzureFunctionActivity'입니다. | 문자열 | 예 |
| 연결된 서비스 | 해당하는 Azure Function App에 대한 Azure Function 연결된 서비스입니다.  | 연결된 서비스 참조 | 예 |
| 함수 이름  | Azure Function App에서 이 작업이 호출하는 함수의 이름입니다. | 문자열 | 예 |
| 메서드  | 함수 호출에 대한 REST API 메서드입니다. | 문자열 지원 형식: "GET", "POST", "PUT"   | 예 |
| 머리글  | 요청에 전송되는 헤더입니다. 예를 들어 요청에 언어 및 형식을 설정하려면 다음과 같이 씁니다. "headers": { "Accept-Language": "en-us", "Content-Type": "application/json" } | 문자열(또는 resultType 문자열이 있는 식) | 아니요 |
| 본문  | 함수 API 메서드에 대한 요청과 함께 전송되는 본문입니다.  | 문자열(또는 resultType 문자열이 있는 식) 또는 개체   | PUT/POST 메서드에 필요합니다. |
|   |   |   | |

 [요청 페이로드 스키마](control-flow-web-activity.md#request-payload-schema) 섹션에서 요청 페이로드의 스키마를 참조하세요.

## <a name="more-info"></a>자세한 정보

Azure 함수 작업은 **라우팅**을 지원합니다. 예를 들어 앱에서 다음 라우팅(`https://functionAPP.azurewebsites.net/api/functionName/{value}?code=<secret>`)을 사용하는 경우 `functionName`은 `functionName/{value}`이며, 런타임 시 원하는 `functionName`을 제공하도록 변수화할 수 있습니다.

또한 Azure 함수 작업은 **쿼리**를 지원합니다. 쿼리는 `functionName`의 일부여야 합니다(예: `HttpTriggerCSharp2?name=hello` - 여기서 `function name`은 `HttpTriggerCSharp2`).

## <a name="next-steps"></a>다음 단계

[Azure Data Factory의 파이프라인 및 작업](concepts-pipelines-activities.md)에서 Data Factory의 작업에 대해 자세히 알아보세요.
