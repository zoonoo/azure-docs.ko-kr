---
title: Azure 데이터 팩터리의 웹후크 활동
description: webhook 활동은 사용자가 지정한 특정 조건으로 연결된 데이터 집합의 유효성을 검사할 때까지 파이프라인 실행을 계속하지 않습니다.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/25/2019
ms.openlocfilehash: ced2279878ee2eb361ec7338647418658e411513
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79213014"
---
# <a name="webhook-activity-in-azure-data-factory"></a>Azure 데이터 팩터리의 웹후크 활동

웹후크 활동은 사용자 지정 코드를 통해 파이프라인 실행을 제어할 수 있습니다. webhook 활동을 사용하면 고객의 코드가 끝점을 호출하고 콜백 URL을 전달할 수 있습니다. 파이프라인 실행은 다음 활동으로 진행되기 전에 콜백 호출을 기다립니다.

## <a name="syntax"></a>구문

```json

{
    "name": "MyWebHookActivity",
    "type": "WebHook",
    "typeProperties": {
        "method": "POST",
        "url": "<URLEndpoint>",
        "headers": {
            "Content-Type": "application/json"
        },
        "body": {
            "key": "value"
        },
        "timeout": "00:03:00",
        "authentication": {
            "type": "ClientCertificate",
            "pfx": "****",
            "password": "****"
        }
    }
}

```

## <a name="type-properties"></a>형식 속성

속성 | 설명 | 허용되는 값 | 필수
-------- | ----------- | -------------- | --------
**(이름)** | 웹후크 활동의 이름입니다. | String | yes |
**종류** | "WebHook"으로 설정해야 합니다. | String | yes |
**메서드** | 대상 끝점에 대한 REST API 메서드입니다. | 문자열입니다. 지원되는 형식은 "POST"입니다. | yes |
**url** | 대상 끝점 및 경로입니다. | 결과가 있는 문자열 또는 **식입니다.문자열의** 값입니다. | yes |
**헤더** | 요청에 전송되는 헤더입니다. 다음은 요청에 대한 언어 및 유형을 설정하는 예제입니다. `"headers" : { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 결과가 있는 문자열 또는 **식입니다.문자열의** 값입니다. | 예. 같은 `Content-Type` `"headers":{ "Content-Type":"application/json"}` 헤더가 필요합니다. |
**몸** | 엔드포인트에 전송된 페이로드를 나타냅니다. | 유효한 JSON 또는 결과가 있는 **식입니다JSON의** Type 값입니다. 요청 페이로드의 스키마에 대한 [페이로드 스키마](https://docs.microsoft.com/azure/data-factory/control-flow-web-activity#request-payload-schema) 요청을 참조하십시오. | yes |
**인증** | 끝점을 호출하는 데 사용되는 인증 방법입니다. 지원되는 형식은 "기본" 및 "ClientCertificate"입니다. 자세한 내용은 [인증](https://docs.microsoft.com/azure/data-factory/control-flow-web-activity#authentication)을 참조하세요. 인증이 필요하지 않은 경우 이 속성을 제외합니다. | 결과가 있는 문자열 또는 **식입니다.문자열의** 값입니다. | 예 |
**타임 아웃** | 활동이 **호출될 callBackUri에** 의해 지정된 콜백이 호출될 때까지 대기하는 시간입니다. 기본값은 10분("00:10:00")입니다. 값에는 TimeSpan 형식 *d가*있습니다. *hh*:*mm*:*ss*. | String | 예 |
**콜백 시 상태 보고** | 사용자가 웹후크 활동의 실패한 상태를 보고할 수 있습니다. | 부울 | 예 |

## <a name="authentication"></a>인증

웹후크 활동은 다음 인증 유형을 지원합니다.

### <a name="none"></a>None

인증이 필요하지 않은 경우 **인증** 속성을 포함하지 마세요.

### <a name="basic"></a>Basic

기본 인증에 사용할 사용자 이름과 암호를 지정합니다.

```json
"authentication":{
   "type":"Basic",
   "username":"****",
   "password":"****"
}
```

### <a name="client-certificate"></a>클라이언트 인증서

PFX 파일과 암호의 Base64 인코딩 내용을 지정합니다.

```json
"authentication":{
   "type":"ClientCertificate",
   "pfx":"****",
   "password":"****"
}
```

### <a name="managed-identity"></a>관리 ID

데이터 팩터리의 관리되는 ID를 사용하여 액세스 토큰이 요청되는 리소스 URI를 지정합니다. Azure Resource 관리 API를 호출하려면 `https://management.azure.com/`을 사용합니다. 관리되는 ID의 작동 방식에 대한 자세한 내용은 [Azure 리소스 개요에 대한 관리ID를](/azure/active-directory/managed-identities-azure-resources/overview)참조하세요.

```json
"authentication": {
    "type": "MSI",
    "resource": "https://management.azure.com/"
}
```

> [!NOTE]
> 데이터 팩터리가 Git 리포지토리로 구성된 경우 기본 또는 클라이언트 인증서 인증을 사용하려면 자격 증명을 Azure Key Vault에 저장해야 합니다. Azure 데이터 팩터리는 Git에 암호를 저장하지 않습니다.

## <a name="additional-notes"></a>추가적인 참고 사항

데이터 팩터리는 URL 끝점으로 전송된 본문에 추가 속성 **callBackUri를** 전달합니다. Data Factory는 지정된 시간 시간 지정 값 보다 전에 이 URI가 호출될 것으로 예상합니다. URI가 호출되지 않으면 활동이 "TimedOut" 상태와 함께 실패합니다.

사용자 지정 끝점에 대한 호출이 실패하면 웹후크 작업이 실패합니다. 모든 오류 메시지는 콜백 본문에 추가하고 이후 활동에 사용할 수 있습니다.

모든 REST API 호출에 대해 끝점이 1분 이내에 응답하지 않으면 클라이언트가 시간 지정합니다. 이 동작은 표준 HTTP 모범 사례입니다. 이 문제를 해결하려면 202 패턴을 구현합니다. 현재의 경우 끝점은 202(허용됨)와 클라이언트 폴링을 반환합니다.

요청의 1분 시간 초과는 활동 시간 초과와 는 아무 관련이 없습니다. 후자는 **callbackUri**.

콜백 URI로 다시 전달된 본문이 유효한 JSON이어야 합니다. `Content-Type` 헤더를 `application/json`으로 설정합니다.

**콜백 속성에서 보고서 상태를** 사용하는 경우 콜백을 수행할 때 본문에 다음 코드를 추가해야 합니다.

```json
{
    "Output": {
        // output object is used in activity output
        "testProp": "testPropValue"
    },
    "Error": {
        // Optional, set it when you want to fail the activity
        "ErrorCode": "testErrorCode",
        "Message": "error message to show in activity error"
    },
    "StatusCode": "403" // when status code is >=400, activity is marked as failed
}
```

## <a name="next-steps"></a>다음 단계

데이터 팩터리에서 지원하는 다음 제어 흐름 활동을 참조하십시오.

- [If 조건 작업](control-flow-if-condition-activity.md)
- [파이프라인 활동 실행](control-flow-execute-pipeline-activity.md)
- [각 활동에 대해](control-flow-for-each-activity.md)
- [메타데이터 활동 받기](control-flow-get-metadata-activity.md)
- [조회 작업](control-flow-lookup-activity.md)
- [웹 활동](control-flow-web-activity.md)
- [Until 작업](control-flow-until-activity.md)
