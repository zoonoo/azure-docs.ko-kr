---
title: Azure Data Factory의 웹후크 작업
description: 웹후크 작업은 사용자가 지정한 특정 조건에 따라 연결된 데이터 세트의 유효성이 검사될 때까지 파이프라인을 계속 실행하지 않습니다.
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/25/2019
ms.openlocfilehash: 4c3ff5d7139f4167769f78aa858c7d7a693539a3
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104785940"
---
# <a name="webhook-activity-in-azure-data-factory"></a>Azure Data Factory의 웹후크 작업

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

웹후크 작업은 사용자 지정 코드를 통해 파이프라인의 실행을 제어할 수 있습니다. 웹후크 작업을 사용하면 고객의 코드는 엔드포인트를 호출하고 엔드포인트에 콜백 URL을 전달할 수 있습니다. 파이프라인 실행은 다음 작업을 진행하기 전에 콜백이 호출될 때까지 기다립니다.

> [!IMPORTANT]
> 웹후크 작업을 사용하면 이제 오류 상태와 사용자 지정 메시지를 작업 및 파이프라인에 다시 표시할 수 있습니다. _reportStatusOnCallBack_ 을 true로 설정하고 콜백 페이로드에 _StatusCode_ 및 _Error_ 를 포함합니다. 자세한 내용은 [참고 사항](#additional-notes) 섹션을 참조하세요.

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
        "reportStatusOnCallBack": false,
        "authentication": {
            "type": "ClientCertificate",
            "pfx": "****",
            "password": "****"
        }
    }
}

```

## <a name="type-properties"></a>형식 속성

속성 | Description | 허용되는 값 | 필수
-------- | ----------- | -------------- | --------
**name** | 웹후크 작업의 이름입니다. | String | 예 |
**type** | “WebHook”로 설정해야 합니다. | String | 예 |
**method** | 대상 엔드포인트에 대한 REST API 메서드입니다. | 문자열입니다. 지원되는 형식은 “POST”입니다. | 예 |
**url** | 대상 엔드포인트 및 경로입니다. | 문자열 또는 문자열의 **resultType** 값이 포함된 식입니다. | 예 |
**headers** | 요청에 전송되는 헤더입니다. 요청에 언어 및 형식을 설정하려면 `"headers" : { "Accept-Language": "en-us", "Content-Type": "application/json" }`과 같이 합니다. | 문자열 또는 문자열의 **resultType** 값이 포함된 식입니다. | 예. `"headers":{ "Content-Type":"application/json"}`와 같은 `Content-Type` 헤더가 필요합니다. |
**body** | 엔드포인트에 전송된 페이로드를 나타냅니다. | 유효한 JSON 또는 JSON의 **resultType** 값이 포함된 식입니다. 요청 페이로드의 스키마는 [요청 페이로드 스키마](./control-flow-web-activity.md#request-payload-schema)를 참조하세요. | 예 |
**인증** | 엔드포인트를 호출하는 데 사용되는 인증 방법입니다. 지원되는 형식은 “Basic” 및 “ClientCertificate”입니다. 자세한 내용은 [인증](./control-flow-web-activity.md#authentication)을 참조하세요. 인증이 필요 없는 경우 이 속성을 제외합니다. | 문자열 또는 문자열의 **resultType** 값이 포함된 식입니다. | 예 |
**timeout** | 작업이 **callBackUri** 로 지정된 콜백이 호출될 때까지 대기하는 시간입니다. 기본값은 10분(“00:10:00”)입니다. 값은 *d*.*hh*:*mm*:*ss* 의 TimeSpan 형식을 가집니다. | String | 예 |
**콜백에 대한 보고서 상태** | 사용자가 웹후크 작업의 실패 상태를 보고할 수 있습니다. | 부울 | 예 |

## <a name="authentication"></a>인증

웹후크 작업은 다음 인증 유형을 지원합니다.

### <a name="none"></a>없음

인증이 필요 없는 경우 **authentication** 속성을 포함하지 않습니다.

### <a name="basic"></a>Basic

기본 인증에 사용할 사용자 이름 및 암호를 지정합니다.

```json
"authentication":{
   "type":"Basic",
   "username":"****",
   "password":"****"
}
```

### <a name="client-certificate"></a>클라이언트 인증서

PFX 파일의 Base64로 인코딩된 콘텐츠 및 암호를 지정합니다.

```json
"authentication":{
   "type":"ClientCertificate",
   "pfx":"****",
   "password":"****"
}
```

### <a name="managed-identity"></a>관리 ID

데이터 팩터리에 대한 관리 ID를 사용하여 요청되는 액세스 토큰에 대한 리소스 URI를 지정합니다. Azure Resource 관리 API를 호출하려면 `https://management.azure.com/`을 사용합니다. 관리 ID의 작동 방식에 대한 자세한 내용은 [Azure 리소스의 관리 ID 개요](../active-directory/managed-identities-azure-resources/overview.md)를 참조하세요.

```json
"authentication": {
    "type": "MSI",
    "resource": "https://management.azure.com/"
}
```

> [!NOTE]
> 데이터 팩터리가 Git 리포지토리로 구성된 경우, 기본 또는 클라이언트 인증서 인증을 사용하기 위해 Azure Key Vault에 자격 증명을 저장해야 합니다. Azure Data Factory는 Git에 암호를 저장하지 않습니다.

## <a name="additional-notes"></a>추가적인 참고 사항

Data Factory는 URL 엔드포인트에 보낸 본문에서 추가 속성 **callBackUri** 를 전달합니다. Data Factory는 지정된 시간 제한 값 전에 해당 URI가 호출될 것으로 예상합니다. URI가 호출되지 않은 경우 작업은 “TimedOut” 상태와 함께 실패합니다.

사용자 지정 엔드포인트에 대한 호출이 실패할 경우 웹후크 작업이 실패합니다. 모든 오류 메시지를 콜백 본문에 추가하고 이후 작업에서 사용할 수 있습니다.

모든 REST API 호출의 경우 엔드포인트가 1분 이내에 응답하지 않으면 클라이언트 시간이 초과합니다. 이 동작은 표준 HTTP 모범 사례입니다. 이 문제를 해결하려면 202 패턴을 구현합니다. 현재의 경우 엔드포인트는 202(수락됨)를 반환하고 클라이언트에서 폴링합니다.

요청의 1분 시간 제한은 작업의 시간 제한과는 관계가 없습니다. 후자는 **callbackUri** 로 지정된 콜백을 기다리는 데 사용됩니다.

콜백 URI로 다시 전달되는 본문은 유효한 JSON이어야 합니다. `Content-Type` 헤더를 `application/json`으로 설정합니다.

**콜백에 대한 보고서 상태** 속성을 사용하는 경우 콜백을 수행할 때 본문에 다음 코드를 추가해야 합니다.

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

Data Factory에서 지원하는 다음 제어 흐름 작업을 확인하세요.

- [If 조건 작업](control-flow-if-condition-activity.md)
- [파이프라인 작업 실행](control-flow-execute-pipeline-activity.md)
- [ForEach 작업](control-flow-for-each-activity.md)
- [메타데이터 작업 가져오기](control-flow-get-metadata-activity.md)
- [조회 작업](control-flow-lookup-activity.md)
- [웹 작업](control-flow-web-activity.md)
- [Until 작업](control-flow-until-activity.md)