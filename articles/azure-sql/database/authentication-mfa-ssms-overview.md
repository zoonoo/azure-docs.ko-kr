---
title: Multi-factor Azure Active Directory 인증 사용
description: Azure SQL Database, Azure SQL Managed Instance 및 Azure Synapse Analytics는 Active Directory 유니버설 인증을 사용 하 여 SSMS (SQL Server Management Studio)에서의 연결을 지원 합니다.
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
titleSuffix: Azure SQL Database & SQL Managed Instance & Azure Synapse Analytics
ms.custom: seoapril2019, has-adal-ref, sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 09/28/2020
tags: azure-synapse
ms.openlocfilehash: 2a3e1e7279e915c0c5992190ef0c8d1d83880dbb
ms.sourcegitcommit: ffa7a269177ea3c9dcefd1dea18ccb6a87c03b70
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91596122"
---
# <a name="using-multi-factor-azure-active-directory-authentication"></a>Multi-factor Azure Active Directory 인증 사용
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Azure SQL Database, Azure SQL Managed Instance 및 Azure Synapse Analytics는 *Azure Active Directory-유니버설 인증을* 사용 하 여 [SSMS (SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) )에서의 연결을 지원 합니다. 이 문서에서는 다양 한 인증 옵션과 Azure SQL에 대 한 Azure Active Directory (Azure AD)에서 유니버설 인증을 사용 하는 것과 관련 된 제한 사항에 대해서도 설명 합니다.

**최신 SSMS 다운로드** - 클라이언트 컴퓨터에서 최신 SSMS 버전을 [SSMS(SQL Server Management Studio) 다운로드](https://aka.ms/ssms)에서 다운로드합니다. 

[!INCLUDE[ssms-connect-azure-ad](../includes/ssms-connect-azure-ad.md)]


이 문서에서 설명하는 모든 기능에 대해 2017년 7월 버전 17.2 이상을 사용합니다. 가장 최근의 연결 대화 상자는 다음 이미지와 유사하게 표시됩니다.

  ![서버 유형, 서버 이름 및 인증에 대 한 설정을 보여 주는 SQL Server Management Studio의 서버에 연결 대화 상자 스크린샷](./media/authentication-mfa-ssms-overview/1mfa-universal-connect.png)

## <a name="authentication-options"></a>인증 옵션

여러 다른 응용 프로그램 (ADO.NET, JDCB, .ODC 등)에서 사용할 수 있는 Azure AD 용 비 대화형 인증 모델은 두 가지입니다. 이러한 두 가지 방법을 사용할 경우 팝업 대화 상자가 표시되지 않습니다.

- `Azure Active Directory - Password`
- `Azure Active Directory - Integrated`

MFA (Azure Multi-Factor Authentication)도 지 원하는 대화형 메서드는 다음과 같습니다. 

- `Azure Active Directory - Universal with MFA`

Azure MFA를 사용하면 간단한 로그인 프로세스에 대한 사용자 요구를 충족하는 동시에 데이터 및 애플리케이션에 대한 액세스를 보호할 수 있습니다. 또한 전화 통화, 문자 메시지, PIN을 사용하는 스마트 카드, 모바일 앱 알림 등의 여러 가지 간편한 인증 옵션을 통해 강력한 인증을 제공하므로 사용자는 선호하는 방법을 선택할 수 있습니다. Azure AD를 사용하는 대화형 MFA는 유효성 검사를 위한 팝업 대화 상자를 표시할 수 있습니다.

Azure Multi-Factor Authentication에 대 한 설명은 [Multi-Factor Authentication](../../active-directory/authentication/multi-factor-authentication.md)를 참조 하세요.
[SQL Server Management Studio에 대한 Azure SQL Database multi-factor authentication 구성](authentication-mfa-ssms-configure.md)을 참조하세요.

### <a name="azure-ad-domain-name-or-tenant-id-parameter"></a>Azure AD 도메인 이름 또는 테넌트 ID 매개 변수

[SSMS 버전 17](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)부터 다른 Azure Active directory에서 게스트 사용자로 현재 azure ad로 가져온 사용자는 연결할 때 Azure ad 도메인 이름 또는 테 넌 트 ID를 제공할 수 있습니다. 게스트 사용자에는 타 Azure AD에서 초대 받은 사용자, outlook.com, hotmail.com, live.com 등의 Microsoft 계정, gmail.com 등의 타 계정이 포함됩니다. 이 정보 `Azure Active Directory - Universal with MFA` 를 사용 하 여 인증에서 올바른 인증 기관을 식별할 수 있습니다. 이 옵션은 outlook.com, hotmail.com 또는 live.com과 같은 Microsoft 계정(MSA)이나 비 MSA 계정을 지원하는 데도 필요합니다. 

유니버설 인증을 사용 하 여 인증 하려는 모든 게스트 사용자는 해당 Azure AD 도메인 이름 또는 테 넌 트 ID를 입력 해야 합니다. 이 매개 변수는 Azure SQL 논리 서버가 연결 된 현재 Azure AD 도메인 이름 또는 테 넌 트 ID를 나타냅니다. 예를 들어 SQL 논리 서버가 azure ad 도메인에 연결 된 경우 사용자가 `contosotest.onmicrosoft.com` `joe@contosodev.onmicrosoft.com` azure ad 도메인에서 가져온 사용자로 호스트 되는 경우 `contosodev.onmicrosoft.com` 이 사용자를 인증 하는 데 필요한 도메인 이름은 `contosotest.onmicrosoft.com` 입니다. 사용자가 SQL 논리 서버에 연결 된 Azure AD의 네이티브 사용자이 고 MSA 계정이 아닌 경우 도메인 이름 또는 테 넌 트 ID가 필요 하지 않습니다. 매개 변수를 입력 하려면 (SSMS 버전 17.2부터):


1. SSMS에서 연결을 엽니다. 서버 이름을 입력 하 고 MFA 인증 **을 사용 하 여 Azure Active Directory-유니버설** 을 선택 합니다. 로그인 할 **사용자 이름을** 추가 합니다.
1. **옵션** 상자를 선택 하 고 **연결 속성** 탭으로 이동 합니다. **데이터베이스에 연결** 대화 상자에서 데이터베이스에 대 한 대화 상자를 완료 합니다. **AD 도메인 이름 또는 테넌트 ID** 상자를 선택한 다음 인증 기관(예: 도메인 이름, **contosotest.onmicrosoft.com**) 또는 테넌트 ID의 GUID를 제공합니다. 

   ![데이터베이스 및 AD 도메인 이름 또는 테 넌 트 ID에 연결에 대 한 설정을 강조 표시 하는 연결 속성 탭의 스크린샷](./media/authentication-mfa-ssms-overview/mfa-tenant-ssms.png)

SSMS 18.x 이상을 실행 하는 경우에는 18.x 이상이 자동으로 인식 하기 때문에 게스트 사용자에 게 AD 도메인 이름 또는 테 넌 트 ID가 더 이상 필요 하지 않습니다.

   ![S M S의 서버에 연결 대화 상자에 있는 연결 속성 탭의 스크린샷. 데이터베이스에 연결 필드에서 "MyDatabase"가 선택 됩니다.](./media/authentication-mfa-ssms-overview/mfa-no-tenant-ssms.png)

### <a name="azure-ad-business-to-business-support"></a>Azure AD 기업 간 지원

> [!IMPORTANT]
> 그룹의 일부가 될 필요 없이 게스트 사용자가 Azure SQL Database, SQL Managed Instance 및 Azure Synapse에 연결할 수 있는 지원은 현재 **공개 미리 보기**상태입니다. 자세한 내용은 [AZURE ad 게스트 사용자 만들기 및 AZURE ad 관리자로 설정](authentication-aad-guest-users.md)을 참조 하세요.

게스트 사용자로 Azure AD B2B 시나리오에 대해 지원 되는 azure AD 사용자 ( [AZURE B2B 공동 작업](../../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md)참조)는 연결 된 azure ad에서 만든 그룹 멤버의 일부로만 SQL Database 및 azure Synapse에 연결 하 고 지정 된 데이터베이스에서 [CREATE USER (transact-sql)](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql) 문을 사용 하 여 수동으로 매핑할 수 있습니다. 예를 들어 azure ad 도메인을 사용 하 여 `steve@gmail.com` 를 AZURE ad에 초대 하는 경우 `contosotest` `contosotest.onmicrosoft.com` 와 같은 azure ad 그룹이 `usergroup` 구성원을 포함 하는 azure ad에 만들어져야 합니다 `steve@gmail.com` . 그런 다음 `MyDatabase` transact-sql 문을 실행 하 여 AZURE AD SQL 관리 또는 AZURE AD DBO에서 특정 데이터베이스 (예:)에 대해이 그룹을 만들어야 합니다 `CREATE USER [usergroup] FROM EXTERNAL PROVIDER` . 

데이터베이스 사용자를 만든 후에는 사용자가 `steve@gmail.com` `MyDatabase` SSMS 인증 옵션을 사용 하 여에 로그인 할 수 있습니다 `Azure Active Directory – Universal with MFA` . 기본적으로에는 `usergroup` connect 권한만 있습니다. 충분 한 권한이 있는 사용자가 데이터베이스에서 추가 데이터 액세스 권한을 [부여](https://docs.microsoft.com/sql/t-sql/statements/grant-transact-sql) 해야 합니다. 

> [!NOTE]
> SSMS 17.x의 경우를 `steve@gmail.com` 게스트 사용자로 사용 하 여 **ad 도메인 이름 또는 테 넌 트 ID** 상자를 확인 하 고 `contosotest.onmicrosoft.com` **연결 속성** 대화 상자에서 ad 도메인 이름을 추가 해야 합니다. **AD 도메인 이름 또는 테 넌 트 ID** 옵션은 **MFA 인증을 사용 하는 Azure Active Directory 범용** 으로만 지원 됩니다. 그렇지 않으면 확인란이 회색으로 표시 됩니다.

## <a name="universal-authentication-limitations"></a>유니버설 인증 제한 사항

- SSMS 및 SqlPackage.exe는 현재 Active Directory 유니버설 인증을 통해 MFA에서 사용할 수 있는 유일한 도구입니다.
- SSMS 버전 17.2은 MFA를 통한 유니버설 인증을 사용 하는 다중 사용자 동시 액세스를 지원 합니다. SSMS 버전 17.0 및 17.1의 경우이 도구는 유니버설 인증을 사용 하 여 SSMS의 인스턴스에 대 한 로그인을 단일 Azure Active Directory 계정으로 제한 합니다. 다른 Azure AD 계정으로 로그인 하려면 다른 SSMS 인스턴스를 사용 해야 합니다. 이 제한은 Active Directory 유니버설 인증으로 제한 됩니다. `Azure Active Directory - Password` 인증, 인증 또는를 사용 하 여 다른 서버에 로그인 할 수 있습니다 `Azure Active Directory - Integrated` `SQL Server Authentication` .
- SSMS는 개체 탐색기, 쿼리 편집기 및 쿼리 저장소 시각화에 대해 Active Directory 유니버설 인증을 지원합니다.
- SSMS 버전 17.2는 데이터 데이터베이스 내보내기/추출/배포를 위한 DacFx Wizard 마법사 지원을 제공합니다. 특정 사용자가 유니버설 인증을 사용하여 최초 인증 대화 상자를 통해 인증되면 DacFx 마법사가 모든 다른 인증 방법에서와 같은 방식으로 작동합니다.
- SSMS 테이블 디자이너는 유니버설 인증을 지원하지 않습니다.
- 지원되는 버전의 SSMS를 사용해야 한다는 점을 제외하고, Active Directory 유니버설 인증에 대한 추가적인 소프트웨어 요구 사항은 없습니다.  
- 유니버설 인증의 ADAL (최신 Active Directory 인증 라이브러리) 버전은 다음 링크를 참조 하세요. [ActiveDirectory. system.identitymodel.](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/)  

## <a name="next-steps"></a>다음 단계

- [SQL Server Management Studio에 대한 Azure SQL Database multi-factor authentication 구성](authentication-mfa-ssms-configure.md)을 참조하세요.
- 데이터베이스에 대한 액세스를 부여합니다. [SQL Database 인증 및 권한 부여: 액세스 부여](logins-create-manage.md)  
- 다른 사용자가 방화벽을 통해 연결할 수 있는지 확인 [합니다. Azure Portal를 사용 하 여 서버 수준 방화벽 규칙을 구성](firewall-configure.md) 합니다.  
- [SQL Database 또는 Azure Synapse를 사용 하 여 Azure Active Directory 인증 구성 및 관리](authentication-aad-configure.md)
- [Azure AD 게스트 사용자 만들기 및 Azure AD 관리자로 설정](authentication-aad-guest-users.md) 
- [Microsoft SQL Server Data-Tier Application Framework(17.0.0 GA)](https://www.microsoft.com/download/details.aspx?id=55088)  
- [SQLPackage.exe](https://docs.microsoft.com/sql/tools/sqlpackage)  
- [BACPAC 파일을 새 데이터베이스로 가져오기](database-import.md)  
- [BACPAC 파일로 데이터베이스 내보내기](database-export.md)  
- C# 인터페이스 [IUniversalAuthProvider Interface](https://msdn.microsoft.com/library/microsoft.sqlserver.dac.iuniversalauthprovider.aspx)  
- MFA 인증 **에 Azure Active Directory-유니버설** 를 사용 하는 경우 [SSMS 17.3](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)부터 ADAL 추적을 사용할 수 있습니다. 기본적으로 꺼짐인 ADAL 추적은 **도구**, **옵션** 메뉴, **Azure Services**, **Azure Cloud**, **ADAL 출력 창 추적 수준**을 사용하고 **보기** 메뉴의 **출력**을 활성화하여 켤 수 있습니다. 추적은 **Azure Active Directory 옵션**을 선택할 때 출력 창에서 사용할 수 있습니다.  
