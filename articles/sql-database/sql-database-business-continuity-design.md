<properties 
   pageTitle="비즈니스 연속성을 위한 SQL 데이터베이스 설계" 
   description="이 섹션에서 설명하는 선택 지침은 어떤 BCDR 기능을 사용해야 하며 그 시기는 언제인지 선택하기 위한 것입니다. 여기에는 SQL DB를 사용할 때 자동으로 얻게 되는 기능도 포함됩니다."
   services="sql-database" 
   documentationCenter="" 
   authors="elfisher" 
   manager="jeffreyg" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management" 
   ms.date="11/16/2015"
   ms.author="elfish"/>

#비즈니스 연속성을 위한 설계

비즈니스 연속성을 위해 응용 프로그램을 설계하려면 다음 질문에 대답해야 합니다.

1. 내 응용 프로그램을 가동 중단에서 보호하기 위해 적절한 비즈니스 연속성 기능은 무엇인가?
2. 어떤 수준의 중복성 및 복제 토폴로지를 사용해야 하는가?

##지역 복원을 사용할 시기

SQL 데이터베이스는 기본적으로 모든 데이터베이스를 보호할 수 있는 기본 제공 보호 기능을 제공합니다. 이러한 보호는 데이터베이스 백업을 지역 중복 Azure 저장소(GRS)에 저장하는 것으로 수행됩니다. 이 방법을 선택할 경우 특별한 구성 또는 추거 리소스 할당이 필요하지 않습니다. 이러한 백업이 있으면 지역 복원 명령을 사용하여 어느 지역에서든 데이터베이스를 복구할 수 있습니다. 응용 프로그램 복구를 위해 지역 복원을 사용하는 방법은 [가동 중단에서 복구](sql-database-disaster-recovery.md) 섹션을 참조하세요.

응용 프로그램이 다음 조건에 맞는 경우 기본 제공 보호 기능을 사용하면 됩니다.

1. 중요 업무용으로 간주되지 않습니다. 연계된 SLA가 없으므로 24시간 이상의 가동 중지가 발생해도 재무적 책임이 없습니다.
2. 데이터 변경 비율(예: 시간당 트랜잭션 수)이 낮습니다. 1시간의 RPO로 방대한 데이터가 손실되지 않습니다.
3. 응용 프로그램이 비용에 민감하여 지역에서 복제를 위한 추가적 비용을 지불할 수 없습니다. 

> [AZURE.NOTE]지역 복원은 작동 중단 시간 동안 백업에서 활성 데이터베이스를 복원하는 데 필요한 특정 지역의 계산 용량을 미리 할당하지 않습니다. 서비스는 지역 복원 요청에 관련된 작업을 지역 내의 기존 데이터베이스에 미치는 영향을 최소화하고 용량 요구 사항을 우선하는 방식으로 관리합니다. 따라서 데이터베이스의 복구 시간은 동시에 같은 지역에서 다른 데이터베이스가 얼마나 복구되는지에 따라 달라집니다.

##지역에서 복제를 사용할 시기

지역에서 복제는 기본 지역과 다른 지역에 복제 데이터베이스(보조)를 만듭니다. 이를 통해 데이터베이스가 복구 후 응용 프로그램의 작업을 지원하는 데 필요한 데이터와 계산 리소스를 보유할 수 있게 됩니다. 장애 조치(failover)를 사용하여 응용 프로그램을 복구하는 방법은 [가동 중단에서 복구](sql-database-disaster-recovery.md) 섹션을 참조하세요.

응용 프로그램이 다음 조건에 맞는 경우 지역에서 복제 기능을 사용하면 됩니다.

1. 중요 업무용입니다. 연계된 SLA가 있고 RPO와 RTO에 민감합니다. 데이터 및 가용성이 손실될 경우 재무적 책임이 발생할 수 있습니다. 
2. 데이터 변경 비율(예: 분 또는 초당당 트랜잭션 수)이 높습니다. 기본 보호 기능을 적용한 상태에서 1시간의 RPO가 허용할 수 없는 수준의 데이터 손실로 이어질 수 있습니다.
3. 지역에서 복제를 사용하는 것에 관련된 비용이 잠재적인 재무적 책임과 연계된 비즈니스 손실보다 크게 낮습니다.

> [AZURE.NOTE]응용 프로그램에서 기본 계층 데이터베이스를 사용하는 경우 지역에서 복제는 지원되지 않습니다.

##표준 vs를 선택 하는 경우. 활성 지역 복제

표준 계층 데이터베이스는 활성 지역 복제를 사용하는 옵션이 없으므로 응용 프로그램이 표준 데이터베이스를 사용하고 위 조건에 맞는 경우 표준 지역 복제를 사용하도로 설정해야 합니다. 반면 프리미엄 데이터베이스는 두 옵션 모두 선택할 수 있습니다. 표준 지역에서 복제는 간편하고 비용이 적은 재해 복구 솔루션으로 설계되었습니다. 특히 작동 중단과 같은 계획되지 않은 이벤트로부터 보호할 때만 사용하는 응용 프로그램에 적절합니다. 표준 지역에서 복제를 사용하면 복구를 위해 DR 쌍을 이루는 지역만 사용할 수 있으며 각각의 주 데이터베이스에 대해 보조 데이터베이스를 하나만 만들 수 없습니다. 응용 프로그램 업그레이드 시나리오에서 보조 데이터베이스가 추가로 필요할 수 있습니다. 따라서 이러한 시나리오가 응용 프로그램에 중요한 경우 활성 지역 복제를 사용하도록 설정해야 합니다. 추가 정보는 [가동 중지 시간 없이 응용 프로그램 업그레이드](sql-database-business-continuity-application-upgrade.md)를 참조하세요.

> [AZURE.NOTE]활성 지역 복제는 보조 데이터베이스에 대한 읽기 전용 액세스도 지원하므로 읽기 전용 작업에 대한 추가 용량도 확보됩니다.

##지역에서 복제를 사용하도록 설정하는 방법

Azure 클래식 포털을 사용하거나 REST API 또는 PowerShell 명령을 호출하여 지역에서 복제를 사용하도록 설정할 수 있습니다.

###Azure 클래식 포털

[AZURE.VIDEO sql-database-enable-geo-replication-in-azure-portal]

1. [Azure 클래식 포털](https://portal.Azure.com)에 로그인합니다.
2. 화면 왼쪽에서 **찾아보기**를 선택한 다음 **SQL 데이터베이스**를 선택합니다.
3. 데이터베이스 블레이드로 이동한 다음 **지역에서 복제 지도**를 선택하고 **지역에서 복제 구성**을 클릭합니다.
4. 지역에서 복제 블레이드로 이동 합니다. 대상 지역을 선택합니다. 
5. 보조 만들기 블레이드로 이동합니다. 대상 지역에서 기존 서버를 선택하거나 새로 만듭니다.
6. 보조 유형(*Readable* 또는 *Non-readable*)을 선택합니다.
7. **만들기**를 클릭하여 구성을 완료합니다.

> [AZURE.NOTE]지역에서 복제 블레이드에서 DR 쌍을 이루는 영역이 *권장*으로 표시됩니다. 프리미엄 계층 데이터베이스를 사용하는 경우 다른 영역을 선택할 수 있습니다. 표준 데이터베이스를 사용하는 경우 변경할 수 없습니다. 프리미엄 데이터베이스의 경우 보조 유형( *Readable* 또는 *Non-readable*)을 선택할 수 있습니다. 표준 데이터베이스에서는 *Non-readable* 보조만 선택할 수 있습니다.


###PowerShell

[New-AzureRmSqlDatabaseSecondary](https://msdn.microsoft.com/library/mt603689.aspx) PowerShell cmdlet을 사용하면 지역에서 복제 구성을 만들 수 있습니다. 이 명령은 동기식이며 주 데이터베이스와 보조 데이터베이스가 동기화 상태일 때 반환됩니다.

프리미엄 또는 표준 데이터베이스를 위한 읽을 수 없는 보조 데이터베이스가 포함된 지역에서 복제를 구성하려면:
		
    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb"
    $secondaryLink = $database | New-AzureRmSqlDatabaseSecondary –PartnerResourceGroupName "rg2" –PartnerServerName "srv2" -AllowConnections "None"

프리미엄 데이터베이스를 위한 읽을 수 있는 보조 데이터베이스가 포함된 지역에서 복제를 만들려면:

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb"
    $secondaryLink = $database | New-AzureRmSqlDatabaseSecondary –PartnerResourceGroupName "rg2" –PartnerServerName "srv2" -AllowConnections "All"
		 

###REST API 

*NonReadableSecondary* 또는 *Secondary* 에 [Create Database](https://msdn.microsoft.com/library/mt163685.aspx) API와 *createMode* set를 사용하여 지역에서 복제 보조 데이터베이스를 프로그래밍 방식으로 만듭니다.

이 API는 비동기적입니다. 반환 후 [Get Replication Link](https://msdn.microsoft.com/library/mt600778.aspx) API를 사용하여 작업의 상태를 확인합니다. 작업이 완료되면 응답 본문의 *replicationState* 필드가 CATCHUP 값을 가집니다.


##장애 조치(failover) 구성을 선택하는 방법 

비즈니스 연속성을 위해 응용 프로그램을 설계할 때는 몇 가지 구성 옵션을 고려해야 합니다. 선택할 옵션은 응용 프로그램 배포 토폴로지와 응용 프로그램의 어느 부분이 작동 중단에 가장 취약한지에 따라 달라집니다. 지침은 [지역에서 복제를 사용하여 재해 복구를 위한 클라우드 솔루션 설계](sql-database-designing-cloud-solutions-for-disaster-recovery.md)를 참조하세요.

<!---HONumber=AcomDC_1203_2015-->
