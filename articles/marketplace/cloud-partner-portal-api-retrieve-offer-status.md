---
title: 제안 상태 검색-Azure Marketplace
description: API를 제공 하 여 제품의 현재 상태를 검색 합니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: mingshen-ms
ms.author: mingshen
ms.date: 07/14/2020
ms.openlocfilehash: 336f23f83c33bcee1887d0e41710e686b794a663
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87272014"
---
# <a name="retrieve-offer-status"></a>제안 상태 검색

> [!NOTE]
> Cloud 파트너 포털 Api는와 통합 되며 파트너 센터에서 계속 작업 합니다. 전환에는 작은 변화가 도입 됩니다. [CLOUD 파트너 포털 API 참조](./cloud-partner-portal-api-overview.md) 에 나열 된 변경 내용을 검토 하 여 파트너 센터로 전환 된 후 코드가 계속 작동 하는지 확인 합니다. CPP Api는 파트너 센터로 전환 하기 전에 이미 통합 된 기존 제품에만 사용 해야 합니다. 새 제품은 파트너 센터 제출 Api를 사용 해야 합니다.

제안의 현재 상태를 검색합니다.

  `GET  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/status?api-version=2017-10-31`

## <a name="uri-parameters"></a>URI 매개 변수

|  **이름**       |   **설명**                            |  **데이터 형식** |
|  -------------  |  ------------------------------------------  |  ------------  |
|  publisherId    | 게시자 식별자입니다(예: `Contoso`).  |     문자열     |
|  offerId        | 제안을 고유하게 식별하는 GUID입니다.      |     문자열     |
|  api-version    | 최신 버전 API                        |     Date       |
|  |  |

## <a name="header"></a>헤더


|  Name           |  값               |
|  -------------  | -------------------  |
|  콘텐츠 형식   |  `application/json`  |
|  권한 부여  | `Bearer YOUR_TOKEN`  |
|  |  |

## <a name="body-example"></a>본문 예제

### <a name="response"></a>응답

``` json
  {
      "status": "succeeded",
      "messages": [],
      "steps": [
      {
          "estimatedTimeFrame": "< 15 min",
          "id": "displaydummycertify",
          "stepName": "Validate Pre-Requisites",
          "description": "Offer settings provided are validated.",
          "status": "complete",
          "messages": [
              {
                  "messageHtml": "Step completed.",
                  "level": "information",
                  "timestamp": "2018-03-16T17:50:45.7215661Z"
              }
          ],       
          "progressPercentage": 100
      },
      {
          "estimatedTimeFrame": "~2-3 days",
          "id": "displaycertify",
          "stepName": "Certification",
          "description": "Your offer is analyzed by our certification systems for issues.",
          "status": "notStarted",
          "messages": [],
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
          "description": "Your virtual machine is being packaged for customers. Additionally, lead systems are being configured and set up.",
          "status": "notStarted",
          "messages": [],
          "progressPercentage": 0
      },
      {
          "estimatedTimeFrame": "< 1 hour",
          "id": "publisher-signoff",
          "stepName": "Publisher signoff",
          "description": "Offer is available to preview. Ensure that everything looks good before making your offer live.",
          "status": "complete",
          "messages": [],
          "progressPercentage": 0
      },
      {
          "estimatedTimeFrame": "~2-5 days",
          "id": "live",
          "stepName": "Live",
          "description": "Offer is publicly visible and is available for purchase.",
          "status": "complete",
          "messages": [],
          "progressPercentage": 0
      }
      ],
      "previewLinks": [],
      liveLinks": [],
  }
```

### <a name="response-body-properties"></a>응답 본문 속성

|  **이름**             |    **설명**                                                                             |
| --------------------  |   -------------------------------------------------------------------------------------------- |
|  상태               | 제안의 상태입니다. 가능한 값 목록을 보려면 아래 [제안 상태](#offer-status)를 참조하세요. |
|  messages             | 제안과 연결된 메시지 배열                                                    |
|  단계                | 제안 게시 동안 제안이 거치는 단계의 배열입니다.                      |
|  estimatedTimeFrame   | 이 단계를 완료하는 데 걸리는 예상 시간(일반 형식)입니다.                       |
|  id                   | 단계의 식별자입니다.                                                                         |
|  stepName             | 단계의 이름입니다.                                                                               |
|  description          | 단계에 대한 설명입니다.                                                                        |
|  상태               | 단계의 상태입니다. 가능한 값 목록을 보려면 아래의 [단계 상태](#step-status)를 참조하세요.    |
|  messages             | 단계와 관련된 메시지의 배열입니다.                                                          |
|  processPercentage    | 단계의 완료율입니다.                                                              |
|  previewLinks         | *현재 구현되지 않음*                                                                    |
|  liveLinks            | *현재 구현되지 않음*                                                                    |
|  notificationEmails   | 파트너 센터로 마이그레이션된 제안에는 사용 되지 않습니다. 마이그레이션된 제안에 대 한 알림 전자 메일은 계정 설정의 판매자 연락처 정보에 지정 된 메일에 전송 됩니다.<br><br>마이그레이션되지 않은 제품의 경우 작업 진행 상황에 대 한 알림이 표시 되는 쉼표로 구분 된 전자 메일 주소 목록입니다.        |
|  |  |

### <a name="response-status-codes"></a>응답 상태 코드

| **코드** |   **설명**                                                                                 |
| -------  |   ----------------------------------------------------------------------------------------------- |
|  200     |  `OK` - 요청이 성공적으로 처리되었으며 제안의 현재 상태가 반환되었습니다. |
|  400     | `Bad/Malformed request` - 오류 응답 본문에 자세한 정보가 들어 있을 수 있습니다.                 |
|  404     | `Not found` - 지정된 엔터티가 존재하지 않습니다.                                                |
|  |  |

### <a name="offer-status"></a>제안 상태

|  **이름**                    |    **설명**                                       |
|  --------------------------  |  ------------------------------------------------------  |
|  NeverPublished              | 제안이 게시된 적이 없습니다.                          |
|  NotStarted                  | 새 제안이지만 시작되지 않았습니다.                            |
|  WaitingForPublisherReview   | 제안이 게시자 승인을 기다리고 있습니다.                 |
|  실행 중                     | 제안 제출을 처리 중입니다.                     |
|  성공                   | 제안 제출 처리를 완료했습니다.               |
|  취소됨                    | 제안 제출이 취소되었습니다.                           |
|  실패                      | 제안 제출에 실패했습니다.                                 |
|  |  |

### <a name="step-status"></a>단계 상태

|  **이름**                    |    **설명**                           |
|  -------------------------   |  ------------------------------------------  |
|  NotStarted                  | 단계가 시작되지 않았습니다.                        |
|  InProgress                  | 단계를 실행 중입니다.                             |
|  WaitingForPublisherReview   | 단계가 게시자 승인을 기다리고 있습니다.      |
|  WaitingForApproval          | 단계가 프로세스 승인을 기다리고 있습니다.        |
|  차단                     | 단계가 차단되었습니다.                             |
|  거부됨                    | 단계가 거부되었습니다.                            |
|  완료                    | 단계가 완료되었습니다.                            |
|  취소됨                    | 단계가 취소되었습니다.                           |
|  |  |
