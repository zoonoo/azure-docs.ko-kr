---
title: Seller Insights FAQ | Microsoft Docs
description: 클라우드 파트너 포털의 Seller insights 기능에 대한 질문과 대답을 소개합니다.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: pbutlerm
ms.openlocfilehash: ec7fc3a7877cf0bffac0043a74c34d6f0f04826b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60362376"
---
<a name="seller-insights-faq"></a>Seller Insights FAQ
===================

이 문서에서는 Seller Insights 내에서 흔히 수행하는 사용자 절차 관련 지침과 Seller Insights 관련 질문을 소개합니다.


<a name="find-definitions-for-the-values-in-the-downloaded-transaction-file"></a>다운로드한 트랜잭션 파일에 포함된 값의 정의 찾기
------------------------------------------------------------------

트랜잭션 파일의 메트릭 값 정의는 [Seller Insights 정의](./si-insights-definitions-v4.md) 문서에서 확인할 수 있습니다.


<a name="see-customer-details-of-transactions-for-which-ive-been-paid"></a>대금을 지급받은 트랜잭션의 고객 세부 정보 확인
-------------------------------------------------------------

지급 모듈에서 트랜잭션을 다운로드하여 레이블이 **지급 상태**인 열을 찾은 다음 “지급됨” 값만 표시되도록 필터를 적용합니다. 고객 세부 정보를 포함 하는 다음 열 나타납니다. **회사 이름**, **고객 전자 메일**를 **고객 국가**를 **고객 상태**, 및 **고객의 우편 번호**합니다.


<a name="calculate-my-open-accounts-receivable"></a>개설된 수취 계정 계산
-------------------------------------

지급 모듈에서 트랜잭션을 다운로드하여 레이블이 **지급 상태**인 열을 찾은 다음 “지급 예정” 및 “지급 준비 안 됨” 값만 표시되도록 필터를 적용합니다. 그런 다음 레이블이 **지급 금액(PC)** 인 열의 값을 합산합니다.


<a name="calculate-revenue-by-customer-usage-period"></a>고객 사용 기간별 매출 계산
------------------------------------------

트랜잭션을 지급 모듈에서을 다운로드 한 후 레이블이 지정 된 열을 찾습니다 **트랜잭션 상태**, "유료" 값을 필터링 합니다.   각 트랜잭션이 나열, 이라는 열에 대 한 **지급 금액 (PC)** 는 귀하가 지불한 된 금액을 나타냅니다.  트랜잭션과 관련 된 사용 기간을 계산 하려면 열을 사용 하 여 **청구 날짜**, 트랜잭션이 적용 되는 기간에 대 한 사용량의 마지막 날의 근사치는 합니다.


<a name="calculate-your-bad-debt"></a>회수 불능 금액 계산
---------------------

지급 모듈에서 트랜잭션을 다운로드하여 레이블이 **최종 수금 상태**인 열을 찾은 다음 “탕감” 값만 표시되도록 필터를 적용합니다. 그런 다음 레이블이 **지급 금액(PC)** 인 열의 값을 합산합니다.


<a name="view-payout-or-customer-contact-information"></a>지급 또는 고객 연락처 정보 확인
-------------------------------------------

“참가자” 역할이 아닌 “소유자” 역할의 사용자로 로그인합니다. 소유자 역할만 지급 및 고객 정보를 확인할 수 있습니다. 사용자 역할에 대한 자세한 내용은 [사용자 관리](./cloud-partner-portal-manage-users.md) 문서에서 확인할 수 있습니다.


<a name="calculate-my-advance-payouts"></a>선지급액 계산
----------------------------

지급 모듈에서 트랜잭션을 다운로드하여 레이블이 **트랜잭션 유형**인 열을 찾은 다음 “청구” 값만 표시되도록 필터를 적용합니다. 그런 다음 레이블이 **최종 수금 상태**인 열을 찾아서 “진행 중” 값만 표시되도록 필터를 적용합니다. 마지막으로 **지급 금액(PC)** 열의 값을 합산하면 고객에게서 수금을 하기 전에 지급된 모든 선지급액이 계산됩니다.


<a name="calculate-customer-refunds"></a>고객 환불액 계산
--------------------------

지급 모듈에서 트랜잭션을 다운로드하여 레이블이 **최종 수금 상태**인 열을 찾은 다음 “환불” 값만 표시되도록 필터를 적용합니다. 그런 다음 **청구 금액(PC)** 열의 값을 합산하면 고객을 대상으로 처리된 모든 환불액이 계산됩니다.


<a name="identify-which-transactions-involved-a-microsoft-channel-partner"></a>Microsoft 채널 파트너가 개입한 트랜잭션 파악
----------------------------------------------------------------

**Azure 라이선스 유형** 열에서 “기업(재판매인 있음)” 및 “클라우드 솔루션 공급자” 값이 표시되도록 필터링된 모든 트랜잭션은 Microsoft 채널 파트너가 개입한 트랜잭션입니다. 파트너 관련 추가 세부 정보를 확인하려는 경우 지급 모듈 다운로드 및 고객 모듈 다운로드에서 **재판매인 이름** 및 **재판매인 전자 메일**을 확인할 수 있습니다.


<a name="identify-trial-usage-and-trial-conversions"></a>평가판 사용 및 평가판 전환 파악
------------------------------------------

해당 특정 주문의 평가 기간(해당하는 경우)이 종료된 날짜를 확인할 수 있도록 이제 주문, 사용량 및 지급 모듈 다운로드에 **평가판 종료 날짜**가 포함됩니다. 평가판 사용량 및 주문을 확인하려면 다운로드에서 **SKU 청구 유형** 열을 찾은 다음 “평가판” 값만 표시되도록 필터를 적용합니다. 평가판 전환을 확인하려면 다운로드에서 **평가판 종료 날짜** 열을 찾은 다음 **평가판 종료 날짜**가 오늘 날짜 이전이며 **취소 날짜** 열이 비어 있거나 **평가판 종료 날짜** 이후인 주문만 표시되도록 필터를 적용합니다.


<a name="when-is-my-monthly-payout-calculated"></a>월간 지급액이 계산되는 시기
------------------------------------

이전 달 말일까지 지급 준비가 된 모든 금액이 매월 15일에 지급됩니다. Microsoft는 매월 3일에 지난달의 지급 금액을 계산하며, 다운로드에서 해당하는 모든 청구 트랜잭션을 업데이트하여 **지급 상태** 열을 “지급 예정”으로 변경합니다. 이러한 트랜잭션은 지급 요청이 은행 계좌로 전송될 때까지 해당 상태로 유지됩니다. 지급 요청이 은행 계좌로 전송되면 **지급 상태**는 “지급됨”으로 업데이트되며 “지급 날짜”는 Microsoft에서 은행에 지급 요청을 제출한 날짜가 표시되도록 업데이트됩니다.


<a name="calculate-customer-acquisition-and-loss"></a>고객 취득일 및 취소일 확인
---------------------------------------

고객 다운로드의 **취득일** 열을 찾아서 고객이 제품 중 하나를 처음 구매한 날짜를 확인할 수 있습니다. 마찬가지로 고객 다운로드에서 **취소일** 열을 찾으면 게시자가 게시한 제품 사용을 고객이 취소한 날짜를 확인할 수 있습니다.


<a name="finding-more-help"></a>추가 도움말 확인
-----------------

- [Seller Insights 정의](./si-insights-definitions-v4.md) - 메트릭과 데이터의 정의를 확인할 수 있습니다.

- [Seller Insights 시작](./si-getting-started.md) - Seller insights 기능을 소개합니다.

