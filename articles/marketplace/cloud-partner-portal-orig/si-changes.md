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
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: ec3784a91f8aeb7f0fedd13c9ab86a844832a578
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2018
ms.locfileid: "48807180"
---
<a name="seller-insights-release-notes"></a>Seller Insights 릴리스 정보 
===============================
(릴리스 날짜: 2018년 7월 28일)

이 문서에서는 [클라우드 파트너 포털](https://cloudpartner.azure.com/#insights)의 Seller Insights 기능 변경 내용에 대한 정보를 제공합니다.


<a name="release-highlights"></a>릴리스 관련 주요 사항
------------------

-   *예상 가격*에서 환율이 적용된 고객 요금을 확인할 수 있습니다.
-   *예상 지급액*에서는 지급 가능액을 미리 확인할 수 있습니다.
-  *사용량 기준 식별자*는 고객 사용량과 주문 및 지급액의 정확한 데이터를 제공합니다.
-   *일 단위 사용량*에서는 고객 사용량을 더 세분화하여 자세히 확인할 수 있습니다.


<a name="changes-to-data-structure-and-taxonomy"></a>데이터 구조 및 분류 변경 내용
--------------------------------------

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
