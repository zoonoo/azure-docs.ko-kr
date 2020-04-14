---
title: 운영 API 검색 | Azure 마켓플레이스
description: 제품에 대한 모든 작업을 검색하거나 지정된 operationId의 특정 작업을 가져옵니다.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: 93b2ca700a987b86aedfdae55d58540c8ffe84ed
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81255876"
---
# <a name="retrieve-operations"></a>작업 검색

> [!NOTE]
> 클라우드 파트너 포털 API는 파트너 센터와 통합되며 오퍼가 파트너 센터로 마이그레이션된 후에도 계속 작동합니다. 통합은 작은 변화를 도입합니다. [Cloud 파트너 포털 API 참조에](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview) 나열된 변경 내용을 검토하여 파트너 센터로 마이그레이션한 후에도 코드가 계속 작동하는지 확인합니다.

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
|  api-version       | 최신 버전 API |    Date      |
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
|  상태                      | 작업 상태 중 `not started` \| `running` \| `failed` \| `completed`하나 한 번에 하나의 작업만 상태 `running`을 가질 수 있습니다. |
|  error                       | 실패한 작업에 대한 오류 메시지입니다.                                                               |
|  |  |

### <a name="response-step-properties"></a>응답 단계 속성

|  **이름**                    |  **설명**                                                                                  |
|  --------------------        |  ------------------------------------------------------------------------------------------------ |
| estimatedTimeFrame | 이 작업의 예상 기간 |
| id | 단계 프로세스의 고유 식별자 |
| description | 단계에 대한 설명입니다. |
| stepName | 스텝의 친숙한 이름 |
| 상태 | 단계의 상태 중 `notStarted` \| `running` \| `failed` \| 하나`completed` |
| messages | 단계 중에 발생하는 모든 알림 또는 경고입니다. 문자열 배열 |
| 진행률 | 단계의 진행을 나타내는 정수 0에서 100까지 |
| | |

### <a name="response-status-codes"></a>응답 상태 코드

| **코드**  |   **설명**                                                                                  |
|  -------- |   -------------------------------------------------------------------------------------------------|
|  200      | `OK` - 요청이 성공적으로 처리되었으며 요청한 작업이 반환되었습니다.        |
|  400      | `Bad/Malformed request` - 오류 응답 본문에 자세한 정보가 들어 있을 수 있습니다.                    |
|  403      | `Forbidden` - 클라이언트는 지정된 네임스페이스에 액세스할 수 없습니다.                          |
|  404      | `Not found` - 지정한 엔터티가 없습니다.                                                 |
|  |  |
