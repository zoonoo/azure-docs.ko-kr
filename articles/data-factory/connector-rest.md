---
title: Azure 데이터 팩터리를 사용하여 REST 원본의 데이터 복사
description: Azure Data Factory 파이프라인의 복사 작업을 사용하여 클라우드 또는 온-프레미스 REST 원본에서 지원되는 싱크 데이터 저장소로 데이터를 복사하는 방법에 대해 알아봅니다.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 11/20/2019
ms.author: jingwang
ms.openlocfilehash: 3e0dd6e0bb81aef340dc83288e6e5c0af0bf11c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75867365"
---
# <a name="copy-data-from-a-rest-endpoint-by-using-azure-data-factory"></a>Azure Data Factory를 사용하여 REST 엔드포인트에서 데이터 복사

이 문서에서는 Azure Data Factory의 복사 작업을 사용하여 REST 엔드포인트에서 데이터를 복사하는 방법을 간략히 설명합니다. 이 문서는 복사 작업에 대한 일반적인 개요를 제공하는 [Azure Data Factory의 복사 작업](copy-activity-overview.md)을 기반으로 합니다.

이 REST 커넥터와 [HTTP 커넥터](connector-http.md)와 [웹 테이블 커넥터](connector-web-table.md)의 차이점은 다음과 같습니다.

- **REST 커넥터는** 특히 RESTful API에서 데이터 복사를 지원합니다. 
- **HTTP 커넥터**는 일반적으로 모든 HTTP 엔드포인트에서 데이터를 검색합니다(예: 파일 다운로드). 이 REST 커넥터를 사용할 수 있게 되기 전에는 HTTP 커넥터를 사용하여 지원은 되지만 REST 커넥터와 비교할 때 기능이 적은 RESTful API에서 데이터를 복사할 수도 있습니다.
- **웹 테이블 커넥터**는 HTML 웹 페이지에서 테이블 콘텐츠를 추출합니다.

## <a name="supported-capabilities"></a>지원되는 기능

REST 원본에서 지원되는 모든 싱크 데이터 저장소로 데이터를 복사할 수 있습니다. 복사 작업에서 원본 및 싱크로 지원되는 데이터 저장소의 목록은 [지원되는 데이터 저장소 및 형식](copy-activity-overview.md#supported-data-stores-and-formats)을 참조하세요.

특히 이 일반 REST 커넥터는 다음을 지원합니다.

- **GET** 또는 **POST** 메서드를 사용하여 REST 엔드포인트에서 데이터 검색
- 다음 인증 중 하나를 사용하여 데이터를 검색합니다: **익명,** **기본,** **AAD 서비스 주체**및 **Azure 리소스에 대한 관리되는 ID.**
- REST API의 **[페이지 매김](#pagination-support)**
- REST JSON 응답을 [있는 그대로](#export-json-response-as-is) 복사하고 [스키마 매핑](copy-activity-schema-and-type-mapping.md#schema-mapping)을 사용하여 붙여넣기 **JSON**의 응답 페이로드만 지원됩니다.

> [!TIP]
> Data Factory에서 REST 커넥터를 구성하기 전에 데이터 검색을 위한 요청을 테스트하려면 헤더 및 본문 요구 사항의 API 사양을 알아봅니다. Postman 또는 웹 브라우저와 같은 도구를 사용하여 유효성을 검사할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>시작

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

다음 섹션에서는 REST 커넥터에 한정된 Data Factory 엔터티를 정의하는 데 사용되는 속성에 대해 자세히 설명합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

REST 연결된 서비스에 다음 속성이 지원됩니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | **형식** 속성은 **RestService**로 설정해야 합니다. | yes |
| url | REST 서비스의 기본 URL입니다. | yes |
| enableServerCertificateValidation | 끝점에 연결할 때 서버 측 SSL 인증서의 유효성을 검사할지 여부입니다. | 예<br /> (기본값은 **true)** |
| authenticationType | REST 서비스에 연결하는 데 사용되는 인증 형식입니다. 허용되는 값은 **Anonymous**, **Basic**, **AadServicePrincipal** 및 **ManagedServiceIdentity**입니다. 추가 속성 및 예제를 보려면 아래 해당 섹션을 참조하세요. | yes |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [통합 런타임](concepts-integration-runtime.md)입니다. [필수 구성 조건](#prerequisites) 섹션에서 자세히 알아보십시오. 지정하지 않으면 이 속성은 기본 Azure Integration Runtime을 사용합니다. |예 |

### <a name="use-basic-authentication"></a>기본 인증 사용

**authenticationType** 속성을 **Basic**으로 설정합니다. 앞 섹션에서 설명한 일반 속성 외에 다음 속성을 지정합니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| userName | REST 엔드포인트에 액세스하는 데 사용할 사용자 이름입니다. | yes |
| password | 사용자(**userName** 값)의 암호입니다. 이 필드를 **SecureString** 형식으로 표시하여 Data Factory에서 안전하게 저장합니다. [Azure Key Vault에 저장된 비밀을 참조](store-credentials-in-key-vault.md)할 수도 있습니다. | yes |

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
| servicePrincipalId | Azure Active Directory 애플리케이션의 클라이언트 ID를 지정합니다. | yes |
| servicePrincipalKey | Azure Active Directory 애플리케이션의 키를 지정합니다. 이 필드를 **SecureString**으로 표시하여 Data Factory에 안전하게 저장하거나, [Azure Key Vault에 저장된 비밀을 참조](store-credentials-in-key-vault.md)합니다. | yes |
| tenant | 애플리케이션이 있는 테넌트 정보(도메인 이름 또는 테넌트 ID)를 지정합니다. Azure Portal의 오른쪽 위 모서리를 마우스로 가리켜 검색합니다. | yes |
| aadResourceId | 권한 부여를 요청하는 AAD 리소스(예: `https://management.core.windows.net`)를 지정합니다.| yes |

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

### <a name="use-managed-identities-for-azure-resources-authentication"></a><a name="managed-identity"></a> Azure 리소스 인증에 관리 ID 사용

**authenticationType** 속성을 **ManagedServiceIdentity**로 설정합니다. 앞 섹션에서 설명한 일반 속성 외에 다음 속성을 지정합니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| aadResourceId | 권한 부여를 요청하는 AAD 리소스(예: `https://management.core.windows.net`)를 지정합니다.| yes |

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
| type | 데이터 세트의 **type** 속성을 **RestResource**로 설정해야 합니다. | yes |
| relativeUrl | 데이터를 포함하는 리소스에 대한 상대 URL입니다. 이 속성을 지정하지 않으면 연결된 서비스 정의에 지정된 URL만 사용됩니다. HTTP 커넥터는 결합된 URL에서 데이터를 `[URL specified in linked service]/[relative URL specified in dataset]`복사합니다. | 예 |

`requestBody` 을 `paginationRules` 설정하고 `requestMethod` `additionalHeaders`데이터 집합에서 계속 지원되는 반면 앞으로 활동 소스에서 새 모델을 사용하는 것이 좋습니다.

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

다음 속성은 복사 활동 **소스** 섹션에서 지원됩니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 복사 작업 원본의 **type** 속성은 **RestSource**로 설정해야 합니다. | yes |
| requestMethod | HTTP 메서드입니다. 허용되는 값은 **Get**(기본값) 또는 **Post**입니다. | 예 |
| additionalHeaders | 추가 HTTP 요청 헤더입니다. | 예 |
| requestBody | HTTP 요청의 본문입니다. | 예 |
| paginationRules | 다음 페이지 요청을 작성하기 위한 페이지 매김 규칙입니다. 자세한 내용은 [페이지 매김 지원](#pagination-support)을 참조하세요. | 예 |
| httpRequestTimeout | HTTP 요청이 응답을 받을 시간 제한(**TimeSpan** 값)입니다. 이 값은 응답 데이터를 읽는 시간 제한이 아니라, 응답을 받을 시간 제한입니다. 기본값은 **00:01:40**입니다.  | 예 |
| requestInterval | 다음 페이지에 대한 요청을 보내기 전에 대기할 시간입니다. 기본값은 **00:00:01**입니다. |  예 |

>[!NOTE]
>REST 커넥터는 에 지정된 "Accept" `additionalHeaders`헤더를 무시합니다. REST 커넥터는 JSON에서 응답만 지원하므로 `Accept: application/json`의 헤더를 자동으로 생성합니다.

**예 1: 페이지 가 있는 Get 메서드 사용**

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

일반적으로 REST API는 단일 요청의 응답 페이로드 크기를 적당한 수로 제한합니다. 많은 양의 데이터를 반환하는 동안 결과를 여러 페이지로 분할하고 발신자가 결과의 다음 페이지를 가져오기 위해 연속요청을 보내야 합니다. 보통 한 페이지에 대한 요청은 동적이고 이전 페이지의 응답에서 반환된 정보로 구성됩니다.

이 일반 REST 커넥터는 다음 페이지 매김 패턴을 지원합니다. 

* 다음 요청의 절대 또는 상대 URL = 현재 응답 본문의 속성 값
* 다음 요청의 절대 또는 상대 URL = 현재 응답 헤더의 헤더 값
* 다음 요청의 쿼리 매개 변수 = 현재 응답 본문의 속성 값
* 다음 요청의 쿼리 매개 변수 = 현재 응답 헤더의 헤더 값
* 다음 요청의 헤더 = 현재 응답 본문의 속성 값
* 다음 요청의 헤더 = 현재 응답 헤더의 헤더 값

**페이지 조정 규칙은** 하나 이상의 대/소문자 구분 키-값 쌍을 포함하는 데이터 집합의 사전으로 정의됩니다. 이 구성은 두 번째 페이지에서 시작하는 요청을 생성하는 데 사용됩니다. 커넥터는 HTTP 상태 코드 204(콘텐츠 없음)를 얻거나 "paginationRules"의 JSONPath 식이 null을 반환하면 반복이 중지됩니다.

페이지 매김 규칙의 **지원되는 키**는 다음과 같습니다.

| Key | 설명 |
|:--- |:--- |
| AbsoluteUrl | 다음 요청을 실행할 URL을 나타냅니다. **절대 URL 또는 상대 URL일**수 있습니다. |
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

해당 REST 복사 활동 소스 `paginationRules` 구성은 특히 다음과 같습니다.

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

## <a name="use-oauth"></a>OAuth 사용
이 섹션에서는 솔루션 템플릿을 사용하여 REST 커넥터의 데이터를 OAuth를 사용하여 JSON 형식으로 Azure Data Lake Storage로 복사하는 방법을 설명합니다. 

### <a name="about-the-solution-template"></a>솔루션 템플릿 소개

템플릿에는 다음 두 가지 활동이 포함되어 있습니다.
- **웹** 활동은 전달자 토큰을 검색한 다음 후속 복사 활동에 권한 부여로 전달합니다.
- 활동을 **복사하여** REST에서 Azure 데이터 레이크 저장소로 데이터를 복사합니다.

이 템플릿은 다음 두 개의 매개 변수를 정의합니다.
- **SinkContainer는** Azure Data Lake 저장소에서 데이터가 복사되는 루트 폴더 경로입니다. 
- **SinkDirectory는** 데이터가 Azure Data Lake 저장소에 복사되는 루트 아래의 디렉터리 경로입니다. 

### <a name="how-to-use-this-solution-template"></a>이 솔루션 템플릿을 사용하는 방법

1. OAuth 템플릿을 **사용하여 REST 또는 HTTP에서 복사로** 이동합니다. 소스 연결에 대한 새 연결을 만듭니다. 
    ![새 연결 만들기](media/solution-template-copy-from-rest-or-http-using-oauth/source-connection.png)

    다음은 새 연결된 서비스(REST) 설정의 주요 단계입니다.
    
     1. **기본 URL에서**사용자 고유의 소스 REST 서비스에 대한 URL 매개 변수를 지정합니다. 
     2. **인증 유형의**경우 *익명*을 선택합니다.
        ![새 REST 연결](media/solution-template-copy-from-rest-or-http-using-oauth/new-rest-connection.png)

2. 대상 연결에 대한 새 연결을 만듭니다.  
    ![새로운 Gen2 연결](media/solution-template-copy-from-rest-or-http-using-oauth/destination-connection.png)

3. **이 템플릿 사용을**선택합니다.
    ![이 템플릿 사용](media/solution-template-copy-from-rest-or-http-using-oauth/use-this-template.png)

4. 다음 예제와 같이 생성된 파이프라인이 표시됩니다. ![](media/solution-template-copy-from-rest-or-http-using-oauth/pipeline.png)

5. **웹** 활동을 선택합니다. **설정에서**해당 **URL**, **메서드,** 헤더 및 **Body를**지정하여 데이터를 복사하려는 서비스의 로그인 API에서 OAuth 베어러 토큰을 검색합니다. **Body** 템플릿의 자리 표시자는 AAD(Azure Active Directory) OAuth의 샘플을 보여 주며 있습니다. 참고 AAD 인증은 REST 커넥터에서 기본적으로 지원되며 OAuth 흐름의 예일 뿐입니다. 

    | 속성 | 설명 |
    |:--- |:--- |:--- |
    | URL |OAuth 베어러 토큰을 검색할 URL을 지정합니다. 예를 들어, 여기에 샘플에서 그것은https://login.microsoftonline.com/microsoft.onmicrosoft.com/oauth2/token |. 
    | 방법 | HTTP 메서드입니다. 허용된 값은 **게시** 및 **Get입니다.** | 
    | headers | 헤더는 HTTP 요청에서 하나의 헤더 이름을 참조하는 사용자 정의입니다. | 
    | 본문 | HTTP 요청의 본문입니다. | 

    ![파이프라인](media/solution-template-copy-from-rest-or-http-using-oauth/web-settings.png)

6. **데이터 복사** 활동에서 *소스* 탭을 선택하면 이전 단계에서 검색된 전달자 토큰(access_token)이 추가 헤더에서 **권한 부여로** 데이터 활동을 복사하도록 전달되는 것을 볼 수 있습니다. 파이프라인 실행을 시작하기 전에 다음 속성에 대한 설정을 확인합니다.

    | 속성 | 설명 |
    |:--- |:--- |:--- | 
    | 요청 메서드 | HTTP 메서드입니다. 허용되는 값은 **Get**(기본값) 또는 **Post**입니다. | 
    | 추가 헤더 | 추가 HTTP 요청 헤더입니다.| 

   ![복사 소스 인증](media/solution-template-copy-from-rest-or-http-using-oauth/copy-data-settings.png)

7. **디버그를**선택하고 **매개 변수를**입력한 다음 **완료를**선택합니다.
   ![파이프라인 실행](media/solution-template-copy-from-rest-or-http-using-oauth/pipeline-run.png) 

8. 파이프라인 실행이 성공적으로 완료되면 다음 예제인 ![파이프라인 실행 결과와 유사한 결과가 표시됩니다.](media/solution-template-copy-from-rest-or-http-using-oauth/run-result.png) 

9. **작업** 열에서 WebActivity의 "출력" 아이콘을 클릭하면 서비스에서 반환되는 access_token 표시됩니다.

   ![토큰 출력](media/solution-template-copy-from-rest-or-http-using-oauth/token-output.png) 

10. **작업** 열에서 CopyActivity의 "입력" 아이콘을 클릭 하면 WebActivity에서 검색 하는 access_token 인증을 위해 CopyActivity에 전달 됩니다. 

    ![토큰 입력](media/solution-template-copy-from-rest-or-http-using-oauth/token-input.png)
        
    >[!CAUTION] 
    >토큰이 일반 텍스트로 기록되지 않도록 하려면 웹 활동에서 "보안 출력"을 사용하도록 설정하고 복사 작업에서 "보안 입력"을 사용하도록 설정합니다.


## <a name="export-json-response-as-is"></a>JSON 응답을 있는 그대로 내보내기

이 REST 커넥터를 사용하여 REST API JSON 응답을 다양한 파일 기반 저장소에 있는 그대로 내보낼 수 있습니다. 이러한 스키마 독립적 복사를 완수하려면 데이터 세트 및 복사 작업의 스키마 매핑에서 "구조"(*스키마*라고도 함) 섹션을 건너뛰세요.

## <a name="schema-mapping"></a>스키마 매핑

데이터를 REST 엔드포인트에서 테이블 형식 싱크로 복사하려면 [스키마 매핑](copy-activity-schema-and-type-mapping.md#schema-mapping)을 참조하세요.

## <a name="next-steps"></a>다음 단계

Azure Data Factory의 복사 작업에서 원본 및 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소 및 형식](copy-activity-overview.md#supported-data-stores-and-formats)을 참조하세요.
