---
title: Azure Blockchain 워크 벤치 REST Api 사용
description: Azure Blockchain 워크 벤치 미리 보기를 사용 하는 방법에 대 한 시나리오 REST API
ms.date: 03/05/2020
ms.topic: how-to
ms.reviewer: brendal
ms.openlocfilehash: 696f1f2f96034f7a044f6a39182774c02804518f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87003069"
---
# <a name="using-the-azure-blockchain-workbench-preview-rest-api"></a>Azure Blockchain 워크 벤치 미리 보기 사용 REST API

Azure Blockchain 워크 벤치 미리 보기 REST API 개발자 및 정보 근로자에 게 블록 체인 응용 프로그램에 대 한 다양 한 통합을 구축 하는 방법을 제공 합니다. 이 문서에서는 REST API 워크 벤치를 사용 하는 방법에 대 한 몇 가지 시나리오를 강조 합니다. 예를 들어, 로그인 한 사용자가 할당 된 블록 체인 응용 프로그램을 보고 상호 작용할 수 있도록 하는 사용자 지정 blockchain 클라이언트를 만들려고 한다고 가정 합니다. 클라이언트는 블록 체인 워크 벤치 API를 사용 하 여 계약 인스턴스를 확인 하 고 스마트 계약에 대 한 작업을 수행할 수 있습니다.

## <a name="blockchain-workbench-api-endpoint"></a>블록 체인 워크 벤치 API 끝점

Blockchain 워크 벤치 Api는 배포에 대 한 끝점을 통해 액세스 됩니다. 배포에 대 한 API 끝점 URL을 가져오려면 다음을 수행 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 왼쪽 탐색 창에서 **리소스 그룹**을 선택 합니다.
1. 배포 된 블록 체인 워크 벤치의 리소스 그룹 이름을 선택 합니다.
1. 목록을 형식 기준으로 사전순으로 정렬하려면 **형식** 열 제목을 선택합니다.
1. 형식이 **App Service**인 리소스가 두 개 있습니다. "-Api" 접미사를 *사용 하 여* **App Service** 유형의 리소스를 선택 합니다.
1. App Service **개요**에서 배포 된 블록 체인 워크 벤치에 대 한 API 끝점 url을 나타내는 **url** 값을 복사 합니다.

    ![App service API 끝점 URL](media/use-api/app-service-api.png)

## <a name="authentication"></a>인증

Blockchain 워크 벤치 REST API에 대 한 요청은 Azure Active Directory (Azure AD)로 보호 됩니다.

REST Api에 대해 인증 된 요청을 수행 하려면 API를 호출 하기 전에 클라이언트 코드에 유효한 자격 증명으로 인증 해야 합니다. 인증은 Azure AD의 다양 한 행위자 간에 조정 되며 인증 증명으로 클라이언트에 [액세스 토큰](../../active-directory/develop/developer-glossary.md#access-token) 을 제공 합니다. 토큰은 REST API 요청의 HTTP 인증 헤더에 전송 됩니다. Azure AD 인증에 대해 자세히 알아보려면 개발자를 [위한 Azure Active Directory](../../active-directory/develop/index.yml)를 참조 하세요.

인증 방법에 대 한 예제는 [REST API 샘플](https://github.com/Azure-Samples/blockchain/tree/master/blockchain-workbench/rest-api-samples) 을 참조 하세요.

## <a name="using-postman"></a>Postman 사용

워크 벤치 Api를 사용 하 여 테스트 하거나 실험 하려는 경우 [Postman](https://www.postman.com) 을 사용 하 여 배포에 대 한 API 호출을 수행할 수 있습니다. GitHub에서 [워크 벤치 API 요청의 샘플 Postman 컬렉션을 다운로드](https://github.com/Azure-Samples/blockchain/tree/master/blockchain-workbench/rest-api-samples/postman) 합니다. 예제 API 요청 인증 및 사용에 대 한 자세한 내용은 추가 정보 파일을 참조 하세요.

## <a name="create-an-application"></a>애플리케이션 만들기

두 개의 API 호출을 사용 하 여 블록 체인 워크 벤치 응용 프로그램을 만듭니다. 이 방법은 워크 벤치 관리자 인 사용자만 수행할 수 있습니다.

응용 프로그램 [게시 API](/rest/api/azure-blockchain-workbench/applications/applicationspost) 를 사용 하 여 응용 프로그램의 JSON 파일을 업로드 하 고 응용 프로그램 ID를 가져옵니다.

### <a name="applications-post-request"></a>응용 프로그램 게시 요청

**Appfile** 매개 변수를 사용 하 여 구성 파일을 요청 본문의 일부로 보냅니다.

``` http
POST /api/v1/applications
Content-Type: multipart/form-data;
Authorization : Bearer {access token}
Content-Disposition: form-data; name="appFile"; filename="/C:/smart-contract-samples/HelloWorld.json"
Content-Type: application/json
```

### <a name="applications-post-response"></a>응용 프로그램 게시 응답

만든 응용 프로그램 ID가 응답에서 반환 됩니다. 다음 API를 호출할 때 구성 파일을 코드 파일과 연결 하려면 응용 프로그램 ID가 필요 합니다.

``` http
HTTP/1.1 200 OK
Content-Type: "application/json"
1
```

### <a name="contract-code-post-request"></a>계약 코드 게시 요청

응용 프로그램 ID를 전달 하 여 응용 프로그램 [계약 코드 게시 API](/rest/api/azure-blockchain-workbench/applications/contractcodepost) 를 사용 하 여 응용 프로그램의 농담 코드 파일을 업로드 합니다. 페이로드는 단일로 된 파일 이거나 색이 있는 파일이 포함 된 압축 파일 일 수 있습니다.

다음 값을 바꿉니다.

| 매개 변수 | 값 |
|-----------|-------|
| ApplicationId | 응용 프로그램 게시 API에서 값을 반환 합니다. |
| {ledgerId} | 원장의 인덱스입니다. 값은 일반적으로 1입니다. [원장 테이블](data-sql-management-studio.md) 에서 값을 확인할 수도 있습니다. |

``` http
POST /api/v1/applications/{applicationId}/contractCode?ledgerId={ledgerId}
Content-Type: multipart/form-data;
Authorization : Bearer {access token}
Content-Disposition: form-data; name="contractFile"; filename="/C:/smart-contract-samples/HelloWorld.sol"
```

### <a name="contract-code-post-response"></a>계약 코드 게시 응답

성공 하면 [ContractCode 테이블](data-sql-management-studio.md)에서 생성 된 계약 코드 ID가 응답에 포함 됩니다.

``` http
HTTP/1.1 200 OK
Content-Type: "application/json"
2
```

## <a name="assign-roles-to-users"></a>사용자에게 역할 할당

응용 프로그램 [역할 할당 게시 API](/rest/api/azure-blockchain-workbench/applications/roleassignmentspost) 를 사용 하 여 응용 프로그램 id, 사용자 ID 및 응용 프로그램 역할 id를 전달 하 여 지정 된 blockchain 응용 프로그램에서 사용자-역할 매핑을 만듭니다. 이 방법은 워크 벤치 관리자 인 사용자만 수행할 수 있습니다.

### <a name="role-assignments-post-request"></a>역할 할당 게시 요청

다음 값을 바꿉니다.

| 매개 변수 | 값 |
|-----------|-------|
| ApplicationId | 응용 프로그램 게시 API에서 값을 반환 합니다. |
| Id | [사용자 테이블](data-sql-management-studio.md)의 사용자 ID 값입니다. |
| {applicationRoleId} | [Applicationrole 테이블](data-sql-management-studio.md)에서 응용 프로그램 id에 연결 된 응용 프로그램 역할 id 값입니다. |

``` http
POST /api/v1/applications/{applicationId}/roleAssignments
Content-Type: application/json;
Authorization : Bearer {access token}

{
  "userId": {userId},
  "applicationRoleId": {applicationRoleId}
}
```

### <a name="role-assignments-post-response"></a>역할 할당 게시 응답

성공 하는 경우 응답은 [Roleassignment](data-sql-management-studio.md)에서 만든 역할 할당 ID를 포함 합니다.

``` http
HTTP/1.1 200
1
```

## <a name="list-applications"></a>애플리케이션 나열

[응용 프로그램 GET API](/rest/api/azure-blockchain-workbench/applications/applicationsget) 를 사용 하 여 사용자에 대 한 모든 Blockchain 워크 벤치 응용 프로그램을 검색 합니다. 이 예제에서는 로그인 한 사용자에 게 두 가지 응용 프로그램에 대 한 액세스 권한이 있습니다.

- [자산 전송](https://github.com/Azure-Samples/blockchain/blob/master/blockchain-workbench/application-and-smart-contract-samples/asset-transfer/readme.md)
- [Refrigerated 교통](https://github.com/Azure-Samples/blockchain/blob/master/blockchain-workbench/application-and-smart-contract-samples/refrigerated-transportation/readme.md)

### <a name="applications-get-request"></a>응용 프로그램 GET 요청

``` http
GET /api/v1/applications
Authorization : Bearer {access token}
```

### <a name="applications-get-response"></a>응용 프로그램에서 응답 가져오기

사용자가 Blockchain Workbench에서 액세스할 수 있는 모든 블록체인 애플리케이션이 응답으로 나열됩니다. Blockchain Workbench 관리자는 모든 블록체인 애플리케이션을 얻게 됩니다. 비-워크 벤치 관리자는 연결 된 응용 프로그램 역할 또는 연결 된 스마트 계약 인스턴스 역할이 하나 이상 있는 모든 블록 체인 응용 프로그램을 가져옵니다.

``` http
HTTP/1.1 200 OK
Content-type: application/json
{
    "nextLink": "/api/v1/applications?skip=2",
    "applications": [
        {
            "id": 1,
            "name": "AssetTransfer",
            "description": "Allows transfer of assets between a buyer and a seller, with appraisal/inspection functionality",
            "displayName": "Asset Transfer",
            "createdByUserId": 1,
            "createdDtTm": "2018-04-28T05:59:14.4733333",
            "enabled": true,
            "applicationRoles": null
        },
        {
            "id": 2,
            "name": "RefrigeratedTransportation",
            "description": "Application to track end-to-end transportation of perishable goods.",
            "displayName": "Refrigerated Transportation",
            "createdByUserId": 7,
            "createdDtTm": "2018-04-28T18:25:38.71",
            "enabled": true,
            "applicationRoles": null
        }
    ]
}
```

## <a name="list-workflows-for-an-application"></a>애플리케이션에 대한 워크플로 나열

[응용 프로그램 워크플로 사용 API 가져오기](/rest/api/azure-blockchain-workbench/applications/workflowsget) 를 사용 하 여 사용자가 블록 체인 워크 벤치에서 액세스할 수 있는 지정 된 blockchain 응용 프로그램의 모든 워크플로를 나열할 수 있습니다. 각 블록체인 애플리케이션에는 하나 이상의 워크플로가 있고 각 워크플로에는 제로 또는 스마트 계약 인스턴스가 있습니다. 하나의 워크플로만 있는 블록체인 클라이언트 애플리케이션의 경우 사용자가 적절한 워크플로 선택할 수 있도록 하는 사용자 경험 흐름을 건너뛰는 것이 좋습니다.

### <a name="application-workflows-request"></a>응용 프로그램 워크플로 요청

``` http
GET /api/v1/applications/{applicationId}/workflows
Authorization: Bearer {access token}
```

### <a name="application-workflows-response"></a>응용 프로그램 워크플로 응답

Blockchain Workbench 관리자는 모든 블록체인 워크플로를 얻게 됩니다. 비 Workbench 관리자는 연결된 애플리케이션 역할 또는 연결된 스마트 계약 인스턴스 역할이 하나 이상 있는 모든 워크플로를 얻게 됩니다.

``` http
HTTP/1.1 200 OK
Content-type: application/json
{
    "nextLink": "/api/v1/applications/1/workflows?skip=1",
    "workflows": [
        {
            "id": 1,
            "name": "AssetTransfer",
            "description": "Handles the business logic for the asset transfer scenario",
            "displayName": "Asset Transfer",
            "applicationId": 1,
            "constructorId": 1,
            "startStateId": 1
        }
    ]
}
```

## <a name="create-a-contract-instance"></a>계약 인스턴스 만들기

[계약 V2 사후 API](/rest/api/azure-blockchain-workbench/contractsv2/contractpost) 를 사용 하 여 워크플로에 대 한 새 스마트 계약 인스턴스를 만듭니다. 사용자는 워크플로에 대 한 스마트 계약 인스턴스를 시작할 수 있는 응용 프로그램 역할에 연결 되어 있는 경우에만 새 스마트 계약 인스턴스를 만들 수 있습니다.

> [!NOTE]
> 이 예제에서는 API의 버전 2가 사용 됩니다. 버전 2 계약 Api는 연결 된 ProvisioningStatus 필드에 대 한 더 많은 세분성을 제공 합니다.

### <a name="contracts-post-request"></a>계약 게시 요청

다음 값을 바꿉니다.

| 매개 변수 | 값 |
|-----------|-------|
| WorkflowId | 워크플로 ID 값은 [워크플로 테이블](data-sql-management-studio.md)에서 계약의 ConstructorID입니다. |
| {contractCodeId} | [ContractCode 테이블](data-sql-management-studio.md)의 계약 코드 ID 값입니다. 만들려는 계약 인스턴스의 응용 프로그램 ID와 원장 ID의 상관 관계를 지정할 수 있습니다. |
| ConnectionId | [연결 테이블](data-sql-management-studio.md)의 연결 ID 값입니다. |

요청 본문의 경우 다음 정보를 사용 하 여 값을 설정 합니다.

| 매개 변수 | 값 |
|-----------|-------|
| workflowFunctionID | [Workflowfunction 테이블](data-sql-management-studio.md)의 ID입니다. |
| workflowActionParameters | 생성자에 전달 되는 매개 변수의 이름 값 쌍입니다. 각 매개 변수에 대해 [Workflowfunctionparameter](data-sql-management-studio.md) 테이블의 workflowFunctionParameterID 값을 사용 합니다. |

``` http
POST /api/v2/contracts?workflowId={workflowId}&contractCodeId={contractCodeId}&connectionId={connectionId}
Content-Type: application/json;
Authorization : Bearer {access token}

{
  "workflowFunctionID": 2,
  "workflowActionParameters": [
    {
      "name": "message",
      "value": "Hello, world!",
      "workflowFunctionParameterId": 3
    }
  ]
}
```

### <a name="contracts-post-response"></a>계약 게시 응답

성공 하면 역할 할당 API는 [ContractActionParameter 테이블](data-sql-management-studio.md)에서 ContractActionID를 반환 합니다.

``` http
HTTP/1.1 200 OK
4
```

## <a name="list-smart-contract-instances-for-a-workflow"></a>워크플로에 대한 스마트 계약 인스턴스 나열

[계약 GET API](/rest/api/azure-blockchain-workbench/contractsv2/contractsget) 를 사용 하 여 워크플로에 대 한 모든 스마트 계약 인스턴스를 표시 합니다. 또는 사용자가 표시된 스마트 계약 인스턴스 중 하나를 심층 분석하도록 허용할 수 있습니다.

### <a name="contracts-request"></a>계약 요청

이 예제에서는 사용자가 조치를 취할 스마트 계약 인스턴스 중 하나와 상호 작용하려 한다고 가정해 봅시다.

``` http
GET api/v1/contracts?workflowId={workflowId}
Authorization: Bearer {access token}
```

### <a name="contracts-response"></a>계약 응답

응답에는 지정 된 워크플로의 모든 스마트 계약 인스턴스가 나열 됩니다. Workbench 관리자는 모든 스마트 계약 인스턴스를 얻게 됩니다. 비 Workbench 관리자는 연결된 애플리케이션 역할 또는 연결된 스마트 계약 인스턴스 역할이 하나 이상 있는 모든 스마트 계약 인스턴스를 얻게 됩니다.

``` http
HTTP/1.1 200 OK
Content-type: application/json
{
    "nextLink": "/api/v1/contracts?skip=3&workflowId=1",
    "contracts": [
        {
            "id": 1,
            "provisioningStatus": 2,
            "connectionID": 1,
            "ledgerIdentifier": "0xbcb6127be062acd37818af290c0e43479a153a1c",
            "deployedByUserId": 1,
            "workflowId": 1,
            "contractCodeId": 1,
            "contractProperties": [
                {
                    "workflowPropertyId": 1,
                    "value": "0"
                },
                {
                    "workflowPropertyId": 2,
                    "value": "My first car"
                },
                {
                    "workflowPropertyId": 3,
                    "value": "54321"
                },
                {
                    "workflowPropertyId": 4,
                    "value": "0"
                },
                {
                    "workflowPropertyId": 5,
                    "value": "0x0000000000000000000000000000000000000000"
                },
                {
                    "workflowPropertyId": 6,
                    "value": "0x0000000000000000000000000000000000000000"
                },
                {
                    "workflowPropertyId": 7,
                    "value": "0x0000000000000000000000000000000000000000"
                },
                {
                    "workflowPropertyId": 8,
                    "value": "0xd882530eb3d6395e697508287900c7679dbe02d7"
                }
            ],
            "transactions": [
                {
                    "id": 1,
                    "connectionId": 1,
                    "transactionHash": "0xf3abb829884dc396e03ae9e115a770b230fcf41bb03d39457201449e077080f4",
                    "blockID": 241,
                    "from": "0xd882530eb3d6395e697508287900c7679dbe02d7",
                    "to": null,
                    "value": 0,
                    "isAppBuilderTx": true
                }
            ],
            "contractActions": [
                {
                    "id": 1,
                    "userId": 1,
                    "provisioningStatus": 2,
                    "timestamp": "2018-04-29T23:41:14.9333333",
                    "parameters": [
                        {
                            "name": "Description",
                            "value": "My first car"
                        },
                        {
                            "name": "Price",
                            "value": "54321"
                        }
                    ],
                    "workflowFunctionId": 1,
                    "transactionId": 1,
                    "workflowStateId": 1
                }
            ]
        }
    ]
}
```

## <a name="list-available-actions-for-a-contract"></a>계약에 사용 가능한 작업 나열

계약 [작업 가져오기 API](/rest/api/azure-blockchain-workbench/contractsv2/contractactionget) 를 사용 하 여 계약 상태가 지정 된 사용 가능한 사용자 작업을 표시 합니다. 

### <a name="contract-action-request"></a>계약 작업 요청

이 예제에서는 사용자가 만든 새 스마트 계약에 대해 사용 가능한 모든 작업을 확인 합니다.

``` http
GET /api/v1/contracts/{contractId}/actions
Authorization: Bearer {access token}
```

### <a name="contract-action-response"></a>계약 동작 응답

지정된 스마트 계약 인스턴스의 현재 상태를 고려할 때 사용자가 수행할 수 있는 모든 작업이 응답으로 나열됩니다.

* 수정: 사용자가 자산의 설명과 가격을 수정할 수 있게 허용합니다.
* 종료: 사용자가 자산의 계약을 종료할 수 있게 허용합니다.

사용자는 연결된 애플리케이션 역할이 있거나 지정된 스마트 계약 인스턴스의 현재 상태에 대한 스마트 계약 인스턴스 역할과 연결된 경우 적용 가능한 모든 작업을 얻게 됩니다.

``` http
HTTP/1.1 200 OK
Content-type: application/json
{
    "nextLink": "/api/v1/contracts/1/actions?skip=2",
    "workflowFunctions": [
        {
            "id": 2,
            "name": "Modify",
            "description": "Modify the description/price attributes of this asset transfer instance",
            "displayName": "Modify",
            "parameters": [
                {
                    "id": 1,
                    "name": "description",
                    "description": "The new description of the asset",
                    "displayName": "Description",
                    "type": {
                        "id": 2,
                        "name": "string",
                        "elementType": null,
                        "elementTypeId": 0
                    }
                },
                {
                    "id": 2,
                    "name": "price",
                    "description": "The new price of the asset",
                    "displayName": "Price",
                    "type": {
                        "id": 3,
                        "name": "money",
                        "elementType": null,
                        "elementTypeId": 0
                    }
                }
            ],
            "workflowId": 1
        },
        {
            "id": 3,
            "name": "Terminate",
            "description": "Used to cancel this particular instance of asset transfer",
            "displayName": "Terminate",
            "parameters": [],
            "workflowId": 1
        }
    ]
}
```

## <a name="execute-an-action-for-a-contract"></a>계약에 대한 작업 실행

[계약 작업 게시 API](/rest/api/azure-blockchain-workbench/contractsv2/contractactionpost) 를 사용 하 여 지정 된 스마트 계약 인스턴스에 대 한 작업을 수행 합니다.

### <a name="contract-action-post-request"></a>계약 작업 게시 요청

이 경우 사용자가 자산의 설명과 가격을 수정 하려는 시나리오를 고려 하세요.

``` http
POST /api/v1/contracts/{contractId}/actions
Authorization: Bearer {access token}
actionInformation: {
    "workflowFunctionId": 2,
    "workflowActionParameters": [
        {
            "name": "description",
            "value": "My updated car"
        },
        {
            "name": "price",
            "value": "54321"
        }
    ]
}
```

사용자는 지정된 스마트 계약 인스턴스 및 사용자의 연결된 애플리케이션 역할 또는 스마트 계약 인스턴스 역할의 상태를 고려한 작업만 실행할 수 있습니다.

### <a name="contract-action-post-response"></a>계약 동작 게시 응답

게시가 성공하면 응답 본문 없이 HTTP 200 OK 응답이 반환됩니다.

``` http
HTTP/1.1 200 OK
Content-type: application/json
```

## <a name="next-steps"></a>다음 단계

Blockchain 워크 벤치 Api에 대 한 참조 정보는 [Azure 블록 체인 워크 벤치 REST API 참조](/rest/api/azure-blockchain-workbench)를 참조 하세요.
