---
title: "사용 API 관련 Faq | Microsoft Docs"
description: "Azure 스택 미터, Azure 사용량 API, 사용 시간 및 보고 된 시간, 오류 코드를 있는 비교 목록입니다."
services: azure-stack
documentationcenter: 
author: AlfredoPizzirani
manager: byronr
editor: 
ms.assetid: 847f18b2-49a9-4931-9c09-9374e932a071
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2016
ms.author: alfredop
ms.openlocfilehash: 166147c8cb4949be1b23e0a06868e66c8a5844f4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="frequently-asked-questions-in-azure-stack-usage-api"></a>질문과 대답 Azure 스택 사용량 API 
이 문서 Azure 스택 사용량 API에 대 한 몇 가지 자주 묻는 질문에 대답 합니다.

## <a name="what-meter-ids-can-i-see"></a>어떤 미터 Id를 볼 수 있습니까?
현재, 네트워크, 저장소 및 계산 리소스 공급자에 대 한 사용 보고 됩니다.

| **리소스 공급자** | **측정기 ID** | **측정 이름** | **단위** | **추가 정보** |
| --- | --- | --- | --- | --- | 
| **네트워크** |F271A8A388C44D93956A063E1D2FA80B |고정 IP 주소 사용 |IP 주소|사용 되는 IP 수 addressess | 
| |9E2739BA86744796B465F64674B822BA |동적 IP 주소 사용 |IP 주소|사용 되는 IP 수 addressess | 
| **저장소** |B4438D5D-453B-4EE1-B42A-DC72E377F1E4 |TableCapacity |GB\*시간 |테이블에서 사용 되는 총 용량 |
| | B5C15376-6C94-4FDD-B655-1A69D138ACA3 |PageBlobCapacity |GB\*시간 |페이지 blob으로 소비 된 총 용량 |
| | B03C6AE7-B080-4BFA-84A3-22C800F315C6 |QueueCapacity |GB\*시간 |큐에서 사용 되는 총 용량 |
| | 09F8879E-87E9-4305-A572-4B7BE209F857 |BlockBlobCapacity |GB\*시간 |블록 blob으로 소비 된 총 용량 |
| | B9FF3CD0-28AA-4762-84BB-FF8FBAEA6A90 |TableTransactions |10, 000s에서 요청 수 |테이블 서비스 요청 (10, 000s) |
| | 50A1AEAF-8ECA-48A0-8973-A5B3077FEE0D |TableDataTransIn |Gb에서는 송 데이터 |테이블 서비스 데이터 유입 gb |
| | 1B8C1DEC-EE42-414B-AA36-6229CF199370 |TableDataTransOut |Outgress gb |테이블 서비스 데이터 유출을 gb |
| | 43DAF82B-4618-444A-B994-40C23F7CD438 |BlobTransactions |10, 000s에서 요청 수 |Blob 서비스 요청 (10, 000s) |
| | 9764F92C-E44A-498E-8DC1-AAD66587A810 |BlobDataTransIn |Gb에서는 송 데이터 |Blob 서비스 데이터 유입 gb |
| | 3023FEF4-ECA5-4D7B-87B3-CFBC061931E8 |BlobDataTransOut |Outgress gb |Blob 서비스 데이터 유출을 gb |
| | EB43DD12-1AA6-4C4B-872C-FAF15A6785EA |QueueTransactions |10, 000s에서 요청 수 |큐 서비스 요청 (10, 000s) |
| | E518E809-E369-4A45-9274-2017B29FFF25 |QueueDataTransIn |Gb에서는 송 데이터 |큐 서비스 데이터 유입 gb | 
| | DD0A10BA-A5D6-4CB6-88C0-7D585CEF9FC2 |QueueDataTransOut |Outgress gb |큐 서비스 데이터 유출을 gb |
| **Compute** |FAB6EB84-500B-4A09-A8CA-7358F8BBAEA5 |기본 VM 크기 시간 |가상 코어 시간 (분) | 횟수입니다. vcores VM을 실행 하는 시간 (분) |
| |9CD92D4C-BAFD-4492-B278-BEDC2DE8232A |Windows VM 크기 시간 |가상 코어 시간 (분) | 횟수입니다. vcores VM을 실행 하는 시간 (분) |
| |6DAB500F-A4FD-49C4-956D-229BB9C8C793 |VM 크기 시간 |VM 시간 |자료와 Windows VM을 캡처합니다. Vcores에 대 한 조정 되지 않습니다. |
| **키 자격 증명 모음** | EBF13B9F-B3EA-46FE-BF54-396E93D48AB4 |주요 자격 증명 모음 트랜잭션 | 10000s의 요청 수| 주요 자격 증명 모음 데이터 평면에서 수신 하는 REST API 요청 수 |

## <a name="how-do-the-azure-stack-usage-apis-compare-to-the-azure-usage-apihttpsmsdnmicrosoftcomlibraryazure1ea5b323-54bb-423d-916f-190de96c6a3c-currently-in-public-preview"></a>Azure 스택 사용량 Api와 비교 하는 방법의 [Azure 사용량 API](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c) (현재 공개 미리 보기)에 있습니까?
* 테 넌 트 사용량 API는 한 가지 예외로 Azure API와 일치:는 *showDetails* 플래그 현재 Azure 스택에서 지원 되지 않습니다.
* 공급자 사용량 API는 Azure 스택만 적용 됩니다.
* 현재는 [RateCard API](https://msdn.microsoft.com/en-us/library/azure/mt219004.aspx) 즉 Azure에서 사용할 수 있는 Azure 스택에서 사용할 수 없는 합니다.

## <a name="what-is-the-difference-between-usage-time-and-reported-time"></a>사용 시간과 보고 된 시간 사이의 차이 무엇입니까?
사용 현황 데이터 보고서는 두 개의 기본 시간 값을 갖습니다.

* **한 번도 보고**합니다. 사용 이벤트 사용 시스템을 입력 하는 경우 시간
* **사용 시간이**합니다. Azure 스택 리소스를 사용 하는 경우 시간

사용 시간과 보고 된 시간에 대 한 특정 사용 이벤트에 대 한 값에서 차이점을 볼 수 있습니다. 지연 때 모든 환경에 여러 개의 시간 만큼 될 수 있습니다.

쿼리할 수는 현재 *보고 된 시간에 의해서만*합니다.

## <a name="what-do-these-usage-api-error-codes-mean"></a>이러한 사용량 API 오류 코드는 무엇을 의미 합니까?
| **HTTP 상태 코드** | **오류 코드** | **설명** |
| --- | --- | --- |
| 400/잘못 된 요청 |*NoApiVersion* |*api 버전* 쿼리 매개 변수가 누락 되었습니다. |
| 400/잘못 된 요청 |*InvalidProperty* |속성이 누락 되었거나 잘못 된 값입니다. 응답 본문에 오류 코드에 있는 메시지는 누락 된 속성을 식별합니다. |
| 400/잘못 된 요청 |*RequestEndTimeIsInFuture* |에 대 한 값 *reportedendtime의* 시작일이 미래입니다. 나중에 허용 되지 않습니다이 인수에 대 한 합니다. |
| 400/잘못 된 요청 |*SubscriberIdIsNotDirectTenant* |호출자의 유효한 테 넌 트를 구독 ID를 사용 하는 공급자 API 호출 합니다. |
| 400/잘못 된 요청 |*SubscriptionIdMissingInRequest* |호출자의 구독 ID가 없습니다. |
| 400/잘못 된 요청 |*InvalidAggregationGranularity* |잘못 된 집계 세분성 요청 되었습니다. 유효한 값은 일별 및 시간입니다. |
| 503 |*ServiceUnavailable* |서비스 사용량이 호출이 제한 되 고 다시 시도 가능한 오류가 발생 했습니다. |

## <a name="next-steps"></a>다음 단계
[대금 청구 및 차지 백 Azure 스택](azure-stack-billing-and-chargeback.md)

[공급자 리소스 사용량 API](azure-stack-provider-resource-api.md)

[테 넌 트 리소스 사용량 API](azure-stack-tenant-resource-usage-api.md)

