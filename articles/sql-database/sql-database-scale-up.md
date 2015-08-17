<properties 
   pageTitle="데이터베이스 서비스 계층 및 성능 수준 변경" 
   description="응용 프로그램 가동 중지 시간 없이 비즈니스 및 비용 요구 사항에 따라 성능을 높이거나 낮추는 데 사용할 수 있는 Azure SQL 데이터베이스 서비스 계층을 사용하여 클라우드 데이터베이스의 규모를 동적으로 확장 및 축소하는 방법을 알아봅니다." 
   services="sql-database" 
   documentationCenter="" 
   authors="stevestein" 
   manager="jeffreyg" 
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services" 
   ms.date="04/02/2015"
   ms.author="sstein"/>


# 데이터베이스 서비스 계층 및 성능 수준 변경 

이 항목에서는 서비스 계층 및 성능 수준 간에 Azure SQL 데이터베이스를 이동하는 단계를 설명합니다.

## 서비스 계층 변경 

[SQL 데이터베이스 Web/Business 데이터베이스를 새 서비스 계층으로 업그레이드](sql-database-upgrade-new-service-tiers.md) 및 [Azure SQL 데이터베이스 서비스 계층 및 성능 수준](https://msdn.microsoft.com/library/azure/dn741336.aspx)의 정보를 사용하여 Azure SQL 데이터베이스에 대해 적절한 서비스 계층 및 성능 수준을 결정합니다.

Azure 관리 포털, [PowerShell](https://msdn.microsoft.com/library/azure/dn546726.aspx) 또는 [REST API](https://msdn.microsoft.com/library/dn505719.aspx)를 사용하여 서비스 계층 간에 쉽게 이동할 수 있습니다.

서비스 계층 간에 이동할 때는 다음 사항을 고려해 보세요.
- 서비스 계층 또는 성능 수준 간에 업그레이드하기 전에 서버에 사용 가능한 할당량이 있는지 확인합니다. 할당량이 더 필요하면 고객 지원에 문의합니다.
- 페더레이션된 데이터베이스는 기본, 표준 또는 프리미엄 서비스 계층으로 업그레이드할 수 없습니다.

- 데이터베이스를 다운그레이드하려면 데이터베이스가 대상 서비스 계층의 최대 허용 크기보다 작아야 합니다. 각 서비스 계층에서 허용되는 크기에 대한 자세한 내용은 이 섹션의 뒷부분에 나오는 서비스 계층 및 데이터베이스 크기 테이블을 참조하세요.

- [표준 지역에서 복제](https://msdn.microsoft.com/library/azure/dn758204.aspx) 또는 [활성 지역 복제](https://msdn.microsoft.com/library/azure/dn741339.aspx)를 사용할 수 있는 데이터베이스를 업그레이드하는 경우에는 주 데이터베이스를 업그레이드하기 전에 먼저 해당 보조 데이터베이스를 원하는 성능 계층으로 업그레이드해야 합니다.

- 프리미엄 서비스 계층에서 다운그레이드하는 경우는 먼저 모든 지역에서 복제 관계를 종료해야 합니다. [연속 복사 관계 종료](https://msdn.microsoft.com/library/azure/dn741323.aspx) 항목에 설명된 단계에 따라 주 데이터베이스와 활성 보조 데이터베이스 간의 복제 프로세스를 중지할 수 있습니다.

- 복원 서비스는 여러 서비스 계층에서 서로 다르게 제공됩니다. 다운그레이드하는 경우 지정 시간으로 복원하는 기능을 잃게 되거나 백업 보존 기간이 단축될 수 있습니다. 자세한 내용은 [Azure SQL 데이터베이스 백업 및 복원](https://msdn.microsoft.com/library/azure/jj650016.aspx)을 참조하세요.

- 24시간 내에 최대 4개의 개별 데이터베이스 변경(서비스 계층 또는 성능 수준)을 수행할 수 있습니다.

- 데이터베이스의 새로운 속성은 변경이 완료될 때까지 적용되지 않습니다.

다음 사항에 유의하세요.
- Business 및 Web 서비스 계층은 2015년 9월에 사용 중지됩니다. 자세한 내용은 [Web 및 Business 버전 지원 종료 FAQ](https://msdn.microsoft.com/library/azure/dn741330.aspx)를 참조하세요.

<note included>
- 현재 구현된 [페더레이션 기능은 Web 및 Business 서비스 계층과 함께 사용 중지](https://msdn.microsoft.com/library/azure/dn741330.aspx)될 예정임에 유의하세요. [Azure SQL 데이터베이스에 대한 탄력적인 확장](sql-database-elastic-scale-get-started.md)을 사용하여 Azure SQL 데이터베이스에서 분할된 확장 솔루션을 빌드하는 것이 좋습니다. 이를 수행하려면 Azure SQL 데이터베이스 탄력적인 확장 미리 보기 시작을 참조하세요.

## 더 높은 서비스 계층으로 업그레이드
다음 방법 중 하나를 사용하여 데이터베이스를 업그레이드합니다. 이들 단계는 프리미엄 서비스 계층으로의 업그레이드에 해당하지만, 모든 업그레이드에 적용됩니다.

### Azure 관리 포털 사용
1. Microsoft 계정을 사용하여 Azure 관리 포털에 로그인합니다.
2. **SQL 데이터베이스** 탭으로 이동합니다.
3. **데이터베이스** 목록에서 데이터베이스를 선택합니다. **데이터베이스 대시보드** 또는 **빠른 시작** 페이지에서 데이터베이스가 열립니다.
4. 데이터베이스에 대해 **규모** 탭을 선택합니다.
5. **일반** 섹션에서 서비스 계층에 대해 **PREMIUM**을 선택합니다.
6. **성능 수준**에 대해 **P1**, **P2** 또는 **P3**을 선택합니다. 각 성능 수준에 제공되는 리소스는 DTU로 표시됩니다. 성능 수준 및 DTU에 대한 자세한 내용은 Azure SQL 데이터베이스 서비스 계층 및 성능 수준을 참조하세요.
8. 화면 맨 아래에 있는 명령 모음에서 **저장** 단추를 클릭합니다.
9. **확인** 메시지가 나타납니다. 제공된 정보를 읽고 확인란을 선택하여 확인합니다.


### Azure PowerShell 사용
1. Set-AzureSqlDatabase를 사용하여 데이터베이스의 성능 수준, 최대 데이터베이스 크기 및 서비스 계층을 지정합니다. 다양한 서비스 계층에서 지원되는 데이터베이스 크기 목록에 대해서는 Azure SQL 데이터베이스 서비스 계층(Edition)을 참조하세요.
2. New-AzureSqlDatabaseServerContext cmdlet을 사용하여 서버 컨텍스트를 설정합니다. 예제 구문이 Azure PowerShell 명령 사용 섹션에서 제공됩니다.
3. 데이터베이스 및 대상 성능 수준에 대한 핸들을 가져옵니다. Set-AzureSqlDatabase –ServiceObjective를 사용하여 성능 수준 지정

**사용 예**
이 예에서:
- 이 예제에서는 프리미엄 서비스 계층으로 업그레이드하는 방법을 보여 줍니다.
- 데이터베이스 이름 "somedb"를 가리키는 $db 핸들이 만들어집니다.
- 프리미엄 성능 수준 1을 가리키는 $P1 핸들이 만들어집니다.
- 데이터베이스 $db에 대한 성능 수준이 $P1(으)로 설정됩니다.

		Windows PowerShell:

		$db = Get-AzureSqlDatabase $serverContext –DatabaseName "somedb"

		$P1= Get-AzureSqlDatabaseServiceObjective $serverContext -ServiceObjectiveName "P1"

		Set-AzureSqlDatabase $serverContext –Database $db –ServiceObjective $P1 –Edition Premium



## 하위 서비스 계층으로 다운그레이드
데이터베이스를 하위 서비스 계층으로 다운그레이드하려면 다음 방법 중 하나를 사용합니다.

### Azure 관리 포털 사용
1. Microsoft 계정을 사용하여 Azure 관리 포털에 로그인합니다.
2. **SQL 데이터베이스** 탭으로 이동합니다.
3. 원하는 데이터베이스에 대해 **규모** 탭을 선택합니다.
4. **일반** 섹션에서 다운그레이드하려는 서비스 계층을 선택합니다.
5. 화면 맨 아래에 있는 명령 모음에서 **저장** 단추를 클릭합니다.
6. 해당하는 경우, **확인** 페이지에서 제공된 정보를 읽고 확인란을 선택하여 변경 내용을 확인합니다.

### Azure PowerShell 사용
1. Set-AzureSqlDatabase를 사용하여 데이터베이스의 서비스 계층, 성능 수준 및 최대 크기를 지정합니다.
2. Azure PowerShell 명령 사용 섹션에서 제공되는 예제 구문에서 New-AzureSqlDatabaseServerContext를 사용하여 서버 컨텍스트를 설정합니다.
3. 다음을 수행합니다.
 - 데이터베이스에 대한 핸들을 가져옵니다.
 - 성능 수준에 대한 핸들을 가져옵니다.
 - Set-AzureSqlDatabase –ServiceObjective를 사용하여 데이터베이스의 서비스 계층, 성능 수준 및 최대 크기를 지정합니다.

**사용 예**

이 예에서는 프리미엄 서비스 계층 데이터베이스에서 표준 서비스 계층 데이터베이스로 다운그레이드하는 방법을 보여 줍니다.

- 데이터베이스 이름 "somedb"를 가리키는 $db 핸들이 만들어집니다.

- 표준 성능 수준 S2를 가리키는 $S2 변수가 만들어집니다.

- 데이터베이스 $db에 대한 성능 수준이 $S2(으)로 설정됩니다.

- –Edition 및 –MaxSizeGB 매개 변수를 사용하여 데이터베이스 서비스 계층 및 해당 데이터베이스의 최대 크기를 지정합니다. –MaxSizeGB 매개 변수에 지정된 값은 대상 서비스 계층에 대해 유효해야 합니다. 각 서비스 계층에 대한 MaxSize 값이 있는 테이블은 이 항목의 앞부분에 나와 있습니다.

		Windows PowerShell:

		$db = Get-AzureSqlDatabase $serverContext –DatabaseName “somedb”

		$S2 = Get-AzureSqlDatabaseServiceObjective $serverContext -ServiceObjectiveName "S2"

		Set-AzureSqlDatabase $serverContext –Database $db –ServiceObjective $S2 –Edition Standard –MaxSizeGB 40

##성능 수준 변경
다음 방법 중 하나를 사용하여 표준 또는 프리미엄 데이터베이스의 성능 수준을 높이거나 낮출 수 있습니다. 데이터베이스의 성능 수준을 변경하는 데는 시간이 걸릴 수 있습니다. 자세한 내용은 뒤에 나오는 [프리미엄 데이터베이스 변경이 미치는 영향](https://msdn.microsoft.com/library/azure/dn369872.aspx#Impact) 섹션을 참조하세요.

활성 지역 복제 관계가 구성된 프리미엄 데이터베이스의 성능 수준을 변경하는 경우 주 데이터베이스와 활성 보조 데이터베이스에 대해 다음 순서를 사용합니다.

이는 활성 보조 데이터베이스가 주 데이터베이스와 같거나 높은 성능 수준이어야 하기 때문입니다. 
- 높은 성능 수준에서 낮은 성능 수준으로 변경하는 경우 먼저 주 데이터베이스에서 시작한 다음 하나 이상의 활성 보조 데이터베이스에서 수행합니다.

- 낮은 성능 수준에서 더 높은 성능 수준으로 변경하는 경우는 활성 보조 데이터베이스에서 시작하고 마지막으로 주 데이터베이스에서 수행합니다.

###Azure 관리 포털 사용
1. Microsoft 계정을 사용하여 Azure 관리 포털에 로그인합니다.
2. **SQL 데이터베이스** 탭으로 이동합니다.
3. 해당 계정 또는 특정 서버에 대해 **데이터베이스** 목록에서 데이터베이스를 선택합니다. **데이터베이스 대시보드** 또는 **빠른 시작** 페이지에서 데이터베이스가 열립니다.
5. 데이터베이스에 대해 **규모** 탭을 선택합니다.
6. **성능 수준** 옵션에 대해 성능 수준을 선택합니다.
7. 화면 맨 아래의 명령 모음에서 **저장** 단추를 클릭합니다.

###Azure PowerShell 사용
1. Set-AzureSqlDatabase를 사용하여 데이터베이스의 성능 수준을 지정합니다.
2. New-AzureSqlDatabaseServerContext cmdlet을 사용하여 서버 컨텍스트를 설정합니다. 예제 구문이 Azure PowerShell 명령 사용 섹션에서 제공됩니다.
3. 다음을 수행합니다.
- 데이터베이스에 대한 핸들을 가져옵니다.
- 성능 수준에 대한 핸들을 가져옵니다.
- Set-AzureSqlDatabase –ServiceObjective를 사용하여 성능 수준을 지정합니다.

**사용 예**

이 예제에서:

- 데이터베이스 이름 "somedb"를 가리키는 $db 핸들이 만들어집니다.

- 프리미엄 성능 수준 2를 가리키는 $P2 핸들이 만들어집니다.

- 데이터베이스 $db에 대한 성능 수준이 $P2(으)로 설정됩니다.

		Windows PowerShell
		$db = Get-AzureSqlDatabase $serverContext –DatabaseName “somedb”

		$P2 = Get-AzureSqlDatabaseServiceObjective $serverContext -ServiceObjectiveName "P2"

		Set-AzureSqlDatabase $serverContext –Database $db –ServiceObjective $P2

##데이터베이스 변경이 미치는 영향
이 섹션에서는 표준 또는 프리미엄 서비스 계층으로 업그레이드하거나 데이터베이스의 성능 수준을 변경하는 경우 미치는 영향에 대한 정보를 제공합니다.

###데이터베이스 변경 중에 응용 프로그램 연결 처리
성능 수준이 변경되거나 업그레이드/다운그레이드가 완료될 때 데이터베이스에 대한 연결이 일시적으로 끊길 수 있으며, 이런 경우 다시 연결하려면 몇 초 정도 시간이 걸릴 수 있습니다. 데이터 센터의 컴퓨터에 장애가 발생하고 데이터베이스 전반에서 SQL 데이터베이스 서비스에 오류가 발생하는 경우, 언제든지 이런 문제가 발생할 수 있으므로 SQL 데이터베이스 응용 프로그램은 끊어진 연결을 복원할 수 있도록 코딩되어야 합니다. 프리미엄 데이터베이스를 사용하거나 프리미엄 데이터베이스 성능 수준을 변경하기 위해 응용 프로그램에서 필요한 구현 변경은 없습니다.

###데이터베이스 변경 시 대기 시간 이해 및 예상
데이터베이스의 SLO 변경에는 종종 데이터 이동이 필요하므로 몇 시간이 경과된 후에야 변경 요청이 완료되고 연결된 대금 청구 변경 내용이 적용됩니다. 데이터 이동은 데이터베이스를 업그레이드 또는 다운그레이드할 때 변경 내용에 대해 발생하며 데이터베이스의 성능 수준을 변경할 때도 발생할 수 있습니다.

**데이터 이동이 포함된 SLO 변경에 대한 대기 시간**

데이터베이스의 저장소 크기를 확인한 후 다음과 같은 추론에 따라 SLO 변경 요청의 대기 시간을 예측할 수 있습니다.

3 x (5분 + 데이터베이스 크기 / 150MB/분)

예를들어, 데이터베이스 크기가 50GB인 경우 SLO 변경 요청의 대기 시간은 다음과 같은 추론을 통해 예측됩니다.

3 x (5 분 + 50GB x 1024MB/GB / 150MB/분) ≈17시간

이 추론을 사용한 하한 및 상한 예측값은 빈 데이터베이스의 경우 15분, 150GB 데이터베이스의 경우 약 2일 단위로 달라집니다. 또한 예측값은 데이터 센터의 조건에 따라서도 달라질 수 있습니다.

**높은 성능 수준에서 낮은 성능 수준으로 변경하는 경우의 대기 시간**

일반적으로 데이터베이스 성능 수준을 높은 성능 수준에서 더 작은 크기로 변경하면 데이터가 이동되지 않습니다. 이런 경우 SLO 변경의 대기 시간이 훨씬 단축되어 일반적으로 대략 몇 초 내에 완료됩니다.

경고: 위의 내용은 프리미엄 및 표준 서비스 계층 간의 다운그레이드에만 적용됩니다. Web, Business 또는 기본 서비스 계층으로 다운그레이드하면 데이터 이동이 발생합니다.

###데이터베이스의 변경 상태 확인
서비스 계층 간의 업그레이드 또는 다운그레이드 도중 또는 성능 수준을 변경할 때 다음 방법 중 하나를 사용하여 데이터베이스의 상태를 확인할 수 있습니다.

####Azure 관리 포털 사용
1. Microsoft 계정을 사용하여 Azure 관리 포털에 로그인합니다.
2. **데이터베이스** 목록에서 데이터베이스를 선택합니다. **데이터베이스 대시보드** 또는 **빠른 시작** 페이지에서 데이터베이스가 열립니다.
3. **데이터베이스 대시보드**의 **Edition** 섹션에서 상태 정보에 대한 **간략 상태** 영역을 참조하세요.
4. SLO(서비스 수준 목표)는 서비스 계층 내의 성능 수준을 나타냅니다.


##Azure PowerShell 명령 사용
이 섹션에서는 Azure PowerShell 명령을 사용하기 위한 필수 구성 요소를 제공합니다.

**필수 구성 요소**

이 항목에서 설명하는 Azure PowerShell cmdlet을 사용하려면 PowerShell이 실행되는 컴퓨터에 다음 소프트웨어가 설치되어 있어야 합니다.
1. http://www.microsoft.com/ko-kr/download/details.aspx?id=34595에서 3.0 이상의 Windows PowerShell 버전을 다운로드합니다.

2. [Azure SDK 및 도구 다운로드](http://azure.microsoft.com/downloads/)의 명령줄 도구 섹션에서 Azure PowerShell을 다운로드합니다.

다음을 수행하세요.
**시작** 화면 또는 **시작** 메뉴에서 Azure PowerShell로 이동한 후 시작합니다.

서버의 사용자 이름 및 암호를 입력합니다.

**New-AzureSqlDatabaseServerContext**를 사용하여 서버 컨텍스트를 만듭니다.

**예제**

		Windows PowerShell
		$subId = <Subscription ID>
		$thumbprint = <Certificate Thumbprint>
		$myCert = Get-Item Cert:\CurrentUser\My\$thumbprint
		Set-AzureSubscription -SubscriptionName "mySubscription" -SubscriptionId $subId -Certificate $myCert
		Select-AzureSubscription -SubscriptionName "mySubscription"
		$serverContext = New-AzureSqlDatabaseServerContext -ServerName "myserver" -UseSubscription


**Azure PowerShell 참조**
이 항목에서 사용되는 Azure PowerShell cmdlet에 대한 자세한 정보를 보려면 [Azure SQL 데이터베이스 Cmdlet](https://msdn.microsoft.com/library/dn546726.aspx)을 참조하세요.

[New-AzureSqlDatabaseServerContext](http://go.microsoft.com/fwlink/?LinkId=391026)

[New-AzureSqlDatabase](http://go.microsoft.com/fwlink/?LinkId=391027)

[Set-AzureSqlDatabase](http://go.microsoft.com/fwlink/?LinkId=391412)
 

<!---HONumber=August15_HO6-->