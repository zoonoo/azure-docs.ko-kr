<properties
   pageTitle="SQL 데이터 웨어하우스 투명한 데이터 암호화(TDE) TSQL 시작 | Microsoft Azure"
   description="SQL 데이터 웨어하우스 투명한 데이터 암호화(TDE) TSQL 시작"
   services="sql-data-warehouse"
   documentationCenter=""
   authors="ronortloff"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.workload="data-management"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="03/23/2016"
   ms.author="rortloff;barbkess;sonyama"/>

# 투명한 데이터 암호화(TDE) 시작
> [AZURE.SELECTOR]
- [Azure 포털](sql-data-warehouse-encryption-tde.md)
- [TSQL](sql-data-warehouse-encryption-tde-tsql.md)

Azure SQL 데이터 웨어하우스 투명한 데이터 암호화(TDE)는 응용 프로그램에 대한 변경 요구 없이 데이터베이스, 연결된 백업 및 저장된 트랜잭션 로그 파일에 대한 실시간 암호화 및 암호 해독을 수행하여 악의적인 활동의 위협으로부터 보호합니다.

TDE는 데이터베이스 암호화 키라는 대칭 키를 사용하여 전체 데이터베이스의 저장소를 암호화합니다. SQL 데이터베이스에서 데이터베이스 암호화 키는 기본 제공 서버 인증서에 의해 보호됩니다. 기본 제공 서버 인증서는 각 SQL 데이터베이스 서버에 대해 고유합니다. Microsoft는 적어도 90일마다 이러한 인증서를 자동으로 회전합니다. TDE에 대한 일반적인 설명은 [투명한 데이터 암호화(TDE)]를 참조하세요.

##암호화 설정

SQL 데이터 웨어하우스에 대한 TDE를 사용하려면 다음 단계를 따르세요.

1. 마스터 데이터베이스에서**dbmanager** 역할의 관리자 또는 멤버인 로그인을 사용하여 데이터베이스를 호스팅하는 서버의 *마스터* 데이터베이스에 연결
2. 다음 문을 실행하여 데이터베이스를 암호화합니다.

```sql
ALTER DATABASE [AdventureWorks] SET ENCRYPTION ON;
```

##암호화 비활성화

SQL 데이터 웨어하우스에 대한 TDE를 비활성화하려면 다음 단계를 따르세요.

1. 마스터 데이터베이스에서**dbmanager** 역할의 관리자 또는 멤버인 로그인을 사용하여 *마스터* 데이터베이스에 연결
2. 다음 문을 실행하여 데이터베이스를 암호화합니다.

```sql
ALTER DATABASE [AdventureWorks] SET ENCRYPTION OFF;
```

##암호화 확인

SQL 데이터 웨어하우스에 대한 암호화 상태를 확인하려면 다음 단계를 따르세요.

1. 마스터 데이터베이스에서**dbmanager** 역할의 관리자 또는 멤버인 로그인을 사용하여 *마스터* 또는 인스턴스 데이터베이스에 연결
2. 다음 문을 실행하여 데이터베이스를 암호화합니다.

```sql
SELECT
	[name],
	[is_encrypted]
FROM
	sys.databases;
```

```1```의 결과는 암호화된 데이터베이스를 나타내고 ```0```은(는) 암호화되지 않은 데이터베이스를 나타냅니다.


<!--Anchors-->
[투명한 데이터 암호화(TDE)]: https://msdn.microsoft.com/library/bb934049.aspx


<!--Image references-->

<!--Link references-->

<!---HONumber=AcomDC_0511_2016-->