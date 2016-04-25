<properties
	pageTitle="스트레치 지원 데이터베이스 백업 및 복원 | Microsoft Azure"
	description="스트레치 지원 데이터베이스를 백업 및 복원하는 방법을 알아봅니다."
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
	ms.date="02/26/2016"
	ms.author="douglasl"/>


# 스트레치 지원 데이터베이스 백업 및 복원

현재 사용 중인 방법을 계속 사용하여 스트레치 지원 데이터베이스를 백업 및 복원할 수 있습니다. SQL Server 백업 및 복원에 대한 자세한 내용은 [SQL Server 데이터베이스 백업 및 복원](https://msdn.microsoft.com/library/ms187048.aspx)을 참조하세요.

스트레치 지원 데이터베이스의 백업은 원격 서버로 마이그레이션 데이터를 포함하지 않는 부분 백업입니다.

스트레치 데이터베이스는 특정 시점 복원을 완전히 지원합니다. SQL Server 데이터베이스를 특정 시점으로 복원하고 Azure 연결 권한을 다시 부여한 후에는 스트레치 데이터베이스에서 동일한 특정 시점으로 원격 데이터를 조정합니다. SQL Server의 특정 시점 복원에 대한 자세한 내용은 [SQL Server 데이터베이스를 특정 시점으로 복원(전체 복구 모델)](https://msdn.microsoft.com/library/ms179451.aspx)을 참조하세요. 복원 후 Azure 연결 권한을 다시 부여하기 위해 실행해야 하는 저장 프로시저에 대한 자세한 내용은 [sys.sp\_rda\_reauthorize\_db(Transact-SQL)](https://msdn.microsoft.com/library/mt131016.aspx)를 참조하세요.

## <a name="Reconnect"></a>백업에서 스트레치 지원 데이터베이스 복원

1.  백업에서 데이터베이스를 복원합니다.

2.  저장 프로시저 [sys.sp\_rda\_reauthorize\_db(Transact-SQL)](https://msdn.microsoft.com/library/mt131016.aspx)를 실행하여 로컬 스트레치 지원 데이터베이스를 Azure에 다시 연결합니다.

    -   기존 데이터베이스 범위 자격 증명을 sysname 또는 varchar(128) 값으로 제공합니다. varchar(max)를 사용하지 마세요. **sys.database\_scoped\_credentials** 뷰에서 자격 증명 이름을 조회할 수 있습니다.

	-   원격 데이터의 복사본을 만들고 복사본에 연결할 것인지 지정합니다.

    ```tsql
    Declare @credentialName nvarchar(128);
    SET @credentialName = N'<database_scoped_credential_name_created_previously>';
    EXEC sp_rda_reauthorize_db @credential = @credentialName, @with_copy = 0;
    ```

## <a name="MoreInfo"></a>백업 및 복원에 대한 자세한 정보
스트레치 데이터베이스를 지원하는 데이터베이스의 백업에는 백업이 실행될 당시의 로컬 데이터 및 적격 데이터만 포함됩니다. 또한 이러한 백업에는 데이터베이스의 원격 데이터가 있는 원격 끝점에 대한 정보도 포함됩니다. 이를 "부분 백업"이라고 합니다. 로컬 및 원격 데이터베이스의 모든 데이터를 포함하는 전체 백업은 지원되지 않습니다.

스트레치 데이터베이스를 지원하는 데이터베이스의 백업을 복원하는 경우 로컬 데이터 및 적격 데이터만 데이터베이스에 복원됩니다. 적격 데이터는 아직 이동하지 않았지만 테이블의 스트레치 데이터베이스 구성에 따라 Azure로 곧 이동할 데이터입니다. 복원 작업이 실행된 후 데이터베이스에는 백업이 실행될 당시의 로컬 및 적격 데이터가 포함되지만 원격 끝점에 연결하는 데 필요한 자격 증명 및 아티팩트는 없습니다.

저장 프로시저 **sys.sp\_rda\_reauthorize\_db**를 실행하여 로컬 데이터베이스와 해당 원격 끝점 간의 연결을 다시 설정해야 합니다. db\_owner만 이 작업을 수행할 수 있습니다. 이 저장 프로시저에는 대상 Azure 서버에 대한 관리자 로그인 및 암호도 필요합니다.

연결을 다시 설정한 후에는 스트레치 데이터베이스에서 원격 끝점의 원격 데이터를 복사하고 로컬 데이터베이스와 연결하여 로컬 데이터베이스에 있는 적격 데이터를 원격 데이터로 조정합니다. 이 프로세스는 자동으로 수행되므로 사용자가 개입할 필요가 없습니다. 이 조정이 실행된 후에는 로컬 데이터베이스와 원격 끝점이 일치하는 상태에 있게 됩니다.

## 참고 항목

[스트레치 데이터베이스 관리 및 문제 해결](sql-server-stretch-database-manage.md)

[sys.sp\_rda\_reauthorize\_db(Transact-SQL)](https://msdn.microsoft.com/library/mt131016.aspx)

[SQL Server 데이터베이스 백업 및 복원](https://msdn.microsoft.com/library/ms187048.aspx)

<!---HONumber=AcomDC_0413_2016-->