<properties
   pageTitle="SQL 데이터 웨어하우스에서 투명한 데이터 암호화(TDE) 시작| Microsoft Azure"
   description="SQL 데이터 웨어하우스에서 투명한 데이터 암호화(TDE) 시작"
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
   ms.date="08/29/2016" 
   ms.author="rortloff;barbkess;sonyama"/>

# SQL 데이터 웨어하우스에서 투명한 데이터 암호화(TDE) 시작

> [AZURE.SELECTOR]
- [보안 개요](sql-data-warehouse-overview-manage-security.md)
- [위협 감지](sql-data-warehouse-security-threat-detection.md)
- [암호화(포털)](sql-data-warehouse-encryption-tde.md)
- [암호화(T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)
- [감사 개요](sql-data-warehouse-auditing-overview.md)
- [하위 수준 클라이언트 감사](sql-data-warehouse-auditing-downlevel-clients.md)


Azure SQL 데이터 웨어하우스 투명한 데이터 암호화(TDE)는 응용 프로그램에 대한 변경 요구 없이 데이터베이스, 연결된 백업 및 저장된 트랜잭션 로그 파일에 대한 실시간 암호화 및 암호 해독을 수행하여 악의적인 활동의 위협으로부터 보호합니다.

TDE는 데이터베이스 암호화 키라는 대칭 키를 사용하여 전체 데이터베이스의 저장소를 암호화합니다. SQL 데이터베이스에서 데이터베이스 암호화 키는 기본 제공 서버 인증서에 의해 보호됩니다. 기본 제공 서버 인증서는 각 SQL 데이터베이스 서버에 대해 고유합니다. Microsoft는 적어도 90일마다 이러한 인증서를 자동으로 회전합니다. SQL 데이터 웨어하우스에서 사용되는 암호화 알고리즘은 AES 256입니다. TDE에 대한 일반적인 설명은 [투명한 데이터 암호화(TDE)]를 참조하세요.

##암호화 설정

SQL 데이터 웨어하우스에 대한 TDE를 사용하려면 다음 단계를 따르세요.

1. [Azure 포털](https://portal.azure.com)에서 데이터베이스 열기
2. 데이터베이스 블레이드에서 **설정** 단추 클릭
3. **투명한 데이터 암호화** 옵션 선택 ![][1]
4. **켜기** 설정 선택 ![][2]
5. **저장** 선택 ![][3]

##암호화 비활성화

SQL 데이터 웨어하우스에 대한 TDE를 비활성화하려면 다음 단계를 따르세요.

1. [Azure 포털](https://portal.azure.com)에서 데이터베이스 열기
2. 데이터베이스 블레이드에서 **설정** 단추 클릭
3. **투명한 데이터 암호화** 옵션 선택 ![][1]
4. **끄기** 설정 선택 ![][4]
5. **저장** 선택 ![][5]

##암호화 DMV

다음 DMV로 암호화를 확인할 수 있습니다.

- [sys.databases]
- [sys.dm\_pdw\_nodes\_database\_encryption\_keys]

<!--MSDN references-->
[투명한 데이터 암호화(TDE)]: https://msdn.microsoft.com/library/bb934049.aspx
[sys.databases]: http://msdn.microsoft.com/library/ms178534.aspx
[sys.dm\_pdw\_nodes\_database\_encryption\_keys]: https://msdn.microsoft.com/library/mt203922.aspx

<!--Image references-->
[1]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings.png
[2]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-on.png
[3]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save.png
[4]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-off.png
[5]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save2.png

<!--Link references-->

<!---HONumber=AcomDC_0907_2016-->