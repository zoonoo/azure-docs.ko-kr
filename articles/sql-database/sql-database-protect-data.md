---
title: "Azure SQL Database 데이터 보호 개요 | Microsoft Docs"
description: "Azure SQL Database의 데이터 보호에 대해 알아봅니다."
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: secure and protect
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-management
ms.date: 12/21/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: f4712d70c0323e607ddcc021809f8097a621730d
ms.openlocfilehash: 67b32b224d86e8d0ed06d8248c3a2c5f5569c5cb
ms.lasthandoff: 12/23/2016


---
# <a name="protecting-data-within-your-sql-database"></a>SQL Database의 데이터 보호

SQL Database는 암호화를 사용하여 데이터를 보호합니다.  

## <a name="overview"></a>개요

SQL Database는 이동 중인 데이터의 경우 [전송 계층 보안](https://support.microsoft.com/en-us/kb/3135244), 미사용 데이터의 경우 [투명한 데이터 암호화](http://go.microsoft.com/fwlink/?LinkId=526242) 및 사용 중인 데이터의 경우 [상시 암호화](https://msdn.microsoft.com/library/mt163865.aspx)를 제공하여 데이터를 보호합니다. SQL Database에서 이러한 데이터 보호 기능을 사용하는 방법에 대한 설명은 [데이터 보호 및 보안](sql-database-protect-data.md)을 참조하세요.

데이터를 암호화하는 다른 방법으로 다음을 고려해 보세요.

* [셀 수준 암호화](https://msdn.microsoft.com/library/ms179331.aspx) 는 특정 열 또는 서로 다른 암호화 키를 가진 데이터의 셀도 암호화합니다.
* 하드웨어 보안 모듈 또는 암호화 키 계층의 중앙 관리가 필요한 경우 [Azure VM에서 SQL Server와 함께 Azure 키 자격 증명 모음](http://blogs.technet.com/b/kv/archive/2015/01/12/using-the-key-vault-for-sql-server-encryption.aspx)을 사용하는 것을 고려해 보세요.


SQL Database는 감사 및 위협 검색 기능을 제공하여 데이터를 보호합니다. 

### <a name="auditing"></a>감사
SQL Database 감사는 데이터베이스 활동을 추적하고 데이터베이스 이벤트를 Azure Storage 계정의 감사 로그에 기록하여 규정 준수를 유지하는 데 도움을 줍니다. 감사를 통해 진행 중인 데이터베이스 활동을 파악하고 이전 활동을 분석 및 조사하여 잠재적인 위협이나 의심스러운 악용 및 보안 위반을 식별할 수 있습니다. 자세한 내용은 [SQL Database 감사 시작](sql-database-auditing-get-started.md)을 참조하세요.  

### <a name="threat-detection"></a>위협 감지
위협 검색은 Azure SQL Database 서비스에 구축되는 추가적 보안 인텔리전스 계층을 제공하여 감사 기능을 보완합니다. 이 기능은 비정상적인 데이터베이스 활동을 파악하고, 수집하고, 검색하기 위해 24시간 내내 작동합니다. 의심스러운 활동, 잠재적 취약성, SQL 삽입 공격 및 비정상적인 데이터베이스 액세스 패턴에 대해 경고합니다. 제공되는 유익하고 실행 가능한 지침에 따라 경고에 대응할 수 있습니다. 자세한 내용은 [SQL 데이터베이스 위협 감지 시작](sql-database-threat-detection-get-started.md)을 참조하세요.  

## <a name="connection-security"></a>연결 보안
연결 보안은 방화벽 규칙 및 연결 암호화를 사용하여 데이터베이스에 대한 연결을 제한하고 보호하는 방법을 가리킵니다.

방화벽 규칙은 서버와 데이터베이스에서 명시적으로 허용 목록에 없는 IP 주소로부터의 연결 시도를 거부하는 데 사용됩니다. 응용 프로그램 또는 클라이언트 컴퓨터의 공용 IP 주소에서 새 데이터베이스에 대해 연결을 시도할 수 있도록 허용하려면 먼저 Azure 클래식 포털, REST API 또는 PowerShell을 사용하여 서버 수준 방화벽 규칙을 만들어야 합니다. 서버 방화벽을 통해 허용되는 IP 주소 범위를 최대한 많이 제한하는 것이 좋습니다. 자세한 내용은 [Azure SQL 데이터베이스 방화벽](https://msdn.microsoft.com/library/ee621782)을 참조하세요.

Azure SQL 데이터베이스에 대한 모든 연결은 데이터베이스로/로부터 데이터 “전송 중"에 항상 암호화(SSL/TLS)가 필요합니다. 응용 프로그램의 연결 문자열에서 연결을 암호화하고 서버 인증서를 신뢰하지 *않도록* 매개 변수를 지정해야 합니다. 이 작업은 Azure 클래식 포털에서 연결 문자열을 복사만 하면 완료됩니다. 그렇지 않으면 연결에서 서버 ID를 확인할 수 없으며 "메시지 가로채기(man-in-the-middle)" 공격에 취약할 수 있습니다. 예를 들어, ADO.NET 드라이버의 경우 이러한 연결 문자열 매개 변수는 **Encrypt=True** 및 **TrustServerCertificate=False**입니다. 자세한 내용은 [Azure SQL Database 연결 암호화 및 인증서 유효성 검사](https://msdn.microsoft.com/library/azure/ff394108#encryption)를 참조하세요.

## <a name="authentication"></a>인증
인증은 데이터베이스에 연결할 때 사용자의 ID를 증명하는 방법을 가리킵니다. SQL 데이터베이스는 두 가지 인증 유형을 지원합니다.

* 사용자 이름 및 암호를 사용하는 **SQL 인증**
* Azure Active Directory에서 관리되는 ID를 사용하고 관리되고 통합된 도메인에서 지원되는 **Azure Active Directory 인증**

데이터베이스의 논리 서버를 만들 때 사용자 이름 및 암호를 사용하여 "서버 관리자" 로그인을 지정했습니다. 이러한 자격 증명을 사용하면 해당 서버의 모든 데이터베이스에 데이터베이스 소유자 또는 "dbo"로 인증할 수 있습니다. Azure Active Directory 인증을 사용하려는 경우 Azure AD 사용자 및 그룹을 허용하는 "Azure AD 관리자"라는 다른 서버 관리자를 만들어야 합니다. 이 관리자는 일반 서버 관리자가 할 수 있는 모든 작업을 수행할 수도 있습니다. Azure AD 관리자를 만들어 Azure Active Directory 인증을 활성화하는 방법에 대한 연습은 [Azure Active Directory 인증을 사용하여 SQL 데이터베이스에 연결](sql-database-aad-authentication.md) 을 참조하세요.

가장 좋은 방법은 응용 프로그램이 다른 계정을 사용하여 인증하는 것입니다. 이 방법을 사용하면 응용 프로그램에 부여되는 사용 권한을 제한하여 응용 프로그램 코드가 SQL 삽입 공격에 취약한 경우 악의적인 활동의 위험을 줄일 수 있습니다. 응용 프로그램을 통해 데이터베이스에 직접 인증할 수 있는 [포함된 데이터베이스 사용자](https://msdn.microsoft.com/library/ff929188)를 만드는 것이 좋습니다. 서버 관리자 로그인으로 사용자 데이터베이스에 연결되어 있는 동안 다음 T-SQL을 실행하여 SQL 인증을 사용하는 포함된 데이터베이스 사용자를 만들 수 있습니다.

```
CREATE USER ApplicationUser WITH PASSWORD = 'strong_password'; -- SQL Authentication
```

Azure AD 관리자를 만든 경우 Azure AD 관리자로 사용자 데이터베이스에 연결되어 있는 동안 다음 T-SQL을 실행하여 Azure Active Directory 인증을 사용하는 포함된 데이터베이스 사용자를 만들 수 있습니다.

```
CREATE USER [Azure_AD_principal_name | Azure_AD_group_display_name] FROM EXTERNAL PROVIDER; -- Azure Active Directory Authentication
```

두 경우 데이터베이스에 연결하려면 응용 프로그램의 연결 문자열에 서버 관리자 로그인 대신 해당 사용자 자격 증명을 지정해야 합니다.

SQL 데이터베이스 인증에 대한 자세한 내용은 [Azure SQL 데이터베이스에서 데이터베이스 및 로그인 관리](sql-database-manage-logins.md)를 참조하세요.

## <a name="authorization"></a>권한 부여
권한 부여는 Azure SQL 데이터베이스 내에서 수행할 수 있는 작업을 가리키며 사용자 계정의 역할 멤버 자격과 사용 권한에 의해 제어됩니다. 사용자에게 필요한 최소한의 권한을 부여하는 것이 가장 좋습니다. Azure SQL 데이터베이스를 사용하면 T-SQL에서 역할을 통해 쉽게 관리할 수 있습니다.

```
ALTER ROLE db_datareader ADD MEMBER ApplicationUser; -- allows ApplicationUser to read data
ALTER ROLE db_datawriter ADD MEMBER ApplicationUser; -- allows ApplicationUser to write data
```

연결 중인 서버 관리자 계정은 데이터베이스 내에서 작업을 수행할 권한이 있는 db_owner의 구성원입니다. 스키마 업그레이드 및 기타 관리 작업을 배포하기 위해서는 이 계정을 저장합니다. 응용 프로그램에서 해당 응용 프로그램에 필요한 최소한의 권한이 있는 데이터베이스에 연결하려면 보다 제한된 사용 권한을 가진 "ApplicationUser" 계정을 사용합니다.

사용자가 Azure SQL 데이터베이스로 수행할 수 있는 작업을 더욱 제한할 수 있는 방법이 있습니다.

* db_datareader 및 db_datawriter 이외의 [데이터베이스 역할](https://msdn.microsoft.com/library/ms189121)은 더 강력한 응용 프로그램 사용자 계정이나 덜 강력한 관리 계정을 만드는 데 사용할 수 있습니다.
* 세분화된 [권한](https://msdn.microsoft.com/library/ms191291) 을 사용하면 개별 열, 테이블, 뷰, 프로시저 및 데이터베이스의 다른 개체에서 수행할 수 있는 작업을 제어할 수 있습니다.
* [가장](https://msdn.microsoft.com/library/vstudio/bb669087) 및 [모듈 서명](https://msdn.microsoft.com/library/bb669102)은 일시적으로 권한을 안전하게 상승시키는 데 사용할 수 있습니다.
* [행 수준 보안](https://msdn.microsoft.com/library/dn765131) 은 사용자가 액세스할 수 있는 행을 제한하는 데 사용할 수 있습니다.
* [데이터 마스킹](sql-database-dynamic-data-masking-get-started.md) 은 중요한 데이터의 노출을 제한하는 데 사용할 수 있습니다.
* [저장 프로시저](https://msdn.microsoft.com/library/ms190782) 는 데이터베이스에서 수행할 수 있는 작업을 제한하는 데 사용할 수 있습니다.

Azure 클래식 포털에서 또는 Azure 리소스 관리자 API를 사용하여 데이터베이스 및 논리 서버를 관리하는 것은 포털 사용자 계정의 역할 할당에 의해 제어됩니다. 이 항목에 대한 자세한 내용은 [Azure 포털의 역할 기반 액세스 제어](../active-directory/role-based-access-control-configure.md)를 참조하세요.

## <a name="encryption"></a>암호화
Azure SQL 데이터베이스는 데이터가 "휴지 상태"일 때 또는 데이터베이스 파일 및 백업에 저장된 경우 [투명한 데이터 암호화](http://go.microsoft.com/fwlink/?LinkId=526242)를 사용하여 데이터를 암호화함으로써 데이터를 보호할 수 있도록 도와줍니다. 데이터베이스를 암호화하려면 데이터베이스 소유자로 연결하고 다음을 실행합니다.

```
ALTER DATABASE [AdventureWorks] SET ENCRYPTION ON;
```

사용자 데이터 암호를 암호화하는 다른 방법으로 다음을 고려해 보세요.

* [셀 수준 암호화](https://msdn.microsoft.com/library/ms179331.aspx) 는 특정 열 또는 서로 다른 암호화 키를 가진 데이터의 셀도 암호화합니다.
* 하드웨어 보안 모듈 또는 암호화 키 계층의 중앙 관리가 필요한 경우 [Azure VM에서 SQL Server와 함께 Azure 키 자격 증명 모음](http://blogs.technet.com/b/kv/archive/2015/01/12/using-the-key-vault-for-sql-server-encryption.aspx)을 사용하는 것을 고려해 보세요.
* [항상 암호화](https://msdn.microsoft.com/library/mt163865.aspx) (미리 보기)를 사용하면 암호화가 응용 프로그램에 하며 클라이언트가 SQL Database와 암호화 키를 공유하지 않고도 클라이언트 응용 프로그램 내부에서 중요 데이터를 암호화할 수 있습니다.

## <a name="auditing"></a>감사
데이터베이스 이벤트의 감사 및 추적은 규정을 준수하고 의심스러운 활동을 식별할 수 있도록 도와줍니다. SQL 데이터베이스 감사를 사용하면 Azure 저장소 계정의 감사 로그에 데이터베이스의 이벤트를 기록할 수 있습니다. 또한 드릴다운 보고서 및 분석을 용이하게 하려면 SQL 데이터베이스 감사 기능을 Microsoft Power BI와 통합합니다. 자세한 내용은 [SQL 데이터베이스 감사 시작](sql-database-auditing-get-started.md)을 참조하세요.

SQL 데이터베이스 위협 탐지는 감사를 기반으로 추가 보안 계층을 제공합니다. 비정상적인 활동에서 보안 경고를 제공하여 발생하는 위협에 대응할 수 있습니다. 자세한 내용은 [SQL 데이터베이스 위협 감지 시작](sql-database-threat-detection-get-started.md)을 참조하세요.  

## <a name="compliance"></a>규정 준수
위의 기능 및 응용 프로그램이 다양한 보안 규정 준수 요구 사항을 충족하도록 도울 수 있는 기능 외에도 Azure SQL 데이터베이스는 정기적인 감사에 참여하고 여러 규정 준수 표준에 대해 인증받았습니다. 자세한 내용은 [Microsoft Azure 보안 센터](https://azure.microsoft.com/support/trust-center/)를 참조하세요. 여기서 최신 [SQL Database 규정 준수 인증서](https://azure.microsoft.com/support/trust-center/services/) 목록을 찾을 수 있습니다.


## <a name="next-steps"></a>다음 단계

- 모든 SQL Database 보안 기능에 대한 개요는 [SQL 보안 개요](sql-database-security-overview.md)를 참조하세요.
- SQL Database에서 액세스 제어 기능을 사용하는 방법에 대한 설명은 [액세스 제어](sql-database-control-access.md)를 참조하세요.
- 사전 모니터링에 대한 설명은 [SQL Database 감사 시작](sql-database-auditing-get-started.md) 및 [SQL Database 위협 검색 시작](sql-database-threat-detection-get-started.md)을 참조하세요.

