---
title: Azure Blockchain Workbench 구성 참조
description: Azure Blockchain Workbench 응용 프로그램 구성 개요.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 3/20/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: 6318b6aafda972242239ce91abb0f67a15721e1e
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2018
---
# <a name="azure-blockchain-workbench-configuration-reference"></a>Azure Blockchain Workbench 구성 참조

 Azure Blockchain Workbench 응용 프로그램은 구성 메타데이터 및 스마트 계약 코드에 의해 정의된 다자간 워크플로입니다. 구성 메타데이터는 블록체인 응용 프로그램의 높은 수준 워크플로 및 상호 작용 모델을 정의합니다. 스마트 계약은 블록체인 응용 프로그램의 비즈니스 논리를 정의합니다. Workbench는 구성 및 스마트 계약 코드를 사용하여 블록체인 응용 프로그램 사용자 환경을 생성합니다.

구성 메타데이터는 각 블록체인 응용 프로그램에 대한 다음 정보를 지정합니다. 

* 블록체인 응용 프로그램의 이름 및 설명
* 블록체인 응용 프로그램 내에서 작업하거나 참여할 수 있는 사용자에 대한 고유한 역할
* 하나 이상의 워크플로가 있어야 합니다. 각 워크플로는 비즈니스 논리의 흐름을 제어하는​상태 시스템의 역할을 합니다. 워크플로는 서로 독립적이거나 또는 상호 작용할 수 있습니다.

각 정의된 워크플로는 다음을 지정합니다.

* 워크플로의 이름과 설명
* 워크플로의 상태.  각 상태는 비즈니스 논리의 제어 흐름에서 한 단계입니다. 
* 다음 상태로 전환하는 작업
* 각 작업을 시작하도록 허용된 사용자 역할
* 코드 파일에서 비즈니스 논리를 나타내는 스마트 계약

## <a name="application"></a>응용 프로그램

블록체인 응용 프로그램은 구성 메타데이터, 워크플로 및 응용 프로그램 내에서 작업하거나 참여할 수 있는 사용자 역할을 포함합니다.

| 필드 | 설명 | 필수 |
|-------|-------------|:--------:|
| ApplicationName | 고유 응용 프로그램 이름. 해당 스마트 계약은 적용 가능한 계약 클래스에 대해 동일한 **ApplicationName**을 사용해야 합니다.  | 예 |
| DisplayName | 응용 프로그램의 친숙한 표시 이름. | 예 |
| 설명 | 응용 프로그램에 대한 설명. | 아니오 |
| ApplicationRoles | [ApplicationRoles](#application-roles)의 콜렉션. 응용 프로그램 내에서 작업하거나 참여할 수 있는 사용자 역할.  | 예 |
| 워크플로 | [워크플로](#workflows) 컬렉션. 각 워크플로는 비즈니스 논리의 흐름을 제어하는​상태 시스템의 역할을 합니다. | 예 |

예를 들어 [구성 파일 예제](#configuration-file-example)를 참조하세요.

## <a name="workflows"></a>워크플로

응용 프로그램의 비즈니스 논리는 작업을 수행하면 비즈니스 논리의 흐름이 한 상태에서 다른 상태로 이동하는 상태 시스템으로 모델링 될 수 있습니다. 워크플로는 이러한 상태 및 작업의 컬렉션입니다. 각 워크플로는 코드 파일에서 비즈니스 논리를 나타내는 하나 이상의 스마트 계약으로 구성됩니다. 실행 가능한 계약은 워크플로의 한 인스턴스.

| 필드 | 설명 | 필수 |
|-------|-------------|:--------:|
| Name | 고유 워크플로 이름. 해당 스마트 계약은 적용 가능한 계약 클래스에 대해 동일한 **Name**을 사용해야 합니다. | 예 |
| DisplayName | 워크플로의 친숙한 표시 이름. | 예 |
| 설명 | 워크플로에 대한 설명. | 아니오 |
| 개시 장치 | [ApplicationRoles](#application-roles)의 콜렉션. 워크플로에서 계약을 만들 권한이 있는 사용자에게 할당된 역할. | 예 |
| StartState | 워크플로의 초기 상태 이름. | 예 |
| properties | [식별자](#identifiers)의 콜렉션. 사용자 경험 도구에서 오프 체인으로 읽히거나 또는 시각화 될 수 있는 데이터를 나타냅니다. | 예 |
| 생성자 | 워크플로의 인스턴스를 만들기 위한 입력 매개 변수를 정의합니다. | 예 |
| Functions | 워크플로에서 실행할 수 있는 [함수](#functions) 컬렉션. | 예 |
| 상태 | 워크플로 [상태](#states)의 컬렉션. | 예 |

예를 들어 [구성 파일 예제](#configuration-file-example)를 참조하세요.

## <a name="type"></a>type

지원되는 데이터 형식.

| type | 설명 |
|-------|-------------|
| address  | *계약* 또는 *사용자*와 같은 블록체인 주소 유형 |
| bool     | Boolean 데이터 형식 |
| 계약 | 계약 유형의 주소 |
| int      | Integer 데이터 형식 |
| money    | Money 데이터 형식 |
| state    | 워크플로 상태 |
| string   | String 데이터 형식 |
| 사용자     | 사용자 유형의 주소 |
| 실시간     | Time 데이터 형식 |
|`[ Application Role Name ]`| 응용 프로그램 역할에 지정된 임의의 이름. 사용자를 그 역할 유형에 해당하는 경우로 제한합니다. |

## <a name="constructor"></a>생성자

워크플로 인스턴스에 대한 입력 매개 변수를 정의합니다.

| 필드 | 설명 | 필수 |
|-------|-------------|:--------:|
| 매개 변수 | 스마트 계약을 시작하는 데 필요한 [식별자](#identifiers) 컬렉션. | 예 |

### <a name="constructor-example"></a>생성자 예제

``` json
{
  "Parameters": [
    {
      "Name": "description",
      "Description": "The description of this asset",
      "DisplayName": "Description",
      "Type": {
        "Name": "string"
      }
    },
    {
      "Name": "price",
      "Description": "The price of this asset",
      "DisplayName": "Price",
      "Type": {
        "Name": "money"
      }
    }
  ]
}

```

## <a name="functions"></a>Functions

워크플로에서 실행할 수 있는 함수를 정의합니다.

| 필드 | 설명 | 필수 |
|-------|-------------|:--------:|
| Name | 함수의 고유한 이름. 해당 스마트 계약은 적용 가능한 함수에 대해 동일한 **Name**을 사용해야 합니다. | 예 |
| DisplayName | 함수의 친숙한 표시 이름. | 예 |
| 설명 | 함수에 대한 설명 | 아니오 |
| 매개 변수 | 함수의 매개 변수에 해당하는 [식별자](#identifiers)의 컬렉션. | 예 |

### <a name="functions-example"></a>함수 예제

``` json
"Functions": [
  {
    "Name": "Modify",
    "DisplayName": "Modify",
    "Description": "Modify the description/price attributes of this asset transfer instance",
    "Parameters": [
      {
        "Name": "description",
        "Description": "The new description of the asset",
        "DisplayName": "Description",
        "Type": {
          "Name": "string"
        }
      },
      {
        "Name": "price",
        "Description": "The new price of the asset",
        "DisplayName": "Price",
        "Type": {
          "Name": "money"
        }
      }
    ]
  },
  {
    "Name": "Terminate",
    "DisplayName": "Terminate",
    "Description": "Used to cancel this particular instance of asset transfer",
    "Parameters": []
  }
]

```

## <a name="states"></a>상태

워크플로 내에서 고유 상태의 컬렉션. 각 상태는 비즈니스 논리의 제어 흐름에서 한 단계를 캡처합니다. 

| 필드 | 설명 | 필수 |
|-------|-------------|:--------:|
| Name | 상태의 고유 이름. 해당 스마트 계약은 적용 가능한 상태에 대해 동일한 **Name**을 사용해야 합니다. | 예 |
| DisplayName | 상태의 친숙한 표시 이름. | 예 |
| 설명 | 상태에 대한 설명. | 아니오 |
| PercentComplete | Blockchain Workbench 사용자 인터페이스에 표시되는 정수 값은 비즈니스 논리 제어 흐름 내에서의 진행 상황을 보여줍니다. | 예 |
| Style | 상태가 성공 또는 실패 상태인지 여부를 나타내는 시각적 힌트. 유효한 값은 다음 두 가지입니다. `Success` 또는 `Failure`. | 예 |
| 전환 | 현재 상태에서 다음 상태 집합으로 [전환](#transitions) 가능한 컬렉션. | 아니오 |

### <a name="states-example"></a>상태 예제

``` json
"States": [
    {
      "Name": "Active",
      "DisplayName": "Active",
      "Description": "The initial state of the asset transfer workflow",
      "PercentComplete": 20,
      "Style": "Success",
      "Transitions": [
        {
          "AllowedRoles": [],
          "AllowedInstanceRoles": [ "InstanceOwner" ],
          "Description": "Cancels this instance of asset transfer",
          "Function": "Terminate",
          "NextStates": [ "Terminated" ],
          "DisplayName": "Terminate Offer"
        },
        {
          "AllowedRoles": [ "Buyer" ],
          "AllowedInstanceRoles": [],
          "Description": "Make an offer for this asset",
          "Function": "MakeOffer",
          "NextStates": [ "OfferPlaced" ],
          "DisplayName": "Make Offer"
        },
        {
          "AllowedRoles": [],
          "AllowedInstanceRoles": [ "InstanceOwner" ],
          "Description": "Modify attributes of this asset transfer instance",
          "Function": "Modify",
          "NextStates": [ "Active" ],
          "DisplayName": "Modify"
        }
      ]
    },
    {
      "Name": "Accepted",
      "DisplayName": "Accepted",
      "Description": "Asset transfer process is complete",
      "PercentComplete": 100,
      "Style": "Success",
      "Transitions": []
    },
    {
      "Name": "Terminated",
      "DisplayName": "Terminated",
      "Description": "Asset transfer has been cancelled",
      "PercentComplete": 100,
      "Style": "Failure",
      "Transitions": []
    }
  ]
```

## <a name="transitions"></a>전환

다음 상태에 사용 가능한 작업. 하나 이상의 사용자 역할은 각 상태에서 하나의 작업을 수행할 수 있으며, 그 곳에서 작업은 워크플로에서 상태를 또 다른 상태로 전환할 수 있습니다. 

| 필드 | 설명 | 필수 |
|-------|-------------|:--------:|
| AllowedRoles | 전환을 시작하도록 허용된 응용 프로그램 역할 목록. 지정된 역할의 모든 사용자가 작업을 수행할 수 있습니다. | 아니오 |
| AllowedInstanceRoles | 전환을 시작하도록 허용된 스마트 계약에 참여하거나 지정된 사용자 역할 목록. 인스턴스 역할은 워크플로 내의 **속성**에서 정의됩니다. 이러한 사용자는 어느 역할 유형의 모든 사용자와는 반대로 스마트 계약서에 참여하거나 지정된 사용자를 나타냅니다. | 아니오 |
| DisplayName | 전환의 친숙한 표시 이름. | 예 |
| 설명 | 전환에 대한 설명. | 아니오 |
| 함수 | 전환을 시작하는 함수의 이름. | 예 |
| NextStates | 성공적인 전환 후 잠재적인 다음 상태의 컬렉션. | 예 |

### <a name="transitions-example"></a>전환 예제

``` json
"Transitions": [
  {
    "AllowedRoles": [],
    "AllowedInstanceRoles": [ "InstanceOwner" ],
    "Description": "Cancels this instance of asset transfer",
    "Function": "Terminate",
    "NextStates": [ "Terminated" ],
    "DisplayName": "Terminate Offer"
  },
  {
    "AllowedRoles": [ "Buyer" ],
    "AllowedInstanceRoles": [],
    "Description": "Make an offer for this asset",
    "Function": "MakeOffer",
    "NextStates": [ "OfferPlaced" ],
    "DisplayName": "Make Offer"
  },
  {
    "AllowedRoles": [],
    "AllowedInstanceRoles": [ "InstanceOwner" ],
    "Description": "Modify attributes of this asset transfer instance",
    "Function": "Modify",
    "NextStates": [ "Active" ],
    "DisplayName": "Modify"
  }
]

```

## <a name="application-roles"></a>응용 프로그램 역할

응용 프로그램 역할은 응용 프로그램 내에서 작업하거나 참여하기를 원하는 사용자에게 할당할 수 있는 역할 집합을 정의합니다. 응용 프로그램 역할을 사용하여 블록체인 응용 프로그램 및 해당 워크플로 내의 작업 및 참여를 제한할 수 있습니다. 

| 필드 | 설명 | 필수 |
|-------|-------------|:--------:|
| Name | 응용 프로그램 역할의 고유한 이름입니다. 해당 스마트 계약은 적용 가능한 역할에 대해 동일한 **Name**을 사용해야 합니다. 기본 형식 이름은 예약되어 있습니다. [형식](#type)과 같은 이름으로 응용 프로그램 역할의 이름을 지정할 수 없습니다.| 예 |
| 설명 | 응용 프로그램 역할에 대한 설명. | 아니오 |

### <a name="application-roles-example"></a>응용 프로그램 역할 예제

``` json
"ApplicationRoles": [
  {
    "Name": "Appraiser",
    "Description": "User that signs off on the asset price"
  },
  {
    "Name": "Buyer",
    "Description": "User that places an offer on an asset"
  }
]
```
## <a name="identifiers"></a>식별자

식별자는 워크플로 속성, 생성자 및 함수 매개 변수를 설명하는 데 사용되는 정보의 컬렉션을 나타냅니다. 

| 필드 | 설명 | 필수 |
|-------|-------------|:--------:|
| Name | 속성 또는 매개 변수의 고유 이름. 해당 스마트 계약은 적용 가능한 속성 또는 매개 변수에 대해 동일한 **Name**을 사용해야 합니다. | 예 |
| DisplayName | 속성 또는 매개 변수의 친숙한 표시 이름. | 예 |
| 설명 | 속성 또는 매개 변수에 대한 설명. | 아니오 |

### <a name="identifiers-example"></a>식별자 예제

``` json
"Properties": [
  {
    "Name": "State",
    "DisplayName": "State",
    "Description": "Holds the state of the contract",
    "Type": {
      "Name": "state"
    }
  },
  {
    "Name": "Description",
    "DisplayName": "Description",
    "Description": "Describes the asset being sold",
    "Type": {
      "Name": "string"
    }
  }
]
```

## <a name="configuration-file-example"></a>구성 파일 예제

다음 예제에서는 요청자가 요청을 보내고 응답자가 요청에 응답을 보내는 기본 요청-응답 응용 프로그램을 정의합니다.

``` json
{
  "ApplicationName": "HelloBlockchain",
  "DisplayName": "Hello, Blockchain!",
  "Description": "A simple application to send request and get response",
  "ApplicationRoles": [
    {
      "Name": "Requestor",
      "Description": "A person sending a request."
    },
    {
      "Name": "Responder",
      "Description": "A person responding to a request"
    }
  ],
  "Workflows": [
    {
      "Name": "RequestResponse",
      "DisplayName": "Request Response",
      "Description": "A simple workflow to send a request and receive a response.",
      "Initiators": [ "Requestor" ],
      "StartState": "Request",
      "Properties": [
        {
          "Name": "State",
          "DisplayName": "State",
          "Description": "Holds the state of the contract.",
          "Type": {
            "Name": "state"
          }
        },
        {
          "Name": "Requestor",
          "DisplayName": "Requestor",
          "Description": "A person sending a request.",
          "Type": {
            "Name": "Requestor"
          }
        },
        {
          "Name": "Responder",
          "DisplayName": "Responder",
          "Description": "A person sending a response.",
          "Type": {
            "Name": "Responder"
          }
        },
        {
          "Name": "RequestMessage",
          "DisplayName": "Request Message",
          "Description": "A request message.",
          "Type": {
            "Name": "string"
          }
        },
        {
          "Name": "ResponseMessage",
          "DisplayName": "Response Message",
          "Description": "A response message.",
          "Type": {
            "Name": "string"
          }
        }
      ],
      "Constructor": {
        "Parameters": [
          {
            "Name": "message",
            "Description": "...",
            "DisplayName": "Request Message",
            "Type": {
              "Name": "string"
            }
          }
        ]
      },
      "Functions": [
        {
          "Name": "SendRequest",
          "DisplayName": "Request",
          "Description": "...",
          "Parameters": [
            {
              "Name": "requestMessage",
              "Description": "...",
              "DisplayName": "Request Message",
              "Type": {
                "Name": "string"
              }
            }
          ]
        },
        {
          "Name": "SendResponse",
          "DisplayName": "Response",
          "Description": "...",
          "Parameters": [
            {
              "Name": "responseMessage",
              "Description": "...",
              "DisplayName": "Response Message",
              "Type": {
                "Name": "string"
              }
            }
          ]
        }
      ],
      "States": [
        {
          "Name": "Request",
          "DisplayName": "Request",
          "Description": "...",
          "PercentComplete": 50,
          "Value": 0,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": ["Responder"],
              "AllowedInstanceRoles": [],
              "Description": "...",
              "Function": "SendResponse",
              "NextStates": [ "Respond" ],
              "DisplayName": "Send Response"
            }
          ]
        },
        {
          "Name": "Respond",
          "DisplayName": "Respond",
          "Description": "...",
          "PercentComplete": 90,
          "Value": 1,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": ["Requestor"],
              "Description": "...",
              "Function": "SendRequest",
              "NextStates": [ "Request" ],
              "DisplayName": "Send Request"
            }
          ]
        }
      ]
    }
  ]
}
```
## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure Blockchain Workbench 배포](blockchain-workbench-deploy.md)

