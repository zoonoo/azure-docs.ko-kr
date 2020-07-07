---
title: Azure Data Factory의 Webhook 활동
description: 웹 후크 작업은 사용자가 지정 된 특정 조건에 따라 연결 된 데이터 집합의 유효성을 검사할 때까지 파이프라인의 실행을 계속 하지 않습니다.
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
ms.openlocfilehash: 4056550ae0a71138d136878fc7e3aa5f6f8f4180
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "81417881"
---
# <a name="webhook-activity-in-azure-data-factory"></a>Azure Data Factory의 Webhook 활동

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

웹 후크 작업은 사용자 지정 코드를 통해 파이프라인의 실행을 제어할 수 있습니다. 웹 후크 활동을 사용 하 여 고객 코드는 끝점을 호출 하 고 콜백 URL을 전달할 수 있습니다. 파이프라인 실행은 다음 작업을 진행 하기 전에 콜백 호출을 기다립니다.

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

속성 | Description | 허용되는 값 | 필수
-------- | ----------- | -------------- | --------
**name** | Webhook 활동의 이름입니다. | String | 예 |
**type** | "WebHook"로 설정 해야 합니다. | String | 예 |
**방법이** | 대상 끝점에 대 한 REST API 메서드입니다. | 문자열입니다. 지원 되는 형식은 "POST"입니다. | 예 |
**url** | 대상 끝점과 경로입니다. | 문자열 또는 문자열의 **resultType** 값이 포함 된 식입니다. | 예 |
**머리글과** | 요청에 전송되는 헤더입니다. 요청에 대 한 언어 및 형식을 설정 하는 예제는 다음과 같습니다 `"headers" : { "Accept-Language": "en-us", "Content-Type": "application/json" }` . | 문자열 또는 문자열의 **resultType** 값이 포함 된 식입니다. | 예. `Content-Type`같은 헤더가 `"headers":{ "Content-Type":"application/json"}` 필요 합니다. |
**body** | 엔드포인트에 전송된 페이로드를 나타냅니다. | 유효한 JSON 또는 **resultType** 값이 json 인 식입니다. 요청 페이로드의 스키마에 대 한 [요청 페이로드 스키마](https://docs.microsoft.com/azure/data-factory/control-flow-web-activity#request-payload-schema) 를 참조 하세요. | 예 |
**인증은** | 끝점을 호출 하는 데 사용 되는 인증 방법입니다. 지원 되는 형식은 "Basic" 및 "ClientCertificate"입니다. 자세한 내용은 [인증](https://docs.microsoft.com/azure/data-factory/control-flow-web-activity#authentication)을 참조하세요. 인증이 필요 하지 않은 경우이 속성을 제외 합니다. | 문자열 또는 문자열의 **resultType** 값이 포함 된 식입니다. | 아니요 |
**timeout** | 작업에서 **Callbackuri** 로 지정 된 콜백이 호출 될 때까지 대기 하는 시간입니다. 기본값은 10 분 ("00:10:00")입니다. 값의 TimeSpan 형식은 *d*입니다. *hh*:*mm*:*ss*. | String | 아니요 |
**콜백에 대 한 보고서 상태** | 사용자가 webhook 활동의 실패 상태를 보고할 수 있습니다. | 부울 | 아니요 |

## <a name="authentication"></a>인증

Webhook 활동은 다음 인증 유형을 지원 합니다.

### <a name="none"></a>None

인증이 필요 하지 않은 경우에는 **authentication** 속성을 포함 하지 마세요.

### <a name="basic"></a>Basic

기본 인증에 사용할 사용자 이름 및 암호를 지정 합니다.

```json
"authentication":{
   "type":"Basic",
   "username":"****",
   "password":"****"
}
```

### <a name="client-certificate"></a>클라이언트 인증서

PFX 파일의 Base64 인코딩 콘텐츠 및 암호를 지정 합니다.

```json
"authentication":{
   "type":"ClientCertificate",
   "pfx":"****",
   "password":"****"
}
```

### <a name="managed-identity"></a>관리 ID

데이터 팩터리의 관리 되는 id를 사용 하 여 액세스 토큰을 요청 하는 리소스 URI를 지정 합니다. Azure Resource 관리 API를 호출하려면 `https://management.azure.com/`을 사용합니다. 관리 id의 작동 방식에 대 한 자세한 내용은 [Azure 리소스에 대 한 관리 되는 id 개요](/azure/active-directory/managed-identities-azure-resources/overview)를 참조 하세요.

```json
"authentication": {
    "type": "MSI",
    "resource": "https://management.azure.com/"
}
```

> [!NOTE]
> 데이터 팩터리가 Git 리포지토리로 구성 된 경우 기본 또는 클라이언트 인증서 인증을 사용 하도록 Azure Key Vault에 자격 증명을 저장 해야 합니다. Azure Data Factory는 Git에 암호를 저장 하지 않습니다.

## <a name="additional-notes"></a>추가적인 참고 사항

Data Factory는 URL 끝점으로 전송 된 본문에서 추가 속성 **Callbackuri** 를 전달 합니다. Data Factory 지정 된 시간 제한 값 이전에이 URI를 호출할 것으로 예상 합니다. URI가 호출 되지 않은 경우 작업은 "TimedOut" 상태와 함께 실패 합니다.

사용자 지정 끝점에 대 한 호출이 실패할 경우 webhook 작업이 실패 합니다. 모든 오류 메시지를 콜백 본문에 추가 하 고 이후 작업에서 사용할 수 있습니다.

모든 REST API 호출에 대해 끝점이 1 분 이내에 응답 하지 않으면 클라이언트 시간이 초과 됩니다. 이 동작은 표준 HTTP 모범 사례입니다. 이 문제를 해결 하려면 202 패턴을 구현 합니다. 현재 경우 끝점이 202 (수락 됨)를 반환 하 고 클라이언트에서 폴링합니다.

요청에 대 한 1 분 제한 시간은 작업 시간 제한에 대해 수행할 작업이 없습니다. 후자는 **Callbackuri**로 지정 된 콜백을 대기 하는 데 사용 됩니다.

콜백 URI로 다시 전달 되는 본문은 유효한 JSON 이어야 합니다. `Content-Type` 헤더를 `application/json`으로 설정합니다.

**콜백 속성의 보고서 상태** 를 사용 하는 경우 콜백을 수행할 때 본문에 다음 코드를 추가 해야 합니다.

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

Data Factory에서 지 원하는 다음 제어 흐름 작업을 참조 하세요.

- [If 조건 작업](control-flow-if-condition-activity.md)
- [파이프라인 작업 실행](control-flow-execute-pipeline-activity.md)
- [ForEach 작업](control-flow-for-each-activity.md)
- [메타데이터 작업 가져오기](control-flow-get-metadata-activity.md)
- [조회 작업](control-flow-lookup-activity.md)
- [웹 작업](control-flow-web-activity.md)
- [Until 작업](control-flow-until-activity.md)
