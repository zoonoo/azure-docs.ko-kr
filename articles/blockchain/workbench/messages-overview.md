---
title: Azure Blockchain Workbench 메시지 통합 개요
description: Azure Blockchain Workbench의 메시지 사용에 대해 간략하게 설명합니다.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 11/12/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: f8f3584475415cf9ca19458f6da78d34df37f438
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/13/2018
ms.locfileid: "51614364"
---
# <a name="azure-blockchain-workbench-messaging-integration"></a>Azure Blockchain Workbench 메시지 통합 개요

Azure Blockchain Workbench는 REST API를 제공할 뿐만 아니라 메시징 기반 통합도 제공합니다. 이 Workbench는 Azure Event Grid를 통해 원장 중심 이벤트를 게시하여 다운스트림 소비자가 데이터를 수집하거나 이러한 이벤트를 기준으로 작업을 수행할 수 있도록 합니다. 신뢰할 수 있는 메시징을 필요로 하는 클라이언트를 위해, Azure Blockchain Workbench는 Azure Service Bus 엔드포인트에 메시지를 배달합니다.

## <a name="input-apis"></a>입력 API

외부 시스템에서 트랜잭션을 시작하여 사용자 및 계약을 만들고 계약을 업데이트하려는 경우 메시징 입력 API를 사용하여 원장에서 트랜잭션을 수행할 수 있습니다. 입력 API를 설명하는 샘플은 [메시징 통합 샘플](https://aka.ms/blockchain-workbench-integration-sample)을 참조하세요.

현재 사용 가능한 입력 API는 다음과 같습니다.

### <a name="create-user"></a>사용자 만들기

새 사용자를 만듭니다.

요청에는 다음 필드가 필요합니다.

| **Name**             | **설명**                                      |
|----------------------|------------------------------------------------------|
| requestId            | 클라이언트 제공 GUID입니다.                                |
| firstname            | 사용자의 이름입니다.                              |
| Lastname             | 사용자의 성입니다.                               |
| emailAddress         | 사용자의 이메일 주소입니다.                           |
| externalId           | 사용자의 Azure AD 개체 ID입니다.                      |
| connectionId         | 블록체인 연결에 대한 고유 식별자입니다. |
| messageSchemaVersion | 메시징 스키마 버전입니다.                            |
| messageName          | **CreateUserRequest**                               |

예제:

``` json
{
    "requestId": "e2264523-6147-41fc-bbbb-edba8e44562d",
    "firstName": "Ali",
    "lastName": "Alio",
    "emailAddress": "aa@contoso.com",
    "externalId": "6a9b7f65-ffff-442f-b3b8-58a35abd1bcd",
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateUserRequest"
}
```

Blockchain Workbench는 다음 필드가 포함된 응답을 반환합니다.

| **Name**              | **설명**                                                                                                             |
|-----------------------|-----------------------------------------------------------------------------------------------------------------------------|
| requestId             | 클라이언트 제공 GUID입니다. |
| userId                | 생성된 사용자의 ID입니다. |
| userChainIdentifier   | 블록체인 네트워크에서 생성된 사용자의 주소입니다. Ethereum에서 주소는 사용자의 **체인 내** 주소입니다. |
| connectionId          | 블록체인 연결에 대한 고유 식별자입니다.|
| messageSchemaVersion  | 메시징 스키마 버전입니다. |
| messageName           | **CreateUserUpdate** |
| status                | 사용자 생성 요청의 상태입니다.  성공하면 값은 **Success**입니다. 실패 시 값은 **Failure**입니다.     |
| additionalInformation | 상태에 따라 제공되는 추가 정보입니다. |

Blockchain Workbench의 성공적인 **사용자 만들기** 응답 예:

``` json
{ 
    "requestId": "e2264523-6147-41fc-bb59-edba8e44562d", 
    "userId": 15, 
    "userChainIdentifier": "0x9a8DDaCa9B7488683A4d62d0817E965E8f248398", 
    "connectionId": 1, 
    "messageSchemaVersion": "1.0.0", 
    "messageName": "CreateUserUpdate", 
    "status": "Success", 
    "additionalInformation": { } 
} 
```

요청이 성공하지 못한 경우 오류에 대한 세부 정보가 추가 정보에 포함됩니다.

``` json
{
    "requestId": "e2264523-6147-41fc-bb59-edba8e44562d", 
    "userId": 15, 
    "userChainIdentifier": null, 
    "connectionId": 1, 
    "messageSchemaVersion": "1.0.0", 
    "messageName": "CreateUserUpdate", 
    "status": "Failure", 
    "additionalInformation": { 
        "errorCode": 4000, 
        "errorMessage": "User cannot be provisioned on connection." 
    }
}
```

### <a name="create-contract"></a>계약 만들기

새 계약을 만듭니다.

요청에는 다음 필드가 필요합니다.

| **Name**             | **설명**                                                                                                           |
|----------------------|---------------------------------------------------------------------------------------------------------------------------|
| requestId            | 클라이언트 제공 GUID입니다. |
| userChainIdentifier  | 블록체인 네트워크에서 생성된 사용자의 주소입니다. Ethereum에서 이 주소는 **체인 내** 주소입니다. |
| applicationName      | 애플리케이션의 이름입니다. |
| workflowName         | 워크플로의 이름입니다. |
| 매개 변수           | 계약 생성을 위한 매개 변수 입력입니다. |
| connectionId         | 블록체인 연결에 대한 고유 식별자입니다. |
| messageSchemaVersion | 메시징 스키마 버전입니다. |
| messageName          | **CreateContractRequest** |

예제:

``` json
{ 
    "requestId": "ce3c429b-a091-4baa-b29b-5b576162b211", 
    "userChainIdentifier": "0x9a8DDaCa9B7488683A4d62d0817E965E8f248398", 
    "applicationName": "AssetTransfer", 
    "workflowName": "AssetTransfer", 
    "parameters": [ 
        { 
            "name": "description", 
            "value": "a 1969 dodge charger" 
        }, 
        { 
            "name": "price", 
            "value": "12345" 
        } 
    ], 
    "connectionId": 1, 
    "messageSchemaVersion": "1.0.0", 
    "messageName": "CreateContractRequest" 
}
```

Blockchain Workbench는 다음 필드가 포함된 응답을 반환합니다.

| **Name**                 | **설명**                                                                   |
|--------------------------|-----------------------------------------------------------------------------------|
| requestId                | 클라이언트 제공 GUID입니다.                                                             |
| contractId               | Azure Blockchain Workbench 내 계약에 대한 고유 식별자입니다. |
| contractLedgerIdentifier | 원장의 계약 주소입니다.                                            |
| connectionId             | 블록체인 연결에 대한 고유 식별자입니다.                               |
| messageSchemaVersion     | 메시징 스키마 버전입니다.                                                         |
| messageName              | **CreateContractUpdate**                                                      |
| status                   | 계약 생성 요청의 상태입니다.  가능한 값은 **제출**, **커밋됨**, **실패**입니다.  |
| additionalInformation    | 상태에 따라 제공되는 추가 정보입니다.                              |

Blockchain Workbench의 제출된 **계약 만들기** 응답 예:

``` json
{
    "requestId": "ce3c429b-a091-4baa-b29b-5b576162b211",
    "contractId": 55,
    "contractLedgerIdentifier": "0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe",
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractUpdate",
    "status": "Submitted"
    "additionalInformation": { }
}
```

Blockchain Workbench의 커밋된 **계약 만들기** 응답 예:

``` json
{
    "requestId": "ce3c429b-a091-4baa-b29b-5b576162b211",
    "contractId": 55,
    "contractLedgerIdentifier": "0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe",
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractUpdate",
    "status": "Committed",
    "additionalInformation": { }
}
```

요청이 성공하지 못한 경우 오류에 대한 세부 정보가 추가 정보에 포함됩니다.

``` json
{
    "requestId": "ce3c429b-a091-4baa-b29b-5b576162b211",
    "contractId": 55,
    "contractLedgerIdentifier": null,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractUpdate",
    "status": "Failure"
    "additionalInformation": {
        "errorCode": 4000,
        "errorMessage": "Contract cannot be provisioned on connection."
    }
}
```

### <a name="create-contract-action"></a>계약 작업 만들기

새 계약 작업을 만듭니다.

요청에는 다음 필드가 필요합니다.

| **Name**                 | **설명**                                                                                                           |
|--------------------------|---------------------------------------------------------------------------------------------------------------------------|
| requestId                | 클라이언트 제공 GUID입니다. |
| userChainIdentifier      | 블록체인 네트워크에서 생성된 사용자의 주소입니다. Ethereum에서 이는 사용자의 **체인 내** 주소입니다. |
| contractLedgerIdentifier | 원장의 계약 주소입니다. |
| workflowFunctionName     | 워크플로 함수의 이름입니다. |
| 매개 변수               | 계약 생성을 위한 매개 변수 입력입니다. |
| connectionId             | 블록체인 연결에 대한 고유 식별자입니다. |
| messageSchemaVersion     | 메시징 스키마 버전입니다. |
| messageName              | **CreateContractActionRequest** |

예제:

``` json
{
    "requestId": "a5530932-9d6b-4eed-8623-441a647741d3",
    "userChainIdentifier": "0x9a8DDaCa9B7488683A4d62d0817E965E8f248398",
    "contractLedgerIdentifier": "0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe",
    "workflowFunctionName": "modify",
    "parameters": [
        {
            "name": "description",
            "value": "a 1969 dodge charger"
        },
        {
            "name": "price",
            "value": "12345"
        }
    ],
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractActionRequest"
}
```

Blockchain Workbench는 다음 필드가 포함된 응답을 반환합니다.

| **Name**              | **설명**                                                                   |
|-----------------------|-----------------------------------------------------------------------------------|
| requestId             | 클라이언트 제공 GUID입니다.|
| contractId            | Azure Blockchain Workbench 내 계약에 대한 고유 식별자입니다. |
| connectionId          | 블록체인 연결에 대한 고유 식별자입니다. |
| messageSchemaVersion  | 메시징 스키마 버전입니다. |
| messageName           | **CreateContractActionUpdate** |
| status                | 계약 작업 요청의 상태입니다. 가능한 값은 **제출**, **커밋됨**, **실패**입니다.                         |
| additionalInformation | 상태에 따라 제공되는 추가 정보입니다. |

Blockchain Workbench의 제출된 **계약 작업 만들기** 응답 예:

``` json
{
    "requestId": "a5530932-9d6b-4eed-8623-441a647741d3",
    "contractId": 105,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractActionUpdate",
    "status": "Submitted",
    "additionalInformation": { }
}
```

Blockchain Workbench의 커밋된 **계약 작업 만들기** 응답 예:

``` json
{
    "requestId": "a5530932-9d6b-4eed-8623-441a647741d3",
    "contractId": 105,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractActionUpdate",
    "status": "Committed"
    "additionalInformation": { }
}
```

요청이 성공하지 못한 경우 오류에 대한 세부 정보가 추가 정보에 포함됩니다.

``` json
{
    "requestId": "a5530932-9d6b-4eed-8623-441a647741d3",
    "contractId": 105,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractActionUpdate",
    "status": "Failure"
    "additionalInformation": {
        "errorCode": 4000,
        "errorMessage": "Contract action cannot be provisioned on connection."
    }
}
```

### <a name="input-api-error-codes-and-messages"></a>입력된 API 오류 코드 및 메시지

**오류 코드 4000: 잘못된 요청 오류**
- 잘못된 connectionId
- CreateUserRequest 역직렬화 실패
- CreateContractRequest 역직렬화 실패
- CreateContractActionRequest 역직렬화 실패
- 애플리케이션 {애플리케이션 이름으로 식별됨} 없음
- 애플리케이션 {애플리케이션 이름으로 식별됨}에 워크플로가 없음
- UserChainIdentifier가 없음
- 계약 {원장 식별자로 식별됨} 없음
- 계약 {원장 식별자로 식별됨}에 함수 {워크플로 함수 이름} 없음
- UserChainIdentifier가 없음

**오류 코드 4090: 충돌 오류**
- 사용자가 이미 존재함
- 계약이 이미 존재함
- 계약 작업이 이미 존재함

**오류 코드 5000: 내부 서버 오류**
- 예외 메시지

## <a name="event-notifications"></a>이벤트 알림

이벤트 알림은 Blockchain Workbench에서 발생하고 Workbench가 연결된 블록체인 네트워크를 사용자 및 다운스트림 시스템에 알리는 데 사용될 수 있습니다. 이벤트 알림을 코드에서 직접 사용하거나, 다운스트림 시스템으로의 데이터 흐름을 트리거하는 Logic Apps 및 Flow와 같은 도구에서 사용할 수도 있습니다.

수신될 수 있는 다양한 메시지의 세부 정보에 대해서는 [알림 메시지 참조](#notification-message-reference)를 참조하세요.

### <a name="consuming-event-grid-events-with-azure-functions"></a>Azure Functions에서 Event Grid 이벤트 사용

사용자가 Event Grid를 사용하여 Blockchain Workbench에서 발생하는 이벤트에 대한 알림을 받기 원할 경우 Azure Functions를 사용하여 Event Grid의 이벤트를 사용할 수 있습니다.

1. Azure Portal에서 **Azure 함수 앱**을 만듭니다.
2. 새 함수를 만듭니다.
3. Event Grid에 대한 템플릿을 찾습니다. 메시지를 읽기 위한 기본 템플릿 코드가 표시됩니다. 필요에 따라 코드를 수정합니다.
4. 함수를 저장합니다. 
5. Blockchain Workbench의 리소스 그룹에서 Event Grid를 선택합니다.

### <a name="consuming-event-grid-events-with-logic-apps"></a>Logic Apps에서 Event Grid 이벤트 사용

1.  Azure Portal에서 새 **Azure 논리 앱**을 만듭니다.
2.  포털에서 Azure 논리 앱을 열면 트리거를 선택하라는 메시지가 표시됩니다. **Azure Event Grid -- 리소스 이벤트가 발생할 때**를 선택합니다.
3. 워크플로 디자이너가 표시되면 로그인하라는 메시지가 표시됩니다.
4. 구독을 선택합니다. 리소스로 **Microsoft.EventGrid.Topics**를 지정합니다. Azure Blockchain Workbench 리소스 그룹의 리소스 이름에서 **리소스 이름**을 선택합니다.
5. Blockchain Workbench의 리소스 그룹에서 Event Grid를 선택합니다.

## <a name="using-service-bus-topics-for-notifications"></a>알림에 Service Bus 토픽 사용

Service Bus 토픽을 사용하여 Blockchain Workbench에서 발생하는 이벤트를 사용자에게 알릴 수 있습니다. 

1.  Workbench의 리소스 그룹 내에서 Service Bus로 이동합니다.
2.  **토픽**을 선택합니다.
3.  **워크벤츠-외부**를 선택합니다.
4.  이 토픽에 대한 새 구독을 만듭니다. 해당 키를 가져옵니다.
5.  이 구독에서 이벤트를 구독하는 프로그램을 만듭니다.

### <a name="consuming-service-bus-messages-with-logic-apps"></a>Logic Apps에서 Service Bus 메시지 사용

1. Azure Portal에서 새 **Azure 논리 앱**을 만듭니다.
2. 포털에서 Azure 논리 앱을 열면 트리거를 선택하라는 메시지가 표시됩니다. 검색 상자에 **Service Bus**를 입력하고 Service Bus와 수행하려는 상호 작용 유형에 적합한 트리거를 선택합니다. 예를 들어 **Service Bus -- 메시지가 항목 구독에 수신되는 경우(자동 완성)** 를 선택할 수 있습니다.
3. 워크플로 디자이너가 표시되면 Service Bus에 대한 연결 정보를 지정합니다.
4. 구독을 선택하고 **워크벤치-외부** 토픽을 지정합니다.
5. 이 트리거에서 메시지를 활용하는 애플리케이션에 대한 논리를 개발합니다.

## <a name="notification-message-reference"></a>알림 메시지 참조

**OperationName**에 따라, 알림 메시지는 다음 메시지 유형 중 하나를 갖습니다.

### <a name="accountcreated"></a>AccountCreated

새 계정을 지정한 체인에 추가하도록 요청되었음을 나타냅니다.

| 이름    | 설명  |
|----------|--------------|
| UserId  | 생성된 사용자의 ID입니다. |
| ChainIdentifier | 블록체인 네트워크에서 생성된 사용자의 주소입니다. Ethereum에서는 사용자의 **체인 내** 주소가 사용됩니다. |

``` csharp
public class NewAccountRequest : MessageModelBase
{
  public int UserID { get; set; }
  public string ChainIdentifier { get; set; }
}
```

### <a name="contractinsertedorupdated"></a>ContractInsertedOrUpdated

분산 원장에서 계약을 삽입 또는 업데이트하기 위한 요청이 수행되었음을 나타냅니다.

| 이름 | 설명 |
|-----|--------------|
| ChainID | 요청과 연결된 체인에 대한 고유 식별자입니다. |
| BlockId | 원장의 블록에 대한 고유 식별자입니다. |
| ContractId | 계약의 고유 식별자입니다. |
| ContractAddress |       원장의 계약 주소입니다. |
| TransactionHash  |     원장에 있는 트랜잭션의 해시입니다. |
| OriginatingAddress |   트랜잭션을 만든 사람의 주소입니다. |
| ActionName       |     작업의 이름입니다. |
| IsUpdate        |      업데이트되는지 식별합니다. |
| 매개 변수       |     작업에 전송된 매개 변수의 이름, 값 및 데이터 형식을 식별하는 개체 목록입니다. |
| TopLevelInputParams |  계약이 하나 이상의 다른 계약에 연결되어 있는 경우 최상위 계약의 매개 변수입니다. |

``` csharp
public class ContractInsertOrUpdateRequest : MessageModelBase
{
    public int ChainId { get; set; }
    public int BlockId { get; set; }
    public int ContractId { get; set; }
    public string ContractAddress { get; set; }
    public string TransactionHash { get; set; }
    public string OriginatingAddress { get; set; }
    public string ActionName { get; set; }
    public bool IsUpdate { get; set; }
    public List<ContractProperty> Parameters { get; set; }
    public bool IsTopLevelUpdate { get; set; }
    public List<ContractInputParameter> TopLevelInputParams { get; set; }
}
```

#### <a name="updatecontractaction"></a>UpdateContractAction

분산 원장에서 특정 계약에 대한 작업 실행 요청이 수행되었음을 나타냅니다.

| 이름                     | 설명                                                                                                                                                                   |
|--------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ContractActionId         | 이 계약 작업에 대한 고유 식별자입니다. |
| ChainIdentifier          | 체인의 고유 식별자입니다. |
| ConnectionId             | 연결에 대한 고유 식별자입니다. |
| UserChainIdentifier      | 블록체인 네트워크에서 생성된 사용자의 주소입니다. Ethereum에서 이 주소는 **체인 내** 주소입니다. |
| ContractLedgerIdentifier | 원장의 계약 주소입니다. |
| WorkflowFunctionName     | 워크플로 함수의 이름입니다. |
| WorkflowName             | 워크플로의 이름입니다. |
| WorkflowBlobStorageURL   | Blob Storage에 있는 계약의 URL입니다. |
| ContractActionParameters | 계약 작업에 대한 매개 변수입니다. |
| TransactionHash          | 원장에 있는 트랜잭션의 해시입니다. |
| 프로비전 상태      | 작업의 현재 프로비전 상태입니다.</br>0 – 생성됨</br>1 - 진행 중</br>2 - 완료</br> 완료는 성공적으로 추가되었다는 원장의 확인을 나타냅니다. |

```csharp
public class ContractActionRequest : MessageModelBase
{
    public int ContractActionId { get; set; }
    public int ConnectionId { get; set; }
    public string UserChainIdentifier { get; set; }
    public string ContractLedgerIdentifier { get; set; }
    public string WorkflowFunctionName { get; set; }
    public string WorkflowName { get; set; }
    public string WorkflowBlobStorageURL { get; set; }
    public IEnumerable<ContractActionParameter> ContractActionParameters { get; set; }
    public string TransactionHash { get; set; }
    public int ProvisioningStatus { get; set; }
}
```

### <a name="updateuserbalance"></a>UpdateUserBalance

특정 분산 원장에서 사용자 잔액에 대한 업데이트 요청이 수행되었음을 나타냅니다.

> [!NOTE]
> 이 메시지는 계정의 자금 지원이 필요한 원장에 대해서만 생성됩니다.
> 

| 이름    | 설명                              |
|---------|------------------------------------------|
| 주소 | 자금이 지원된 사용자의 주소입니다. |
| Balance | 사용자의 잔액입니다.         |
| ChainID | 체인의 고유 식별자입니다.     |


``` csharp
public class UpdateUserBalanceRequest : MessageModelBase
{
    public string Address { get; set; }
    public decimal Balance { get; set; }
    public int ChainID { get; set; }
}
```

### <a name="insertblock"></a>InsertBlock

메시지는 분산 원장에서 블록을 추가하기 위한 요청이 수행되었음을 나타냅니다.

| 이름           | 설명                                                            |
|----------------|------------------------------------------------------------------------|
| ChainId        | 블록이 추가된 체인의 고유 식별자입니다.             |
| BlockId        | Azure Blockchain Workbench 내 블록에 대한 고유 식별자입니다. |
| BlockHash      | 블록의 해시입니다.                                                 |
| BlockTimeStamp | 블록의 타임스탬프입니다.                                            |

``` csharp
public class InsertBlockRequest : MessageModelBase
{
    public int ChainId { get; set; }
    public int BlockId { get; set; }
    public string BlockHash { get; set; }
    public int BlockTimestamp { get; set; }
}
```

### <a name="inserttransaction"></a>InsertTransaction

메시지는 분산 원장의 트랜잭션 추가 요청에 대한 세부 정보를 제공합니다.

| 이름            | 설명                                                            |
|-----------------|------------------------------------------------------------------------|
| ChainId         | 블록이 추가된 체인의 고유 식별자입니다.             |
| BlockId         | Azure Blockchain Workbench 내 블록에 대한 고유 식별자입니다. |
| TransactionHash | 트랜잭션의 해시입니다.                                           |
| 원본            | 트랜잭션을 만든 사람의 주소입니다.                      |
| 받는 사람              | 트랜잭션의 의도된 받는 사람의 주소입니다.              |
| 값           | 트랜잭션에 포함된 값입니다.                                 |
| IsAppBuilderTx  | Blockchain Workbench 트랜잭션인지를 식별합니다.                         |

``` csharp
public class InsertTransactionRequest : MessageModelBase
{
    public int ChainId { get; set; }
    public int BlockId { get; set; }
    public string TransactionHash { get; set; }
    public string From { get; set; }
    public string To { get; set; }
    public decimal Value { get; set; }
    public bool IsAppBuilderTx { get; set; }
}
```

### <a name="assigncontractchainidentifier"></a>AssignContractChainIdentifier

계약의 체인 식별자 할당에 대한 세부 정보를 제공합니다. 예를 들어 Ethereum 블록체인에서 원장의 계약 주소입니다.

| 이름            | 설명                                                                       |
|-----------------|-----------------------------------------------------------------------------------|
| ContractId      | Azure Blockchain Workbench 내 계약에 대한 고유 식별자입니다. |
| ChainIdentifier | 체인의 계약에 대한 식별자입니다.                             |

``` csharp
public class AssignContractChainIdentifierRequest : MessageModelBase
{
    public int ContractId { get; set; }
    public string ChainIdentifier { get; set; }
}
```

## <a name="classes-used-by-message-types"></a>메시지 유형에서 사용되는 클래스

### <a name="messagemodelbase"></a>MessageModelBase

모든 메시지에 대한 기본 모델입니다.

| 이름          | 설명                          |
|---------------|--------------------------------------|
| OperationName | 작업 이름           |
| RequestId     | 요청의 고유 식별자입니다. |

``` csharp
public class MessageModelBase
{
    public string OperationName { get; set; }
    public string RequestId { get; set; }
}
```

### <a name="contractinputparameter"></a>ContractInputParameter

매개 변수의 이름, 값 및 형식을 포함합니다.

| 이름  | 설명                 |
|-------|-----------------------------|
| 이름  | 매개 변수의 이름입니다.  |
| 값 | 매개 변수의 값입니다. |
| type  | 매개 변수의 형식입니다.  |

``` csharp
public class ContractInputParameter
{
    public string Name { get; set; }
    public string Value { get; set; }
    public string Type { get; set; }
}
```

#### <a name="contractproperty"></a>ContractProperty

속성의 ID, 이름, 값 및 형식을 포함합니다.

| 이름  | 설명                |
|-------|----------------------------|
| Id    | 속성의 ID입니다.    |
| 이름  | 속성의 이름  |
| 값 | 속성의 값입니다. |
| type  | 속성의 유형입니다.  |

``` csharp
public class ContractProperty
{
    public int Id { get; set; }
    public string Name { get; set; }
    public string Value { get; set; }
    public string DataType { get; set; }
}
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [스마트 계약 통합 패턴](integration-patterns.md)