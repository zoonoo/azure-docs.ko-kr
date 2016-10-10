<properties
   pageTitle="SQL Data Warehouse의 투명한 데이터 암호화(포털) | Microsoft Azure"
   description="SQL Data Warehouse의 TDE(투명한 데이터 암호화)"
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
   ms.date="09/24/2016" 
   ms.author="rortloff;barbkess;sonyama"/>

# SQL 데이터 웨어하우스에서 투명한 데이터 암호화(TDE) 시작

> [AZURE.SELECTOR]
- [보안 개요](sql-data-warehouse-overview-manage-security.md)
- [인증](sql-data-warehouse-authentication.md)
- [암호화(포털)](sql-data-warehouse-encryption-tde.md)
- [암호화(T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)

## 필요한 권한

TDE(투명한 데이터 암호화)를 사용하려면 관리자 또는 dbmanager 역할의 멤버여야 합니다.

## 암호화 설정

SQL 데이터 웨어하우스에 대한 TDE를 사용하려면 다음 단계를 따르세요.

1. [Azure 포털](https://portal.azure.com)에서 데이터베이스 열기
2. 데이터베이스 블레이드에서 **설정** 단추 클릭
3. **투명한 데이터 암호화** 옵션 선택 ![][1]
4. **켜기** 설정 선택 ![][2]
5. **저장** 선택 ![][3]

## 암호화 비활성화

SQL 데이터 웨어하우스에 대한 TDE를 비활성화하려면 다음 단계를 따르세요.

1. [Azure 포털](https://portal.azure.com)에서 데이터베이스 열기
2. 데이터베이스 블레이드에서 **설정** 단추 클릭
3. **투명한 데이터 암호화** 옵션 선택 ![][1]
4. **끄기** 설정 선택 ![][4]
5. **저장** 선택 ![][5]

## 암호화 DMV

다음 DMV로 암호화를 확인할 수 있습니다.

- [sys.databases]
- [sys.dm\_pdw\_nodes\_database\_encryption\_keys]

<!--MSDN references-->
[Transparent Data Encryption (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx
[sys.databases]: http://msdn.microsoft.com/library/ms178534.aspx
[sys.dm\_pdw\_nodes\_database\_encryption\_keys]: https://msdn.microsoft.com/library/mt203922.aspx

<!--Image references-->
[1]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings.png
[2]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-on.png
[3]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save.png
[4]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-off.png
[5]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save2.png

<!--Link references-->

<!---HONumber=AcomDC_0928_2016-->