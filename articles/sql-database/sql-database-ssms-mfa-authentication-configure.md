---
title: Multi-Factor Authentication 구성
description: SQL Database 및 Azure Synapse Analytics 용 SSMS에서 Multi-factor Authentication을 사용 하는 방법을 알아봅니다.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: has-adal-ref
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 08/27/2019
ms.openlocfilehash: e551ac54414cb3114a7d0905b1b2e59b0d16580a
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83198541"
---
# <a name="configure-multi-factor-authentication-for-sql-server-management-studio-and-azure-ad"></a>SQL Server Management Studio 및 Azure AD에 대한 Multi-factor Authentication(MFA) 구성

이 항목에서는 SQL Server Management Studio에서 Azure Active Directory Multi-factor Authentication(MFA)을 사용하는 방법을 보여 줍니다. Azure AD MFA는 SSMS 또는 SqlPackage를 Azure [SQL Database](sql-database-technical-overview.md) 및 [azure Synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)에 연결 하는 경우에 사용할 수 있습니다. Multi-factor authentication Azure SQL Database의 개요는 [SQL Database 및 Azure Synapse를 통한 유니버설 인증 (MFA에 대 한 SSMS 지원)](sql-database-ssms-mfa-authentication.md)을 참조 하세요.

> [!NOTE]
> 이 항목은 azure sql server에 적용 되며, Azure SQL server에 생성 되는 SQL Database 및 Azure Synapse 데이터베이스 모두에 적용 됩니다. 간단히 하기 위해 SQL Database와 Azure Synapse를 둘 다 참조할 때 SQL Database 사용 됩니다.

## <a name="configuration-steps"></a>구성 단계

1. **Azure Active Directory 구성** - 자세한 내용은 [Azure AD 디렉터리 관리](https://msdn.microsoft.com/library/azure/hh967611.aspx), [Azure Active Directory와 온-프레미스 ID 통합](../active-directory/hybrid/whatis-hybrid-identity.md), [Azure AD에 고유한 도메인 이름 추가](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/), [Microsoft Azure는 이제 Windows Server Active Directory와의 페더레이션 지원](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/) 및 [Windows PowerShell을 사용하여 Azure AD 관리](https://msdn.microsoft.com/library/azure/jj151815.aspx)를 참조하세요.
2. **MFA 구성** -단계별 지침은 [azure Multi-Factor Authentication?](../active-directory/authentication/multi-factor-authentication.md), [Azure SQL Database 및 azure Synapse를 사용 하는 조건부 액세스 (MFA)](sql-database-conditional-access.md)를 참조 하세요. (전체 조건부 액세스에는 Premium Azure Active Directory (Azure AD)가 필요 합니다. 표준 Azure AD에서는 제한된 MFA가 제공됩니다.
3. **Azure AD 인증에 대 한 SQL Database 또는 Azure Synapse 구성** -단계별 지침은 [Azure Active Directory 인증을 사용 하 여 SQL Database 또는 Azure Synapse에 연결](sql-database-aad-authentication.md)을 참조 하세요.
4. **SSMS 다운로드** - 클라이언트 컴퓨터에서 최신 SSMS를 [SSMS(SQL Server Management Studio) 다운로드](https://msdn.microsoft.com/library/mt238290.aspx)에서 다운로드합니다. 이 항목의 모든 기능에서는 2017년 7월 버전 17.2 이상을 사용합니다.  

## <a name="connecting-by-using-universal-authentication-with-ssms"></a>SSMS를 통한 유니버설 인증을 사용하여 연결

다음 단계에서는 최신 SSMS를 사용 하 여 SQL Database 또는 SAzure Synapse에 연결 하는 방법을 보여 줍니다.

1. 유니버설 인증을 사용하여 연결하려면 **서버에 연결** 대화 상자에서 **Active Directory - MFA 지원을 통한 유니버설 인증**을 선택합니다. **Active Directory 유니버설 인증**이 표시되면 최신 SSMS이 아닌 것입니다.  
   ![1mfa-universal-connect][1]  
2. Azure Active Directory 자격 증명을 사용하여 **사용자 이름** 상자를 `user_name@domain.com` 형식으로 입력합니다.  
   ![1mfa-universal-connect-user](./media/sql-database-ssms-mfa-auth/1mfa-universal-connect-user.png)   
3. 게스트 사용자로 연결 하는 경우 SSMS 4.x 이상에서 자동으로 인식 하기 때문에 게스트 사용자에 대 한 AD 도메인 이름 또는 테 넌 트 ID 필드를 더 이상 완료할 필요가 없습니다. 자세한 내용은 [SQL Database 및 Azure Synapse를 사용한 유니버설 인증 (MFA에 대 한 SSMS 지원)](sql-database-ssms-mfa-authentication.md)을 참조 하세요.
   ![mfa-테 넌 트 없음-ssms](./media/sql-database-ssms-mfa-auth/mfa-no-tenant-ssms.png)

   그러나 SSMS 17.x 이상을 사용 하 여 게스트 사용자로 연결 하는 경우에는 **옵션**을 클릭 하 고 **연결 속성** 대화 상자에서 **AD 도메인 이름 또는 테 넌 트 ID** 상자를 완료 해야 합니다.
   ![mfa-tenant-ssms](./media/sql-database-ssms-mfa-auth/mfa-tenant-ssms.png)

4. SQL Database 및 Azure Synapse에 대 한 일반적인 경우 **옵션** 을 클릭 하 고 **옵션** 대화 상자에서 데이터베이스를 지정 해야 합니다. 연결된 사용자가 게스트 사용자이면(즉 joe@outlook.com) 상자를 선택하고 현재 AD 도메인 이름이나 테넌트 ID를 옵션의 일부로 추가해야 합니다. [SQL Database 및 Azure Synapse를 사용 하는 유니버설 인증 (MFA에 대 한 SSMS 지원)](sql-database-ssms-mfa-authentication.md)을 참조 하세요. 그런 다음 **연결**을 클릭합니다.  
5. **사용자 계정 로그인** 대화 상자가 나타나면 Azure Active Directory ID의 계정 및 암호를 제공합니다. 사용자가 Azure AD와 페더레이션된 도메인에 속할 경우 암호가 필요하지 않습니다.  
   ![2mfa-sign-in][2]  

   > [!NOTE]
   > MFA가 필요하지 않은 계정을 사용하는 유니버설 인증의 경우 이 시점에서 연결합니다. MFA가 필요한 사용자는 다음 단계를 계속 진행합니다.
   >  
   
6. 두 개의 MFA 설치 대화 상자가 나타날 수 있습니다. 이 일회성 작업은 MFA 관리자 설정에 따라 다르므로 선택적일 수 있습니다. MFA 사용 도메인의 경우 이 단계는 경우에 따라 미리 정의됩니다(예를 들어 도메인은 사용자에게 스마트 카드와 핀을 사용하도록 요구함).  
   ![3mfa-setup][3]  
7. 두 번째 가능한 일회성 대화 상자를 사용하여 인증 방법의 세부 정보를 선택할 수 있습니다. 가능한 옵션은 관리자가 구성합니다.  
   ![4mfa-verify-1][4]  
8. Azure Active Directory는 사용자에게 확인 정보를 보냅니다. 확인 코드를 받게되면 **확인 코드 입력** 상자에 해당 코드를 입력하고 **로그인**을 클릭합니다.  
   ![5mfa-verify-2][5]  

확인이 완료되면 SSMS는 일반적으로 가정된 유효한 자격 증명 및 방화벽 액세스를 연결합니다.

## <a name="next-steps"></a>다음 단계

- Multi-factor authentication Azure SQL Database의 개요는 SQL Database 및 Azure Synapse를 통한 유니버설 인증 [(MFA에 대 한 SSMS 지원)](sql-database-ssms-mfa-authentication.md)을 참조 하세요.  
- 데이터베이스에 대한 액세스를 부여합니다. [SQL Database 인증 및 권한 부여: 액세스 부여](sql-database-manage-logins.md)  
- 방화벽을 통해 연결할 수 있는지 확인합니다. [Azure Portal을 사용하여 Azure SQL Database 서버 수준 방화벽 규칙 구성](sql-database-configure-firewall-settings.md)  
- **Active Directory- MFA 유니버설** 인증을 사용할 때 ADAL 추적은 [SSMS 17.3](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)에서 시작할 수 있습니다. 기본적으로 꺼짐인 ADAL 추적은 **도구**, **옵션** 메뉴, **Azure Services**, **Azure Cloud**, **ADAL 출력 창 추적 수준**을 사용하고 **보기** 메뉴의 **출력**을 활성화하여 켤 수 있습니다. 추적은 **Azure Active Directory 옵션**을 선택할 때 출력 창에서 사용할 수 있습니다.   


[1]: ./media/sql-database-ssms-mfa-auth/1mfa-universal-connect.png
[2]: ./media/sql-database-ssms-mfa-auth/2mfa-sign-in.png
[3]: ./media/sql-database-ssms-mfa-auth/3mfa-setup.png
[4]: ./media/sql-database-ssms-mfa-auth/4mfa-verify-1.png
[5]: ./media/sql-database-ssms-mfa-auth/5mfa-verify-2.png

