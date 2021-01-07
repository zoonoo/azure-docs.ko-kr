---
title: 라이브 API로 이동-Azure Marketplace
description: Go Live API는 제품 라이브 목록 추가 프로세스를 시작합니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: mingshen-ms
ms.author: mingshen
ms.date: 07/14/2020
ms.openlocfilehash: d612b796f85c9eaab1600c55cde7e79acb49f352
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87292946"
---
# <a name="go-live"></a>Go Live

> [!NOTE]
> Cloud 파트너 포털 Api는와 통합 되며 파트너 센터에서 계속 작업 합니다. 전환에는 작은 변화가 도입 됩니다. [CLOUD 파트너 포털 API 참조](./cloud-partner-portal-api-overview.md) 에 나열 된 변경 내용을 검토 하 여 파트너 센터로 전환 된 후 코드가 계속 작동 하는지 확인 합니다. CPP Api는 파트너 센터로 전환 하기 전에 이미 통합 된 기존 제품에만 사용 해야 합니다. 새 제품은 파트너 센터 제출 Api를 사용 해야 합니다.

이 API는 앱을 프로덕션 환경으로 푸시하는 프로세스를 시작합니다. 이 작업은 대개 오랫동안 실행됩니다. 이 호출에서는 [게시](./cloud-partner-portal-api-publish-offer.md) API 작업의 알림 전자 메일 목록을 사용합니다.

 `POST  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/golive?api-version=2017-10-31` 

## <a name="uri-parameters"></a>URI 매개 변수
--------------

|  **이름**      |   **설명**                                                           | **데이터 형식** |
|  --------      |   ---------------                                                           | ------------- |
| publisherId    | 검색할 제품의 게시자 식별자입니다(예: `contoso`).       |  String       |
| offerId        | 검색할 제품의 제품 식별자입니다.                                   |  String       |
| api-version    | 최신 버전의 API입니다.                                                   |  날짜         |
|  |  |  |

## <a name="header"></a>헤더
------

|  **이름**       |     **값**       |
|  ---------      |     ----------      |
| 콘텐츠 형식    | `application/json`  |
| 권한 부여   | `Bearer YOUR_TOKEN` |
|  |  |

## <a name="body-example"></a>본문 예제

### <a name="response"></a>응답

#### <a name="migrated-offers"></a>마이그레이션된 제품

`Location: /api/publishers/contoso/offers/contoso-offer/operations/56615b67-2185-49fe-80d2-c4ddf77bb2e8?api-version=2017-10-31`

#### <a name="non-migrated-offers"></a>마이그레이션되지 않은 제품

`Location: /api/operations/contoso$contoso-offer$2$preview?api-version=2017-10-31`

### <a name="response-header"></a>응답 헤더

|  **이름**             |      **값**                                                            |
|  --------             |      ----------                                                           |
| 위치    |  이 작업의 상태를 검색 하기 위한 상대 경로입니다.            |
|  |  |

### <a name="response-status-codes"></a>응답 상태 코드

| **코드** |  **설명**                                                                        |
| -------- |  ----------------                                                                        |
|  202     | `Accepted` - 요청이 성공적으로 수락되었습니다. 응답에는 작업 상태 추적을 위한 위치가 포함됩니다. |
|  400     | `Bad/Malformed request` -응답 본문 내에서 추가 오류 정보가 확인되었습니다. |
|  404     |  `Not found` - 지정한 엔터티가 없습니다.                                       |
|  |  |
