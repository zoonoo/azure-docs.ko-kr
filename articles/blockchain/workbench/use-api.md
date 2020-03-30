---
title: Azure 블록체인 워크벤치 REST API 사용
description: Azure 블록 체인 워크 벤치 미리 보기 REST API를 사용하는 방법에 대한 시나리오
ms.date: 03/05/2020
ms.topic: article
ms.reviewer: brendal
ms.openlocfilehash: 3084fcf343bc42fe01bf352b6791916d62f63540
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672742"
---
# <a name="using-the-azure-blockchain-workbench-preview-rest-api"></a>Azure 블록 체인 워크벤치 미리 보기 REST API 사용

Azure 블록 체인 워크 벤치 미리보기 REST API는 개발자와 정보 근로자에게 블록 체인 응용 프로그램에 대한 풍부한 통합을 구축 할 수있는 방법을 제공합니다. 이 문서에서는 Workbench REST API를 사용하는 방법에 대한 몇 가지 시나리오를 중시합니다. 예를 들어 로그인한 사용자가 할당된 블록 체인 응용 프로그램을 보고 상호 작용할 수 있는 사용자 지정 블록 체인 클라이언트를 만들려고 한다고 가정합니다. 클라이언트는 Blockchain Workbench API를 사용하여 계약 인스턴스를 보고 스마트 계약에 대한 조치를 취할 수 있습니다.

## <a name="blockchain-workbench-api-endpoint"></a>블록체인 워크벤치 API 엔드포인트

블록체인 워크벤치 API는 배포를 위한 엔드포인트를 통해 액세스됩니다. 배포에 대한 API 끝점 URL을 얻으려면 다음을 수행하십시오.

1. [Azure 포털에](https://portal.azure.com)로그인합니다.
1. 왼쪽 탐색 창에서 **리소스 그룹을**선택합니다.
1. 배포된 블록 체인 워크 벤치의 리소스 그룹 이름을 선택합니다.
1. 목록을 형식 기준으로 사전순으로 정렬하려면 **형식** 열 제목을 선택합니다.
1. 형식이 **App Service**인 리소스가 두 개 있습니다. "-api" 접미사가 *있는* **앱 서비스** 형식의 리소스를 선택합니다.
1. 앱 서비스 **개요에서** **배포된** 블록 체인 워크벤치에 API 끝점 URL을 나타내는 URL 값을 복사합니다.

    ![앱 서비스 API 엔드포인트 URL](media/use-api/app-service-api.png)

## <a name="authentication"></a>인증

블록 체인 워크 벤치 REST API에 대한 요청은 Azure Active Directory (Azure AD)로 보호됩니다.

REST API에 인증된 요청을 하려면 API를 호출하기 전에 클라이언트 코드에 유효한 자격 증명을 사용하여 인증해야 합니다. 인증은 Azure AD에 의해 다양한 행위자 간에 조정되며 클라이언트에 인증 증명으로 [액세스 토큰을](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#access-token) 제공합니다. 그런 다음 이 토큰은 REST API 요청의 HTTP 권한 부여 헤더로 전송됩니다. Azure AD 인증에 대한 자세한 내용은 [개발자용 Azure Active Directory를](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide)참조하십시오.

인증 방법에 대한 예제는 [REST API 샘플을](https://github.com/Azure-Samples/blockchain/tree/master/blockchain-workbench/rest-api-samples) 참조하십시오.

## <a name="using-postman"></a>Postman 사용

Workbench API를 테스트하거나 실험하려는 경우 [Postman을](https://www.postman.com) 사용하여 배포에 대한 API 호출을 수행할 수 있습니다. [GitHub에서 Workbench API 요청의 샘플 Postman 컬렉션을 다운로드합니다.](https://github.com/Azure-Samples/blockchain/tree/master/blockchain-workbench/rest-api-samples/postman) 예제 API 요청의 인증 및 사용에 대한 자세한 내용은 README 파일을 참조하십시오.

## <a name="create-an-application"></a>애플리케이션 만들기

두 개의 API 호출을 사용하여 블록 체인 워크 벤치 응용 프로그램을 만듭니다. 이 메서드는 Workbench 관리자인 사용자만 수행할 수 있습니다.

응용 [프로그램 POST API를](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/applications/applicationspost) 사용하여 응용 프로그램의 JSON 파일을 업로드하고 응용 프로그램 ID를 가져옵니다.

### <a name="applications-post-request"></a>응용 프로그램 게시물 요청

**appFile** 매개 변수를 사용하여 구성 파일을 요청 본문일부로 보냅니다.

``` http
POST /api/v1/applications
Content-Type: multipart/form-data;
Authorization : Bearer {access token}
Content-Disposition: form-data; name="appFile"; filename="/C:/smart-contract-samples/HelloWorld.json"
Content-Type: application/json
```

### <a name="applications-post-response"></a>응용 프로그램 POST 응답

생성된 응용 프로그램 ID가 응답에서 반환됩니다. 다음 API를 호출할 때 구성 파일을 코드 파일과 연결하려면 응용 프로그램 ID가 필요합니다.

``` http
HTTP/1.1 200 OK
Content-Type: "application/json"
1
```

### <a name="contract-code-post-request"></a>계약 코드 POST 요청

응용 [프로그램 계약 코드 POST API를](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/applications/contractcodepost) 사용하여 응용 프로그램 ID를 전달하여 응용 프로그램의 Solidity 코드 파일을 업로드합니다. 페이로드는 단일 솔리드 티 파일 또는 솔리드 티 파일을 포함 하는 압축 된 파일 수 있습니다.

다음 값을 바꿉니다.

| 매개 변수 | 값 |
|-----------|-------|
| {응용 프로그램 ID} | 응용 프로그램 POST API에서 값을 반환합니다. |
| {원장ID} | 원장의 인덱스입니다. 값은 일반적으로 1입니다. [원장 테이블에서](data-sql-management-studio.md) 값을 확인할 수도 있습니다. |

``` http
POST /api/v1/applications/{applicationId}/contractCode?ledgerId={ledgerId}
Content-Type: multipart/form-data;
Authorization : Bearer {access token}
Content-Disposition: form-data; name="contractFile"; filename="/C:/smart-contract-samples/HelloWorld.sol"
```

### <a name="contract-code-post-response"></a>계약 코드 POST 응답

성공하면 응답에는 [ContractCode 테이블에서](data-sql-management-studio.md)만든 계약 코드 ID가 포함됩니다.

``` http
HTTP/1.1 200 OK
Content-Type: "application/json"
2
```

## <a name="assign-roles-to-users"></a>사용자에게 역할 할당

응용 [프로그램 ID,](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/applications/roleassignmentspost) 사용자 ID 및 응용 프로그램 역할 ID를 전달하여 응용 프로그램 역할 할당 POST API를 사용하여 지정된 블록 체인 응용 프로그램에서 사용자 간 역할 매핑을 만듭니다. 이 메서드는 Workbench 관리자인 사용자만 수행할 수 있습니다.

### <a name="role-assignments-post-request"></a>역할 할당 POST 요청

다음 값을 바꿉니다.

| 매개 변수 | 값 |
|-----------|-------|
| {응용 프로그램 ID} | 응용 프로그램 POST API에서 값을 반환합니다. |
| {사용자 ID} | [사용자 테이블의](data-sql-management-studio.md)사용자 ID 값입니다. |
| {응용 프로그램역할 ID} | 응용 프로그램 역할 ID [값은 ApplicationRole 테이블에서](data-sql-management-studio.md)응용 프로그램 ID에 연결됩니다. |

``` http
POST /api/v1/applications/{applicationId}/roleAssignments
Content-Type: application/json;
Authorization : Bearer {access token}

{
  "userId": {userId},
  "applicationRoleId": {applicationRoleId}
}
```

### <a name="role-assignments-post-response"></a>역할 할당 POST 응답

성공하면 응답에는 [RoleAssignment 테이블에서](data-sql-management-studio.md)생성된 역할 할당 ID가 포함됩니다.

``` http
HTTP/1.1 200
1
```

## <a name="list-applications"></a>애플리케이션 나열

응용 [프로그램 GET API를](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/applications/applicationsget) 사용하여 사용자에 대한 모든 Blockchain 워크벤치 응용 프로그램을 검색합니다. 이 예제에서 로그인한 사용자는 다음 두 응용 프로그램에 액세스할 수 있습니다.

- [자산 이전](https://github.com/Azure-Samples/blockchain/blob/master/blockchain-workbench/application-and-smart-contract-samples/asset-transfer/readme.md)
- [냉장 운송](https://github.com/Azure-Samples/blockchain/blob/master/blockchain-workbench/application-and-smart-contract-samples/refrigerated-transportation/readme.md)

### <a name="applications-get-request"></a>응용 프로그램 GET 요청

``` http
GET /api/v1/applications
Authorization : Bearer {access token}
```

### <a name="applications-get-response"></a>응용 프로그램 GET 응답

사용자가 Blockchain Workbench에서 액세스할 수 있는 모든 블록체인 애플리케이션이 응답으로 나열됩니다. Blockchain Workbench 관리자는 모든 블록체인 애플리케이션을 얻게 됩니다. Workbench가 아닌 관리자는 하나 이상의 연결된 응용 프로그램 역할 또는 연결된 스마트 계약 인스턴스 역할이 있는 모든 블록 체인 응용 프로그램을 가져옵니다.

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

[응용 프로그램 워크플로 GET API를](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/applications/workflowsget) 사용하여 사용자가 블록 체인 워크벤치에서 액세스 할 수있는 지정된 블록 체인 응용 프로그램의 모든 워크 플로우를 나열합니다. 각 블록체인 애플리케이션에는 하나 이상의 워크플로가 있고 각 워크플로에는 제로 또는 스마트 계약 인스턴스가 있습니다. 하나의 워크플로만 있는 블록체인 클라이언트 애플리케이션의 경우 사용자가 적절한 워크플로 선택할 수 있도록 하는 사용자 경험 흐름을 건너뛰는 것이 좋습니다.

### <a name="application-workflows-request"></a>응용 프로그램 워크플로 요청

``` http
GET /api/v1/applications/{applicationId}/workflows
Authorization: Bearer {access token}
```

### <a name="application-workflows-response"></a>애플리케이션 워크플로우 응답

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

[계약 V2 POST API를](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/contractsv2/contractpost) 사용하여 워크플로에 대한 새 스마트 계약 인스턴스를 만듭니다. 사용자는 사용자가 응용 프로그램 역할과 연결된 경우에만 새 스마트 계약 인스턴스를 만들 수 있으며, 워크플로에 대한 스마트 계약 인스턴스를 시작할 수 있습니다.

> [!NOTE]
> 이 예제에서는 API의 버전 2가 사용됩니다. 버전 2 계약 API는 연결된 프로비저닝 상태 필드에 대해 보다 세분성을 제공합니다.

### <a name="contracts-post-request"></a>계약 포스트 요청

다음 값을 바꿉니다.

| 매개 변수 | 값 |
|-----------|-------|
| {워크플로우Id} | 워크플로 ID 값은 [워크플로 테이블의](data-sql-management-studio.md)계약의 생성자 ID입니다. |
| {계약코드Id} | [계약 코드 테이블에서](data-sql-management-studio.md)계약 코드 ID 값입니다. 만들려는 계약 인스턴스에 대한 응용 프로그램 ID 및 원장 ID의 상관 관계를 지정합니다. |
| {연결 ID} | 연결 [테이블의](data-sql-management-studio.md)연결 ID 값입니다. |

요청 본문에 대 한 다음 정보를 사용 하 여 값을 설정 합니다.

| 매개 변수 | 값 |
|-----------|-------|
| 워크플로기능ID | [워크플로기능 테이블의](data-sql-management-studio.md)ID. |
| 워크플로액션매개변수 | 생성자에게 전달된 매개 변수의 이름 값 쌍입니다. 각 매개 변수에 대해 워크플로함수매개 변수 테이블에서 [워크플로함수매개](data-sql-management-studio.md) ID 값을 사용합니다. |

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

### <a name="contracts-post-response"></a>계약 후 응답

성공하면 역할 할당 API는 [ContractActionParameter 테이블에서 ContractActionID를](data-sql-management-studio.md)반환합니다.

``` http
HTTP/1.1 200 OK
4
```

## <a name="list-smart-contract-instances-for-a-workflow"></a>워크플로에 대한 스마트 계약 인스턴스 나열

[계약 GET API를](/rest/api/azure-blockchain-workbench/contractsv2/contractsget) 사용하여 워크플로에 대한 모든 스마트 계약 인스턴스를 표시합니다. 또는 사용자가 표시된 스마트 계약 인스턴스 중 하나를 심층 분석하도록 허용할 수 있습니다.

### <a name="contracts-request"></a>계약 요청

이 예제에서는 사용자가 조치를 취할 스마트 계약 인스턴스 중 하나와 상호 작용하려 한다고 가정해 봅시다.

``` http
GET api/v1/contracts?workflowId={workflowId}
Authorization: Bearer {access token}
```

### <a name="contracts-response"></a>계약 응답

응답에는 지정된 워크플로의 모든 스마트 계약 인스턴스가 나열됩니다. Workbench 관리자는 모든 스마트 계약 인스턴스를 얻게 됩니다. 비 Workbench 관리자는 연결된 애플리케이션 역할 또는 연결된 스마트 계약 인스턴스 역할이 하나 이상 있는 모든 스마트 계약 인스턴스를 얻게 됩니다.

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

[계약 작업 GET API를](/rest/api/azure-blockchain-workbench/contractsv2/contractactionget) 사용하여 계약 상태에 따라 사용 가능한 사용자 작업을 표시합니다. 

### <a name="contract-action-request"></a>계약 작업 요청

이 예제에서 사용자는 만든 새 스마트 계약에 대해 사용 가능한 모든 작업을 보고 있습니다.

``` http
GET /api/v1/contracts/{contractId}/actions
Authorization: Bearer {access token}
```

### <a name="contract-action-response"></a>계약 작업 응답

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

[계약 작업 POST API를](/rest/api/azure-blockchain-workbench/contractsv2/contractactionpost) 사용하여 지정된 스마트 계약 인스턴스에 대한 작업을 수행합니다.

### <a name="contract-action-post-request"></a>계약 작업 POST 요청

이 경우 사용자가 자산의 설명과 가격을 수정하려는 시나리오를 고려합니다.

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

### <a name="contract-action-post-response"></a>계약 작업 POST 응답

게시가 성공하면 응답 본문 없이 HTTP 200 OK 응답이 반환됩니다.

``` http
HTTP/1.1 200 OK
Content-type: application/json
```

## <a name="next-steps"></a>다음 단계

블록체인 워크벤치 API에 대한 참조 정보는 [Azure 블록체인 워크벤치 REST API 참조를](https://docs.microsoft.com/rest/api/azure-blockchain-workbench)참조하십시오.
