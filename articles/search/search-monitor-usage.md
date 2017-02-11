---
title: "Azure Search 서비스의 사용량 및 통계 모니터링 | Microsoft Docs"
description: "Microsoft Azure에서 호스팅되는 검색 서비스인 Azure 검색에 대해 리소스 소비 및 인덱스 크기를 추적합니다."
services: search
documentationcenter: 
author: HeidiSteen
manager: jhubbard
editor: 
tags: azure-portal
ms.assetid: 122948de-d29a-426e-88b4-58cbcee4bc23
ms.service: search
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 10/29/2016
ms.author: heidist
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 6e40acb01d6297aa66090859533fd48c108a9a88


---
# <a name="monitor-usage-and-query-metrics-in-an-azure-search-service"></a>Azure Search 서비스의 사용량 및 쿼리 메트릭 모니터링
Azure Search는 초당 쿼리 수(QPS), 대기 시간, 그리고 볼륨이 용량을 초과하는 경우 삭제된 쿼리 비율을 포함하는 서비스 수준에서 쿼리 실행에 대한 통계를 수집합니다. 메트릭은 모니터링 블레이드를 통해 포털에 표시됩니다.

   ![QPS 활동의 스크린 샷][5]

이 문서에서는 서비스 전체에 대한 메트릭을 설명합니다. 인덱스 수준에서의 작업을 파악하기 위해 검색 트래픽 분석을 사용하고 Power BI를 사용하여 분석을 봅니다. 시작하려면 [Azure 검색을 위한 검색 트래픽 분석](search-traffic-analytics.md) 을 방문하세요.

## <a name="view-query-throughput-statistics"></a>쿼리 처리량 통계 보기
서비스 대시보드에서 모니터링 블레이드를 클릭하여 모니터링 타일을 엽니다.

   ![모니터링 타일][2]

서비스 수준에서의 쿼리 작업은 쿼리 실행, 대기 시간, 제한에 대해 기록됩니다. 메트릭은 지속적으로 수집되지만 포털이 가장 최근 작업을 표시하도록 하는 데 몇 분 정도 걸릴 수 있습니다. 

메트릭 타일을 클릭하여 세부 정보를 보거나, 경고를 추가하거나, 진단을 사용하거나, 차트를 편집합니다.

  ![메트릭 블레이드의 명령][4]

### <a name="set-up-alerts"></a>경고 설정
쿼리 실행, 대기 시간 또는 제한 작업이 정해진 기준을 초과하는 경우, 메트릭 세부 정보 페이지에서 경고를 구성하여 전자 메일 알림을 트리거할 수 있습니다.

### <a name="enable-diagnostics"></a>진단 사용
진단을 켜면 진단 데이터가 저장되는 위치, 작업 로그 및 메트릭을 포함할지 여부, 데이터의 보존 기간을 구성할 수 있습니다.

### <a name="change-chart-type-and-data-collection-interval"></a>차트 유형 및 데이터 수집 간격 변경
각 메트릭에서 **편집**을 클릭하여 선 그래프에서 가로 막대형 차트로 변경하거나 다른 시간 범위를 포함하도록 x-축을 수정할 수 있습니다.

  ![시간 범위 구성][3]

## <a name="view-counts-and-resource-usage-in-the-portal"></a>포털에서 개수 및 리소스 사용량 보기
인덱스 및 문서 크기의 증가를 추적하면 서비스에 규정한 상한에 도달하기 전에 용량을 조정할 수 있습니다. 

리소스 사용량을 모니터링하려면 [포털](https://portal.azure.com)에서 서비스에 대한 개수와 통계를 봅니다. 또한 사용자 지정 서비스 관리 도구를 빌드하는 경우 프로그래밍 방식으로 정보를 가져올 수도 있습니다.

1. [포털](https://portal.azure.com)에 로그인합니다. 
2. Azure 검색 서비스의 서비스 대시보드를 엽니다. 홈페이지에서 서비스에 대한 타일을 찾거나 표시줄에 있는 찾아보기에서 서비스를 탐색할 수 있습니다. 

사용 섹션에는 사용 가능한 리소스의 어떤 부분이 현재 사용 중인지 알려주는 측정기가 포함됩니다. 인덱스, 문서, 저장소에 대한 서비스당 제한에 대한 정보는 [서비스 제한](search-limits-quotas-capacity.md)을 참조하세요.

  ![사용량 타일][1]

> [!NOTE]
> 위의 스크린샷은 무료 서비스의 경우로 여기에는 하나의 복제본과 각각의 파티션의 최대값이 있으며, 3개의 인덱스, 10,000개의 문서 또는 50MB의 데이터 중 먼저 도달하는 것까지만 호스팅할 수 있습니다. 기본 또는 표준 계층에서 만든 서비스는 훨씬 더 큰 서비스 제한이 있습니다. 계층 선택에 대한 자세한 내용은 [계층 또는 SKU 선택](search-sku-tier.md)을 참조하세요.
> 
> 

### <a name="get-index-statistics-using-the-rest-api"></a>REST API를 사용하여 인덱스 통계 가져오기
Azure 검색 REST API와 .NET SDK는 모두 서비스 메트릭에 대한 프로그래밍 방식 액세스를 제공합니다.  [인덱서](https://msdn.microsoft.com/library/azure/dn946891.aspx) 를 사용하여 Azure SQL 데이터베이스나 DocumentDB에서 인덱스를 로드하는 경우, 추가 API를 사용하여 필요한 숫자를 얻을 수 있습니다. 

* [인덱스 통계 가져오기](https://msdn.microsoft.com/library/azure/dn798942.aspx)
* [문서 수 계산](https://msdn.microsoft.com/library/azure/dn798924.aspx)
* [인덱서 상태 가져오기](https://msdn.microsoft.com/library/azure/dn946884.aspx)

## <a name="next-steps"></a>다음 단계
기존 서비스에 대해 파티션과 복제본의 할당 균형을 조정하는 방법에 대한 지침은 [복제본 및 파티션 확장](search-limits-quotas-capacity.md)을 검토하세요. 

서비스 관리에 대한 자세한 내용은 [Microsoft Azure에서 검색 서비스 관리](search-manage.md) 또는 튜닝 지침은 [성능 및 최적화](search-performance-optimization.md)를 방문하십시오.

<!--Image references-->
[1]: ./media/search-monitor-usage/AzureSearch-Monitor1.PNG
[2]: ./media/search-monitor-usage/AzSearch-Monitor-Tile.PNG
[3]: ./media/search-monitor-usage/AzSearch-Monitor-Intervals.PNG
[4]: ./media/search-monitor-usage/AzSearch-Monitor-AlertCmd.PNG
[5]: ./media/search-monitor-usage/AzSearch-Monitor-BarChart.PNG








<!--HONumber=Nov16_HO3-->


