<properties 
   pageTitle="SQL 데이터베이스 보안 개요" 
   description="인증, 권한 부여, 연결 보안, 암호화 및 규정 준수와 관련하여 클라우드 및 SQL Server 온-프레미스 간의 차이점을 포함하여 Azure SQL 데이터베이스 및 SQL Server 보안에 대해 알아봅니다." 
   services="sql-database" 
   documentationCenter="" 
   authors="tmullaney" 
   manager="jeffreyg" 
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services" 
   ms.date="07/14/2015"
   ms.author="thmullan;jackr"/>


# SQL 데이터베이스 보안 설정

## 개요

이 문서는 Azure SQL 데이터베이스를 사용하여 응용 프로그램의 데이터 계층에 보안을 설정하기 위한 기본 사항을 안내합니다. 특히, 이 문서는 [SQL 데이터베이스 시작 자습서](sql-database-get-started.md)에서 만든 데이터베이스에 대한 액세스 제한, 데이터 보호 및 작업 모니터링을 위한 리소스로 시작할 수 있습니다. SQL의 모든 버전에서 사용할 수 있는 보안 기능의 전체 개요에 대해서는 [SQL Server 데이터베이스 엔진 및 Azure SQL 데이터베이스를 위한 보안 센터](https://msdn.microsoft.com/library/bb510589)를 참조하세요.

## 연결 보안

연결 보안은 방화벽 규칙 및 연결 암호화를 사용하여 데이터베이스에 대 한 연결을 제한하고 보호하는 방법을 가리킵니다.

방화벽 규칙은 서버와 데이터베이스에서 명시적으로 허용 목록에 없는 IP 주소로부터의 연결 시도를 거부하는 데 사용됩니다. 응용 프로그램 또는 클라이언트 컴퓨터의 공용 IP 주소에서 새 데이터베이스에 대해 연결을 시도할 수 있도록 허용하려면 먼저 Azure 관리 포털, REST API 또는 PowerShell을 사용하여 서버 수준 방화벽 규칙을 만들어야 합니다. 서버 방화벽을 통해 허용되는 IP 주소 범위를 최대한 많이 제한하는 것이 좋습니다. 자세한 내용은 [Azure SQL 데이터베이스 방화벽](https://msdn.microsoft.com/library/ee621782)을 참조하세요.

Azure SQL 데이터베이스에 대한 모든 연결은 데이터베이스로/로부터 데이터 “전송 중"에 항상 암호화(SSL/TLS)가 필요합니다. 응용 프로그램의 연결 문자열에서 연결을 암호화하고 서버 인증서를 신뢰하지 *않도록* 매개 변수를 지정해야 합니다(Azure 관리 포털에서 연결 문자열을 복사하는 경우 이 작업이 자동으로 수행됨). 그렇지 않으면 연결에서 서버의 ID를 확인할 수 없으며 "메시지 가로채기(man-in-the-middle)" 공격에 취약할 수 있습니다. 예를 들어, ADO.NET 드라이버의 경우 이러한 연결 문자열 매개 변수는 **Encrypt=True** 및 **TrustServerCertificate=False**입니다. 자세한 내용은 [Azure SQL 데이터베이스 연결 암호화 및 인증서 유효성 검사](https://msdn.microsoft.com/library/azure/ff394108#encryption)를 참조하세요.


## 인증

인증은 데이터베이스에 연결할 때 사용자의 ID를 증명하는 방법을 가리킵니다. SQL 데이터베이스는 현재 사용자 이름 및 암호를 사용한 SQL 인증을 지원합니다.

데이터베이스의 논리 서버를 만들 때 사용자 이름 및 암호를 사용하여 "서버 관리자" 로그인을 지정했습니다. 이러한 자격 증명을 사용하면 해당 서버의 모든 데이터베이스에 데이터베이스 소유자 또는 "dbo"로 인증할 수 있습니다.

그러나 가장 좋은 방법은 응용 프로그램이 다른 계정을 사용하여 인증하는 것입니다. 이 방법을 사용하면 응용 프로그램에 부여되는 사용 권한을 제한하여 응용 프로그램 코드가 SQL 삽입 공격에 취약한 경우 악의적인 활동의 위험을 줄일 수 있습니다. 응용 프로그램을 통해 사용자 이름과 암호로 단일 데이터베이스에 직접 인증할 수 있는 [포함된 데이터베이스 사용자](https://msdn.microsoft.com/library/ff929188)를 만드는 것이 좋습니다. 서버 관리자 로그인으로 사용자 데이터베이스에 연결되어 있는 동안 다음 T-SQL을 실행하여 포함된 데이터베이스 사용자를 만들 수 있습니다.

```
CREATE USER ApplicationUser WITH PASSWORD = 'strong_password';
```

데이터베이스에 연결하려면 응용 프로그램의 연결 문자열에 서버 관리자 로그인 대신 사용자 이름과 암호를 지정해야 합니다.

SQL 데이터베이스 인증에 대한 자세한 내용은 [Azure SQL 데이터베이스에서 데이터베이스 및 로그인 관리](https://msdn.microsoft.com/library/ee336235)를 참조하세요.


## 권한 부여
권한 부여는 Azure SQL 데이터베이스 내에서 수행할 수 있는 작업을 가리키며 사용자 계정의 역할 멤버 자격과 사용 권한에 의해 제어됩니다. 사용자에게 필요한 최소한의 권한을 부여하는 것이 가장 좋습니다. Azure SQL 데이터베이스를 사용하면 T-SQL에서 역할을 통해 쉽게 관리할 수 있습니다.

```
ALTER ROLE db_datareader ADD MEMBER ApplicationUser; -- allows ApplicationUser to read data
ALTER ROLE db_datawriter ADD MEMBER ApplicationUser; -- allows ApplicationUser to write data
```

연결 중인 서버 관리자 계정은 데이터베이스 내에서 작업을 수행할 권한이 있는 db_owner의 구성원입니다. 스키마 업그레이드 및 기타 관리 작업을 배포하기 위해서는 이 계정을 저장합니다. 응용 프로그램에서 해당 응용 프로그램에 필요한 최소한의 권한이 있는 데이터베이스에 연결하려면 보다 제한된 사용 권한을 가진 "ApplicationUser" 계정을 사용합니다.

사용자가 Azure SQL 데이터베이스로 수행할 수 있는 작업을 더욱 제한할 수 있는 방법이 있습니다.

* db_datareader 및 db_datawriter 이외의 [데이터베이스 역할](https://msdn.microsoft.com/library/ms189121)은 더 강력한 응용 프로그램 사용자 계정이나 덜 강력한 관리 계정을 만드는 데 사용할 수 있습니다.
* 세분화된 [권한](https://msdn.microsoft.com/library/ms191291)을 사용하면 개별 열, 테이블, 뷰, 프로시저 및 데이터베이스의 다른 개체에서 수행할 수 있는 작업을 제어할 수 있습니다.
* [가장](https://msdn.microsoft.com/library/vstudio/bb669087) 및 [모듈 서명](https://msdn.microsoft.com/library/bb669102)은 일시적으로 권한을 안전하게 상승시키는 데 사용할 수 있습니다.
* [행 수준 보안](https://msdn.microsoft.com/library/dn765131)을 사용하면 사용자가 볼 수 있는 행을 필터링할 수 있습니다.
* [데이터 마스킹](sql-database-dynamic-data-masking-get-started.md)은 중요한 데이터의 노출을 제한하는 데 사용할 수 있습니다.
* [저장 프로시저](https://msdn.microsoft.com/library/ms190782)는 데이터베이스에서 수행할 수 있는 작업을 제한하는 데 사용할 수 있습니다.

Azure 관리 포털에서 또는 Azure 리소스 관리자 API를 사용하여 데이터베이스 및 논리 서버를 관리하는 것은 포털 사용자 계정의 역할 할당에 의해 제어됩니다. 이 항목에 대한 자세한 내용은 [Azure 미리 보기 포털의 역할 기반 액세스 제어](../role-based-access-control-configure.md)를 참조하세요.


## 암호화

Azure SQL 데이터베이스는 데이터가 "휴지 상태"일 때 또는 데이터베이스 파일 및 백업에 저장된 경우 [투명한 데이터 암호화](http://go.microsoft.com/fwlink/?LinkId=526242)를 사용하여 데이터를 암호화함으로써 데이터를 보호할 수 있도록 도와줍니다. 데이터베이스를 암호화하려면 데이터베이스 소유자로 연결하고 다음을 실행합니다.

```
CREATE DATABASE ENCRYPTION KEY 
   WITH ALGORITHM = AES_256 
   ENCRYPTION BY SERVER CERTIFICATE ##MS_TdeCertificate##;
   
ALTER DATABASE [AdventureWorks] SET ENCRYPTION ON;
```

사용자 데이터 암호를 암호화하는 다른 방법으로 다음을 고려해 보세요.

* [셀 수준 암호화](https://msdn.microsoft.com/library/ms179331.aspx)는 특정 열 또는 서로 다른 암호화 키를 가진 데이터의 셀도 암호화합니다.
* 하드웨어 보안 모듈 또는 암호화 키 계층의 중앙 관리가 필요한 경우 [Azure VM에서 SQL Server와 함께 Azure 키 자격 증명 모음](http://blogs.technet.com/b/kv/archive/2015/01/12/using-the-key-vault-for-sql-server-encryption.aspx)을 사용하는 것을 고려해 보세요.


## 감사

데이터베이스 이벤트의 감사 및 추적은 규정을 준수하고 의심스러운 활동을 식별할 수 있도록 도와줍니다. SQL 데이터베이스 감사를 사용하면 Azure 저장소 계정의 감사 로그에 데이터베이스의 이벤트를 기록할 수 있습니다. 또한 드릴다운 보고서 및 분석을 용이하게 하려면 SQL 데이터베이스 감사 기능을 Microsoft Power BI와 통합합니다. 자세한 내용은 [SQL 데이터베이스 감사 시작](sql-database-auditing-get-started.md)을 참조하세요.

## 규정 준수

위의 기능 및 응용 프로그램이 다양한 보안 규정 준수 요구 사항을 충족하도록 도울 수 있는 기능 외에도 Azure SQL 데이터베이스는 정기적인 감사에 참여하고 여러 규정 준수 표준에 대해 인증받았습니다. 자세한 내용은 [Microsoft Azure 보안 센터](http://azure.microsoft.com/support/trust-center/)를 참조하세요. 여기서 최신 [SQL 데이터베이스 규정 준수 인증서](http://azure.microsoft.com/support/trust-center/services/) 목록을 찾을 수 있습니다.
 

<!---HONumber=July15_HO4-->