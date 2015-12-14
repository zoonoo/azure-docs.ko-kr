<properties 
	pageTitle="Azure SQL 데이터베이스 탄력적 데이터베이스 풀의 가격 및 성능 고려 사항" 
	description="탄력적 데이터베이스 풀은 탄력적 데이터베이스 그룹에서 공유하는 가용 리소스의 컬렉션입니다. 이 문서는 탄력적 데이터베이스 풀을 사용하여 데이터베이스의 그룹에 대한 적합성을 평가하는데 도움이 되는 지침을 제공합니다." 
	services="sql-database" 
	documentationCenter="" 
	authors="stevestein" 
	manager="jeffreyg" 
	editor=""/>

<tags 
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="12/01/2015" 
	ms.author="sstein" 
	ms.workload="data-management" 
	ms.topic="article" 
	ms.tgt_pltfrm="NA"/>


# 탄력적 데이터베이스 풀의 가격 및 성능 고려 사항


이 문서에서는 데이터베이스 사용 패턴 및 탄력적 데이터베이스 풀과 단일 데이터베이스 간의 가격 차이를 기반으로 하여 데이터베이스 그룹에 탄력적 데이터베이스 풀을 사용하는 것이 비용 효율적인지를 평가하는 데 도움이 되는 지침을 제공합니다. 추가 지침은 SQL 데이터베이스의 기존 집합에 필요한 현재 풀 크기를 결정 하는데 도움이 되도록 제공 됩니다.

- 탄력적 데이터베이스 풀의 개요는 [SQL 데이터베이스 탄력적 데이터베이스 풀](sql-database-elastic-pool.md)을 참조하세요.
- 탄력적 데이터베이스 풀의 자세한 정보는 [SQL 데이터베이스 탄력적 데이터베이스 풀 참조](sql-database-elastic-pool-reference.md)를 참고하세요.


> [AZURE.NOTE]탄력적 데이터베이스 풀은 현재 미리 보기 상태이며, SQL 데이터베이스 V12 서버에서만 사용할 수 있습니다.

## 탄력적 데이터베이스 풀

SaaS Isv 여러 데이터베이스로 구성된 대규모 데이터 계층 위에 구축 하는 응용 프로그램을 개발 합니다. 일반적인 응용 프로그램 패턴은 개개인의 다양하고 예측할 수 없는 사용패턴을 가진 다른 고객을 가진 각각의 데이터베이스에 관한 것입니다. 개별적인 각각의 데이터베이스의 예측하는 리소스 요구를 예측하는 것은 ISV에게는 어려울 수 있습니다. 이러한 상황에서, ISV는 모든 데이터 베이스에 대한 긍정적인 처리량 및 반응속도를 보장하려면 리소스에 상당한 지출이 초과 공급될 수 있습니다. 또는 ISV는 짧은 시간과 고객에게 성능저하 경험의 위험을 줄 수 있습니다.

Azure SQL 데이터베이스의 탄력적 데이터베이스 풀은 SaaS ISVs를 각각의 데이터베이스에 성능 탄력성을 제공하는 동시에 규정된 예산내의 데이터베이스 그룹에 가격 성능을 최적화를 가능하게 합니다. 탄력적 데이터베이스 풀을 사용하면 ISV가 예측할 수 없는 개별 데이터베이스의 사용 기간을 수용하기 위해 여러 데이터베이스에서 공유되는 풀에 대한 eDTU(탄력적 데이터베이스 처리량 단위)를 구입할 수 있습니다. 풀에 대한 eDTU 요구 사항은 해당 데이터베이스의 집계 사용률에 의해 결정됩니다. 풀에 사용 가능한 eDTU 양은 ISV 예산에 의해 제어됩니다. 탄력적 데이터베이스 풀을 통해 ISV는 풀에 대한 예산이 성능에 미치는 영향 및 성능이 예산에 미치는 영향을 쉽게 파악할 수 있습니다. ISV는 풀에 데이터베이스를 추가하고, 데이터베이스에 대한 필수 eDTU 보증 또는 최대값을 설정한 다음 예산에 따라 풀의 eDTU를 설정하면 됩니다. 탄력적 데이터베이스 풀을 통해 ISV는 린 시작부터 성숙한 비즈니스까지 점차 규모를 늘리면서 서비스를 원활하게 확장할 수 있습니다.
  


## 탄력적 데이터베이스 풀을 고려하는 경우

탄력적 데이터베이스 풀은 특정 사용률 패턴이 있는 다수의 데이터베이스에 적합합니다. 주어진 데이터 베이스에 대해, 이 패턴은 상대적으로 사용률 급증이 드물고 평균 사용률이 낮음으로 규정됩니다.

풀에 더 많은 데이터베이스를 추가할수록 절감이 커집니다. 응용 프로그램 사용 패턴에 따라 S3 데이터베이스 2개 수준의 절감이 가능합니다.

다음 섹션은 탄력적 데이터베이스 풀 사용이 특정 데이터베이스 컬렉션에 유용한지를 평가하는 방법을 이해하는 데 도움이 됩니다. 예제에서는 Standard 탄력적 데이터베이스 풀을 사용하지만 Basic 및 Premium 풀에도 동일한 원칙이 적용됩니다.

### 데이터베이스 사용량 패턴 평가

다음 그림에는 대부분의 시간을 유휴 상태로 있지만 정기적으로 갑작스러운 활동이 증가하는 데이터베이스의 예가 나와있습니다. 다음은 탄력적 데이터베이스 풀에 적합한 사용률 패턴입니다.
 
   ![하나의 데이터베이스][1]

위의 있는 한 시간 주기 그림에서 DB1이 90 DTUs에서 정점을 찍지만, 전체 평균 사용량은 5dtu보다 낮습니다. 단일 데이터베이스에서 이 작업을 실행 하려면 S3 성능 수준이 필요 합니다. 그러나 대부분의 리소스가 낮은 활동기간동안 사용되지 않은 채 남아있습니다.

탄력적 데이터베이스 풀을 통해 사용하지 않은 DTU를 여러 데이터베이스에서 공유할 수 있으므로 필요한 총 DTU 양과 전체 비용이 감소합니다.

앞의 예제에 기초하여, DB1같은 유사한 사용률 패턴의 추가 데이터베이스가 있다고 가정합니다. 아래의 두 그림에는, 4개의 데이터베이스 사용률과 20개의 데이터베이스가 시간에 따라 사용률의 특성이 겹치지 않는다는 것을 설명하기 위해 같은 그래프에 겹쳐져 있습니다.

   ![4개의 데이터베이스][2]

   ![20개의 데이터베이스][3]

20개의 데이터베이스간의 DTU 사용률 집계는 그래프 위에 검은 선으로 표시됐습니다. 이는 DTU 사용률 집계가 100 DTU를 초과하지 않음을 보여 주며, 이 기간 동안 20개 데이터베이스가 100 eDTU를 공유할 수 있음을 나타냅니다. 이러한 결과 20X DTUs의 감소와 13x 가격 감소를 비교합니다. 단일 데이터베이스의 S3 퍼포먼스 수준내의 각각의 데이터베이스를 배치하기위해


이 예는 다음 이유로 이상적입니다.

- 최고사용률과 데이터베이스당 평균 사용률간에 큰차이가 있습니다.  
- 각 데이터베이스의 최고사용률이 시간내에 여러 지점에서 발생합니다. 
- eDTU는 다수의 데이터베이스에서 공유됩니다.


탄력적 데이터베이스 풀의 가격은 풀 DTU의 함수입니다. 풀의 eDTU 단가는 단일 데이터베이스에 대한 DTU 단가보다 1.5배지만 **많은 데이터베이스가 풀 eDTU를 공유할 수 있으므로 필요한 총 eDTU가 감소하는 경우가 많습니다**. 가격 책정 및 eDTU 공유에서의 이러한 차이가 풀이 제공할 수 있는 가격 절감 가능성의 기초가 됩니다.

<br>

데이터베이스 개수 및 데이터베이스 사용률과 관련된 다음 규칙은 단일 데이터베이스에 대한 성능 수준 사용과 비교하여 탄력적 데이터베이스 풀이 비용 절감을 제공하는 데 도움이 됩니다.


### 데이터베이스의 최소 수

단일 데이터베이스에 대한 성능 수준의 DTU의 합계가 풀에 필요한 eDTU의 1.5배 이상인 경우 탄력적 풀은 더욱 비용 효율적입니다. 사용 가능한 크기에 대해서는 [탄력적 데이터베이스 풀과 탄력적 데이터베이스에 대한 eDTU 및 저장소 제한](sql-database-elastic-pool-reference.md#edtu-and-storage-limits-for-elastic-pools-and-elastic-databases)을 참조하세요.


***예제***<br> 100 eDTU 탄력적 데이터베이스 풀이 단일 데이터베이스에 대한 성능 수준 사용보다 비용 효율적이려면 최소 2개의 S3 데이터베이스 또는 15개의 S0 데이터베이스가 필요합니다.



### 최대 동시에 최고 데이터베이스

eDTU를 공유하면 풀의 일부 데이터베이스가 단일 데이터베이스에 대한 성능 수준을 사용할 때 사용 가능한 제한까지 eDTU를 동시에 사용할 수 없습니다. 동시에 최대 사용량에 도달하는 데이터베이스 수가 작을수록 더 낮은 풀 eDTU를 설정할 수 있으며 더 비용 효율적이 됩니다. 일반적으로 eDTU 제한까지 동시에 최대 사용량에 도달하는 풀의 데이터베이스 수가 2/3(또는 67%)를 초과하면 안 됩니다.

***예제***<br> 200 eDTU 풀에서는 S3 데이터베이스 3개의 비용을 줄이기 위해 최대 2개의 데이터베이스가 동시에 최대 사용률에 도달할 수 있습니다. 이러한 S3 데이터베이스 4개 중 3개 이상이 동시에 최대 사용률에 도달하는 경우 200 eDTU 이상으로 풀 크기를 조정해야 합니다. 풀 크기가 200 eDTU 이상으로 조정된 경우 단일 데이터베이스에 대한 성능 수준보다 낮은 비용을 유지하려면 S3 데이터베이스를 풀에 더 추가해야 합니다.


예에서는 풀에 있는 다른 데이터베이스의 사용률을 고려 하지 않습니다. 모든 데이터베이스 시간에 특정된 시점에서 일부 사용률의 경우, 데이터베이스의 2/3(또는 67%) 보다 작은 사용률이 동시에 정점에 달할 수 있습니다.


### 데이터베이스당 DTU 사용률

데이터 베이스 사용률의 최고와 평균 사이의 큰 차이는 장시간 동안 낮은 사용률 그리고 짧은 시간 동안 큰사용률을 나타냅니다. 이 사용률 패턴은 데이터베이스에서 리소스를 공유 하기에 이상적입니다. 최고 사용률이 평균사용률의 1.5배 이상이 될때 풀에 대한 데이터베이스를 고려해야 합니다.

    
***예제***<br> 최대 사용률이 100 DTU이고 평균 67 DTU 이하를 사용하는 S3 데이터베이스는 탄력적 데이터베이스 풀에서 eDTU를 공유하는 데 적합합니다. 또는 최대 사용률이 20 DTU이고 평균 13 DTU 이하를 사용하는 S1 데이터베이스는 탄력적 데이터베이스 풀에 적합합니다.
    

## 탄력적 데이터베이스 풀과 단일 데이터베이스 간의 가격 차이를 비교하는 추론 

다음 추론은 탄력적 데이터베이스 풀이 개별 단일 데이터베이스 사용보다 비용 효율적인지를 예측하는 데 도움이 될 수 있습니다.

1. 다음 수식에 따라 풀에 필요한 eDTU를 예측합니다.
    
    MAX(*총 DB 수* * *DB당 평균 DTU 사용률*, *동시에 최대 사용률에 도달한 DB 수* * *DB당 최대 DTU 사용률*)

2. 풀에 사용 가능한 가장 작은 eDTU 값을 1단계의 예측보다 큰 값으로 선택합니다. 사용 가능한 eDTU 선택 항목은 [탄력적 데이터베이스 풀 및 탄력적 데이터베이스에 대한 eDTU 및 저장소 제한](sql-database-elastic-pool-reference.md#edtu-and-storage-limits-for-elastic-pools-and-elastic-databases)에 나열된 유효한 eDTU 값을 참조하세요.


3. 다음과 같이 풀에 대한 가격을 계산 합니다.

    풀 가격 = *풀 eDTU* * *풀 eDTU 단위 가격*

    가격 정보는 [SQL 데이터베이스 가격 정보](http://azure.microsoft.com/pricing/details/sql-database/)를 참조하세요.


4. 단일 데이터베이스에 대한 적절한 성능 수준을 사용하여 가격에 3단계에서 풀 가격을 비교합니다.



## 기존 SQL 데이터베이스의 최적 풀 eDTU 크기 결정 

탄력적 데이터베이스 풀의 최적 크기는 eDTU 집계와 풀의 모든 데이터베이스에 필요한 저장소 리소스에 따라 달라집니다. 이것은 다음 두 수량 중 큰 값 결정을 포함합니다.

* 풀에 있는 모든 데이터베이스의 최대 DTU 수입니다.
* 풀에 있는 모든 데이터베이스의 최대 저장된 바이트 수입니다. 

사용 가능한 크기에 대해서는 [탄력적 데이터베이스 풀과 탄력적 데이터베이스에 대한 eDTU 및 저장소 제한](sql-database-elastic-pool-reference.md#edtu-and-storage-limits-for-elastic-pools-and-elastic-databases)을 참조하세요.


### 크기 권장 사항에 대해 서비스 계층 관리자 (STA) 및 동적 관리 뷰 (Dmv)를 사용합니다.   

STA 및 DMV는 탄력적 데이터베이스 풀의 크기 조정을 위해 다양한 도구 옵션과 기능을 제공합니다. 사용된 도구 옵션에 관계없이 크기 예측은 탄력적 데이터베이스 풀의 초기 평가 및 생성 시에만 사용해야 합니다. 풀을 만든 후 해당 리소스 사용량을 정확하게 모니터링해야 하고 필요에 따라 풀의 성능 설정을 위/아래로 조정해야 합니다.

**STA**<br>STA는 기존 SQL 데이터베이스 서버에서 데이터베이스의 기록 리소스 사용률을 자동으로 평가하고 적절한 탄력적 데이터베이스 풀 구성을 권장하는 [Azure 포털](https://portal.azure.com)의 기본 제공 도구입니다. 자세한 내용은 [탄력적 데이터베이스 풀 가격 책정 계층 권장 사항](sql-database-elastic-pool-portal.md#elastic-database-pool-pricing-tier-recommendations)을 참조하세요.

**DMV 크기 조정 도구**<br>DMV 크기 조정 도구는 PowerShell 스크립트로 제공되며 서버의 기존 데이터베이스에 대한 탄력적 데이터베이스 풀의 크기 예측을 사용자 지정할 수 있게 해줍니다.

### STA 및 DMV 도구 중에서 선택 

특정 응용 프로그램을 분석 하기 위해 적절한 도구를 선택 합니다. 다음 표에서 이러한 2개의 크기조정 차이점을 요약:

| 기능 | STA | Dmv |
| :--- | :--- | :--- |
| 분석에 사용되는 데이터 샘플의 세분성입니다. | 15초 | 15초 |
| 풀과 단일 데이터 성능 수준 사이의 가격 차이를 고려합니다. | 예 | 아니요 |
| 서버 내에서 데이터베이스 분석목록을 사용자 지정할 수 있습니다. | 아니요 | 예 |
| 분석에 사용되는 기간을 사용자 지정할 수 있습니다. | 아니요 | 예 |


### STA를 사용하여 탄력적 풀 크기 추정  

STA는 데이터베이스의 사용률 기록을 평가하고 단일 데이터베이스에 대한 성능 수준 사용보다 비용 효율적인 경우 탄력적 데이터베이스 풀을 권장합니다. 풀이 권장되는 경우 도구는 권장 데이터베이스 목록을 제공하고, 각 탄력적 데이터베이스에 대한 풀 eDTU의 권장량 및 최대/최소 eDTU 설정도 제공합니다. 데이터베이스가 풀의 후보로 간주되려면 7일 이상 존재해야 합니다.

STA는 기존 서버에 탄력적 데이터베이스 풀을 추가할 때 포털에서 사용할 수 있습니다. 탄력적 데이터베이스 풀의 권장 사항을 해당 서버에 사용할 수 있는 경우 "탄력적 데이터베이스 풀" 생성 페이지에 표시됩니다. 고객은 언제든지 권장 구성을 변경하여 고유한 탄력적 데이터베이스 풀 그룹화를 만들 수 있습니다.

자세한 내용은 [탄력적 데이터베이스 풀 가격 책정 계층 권장 사항](sql-database-elastic-pool-portal.md#elastic-database-pool-pricing-tier-recommendations)을 참조하세요.

### 동적 관리 뷰(Dmv)를 사용하여 탄력적 풀 크기 추정 

[sys.dm\_db\_resource\_stats](https://msdn.microsoft.com/library/dn800981.aspx) DMV는 개별 데이터베이스의 리소스 사용률을 측정합니다. 이 DMV는 데이터베이스 성능 수준 제한을 백분율로 표시하는 데이터베이스의 로그 사용률, 로그, IO,CPU를 제공합니다. 이 데이터는 15초 간격으로 데이터베이스의 DTU사용량을 계산하는데 쓰입니다.

15초 간격의 탄력적 데이터베이스 풀에 대한 eDTU 사용률 집계는 해당 시간 동안 모든 후보 데이터베이스에 대한 eDTU 사용률을 집계하여 예측할 수 있습니다. 특정 성능 목표에 따라 샘플 데이터의 작은 비율을 취소하는 것이 합리적일 수도 있습니다. 예를 들어 모든 시간 간격에 대한 eDTU 집계의 99번째 백분위 수 값을 적용하여 이상치를 제외하고 샘플링된 시간 간격의 99%에 맞는 탄력적 데이터베이스 풀 eDTU를 제공할 수 있습니다.

## 데이터베이스 eDTU 사용량 집계를 예측하기 위한 PowerShell 스크립트

여기서는 SQL 데이터베이스 서버의 사용자 데이터베이스에 대한 eDTU 집계 값을 예측하는 샘플 PowerShell 스크립트를 제공합니다.

스크립트가 실행 되는 동안만 데이터를 수집 합니다. 일반적인 프로덕션 작업은 최소 하루동안 스크립트를 실행해야 하지만, 일주일 혹은 그 이상 실행하는 것은 더욱더 정확한 예상값을 제공합니다. 일반적인 데이터베이스 작업 부하를 나타내는 시간 기간에 스크립트를 실행 합니다.

> [AZURE.IMPORTANT]스크립트를 실행 하는 동안 PowerShell 창을 꼭 열어두어야 합니다. 스크립트를 계속 실행하여 기본 및 최대 사용량 시간의 일반적인 증가를 나타내는 데이터를 충분히 캡쳐하기 전까지는 절대로 PowerShell 창을 닫지마세요.

### 필수 조건 

스크립트를 실행 하기 전에 다음을 설치 합니다.:

- 최신 [PowerShell 명령줄 도구](http://go.microsoft.com/?linkid=9811175&clcid=0x409)
- [SQL Server 2014 기능 팩](https://www.microsoft.com/download/details.aspx?id=42295)


### 스크립트 세부 정보


클라우드의 VM 또는 로컬 컴퓨터에서 스크립트를 실행할 수 있습니다. 로컬 컴퓨터에서 실행할 경우에 스크립트를 대상 데이터베이스에서 데이터를 다운로드 해야 하기 때문에 데이터 송신 요금이 발생할 수도 있습니다. 아래에서 데이터베이스의 수 및 스크립트를 실행 하는 기간에 따라 예상 데이터 볼륨을 예측 합니다. Azure 데이터 전송 비용은 [데이터 전송 가격 정보](http://azure.microsoft.com/pricing/details/data-transfers/)를 참조하세요.
       
 -     시간당 1 데이터베이스 = 38 (kb)
 -     하루 1 데이터베이스 = 900 KB
 -     주당 1 데이터베이스 = 6MB
 -     하루에 100 데이터베이스= 90MB
 -     주당 500 데이터베이스 = 3GB

스크립트는 표준 탄력적 풀 계층의 현재 공용 미리 보기 기능에 적합 하지 않은 특정 데이터베이스를 제외 합니다. 대상 서버에서 추가 데이터베이스를 제외 해야하는 경우에 조건에 맞게 스크립트를 변경할 수 있습니다. 기본적으로 스크립트는 다음에 대한 정보를 컴파일하지 않습니다.

* 탄력적 데이터베이스 (탄력적 풀에서 이미 포함).
* 서버의 Master 데이터베이스

스크립트는 출력 데이터베이스 분석에 대한 중간 데이터를 저장 해야 합니다. 신규 또는 기존 데이터베이스를 사용할 수 있습니다. 필수적이지 않지만 도구를 실행할 때 출력 데이터베이스 분석 결과 영향을 방지하려면 다른 서버에 있어야 합니다. 출력 되는 데이터베이스에 최소한의 성능 수준은 최소한 S0 또는 그 이상을 추천합니다. 많은 수의 데이터베이스에 대한 긴 기간 데이터를 수집 하는 경우 더 높은 성능 수준으로 출력 데이터베이스를 업그레이드 하는 것이 좋습니다.

스크립트는 대상 서버 (탄력적 데이터베이스 풀 후보)에 연결 하는데 사용할 자격 증명을 제공할 수 있습니다 "abcdef.database.windows.net"와 같은 전체 서버 이름의 필요 합니다. 현재 스크립트는 한 번에 둘 이상 서버의 분석을 지원하지 않습니다.


초기 매개 변수 집합에 대한 값을 제출한 후 Azure 계정에 로그온하라는 메시지가 표시됩니다. 출력 데이터베이스 서버가 아닌 대상 서버에 로그온 됩니다.
	
다음 경고가 뜬 경우에는, 경고를 무시하고 스크립트를 실행할 수 있습니다.

- 경고: Switch-azuremode cmdlet이 반대되었습니다.
- 경고: SQL Server 서비스 정보를 얻을 수 없습니다. 다음 오류로 인해 'Microsoft.Azure.Commands.Sql.dll’의 WMI에 연결 하지 못했습니다: RPC 서버를 사용할 수 없습니다.

스크립트가 완료되면 대상 서버의 모든 후보 데이터베이스를 포함하는 탄력적 풀에 필요한 eDTU의 예상 수를 출력합니다. 이러한 데이터베이스를 보관하는 탄력적 데이터베이스 풀을 만들고 구성하는 데 이 예상 eDTU를 사용할 수 있습니다. 풀을 만들고 데이터베이스를 풀로 이동한 후 며칠 동안 모니터링해야 하며 필요에 따라 풀 DTU 구성을 조정해야 합니다.

> [AZURE.IMPORTANT]이 스크립트에서는 Azure PowerShell 버전 1.0 *미만*의 명령을 포함합니다. **Get-Module azure | format-table version** 명령으로 Azure PowerShell의 버전을 확인할 수 있습니다. 자세한 내용은 [Azure PowerShell에서 Switch-AzureMode 중단](https://github.com/Azure/azure-powershell/wiki/Deprecation-of-Switch-AzureMode-in-Azure-PowerShell)을 참조하세요.


    
    param (
    [Parameter(Mandatory=$true)][string]$AzureSubscriptionName, # Azure Subscription name - can be found on the Azure portal: https://portal.azure.com/
    [Parameter(Mandatory=$true)][string]$ResourceGroupName, # Resource Group name - can be found on the Azure portal: https://portal.azure.com/
    [Parameter(Mandatory=$true)][string]$servername, # full server name like "abcdefg.database.windows.net"
    [Parameter(Mandatory=$true)][string]$username, # user name
    [Parameter(Mandatory=$true)][string]$serverPassword, # password
    [Parameter(Mandatory=$true)][string]$outputServerName, # metrics collection database for analysis. full server name like "zyxwvu.database.windows.net"
    [Parameter(Mandatory=$true)][string]$outputdatabaseName,
    [Parameter(Mandatory=$true)][string]$outputDBUsername,
    [Parameter(Mandatory=$true)][string]$outputDBpassword,
    [Parameter(Mandatory=$true)][int]$duration_minutes # How long to run. Recommend to run for the period of time when your typical workload is running. At least 10 mins.
    )
    
    Add-AzureAccount 
    Select-AzureSubscription $AzureSubscriptionName
    Switch-AzureMode AzureResourceManager
    
    $server = Get-AzureSqlServer -ServerName $servername.Split('.')[0] -ResourceGroupName $ResourceGroupName
    
    # Check version/upgrade status of the server
    $upgradestatus = Get-AzureSqlServerUpgrade -ServerName $servername.Split('.')[0] -ResourceGroupName $ResourceGroupName
    $version = ""
    if ([string]::IsNullOrWhiteSpace($server.ServerVersion)) 
    {
    $version = $upgradestatus.Status
    }
    else
    {
    $version = $server.ServerVersion
    }
    
    # For Elastic database pool candidates, we exclude master, and any databases that are already in a pool. You may add more databases to the excluded list below as needed
    $ListOfDBs = Get-AzureSqlDatabase -ServerName $servername.Split('.')[0] -ResourceGroupName $ResourceGroupName | Where-Object {$_.DatabaseName -notin ("master") -and $_.CurrentServiceLevelObjectiveName -notin ("ElasticPool") -and $_.CurrentServiceObjectiveName -notin ("ElasticPool")}
    
    $outputConnectionString = "Data Source=$outputServerName;Integrated Security=false;Initial Catalog=$outputdatabaseName;User Id=$outputDBUsername;Password=$outputDBpassword"
    $destinationTableName = "resource_stats_output"
    
    # Create a table in output database for metrics collection
    $sql = "
    IF  NOT EXISTS (SELECT * FROM sys.objects 
    WHERE object_id = OBJECT_ID(N'$($destinationTableName)') AND type in (N'U'))
    
    BEGIN
    Create Table $($destinationTableName) (database_name varchar(128), slo varchar(20), end_time datetime, avg_cpu float, avg_io float, avg_log float, db_size float);
    Create Clustered Index ci_endtime ON $($destinationTableName) (end_time);
    END
    TRUNCATE TABLE $($destinationTableName);
    "
    Invoke-Sqlcmd -ServerInstance $outputServerName -Database $outputdatabaseName -Username $outputDBUsername -Password $outputDBpassword -Query $sql -ConnectionTimeout 120 -QueryTimeout 120 
    
    # waittime (minutes) is interval between data collection queries in the loop below.
    $Waittime = 10
    $end_Time = [DateTime]::UtcNow
    $start_time = $end_time.AddMinutes(-$Waittime)
    $finish_time = $end_Time.AddMinutes($duration_minutes)
    
    While ($end_time -lt $finish_time)
    {
    Write-Host "Collecting metrics..." 
    foreach ($db in $ListOfDBs)
    {
    if ($version -in ("12.0", "Completed")) # for V12 databases 
    {
    $sql = "Declare @dbname varchar(128) = '$($db.DatabaseName)';"
    $sql += "Declare @SLO varchar(20) = '$($db.CurrentServiceLevelObjectiveName)';"
    $sql+= "
    Declare @DTU_cap int, @db_size float;
    Select @DTU_cap = CASE @SLO 
    WHEN 'Basic' THEN 5
    WHEN 'S0' THEN 10
    WHEN 'S1' THEN 20
    WHEN 'S2' THEN 50
    WHEN 'S3' THEN 100
    WHEN 'P1' THEN 125
    WHEN 'P2' THEN 250
    WHEN 'P3' THEN 1000
    ELSE 50 -- assume Web/Business DBs
    END
    SELECT @db_size = SUM(reserved_page_count) * 8.0/1024/1024 FROM sys.dm_db_partition_stats
    SELECT @dbname as database_name, @SLO, dateadd(second, round(datediff(second, '2015-01-01', end_time) / 15.0, 0) * 15,'2015-01-01')
    as end_time, avg_cpu_percent * (@DTU_cap/100.0) AS avg_cpu, avg_data_io_percent * (@DTU_cap/100.0) AS avg_io, avg_log_write_percent * (@DTU_cap/100.0) AS avg_log, @db_size as db_size FROM sys.dm_db_resource_stats
    WHERE end_time > '$($start_time)' and end_time <= '$($end_time)';
    " 
    }
    else
    {
    $sql = "Declare @dbname varchar(128) = '$($db.DatabaseName)';"
    $sql += "Declare @SLO varchar(20) = '$($db.CurrentServiceLevelObjectiveName)';"
    $sql+= "
    Declare @DTU_cap int, @db_size float;
    Select @DTU_cap = CASE @SLO 
    WHEN 'Basic' THEN 5
    WHEN 'S0' THEN 10
    WHEN 'S1' THEN 20
    WHEN 'S2' THEN 50
    WHEN 'P1' THEN 100
    WHEN 'P2' THEN 200
    WHEN 'P3' THEN 800
    ELSE 50 -- assume Web/Business DBs
    END
    SELECT @db_size = SUM(reserved_page_count) * 8.0/1024/1024 from sys.dm_db_partition_stats
    SELECT @dbname as database_name, @SLO, dateadd(second, round(datediff(second, '2015-01-01', end_time) / 15.0, 0) * 15,'2015-01-01')
    as end_time, avg_cpu_percent * (@DTU_cap/100.0) AS avg_cpu, avg_data_io_percent * (@DTU_cap/100.0) AS avg_io, avg_log_write_percent * (@DTU_cap/100.0) AS avg_log, @db_size as db_size FROM sys.dm_db_resource_stats
    WHERE end_time > '$($start_time)' and end_time <= '$($end_time)';
    " 
    }
    
    $result = Invoke-Sqlcmd -ServerInstance $servername -Database $db.DatabaseName -Username $username -Password $serverPassword -Query $sql -ConnectionTimeout 120 -QueryTimeout 3600 
    #bulk copy the metrics to output database
    $bulkCopy = new-object ("Data.SqlClient.SqlBulkCopy") $outputConnectionString 
    $bulkCopy.BulkCopyTimeout = 600
    $bulkCopy.DestinationTableName = "$destinationTableName";
    $bulkCopy.WriteToServer($result);
    
    }
    
    $start_time = $start_time.AddMinutes($Waittime)
    $end_time = $end_time.AddMinutes($Waittime)
    Write-Host $start_time
    Write-Host $end_time
    do {
    Start-Sleep 1
       }
    until (([DateTime]::UtcNow) -ge $end_time)
    }
    
    Write-Host "Analyzing the collected metrics...."
    # Analysis query that does aggregation of the resource metrics to calculate pool size.
    $sql1 = 'Declare @DTU_Perf_99 as float, @DTU_Storage as float;
    WITH group_stats AS
    (
    SELECT end_time, SUM(db_size) AS avg_group_Storage, SUM(avg_cpu) AS avg_group_cpu, SUM(avg_io) AS avg_group_io,SUM(avg_log) AS avg_group_log
    FROM resource_stats_output 
    WHERE slo LIKE '
    
    $sql2 = '
    GROUP BY end_time
    )
    -- calculate aggregate storage and DTUs for all DBs in the group
    , group_DTU AS
    (
    SELECT end_time, avg_group_Storage, 
    (SELECT Max(v)
       FROM (VALUES (avg_group_cpu), (avg_group_log), (avg_group_io)) AS value(v)) AS avg_group_DTU
    FROM group_stats
    )
    -- Get top 1 percent of the storage and DTU utilization samples.
    , top1_percent AS (
    SELECT TOP 1 PERCENT avg_group_Storage, avg_group_dtu FROM group_dtu ORDER BY [avg_group_DTU] DESC
    )
    
    -- Max and 99th percentile DTU for the given list of databases if converted into an elastic pool. Storage is increased by factor of 1.25 to accommodate for future growth. Currently storage limit of the pool is determined by the amount of DTUs based on 1GB/DTU.
    --SELECT MAX(avg_group_Storage)*1.25/1024.0 AS Group_Storage_DTU, MAX(avg_group_dtu) AS Group_Performance_DTU, MIN(avg_group_dtu) AS Group_Performance_DTU_99th_percentile FROM top1_percent;
    SELECT @DTU_Storage = MAX(avg_group_Storage)*1.25/1024.0, @DTU_Perf_99 = MIN(avg_group_dtu) FROM top1_percent;
    IF @DTU_Storage > @DTU_Perf_99 
    SELECT ''Total number of DTUs dominated by storage: '' + convert(varchar(100), @DTU_Storage)
    ELSE 
    SELECT ''Total number of DTUs dominated by resource consumption: '' + convert(varchar(100), @DTU_Perf_99)'
    
    #check if there are any web/biz edition dbs in the collected metrics
    $checkslo = "SELECT TOP 1 slo FROM resource_stats_output WHERE slo LIKE 'shared%'"
    $output = Invoke-Sqlcmd -ServerInstance $outputServerName -Database $outputdatabaseName -Username $outputDBUsername -Password $outputDBpassword -Query $checkslo -QueryTimeout 3600 | select -expand slo
    if ($output -like "Shared*")
    {
    write-host "`nWeb/Business edition:" -BackgroundColor Green -ForegroundColor Black
    $sql = $sql1 + "'Shared%'"  + $sql2
    $data = Invoke-Sqlcmd -ServerInstance $outputServerName -Database $outputdatabaseName -Username $outputDBUsername -Password $outputDBpassword -Query $sql -QueryTimeout 3600
    $data | %{'{0}' -f $_[0]}
    }
    
    #check if there are any basic edition dbs in the collected metrics
    $checkslo = "SELECT TOP 1 slo FROM resource_stats_output WHERE slo LIKE 'Basic%'"
    $output = Invoke-Sqlcmd -ServerInstance $outputServerName -Database $outputdatabaseName -Username $outputDBUsername -Password $outputDBpassword -Query $checkslo -QueryTimeout 3600 | select -expand slo
    if ($output -like "Basic*")
    {
    write-host "`nBasic edition:" -BackgroundColor Green -ForegroundColor Black
    $sql = $sql1 + "'Basic%'"  + $sql2
    $data = Invoke-Sqlcmd -ServerInstance $outputServerName -Database $outputdatabaseName -Username $outputDBUsername -Password $outputDBpassword -Query $sql -QueryTimeout 3600
    $data | %{'{0}' -f $_[0]} 
    }
    
    #check if there are any standard edition dbs in the collected metrics
    $checkslo = "SELECT TOP 1 slo FROM resource_stats_output WHERE slo LIKE 'S%' AND slo NOT LIKE 'Shared%'"
    $output = Invoke-Sqlcmd -ServerInstance $outputServerName -Database $outputdatabaseName -Username $outputDBUsername -Password $outputDBpassword -Query $checkslo -QueryTimeout 3600 | select -expand slo
    if ($output -like "S*")
    {
    write-host "`nStandard edition:" -BackgroundColor Green -ForegroundColor Black
    $sql = $sql1 + "'S%' AND slo NOT LIKE 'Shared%'"  + $sql2
    $data = Invoke-Sqlcmd -ServerInstance $outputServerName -Database $outputdatabaseName -Username $outputDBUsername -Password $outputDBpassword -Query $sql -QueryTimeout 3600
    $data | %{'{0}' -f $_[0]}
    }
    
    #check if there are any premium edition dbs in the collected metrics
    $checkslo = "SELECT TOP 1 slo FROM resource_stats_output WHERE slo LIKE 'P%'"
    $output = Invoke-Sqlcmd -ServerInstance $outputServerName -Database $outputdatabaseName -Username $outputDBUsername -Password $outputDBpassword -Query $checkslo -QueryTimeout 3600 | select -expand slo
    if ($output -like "P*")
    {
    write-host "`nPremium edition:" -BackgroundColor Green -ForegroundColor Black
    $sql = $sql1 + "'P%'"  + $sql2
    $data = Invoke-Sqlcmd -ServerInstance $outputServerName -Database $outputdatabaseName -Username $outputDBUsername -Password $outputDBpassword -Query $sql -QueryTimeout 3600
    $data | %{'{0}' -f $_[0]}
    }
        

## 요약

모든 단일 데이터베이스에는 탄력적 데이터베이스 풀의 적합한 후보가 아닙니다. 사용 패턴이 낮은 평균 사용률과 사용률 급증이 비교적 드문 데이터베이스는 탄력적 데이터베이스 풀에 적합한 후보입니다. 응용 프로그램 사용 패턴은 동적이고, 따라서 탄력적 데이터베이스 풀이 데이터베이스 일부 혹은 전체에게 좋은 선택인지 아닌지 초기 평가를 내리기 위해 이 문서에서 설명한 정보와 도구를 사용하세요. 이 문서는 탄력적 데이터베이스 풀이 잘 맞는지 여부에 대한 당신의 결정을 돕기위한 기점입니다. 계속 기록 리소스 사용량(STA 또는 DMVs를 사용)을 모니터링하고 지속적으로 데이터베이스의 성능 수준을 재평가 해야한다는 것을 기억하세요. 탄력적 데이터베이스 풀 내부와 외부로 데이터베이스를 쉽게 이동할 수 있으며, 데이터베이스 수가 매우 많은 경우 다양한 크기의 여러 풀에 데이터베이스를 나눌 수 있습니다.



<!--Image references-->
[1]: ./media/sql-database-elastic-pool-guidance/one-database.png
[2]: ./media/sql-database-elastic-pool-guidance/four-databases.png
[3]: ./media/sql-database-elastic-pool-guidance/twenty-databases.png

<!---HONumber=AcomDC_1203_2015-->