---
title: Azure Security Center 검색 | Microsoft Docs
description: Azure Security Center Azure Monitor 로그 검색을 사용 하 여 보안 데이터를 검색 및 분석 하는 방법에 대해 알아봅니다.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 45b9756b-6449-49ec-950b-5ed1e7c56daa
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/11/2017
ms.author: rkarlin
ms.openlocfilehash: c88e2025444aa2fd83e05fdff97ea640ceefaa04
ms.sourcegitcommit: e3b0fb00b27e6d2696acf0b73c6ba05b74efcd85
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68662921"
---
# <a name="azure-security-center-search-retired"></a>Azure Security Center 검색 (사용 되지 않음)

> [!NOTE]
> Security Center의 검색 대시보드는 2019 년 7 월 31 일에 사용이 중지 되었습니다. 자세한 내용 및 대체 서비스는 [Security Center 기능 사용 중지 (7 월 2019)](security-center-features-retirement-july2019.md#menu_search)를 참조 하세요.

Azure Security Center [Azure Monitor 로그 검색](../log-analytics/log-analytics-log-searches.md) 을 사용 하 여 보안 데이터를 검색 하 고 분석 합니다. Azure Monitor 로그는 데이터를 신속 하 게 검색 하 고 통합할 수 있는 쿼리 언어를 포함 합니다. Security Center에서 Azure Monitor 로그 검색을 활용 하 여 쿼리를 생성 하 고 수집 된 데이터를 분석할 수 있습니다.

검색은 Security Center의 무료 계층과 표준 계층에서 모두 사용 가능합니다.  로그 검색에서 사용 가능한 데이터는 작업 영역에 적용되는 계층 수준에 따라 달라집니다.  자세한 내용은 Security Center [가격 책정 페이지](../security-center/security-center-pricing.md)를 참조하세요.


> [!NOTE]
> Security Center는 무료 계층이 적용되는 작업 영역의 보안 데이터를 저장하지 않습니다. 무료 계층이 적용되는 작업 영역으로 다양한 로그를 전송하고 해당 데이터를 검색할 수는 있지만, 이 경우 Security Center의 데이터는 검색 결과에 포함되지 않습니다. Security Center는 표준 계층이 적용되는 작업 영역에만 데이터를 저장합니다.
>
>

## <a name="access-search"></a>검색 액세스
1. Security Center 주 메뉴에서 **검색**을 선택합니다.

   ![로그 검색 선택][1]

2. Security Center에 Azure 구독에 포함된 모든 작업 영역이 나열됩니다. 작업 영역을 선택합니다. 작업 영역이 하나뿐인 경우 이 작업 영역 선택기가 표시되지 않습니다.

   ![작업 영역 선택][2]

3. **로그 검색**이 열립니다. 선택한 작업 영역에서 더 많은 데이터를 쿼리하려면 다음 예제 쿼리를 입력합니다.

   SecurityEvent | where EventID == 4625 | summarize count() by TargetAccount

   결과에는 로그인 하지 못한 모든 계정 (이벤트 4625)이 표시 됩니다.

   ![검색 결과][3]

선택한 작업 영역에서 데이터를 쿼리 하는 방법에 대 한 자세한 내용은 [Kusto 쿼리 언어](../log-analytics/log-analytics-search-reference.md) 를 참조 하세요.

## <a name="next-steps"></a>다음 단계
이 문서에서는 Security Center에서 검색에 액세스하는 방법을 알아보았습니다. Security Center Azure Monitor 로그 검색을 사용 합니다. Azure Monitor 로그 검색에 대 한 자세한 내용은 다음을 참조 하세요.

- [Azure Monitor 로그 란?](../log-analytics/log-analytics-overview.md) – Azure Monitor 로그 개요
- [Azure Monitor 로그의 로그 검색 이해](../log-analytics/log-analytics-log-search-new.md) -로그 검색을 Azure Monitor 로그에서 사용 하는 방법을 설명 하 고 로그 검색을 만들기 전에 이해 해야 하는 개념을 제공 합니다.
- [Azure Monitor 로그에서 로그 검색을 사용 하 여 데이터 찾기](../log-analytics/log-analytics-log-searches.md) -로그 검색 사용에 대 한 자습서
- [Kusto 검색 참조](../log-analytics/log-analytics-search-reference.md) -Azure Monitor 로그의 쿼리 언어에 대해 설명 합니다.

Security Center에 대해 자세히 알아보려면 다음 항목을 참조하세요.

- [Security Center 개요](security-center-intro.md) – Security Center의 주요 기능에 대해 설명합니다.

<!--Image references-->
[1]: ./media/security-center-search/search.png
[2]: ./media/security-center-search/workspace-selector.png
[3]: ./media/security-center-search/log-search.png
