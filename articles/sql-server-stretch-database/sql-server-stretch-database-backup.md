<properties
	pageTitle="스트레치 지원 데이터베이스 백업 | Microsoft Azure"
	description="스트레치 지원 데이터베이스를 백업하는 방법을 알아봅니다."
	services="sql-server-stretch-database"
	documentationCenter=""
	authors="douglaslMS"
	manager=""
	editor=""/>

<tags
	ms.service="sql-server-stretch-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/14/2016"
	ms.author="douglasl"/>

# 스트레치 지원 데이터베이스 백업

데이터베이스 백업은 다양한 유형의 실패, 오류 및 재해로부터 복구하는 데 도움이 됩니다.

-   스트레치 지원 SQL Server 데이터베이스를 백업해야 합니다.  

-   Microsoft Azure는 스트레치 데이터베이스가 SQL Server에서 Azure로 마이그레이션한 원격 데이터를 자동으로 백업합니다.

>    [AZURE.NOTE] 백업은 전체 고가용성 및 비즈니스 연속성 솔루션의 한 부분입니다. 고가용성에 대한 자세한 내용은 [고가용성 솔루션](https://msdn.microsoft.com/library/ms190202.aspx)을 참조하세요.

## SQL Server 데이터 백업  

스트레치 지원 SQL Server 데이터베이스를 백업하려면 현재 사용 중인 SQL Server 백업 방법을 계속 사용할 수 있습니다. 자세한 내용은 [SQL Server 데이터베이스 백업 및 복원](https://msdn.microsoft.com/library/ms187048.aspx)을 참조하세요.

스트레치 지원 SQL Server 데이터베이스의 백업에는 로컬 데이터와 백업이 실행된 시점에 마이그레이션에 적합한 데이터만 포함됩니다. 적격 데이터는 아직 마이그레이션하지 않았지만 테이블의 마이그레이션 설정에 따라 Azure로 곧 마이그레이션될 데이터입니다. 이것을 **단순** 백업이라고 하며 Azure로 이미 마이그레이션한 데이터는 포함되지 않습니다.

## 원격 Azure 데이터 백업   

Microsoft Azure는 스트레치 데이터베이스가 SQL Server에서 Azure로 마이그레이션한 원격 데이터를 자동으로 백업합니다.

### Azure는 자동 백업으로 데이터 손실의 위험을 줄여줍니다.  
Azure의 SQL Server 스트레치 데이터베이스 서비스는 적어도 8시간마다 자동 저장소 스냅숏을 사용하여 원격 데이터베이스를 보호합니다. 가능한 넓은 복원 지점의 범위를 제공하기 위해 각 스냅숏은 7일 동안 유지됩니다.

### Azure는 지리적 중복으로 데이터 손실의 위험을 줄여줍니다.  
Azure 데이터베이스 백업은 지리적 중복 Azure 저장소(RA-GRS)에 저장되므로 기본적으로 지리적으로 중복됩니다. 지역 중복 저장소는 기본 지역에서 수백 마일 떨어져 있는 보조 영역에 데이터를 복제합니다. 기본 및 보조 지역에서 데이터는 별도 오류 도메인 및 업그레이드 도메인에 걸쳐 각각 세 번씩 복제됩니다. 이렇게 하면 Azure 영역 중 하나를 사용할 수 없게 하는 완전한 지역 가동 중단 또는 재해 경우더라도 데이터를 지속적으로 사용할 수 있습니다.

### <a name="stretchRPO"></a>스트레치 데이터베이스는 마이그레이션된 행을 일시적으로 유지하여 Azure 데이터의 데이터 손실 위험을 줄여줍니다.
스트레치 데이터베이스는 SQL Server의 적격 행을 Azure로 마이그레이션한 후 해당 행을 8시간 이상 스테이징 테이블에 유지합니다. Azure 데이터베이스의 백업을 복원하면 스트레치 데이터베이스는 스테이징 테이블에 저장된 행을 사용하여 SQL Server 및 Azure 데이터베이스를 조정합니다.

Azure 데이터의 백업을 복원한 후에는 저장 프로시저 [sys.sp\_rda\_reauthorize\_db](https://msdn.microsoft.com/library/mt131016.aspx)를 실행하여 스트레치 지원 SQL Server 데이터베이스를 원격 Azure 데이터베이스에 다시 연결해야 합니다. **sys.sp\_rda\_reauthorize\_db**를 실행하면 스트레치 데이터베이스는 SQL Server와 Azure 데이터베이스를 자동으로 조정합니다.

스트레치 데이터베이스가 스테이징 테이블에 일시적으로 유지하는 마이그레이션된 데이터의 시간을 늘리려면 저장 프로시저 [sys.sp\_rda\_set\_rpo\_duration](https://msdn.microsoft.com/library/mt707766.aspx)을 실행하고 시간 수를 8시간보다 크게 지정합니다. 유지할 데이터 양을 결정하려면 다음 요소를 고려하세요.
-   자동 Azure 백업의 빈도(8시간 간격 이상)
-   문제가 발생한 후 문제를 인식하고 백업을 복원하기로 결정하는 데 필요한 시간
-   Azure 복원 작업의 기간

> [AZURE.NOTE] 스트레치 데이터베이스가 스테이징 테이블에 일시적으로 유지하는 데이터의 양을 늘리면 SQL Server에 필요한 공간의 크기도 커집니다.

스트레치 데이터베이스가 현재 스테이징 테이블에 일시적으로 유지하는 데이터의 시간을 확인하려면 저장 프로시저 [sys.sp\_rda\_get\_rpo\_duration](https://msdn.microsoft.com/library/mt707767.aspx)을 실행합니다.

## 참고 항목

[스트레치 데이터베이스 관리 및 문제 해결](sql-server-stretch-database-manage.md)

[sys.sp\_rda\_reauthorize\_db(Transact-SQL)](https://msdn.microsoft.com/library/mt131016.aspx)

[SQL Server 데이터베이스 백업 및 복원](https://msdn.microsoft.com/library/ms187048.aspx)

<!---HONumber=AcomDC_0615_2016-->