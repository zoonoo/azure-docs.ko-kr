<properties 
	pageTitle="탄력적 데이터베이스 풀의 가격 및 성능 고려 사항ㅣAzure SQL 데이터베이스 탄력적 데이터베이스 풀" 
	description="탄력적 데이터베이스 풀은 탄력적 데이터베이스 그룹에서 공유하는 가용 리소스의 컬렉션입니다. 이 문서는 탄력적 데이터베이스 풀을 사용하여 데이터베이스의 그룹에 대한 적합성을 평가하는데 도움이 되는 지침을 제공합니다." 
	services="sql-database" 
	documentationCenter="" 
	authors="stevestein" 
	manager="jeffreyg" 
	editor=""/>

<tags 
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="06/24/2015" 
	ms.author="sstein" 
	ms.workload="data-management" 
	ms.topic="article" 
	ms.tgt_pltfrm="NA"/>


# 탄력적 데이터베이스 풀의 가격 및 성능 고려 사항


이 문서에서는 데이터베이스 사용 패턴 및 가격 차이 탄력적 풀 및 단일 데이터베이스를 기반으로 평가 하는 탄력적 데이터베이스 풀을 사용하여 데이터베이스의 그룹에 대한 효율적인 비용 경우 도움이 되는 지침을 제공 합니다. 추가 지침은 SQL 데이터베이스의 기존 집합에 필요한 현재 풀 크기를 결정 하는데 도움이 되도록 제공 됩니다.

- 탄력적 데이터베이스 풀의 개요는 [SQL 데이터베이스 탄력적 데이터베이스 풀](sql-database-elastic-pool.md)을 참조하십시오.
- 탄력적 데이터베이스 풀의 자세한 정보는 [SQL 데이터베이스 탄력적 데이터베이스 풀 참조](sql-database-elastic-pool-reference.md)를 참고하십시오.


> [AZURE.NOTE]탄력적 풀은 현재 Preview이며, SQL Database V12 서버와만 사용할 수 있습니다.

## 탄력적 데이터베이스 풀

SaaS Isv 여러 데이터베이스로 구성 된 대규모 데이터 계층 위에 구축 하는 응용 프로그램을 개발 합니다. 일반적인 응용 프로그램 패턴은 개개인의 다양하고 예측할 수 없는 사용패턴을 가진 다른 고객을 가진 각각의 데이터베이스에 관한 것입니다. 개별적인 각각의 데이터베이스의 예측하는 리소스 요구를 예측하는 것은 ISV에게는 어려울 수 있습니다. 이러한 상황에서, ISV는 모든 데이터 베이스에 대한 긍정적인 처리량 및 반응속도를 보장하려면 리소스에 상당한 지출이 초과 공급될 수 있습니다. 또는 ISV는 짧은 시간과 고객에게 성능저하 경험의 위험을 줄 수 있습니다.

Azure SQL 데이터베이스의 탄력적 데이터베이스 풀은 SaaS ISVs를 각각의 데이터베이스에 성능 탄력성을 제공하는 동시에 규정된 예산내의 데이터베이스 그룹에 가격 성능을 최적화를 가능하게 합니다. 탄력적 풀의 사용량을 예측할 수 없는 기간을 수용하는 다양한 데이터베이스와 공유하여 데이터베이스 처리량 단위(Database Throughput Units, DTU)를 구입하여 ISV를 사용 가능하게 합니다 탄력적 풀에 대한 DTU 요구 사항은 해당 데이터베이스의 집계 사용률에 의해 결정 됩니다. 탄력적 풀에 사용 가능한 DTU양은 ISV 예산에 의해 제어됩니다. 탄력적 풀 은 예산 성능과 해당 풀의 반대편에 영향을 주기 때문에 ISV를 더욱 쉽게 만들어줍니다. 모든 필수 DTU 보증 혹은 데이터베이스 캡을 설정하면, ISV는 데이터베이스를 탄력적 풀에 쉽게 추가 할 수 있습니다. 그리고 예산의 기초하여 예산의 DTU를 설정할 수 있습니다. ISVs의 탄력적 풀을 사용하여 린스타트업에서부터 언제나 increasing scale의 성숙한 비즈니스까지 그들의 서비스를 원활하게 증가시킬 수 있습니다.
  


## 탄력적 데이터베이스 풀을 고려하는 경우

탄력적 풀은 특정사용패턴을 사용한 많은 수의 데이터 베이스에 가장 적합합니다. 주어진 데이터 베이스에 대해, 이 패턴은 상대적으로 사용률 급증이 드물고 평균 사용률이 낮음으로 규정됩니다.

The more databases you can add to a pool the greater your savings become, 그러나 응용 프로그램 사용패턴에 따라, 4개의 S3데이터베이스 보다 적은 수로 비용을 절감 할 수 있습니다.

다음 섹션에서는 탄력적 풀을 사용하여 데이터베이스의 특정 컬렉션에 적합할 경우 평가 하는 방법을 이해 하는 데 도움이 됩니다.

### 데이터베이스 사용량 패턴 평가

다음 그림에는 대부분의 시간을 유휴 상태로 있지만 정기적으로 갑작스러운 활동이 증가하는 데이터베이스의 예가 나와있습니다. 다음은 탄력적 풀에 대한 가장 적합한 사용률 패턴입니다.
 
   ![하나의 데이터베이스][1]

위의 있는 한 시간 주기 그림에서 DB1이 90 DTUs에서 정점을 찍지만, 전체 평균 사용량은 5dtu보다 낮습니다. 단일 데이터베이스에서 이 작업을 실행 하려면 S3 성능 수준이 필요 합니다. 그러나 대부분의 리소스가 낮은 활동기간동안 사용되지 않은 채 남아있습니다.

탄력적 풀은 이러한 사용 하지 않는 Dtu 여러 데이터베이스 간에 공유 하도록 허용하고 있으므로 필요한 DTUs의 양과 전체 비용을 줄입니다.

앞의 예제에 기초하여, DB1같은 유사한 사용률 패턴의 추가 데이터베이스가 있다고 가정합니다. 아래의 두 그림에는, 4개의 데이터베이스 사용률과 20개의 데이터베이스가 시간에 따라 사용률의 특성이 겹치지 않는다는 것을 설명하기 위해 같은 그래프에 겹쳐져 있습니다.

   ![4개의 데이터베이스][2]

   ![20개의 데이터베이스][3]

20개의 데이터베이스간의 DTU 사용률 집계는 그래프 위에 검은 선으로 표시됐습니다. 이것은 DTU사용률 집계가 절대로 100 DTU를 초과하지 않는다는 것을 보여주고, 20개의 데이터베이스가 이 시간동안 100DTUs를 공유할 수 있다는 것을 나타냅니다. 이러한 결과 20X DTUs의 감소와 6x 가격 감소를 비교합니다. 단일 데이터베이스의 S3 퍼포먼스 수준내의 각각의 데이터베이스를 배치하기위해


이 예는 다음 이유로 이상적입니다.

- 최고사용률과 데이터베이스당 평균 사용률간에 큰차이가 있습니다.  
- 각 데이터베이스의 최고사용률이 시간내에 여러 지점에서 발생합니다. 
- Dtu는 많은 수의 데이터베이스 간에 공유 됩니다.


탄력적 풀의 가격은 풀DTUs의 함수와 데이터베이스의 수가 됩니다. GA 가격의 풀에 대한 DTU 유닛 가격이 단일 데이터베이스의 DTU 가격보다 3배는 훌륭하고, Pool DTU는 많은 데이터베이스들에게 공유할 수 있고, 대부분의 경우 총 DTU보다 적은 수를 필요로 합니다. 가격 및 DTU 공유에서 이러한 차이점은 풀을 제공할 수 있는 가격 절감 가능성의 기반이 됩니다.

<br>

다음 규칙이 데이터베이스 수 및 데이터베이스 사용에 관련된 탄력적 풀이 단일 데이터베이스에 대한 성능 수준을 사용하여 비교할 비용 절감을 제공하는데 도움이 됩니다. 지침 일반 공급 (GA) 가격을 기반으로 합니다. GA 가격은 미리보기 중 50%할인되고, 이 rule of thumb는 비교적 보수적임을 참고합니다.


### 데이터베이스의 최소 수

GA 가격과 3개 이상의 데이터베이스에 의해 1개의 DTU가 공유 가능할 수 있게 하는 것이 선택될 경우 탄력적 풀은 더욱 가성비가 뛰어납니다. 이것은 단일 데이터베이스 성능 수준의 DTUs의 합보다 풀의 dtus가 3배 더 많다는 것을 의미합니다. 사용 가능한 크기에 대해서는 [탄력적 풀 및 탄력적 데이터베이스에 대한 DTU 및 저장소 제한](sql-database-elastic-pool-reference.md#dtu-and-storage-limits-for-elastic-pools-and-elastic-databases)을 참조하세요.


***예제***<br> 100DTU 탄력적 풀이 단일데이터베이스의 성능 수준을 사용하는 것보다 비용효율이 더 크려면 최소 4개의 S3데이터베이스 혹은 36개의 S0데이터 베이스가 필요합니다. (가격 미리보기 참고, 2개의 S3데이터 베이스 또는 17개의 S0데이터베이스의 손익분기점).



### 최대 동시에 최고 데이터베이스

단일 데이터베이스 성능수준을 사용할 때 Dtu를 공유 하 여 풀의 일부 데이터 베이스가 사용 가능 제한까지 동시에 사용될 수 있습니다. 더 적은 데이터베이스를 동시에 최대, 낮을수록 DTU를 설정할 수 있습니다 풀 DTU의 비용 효율성이 높아집니다. 일반적으로 풀에 있는 데이터베이스 1/3 이상은 자신의 DTU 한계까지 동시에 최고를 찍으면 안됩니다.

***예제***<br> 200 DTU 풀에서 4 S3 데이터베이스에 대한 비용을 줄이기 위해, 최대 2개의 데이터베이스만의 해당 사용률의 정점을 찍었습니다. 그와 반대로, 4개의 s3중에 2개이상이 동시에 정점을 찍은 경우 200DTUs이상으로 풀을 조정할 것입니다. 그리고 200 개 이상의 Dtu의 크기를 조정하는 경우, 단일데이터베이스의 성능수준보다 더 낮은 가격을 고수하기 위해 더 많은 S3데이터베이스가 풀에 추가될 것입니다.


예에서는 풀에 있는 다른 데이터베이스의 사용률을 고려 하지 않습니다. 모든 데이터베이스 시간에 특정된 시점에서 일부 사용률의 경우, 데이터베이스의 1/3 보다 작은 사용률이 동시에 정점에 달할 수 있습니다.


### 데이터베이스당 DTU 사용률

데이터 베이스 사용률의 최고와 평균 사이의 큰 차이는 장시간 동안 낮은 사용률 그리고 짧은 시간 동안 큰사용률을 나타냅니다. 이 사용률 패턴은 데이터베이스에서 리소스를 공유 하기에 이상적입니다. 최고 사용률이 평균사용률의 3배 이상이 될때 풀에 대한 데이터베이스를 고려해야 합니다.

    
***예제***<br> S3데이터베이스가 최고 사용률을 100dtus를 찍고 평균 사용률을 30dtus나 그 이하를 찍으면 탄력적 풀의 dtu 공유의 좋은 후보입니다 그렇지 않고, S1데이터 베이스가 최고사용률 20dtusr고 평균사용률이 7dtus거나 그보다 작아도 탄력적 풀의 좋은 후보입니다.
    

## 탄력적 풀과 단일 데이터베이스간의 가격차이 체험비교 

다음과 같은 체험비교는 탄력적 풀이 개별 단일 데이터베이스보다 더욱 비용 효율적인지 아닌지 측정할 때 도움이 됩니다.

1. 풀에 예상 dtus가 다음과 같을 때 필요합니다.
    
    MAX (* 총 수의 Db * * *DTU 사용률 DB 당 평균*, *데 최고 동시에 횟수 Db* * *DB 당 최대 DTU 사용률*)

2. 1단계에서 가장 작은 사용 가능한 dtu값을 예상보다 더 큰 풀에서 선택합니다. 사용 가능한 dtu선택, dtu목록의 유효한 값을 보려면 : [DTU 및 저장소 풀을 확대/축소 및 확대/축소 데이터베이스 제한](sql-database-elastic-pool-reference.md#dtu-and-storage-limits-for-elastic-pools-and-elastic-databases)을 참고하세요.

    


3. 다음과 같이 풀에 대한 가격을 계산 합니다.

    풀 가격 = (* 풀 Dtu * * *풀 DTU 단가*) + (* 총 번호 Db * * *풀 DB 단가*)

    가격 정보는 [SQL 데이터베이스 가격 정보](http://azure.microsoft.com/pricing/details/sql-database/)를 참조하세요.


4. 단일 데이터베이스에 대한 적절한 성능 수준을 사용하여 가격에 3단계에서 풀 가격을 비교합니다.



## 기존 SQL 데이터베이스에 대한 최상의 풀 DTU 크기를 결정합니다. 

탄력적 풀의 적절한 크기는 dtus집계와 풀의 모든 데이터베이스에 대해 필요한 저장된 원본의 값에 의해 달라집니다. 이것은 다음 두 수량 중 큰 값 결정을 포함합니다.

* 풀에 있는 모든 데이터베이스의 최대 DTU 수입니다.
* 풀에 있는 모든 데이터베이스의 최대 저장된 바이트 수입니다. 

참고 Standard 서비스 계층은 저장소의 1GB당 1dtu가 허용됩니다. 예를 들어, 풀 200 Dtu를 구성 된 경우 저장소 한계는 200GB입니다.

### 크기 권장 사항에 대해 서비스 계층 관리자 (STA) 및 동적 관리 뷰 (Dmv)를 사용합니다.   

STA 및 Dmv는 탄력적 풀의 다른 도구옵션과 크기 조정 기능을 제공합니다. 도구 옵션 사용에 관계 없이 예상 규모는 탄력적 풀의 생성과 초기 평가에만 사용해야 합니다. 탄력적 풀을 만든 후 해당 리소스 사용량을 정확 하 게 모니터링 해야 하 고 필요에 따라 위쪽과 아래쪽 풀의 성능 설정을 조정 합니다.

**STA**<br>STA는 자동으로 기존 SQL 데이터베이스 서버에서 데이터베이스의 기록 리소스 사용률을 평가 하 고 적절한 탄력적 풀 구성을 권장 하는 Azure 포털의 기본 제공 도구입니다.

**DMV크기조정도구**<br>DMV 크기 조정 도구는 PowerShell 스크립트로 제공 되 고 기존 데이터베이스 서버에 대한 탄력적 풀의 크기 예측을 사용자 지정할 수 있습니다.

### STA 및 DMV 도구 중에서 선택 

특정 응용 프로그램을 분석 하기 위해 적절한 도구를 선택 합니다. 다음 표에서 이러한 2개의 크기조정 차이점을 요약:

| 기능 | STA | Dmv |
| :--- | :--- | :--- |
| 분석에 사용되는 데이터 샘플의 세분성입니다. | 15초 | 15초 |
| 풀과 단일 데이터 성능 수준 사이의 가격 차이를 고려합니다. | 예 | 아니요 |
| 서버 내에서 데이터베이스 분석목록을 사용자 지정할 수 있습니다. | 아니요 | 예 |
| 분석에 사용되는 기간을 사용자 지정할 수 있습니다. | 아니요 | 예 |


### STA를 사용하여 탄력적 풀 크기 추정  

STA는 데이터베이스의 사용률 기록을 평가하고 단일 데이터베이스에 대한 성능 수준을 사용할 때 보다 더 비용 효율적이면 탄력적 풀을 권장합니다. 탄력적 풀이 권장 되는 경우 도구는 권장 데이터베이스 목록을 제공하고, pool DTUs의 양을 추천 및 각각의 탄력적 데이터베이스에 대한 DTU 최대/최소값을 설정합니다. 탄력적 풀의 후보로 간주하려면 데이터베이스에 14일 이상 있어야 합니다. 탄력적 데이터베이스 풀의 공용 미리보기는 표준으로 제한되어, 프리미엄 데이터베이스는 탄력적 풀에 대해 후보로 아직 고려되지 않습니다.

STA는 기존 서버에 탄력적 풀을 추가 하는 경우 Azure 포털에서 사용할 수 있습니다. 탄력적 풀의 권장사항이 해당 서버에 대해 사용할 수 있는 경우, “탄력적 데이터베이스 풀" 블레이드 생성이 표시됩니다. 사용자는 자신만의 탄력적 풀 그룹을 만들기 위해 항상 권장 구성을 변경할 수 있습니다.

### 동적 관리 뷰(Dmv)를 사용하여 탄력적 풀 크기 추정 

[sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) DMV 개별 데이터베이스의 리소스 사용량을 측정 합니다. 이 DMV는 데이터베이스 성능 수준 제한을 백분율로 표시하는 데이터베이스의 로그 사용률, 로그, IO,CPU를 제공합니다. 이 데이터는 15초 간격으로 데이터베이스의 DTU사용량을 계산하는데 쓰입니다.

15초 간격동안 탄력적 풀의 DTU 사용량 집계는 그 시간동안 모든 후보의 데이터베이스의 DTUI사용량을 집계하여 예상할 수 있습니다. 특정 성능 목표에 따라 샘플 데이터의 작은 비율을 취소하는 것이 합리적일 수도 있습니다. 예를 들어 모든 시간 간격에 걸쳐 집계하는 Dtu의 99번째 백분위 수 값을 적용할 제외된 분리물에 적용할 수 있습니다. 그리고 샘플 시간 간격의 99%에 맞는 탄력적 풀 DTU를 제공합니다.

## 집계 DTU 데이터베이스 사용량을 예측 하기 위한 PowerShell 스크립트

SQL 데이터베이스 서버에서 사용자 데이터베이스에 대한 집계 DTU 값을 추정하는 샘플 PowerShell 스크립트가 여기 제공 됩니다.

스크립트가 실행 되는 동안만 데이터를 수집 합니다. 일반적인 프로덕션 작업은 최소 하루동안 스크립트를 실행해야 하지만, 일주일 혹은 그 이상 실행하는 것은 더욱더 정확한 예상값을 제공합니다. 일반적인 데이터베이스 작업 부하를 나타내는 시간 기간에 스크립트를 실행 합니다.

> [AZURE.IMPORTANT]스크립트를 실행 하는 동안 PowerShell 창을 꼭 열어두어야 합니다. 스크립트를 계속 실행하여 기본 및 최대 사용량 시간의 일반적인 증가를 나타내는 데이터를 충분히 캡쳐하기 전까지는 절대로 PowerShell 창을 닫지마세요.

### 필수 조건 

스크립트를 실행 하기 전에 다음을 설치 합니다.:

- 최신 [Powershell 명령줄 도구](http://go.microsoft.com/?linkid=9811175&clcid=0x409).
- [SQL Server 2014 기능 팩](https://www.microsoft.com/download/details.aspx?id=42295).


### 스크립트 세부 정보


클라우드의 VM 또는 로컬 컴퓨터에서 스크립트를 실행할 수 있습니다. 로컬 컴퓨터에서 실행할 경우에 스크립트를 대상 데이터베이스에서 데이터를 다운로드 해야 하기 때문에 데이터 송신 요금이 발생할 수도 있습니다. 아래에서 데이터베이스의 수 및 스크립트를 실행 하는 기간에 따라 예상 데이터 볼륨을 예측 합니다. Azure 데이터 전송 비용은 [데이터 전송 가격 정보](http://azure.microsoft.com/pricing/details/data-transfers/)를 참조하세요.
       
 -     시간당 1 데이터베이스 = 38 (kb)
 -     하루 1 데이터베이스 = 900 KB
 -     주당 1 데이터베이스 = 6MB
 -     하루에 100 데이터베이스= 90MB
 -     주당 500 데이터베이스 = 3GB

스크립트는 표준 탄력적 풀 계층의 현재 공용 미리 보기 기능에 적합 하지 않은 특정 데이터베이스를 제외 합니다. 대상 서버에서 추가 데이터베이스를 제외 해야하는 경우에 조건에 맞게 스크립트를 변경할 수 있습니다. 기본적으로 스크립트는 다음에 대한 정보를 컴파일하지 않습니다.

* V12 서버에 있는 모든 프리미엄 데이터베이스.
* V11 서버의 P2 및 P3 데이터베이스
* 탄력적 데이터베이스 (탄력적 풀에서 이미 포함).
* 서버의 Master 데이터베이스

스크립트는 출력 데이터베이스 분석에 대한 중간 데이터를 저장 해야 합니다. 신규 또는 기존 데이터베이스를 사용할 수 있습니다. 필수적이지 않지만 도구를 실행할 때 출력 데이터베이스 분석 결과 영향을 방지하려면 다른 서버에 있어야 합니다. 출력 되는 데이터베이스에 최소한의 성능 수준은 최소한 S0 또는 그 이상을 추천합니다. 많은 수의 데이터베이스에 대한 긴 기간 데이터를 수집 하는 경우 더 높은 성능 수준으로 출력 데이터베이스를 업그레이드 하는 것이 좋습니다.

스크립트는 대상 서버 (탄력적 데이터베이스 풀 후보)에 연결 하는데 사용할 자격 증명을 제공할 수 있습니다 "abcdef.database.windows.net"와 같은 전체 서버 이름의 필요 합니다. 현재 스크립트는 한번에 여러 서버를 분석을 지원 하지 않습니다.



초기 매개 변수 집합에 대한 값을 제출한 후 azure 계정에 로그온 하 라는 메시지가 표시 됩니다. 출력 데이터베이스 서버가 아닌 대상 서버에 로그온 됩니다.
	
다음 경고가 뜬 경우에는, 경고를 무시하고 스크립트를 실행할 수 있습니다.

- 경고: Switch-azuremode cmdlet이 반대되었습니다.
- 경고: SQL Server 서비스 정보를 얻을 수 없습니다. 다음 오류로 인해 'Microsoft.Azure.Commands.Sql.dll’의 WMI에 연결 하지 못했습니다: RPC 서버를 사용할 수 없습니다.

스크립트가 완료 되 고 대상 서버에서 모든 후보 데이터베이스를 포함 하는 탄력적 풀에 필요한 Dtu의 예상된 수를 출력 합니다. 이 예상된 DTU는 이러한 데이터베이스를 보관 하는 탄력적 풀을 만들고 구성 하기 위해 사용할 수 있습니다. 풀이 만들어진 데이터베이스가 풀로 이동 되 면 며칠 동안 밀접 하 게 모니터링 해야 하 고 필요에 따라 풀 DTU 구성을 조정해야 합니다.


복사하기위해 전체 스크립트를 선택하려면 아무 문장이나 3번 클릭하세요. (세 번 클릭).

    
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
    
    # For Elastic database pool candidates, we exclude master, Premium SKU (P1 in previous version still qualifies), and any databases that are already in a pool. You may add more to the excluded list below as needed
    if ($version -in ("12.0", "Completed")) # This is on Azure Database V12. 
    {
    $ListOfDBs = Get-AzureSqlDatabase -ServerName $servername.Split('.')[0] -ResourceGroupName $ResourceGroupName | Where-Object {$_.DatabaseName -notin ("master") -and $_.CurrentServiceLevelObjectiveName -notin ("ElasticPool") -and $_.CurrentServiceLevelObjectiveName -notlike ("P*") -and $_.CurrentServiceObjectiveName -notin ("ElasticPool") -and $_.CurrentServiceObjectiveName -notlike ("P*")}
    }
    else # This is previous versions of Azure Database
    {
    $ListOfDBs = Get-AzureSqlDatabase -ServerName $servername.Split('.')[0] -ResourceGroupName $ResourceGroupName | Where-Object {$_.DatabaseName -notin ("master") -and $_.CurrentServiceLevelObjectiveName -notin ("ElasticPool", "P2", "P3") -and $_.CurrentServiceObjectiveName -notin ("ElasticPool", "P2", "P3")}
    }
    
    # Due to pricing model difference, we don't recommend to put less than 3 databases in a pool. 
    if ($ListOfDBs.Count -lt 3)
    {
    Write-Host "There are less than 3 qualified standalone databases in this server. Not a good candidate for Elastic Database Pool!" -ForegroundColor Red
    }
    
    $outputConnectionString = "Data Source=$outputServerName;Integrated Security=false;Initial Catalog=$outputdatabaseName;User Id=$outputDBUsername;Password=$outputDBpassword"
    $destinationTableName = "resource_stats_output"
    
    # Create a table in output database for metrics collection
    $sql = "
    IF  NOT EXISTS (SELECT * FROM sys.objects 
    WHERE object_id = OBJECT_ID(N'$($destinationTableName)') AND type in (N'U'))
    
    BEGIN
    Create Table $($destinationTableName) (database_name varchar(128), end_time datetime, avg_cpu float, avg_io float, avg_log float, db_size float);
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
    ELSE 50 -- assume Web/Business DBs
    END
    SELECT @db_size = SUM(reserved_page_count) * 8.0/1024/1024 FROM sys.dm_db_partition_stats
    SELECT @dbname as database_name, dateadd(second, round(datediff(second, '2015-01-01', end_time) / 15.0, 0) * 15,'2015-01-01')
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
    WHEN 'P1' THEN 100 -- for old version of SQL database, P1 has same performance capacity as S3.
    ELSE 50 -- assume Web/Business DBs
    END
    SELECT @db_size = SUM(reserved_page_count) * 8.0/1024/1024 from sys.dm_db_partition_stats
    SELECT @dbname as database_name, dateadd(second, round(datediff(second, '2015-01-01', end_time) / 15.0, 0) * 15,'2015-01-01')
    as end_time, avg_cpu_percent * (@DTU_cap/100.0) AS avg_cpu, avg_data_io_percent * (@DTU_cap/100.0) AS avg_io, avg_log_write_percent * (@DTU_cap/100.0) AS avg_log, @db_size as db_size FROM sys.dm_db_resource_stats
    WHERE end_time > '$($start_time)' and end_time <= '$($end_time)';
    " 
    }
    #write-host $sql
    
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
    
    
    # Analysis query that does aggregation of the resource metrics to calculate pool size.
    $sql = 'Declare @DTU_Perf_99 as float, @DTU_Storage as float;
    WITH group_stats AS
    (
    SELECT end_time, SUM(db_size) AS avg_group_Storage, SUM(avg_cpu) AS avg_group_cpu, SUM(avg_io) AS avg_group_io,SUM(avg_log) AS avg_group_log
    FROM resource_stats_output
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
    SELECT @DTU_Storage AS "Pool Size DTU dominated by storage"
    ELSE 
    SELECT @DTU_Perf_99 AS "Pool Size DTU dominated by resource consumption";'
    
    #write-host $sql
    Invoke-Sqlcmd -ServerInstance $outputServerName -Database $outputdatabaseName -Username $outputDBUsername -Password $outputDBpassword -Query $sql -QueryTimeout 3600
    
    

## 요약

모든 단일 데이터베이스에는 탄력적 데이터베이스 풀의 적합한 후보가 아닙니다. 데이터베이스의 사용 패턴이 낮은 평균 사용률과 사용률 급증이 비교적 드문 경우 탄력적 풀의 최상의 후보입니다. 응용 프로그램 사용 패턴은 동적이고, 따라서 탄력적 데이터베이스 풀이 데이터베이스 일부 혹은 전체에게 좋은 선택인지 아닌지 초기 평가를 내리기 위해 이 문서에서 설명한 정보와 도구를 사용하세요. 이 문서는 탄력적 데이터베이스 풀이 잘 맞는지 여부에 대한 당신의 결정을 돕기위한 기점입니다. 계속 기록 리소스 사용량(STA 또는 DMVs를 사용)을 모니터링하고 지속적으로 데이터베이스의 성능 수준을 재평가 해야한다는 것을 기억하세요. 탄력적 풀에서 데이터베이스를 쉽게 이동할 수 있고, 매우 많은 수의 데이터베이스를 가지고 있는 경우, 데이터베이스를 나눌 수 있는 다양한 크기의 여러 풀을 가질 수 있다는 것을 염두하십시오.



<!--Image references-->
[1]: ./media/sql-database-elastic-pool-guidance/one-database.png
[2]: ./media/sql-database-elastic-pool-guidance/four-databases.png
[3]: ./media/sql-database-elastic-pool-guidance/twenty-databases.png

<!---HONumber=July15_HO4-->