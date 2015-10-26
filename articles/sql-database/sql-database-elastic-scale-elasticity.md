<properties 
	pageTitle="분할된 데이터베이스 탄력성" 
	description="개념을 설명하고 Azure SQL 데이터베이스를 쉽게 확장할 수 있는 기능인 분할된 데이터베이스 탄력성에 대한 예제를 제공합니다." 
	services="sql-database" 
	documentationCenter="" 
	manager="jeffreyg" 
	authors="sidneyh" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/22/2015" 
	ms.author="sidneyh"/>

# 분할된 데이터베이스 탄력성 

**분할된 데이터베이스 탄력성**을 통해 응용 프로그램 개발자는 요구에 따라 데이터베이스 리소스를 동적으로 늘리고 줄여 응용 프로그램의 성능을 최적화하고 비용을 최소화할 수 있습니다. [Basic, Standard 및 Premium 서비스 계층](sql-database-service-tiers)과 함께 Azure SQL 데이터베이스에 대한 **탄력적 데이터베이스 도구**를 사용하면 매우 강력한 탄력성 시나리오를 활용할 수 있습니다. 탄력적 데이터베이스 도구는 수평적 크기 조정을 사용합니다. 수평적 크기 조정이란 데이터베이스([또는 "분할된 데이터베이스"](sql-database-elastic-scale-glossary.md))를 분할된 데이터베이스 집합에서 추가하거나 제거하여 용량을 늘리거나 줄이는 디자인 패턴을 의미합니다. 마찬가지로, SQL 데이터베이스 서비스 계층은 단일 데이터베이스의 리소스를 요구에 따라 적절히 확장하거나 축소할 수 있는 **수직적 크기 조정** 기능을 제공합니다. 단일 분할된 데이터베이스의 수직 크기 조정 및 많은 분할된 데이터베이스의 수평적 크기 조정을 함께 사용하면 응용 프로그램 개발자가 성능, 용량 및 비용 최적화 요구에 맞게 크기를 조정할 수 있는 매우 융통성 있는 환경을 구현할 수 있습니다.

새로 도입된 **탄력적 데이터베이스 도구**기능과 수직적 크기조정은 목표를 달성하기 쉽게 만들어줍니다. 풀에 개별 데이터베이스 리소스 소비량을 전체 풀 간에 공유 하는 예산 내에서 늘리거나 줄이도록 *자동으로*허용합니다. 탄력적 데이터베이스 풀을 활용 하지 않도록 선택 하는 응용 프로그램의 경우 이 문서는 가로 크기 조정 작업을 자동화 하기 위한 몇 가지 일반적인 시나리오와 수직 확장을 관리 하기 위한 정책 기반 메커니즘을 구현 하기 위한 다른 기술을 설명 합니다.

## 수평적 크기 조정 예제: 콘서트 스파이크

수평적 크기 조정에 대한 정식 시나리오는 콘서트 티켓에 대한 트랜잭션을 처리하는 응용 프로그램입니다. 일반 고객 볼륨에서 응용 프로그램은 최소 데이터베이스 리소스를 사용하여 구매 트랜잭션을 처리합니다. 그러나 인기 콘서트의 티켓을 판매하는 경우 단일 데이터베이스는 고객의 요구의 대량 스파이크를 처리할 수 없습니다.

트랜잭션의 급격한 증가를 처리하기 위해 응용 프로그램은 수평적으로 확장됩니다. 이제 응용 프로그램은 많은 분할된 데이터베이스 간에 트랜잭션 부하를 분산할 수 있습니다. 추가 리소스가 더 이상 필요하지 않으면 데이터베이스 계층이 정상적인 사용을 위해 다시 축소됩니다. 이런 식으로 수평적 크기 조정을 통해 응용 프로그램은 고객의 요구에 맞춰 확장하고 리소스가 더 이상 필요하지 않은 경우 축소할 수 있습니다.

## 수직적 크기 조정 예제:원격 분석

수직적 크기 조정에 대한 정식 시나리오는 **분할된 데이터베이스 집합**을 사용하여 운영 원격 분석을 저장하는 응용 프로그램입니다. 이 시나리오에서는 단일 분할된 데이터베이스에 대해 하루에 모든 원격 분석 데이터를 공동 배치하는 것이 좋습니다. 이 응용 프로그램에서는 현재 날짜에 대한 데이터가 분할된 데이터베이스에 수집되고 이후에는 새로운 분할된 데이터베이스가 프로비전됩니다. 그런 다음 운영 데이터를 에이징하고 적절히 쿼리할 수 있습니다.

부하가 높은 상황에서 원격 분석 데이터를 수집하려면 더 높은 서비스 계층을 사용하는 것이 좋습니다. 즉, Premium 데이터베이스가 Basic 데이터베이스보다는 낫습니다. 데이터베이스가 최대 용량에 도달하면 수집에서 분석 및 보고로 전환됩니다. 이를 위한 Standard 계층의 성능은 해당 작업과 동일합니다. 따라서 오래된 데이터에 대한 낮은 성능 요구 사항에 맞춰 분할된 데이터베이스(가장 최근에 생성된 데이터베이스가 아닌)에서 서비스 계층을 수직적으로 축소할 수 있습니다.

단일 데이터베이스의 성능을 높여 향상된 성능을 달성하는 데에도 수직적 크기 조정을 사용할 수 있습니다. 세금 신고 응용 프로그램을 한 예로 들 수 있습니다. 신고 시, 단일 고객의 데이터는 모두 동일한 데이터베이스에 유지하고 분할된 데이터베이스의 성능을 향상하는 것이 좋습니다. 응용 프로그램에 따라 리소스를 수직적으로 확장 및 축소한다면 비용과 성능 요구 사항 모두에 대해 최적화할 수 있습니다.

데이터베이스 계층에 대한 수평적 및 수직적 크기 조정은 응용 프로그램 확장성 및 분할된 데이터베이스 탄력성의 핵심입니다.

이 문서에서는 분할된 데이터베이스 탄력성 시나리오에 대한 컨텍스트뿐만 아니라 수직적 및 수평적 크기 조정 구현 예제도 제공합니다.

## 분할된 데이터베이스 탄력성의 역학 

수직적 및 수평적 세로 크기 조정은 다음과 같은 세 가지 기본 구성 요소로 구성된 기능입니다.

1. **원격 분석**
2. **규칙**
3. **작업**   

## 원격 분석

**데이터 기반 탄력성**은 탄력적인 확장 응용 프로그램의 핵심 기능입니다. 성능 요구 사항에 따라 원격 분석을 사용하여 수직적 또는 수평적으로 크기 조정할지에 대해 데이터를 기반으로 한 결정을 내립니다.

#### 원격 분석 데이터 원본
Azure SQL DB의 컨텍스트에서 분할된 데이터베이스 탄력성의 데이터 원본으로 활용할 수 있는 핵심 원본이 몇 가지 있습니다.

1. **성능 원격 분석**은 **sys.resource\_stats** 보기에서 5분 동안 표시됩니다. 
2. 시간별 **데이터베이스 용량 원격 분석**은 **sys.resource\_usage** 보기를 통해 표시됩니다.  

다음 쿼리를 사용해서 마스터 DB를 쿼리하여 성능 리소스 사용량을 분석할 수 있습니다. 여기서 'Shard\_20140623'은 대상 데이터베이스의 이름입니다.

    SELECT TOP 10 *  
    FROM sys.resource_stats  
    WHERE database_name = 'Shard_20140623'  
    ORDER BY start_time DESC 

**성능 원격 분석**은 일시적인 동작을 제거하기 위해 일정 기간(아래 쿼리에서는 7일) 동안 요약할 수 있습니다.

    SELECT  
    avg(avg_cpu_percent) AS 'Average CPU Utilization In Percent', 
        max(avg_cpu_percent) AS 'Maximum CPU Utilization In Percent', 
        avg(avg_physical_data_read_percent) AS 'Average Physical Data Read Utilization In Percent', 
        max(avg_physical_data_read_percent) AS 'Maximum Physical Data Read Utilization In Percent', 
        avg(avg_log_write_percent) AS 'Average Log Write Utilization In Percent', 
        max(avg_log_write_percent) AS 'Maximum Log Write Utilization In Percent', 
        avg(active_session_count) AS 'Average # of Sessions', 
        max(active_session_count) AS 'Maximum # of Sessions', 
        avg(active_worker_count) AS 'Average # of Workers', 
        max(active_worker_count) AS 'Maximum # of Workers' 
    FROM sys.resource_stats  
    WHERE database_name = ' Shard_20140623' AND start_time > DATEADD(day, -7, GETDATE()); 

**sys.resource\_usag** 보기에 대해 비슷한 쿼리를 사용하여 **데이터베이스 용량**을 측정할 수 있습니다. **storage\_in\_megabytes** 열의 최대값은 데이터베이스의 현재 크기입니다. 이러한 원격 분석은 특정 분할된 데이터베이스가 해당 용량에 도달하면 응용 프로그램을 수평적으로 확장할 경우에 유용합니다.

    SELECT TOP 10 * 
    FROM [sys].[resource_usage] 
    WHERE database_name = 'Shard_20140623'  
    ORDER BY time DESC 

데이터가 특정 분할된 데이터베이스에 수집될 때 하루 다음으로 미루고 분할된 데이터베이스에 향후 작업을 처리할 수 있는 충분한 용량이 있는지 확인하는 것이 좋습니다. 선형 회귀를 실제로 구현하지 않는 동안에는 다음 쿼리가 연속된 두 날짜 간 데이터베이스 용량의 최대 델타를 반환합니다. 그런 다음 수행 중인 작업(또는 비 작업)을 생성하는 규칙에 이러한 원격 분석을 적용할 수 있습니다.

    WITH MaxDatabaseDailySize AS( 
        SELECT 
            ROW_NUMBER() OVER (ORDER BY convert(date, [time]) DESC) as [Order], 
            CONVERT(date,[time]) as [date],  
            MAX(storage_in_megabytes) as [MaxSizeDay] 
        FROM [sys].[resource_usage] 
        WHERE  
            database_name = 'Shard_20140623' 
        GROUP BY CONVERT(date,[time]) 
        ) 
    
    SELECT 
        MAX(ISNULL(Size.[MaxSizeDay] - PreviousDaySize.[MaxSizeDay], 0)) 
    FROM  
        MaxDatabaseDailySize Size INNER JOIN 
        MaxDatabaseDailySize PreviousDaySize ON Size.[order]+1 = PreviousDaySize.[order] 
    WHERE 
        Size.[order] < 8 

## 규칙  

규칙은 동작의 수행 여부를 결정하는 의사 결정 엔진입니다. 일부 규칙은 매우 간단한 반면 일부는 훨씬 더 복잡합니다. 아래 코드 조각과 같이 분할된 데이터베이스가 $SafetyMargin(예: 최대 용량의 80%)에 도달하면 새 분할된 데이터베이스가 프로비전되도록 용량 중심의 규칙을 구성할 수 있습니다.

    # Determine if the current DB size plus the maximum daily delta size is greater than the threshold 
    if( ($CurrentDbSizeMb + $MaxDbDeltaMb) -gt ($MaxDbSizeMb * $SafetyMargin))  
    {#provision new shard} 

위의 데이터 원본이 제공된 경우 수많은 분할된 데이터베이스 탄력성 시나리오를 수행하기 위해 몇 가지 규칙을 형성할 수 있습니다.

## 작업  

규칙의 결과에 따라 작업(또는 비 작업)이 결과입니다. 가장 일반적인 두 가지 작업은 다음과 같습니다.

* 분할된 데이터베이스의 서비스 계층 또는 성능 수준 증가 또는 감소 
* 분할된 데이터베이스 집합의 분할된 데이터베이스 추가 또는 제거

수평적 및 수직적 크기 조정 솔루션에서 작업의 결과는 즉각적이지 않습니다. 예를 들어 수직적으로 크기를 조정할 때 Basic 데이터베이스의 서비스 계층을 Premium 데이터베이스로 늘리는 ALTER DATABASE 명령을 실행하면 가변 기간이 사용됩니다. 기간은 데이터베이스의 크기에 따라 크게 다릅니다. 자세한 내용은 [서비스 계층 및 성능 수준 변경](http://msdn.microsoft.com/library/azure/dn369872.aspx)을 참조하세요. 마찬가지로, 새 분할된 데이터베이스의 할당 또는 프로비전도 즉각적이지 않습니다. 따라서 수평적 및 수직적 크기 조정의 경우 응용 프로그램에서 새 데이터베이스 변경 또는 프로비전에 대해 0이 아닌 시간을 고려해야 합니다.

## 분할된 데이터베이스 탄력성 시나리오 예제 

아래 그림의 예제에서는 두 가지 탄력적인 확장 시나리오를 강조합니다.: 1. 분할된 데이터베이스 맵의 수평적 크기 조정 2. 개별 분할된 데이터베이스의 수직적 크기 조정

![Operationl 데이터 수집][1]

수평적으로 확장하기 위해 새 분할된 데이터베이스를 프로비전하고 분할된 데이터베이스 맵에 등록하여 데이터베이스 계층을 수평적으로 증가하는 규칙(날짜 또는 데이터베이스 크기 기반)을 사용합니다. 그런 다음, 수평적으로 크기를 조정하기 위해 하루가 넘는 모든 분할된 데이터베이스가 Premium Edition에서 Standard 또는 Basic Edition으로 다운그레이드되는 두 번째 규칙이 구현됩니다.

여기서 원격 분석 시나리오를 다시 살펴보겠습니다. 응용 프로그램은 날짜별로 분할을 수행합니다. 이 시나리오는 원격 분석 데이터를 연속적으로 수집하며, 로딩 시간에는 고성능 버전을 요구하지만 데이터가 오래될수록 낮은 성능을 요구합니다. 현재 날짜의 데이터 Tnow가 고성능 데이터베이스(Premium)에 기록됩니다. 자정이 되면 전날의 분할된 데이터베이스(이제 T-1)가 수집에 더 이상 사용되지 않습니다. 현재 데이터는 현재 Tnow를 통해 수집됩니다. 다음 날 전에 새 분할된 데이터베이스를 프로비전하고 분할된 데이터베이스 맵([T+1])에 등록해야 합니다.

현재 분할된 데이터베이스([Tnow])가 최대 용량에 가까워지면 매일 다음 날 전에 새 분할된 데이터베이스를 프로비전하거나 새 분할된 데이터베이스를 프로비전하여 이를 수행할 수 있습니다. 이러한 방법 중 하나를 사용하면 특정한 날짜용으로 작성된 모든 원격 분석에 대해 데이터가 로컬로 유지됩니다. 더 상세한 세분성을 위해 시간당 분할을 적용할 수 있습니다. 새로운 분할된 데이터베이스가 프로비전되면 [T-1]이 쿼리 및 보고에 사용되므로 데이터베이스의 성능 수준을 낮춰 비용을 절감하는 것이 좋습니다. DB의 콘텐츠가 오래되면 성능 계층을 더 낮추거나 응용 프로그램에 따라 DB의 콘텐츠를 Azure 저장소에 보관하거나 삭제할 수 있습니다.

## 분할된 데이터베이스 탄력성 시나리오 실행  

수평 및 수직적 크기 조정 시나리오를 손쉽게 실제 구현할 수 있도록 몇 가지 분할된 데이터베이스 [탄력성 예제 스크립트](http://go.microsoft.com/?linkid=9862617)를 생성하여 스크립트 센터에 게시했습니다. Azure 자동화 서비스에서 실행되도록 작성된 이러한 PowerShell Runbook은 탄력적 데이터베이스 클라이언트 라이브러리 및 Azure SQL 데이터베이스를 조작하는 몇 가지 메서드를 제공합니다. 이러한 코드 샘플을 기반으로 작성하거나 추출하여, 수평적, 수직적 또는 두 가지 모두의 크기 조정 시나리오를 자동화는 데 필요한 스크립트를 만들 수 있습니다.

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->


<!--anchors-->
[Telemetry]: #telemetry
[Rule]: #rule
[Action]: #action
 

<!---HONumber=Oct15_HO3-->