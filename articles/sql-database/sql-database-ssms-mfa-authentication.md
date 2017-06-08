---
title: Multi-Factor Authentication - Azure SQL | Microsoft Docs
description: "SQL 데이터베이스 및 SQL 데이터 웨어하우스용 SSMS에서 Multi-Factor Authentication 사용"
services: sql-database
documentationcenter: 
author: BYHAM
manager: jhubbard
editor: 
tags: 
ms.assetid: fbd6e644-0520-439c-8304-2e4fb6d6eb91
ms.service: sql-database
ms.custom: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 04/07/2017
ms.author: rickbyh
ms.translationtype: Human Translation
ms.sourcegitcommit: a3ca1527eee068e952f81f6629d7160803b3f45a
ms.openlocfilehash: 33282f1ce0c31056524359aeb42399be550334e2
ms.contentlocale: ko-kr
ms.lasthandoff: 04/27/2017


---
# <a name="universal-authentication-with-sql-database-and-sql-data-warehouse-ssms-support-for-mfa"></a>SQL Database 및 SQL Data Warehouse에 대한 유니버설 인증(MFA에 대한 SSMS 지원)
Azure SQL Database 및 Azure SQL Data Warehouse는 *Active Directory 유니버설 인증*을 사용하여 SSMS(SQL Server Management Studio)에서의 연결을 지원합니다. 

- Active Directory 유니버설 인증은 2가지 비대화형 인증 방법(Active Directory 암호 인증 및 Active Directory 통합 인증)을 지원합니다. 비대화형 Active Directory 암호 및 Active Directory 통합 인증 방법은 여러 다른 응용 프로그램(ADO.NET, JDBC, ODBC 등)에서 사용할 수 있습니다. 이러한 두 가지 방법을 사용할 경우 팝업 대화 상자가 절대 표시되지 않습니다.

- Active Directory 유니버설 인증은 *Azure MFA(Multi-factor Authentication)*를 지원하는 대화형 방법입니다. Azure MFA는 간단한 로그인 프로세스에 대한 사용자 요구를 충족하는 동안 데이터와 응용 프로그램에 대한 액세스를 보호하는 데 도움이 됩니다. 또한 전화 통화, 문자 메시지, PIN을 사용하는 스마트 카드, 모바일 앱 알림 등의 여러 가지 간편한 인증 옵션을 통해 강력한 인증을 제공하므로 사용자는 선호하는 방법을 선택할 수 있습니다. Azure AD를 사용하는 대화형 MFA는 유효성 검사를 위한 팝업 대화 상자를 표시할 수 있습니다.

Multi-Factor Authentication에 대한 설명을 보려면 [Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md)을 참조하세요.
[SQL Server Management Studio에 대한 Azure SQL Database multi-factor authentication 구성](sql-database-ssms-mfa-authentication-configure.md)을 참조하세요.

### <a name="azure-ad-domain-name-or-tenant-id-parameter"></a>Azure AD 도메인 이름 또는 테넌트 ID 매개 변수   

[SSMS 버전 17](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)부터 다른 Azure Active 디렉터리에서 현재 Active Directory로 가져온 사용자는 연결할 때 Azure AD 도메인 이름 또는 테넌트 ID를 제공할 수 있습니다. 이를 통해 **Active Directory 유니버설 인증**에서 올바른 인증 기관을 식별할 수 있습니다. 이 옵션은 outlook.com, hotmail.com 또는 live.com과 같은 Microsoft 계정(MSA)을 지원하는 데도 필요합니다. 유니버설 인증을 사용하여 인증을 받으려면 이러한 모든 사용자는 Azure AD 도메인 이름 또는 테넌트 ID를 입력해야 합니다. 이 매개 변수는 Azure 서버와 연결되는 현재 Azure AD 도메인 이름/테넌트 ID를 나타냅니다. 예를 들어 Azure 서버가 사용자 `joe@contosodev.onmicrosoft.com`이 Azure AD 도메인 `contosodev.onmicrosoft.com`에서 가져온 사용자로 호스트되는 Azure AD 도메인 `contosotest.onmicrosoft.com`에 연결되는 경우 이 사용자를 인증하는 데 필요한 도메인 이름은 `contosotest.onmicrosoft.com`입니다. 사용자가 Azure 서버에 연결된 Azure AD의 네이티브 사용자이고 MSA 계정이 아닌 경우 도메인 이름 또는 테넌트 ID는 필요하지 않습니다. 매개 변수를 입력하려면(SSMS 버전 17부터) **데이터베이스에 연결** 대화 상자에서 대화 상자를 완료하고 **Active Directory 유니버설 인증**을 선택한 후 **옵션**, **연결 속성** 탭을 차례로 클릭하고 **AD 도메인 이름 또는 테넌트 ID** 상자를 선택하고 도메인 이름(**contosotest.onmicrosoft.com**) 또는 테넌트 ID의 GUID와 같은 인증 기관을 제공합니다.

## <a name="universal-authentication-limitations-for-sql-database-and-sql-data-warehouse"></a>SQL 데이터베이스 및 SQL 데이터 웨어하우스에 대한 유니버설 인증 제한 사항
* SSMS는 현재 Active Directory 유니버설 인증을 통해 MFA에서 사용할 수 있는 유일한 도구입니다.
* 유니버설 인증을 사용하여 SSMS 인스턴스에 대해 단일 Azure Active Directory 계정으로만 로그인할 수 있습니다. 다른 Azure AD 계정으로 로그인하려면 SSMS의 다른 인스턴스를 사용해야 합니다. (이 제한은 Active Directory 유니버설 인증으로 제한됩니다. Active Directory 암호 인증, Active Directory 통합 인증 또는 SQL Server 인증을 사용하여 다른 서버에 로그인할 수 있습니다.)
* SSMS는 개체 탐색기, 쿼리 편집기 및 쿼리 저장소 시각화에 대해 Active Directory 유니버설 인증을 지원합니다.
* DacFx 및 스키마 디자이너는 유니버설 인증을 지원하지 않습니다.
* 지원되는 버전의 SSMS를 사용해야 한다는 점을 제외하고, Active Directory 유니버설 인증에 대한 추가적인 소프트웨어 요구 사항은 없습니다.


## <a name="next-steps"></a>다음 단계

* [SQL Server Management Studio에 대한 Azure SQL Database multi-factor authentication 구성](sql-database-ssms-mfa-authentication-configure.md)을 참조하세요.
* 데이터베이스에 대한 액세스를 부여합니다. [SQL Database 인증 및 권한 부여: 액세스 부여](sql-database-manage-logins.md)  
방화벽을 통해 연결할 수 있는지 확인합니다. [Azure Portal을 사용하여 Azure SQL Database 서버 수준 방화벽 규칙 구성](sql-database-configure-firewall-settings.md)
* Azure AD 구성 및 관리는 [SQL Database 또는 SQL Data Warehouse에서 Azure Active Directory 인증 구성 및 관리](sql-database-aad-authentication-configure.md)를 참조하세요.



