---
title: Azure Data Factory를 사용 하 여 REST 원본에서 데이터 복사
description: Azure Data Factory 파이프라인의 복사 작업을 사용하여 클라우드 또는 온-프레미스 REST 원본에서 지원되는 싱크 데이터 저장소로 데이터를 복사하는 방법에 대해 알아봅니다.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: jingwang
ms.openlocfilehash: 6bb597ab49050c2bb365379cfac44f4b4d176af1
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73680400"
---
# <a name="copy-data-from-a-rest-endpoint-by-using-azure-data-factory"></a>Azure Data Factory를 사용하여 REST 엔드포인트에서 데이터 복사

이 문서에서는 Azure Data Factory의 복사 작업을 사용하여 REST 엔드포인트에서 데이터를 복사하는 방법을 간략히 설명합니다. 이 문서는 복사 작업에 대한 일반적인 개요를 제공하는 [Azure Data Factory의 복사 작업](copy-activity-overview.md)을 기반으로 합니다.

이 REST 커넥터와 [HTTP 커넥터](connector-http.md)와 [웹 테이블 커넥터](connector-web-table.md)의 차이점은 다음과 같습니다.

- **REST 커넥터** 는 RESTful api에서 데이터를 복사 하는 것을 구체적으로 지원 합니다. 
- **HTTP 커넥터**는 일반적으로 모든 HTTP 엔드포인트에서 데이터를 검색합니다(예: 파일 다운로드). 이 REST 커넥터를 사용할 수 있게 되기 전에는 HTTP 커넥터를 사용하여 지원은 되지만 REST 커넥터와 비교할 때 기능이 적은 RESTful API에서 데이터를 복사할 수도 있습니다.
- **웹 테이블 커넥터**는 HTML 웹 페이지에서 테이블 콘텐츠를 추출합니다.

## <a name="supported-capabilities"></a>지원되는 기능

REST 원본에서 지원되는 모든 싱크 데이터 저장소로 데이터를 복사할 수 있습니다. 복사 작업에서 원본 및 싱크로 지원되는 데이터 저장소의 목록은 [지원되는 데이터 저장소 및 형식](copy-activity-overview.md#supported-data-stores-and-formats)을 참조하세요.

특히 이 일반 REST 커넥터는 다음을 지원합니다.

- **GET** 또는 **POST** 메서드를 사용하여 REST 엔드포인트에서 데이터 검색
- 다음 인증 중 하나를 사용 하 여 데이터 검색: **익명**, **기본**, **AAD 서비스 사용자**및 **Azure 리소스에 대 한 관리 id**입니다.
- REST API의 **[페이지 매김](#pagination-support)**
- REST JSON 응답을 [있는 그대로](#export-json-response-as-is) 복사하고 [스키마 매핑](copy-activity-schema-and-type-mapping.md#schema-mapping)을 사용하여 붙여넣기 **JSON**의 응답 페이로드만 지원됩니다.

> [!TIP]
> Data Factory에서 REST 커넥터를 구성하기 전에 데이터 검색을 위한 요청을 테스트하려면 헤더 및 본문 요구 사항의 API 사양을 알아봅니다. Postman 또는 웹 브라우저와 같은 도구를 사용하여 유효성을 검사할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>시작

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

다음 섹션에서는 REST 커넥터에 한정된 Data Factory 엔터티를 정의하는 데 사용되는 속성에 대해 자세히 설명합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

REST 연결된 서비스에 다음 속성이 지원됩니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | **Type** 속성은 **RestService**로 설정 해야 합니다. | 예 |
| URL | REST 서비스의 기본 URL입니다. | 예 |
| enableServerCertificateValidation | 끝점에 연결할 때 서버 쪽 SSL 인증서의 유효성을 검사할지 여부를 나타냅니다. | 아니요<br /> (기본값: **true**) |
| authenticationType | REST 서비스에 연결하는 데 사용되는 인증 형식입니다. 허용되는 값은 **Anonymous**, **Basic**, **AadServicePrincipal** 및 **ManagedServiceIdentity**입니다. 추가 속성 및 예제를 보려면 아래 해당 섹션을 참조하세요. | 예 |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [통합 런타임](concepts-integration-runtime.md)입니다. [전제 조건](#prerequisites) 섹션에서 자세히 알아보세요. 지정하지 않으면 이 속성은 기본 Azure Integration Runtime을 사용합니다. |아니요 |

### <a name="use-basic-authentication"></a>기본 인증 사용

**authenticationType** 속성을 **Basic**으로 설정합니다. 앞 섹션에서 설명한 일반 속성 외에 다음 속성을 지정합니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| userName | REST 엔드포인트에 액세스하는 데 사용할 사용자 이름입니다. | 예 |
| password | 사용자(**userName** 값)의 암호입니다. 이 필드를 **SecureString** 형식으로 표시하여 Data Factory에서 안전하게 저장합니다. [Azure Key Vault에 저장된 비밀을 참조](store-credentials-in-key-vault.md)할 수도 있습니다. | 예 |

**예제**

```json
{
    "name": "RESTLinkedService",
    "properties": {
        "type": "RestService",
        "typeProperties": {
            "authenticationType": "Basic",
            "url" : "<REST endpoint>",
            "userName": "<user name>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="use-aad-service-principal-authentication"></a>AAD 서비스 주체 인증 사용

**authenticationType** 속성을 **AadServicePrincipal**로 설정합니다. 앞 섹션에서 설명한 일반 속성 외에 다음 속성을 지정합니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| servicePrincipalId | Azure Active Directory 애플리케이션의 클라이언트 ID를 지정합니다. | 예 |
| servicePrincipalKey | Azure Active Directory 애플리케이션의 키를 지정합니다. 이 필드를 **SecureString**으로 표시하여 Data Factory에 안전하게 저장하거나, [Azure Key Vault에 저장된 비밀을 참조](store-credentials-in-key-vault.md)합니다. | 예 |
| 테넌트 | 애플리케이션이 있는 테넌트 정보(도메인 이름 또는 테넌트 ID)를 지정합니다. Azure Portal의 오른쪽 위 모서리를 마우스로 가리켜 검색합니다. | 예 |
| aadResourceId | 권한 부여를 요청하는 AAD 리소스(예: `https://management.core.windows.net`)를 지정합니다.| 예 |

**예제**

```json
{
    "name": "RESTLinkedService",
    "properties": {
        "type": "RestService",
        "typeProperties": {
            "url": "<REST endpoint e.g. https://www.example.com/>",
            "authenticationType": "AadServicePrincipal",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "value": "<service principal key>",
                "type": "SecureString"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "aadResourceId": "<AAD resource URL e.g. https://management.core.windows.net>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="managed-identity"></a> Azure 리소스 인증에 관리 ID 사용

**authenticationType** 속성을 **ManagedServiceIdentity**로 설정합니다. 앞 섹션에서 설명한 일반 속성 외에 다음 속성을 지정합니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| aadResourceId | 권한 부여를 요청하는 AAD 리소스(예: `https://management.core.windows.net`)를 지정합니다.| 예 |

**예제**

```json
{
    "name": "RESTLinkedService",
    "properties": {
        "type": "RestService",
        "typeProperties": {
            "url": "<REST endpoint e.g. https://www.example.com/>",
            "authenticationType": "ManagedServiceIdentity",
            "aadResourceId": "<AAD resource URL e.g. https://management.core.windows.net>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>데이터 세트 속성

이 섹션에서는 REST 데이터 세트에서 지원하는 속성의 목록을 제공합니다. 

데이터 세트 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 세트 및 연결된 서비스](concepts-datasets-linked-services.md)를 참조하세요. 

REST의 데이터를 복사하려는 경우 다음과 같은 속성이 지원됩니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 데이터 세트의 **type** 속성을 **RestResource**로 설정해야 합니다. | 예 |
| relativeUrl | 데이터를 포함하는 리소스에 대한 상대 URL입니다. 이 속성을 지정하지 않으면 연결된 서비스 정의에 지정된 URL만 사용됩니다. | 아니요 |

데이터 집합에서 `requestMethod`, `additionalHeaders`, `requestBody` 및 `paginationRules`를 설정 하는 경우에는 그대로 계속 지원 되지만 작업 원본에서 새 모델을 사용 하는 것이 좋습니다.

**예제:**

```json
{
    "name": "RESTDataset",
    "properties": {
        "type": "RestResource",
        "typeProperties": {
            "relativeUrl": "<relative url>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<REST linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>복사 활동 속성

이 섹션에서는 REST 원본에서 지원하는 속성의 목록을 제공합니다.

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md)을 참조하세요. 

### <a name="rest-as-source"></a>REST를 원본으로

복사 작업 **source** 섹션에서 다음 속성이 지원됩니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 복사 작업 원본의 **type** 속성은 **RestSource**로 설정해야 합니다. | 예 |
| requestMethod | HTTP 메서드입니다. 허용되는 값은 **Get**(기본값) 또는 **Post**입니다. | 아니요 |
| additionalHeaders | 추가 HTTP 요청 헤더입니다. | 아니요 |
| requestBody | HTTP 요청의 본문입니다. | 아니요 |
| paginationRules | 다음 페이지 요청을 작성하기 위한 페이지 매김 규칙입니다. 자세한 내용은 [페이지 매김 지원](#pagination-support)을 참조하세요. | 아니요 |
| httpRequestTimeout | HTTP 요청이 응답을 받을 시간 제한(**TimeSpan** 값)입니다. 이 값은 응답 데이터를 읽는 시간 제한이 아니라, 응답을 받을 시간 제한입니다. 기본값은 **00:01:40**입니다.  | 아니요 |
| requestInterval | 다음 페이지에 대한 요청을 보내기 전에 대기할 시간입니다. 기본값은 **00:00:01**입니다. |  아니요 |

**예제 1: 페이지 매김을 사용 하 여 Get 메서드 사용**

```json
"activities":[
    {
        "name": "CopyFromREST",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<REST input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "RestSource",
                "additionalHeaders": {
                    "x-user-defined": "helloworld"
                },
                "paginationRules": {
                    "AbsoluteUrl": "$.paging.next"
                },
                "httpRequestTimeout": "00:01:00"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

**예제 2: POST 메서드 사용**

```json
"activities":[
    {
        "name": "CopyFromREST",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<REST input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "RestSource",
                "requestMethod": "Post",
                "requestBody": "<body for POST REST request>",
                "httpRequestTimeout": "00:01:00"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="pagination-support"></a>페이지 매김 지원

일반적으로 REST API은 적절 한 수의 단일 요청에 대 한 응답 페이로드 크기를 제한 합니다. 많은 양의 데이터를 반환 하는 경우 결과를 여러 페이지로 분할 하 고 호출자가 다음 결과 페이지를 가져오기 위해 연속 요청을 보내야 합니다. 보통 한 페이지에 대한 요청은 동적이고 이전 페이지의 응답에서 반환된 정보로 구성됩니다.

이 일반 REST 커넥터는 다음 페이지 매김 패턴을 지원합니다. 

* 현재 응답 본문의 다음 요청 절대 또는 상대 URL = 속성 값
* 현재 응답 헤더의 다음 요청 절대 또는 상대 URL = 헤더 값
* 다음 요청의 쿼리 매개 변수 = 현재 응답 본문의 속성 값
* 다음 요청의 쿼리 매개 변수 = 현재 응답 헤더의 헤더 값
* 다음 요청의 헤더 = 현재 응답 본문의 속성 값
* 다음 요청의 헤더 = 현재 응답 헤더의 헤더 값

**페이지 매김 규칙** 은 대/소문자를 구분 하는 키-값 쌍이 하나 이상 포함 된 데이터 집합의 사전으로 정의 됩니다. 이 구성은 두 번째 페이지에서 시작하는 요청을 생성하는 데 사용됩니다. 커넥터는 HTTP 상태 코드 204 (콘텐츠 없음)가 발생 하거나 "paginationRules"의 JSONPath 식이 null을 반환 하는 경우 반복을 중지 합니다.

페이지 매김 규칙의 **지원되는 키**는 다음과 같습니다.

| 키 | 설명 |
|:--- |:--- |
| AbsoluteUrl | 다음 요청을 실행할 URL을 나타냅니다. **절대 url 또는 상대 url**일 수 있습니다. |
| QueryParameters.*request_query_parameter* OR QueryParameters['request_query_parameter'] | "request_query_parameter"는 다음 HTTP 요청 URL에 있는 하나의 쿼리 매개 변수 이름을 참조하는 사용자 정의 항목입니다. |
| Headers.*request_header* OR Headers['request_header'] | "request_header"는 다음 HTTP 요청에 있는 하나의 헤더 이름을 참조하는 사용자 정의 항목입니다. |

페이지 매김 규칙의 **지원되는 값**은 다음과 같습니다.

| 값 | 설명 |
|:--- |:--- |
| Headers.*response_header* OR Headers['response_header'] | "response_header"는 현재 HTTP 요청에 있는 하나의 헤더 이름을 참조하는 사용자 정의 항목으로, 다음 요청을 실행하는 데 해당 값이 사용됩니다. |
| "$"(응답 본문의 루트를 나타냄)로 시작하는 JSONPath 식 | 응답 본문은 JSON 개체를 하나만 포함해야 합니다. JSONPath 식은 다음 요청을 실행하는 데 사용되는 단일 기본 값을 반환해야 합니다. |

**예제:**

Facebook Graph API는 다음 구조의 응답을 반환합니다. 이 경우 다음 페이지의 URL은 ***paging.next***에 표시됩니다.

```json
{
    "data": [
        {
            "created_time": "2017-12-12T14:12:20+0000",
            "name": "album1",
            "id": "1809938745705498_1809939942372045"
        },
        {
            "created_time": "2017-12-12T14:14:03+0000",
            "name": "album2",
            "id": "1809938745705498_1809941802371859"
        },
        {
            "created_time": "2017-12-12T14:14:11+0000",
            "name": "album3",
            "id": "1809938745705498_1809941879038518"
        }
    ],
    "paging": {
        "cursors": {
            "after": "MTAxNTExOTQ1MjAwNzI5NDE=",
            "before": "NDMyNzQyODI3OTQw"
        },
        "previous": "https://graph.facebook.com/me/albums?limit=25&before=NDMyNzQyODI3OTQw",
        "next": "https://graph.facebook.com/me/albums?limit=25&after=MTAxNTExOTQ1MjAwNzI5NDE="
    }
}
```

해당 REST 복사 활동 원본 구성은 특히 `paginationRules` 다음과 같습니다.

```json
"typeProperties": {
    "source": {
        "type": "RestSource",
        "paginationRules": {
            "AbsoluteUrl": "$.paging.next"
        },
        ...
    },
    "sink": {
        "type": "<sink type>"
    }
}
```

## <a name="export-json-response-as-is"></a>JSON 응답을 있는 그대로 내보내기

이 REST 커넥터를 사용하여 REST API JSON 응답을 다양한 파일 기반 저장소에 있는 그대로 내보낼 수 있습니다. 이러한 스키마 독립적 복사를 완수하려면 데이터 세트 및 복사 작업의 스키마 매핑에서 "structure"(*스키마*라고도 함) 섹션을 건너뜁니다.

## <a name="schema-mapping"></a>스키마 매핑

데이터를 REST 엔드포인트에서 테이블 형식 싱크로 복사하려면 [스키마 매핑](copy-activity-schema-and-type-mapping.md#schema-mapping)을 참조하세요.

## <a name="next-steps"></a>다음 단계

Azure Data Factory의 복사 작업에서 원본 및 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소 및 형식](copy-activity-overview.md#supported-data-stores-and-formats)을 참조하세요.
