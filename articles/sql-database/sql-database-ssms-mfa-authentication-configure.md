---
title: "Multi-Factor Authentication 구성 - Azure SQL | Microsoft Docs"
description: "SQL 데이터베이스 및 SQL 데이터 웨어하우스용 SSMS에서 Multi-Factor Authentication 사용"
services: sql-database
documentationcenter: 
author: BYHAM
manager: jhubbard
editor: 
tags: 
ms.assetid: 
ms.service: sql-database
ms.custom: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 06/08/2017
ms.author: rickbyh
ms.translationtype: Human Translation
ms.sourcegitcommit: 245ce9261332a3d36a36968f7c9dbc4611a019b2
ms.openlocfilehash: 1815ea909e35a02b82b4c836d7baaf6390d20bdd
ms.contentlocale: ko-kr
ms.lasthandoff: 06/09/2017


---
# <a name="configure-azure-sql-database-multi-factor-authentication-for-sql-server-management-studio"></a>SQL Server Management Studio에 대한 Azure SQL Database Multi-Factor Authentication 구성

이 항목에서는 SQL Server Management Studio에 대한 Azure SQL Database Multi-Factor Authentication 구성 방법을 보여 줍니다. 

Azure SQL Database 다단계 인증에 대한 개요는 [SQL Database 및 SQL Data Warehouse에 대한 유니버설 인증(MFA에 대한 SSMS 지원)](sql-database-ssms-mfa-authentication.md)을 참조하세요.

## <a name="configuration-steps"></a>구성 단계

1. **Azure Active Directory 구성** - 자세한 내용은 [Azure Active Directory와 온-프레미스 ID 통합](../active-directory/active-directory-aadconnect.md), [Azure AD에 고유한 도메인 이름 추가](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/), [Microsoft Azure는 이제 Windows Server Active Directory와의 페더레이션 지원](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/), [Azure AD 디렉터리 관리](https://msdn.microsoft.com/library/azure/hh967611.aspx) 및 [Windows PowerShell을 사용하여 Azure AD 관리](https://msdn.microsoft.com/library/azure/jj151815.aspx)를 참조하세요.
2. **MFA 구성** - 단계별 지침은 [Conditional Access (MFA) with Azure SQL Database and Data Warehouse](sql-database-conditional-access.md)(Azure SQL Database 및 데이터 웨어하우스를 사용하여 조건부 액세스(MFA))를 참조하세요. 
3. **Azure AD 인증을 위해 SQL Database 또는 SQL Data Warehouse 구성** - 단계별 지침은 [Azure Active Directory 인증을 사용하여 SQL Database 또는 SQL Data Warehouse에 연결](sql-database-aad-authentication.md)을 참조하세요.
4. **SSMS 다운로드** - 클라이언트 컴퓨터에서 최신 SSMS(2016년 8월 이상)를 [SSMS(SQL Server Management Studio) 다운로드](https://msdn.microsoft.com/library/mt238290.aspx)에서 다운로드합니다.

## <a name="connecting-by-using-universal-authentication-with-ssms"></a>SSMS를 통한 유니버설 인증을 사용하여 연결

다음 단계에서는 최신 SSMS를 사용하여 SQL 데이터베이스 또는 SQL 데이터 웨어하우스에 연결하는 방법을 설명합니다.

1. 유니버설 인증을 사용하여 연결하려면 **서버에 연결** 대화 상자에서 **Active Directory 유니버설 인증**을 선택합니다.

   ![1mfa-universal-connect][1]
2. SQL Database 및 SQL Data Warehouse에 대해 평소처럼 **옵션**을 클릭하고 **옵션** 대화 상자에서 데이터베이스를 지정해야 합니다. 그런 다음 **연결**을 클릭합니다.
3. **사용자 계정 로그인** 대화 상자가 나타나면 Azure Active Directory ID의 계정 및 암호를 제공합니다.

   ![2mfa-sign-in][2]
   
   > [!NOTE]
   > MFA가 필요하지 않은 계정을 사용하는 유니버설 인증의 경우 이 시점에서 연결합니다. MFA가 필요한 사용자는 다음 단계를 계속 진행합니다.
   > 
   > 
4. 두 개의 MFA 설치 대화 상자가 나타날 수 있습니다. 이 일회성 작업은 MFA 관리자 설정에 따라 다르므로 선택적일 수 있습니다. MFA 사용 도메인의 경우 이 단계는 경우에 따라 미리 정의됩니다(예를 들어 도메인은 사용자에게 스마트 카드와 핀을 사용하도록 요구함).  

   ![3mfa-setup][3]
5. 두 번째 가능한 일회성 대화 상자를 사용하여 인증 방법의 세부 정보를 선택할 수 있습니다. 가능한 옵션은 관리자가 구성합니다.

   ![4mfa-verify-1][4]
6. Azure Active Directory는 사용자에게 확인 정보를 보냅니다. 확인 코드를 받게되면 **확인 코드 입력** 상자에 해당 코드를 입력하고 **로그인**을 클릭합니다.

   ![5mfa-verify-2][5]

확인이 완료되면 SSMS는 일반적으로 가정된 유효한 자격 증명 및 방화벽 액세스를 연결합니다.

## <a name="next-steps"></a>다음 단계

* Azure SQL Database 다단계 인증에 대한 개요는 SQL Database 및 SQL Data Warehouse에 대한 유니버설 인증(MFA에 대한 SSMS 지원)](sql-database-ssms-mfa-authentication.md)을 참조하세요.
* 데이터베이스에 대한 액세스를 부여합니다. [SQL Database 인증 및 권한 부여: 액세스 부여](sql-database-manage-logins.md)  
방화벽을 통해 연결할 수 있는지 확인합니다. [Azure Portal을 사용하여 Azure SQL Database 서버 수준 방화벽 규칙 구성](sql-database-configure-firewall-settings.md)

[1]: ./media/sql-database-ssms-mfa-auth/1mfa-universal-connect.png
[2]: ./media/sql-database-ssms-mfa-auth/2mfa-sign-in.png
[3]: ./media/sql-database-ssms-mfa-auth/3mfa-setup.png
[4]: ./media/sql-database-ssms-mfa-auth/4mfa-verify-1.png
[5]: ./media/sql-database-ssms-mfa-auth/5mfa-verify-2.png


