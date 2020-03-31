---
title: API 형식 지원의 제한 및 세부 정보
titleSuffix: Azure API Management
description: Azure API 관리에서 Open API, WSDL 및 WADL 형식에 대한 알려진 문제 및 제한 사항에 대한 자세한 내용을 참조하십시오.
services: api-management
documentationcenter: ''
author: vladvino
manager: vlvinogr
editor: ''
ms.assetid: 7a5a63f0-3e72-49d3-a28c-1bb23ab495e2
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/02/2020
ms.author: apimpm
ms.openlocfilehash: 61d43addfdf9008cb7aa8a073dcf3bb702cb55f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76513374"
---
# <a name="api-import-restrictions-and-known-issues"></a>API 가져오기 제한 사항 및 알려진 문제

## <a name="about-this-list"></a>다음 목록 정보

API를 가져올 때 가져오기를 성공적으로 수행하기 전에 수정해야 하는 몇 가지 제한 사항이 나 식별될 수 있습니다. 이 문서에서는 API의 가져오기 형식으로 구성된 이러한 제한 사항을 문서화합니다. 또한 OpenAPI 내보내기의 작동 방식에 대해서도 설명합니다.

## <a name="openapiswagger-import-limitations"></a><a name="open-api"> </a>오픈API/스웨거 수입 제한

OpenAPI 문서를 가져올 때 오류가 표시되면 해당 문서의 유효성을 미리 검사했는지 확인하세요. 유효성 검사를 수행할 때는 Azure Portal에서 디자인 - 프런트 엔드 - OpenAPI 사양 편집기를 선택하여 디자이너를 사용할 수도 있고, <a href="https://editor.swagger.io">Swagger Editor</a> 등의 타사 도구를 사용할 수도 있습니다.

### <a name="general"></a><a name="open-api-general"> </a>일반

-   경로와 쿼리 간에 필수 매개 변수 이름은 고유해야 합니다. OpenAPI에서는 매개 변수 이름이 경로, 쿼리, 헤더 등의 특정 위치 내에서만 고유하면 됩니다. 하지만 API Management에서는 경로 및 쿼리 매개 변수를 기준으로 작업을 구분할 수 있습니다. OpenAPI에서는 이러한 구분이 지원되지 않습니다. 따라서 전체 URL 템플릿 내에서 매개 변수 이름이 고유해야 합니다.
-   `\$ref`포인터는 외부 파일을 참조할 수 없습니다.
-   `x-ms-paths``x-servers` 지원되는 유일한 확장입니다.
-   사용자 지정 확장명은 가져오기 시 무시되며 내보내기용으로 저장되거나 보존되지 않습니다.
-   `Recursion`- API Management는 재귀적으로 정의된 정의(예: 자신을 참조하는 스키마)를 지원하지 않습니다.
-   이 경우에는 원본 파일 URL(사용 가능한 경우)이 상대 서버 URL에 적용됩니다.
-   보안 정의는 무시됩니다.
-   API 작업에 대한 인라인 스키마 정의는 지원되지 않습니다. 스키마 정의는 API 범위에 정의되며 API 작업 요청 또는 응답 범위에서 참조할 수 있습니다.
-   정의된 URL 매개변수는 URL 템플릿의 일부여야 합니다.
-   `Produces`API에서 반환되는 MIME 형식을 설명하는 키워드는 지원되지 않습니다. 

### <a name="openapi-version-2"></a><a name="open-api-v2"> </a>OpenAPI 버전 2

-   JSON 형식만 지원됩니다.

### <a name="openapi-version-3"></a><a name="open-api-v3"> </a>OpenAPI 버전 3

-   많은 `servers` 것이 지정되면 API 관리에서 첫 번째 HTTP URL을 선택하려고 시도합니다. HTTPs URL이 없으면 첫 번째 HTTP URL이 선택됩니다. HTTP URL도 없으면 서버 URL은 비어 있게 됩니다.
-   `Examples`지원되지 는 않지만 `example` 지원되지 않습니다.

## <a name="openapi-import-update-and-export-mechanisms"></a>OpenAPI 가져오기, 업데이트 및 내보내기 메커니즘

### <a name="add-new-api-via-openapi-import"></a>OpenAPI 가져오기를 통해 새 API 추가

OpenAPI 문서에 있는 각 작업에 대해 Azure 리소스 이름 및 표시 이름을 `operationId` `summary` 각각 설정하여 새 작업이 만들어집니다. `operationId`값은 아래에 설명된 규칙에 따라 정규화됩니다. `summary`값은 있는 것으로 가져오고 길이는 300자로 제한됩니다.

지정되지 않은 경우(즉, `operationId` 존재하지 `null`않거나 비어 있음) Azure 리소스 이름 값은 HTTP 메서드 및 경로 `get-foo`템플릿(예: )을 결합하여 생성됩니다.

`summary` 지정되지 않은 경우(즉, 존재하지 `null`않거나 비어 `display name` 있음) 값은 로 `operationId`설정됩니다. `operationId` 지정하지 않으면 HTTP 메서드와 경로 템플릿을 결합하여 표시 이름 값이 `Get - /foo`생성됩니다( 예: .

### <a name="update-an-existing-api-via-openapi-import"></a>OpenAPI 가져오기를 통해 기존 API 업데이트

가져오는 동안 기존 API는 OpenAPI 문서에 설명된 API와 일치하도록 변경됩니다. OpenAPI 문서의 각 작업은 해당 `operationId` 값을 기존 작업의 Azure 리소스 이름과 비교하여 기존 작업과 일치합니다.

일치하는 항목이 발견되면 기존 작업의 속성이 "현재" 업데이트됩니다.

일치하는 일치를 찾지 못하면 위의 섹션에 설명된 규칙을 사용하여 새 작업이 만들어집니다. 각 새 작업에 대해 가져오기는 동일한 HTTP 메서드 및 경로 템플릿을 사용하여 기존 작업에서 정책을 복사하려고 시도합니다.

기존의 일치하지 않는 모든 작업이 삭제됩니다.

가져오기를 보다 예측 가능하게 만들려면 다음 지침을 따르십시오.

- 모든 작업에 `operationId` 대한 속성을 지정해야 합니다.
- 초기 가져오기 `operationId` 후에는 변경하지 않습니다.
- 변경 `operationId` 하지 마십시오 및 HTTP 메서드 또는 경로 템플릿을 동시에.

### <a name="export-api-as-openapi"></a>API를 OpenAPI로 내보내기

각 작업에 대해 Azure 리소스 이름이 `operationId`로 내보내지고 표시 이름이 `summary`로 내보내집니다.
작업에 대한 정규화 규칙Id

- 소문자로 변환합니다.
- 예를 들어 `GET-/foo/{bar}?buzz={quix}` 영숫자가 아닌 문자의 각 시퀀스를 단일 대시로 `get-foo-bar-buzz-quix-`바꿉니다.
- 예를 들어, 양쪽에 대시를 `get-foo-bar-buzz-quix-` 트림하면`get-foo-bar-buzz-quix`
- 76자, 리소스 이름에 대한 최대 제한보다 작은 4자에 맞게 잘립니다.
- 중복 제거 접미사에 나머지 네 문자를 사용 하 여 필요한 `-1, -2, ..., -999`경우 의 형태로 .


## <a name="wsdl"></a><a name="wsdl"> </a>WSDL

WSDL 파일은 SOAP 통과 및 SOAP-REST API를 만드는 데 사용됩니다.

-   **SOAP 바인딩** - 스타일 "document" 및 "literal" 인코딩의 SOAP 바인딩만 사용할 수 있습니다. "rpc" 스타일 또는 SOAP 인코딩은 지원되지 않습니다.
-   **WSDL:Import** - 이 특성은 지원되지 않습니다. 고객은 가져오기를 문서 하나로 병합해야 합니다.
-   **여러 부분으로 된 메시지** - 이러한 유형의 메시지는 지원되지 않습니다.
-   **WCF wsHttpBinding** - Windows Communication Foundation을 사용하여 만든 SOAP 서비스는 basicHttpBinding을 사용해야 합니다. wsHttpBinding은 지원되지 않습니다.
-   **MTOM** - MTOM을 사용한 서비스는 <em>작동할 수 있습니다</em>. 현재는 공식적으로 지원되지 않습니다.
-   **재귀** - 재귀적으로 정의된(예: 자체의 배열을 참조) 형식은 APIM에서 지원되지 않습니다.
-   **여러 네임스페이스** - 하나의 스키마에서 여러 네임스페이스를 사용할 수 있지만 메시지 파트를 정의하는 데는 대상 네임스페이스만 사용할 수 있습니다. 다른 입력 또는 출력 요소를 정의하는 데 사용되는 대상 이외의 네임스페이스는 보존되지 않습니다. 이러한 WSDL 문서를 가져올 수 있지만 내보내기 시 모든 메시지 파트의 대상 네임스페이스는 WSDL입니다.
-   **배열** - SOAP-REST 변환은 아래 예제에 표시된 래핑된 배열만 지원합니다.

```xml
    <complexType name="arrayTypeName">
        <sequence>
            <element name="arrayElementValue" type="arrayElementType" minOccurs="0" maxOccurs="unbounded"/>
        </sequence>
    </complexType>
    <complexType name="typeName">
        <sequence>
            <element name="element1" type="someTypeName" minOccurs="1" maxOccurs="1"/>
            <element name="element2" type="someOtherTypeName" minOccurs="0" maxOccurs="1" nillable="true"/>
            <element name="arrayElement" type="arrayTypeName" minOccurs="1" maxOccurs="1"/>
        </sequence>
    </complexType>
```

## <a name="wadl"></a><a name="wadl"> </a>WADL

현재, 알려진 WADL 가져오기 문제가 없습니다.
