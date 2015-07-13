<properties
   pageTitle="SQL 데이터 웨어하우스를 통한 탄력적인 성능과 확장| Microsoft Azure"
   description="데이터 웨어하우스 단위를 사용하여 계산 리소스를 증가/축소하면서 SQL 데이터 웨어하우스의 탄력성에 대해 이해합니다. 코드 예제가 제공됩니다."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="TwoUnder"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/25/2015"
   ms.author="nicw;JRJ@BigBangData.co.uk;mausher"/>

# SQL 데이터 웨어하우스를 통한 탄력적인 성능과 확장
계산 능력을 탄력적으로 늘리거나 줄이려면 SQL 데이터 웨어하우스에 할당된 데이터 웨어하우스 단위(DWU)의 수를 조정하기만 하면 됩니다. 데이터 웨어하우스 단위는 SQL 데이터 웨어하우스에서 제공하는 새로운 개념으로 이것을 간단하고 효과적으로 관리해줍니다. 이 토픽에서는 데이터 웨어하우스 단위를 사용하여 계산 능력을 탄력적으로 조정할 수 있는 방법과 함께 데이터 웨어하우스 단위에 대해 소개합니다. 이 문서는 또한 사용자 환경에 맞게 적절한 DWU 값을 설정하는 방법에 대한 일부 초기 지침도 제공합니다.

## 데이터 웨어하우스 단위란?
Microsoft는 내부적으로 수 많은 성능 벤치마크 테스트를 실행하여 고객에게 경쟁력 있는 제품이 되려면 얼마나 많은 하드웨어가 어떤 구성으로 제공되어야 하는지 결정하고 있습니다. 계산 능력의 증가/감소는 100DWU의 블록으로 실행 가능하지만 100DWU의 모든 배수가 지원되는 것은 아닙니다.

## 얼마나 많은 DWU를 사용해야 합니까?
SQL 데이터 웨어하우스는 많은 다양한 솔루션을 고객을 위해 차단 해제할 수 있습니다. 마찬가지로 고객이 실행하게 될 쿼리 유형, 고객이 사용하게 될 데이터 용량, 스키마의 아키텍처, 데이터 분산 방법, 데이터에 액세스할 사용자 수, 그 빈도 등도 매우 다양합니다.

고객 범주에 적절한 규범적인 DWU 시작점을 제공하기 보다는 이 질문을 실용적인 방식으로 접근해보겠습니다. SQL 데이터 웨어하우스의 성능은 선형적으로 조정되며 하나의 계산 규모에서 다른 규모로의 변경은(예를 들어 100DWU에서 2000DWU로) 몇 초 안에 실행됩니다. 따라서 여러 가지를 자유롭게 시도해볼 수 있으며 사용자의 시나리오에 무엇이 최적인지 결정할 수 있습니다.


1. 개발 중인 데이터 웨어하우스의 경우에는 적은 수의 DWU로 시작해 보겠습니다.
2. 응용 프로그램 성능을 모니터링하여 관찰 중인 성능과 비교해서 DWU에 익숙해질 수 있게 합니다.
3. 선형 규모를 가정함으로써 자사의 요구 사항에 맞는 최적의 성능 수준을 달성하기 위해서는 성능이 얼마나 더 빠르거나 느려야 하는지 결정합니다. 
4. 사용 중인 DWU 양을 필요에 따라 줄이거나 증가시킵니다. 그러면 서비스가 신속하게 응답하여 DWU 요구 사항에 맞게 계산 리소스를 조정합니다.
5. 자사의 요구 사항에 맞는 최적의 성능 수준에 도달할 때까지 조정합니다.

변동이 많은 워크로드를 가진 응용 프로그램의 경우에는 성능 수준을 위/아래로 움직여서 최대치와 최저치를 수용할 수 있습니다. 예를 들어, 워크로드가 일반적으로 월말에 최대치에 도달한다면 이 시기에 DWU를 추가하고 이 시기가 끝나면 다시 감소시키도록 계획할 수 있습니다.
 
## 계산 리소스 위/아래로 조정
클라우드 저장소와는 독립적으로 SQL 데이터 웨어하우스의 탄력성 덕분에 데이터 웨어하우스 단위(DWU)의 슬라이딩 규모를 사용함으로써 계산 능력을 확장, 축소 또는 일시 중지할 수 있습니다. 따라서 계산 능력을 자사에 맞게 최적으로 조정할 수 있는 유연성이 확보됩니다.

계산 능력의 증가는 [Azure 포털][]을 통해 T-SQL, REST Api 또는 Powershell을 사용하여 가능합니다. 위/아래로 조정하면 실행 중인 모든 큐 대기 활동이 취소되지만 몇 초 안에 완료되므로 더 많은 또는 더 적은 계산 능력으로 다시 시작할 수 있습니다.

다음 T-SQL 코드는 SQL 데이터 웨어하우스에 대한 DWU의 할당을 조정하는 방법을 보여줍니다.

```
ALTER DATABASE MySQLDW 
MODIFY (SERVICE_OBJECTIVE = 'DW1000')
;
```

아래 코드를 사용하여 Powershell을 통해 동일한 결과를 얻을 수도 있습니다.

```
Set-AzureSQLDatabase -DatabaseName "MySQLDW" -ServerName "MyServer.database.windows.net" -ServiceObjective "DW1000"
```

## 계산 리소스 일시 중지
SQL 데이터 웨어하우스의 고유한 특징은 요청 시 계산을 일시 중지 및 다시 시작할 수 있다는 점입니다. 팀이 밤, 주말, 공휴일 또는 기타 이유로 일정 기간 동안 데이터 웨어하우스 인스턴스를 사용하지 않는 다면 이 기간 동안 데이터 웨어하우스 인스턴스를 일시 중지하고 다시 돌아왔을 때 중지한 부분부터 다시 시작할 수 있습니다.

일시 중지 작업은 계산 리소스를 데이터 센터 내에서 사용 가능한 리소스 풀로 되돌리며, 다시 시작 작업은 설정한 DWU에 필요한 계산 리소스를 확보한 다음 데이터 웨어하우스 인스턴스에 할당합니다.

계산 능력의 일시 중지 및 다시 시작은 [Azure 포털][]을 통해 REST Api 또는 Powershell을 사용하여 가능합니다. 일시 중지는 실행 중이거나 대기 중인 모든 활동을 취소하며 돌아왔을 때는 몇 초 만에 계산 리소스를 다시 시작할 수 있습니다.

아래 코드는 PowerShell을 사용하여 일시 중지를 수행하는 방법을 보여줍니다.

```
Suspend-AzureSqlDatabase –ResourceGroupName "ResourceGroup11" –ServerName
"Server01" –DatabaseName "Database02"
```

PowerShell을 사용하면 서비스를 다시 시작하는 것이 매우 간단합니다.

```
Resume-AzureSqlDatabase –ResourceGroupName "ResourceGroup11" –ServerName "Server01" –DatabaseName "Database02"
```

PowerShell을 사용하는 방법에 대한 자세한 내용은 [PowerShell cmdlet 소개][] 문서를 참조하세요.

> [Azure.Note]저장소는 계산과는 별도이기 때문에 저장소는 일시 중지에 영향을 받지 않습니다.

## 다음 단계
성능 개요는 [성능 개요][]를 참조하세요.

<!--Image references-->

<!--Article references-->
[성능 개요]: sql-data-warehouse-overview-performance.md
[PowerShell cmdlet 소개]: sql-data-warehouse-get-started-powershell-cmdlets.md

<!--MSDN references-->


<!--Other Web references-->

[Azure 포털]: http://portal.azure.com/

<!---HONumber=July15_HO1-->