---
title: Seller Insights 릴리스 정보 | Microsoft Docs
description: Seller Insights 기능 변경 내용에 대한 정보를 제공합니다.
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
ms.date: 3/3/2019
ms.author: pbutlerm
ms.openlocfilehash: 7df96d53b02e80283f263901dc7e26ed4edb947d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60311005"
---
# <a name="seller-insights-release-notes"></a>Seller Insights 릴리스 정보 

(릴리스 날짜: 2019 년 3 월 1 일)

이 문서에서는 [클라우드 파트너 포털](https://cloudpartner.azure.com/#insights)의 Seller Insights 기능 변경 내용에 대한 정보를 제공합니다.

## <a name="release-highlights-for-march-1-2019"></a>2019 년 3 월 1 일에 대 한 릴리스 주요 내용

* *고객 추세* 요약에 추가
* *상위 5 대 고객* 요약에서 고객에 게 모든 Azure 구독을 반영
* *사용량 추세 및 활성 주문 추세 정규화* 아래로 이동 하는 요약에서 *한눈에 월별 주문을*
* *지급 조정 보고서* 업데이트
* *상위 5 대 고객* 지급에서 고객에 게 모든 Azure 구독을 반영
* *사용 현황 보고서* 고객 ID를 사용 하 여 업데이트
* *고객 재직* 주문 및 사용 현황의 고객에 게 모든 Azure 구독을 반영


(릴리스 날짜: 2018 년 7 월 28 일)

## <a name="release-highlights-for-july-28-2018"></a>2018 년 7 월 28 일에 대 한 릴리스 주요 내용


-   *예상 가격*에서 환율이 적용된 고객 요금을 확인할 수 있습니다.
-   *예상 지급액*에서는 지급 가능액을 미리 확인할 수 있습니다.
-  *사용량 기준 식별자*는 고객 사용량과 주문 및 지급액의 정확한 데이터를 제공합니다.
-   *일 단위 사용량*에서는 고객 사용량을 더 세분화하여 자세히 확인할 수 있습니다.


### <a name="changes-to-data-structure-and-taxonomy"></a>데이터 구조 및 분류 변경 내용

아래 표에는 이번 릴리스에서 추가되거나 크게 변경된 메트릭이 나와 있습니다. 

| **새 용어**                   |    **정의**                                                             |
|--------------------------------|  ---------------------------------------------------------------------------- |
| 가격(CC)                     | 지정된 SKU의 사용 단위 가격(고객 통화)입니다.       |
| 예상 연장 요금(CC) | 지정된 SKU의 사용 단위 수량에 대해 예상되는 연장 요금(고객 통화)입니다. 반올림 또는 버림 오류로 인해 이 값은 정확하지 않을 수도 있습니다.   |
| PC(게시자 통화)        | 게시자가 지급용으로 선택한 기본 설정 통화입니다.                               |
| 예상 가격(PC)           | 지정된 SKU의 사용 단위에 대해 예상되는 가격입니다. 사용량 계산 날짜의 환율(게시자 통화)을 기준으로 합니다. 반올림 또는 버림 오류로 인해 이 값은 정확하지 않을 수도 있습니다.   |
| 예상 연장 요금(PC) | 지정된 SKU의 사용 단위 수량에 대해 예상되는 연장 요금입니다. 사용량 계산 날짜의 환율(게시자 통화)을 기준으로 합니다. 반올림 또는 버림 오류로 인해 이 값은 정확하지 않을 수도 있습니다. |
| 예상 지급액(PC)          | 지정된 SKU의 사용 단위 수량에 대해 예상되는 지급액입니다. 사용량 계산 날짜의 환율(게시자 통화)을 기준으로 합니다. 반올림 또는 버림 오류로 인해 이 값은 정확하지 않을 수도 있습니다.   |
| 사용량 기준                | 지급 보고서의 항목과 연결된 특정 SKU를 고객이 사용한 기간(1일 이상)의 식별자입니다. |
|  |  |
