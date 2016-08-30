<properties
   pageTitle="SQL 데이터베이스 및 SQL 데이터 웨어하우스를 사용한 Azure AD MFA에 대한 SSMS 지원 | Microsoft Azure"
   description="SQL 데이터베이스 및 SQL 데이터 웨어하우스용 SSMS에서 Multi-Factor Authentication 사용"
   services="sql-database"
   documentationCenter=""
   authors="BYHAM"
   manager="jhubbard"
   editor=""
   tags=""/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="08/15/2016"
   ms.author="rick.byham@microsoft.com"/>

# SQL 데이터베이스 및 SQL 데이터 웨어하우스를 사용한 Azure AD MFA에 대한 SSMS 지원

Azure SQL 데이터베이스 및 Azure SQL 데이터 웨어하우스는 이제 *Active Directory 유니버설 인증*을 사용하여 SSMS(SQL Server Management Studio)에서의 연결을 지원합니다. Active Directory 유니버설 인증은 *Azure MFA(Multi-factor Authentication)*를 지원하는 대화형 워크플로입니다. Azure MFA는 간단한 로그인 프로세스에 대한 사용자 요구를 충족하는 동안 데이터와 응용 프로그램에 대한 액세스를 보호하는 데 도움이 됩니다. 또한 전화 통화, 문자 메시지, PIN을 사용하는 스마트 카드, 모바일 앱 알림 등의 여러 가지 간편한 인증 옵션을 통해 강력한 인증을 제공하므로 사용자는 선호하는 방법을 선택할 수 있습니다. Multi-Factor Authentication에 대한 설명을 보려면 [Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md)을 참조하세요.

SSMS는 이제 다음을 지원합니다.

- 팝업 대화 상자 유효성 검사를 위한 Azure AD가 있는 대화형 MFA
- 여러 다른 응용 프로그램(ADO.NET, JDBC, ODBC 등)에서 사용할 수 있는 비대화형 Active Directory 암호 및 Active Directory 통합 인증 방법 이러한 두 가지 방법을 사용할 경우 팝업 대화 상자가 절대 표시되지 않습니다.

MFA에 대해 사용자 계정을 구성하면 대화형 인증 워크플로는 팝업 대화 상자, 스마트 카드 사용 등을 통해 추가 사용자 작업을 요구합니다. MFA에 대해 사용자 계정을 구성하면 사용자는 연결을 위해 Azure 유니버설 인증을 선택해야 합니다. 사용자 계정이 MFA를 요구하지 않으면 사용자는 다른 두 가지 Azure Active Directory 인증 옵션을 사용할 수 있습니다.

## SQL 데이터베이스 및 SQL 데이터 웨어하우스에 대한 유니버설 인증 제한 사항

- SSMS는 현재 Active Directory 유니버설 인증을 통해 MFA에서 사용할 수 있는 유일한 도구입니다.
- 유니버설 인증을 사용하여 SSMS 인스턴스에 대해 단일 Azure Active Directory 계정으로만 로그인할 수 있습니다. 다른 Azure AD 계정으로 로그인하려면 SSMS의 다른 인스턴스를 사용해야 합니다. (이 제한은 Active Directory 유니버설 인증으로 제한됩니다. Active Directory 암호 인증, Active Directory 통합 인증 또는 SQL Server 인증을 사용하여 다른 서버에 로그인할 수 있습니다.)
- SSMS는 개체 탐색기, 쿼리 편집기 및 쿼리 저장소 시각화에 대해 Active Directory 유니버설 인증을 지원합니다.
- DacFx 및 스키마 디자이너는 유니버설 인증을 지원하지 않습니다.
- MSA 계정은 Active Directory 유니버설 인증에 대해 지원되지 않습니다.
- 다른 Active Directory에서 현재 Active Directory로 가져온 사용자의 경우 SSMS에서 Active Directory 유니버설 인증이 지원되지 않습니다.
- 지원되는 버전의 SSMS를 사용해야 한다는 점을 제외하고, Active Directory 유니버설 인증에 대한 추가적인 소프트웨어 요구 사항은 없습니다.

## 구성 단계

Multi-factor Authentication를 구현하려면 4가지 기본 단계가 필요합니다.

1. **Azure Active Directory 구성** – 자세한 내용은 [Azure Active Directory와 온-프레미스 ID 통합](../active-directory/active-directory-aadconnect.md), [Azure AD에 고유한 도메인 이름 추가](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/), [이제 Microsoft Azure에서 Windows Server Active Directory와의 페더레이션 지원](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/), [Azure AD 디렉터리 관리](https://msdn.microsoft.com/library/azure/hh967611.aspx) 및 [Windows PowerShell을 사용한 Azure AD 관리](https://msdn.microsoft.com/library/azure/jj151815.aspx)를 참조하세요.

2. **MFA 구성** – 단계별 지침을 보려면 [Azure Multi-factor Authentication 구성](../multi-factor-authentication/multi-factor-authentication-whats-next.md)을 참조하세요.

3. **Azure AD 인증을 위해 SQL 데이터베이스 또는 SQL 데이터 웨어하우스 구성** – 단계별 지침에 대해서는 [Azure Active Directory 인증을 사용하여 SQL 데이터베이스 또는 SQL 데이터 웨어하우스에 연결](sql-database-aad-authentication.md)을 참조하세요.

4. **SSMS 다운로드** - 클라이언트 컴퓨터에서 최신 SSMS(2016년 8월 이상)를 [SSMS(SQL Server Management Studio) 다운로드](https://msdn.microsoft.com/library/mt238290.aspx)에서 다운로드합니다.

## SSMS를 통한 유니버설 인증을 사용하여 연결

다음 단계에서는 최신 SSMS를 사용하여 SQL 데이터베이스 또는 SQL 데이터 웨어하우스에 연결하는 방법을 설명합니다.

1. 유니버설 인증을 사용하여 연결하려면 **서버에 연결** 대화 상자에서 **Active Directory 유니버설 인증**을 선택합니다. ![1mfa-universal-connect][1]

2. SQL 데이터베이스 및 SQL 데이터 웨어하우스에 대해 평소처럼 **옵션**을 클릭하고 **옵션** 대화 상자에서 데이터베이스를 지정합니다. 그런 다음 **연결**을 클릭합니다.
3. **사용자 계정 로그인** 대화 상자가 나타나면 Azure Active Directory ID의 계정 및 암호를 제공합니다. ![2mfa-sign-in][2]

    > [AZURE.NOTE] MFA를 요구하지 않는 계정을 사용한 유니버설 인증의 경우 이 시점에서 연결합니다. MFA가 필요한 사용자는 다음 단계를 계속 진행합니다.
 
4. 두 개의 MFA 설치 대화 상자가 나타날 수 있습니다. 이 일회성 작업은 MFA 관리자 설정에 따라 다르므로 선택적일 수 있습니다. MFA 사용 도메인의 경우 이 단계는 경우에 따라 미리 정의됩니다(예를 들어 도메인은 사용자에게 스마트 카드와 핀을 사용하도록 요구함). ![3mfa-setup][3]

5. 두 번째 가능한 일회성 대화 상자를 사용하여 인증 방법의 세부 정보를 선택할 수 있습니다. 가능한 옵션은 관리자가 구성합니다. ![4mfa-verify-1][4]
 
6. Azure Active Directory는 사용자에게 확인 정보를 보냅니다. 확인 코드를 받게되면 **확인 코드 입력** 상자에 해당 코드를 입력하고 **로그인**을 클릭합니다. ![5mfa-verify-2][5]

확인이 완료되면 SSMS는 일반적으로 가정된 유효한 자격 증명 및 방화벽 액세스를 연결합니다.

##다음 단계  

다른 데이터베이스에 대한 액세스 권한 부여: [SQL 데이터베이스 인증 및 권한 부여: 액세스 권한 부여](sql-database-manage-logins.md) 다른 사용자가 방화벽을 통해 연결할 수 있는지 확인: [Azure 포털을 사용하여 Azure SQL 데이터베이스 서버 수준 방화벽 규칙 구성](sql-database-configure-firewall-settings.md)


[1]: ./media/sql-database-ssms-mfa-auth/1mfa-universal-connect.png
[2]: ./media/sql-database-ssms-mfa-auth/2mfa-sign-in.png
[3]: ./media/sql-database-ssms-mfa-auth/3mfa-setup.png
[4]: ./media/sql-database-ssms-mfa-auth/4mfa-verify-1.png
[5]: ./media/sql-database-ssms-mfa-auth/5mfa-verify-2.png

<!---HONumber=AcomDC_0817_2016-->