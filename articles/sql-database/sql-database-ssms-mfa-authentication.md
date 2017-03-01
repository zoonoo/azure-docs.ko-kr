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
ms.custom: authentication and authorization
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 01/23/2017
ms.author: rickbyh
translationtype: Human Translation
ms.sourcegitcommit: 6e68ff56eaab3de8166711c872bf1b5707dbdf56
ms.openlocfilehash: ce77a059a05f83d74e80c71a87a22de6755b2dbb
ms.lasthandoff: 02/16/2017


---
# <a name="ssms-support-for-azure-ad-mfa-with-sql-database-and-sql-data-warehouse"></a>SQL 데이터베이스 및 SQL 데이터 웨어하우스를 사용한 Azure AD MFA에 대한 SSMS 지원
Azure SQL 데이터베이스 및 Azure SQL 데이터 웨어하우스는 이제 *Active Directory 유니버설 인증*을 사용하여 SSMS(SQL Server Management Studio)에서의 연결을 지원합니다. Active Directory 유니버설 인증은 *Azure MFA(Multi-factor Authentication)* 를 지원하는 대화형 워크플로입니다. Azure MFA는 간단한 로그인 프로세스에 대한 사용자 요구를 충족하는 동안 데이터와 응용 프로그램에 대한 액세스를 보호하는 데 도움이 됩니다. 또한 전화 통화, 문자 메시지, PIN을 사용하는 스마트 카드, 모바일 앱 알림 등의 여러 가지 간편한 인증 옵션을 통해 강력한 인증을 제공하므로 사용자는 선호하는 방법을 선택할 수 있습니다. 

* Multi-Factor Authentication에 대한 설명을 보려면 [Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md)을 참조하세요.
* [SQL Server Management Studio에 대한 Azure SQL Database multi-factor authentication 구성](sql-database-ssms-mfa-authentication-configure.md)을 참조하세요.

## <a name="multi-factor-options"></a>다중 요소 옵션

SSMS는 이제 다음을 지원합니다.

* 팝업 대화 상자 유효성 검사를 위한 Azure AD가 있는 대화형 MFA
* 여러 다른 응용 프로그램(ADO.NET, JDBC, ODBC 등)에서 사용할 수 있는 비대화형 Active Directory 암호 및 Active Directory 통합 인증 방법 이러한 두 가지 방법을 사용할 경우 팝업 대화 상자가 절대 표시되지 않습니다.

MFA에 대해 사용자 계정을 구성하면 대화형 인증 워크플로는 팝업 대화 상자, 스마트 카드 사용 등을 통해 추가 사용자 작업을 요구합니다. MFA에 대해 사용자 계정을 구성하면 사용자는 연결을 위해 Azure 유니버설 인증을 선택해야 합니다. 사용자 계정이 MFA를 요구하지 않으면 사용자는 다른 두 가지 Azure Active Directory 인증 옵션을 사용할 수 있습니다.

## <a name="universal-authentication-limitations-for-sql-database-and-sql-data-warehouse"></a>SQL 데이터베이스 및 SQL 데이터 웨어하우스에 대한 유니버설 인증 제한 사항
* SSMS는 현재 Active Directory 유니버설 인증을 통해 MFA에서 사용할 수 있는 유일한 도구입니다.
* 유니버설 인증을 사용하여 SSMS 인스턴스에 대해 단일 Azure Active Directory 계정으로만 로그인할 수 있습니다. 다른 Azure AD 계정으로 로그인하려면 SSMS의 다른 인스턴스를 사용해야 합니다. (이 제한은 Active Directory 유니버설 인증으로 제한됩니다. Active Directory 암호 인증, Active Directory 통합 인증 또는 SQL Server 인증을 사용하여 다른 서버에 로그인할 수 있습니다.)
* SSMS는 개체 탐색기, 쿼리 편집기 및 쿼리 저장소 시각화에 대해 Active Directory 유니버설 인증을 지원합니다.
* DacFx 및 스키마 디자이너는 유니버설 인증을 지원하지 않습니다.
* MSA 계정은 Active Directory 유니버설 인증에 대해 지원되지 않습니다.
* 다른 Azure Active Directory에서 현재 Active Directory로 가져온 사용자의 경우 SSMS에서 Active Directory 유니버설 인증이 지원되지 않습니다. 이러한 사용자는 계정의 유효성을 검사하는 테넌트 ID가 필요하며 이를 제공하는 메커니즘이 없으므로 지원되지 않습니다.
* 지원되는 버전의 SSMS를 사용해야 한다는 점을 제외하고, Active Directory 유니버설 인증에 대한 추가적인 소프트웨어 요구 사항은 없습니다.



## <a name="next-steps"></a>다음 단계

* [SQL Server Management Studio에 대한 Azure SQL Database multi-factor authentication 구성](sql-database-ssms-mfa-authentication-configure.md)을 참조하세요.
* 데이터베이스에 대한 액세스를 부여합니다. [SQL Database 인증 및 권한 부여: 액세스 부여](sql-database-manage-logins.md)  
방화벽을 통해 연결할 수 있는지 확인합니다. [Azure Portal을 사용하여 Azure SQL Database 서버 수준 방화벽 규칙 구성](sql-database-configure-firewall-settings.md)



