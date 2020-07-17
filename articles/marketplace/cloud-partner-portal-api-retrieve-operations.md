---
title: 작업 API 검색-Azure Marketplace
description: API를 통해 제품의 모든 작업을 검색 하거나 지정 된 operationId에 대해 특정 작업을 가져옵니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 04/08/2020
ms.openlocfilehash: c0611cb3cbc24e2b105cdef134e30a7c2fbdd445
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86113465"
---
# <a name="retrieve-operations"></a>작업 검색

> [!NOTE]
> Cloud 파트너 포털 API는 파트너 센터와 통합되며 제품을 파트너 센터로 마이그레이션한 후에도 계속 작동합니다. 통합에는 작은 변경 사항이 도입되었습니다. 파트너 센터로 마이그레이션한 후 코드가 계속 작동 하는지 확인 하려면 [CLOUD 파트너 포털 API 참조](./cloud-partner-portal-api-overview.md) 에 나열 된 변경 내용을 검토 합니다.

제품에 대한 모든 작업을 검색하거나 지정된 operationId의 특정 작업을 가져옵니다. 클라이언트는 쿼리 매개 변수를 사용하여 실행 중인 작업을 필터링할 수 있습니다.

``` https

  GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/submissions/?api-version=2017-10-31&status=<filteredStatus>

  GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/operations/<operationId>?api-version=2017-10-31

```


## <a name="uri-parameters"></a>URI 매개 변수

|  **이름**          |      **설명**                                                                                           | **데이터 형식** |
|  ----------------  |     --------------------------------------------------------------------------------------------------------   |  -----------  |
|  publisherId       |  게시자 식별자입니다(예: `Contoso`).                                                                   |  String       |
|  offerId           |  제안 식별자입니다.                                                                                              |  String       |
|  operationId       |  제품에 대한 작업을 고유하게 식별하는 GUID입니다. operationId는 이 API를 사용하여 검색할 수 있으며, [제품 게시](./cloud-partner-portal-api-publish-offer.md) API와 같은 장기 실행 작업에 대한 응답의 HTTP 헤더에도 반환됩니다.  |   Guid   |
|  api-version       | 최신 버전 API |    날짜      |
|  |  |  |

## <a name="header"></a>헤더


|  **이름**          |  **값**           |
|  ---------------   | -------------------- |
|  콘텐츠 형식      | `application/json`   |
|  권한 부여     | `Bearer YOUR_TOKEN`  |
|  |  |


## <a name="body-example"></a>본문 예제

### <a name="response"></a>응답

#### <a name="get-operations"></a>작업 가져오기

``` json
    [
        {
            "id": "5a63deb5-925b-4ee0-938b-7c86fbf287c5",
            "offerId": "56615b67-2185-49fe-80d2-c4ddf77bb2e8",
            "offerVersion": 1,
            "offerTypeId": "microsoft-azure-virtualmachines",
            "publisherId": "contoso",
            "submissionType": "publish",
            "submissionState": "running",
            "publishingVersion": 2,
            "slot": "staging",
            "version": 636576975611768314,
            "definition": {
                "metadata": {
                    "emails": "jdoe@contoso.com"
                }
            },
            "changedTime": "2018-03-26T21:46:01.179948Z"
        }
    ]
```

#### <a name="get-operation"></a>작업 가져오기

``` json
    [
        {
            "status" : "running",
            "messages" : [],
            "publishingVersion" : 2,
            "offerVersion" : 1,
            "cancellationRequestState": "canCancel",
            "steps": [
                        {
                            "estimatedTimeFrame": "< 15 min",
                            "id": "displaydummycertify",
                            "stepName": "Validate Pre-Requisites",
                            "description": "Offer settings provided are validated",
                            "status": "complete",
                            "messages": 
                            [
                                {
                                    "messageHtml": "Step completed.",
                                    "level": "information",
                                    "timestamp": "2017-03-28T19:50:36.500052Z"
                                }
                            ],
                            "progressPercentage": 100
                        },
                        {
                            "estimatedTimeFrame": "< 5 day",
                            "id": "displaycertify",
                            "stepName": "Certification",
                            "description": "Your offer is analyzed by our certification systems for issues.",
                            "status": "blocked",
                            "messages": 
                            [
                                {
                                    "messageHtml": "No virtual machine image was found for the plan contoso.",
                                    "level": "error",
                                    "timestamp": "2017-03-28T19:50:39.5506018Z"
                                },
                                {
                                    "messageHtml": "This step has not started yet.",
                                    "level": "information",
                                    "timestamp": "2017-03-28T19:50:39.5506018Z"
                                }
                            ],
                            "progressPercentage": 0
                        },
                        {
                            "estimatedTimeFrame": "< 1 day",
                            "id": "displayprovision",
                            "stepName": "Provisioning",
                            "description": "Your virtual machine is being replicated in our production systems.",
                            "status": "notStarted",
                            "messages": [],
                            "progressPercentage": 0
                        },
                        {
                            "estimatedTimeFrame": "< 1 hour",
                            "id": "displaypackage",
                            "stepName": "Packaging and Lead Generation Registration",
                            "description": "Your virtual machine is packaged for being shown to your customers. Additionally, we hookup our lead generation systems to send leads for your offer.",
                            "status": "notStarted",
                            "messages": [],
                            "progressPercentage": 0
                        },
                        {
                            "id": "publisher-signoff",
                            "stepName": "Publisher signoff",
                            "description": "Offer is available to preview. Ensure that everything looks good before making your offer live.",
                            "status": "notStarted",
                            "messages": [],
                            "progressPercentage": 0
                        },
                        {
                            "estimatedTimeFrame": "~2-5 days",
                            "id": "live",
                            "stepName": "Live",
                            "description": "Offer is publicly visible and is available for purchase.",
                            "status": "notStarted",
                            "messages": [],
                            "progressPercentage": 0
                        }
                    ],
                "previewLinks": [],
                "liveLinks": [],
            }
        }
    ]
```

### <a name="response-body-properties"></a>응답 본문 속성

|  **이름**                    |  **설명**                                                                                  |
|  --------------------        |  ------------------------------------------------------------------------------------------------ |
|  id                          | 작업을 고유하게 식별하는 GUID입니다.                                                       |
|  submissionType              | 제품에 대해 보고되는 작업 유형(예: `Publish/GoLive`)을 식별합니다.      |
|  createdDateTime             | 작업이 만들어진 UTC 날짜/시간입니다.                                                       |
|  lastActionDateTime          | 작업에 대한 마지막 업데이트가 수행된 UTC 날짜/시간입니다.                                       |
|  상태                      | 작업의 상태 `not started` \| `running` \| `failed` \| `completed` 입니다. 한 번에 하나의 작업만 상태 `running`을 가질 수 있습니다. |
|  오류                       | 실패한 작업에 대한 오류 메시지입니다.                                                               |
|  |  |

### <a name="response-step-properties"></a>응답 단계 속성

|  **이름**                    |  **설명**                                                                                  |
|  --------------------        |  ------------------------------------------------------------------------------------------------ |
| estimatedTimeFrame | 이 작업의 예상 기간입니다. |
| id | 단계 프로세스에 대 한 고유 식별자입니다. |
| description | 단계에 대한 설명입니다. |
| stepName | 단계의 이름입니다. |
| 상태 | 단계의 상태 이며 다음 중 하나 `notStarted` \| `running` \| `failed` \| 입니다.`completed` |
| messages | 단계를 수행 하는 동안 발생 한 알림 또는 경고입니다. 문자열 배열 |
| System.componentmodel.progresschangedeventargs.progresspercentage | 단계의 진행을 나타내는 0에서 100 사이의 정수입니다. |
| | |

### <a name="response-status-codes"></a>응답 상태 코드

| **코드**  |   **설명**                                                                                  |
|  -------- |   -------------------------------------------------------------------------------------------------|
|  200      | `OK` - 요청이 성공적으로 처리되었으며 요청한 작업이 반환되었습니다.        |
|  400      | `Bad/Malformed request` - 오류 응답 본문에 자세한 정보가 들어 있을 수 있습니다.                    |
|  403      | `Forbidden` - 클라이언트는 지정된 네임스페이스에 액세스할 수 없습니다.                          |
|  404      | `Not found` - 지정한 엔터티가 없습니다.                                                 |
|  |  |
