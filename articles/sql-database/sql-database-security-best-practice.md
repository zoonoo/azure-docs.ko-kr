---
title: 일반적인 보안 요구 사항 해결을 위한 플레이북 | 마이크로 소프트 문서
titleSuffix: Azure SQL Database
description: 이 문서에서는 Azure SQL Database의 일반적인 보안 요구 사항 및 모범 사례를 제공합니다.
ms.service: sql-database
ms.subservice: security
author: VanMSFT
ms.author: vanto
ms.topic: article
ms.date: 02/20/2020
ms.reviewer: ''
ms.openlocfilehash: 7b3a223ca504bff380afad54afda73880717814f
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/10/2020
ms.locfileid: "81115377"
---
# <a name="playbook-for-addressing-common-security-requirements-with-azure-sql-database"></a>Azure SQL 데이터베이스로 일반적인 보안 요구 사항을 해결하기 위한 플레이북

> [!NOTE]
> 이 문서에서는 일반적인 보안 요구 사항을 해결하는 방법에 대한 모범 사례를 제공합니다. 모든 요구 사항이 모든 환경에 적용되는 것은 아니며 구현할 기능을 데이터베이스 및 보안 팀에 문의해야 합니다.

## <a name="solving-common-security-requirements"></a>일반적인 보안 요구 사항 해결

이 문서에서는 Azure SQL Database를 사용하여 새 응용 프로그램이나 기존 응용 프로그램에 대한 일반적인 보안 요구 사항을 해결하는 방법에 대한 지침을 제공합니다. 그것은 높은 수준의 보안 영역에 의해 구성되어 있습니다. 특정 위협을 해결하려면 공통 [보안 위협 및 잠재적 완화](#common-security-threats-and-potential-mitigations) 섹션을 참조하십시오. 온-프레미스에서 Azure로 응용 프로그램을 마이그레이션할 때 제시된 권장 사항 중 일부를 적용할 수 있지만 마이그레이션 시나리오는 이 문서의 초점이 아닙니다.

### <a name="azure-sql-database-deployment-offers-covered-in-this-guide"></a>이 가이드에서 다루는 Azure SQL Database 배포

- [SQL 데이터베이스](https://docs.microsoft.com/azure/sql-database/sql-database-single-index): Azure SQL Database [서버의](sql-database-servers.md) 단일 [데이터베이스](sql-database-single-database.md) 및 [탄력적 풀](sql-database-elastic-pool.md)
- [관리되는 인스턴스](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-index)

### <a name="sql-deployment-offers-not-covered-in-this-guide"></a>이 가이드에서는 다루지 않는 SQL 배포 제안

- Azure SQL Data Warehouse
- Azure SQL VM(IaaS)
- SQL Server 온-프레미스

### <a name="audience"></a>사용자

이 가이드의 대상은 Azure SQL Database를 보호하는 방법에 대한 질문에 직면한 고객입니다. 이 모범 사례 문서에 관심이 있는 역할에는 다음이 포함되나 이에 국한되지 않습니다.

- 보안 설계자
- 보안 관리자
- 규정 준수 책임자
- 개인 정보 보호 책임자
- 보안 엔지니어

### <a name="using-this-guide"></a><a id="using"></a>이 가이드 사용

이 문서는 기존 Azure SQL [Database 보안](sql-database-security-overview.md) 문서의 사용 설명서를 사용합니다.

달리 명시되지 않는 한 각 섹션에 나열된 모든 모범 사례를 따라 각 목표 또는 요구 사항을 달성하는 것이 좋습니다. 특정 보안 규정 준수 표준 또는 모범 사례를 충족하기 위해 중요한 규정 준수 제어는 해당되는 경우 요구 사항 또는 목표 섹션 아래에 나열됩니다. 다음은 이 문서에서 참조하는 보안 표준 및 규정입니다.

- [페드램](https://www.fedramp.gov/documents/): AC-04, AC-06
- [SOC](https://www.aicpa.org/interestareas/frc/assuranceadvisoryservices/sorhome.html): CM-3, SDL-3
- [ISO/IEC 27001](https://www.iso27001security.com/html/27001.html): 액세스 제어, 암호화
- [Microsoft 운영 보안 보증(OSA) 사례](https://www.microsoft.com/en-us/securityengineering/osa/practices): #1-6 및 #9
- [NIST 특별 간행물 800-53 보안 제어](https://nvd.nist.gov/800-53): AC-5, AC-6
- [PCI DSS](https://www.pcisecuritystandards.org/document_library): 6.3.2, 6.4.2

### <a name="feedback"></a>사용자 의견

여기에 나열된 권장 사항 및 모범 사례를 계속 업데이트할 계획입니다. 이 문서의 맨 아래에 있는 **피드백** 링크를 사용하여 이 문서에 대한 입력 또는 수정 사항을 제공합니다.

## <a name="authentication"></a>인증

인증은 사용자의 신원을 증명하는 과정입니다. Azure SQL Database는 두 가지 인증 유형을 지원합니다.

- SQL 인증
- Azure Active Directory 인증

> [!NOTE]
> Azure Active Directory 인증은 모든 도구 및 타사 응용 프로그램에 대해 지원되지 않을 수 있습니다.

### <a name="central-management-for-identities"></a>ID에 대한 중앙 관리

중앙 ID 관리는 다음과 같은 이점을 제공합니다.

- Azure SQL Database 서버 및 데이터베이스에서 로그인을 복제하지 않고 그룹 계정 및 사용자 권한을 제어합니다.
- 간소화되고 유연한 권한 관리.
- 대규모 애플리케이션 관리

**구현 방법**:

- 중앙 집중식 ID 관리를 위해 Azure Active Directory(Azure AD) 인증을 사용합니다.

**모범 사례**:

- Azure AD 테넌트를 만들고 사용자를 나타내도록 [사용자를 만들고](../active-directory/fundamentals/add-users-azure-active-directory.md) 앱, 서비스 및 자동화 도구를 나타내는 [서비스 주체를](../active-directory/develop/app-objects-and-service-principals.md) 만듭니다. 서비스 주체는 Windows 및 Linux의 서비스 계정과 동일합니다. 

- 그룹 할당을 통해 Azure AD 보안 주체에 리소스에 대한 액세스 권한을 할당합니다: Azure AD 그룹 만들기, 그룹에 대한 액세스 권한 부여, 그룹에 개별 구성원 추가 데이터베이스에서 Azure AD 그룹을 매핑하는 포함된 데이터베이스 사용자를 만듭니다. 데이터베이스 내부에 권한을 할당하려면 Azure AD 그룹과 연결된 사용자를 적절한 사용 권한이 있는 데이터베이스 역할에 배치합니다.
  - 문서를 참조하고 [SQL을 사용하여 Azure Active Directory 인증을 구성 및 관리하고 SQL을](sql-database-aad-authentication-configure.md) [사용하여 인증하기 위해 Azure AD를 사용합니다.](sql-database-aad-authentication.md)
  > [!NOTE]
  > 관리되는 인스턴스에서 마스터 데이터베이스의 Azure AD 보안 주체에 매핑되는 로그인을 만들 수도 있습니다. [로그인 만들기(거래 SQL)를](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current)참조하십시오.

- Azure AD 그룹을 사용하면 권한 관리와 그룹 소유자가 모두 단순화되며 리소스 소유자는 그룹에 구성원을 추가/제거할 수 있습니다. 

- 각 SQL DB 서버에 대해 Azure AD 관리자에 대한 별도의 그룹을 만듭니다.

  - Azure SQL [Database 서버에 대한 Azure Active Directory 관리자 프로비전](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)을 참조하십시오.

- Azure AD 감사 활동 보고서를 사용하여 Azure AD 그룹 구성원 자격 변경을 모니터링합니다. 

- 관리되는 인스턴스의 경우 Azure AD 관리자를 만들려면 별도의 단계가 필요합니다. 
  - 문서를 참조하고 [관리되는 인스턴스에 대한 Azure Active Directory 관리자 프로비전.](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-managed-instance) 

> [!NOTE]
> - Azure AD 인증은 Azure SQL 감사 로그에 기록되지만 Azure AD 로그인 로그에는 기록되지 않습니다.
> - Azure에서 부여된 RBAC 권한은 Azure SQL DB 사용 권한에는 적용되지 않습니다. 이러한 사용 권한은 기존 SQL 권한을 사용하여 SQL DB에서 수동으로 생성/매핑되어야 합니다.
> - 클라이언트 측에서 Azure AD 인증은 인터넷에 액세스하거나 UDR(사용자 정의 경로)을 통해 VNet에 액세스해야 합니다.
> - Azure AD 액세스 토큰은 클라이언트 측에서 캐시되며 수명은 토큰 구성에 따라 다릅니다. Azure Active [디렉터리에서 구성 가능한 토큰 수명](../active-directory/develop/active-directory-configurable-token-lifetimes.md) 문서 참조
> - Azure AD 인증 문제 해결에 대한 지침은 다음 블로그를 참조하십시오.<https://techcommunity.microsoft.com/t5/azure-sql-database/troubleshooting-problems-related-to-azure-ad-authentication-with/ba-p/1062991>

### <a name="multi-factor-authentication-mfa"></a>MFA(Multi-Factor Authentication)

> 에 언급 된: OSA 연습 #2, ISO 액세스 제어 (AC)

MFA(Azure 다단계 인증)는 두 개 이상의 인증 형식을 요구하여 추가 보안을 제공하는 데 도움이 됩니다.

**구현 방법**:

- 조건부 액세스를 사용하여 Azure AD에서 [MFA를 사용하도록 설정하고](../active-directory/authentication/concept-mfa-howitworks.md) 대화형 인증을 사용합니다. 

- 다른 방법은 전체 Azure AD 또는 AD 도메인에 대해 MFA를 사용하도록 설정하는 것입니다.

**모범 사례**:

- Azure AD에서 조건부 액세스를 활성화합니다(프리미엄 구독 필요). 
  - [Azure AD의 조건부 액세스](../active-directory/conditional-access/overview.md)문서를 참조하십시오.  

- Azure AD 그룹을 만들고 Azure AD 조건부 액세스를 사용하여 선택한 그룹에 대해 MFA 정책을 사용하도록 설정합니다. 
  - [조건부 액세스 배포 계획](../active-directory/conditional-access/plan-conditional-access.md)문서를 참조하십시오. 

- MFA는 전체 Azure AD 또는 Azure AD를 사용하는 전체 Active Directory에 대해 활성화할 수 있습니다. 

- 암호가 대화식으로 요청된 다음 MFA 인증이 있는 SQL DB에 Azure AD 대화형 인증 모드를 사용합니다.      
  - SSMS에서 범용 인증을 사용합니다. Azure SQL [데이터베이스 및 Azure SQL 데이터 웨어하우스(MFA에 대한 SSMS 지원)를 사용하여 다단계 AAD 인증을 사용하는](sql-database-ssms-mfa-authentication.md)문서를 참조하십시오.
  - SQL 서버 데이터 도구(SSDT)에서 지원되는 대화형 인증을 사용합니다. SQL Server [데이터 도구(SSDT)의 Azure Active Directory 지원](https://docs.microsoft.com/sql/ssdt/azure-active-directory?view=azuresqldb-current)문서를 참조하십시오.
  - MFA를 지원하는 다른 SQL 도구를 사용합니다. 
    - 내보내기/추출/배포 데이터베이스에 대한 SSMS 마법사 지원  
    - [sqlpackage.exe](https://docs.microsoft.com/sql/tools/sqlpackage): 옵션 '/ua' 
    - [sqlcmd 유틸리티](https://docs.microsoft.com/sql/tools/sqlcmd-utility): 옵션 -G (대화 형)
    - [숨은 숨바금 유틸리티](https://docs.microsoft.com/sql/tools/bcp-utility): 옵션 -G (대화 형) 

- MFA 지원을 사용한 대화형 인증을 사용하여 Azure SQL 데이터베이스에 연결하려면 응용 프로그램을 구현합니다. 
  - 문서를 참조, [Azure 다단계 인증을 사용하여 Azure SQL 데이터베이스에 연결.](active-directory-interactive-connect-azure-sql-db.md) 
  > [!NOTE]
  > 이 인증 모드에는 사용자 기반 ID가 필요합니다. 개별 Azure AD 사용자 인증을 우회하는 신뢰할 수 있는 ID 모델을 사용하는 경우(예: Azure 리소스에 대해 관리되는 ID 사용) MFA는 적용되지 않습니다.

### <a name="minimize-the-use-of-password-based-authentication-for-users"></a>사용자에 대한 암호 기반 인증 사용 최소화

> 에 언급 된: OSA 연습 #4, ISO 액세스 제어 (AC)

암호 기반 인증 방법은 약한 형태의 인증입니다. 자격 증명이 손상되거나 실수로 부여될 수 있습니다.

**구현 방법**:

- 암호 사용을 제거하는 Azure AD 통합 인증을 사용합니다.

**모범 사례**:

- Windows 자격 증명을 사용하여 단일 사인온 인증을 사용합니다. 온-프레미스 AD 도메인을 Azure AD로 페더레이트하고 통합 Windows 인증(Azure AD가 있는 도메인 조인 컴퓨터의 경우)을 사용합니다.
  - [Azure AD 통합 인증에 대한 SSMS 지원](sql-database-aad-authentication-configure.md#active-directory-integrated-authentication)문서를 참조하십시오.

### <a name="minimize-the-use-of-password-based-authentication-for-applications"></a>응용 프로그램에 대한 암호 기반 인증 사용 최소화 

> 에 언급 된: OSA 연습 #4, ISO 액세스 제어 (AC)

**구현 방법**:

- Azure 관리 ID를 사용하도록 설정합니다. 통합 또는 인증서 기반 인증을 사용할 수도 있습니다. 

**모범 사례**:

- [Azure 리소스에 관리되는 ID를](../active-directory/managed-identities-azure-resources/overview.md)사용합니다.
  - [시스템 할당 관리 ID](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-sql.md) 
  - [사용자 할당 관리 ID](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)
  - [관리되는 ID가 있는 앱 서비스에서 Azure SQL 데이터베이스 사용(코드 변경 없이)](https://github.com/Azure-Samples/app-service-msi-entityframework-dotnet)

- 응용 프로그램에 인증서 기반 인증을 사용합니다. 
  - 이 [코드 샘플을](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/security/azure-active-directory-auth/token)참조하십시오. 

- 통합 페더레이션 된 도메인 및 도메인 조인 된 컴퓨터에 대 한 Azure AD 인증을 사용 합니다 (위의 섹션 참조).
  - 통합 [인증에 대한 샘플 응용 프로그램을](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/security/azure-active-directory-auth/integrated)참조하십시오.

### <a name="protect-passwords-and-secrets"></a>암호 및 비밀 보호

암호를 피할 수 없는 경우 암호가 보호되는지 확인합니다.

**구현 방법**:

- Azure 키 자격 증명 모음을 사용하여 암호 및 암호를 저장합니다. 해당하는 경우 Azure AD 사용자와 함께 Azure SQL 데이터베이스에 MFA를 사용합니다.

**모범 사례**:

- 암호 나 비밀을 피할 수없는 경우 Azure Key Vault에 사용자 암호 및 응용 프로그램 암호를 저장하고 Key Vault 액세스 정책을 통해 액세스를 관리하십시오. 

- 다양한 앱 개발 프레임워크는 앱의 비밀을 보호하기 위한 프레임워크별 메커니즘을 제공할 수도 있습니다. 예를 들어 ASP.NET [핵심 응용 프로그램입니다.](https://docs.microsoft.com/aspnet/core/security/app-secrets?view=aspnetcore-2.1&tabs=windows)

### <a name="use-sql-authentication-for-legacy-applications"></a>레거시 응용 프로그램에 SQL 인증 사용 

SQL 인증은 사용자 이름과 암호를 사용하여 Azure SQL Database에 연결할 때 사용자의 인증을 말합니다. 로그인은 각 SQL Database 서버 또는 관리되는 인스턴스에서 만들어야 하며 각 데이터베이스에서 생성된 사용자여야 합니다.

**구현 방법**:

- SQL 인증을 사용합니다.

**모범 사례**:

- 서버 관리자는 로그인 및 사용자를 만듭니다. 포함된 데이터베이스 사용자를 암호로 사용하지 않는 한 모든 암호는 마스터 데이터베이스에 저장됩니다.
  - SQL 데이터베이스 [및 SQL 데이터 웨어하우스에 대한 데이터베이스 액세스 권한 제어 및 권한 부여](sql-database-manage-logins.md)문서를 참조하십시오.

## <a name="access-management"></a>액세스 관리

액세스 관리(권한 부여라고도 함)는 Azure SQL Database에 대한 권한 있는 사용자의 액세스 및 권한을 제어하고 관리하는 프로세스입니다.

### <a name="implement-principle-of-least-privilege"></a>최소 권한 원칙 구현

> 언급 된: FedRamp 제어 AC-06, NIST: AC-6, OSA 연습 #3

최소 권한 원칙에 따르면 사용자는 작업을 완료하는 데 필요한 것보다 더 많은 권한이 없어야 합니다. 자세한 내용은 문서를 참조 [하십시오 그냥 충분 한 관리](https://docs.microsoft.com/powershell/scripting/learn/remoting/jea/overview).

**구현 방법**:

필요한 작업을 완료하는 데 필요한 [권한만 할당합니다.](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine)

- SQL 데이터베이스에서: 
    - 세분화된 사용 권한 및 사용자 정의 데이터베이스 역할(또는 MI의 서버 역할)을 사용합니다. 
        1. 필요한 역할 만들기
            - [CREATE ROLE](https://docs.microsoft.com/sql/t-sql/statements/create-role-transact-sql)
            - [CREATE SERVER ROLE](https://docs.microsoft.com/sql/t-sql/statements/create-server-role-transact-sql)
        1. 필수 사용자 만들기
            - [사용자 만들기](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql)
        1. 사용자를 역할에 구성원으로 추가 
            - [ALTER ROLE](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql)
            - [서버 역할 변경](https://docs.microsoft.com/sql/t-sql/statements/alter-server-role-transact-sql)
        1. 그런 다음 역할에 권한을 할당합니다. 
            - [GRANT](https://docs.microsoft.com/sql/t-sql/statements/grant-transact-sql) 
    - 불필요한 역할에 사용자를 할당하지 않도록 합니다.

- Azure 리소스 관리자에서:
  - 사용 가능한 경우 기본 제공 역할을 사용하거나 사용자 지정 RBAC 역할을 사용하고 필요한 권한을 할당합니다.
    - [Azure에 대한 기본 제공 역할](../role-based-access-control/built-in-roles.md) 
    - [Azure 리소스에 대한 사용자 지정 역할](../role-based-access-control/custom-roles.md)

**모범 사례**:

다음 모범 사례는 선택 사항이지만 보안 전략의 관리 및 지원 성이 향상됩니다. 

- 가능하면 가능한 최소한의 사용 권한 집합으로 시작하여 실제 필요성(및 정당화)이 있는 경우 권한을 하나씩 추가하기 시작하십시오. 

- 개별 사용자에게 사용 권한을 할당하지 않습니다. 대신 역할(데이터베이스 또는 서버 역할)을 일관되게 사용합니다. 역할은 사용 권한을 보고하고 문제를 해결하는 데 큰 도움이 됩니다. (Azure RBAC는 역할을 통한 권한 할당만 지원합니다.) 

- 필요한 정확한 사용 권한으로 사용자 지정 역할을 만들고 사용합니다. 실제로 사용되는 일반적인 역할: 
  - 보안 배포 
  - 관리자 
  - Developer 
  - 지원 인력 
  - 감사자 
  - 자동화된 프로세스 
  - 최종 사용자 
  
- 역할의 사용 권한이 사용자에게 필요한 사용 권한과 정확히 일치하는 경우에만 기본 제공 역할을 사용합니다. 사용자를 여러 역할에 할당할 수 있습니다. 

- SQL Server 데이터베이스 엔진의 사용 권한은 다음 범위에 적용할 수 있습니다. 범위가 작을수록 부여된 사용 권한의 영향이 작아지다. 
  - Azure SQL Database 서버(마스터 데이터베이스의 특수 역할) 
  - 데이터베이스 
  - 스키마
      - 스키마를 사용하여 데이터베이스 내에서 권한을 부여하는 것이 좋습니다. (참고: [SQL Server용 스키마 디자인: 보안을 염두에 둔 스키마 디자인 권장 사항)](http://andreas-wolter.com/en/schema-design-for-sql-server-recommendations-for-schema-design-with-security-in-mind/)
  - 개체(표, 보기, 절차 등) 
  > [!NOTE]
  > 이 수준은 전체 구현에 불필요한 복잡성을 추가하므로 개체 수준에 사용 권한을 적용하지 않는 것이 좋습니다. 개체 수준 권한을 사용하기로 결정한 경우 이러한 권한을 명확하게 문서화해야 합니다. 같은 이유로 권장되지 않는 열 수준 사용 권한에도 동일하게 적용됩니다. 또한 기본적으로 테이블 수준 [DENY는](https://docs.microsoft.com/sql/t-sql/statements/deny-object-permissions-transact-sql) 열 수준 GRANT를 재정의하지 않습니다. 이렇게 하려면 [일반적인 기준 준수 서버 구성을](https://docs.microsoft.com/sql/database-engine/configure-windows/common-criteria-compliance-enabled-server-configuration-option) 활성화해야 합니다.

- [취약성 평가(VA)를](https://docs.microsoft.com/sql/relational-databases/security/sql-vulnerability-assessment) 사용하여 정기적인 검사를 수행하여 너무 많은 사용 권한을 테스트합니다.

### <a name="implement-separation-of-duties"></a>업무 분리 구현

> 에 언급 된: 페드 램: AC-04, NIST: AC-5, ISO: A.6.1.2, PCI 6.4.2, SOC: CM-3, SDL-3

업무 분리라고도 하는 업무 분리는 중요한 작업을 다른 사용자에게 할당된 여러 작업으로 분할해야 하는 요구 사항을 설명합니다. 업무 분리는 데이터 유출을 방지하는 데 도움이 됩니다.

**구현 방법**:

- 필요한 업무 분리 수준을 식별합니다. 예제: 
  - 개발/테스트 및 프로덕션 환경 간 
  - DBA(데이터베이스 관리자) 관리 수준 작업과 개발자 작업의 보안에 민감한 작업. 
    - 예: 감사자, RLS(역할 수준 보안에 대한 보안 정책 작성), DDL 권한을 사용하여 SQL Database 개체 구현.

- 시스템에 액세스하는 사용자(및 자동화된 프로세스)의 포괄적인 계층 구조를 식별합니다.

- 필요한 사용자 그룹에 따라 역할을 만들고 역할에 권한을 할당합니다. 
  - Azure 포털또는 PowerShell 자동화를 통해 관리 수준 작업의 경우 RBAC 역할을 사용합니다. 요구 사항과 일치하는 기본 제공 역할을 찾거나 사용 가능한 권한을 사용하여 사용자 지정 RBAC 역할을 만듭니다. 
  - 관리되는 인스턴스에서 서버 전체 작업(새 로그인, 데이터베이스 만들기)을 위한 서버 역할을 만듭니다. 
  - 데이터베이스 수준 작업에 대한 데이터베이스 역할을 만듭니다.

- 특정 중요한 작업의 경우 사용자를 대신하여 작업을 실행하기 위해 인증서에 의해 서명된 특수 저장 프로시저를 만드는 것이 좋습니다. 디지털 서명된 저장 프로시저의 한 가지 중요한 장점은 프로시저를 변경하면 이전 버전의 프로시저에 부여된 사용 권한이 즉시 제거된다는 것입니다.
  - 예: [자습서: 인증서를 통해 저장 프로시저 서명](https://docs.microsoft.com/sql/relational-databases/tutorial-signing-stored-procedures-with-a-certificate) 

- Azure Key Vault에서 고객 관리 키를 사용하여 투명 데이터 암호화(TDE)를 구현하여 데이터 소유자와 보안 소유자 간의 업무 분리를 활성화합니다. 
  - Azure [포털에서 Azure 저장소 암호화에 대한 고객 관리 키 구성.](../storage/common/storage-encryption-keys-portal.md) 

- DBA에서 매우 중요한 것으로 간주되고 여전히 DBA 작업을 수행할 수 있는 데이터를 볼 수 없도록 하려면 역할 구분을 사용하여 항상 암호화된 작업을 사용할 수 있습니다. 
  - 문서, [항상 암호화된 키 관리 개요,](https://docs.microsoft.com/sql/relational-databases/security/encryption/overview-of-key-management-for-always-encrypted) [역할 구분을 위한 키 프로비저닝](https://docs.microsoft.com/sql/relational-databases/security/encryption/configure-always-encrypted-keys-using-powershell#KeyProvisionWithRoles)및 [역할 구분을 통해 열 마스터 키 회전](https://docs.microsoft.com/sql/relational-databases/security/encryption/rotate-always-encrypted-keys-using-powershell#column-master-key-rotation-with-role-separation)을 참조하십시오. 

- 항상 암호화된 것을 사용할 수 없거나 적어도 시스템을 거의 사용할 수 없게 만들 수 있는 주요 비용과 노력이 없는 경우 다음과 같은 보상 제어를 사용하여 타협을 시도하고 완화할 수 있습니다. 
  - 프로세스에 대한 인간의 개입. 
  - 감사 추적 - 감사에 대한 자세한 내용은 [중요한 보안 이벤트를](#audit-critical-security-events)참조, 감사합니다.

**모범 사례**:

- 개발/테스트 및 프로덕션 환경에 다른 계정이 사용되었는지 확인합니다. 다른 계정은 테스트 & 생산 시스템의 분리를 준수하는 데 도움이됩니다.

- 개별 사용자에게 사용 권한을 할당하지 않습니다. 대신 역할(데이터베이스 또는 서버 역할)을 일관되게 사용합니다. 역할을 갖는 것은 사용 권한을 보고하고 문제를 해결하는 데 큰 도움이 됩니다.

- 사용 권한이 필요한 사용 권한과 정확히 일치하는 경우 기본 제공 역할을 사용하십시오. 

- 기본 제공 역할이 너무 많은 사용 권한을 부여하거나 권한이 부족할 때 사용자 정의 역할을 만들고 사용합니다. 

- 역할 할당은 T-SQL의 SQL 에이전트 작업 단계 내에서 또는 RBAC 역할에 Azure PIM을 사용하여 DSD(업무의 동적 분리)라고도 하는 임시로 수행할 수도 있습니다. 

- DBA가 암호화 키 또는 키 저장소에 액세스할 수 없고 키에 액세스할 수 있는 보안 관리자가 데이터베이스에 액세스할 수 없도록 합니다. [확장 가능한 키 관리(EKM)를](https://docs.microsoft.com/sql/relational-databases/security/encryption/extensible-key-management-ekm) 사용하면 이러한 분리를 더 쉽게 달성할 수 있습니다. [Azure 키 볼트는](https://azure.microsoft.com/services/key-vault/) EKM을 구현하는 데 사용할 수 있습니다. 

- 항상 보안 관련 작업에 대한 감사 추적이 있어야 합니다. 

- 기본 제공 RBAC 역할의 정의를 검색하여 사용된 사용 권한을 확인하고 PowerShell을 통해 이러한 역할의 발췌 및 누적을 기반으로 사용자 지정 역할을 만들 수 있습니다.

- db_owner 데이터베이스 역할의 모든 구성원이 TDE(투명 데이터 암호화)와 같은 보안 설정을 변경하거나 SLO를 변경할 수 있으므로 이 구성원은 주의해서 부여해야 합니다. 그러나 db_owner 권한이 필요한 많은 작업이 있습니다. DB 옵션 변경과 같은 데이터베이스 설정을 변경하는 작업입니다. 감사는 모든 솔루션에서 중요한 역할을 합니다.

- db_owner 권한을 제한할 수 없으므로 관리 계정이 사용자 데이터를 볼 수 없습니다. 데이터베이스에 매우 중요한 데이터가 있는 경우 항상 암호화되어 db_owners 또는 다른 DBA가 데이터베이스를 볼 수 없도록 안전하게 사용할 수 있습니다.

> [!NOTE]
> SoD(업무 분리)를 달성하는 것은 보안 관련 또는 문제 해결 작업에 어려움을 겪고 있습니다. 개발 및 최종 사용자 역할과 같은 다른 영역은 분리하기가 더 쉽습니다. 대부분의 규정 준수 관련 컨트롤을 사용하면 다른 솔루션이 실용적이지 않을 때 감사와 같은 대체 제어 기능을 사용할 수 있습니다.

SoD에 대해 자세히 알아보려는 독자는 다음 리소스를 권장합니다. 

- Azure SQL 데이터베이스의 경우.  
  - [SQL Database 및 SQL Data Warehouse에 대한 액세스 제어 및 권한 부여](sql-database-manage-logins.md)
  - [응용 프로그램 개발자를 위한 엔진 분리 업무](https://docs.microsoft.com/previous-versions/sql/sql-server-2008/cc974525(v=sql.100)) 
  - [SQL Server 2014에서 업무 분리](https://www.microsoft.com/download/details.aspx?id=39269)
  - [SQL Server에서 저장 프로시저에 서명](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/signing-stored-procedures-in-sql-server)

- Azure 리소스 관리의 경우:
  - [Azure에 대한 기본 제공 역할](../role-based-access-control/built-in-roles.md) 
  - [Azure 리소스에 대한 사용자 지정 역할](../role-based-access-control/custom-roles.md)
  - [Azure AD 권한 있는 ID 관리를 사용하여 높은 액세스](https://www.microsoft.com/en-us/itshowcase/using-azure-ad-privileged-identity-management-for-elevated-access)

### <a name="perform-regular-code-reviews"></a>정기적인 코드 검토 수행

> 에 언급 된: PCI: 6.3.2, SOC: SDL-3 

업무 분리는 데이터베이스의 데이터에 국한되지 않고 응용 프로그램 코드를 포함합니다. 악의적인 코드는 잠재적으로 보안 제어를 우회할 수 있습니다. 프로덕션에 사용자 지정 코드를 배포하기 전에 배포되는 코드를 검토해야 합니다.

**구현 방법**:

- 소스 제어를 지원하는 Azure Data Studio와 같은 데이터베이스 도구를 사용합니다. 

- 분리된 코드 배포 프로세스를 구현합니다.

- 주 지사에 커밋하기 전에 코드 자체의 작성자가 아닌 사람(코드 자체의 작성자 제외)은 사기 및 불량 액세스로부터 보호하기 위해 악의적인 데이터 수정뿐만 아니라 잠재적인 권한 상승 위험에 대한 코드를 검사해야 합니다. 소스 제어 메커니즘을 사용하여 이 작업을 수행할 수 있습니다.

**모범 사례**:

- 표준화: 코드 업데이트에 대해 따라야 할 표준 절차를 구현하는 데 도움이 됩니다. 

- 취약성 평가에는 과도한 권한, 이전 암호화 알고리즘 사용 및 데이터베이스 스키마 내의 기타 보안 문제를 확인하는 규칙이 포함되어 있습니다. 

- SQL 주입에 취약한 코드를 검사하는 고급 위협 보호를 사용하여 QA 또는 테스트 환경에서 추가 검사를 수행할 수 있습니다.

- 알아서 해야 할 사항의 예: 
  - 자동화된 SQL 코드 업데이트 배포 내에서 사용자를 만들거나 보안 설정을 변경합니다. 
  - 제공된 매개 변수에 따라 셀의 금전적 가치를 일치하지 않는 방식으로 업데이트하는 저장 프로시저입니다. 

- 검토를 수행하는 사람이 원래 코드 작성자가 아닌 개인이며 코드 검토 및 보안 코딩에 대해 잘 알고 있어야 합니다.

- 코드 변경의 모든 소스를 알고 있어야 합니다. 코드는 T-SQL 스크립트에 있을 수 있습니다. 뷰, 함수, 트리거 및 저장 프로시저의 형태로 실행되거나 배포될 임시 명령일 수 있습니다. SQL 에이전트 작업 정의(단계)의 일부일 수 있습니다. 또한 SSIS 패키지, Azure 데이터 팩터리 및 기타 서비스 내에서 실행할 수도 있습니다.

## <a name="data-protection"></a>데이터 보호

데이터 보호는 암호화 또는 난독화로 인해 중요한 정보를 손상으로부터 보호하는 기능 집합입니다.

> [!NOTE]
> Microsoft는 Azure SQL Database에서 FIPS 140-2 수준 1을 준수하고 있음을 증명합니다. 이는 FIPS 140-2 레벨 1 허용 가능한 알고리즘과 FIPS 140-2 수준 1의 엄격한 사용을 확인한 후 필요한 키 길이, 키 관리, 키 생성 및 키 저장소와의 일관성을 포함하여 해당 알고리즘의 검증된 인스턴스를 확인한 후에 수행됩니다. 이 증명은 고객이 FIPS 140-2 Level 1의 데이터 처리 또는 시스템 또는 응용 프로그램 전달시 검증된 인스턴스의 사용에 대한 요구 또는 요구 사항에 응답할 수 있도록 하기 위한 것입니다. 우리는 "FIPS 140-2 레벨 1 준수"와 "FIPS 140-2 레벨 1 준수"라는 용어를 정의하여 "FIPS 140-2 레벨 1 검증"이라는 다른 용어의 미국 및 캐나다 정부에 의도된 적용 가능성을 입증하기 위해 사용되었습니다. 


### <a name="encrypt-data-in-transit"></a>전송 중 데이터 암호화

> 에서 언급: OSA 연습 #6, ISO 제어 가족: 암호화

클라이언트와 서버 간에 데이터가 이동하는 동안 데이터를 보호합니다. 네트워크 [보안을](#network-security)참조하십시오.

### <a name="encrypt-data-at-rest"></a>미사용 암호화 데이터

> 에서 언급: OSA 연습 #6, ISO 제어 가족: 암호화

미사용 암호화는 데이터베이스, 로그 및 백업 파일에 유지될 때 데이터의 암호화 보호입니다.

**구현 방법**:

- 서비스 관리 키가 있는 [투명 데이터베이스 암호화(TDE)는](transparent-data-encryption-azure-sql.md) Azure SQL Database에서 2017년 이후에 생성된 모든 데이터베이스에 대해 기본적으로 활성화됩니다.
- 관리되는 인스턴스에서 온-프레미스 서버를 사용 하 여 복원 작업에서 데이터베이스를 만든 경우 원래 데이터베이스의 TDE 설정이 사용 됩니다. 원래 데이터베이스에 TDE를 사용하도록 설정하지 않은 경우 관리되는 인스턴스에 대해 TDE를 수동으로 켜는 것이 좋습니다.

**모범 사례**:

- 미사용 암호화가 필요한 데이터를 마스터 데이터베이스에 저장하지 마십시오. 마스터 데이터베이스는 TDE로 암호화할 수 없습니다.

- TDE 보호에 대한 투명성과 세부적인 제어가 필요한 경우 Azure 키 자격 증명 모음에서 고객 관리 키를 사용합니다. Azure Key Vault를 사용하면 언제든지 권한을 취소하여 데이터베이스에 액세스할 수 없게 만들 수 있습니다. 다른 키와 함께 TDE 프로텍터를 중앙에서 관리하거나 Azure 키 자격 증명 모음을 사용하여 자신의 일정에 따라 TDE 프로텍터를 회전할 수 있습니다.

- Azure Key Vault에서 고객 관리 키를 사용하는 경우 문서, [Azure 키 자격 증명 모음으로 TDE를 구성하기 위한 지침](transparent-data-encryption-byok-azure-sql.md#recommendations-when-configuring-akv) 및 Azure 키 자격 증명 [모음으로 지리적 DR을 구성하는 방법을](transparent-data-encryption-byok-azure-sql.md#geo-dr-and-customer-managed-tde)따릅니다.

### <a name="protect-sensitive-data-in-use-from-high-privileged-unauthorized-users"></a>권한이 높은 권한이 없는 사용자로부터 사용 중의 중요한 데이터 보호

사용 중 데이터는 SQL 쿼리를 실행하는 동안 데이터베이스 시스템의 메모리에 저장된 데이터입니다. 데이터베이스에 중요한 데이터가 저장되는 경우 권한이 높은 사용자가 데이터베이스에서 중요한 데이터를 볼 수 없도록 조직에서 필요할 수 있습니다. 조직의 Microsoft 운영자 나 DBA와 같은 권한이 높은 사용자는 데이터베이스를 관리할 수 있어야 하지만 SQL Server 프로세스의 메모리에서 중요한 데이터를 보거나 잠재적으로 유출하거나 데이터베이스를 쿼리하여 볼 수 없습니다.

중요한 데이터와 중요한 데이터를 메모리에서 암호화하고 관리자가 일반 텍스트로 액세스할 수 없도록 하는 정책은 준수해야 하는 조직 및 규정 준수 규정과 관련이 있습니다. 관련 요구 사항: [중요한 데이터를 식별하고 태그를 붙입니다.](#identify-and-tag-sensitive-data)

**구현 방법**:

- [항상 암호화를](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) 사용하여 중요한 데이터가 메모리/사용 중에서도 Azure SQL Database의 일반 텍스트에 노출되지 않도록 합니다. 항상 암호화된 데이터베이스 관리자(DBA) 및 클라우드 관리자(또는 권한이 높지만 권한이 없는 사용자를 가장할 수 있는 악의적인 행위자)로부터 데이터를 보호하고 데이터에 액세스할 수 있는 사용자를 보다 세한 제어할 수 있습니다.

**모범 사례**:

- 항상 암호화된 데이터는 미사용(TDE) 또는 전송 중(SSL/TLS)의 데이터를 암호화하는 대신 사용되지 않습니다. 성능 및 기능에 미치는 영향을 최소화하기 위해 중요하지 않은 데이터에는 항상 암호화된 데이터를 사용해서는 안 됩니다. TDE 및 전송 계층 보안(TLS)과 함께 항상 암호화된 경우 미사용, 전송 중 및 사용 중 데이터를 포괄적으로 보호하는 것이 좋습니다. 

- 프로덕션 데이터베이스에 Always Encrypted을 배포하기 전에 식별된 중요한 데이터 열을 암호화하는 것이 미치는 영향을 평가합니다. 일반적으로 항상 암호화된 것은 암호화된 열에 대한 쿼리 기능을 줄이고 [항상 암호화된 기능 세부 정보](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine#feature-details)- 기능 세부 정보에 나열된 다른 제한 사항이 있습니다. 따라서 기능을 다시 구현하기 위해 응용 프로그램을 다시 설계해야 할 수도 있고, 쿼리가 클라이언트 측에서 지원하지 않거나, 저장된 프로시저, 함수, 뷰 및 트리거의 정의를 포함하여 데이터베이스 스키마를 리팩터링해야 할 수 있습니다. 기존 응용 프로그램은 항상 암호화된 열의 제한 사항 및 제한을 준수하지 않으면 암호화된 열에서 작동하지 않을 수 있습니다. 항상 암호화된 것을 지원하는 Microsoft 도구, 제품 및 서비스의 에코시스템은 증가하고 있지만, 그 중 상당수는 암호화된 열로 는 작동하지 않습니다. 열을 암호화하면 워크로드의 특성에 따라 쿼리 성능에도 영향을 미칠 수 있습니다. 

- 악의적인 DBA로부터 데이터를 보호하기 위해 항상 암호화된 키를 사용하는 경우 역할 구분을 사용하여 항상 암호화된 키를 관리합니다. 역할 구분을 사용하면 보안 관리자가 실제 키를 만듭니다. DBA는 데이터베이스에서 실제 키를 설명하는 열 마스터 키 및 열 암호화 키 메타데이터 개체를 만듭니다. 이 과정에서 보안 관리자는 데이터베이스에 액세스할 필요가 없으며 DBA는 일반 텍스트의 실제 키에 액세스할 필요가 없습니다. 
  - 자세한 내용은 [역할 구분을 통해 키 관리](https://docs.microsoft.com/sql/relational-databases/security/encryption/overview-of-key-management-for-always-encrypted#managing-keys-with-role-separation) 문서를 참조하십시오. 

- 쉽게 관리할 수 있는 Azure 키 볼트에 열 마스터 키를 저장합니다. 키 관리를 어렵게 만드는 Windows 인증서 저장소(일반적으로 분산키 저장소 솔루션(중앙 키 관리 솔루션과 는 반대)를 사용하지 마십시오. 

- 여러 키(열 마스터 키 또는 열 암호화 키)를 사용하는 장단점을 신중하게 생각해 보십시오. 키 관리 비용을 줄이기 위해 키 수를 작게 유지합니다. 일반적으로 데이터베이스당 하나의 열 마스터 키와 하나의 열 암호화 키는 키 회전이 아닌 정상 상태 환경에서 충분합니다. 서로 다른 사용자 그룹이 있는 경우 각각 다른 키를 사용하고 다른 데이터에 액세스하는 경우 추가 키가 필요할 수 있습니다.  

- 규정 준수 요구 사항에 따라 열 마스터 키를 회전합니다. 또한 열 암호화 키를 회전해야 하는 경우 온라인 암호화를 사용하여 응용 프로그램 가동 중지 시간을 최소화하는 것이 좋습니다. 
  - 문서, [성능 및 가용성 고려 사항을](https://docs.microsoft.com/sql/relational-databases/security/encryption/configure-column-encryption-using-powershell#performance-and-availability-considerations)참조하십시오. 

- 데이터에 대한 계산(같음)을 지원해야 하는 경우 결정적 암호화를 사용합니다. 그렇지 않으면 무작위 암호화를 사용합니다. 낮은 엔트로피 데이터 집합 또는 공개적으로 알려진 배포가 있는 데이터 집합에 대해 결정적 암호화를 사용하지 마십시오. 

- 사용자의 동의 없이 합법적으로 데이터에 액세스하는 타사에 대해 우려되는 경우 일반 텍스트의 키와 데이터에 액세스할 수 있는 모든 응용 프로그램 및 도구가 Microsoft Azure Cloud 외부에서 실행되도록 해야 합니다. 키에 액세스하지 않으면 제3자도 암호화를 우회하지 않는 한 데이터를 해독할 수 없습니다.

- 항상 암호화된 키(및 보호된 데이터)에 대한 임시 액세스 권한을 부여하는 것을 쉽게 지원하지 않습니다. 예를 들어 DBA가 중요하고 암호화된 데이터에 대한 일부 정리 작업을 수행할 수 있도록 DBA와 키를 공유해야 하는 경우를 예로 들 수 있습니다. 안정성을 취소하는 유일한 방법은 DBA에서 데이터에 대한 액세스를 취소하는 유일한 방법은 비용이 많이 드는 작업인 데이터를 보호하는 열 암호화 키와 열 마스터 키를 모두 회전하는 것입니다. 

- 암호화된 열의 일반 텍스트 값에 액세스하려면 CMK를 보유한 키 저장소에 구성된 열을 보호하는 CMK(열 마스터 키)에 액세스할 수 있어야 합니다. 또한 사용자는 **모든 열 마스터 키 정의 보기를** 가지고 있고 열 암호화 키 정의 데이터베이스 권한을 **볼** 수 있어야 합니다.

### <a name="control-access-of-application-users-to-sensitive-data-through-encryption"></a>암호화를 통해 중요한 데이터에 대한 응용 프로그램 사용자의 액세스 제어

암호화는 암호화 키에 액세스할 수 있는 특정 응용 프로그램 사용자만 데이터를 보거나 업데이트할 수 있도록 하는 방법으로 사용할 수 있습니다.

**구현 방법**:

- 셀 수준 암호화(CLE)를 사용합니다. 자세한 내용은 [문서, 데이터 열 암호화를](https://docs.microsoft.com/sql/relational-databases/security/encryption/encrypt-a-column-of-data) 참조하십시오. 
- 항상 암호화된 암호화를 사용하지만 제한 사항에 유의하십시오. 제한 사항은 다음과 같습니다.

**모범 사례**

CLE를 사용하는 경우:

- SQL 권한 및 역할을 통해 키에 대한 액세스를 제어합니다. 

- 데이터 암호화에 AES(AES 256 권장)를 사용합니다. RC4, DES 및 TripleDES와 같은 알고리즘은 더 이상 사용되지 않으므로 알려진 취약점으로 인해 사용해서는 안 됩니다. 

- 3DES를 사용하지 않도록 비대칭 키/인증서(암호가 아님)로 대칭 키를 보호합니다. 

- 내보내기/가져오기(bacpac 파일)를 통해 셀 수준 암호화를 사용하여 데이터베이스를 마이그레이션할 때는 주의해야 합니다. 
  - Azure SQL [Database에서 셀 수준 암호화를 사용하는](https://blogs.msdn.microsoft.com/sqlsecurity/2015/05/12/recommendations-for-using-cell-level-encryption-in-azure-sql-database/) 방법에 대한 도움말, 데이터를 마이그레이션할 때 키 손실을 방지하는 방법 및 기타 모범 사례 지침을 참조하십시오.

항상 암호화는 주로 Azure SQL Database(클라우드 연산자, DBA)의 권한이 높은 사용자로부터 사용 중인 데이터를 보호하기 위해 설계되었습니다. [Protect sensitive data in use from high-privileged, unauthorized users](#protect-sensitive-data-in-use-from-high-privileged-unauthorized-users) 응용 프로그램 사용자로부터 데이터를 보호하기 위해 항상 암호화를 사용할 때 다음 문제를 알고 있어야 합니다.

- 기본적으로 항상 암호화된 항상 암호화를 지원하는 모든 Microsoft 클라이언트 드라이버는 열 암호화 키의 전역(응용 프로그램당 하나씩) 캐시를 유지 관리합니다. 클라이언트 드라이버가 열 마스터 키를 보유한 키 저장소에 연락하여 일반 텍스트 열 암호화 키를 획득하면 일반 텍스트 열 암호화 키가 캐시됩니다. 따라서 다중 사용자 응용 프로그램의 사용자로부터 데이터를 격리하기가 어렵습니다. 응용 프로그램이 키 저장소(예: Azure Key Vault)와 상호 작용할 때 최종 사용자를 가장하는 경우 사용자의 쿼리가 열 암호화 키로 캐시를 채웁니다. 드라이버는 키 저장소를 호출하지 않으며 두 번째 사용자가 열 암호화 키에 액세스할 수 있는 권한이 있는지 확인하지 않습니다. 따라서 사용자가 키에 액세스할 수 없는 경우에도 암호화된 데이터를 볼 수 있습니다. 다중 사용자 응용 프로그램 내에서 사용자를 격리하려면 열 암호화 키 캐싱을 사용하지 않도록 설정할 수 있습니다. 캐싱을 사용하지 않도록 설정하면 드라이버가 각 데이터 암호화 또는 암호 해독 작업에 대해 키 저장소에 문의해야 하므로 추가 성능 오버헤드가 발생합니다.

### <a name="protect-data-against-unauthorized-viewing-by-application-users-while-preserving-data-format"></a>데이터 형식을 유지하면서 응용 프로그램 사용자가 무단으로 보는 것을 방지합니다.
권한이 없는 사용자가 데이터를 볼 수 없도록 하는 또 다른 방법은 데이터 형식과 형식을 유지하면서 데이터를 난독 처리하거나 마스킹하여 사용자 응용 프로그램이 데이터를 계속 처리하고 표시할 수 있도록 하는 것입니다.

**구현 방법**:

- [동적 데이터 마스킹을](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking) 사용하여 테이블 열을 난독 처리합니다.

> [!NOTE]
> 항상 암호화된 동적 데이터 마스킹은 작동하지 않습니다. 동적 데이터 마스킹을 통해 앱 사용자의 데이터를 마스킹하는 것과 사용 중인 데이터 보호의 우선 순위를 지정해야 하는 동일한 열을 암호화하고 마스킹할 수 없습니다.

**모범 사례**:

> [!NOTE]
> 동적 데이터 마스킹은 권한이 높은 사용자로부터 데이터를 보호하는 데 사용할 수 없습니다. 마스킹 정책은 db_owner 같은 관리 액세스 권한이 있는 사용자에게는 적용되지 않습니다.

- 동적 데이터 마스킹을 해결할 수 있기 때문에 앱 사용자가 임시 쿼리를 실행하도록 허용하지 마십시오.  
  - 자세한 내용은 [추론 또는 무차별 암호 대입 기술을 사용하여 마스킹을 우회하는](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking#security-note-bypassing-masking-using-inference-or-brute-force-techniques) 문서를 참조하십시오.  

- SQL 사용 권한, 역할, RLS를 통해 적절한 액세스 제어 정책을 사용하여 마스크된 열에서 업데이트할 수 있는 사용자 권한을 제한합니다. 열에 마스크를 만들면 해당 열에 대한 업데이트가 방지되지 않습니다. 마스크된 열을 쿼리할 때 마스크된 데이터를 수신하는 사용자는 쓰기 권한이 있는 경우 데이터를 업데이트할 수 있습니다.    

-  동적 데이터 마스킹은 마스크된 값의 통계 적 속성을 유지하지 않습니다. 이는 쿼리 결과(예: 필터링 조건자 또는 마스크된 데이터에 대한 조인을 포함하는 쿼리)에 영향을 미칠 수 있습니다.

## <a name="network-security"></a>네트워크 보안
네트워크 보안은 Azure SQL Database로 전송되는 데이터를 보호하기 위한 액세스 제어 및 모범 사례를 말합니다.

### <a name="configure-my-client-to-connect-securely-to-azure-sql-database"></a>Azure SQL 데이터베이스에 안전하게 연결하도록 클라이언트 구성 
잘 알려진 취약점이 있는 클라이언트 컴퓨터 및 응용 프로그램(예: 이전 TLS 프로토콜 및 암호 제품군 사용)이 Azure SQL Database에 연결되지 않도록 하는 방법에 대한 모범 사례입니다.

**구현 방법**:

- Azure SQL 데이터베이스에 연결하는 클라이언트 컴퓨터가 [TLS(전송 계층 보안)를](sql-database-security-overview.md#transport-layer-security-tls-encryption-in-transit)사용하고 있는지 확인합니다.

**모범 사례**:

- 암호화를 사용하도록 설정하여 SQL DB에 연결하도록 모든 앱 및 도구를 구성합니다. 
  - 암호화 = 켜기, TrustServer인증서 = 꺼져(또는 Microsoft 이외의 드라이버와 동일). 

- 앱에서 TLS를 지원하지 않거나 이전 버전의 TLS를 지원하는 드라이버를 사용하는 경우 가능하면 드라이버를 교체합니다. 가능하지 않은 경우 보안 위험을 신중하게 평가합니다. 

- [TLS(전송 계층 보안) 레지스트리 설정당](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings#tls-10)Azure SQL 데이터베이스에 연결하는 클라이언트 컴퓨터에서 SSL 2.0, SSL 3.0, TLS 1.0 및 TLS 1.1의 취약점을 통해 공격 벡터를 줄입니다. 

- 클라이언트에서 사용할 수 있는 암호 제품군 확인: [TLS/SSL(Schannel SSP)의 암호 스위트.](https://docs.microsoft.com/windows/desktop/SecAuthN/cipher-suites-in-schannel) 특히 [TLS 암호 모음 순서 구성당 3DES를](https://docs.microsoft.com/windows-server/security/tls/manage-tls#configuring-tls-cipher-suite-order)사용하지 않도록 설정합니다. 

- Azure SQL Database의 경우 프록시 및 리디렉션 연결 유형 모두에 대해 암호화가 적용됩니다. 관리되는 인스턴스를 사용하는 경우 서버 측에서 암호화를 적용하므로 **프록시** 연결 유형(기본값)을 사용합니다. **리디렉션** 연결 유형은 현재 암호화 적용을 지원하지 않으며 개인 IP 연결에서만 사용할 수 있습니다. 

- 자세한 내용은 [Azure SQL 연결 아키텍처 - 연결 정책을](sql-database-connectivity-architecture.md#connection-policy)참조하십시오.


### <a name="minimize-attack-surface"></a>공격 표면 최소화
악의적인 사용자가 공격할 수 있는 기능 의 수를 최소화합니다. Azure SQL 데이터베이스에 대한 네트워크 액세스 제어를 구현합니다.

> 에 언급 된: OSA 연습 #5

**구현 방법**:

Azure SQL Database 서버(단일 데이터베이스 또는 탄력적 풀 포함):
- Azure 서비스에 대한 액세스 허용을 OFF로 설정합니다.

- VNet 서비스 끝점 및 VNet 방화벽 규칙을 사용합니다.

- 비공개 링크(미리 보기)를 사용합니다.

관리되는 인스턴스에서:
- 네트워크 요구 [사항의](sql-database-managed-instance-connectivity-architecture.md#network-requirements)지침을 따릅니다. 

**모범 사례**:

- 개인 끝점에 연결하여 Azure SQL Database에 대한 액세스를 제한합니다(예: 개인 데이터 경로 사용): 
  - 관리되는 인스턴스는 외부 액세스를 방지하기 위해 VNet 내에서 격리될 수 있습니다. 동일한 리전에서 동일하거나 피어링된 VNet에 있는 응용 프로그램 및 도구는 직접 액세스할 수 있습니다. 서로 다른 지역에 있는 응용 프로그램 및 도구는 VNet-VNet 연결 또는 ExpressRoute 회로 피어링을 사용하여 연결을 설정할 수 있습니다. 고객은 NSG(네트워크 보안 그룹)를 사용하여 관리되는 인스턴스에 대한 액세스가 필요한 리소스에대해서만 포트 1433에 대한 액세스를 제한해야 합니다. 
  - 단일 데이터베이스 또는 탄력적 풀을 포함하는 SQL Database 서버의 경우 VNet 내의 SQL Database 서버에 전용 개인 IP를 제공하는 [개인 링크](sql-database-private-endpoint-overview.md) 기능을 사용합니다. 또한 [VNet 방화벽 규칙과 함께 VNet 서비스 끝점을](sql-database-vnet-service-endpoint-rule-overview.md) 사용하여 SQL Database 서버에 대한 액세스를 제한할 수도 있습니다.
  - 모바일 사용자는 지점 간 VPN 연결을 사용하여 데이터 경로를 통해 연결해야 합니다.
  - 온-프레미스 네트워크에 연결된 사용자는 사이트 간 VPN 연결 또는 ExpressRoute를 사용하여 데이터 경로를 통해 연결해야 합니다.

- 공용 끝점에 연결하여 Azure SQL Database에 액세스할 수 있습니다(예: 공용 데이터 경로 사용). 다음 모범 사례를 고려해야 합니다. 
  - SQL Database 서버의 경우 [IP 방화벽 규칙을](sql-database-firewall-configure.md) 사용하여 승인된 IP 주소에만 대한 액세스를 제한합니다.
  - 관리되는 인스턴스의 경우 NSG(네트워크 보안 그룹)를 사용하여 포트 3342에 대한 액세스를 필요한 리소스로만 제한합니다. 자세한 내용은 [공용 끝점에서 Azure SQL Database 관리 인스턴스를 안전하게](sql-database-managed-instance-public-endpoint-securely.md)사용참조하세요. 

> [!NOTE]
> 관리되는 인스턴스 공용 끝점은 기본적으로 활성화되지 않으며 명시적으로 활성화되어야 합니다. 회사 정책에서 공용 끝점 사용을 허용하지 않으면 [Azure 정책을](../governance/policy/overview.md) 사용하여 처음에 공용 끝점을 사용하도록 설정하지 못하도록 합니다.

- Azure 네트워킹 구성 요소 설정: 
  - [네트워크 보안에 대한 Azure 모범 사례를](../security/fundamentals/network-best-practices.md)따릅니다.
  - Azure 가상 네트워크에서 설명하는 모범 사례에 따라 VNet(가상 네트워크) 구성을 자주 [묻는 질문(FAQ)](../virtual-network/virtual-networks-faq.md) 및 계획에 따라 계획합니다. 
  - VNet을 여러 서브넷으로 분할하고 동일한 서브넷(예: 프런트 엔드 대 백 엔드 리소스)과 유사한 역할에 대한 리소스를 할당합니다.
  - [NSG(네트워크 보안 그룹)를](../virtual-network/security-overview.md) 사용하여 Azure VNet 경계 내의 서브넷 간의 트래픽을 제어합니다.
  - 구독에 대해 [Azure Network 감시기를](../network-watcher/network-watcher-monitoring-overview.md) 사용하여 인바운드 및 아웃바운드 네트워크 트래픽을 모니터링합니다.

### <a name="configure-power-bi-for-secure-connections-to-azure-sql-database"></a>Azure SQL 데이터베이스에 대한 보안 연결을 위해 전원 BI 구성

**모범 사례**:

- Power BI 데스크톱의 경우 가능하면 개인 데이터 경로를 사용합니다. 

- [TLS(전송 계층 보안)](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings) 레지스트리 설정에 따라 클라이언트 컴퓨터에서 레지스트리 키를 설정하여 Power BI Desktop이 TLS1.2를 사용하여 연결되고 있는지 확인합니다. 

- Power BI를 사용하여 [RLS(행 수준 보안)를](https://docs.microsoft.com/power-bi/service-admin-rls)통해 특정 사용자에 대한 데이터 액세스를 제한합니다. 

- Power BI 서비스의 경우 [온-프레미스 데이터 게이트웨이를](https://docs.microsoft.com/power-bi/service-gateway-onprem)사용하여 제한 사항 및 고려 사항을 염두에 두어야 [합니다.](https://docs.microsoft.com/power-bi/service-gateway-deployment-guidance#installation-considerations-for-the-on-premises-data-gateway)

### <a name="configure-app-service-for-secure-connections-to-azure-sql-database"></a>Azure SQL 데이터베이스에 대한 보안 연결을 위한 앱 서비스 구성

**모범 사례**:

- 간단한 웹 앱의 경우 공용 끝점을 통해 연결하려면 **Azure 서비스 허용을** ON으로 설정해야 합니다. 

- 관리되는 인스턴스에 대한 개인 데이터 경로 연결을 위해 [앱을 Azure 가상 네트워크와 통합합니다.](../app-service/web-sites-integrate-with-vnet.md) 선택적으로 [ASE(앱 서비스 환경)가](../app-service/environment/intro.md)있는 웹 앱을 배포할 수도 있습니다. 

- SQL Database 서버의 데이터베이스에 연결하는 ASE 또는 VNet 통합 웹 앱이 있는 웹 앱의 경우 [VNet 서비스 끝점 및 VNet 방화벽 규칙을](sql-database-vnet-service-endpoint-rule-overview.md) 사용하여 특정 VNet 및 서브넷의 액세스를 제한할 수 있습니다. 그런 다음 **Azure 서비스 허용을** 꺼로 설정합니다. 개인 데이터 경로를 통해 관리되는 인스턴스에 ASE를 연결할 수도 있습니다.  

- Azure 앱 [서비스를 사용하여 PaaS 웹 및 모바일 응용 프로그램을 보호하는 모범 사례인](../security/security-paas-applications-using-app-services.md)문서에 따라 웹 앱이 구성되었는지 확인합니다. 

- [WAF(웹 응용 프로그램 방화벽)를](../application-gateway/waf-overview.md) 설치하여 일반적인 악용 및 취약점으로부터 웹 앱을 보호합니다.

### <a name="configure-azure-vm-hosting-for-secure-connections-to-azure-sql-database"></a>Azure SQL 데이터베이스에 대한 보안 연결을 위해 Azure VM 호스팅 구성

**모범 사례**:

- Azure VM의 NSG에서 허용 및 거부 규칙의 조합을 사용하여 VM에서 액세스할 수 있는 지역을 제어합니다.

- [Azure의 IaaS 워크로드에 대한 보안 모범 사례인 아티클에](../security/azure-security-iaas.md)따라 VM이 구성되었는지 확인합니다.

- 모든 VM이 특정 VNet 및 서브넷과 연결되어 있는지 확인합니다. 

- [강제 터널링에 대한](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md#about-forced-tunneling)지침에 따라 기본 경로 0.0.0.0/인터넷이 필요한지 평가합니다. 
  - 예(예)인 경우 프런트 엔드 서브넷은 기본 경로를 유지합니다.
  - 중간 계층 또는 백 엔드 서브넷과 같은 경우 인터넷을 통해 온-프레미스(크로스-프레미스라고도 있음)에 도달하지 않도록 강제 터널링을 사용하도록 설정합니다. 

- 피어링을 사용하거나 온-프레미스에 연결하는 경우 [선택적 기본 경로를](../virtual-network/virtual-networks-udr-overview.md#optional-default-routes) 구현합니다. 

- 패킷 검사를 위해 VNet의 모든 트래픽을 네트워크 가상 어플라이언스로 보내야 하는 경우 [사용자 정의 경로를](../virtual-network/virtual-networks-udr-overview.md#user-defined) 구현합니다. 

- Azure 백본 네트워크를 통해 Azure 저장소와 같은 PaaS 서비스에 대한 보안 액세스를 위해 [VNet 서비스 끝점을](sql-database-vnet-service-endpoint-rule-overview.md) 사용합니다. 

### <a name="protect-against-distributed-denial-of-service-ddos-attacks"></a>DDoS(분산 서비스 거부) 공격으로부터 보호
DDoS(분산 서비스 거부) 공격은 악의적인 사용자가 Azure 인프라를 압도하고 유효한 로그인 및 워크로드를 거부하도록 하기 위해 네트워크 트래픽의 홍수를 Azure SQL Database로 보내려는 시도입니다.

> 에서 언급: OSA 연습 #9

**구현 방법**:

DDoS 보호는 Azure 플랫폼의 일부로 자동으로 활성화됩니다. 여기에는 항상 켜져 있는 트래픽 모니터링과 공용 엔드포인트에 대한 네트워크 수준 공격의 실시간 완화가 포함됩니다. 

- [Azure DDoS 보호를](../virtual-network/ddos-protection-overview.md) 사용하여 VNet에 배포된 리소스와 연결된 공용 IP 주소를 모니터링합니다.

- [Azure SQL 데이터베이스에 대한 고급 위협 보호를](sql-database-threat-detection-overview.md) 사용하여 데이터베이스에 대한 DoS(서비스 거부) 공격을 검색합니다.

**모범 사례**:

- [공격 최소화 표면에](#minimize-attack-surface) 설명된 방법을 따르면 DDoS 공격 위협을 최소화할 수 있습니다. 

- 고급 위협 보호 **무차별 암호 대입 SQL 자격 증명** 경고는 무차별 암호 대입 공격을 감지하는 데 도움이 됩니다. 경우에 따라 경고는 침투 테스트 워크로드를 구분할 수도 있습니다. 

- SQL 데이터베이스에 연결하는 Azure VM 호스팅 응용 프로그램의 경우: 
  - 권장 사항에 따라 Azure 보안 센터에서 인터넷 관련 끝점을 통해 액세스를 제한합니다. 
  - 가상 시스템 확장 집합을 사용하여 Azure VM에서 응용 프로그램의 여러 인스턴스를 실행합니다. 
  - 무차별 암호 대입 공격을 방지하기 위해 인터넷에서 RDP 및 SSH를 사용하지 않도록 설정합니다. 

## <a name="monitoring-logging-and-auditing"></a>모니터링, 로깅 및 감사  
이 섹션에서는 데이터베이스에 액세스하거나 악용하려는 비정상적인 시도를 나타내는 비정상적인 활동을 감지하는 데 도움이 되는 기능을 참조합니다. 또한 데이터베이스 이벤트를 추적하고 캡처하도록 데이터베이스 감사를 구성하는 모범 사례에 대해서도 설명합니다.

### <a name="protect-databases-against-attacks"></a>공격으로부터 데이터베이스 보호 
고급 위협 보호를 사용하면 비정상적인 활동에 대한 보안 경고를 제공하여 잠재적인 위협이 발생할 때 이를 감지하고 대응할 수 있습니다.

**구현 방법**:

- [SQL에 대한 고급 위협 보호를](sql-database-threat-detection-overview.md#advanced-threat-protection-alerts) 사용하여 다음과 같은 데이터베이스에 액세스하거나 악용하려는 특이하고 잠재적으로 유해한 시도를 검색합니다.
  - SQL 주입 공격.
  - 자격 증명 도난/누출.
  - 권한 남용.
  - 데이터 유출.

**모범 사례**:

- 특정 SQL 데이터베이스 서버 또는 관리되는 인스턴스에 대해 Azure SQL 데이터베이스에 대한 [고급 데이터 보안(ADS)을](sql-database-advanced-data-security.md#getting-started-with-ads) 구성합니다. [또한 Azure 보안 센터 표준 계층으로](../security-center/security-center-pricing.md)전환하여 구독의 모든 SQL Database 서버 및 관리되는 인스턴스에 대해 ADS를 구성할 수도 있습니다. 

- 전체 조사 환경을 위해 SQL 데이터베이스 [감사를](sql-database-auditing.md)사용하도록 설정하는 것이 좋습니다. 감사를 사용하면 데이터베이스 이벤트를 추적하고 Azure Storage 계정 또는 Azure Log Analytics 작업 영역의 감사 로그에 쓸 수 있습니다. 

### <a name="audit-critical-security-events"></a>중요한 보안 이벤트 감사
데이터베이스 이벤트를 추적하면 데이터베이스 활동을 이해하는 데 도움이 됩니다. 비즈니스 문제 또는 보안 위반으로 의심되는 것을 나타낼 수 있는 불일치 및 이상 징후를 파악할 수 있습니다. 또한 규정 준수 표준을 준수할 수 있도록 지원합니다. 

**구현 방법**:

-  [SQL Database 감사를](sql-database-auditing.md) 사용하여 데이터베이스 이벤트를 추적하고 Azure Storage 계정, Log Analytics 작업 영역(미리 보기) 또는 이벤트 허브(미리 보기)의 감사 로그에 기록합니다. 

- 감사 로그는 Azure Storage 계정, Azure Monitor 로그에서 사용할 수 있는 로그 분석 작업 영역 또는 이벤트 허브를 사용하여 소비하는 이벤트 허브에 기록할 수 있습니다. 이러한 옵션을 조합하여 구성할 수 있으며, 감사 로그는 각각에 대해 작성됩니다. 

**모범 사례**:

- 이벤트를 감사하기 위해 데이터베이스 서버에서 [SQL Database 감사를](sql-database-auditing.md) 구성하면 해당 서버의 기존 데이터베이스와 새로 생성된 모든 데이터베이스가 감사됩니다.
- 기본적으로 감사 정책에는 데이터베이스에 대한 모든 작업(쿼리, 저장 프로시저 및 성공 및 실패한 로그인)이 포함되며, 이로 인해 감사 로그가 많이 발생할 수 있습니다. 고객은 PowerShell 을 [사용하여 다양한 유형의 작업 및 작업 그룹에 대한 감사를 구성하는](sql-database-auditing.md#manage-auditing)것이 좋습니다. 이렇게 구성하면 감사된 작업 수를 제어하고 이벤트 손실 위험을 최소화할 수 있습니다. 사용자 지정 감사 구성을 통해 고객은 필요한 감사 데이터만 캡처할 수 있습니다.
- 감사 로그는 [Azure 포털](https://portal.azure.com/)또는 구성된 저장소 위치에서 직접 사용할 수 있습니다. 


> [!NOTE]
> Log Analytics에 대한 감사를 사용하도록 설정하면 수집 속도에 따라 비용이 발생합니다. 이 [옵션을](https://azure.microsoft.com/pricing/details/monitor/)사용하는 데 드는 관련 비용에 유의하거나 Azure 저장소 계정에 감사 로그를 저장하는 것이 좋습니다. 

**추가 리소스**:

- [SQL 데이터베이스 감사](sql-database-auditing.md)
- [SQL Server 감사](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine) 

### <a name="secure-audit-logs"></a>감사 로그 보안
저장소 계정에 대한 액세스를 제한하여 업무 분리를 지원하고 DBA를 감사자와 분리합니다. 

**구현 방법**:

- 감사 로그를 Azure Storage에 저장할 때 저장소 계정에 대한 액세스가 최소한의 보안 원칙으로 제한되는지 확인합니다. 저장소 계정에 액세스할 수 있는 사람을 제어합니다.
    - 자세한 내용은 [Azure 저장소에 대한 액세스 권한 부여를](../storage/common/storage-auth.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)참조하십시오.

**모범 사례**:

- 감사 대상에 대한 액세스를 제어하는 것은 DBA를 감사인과 분리하는 핵심 개념입니다. 

- 중요한 데이터에 대한 액세스를 감사할 때 는 감사인에게 정보가 유출되지 않도록 데이터 암호화를 사용하여 데이터를 보호하는 것이 좋습니다. 자세한 내용은 [권한이 높은 권한이 없는 사용자로부터 사용 중의 중요한 데이터 보호](#protect-sensitive-data-in-use-from-high-privileged-unauthorized-users)섹션을 참조하십시오.

## <a name="security-management"></a>보안 관리

이 섹션에서는 데이터베이스 보안 상태를 관리하기 위한 다양한 측면과 모범 사례에 대해 설명합니다. 여기에는 보안 표준을 충족하도록 데이터베이스를 구성하고, 데이터베이스에서 잠재적으로 중요한 데이터에 대한 액세스를 검색하고 분류및 추적하기 위한 모범 사례가 포함됩니다. 

### <a name="ensure-that-the-databases-are-configured-to-meet-security-best-practices"></a>데이터베이스가 보안 모범 사례를 충족하도록 구성되었는지 확인 

잠재적인 데이터베이스 취약점을 발견하고 수정하여 데이터베이스 보안을 사전에 개선합니다.

**구현 방법**:

- SQL 취약성 평가(VA)를 사용하여 데이터베이스에서 보안 문제를 검색하고 데이터베이스에서 주기적으로 자동으로 실행되도록 합니다. [SQL Vulnerability Assessment](https://docs.microsoft.com/sql/relational-databases/security/sql-vulnerability-assessment)

**모범 사례**:

- 처음에는 데이터베이스에서 VA를 실행하고 보안 모범 사례와 반대되는 실패한 검사를 수정하여 반복합니다. 검사가 _정리되어_있거나 모든 검사가 통과될 때까지 허용 가능한 구성에 대한 기준을 설정합니다.  

- 주기적 되풀이 검사를 구성하여 일주일에 한 번 실행하고 관련 담당자를 구성하여 요약 이메일을 수신하도록 구성합니다. 

- 매주 마다 검사한 다음 VA 요약을 검토합니다. 발견된 취약점이 있는 경우 이전 검색 결과에서 드리프트를 평가하고 검사를 해결해야 하는지 확인합니다. 구성변경에 대한 정당한 이유가 있는지 검토합니다.   

- 검사를 해결하고 관련있는 경우 기준을 업데이트합니다. 작업을 해결하기 위한 티켓 항목을 만들고 해결될 때까지 추적합니다. 

**추가 리소스**:

- [SQL 취약성 평가](https://docs.microsoft.com/sql/relational-databases/security/sql-vulnerability-assessment) 
- [SQL 취약성 평가 서비스는 데이터베이스 취약성을 식별하는 데 도움이 됩니다.](sql-vulnerability-assessment.md)

### <a name="identify-and-tag-sensitive-data"></a>중요한 데이터 식별 및 태그 

중요한 데이터가 포함될 수 있는 열을 검색합니다. 중요한 데이터로 간주되는 것은 고객, 규정 준수 규정 등에 따라 달라지며 해당 데이터를 담당하는 사용자가 평가해야 합니다. 고급 감도 기반 감사 및 보호 시나리오를 사용하도록 열을 분류합니다. 

**구현 방법**:

- [SQL 데이터 검색 및 분류를](sql-database-data-discovery-and-classification.md) 사용하여 데이터베이스의 중요한 데이터를 검색, 분류, 레이블 지정 및 보호합니다. 
  - SQL 데이터 검색 및 분류 대시보드에서 자동화된 검색에 의해 생성된 분류 권장 사항을 봅니다. 중요한 데이터가 분류 레이블로 지속적으로 태그가 지정되도록 관련 분류를 수락합니다. 
  - 자동화된 메커니즘에서 검색되지 않은 추가 중요한 데이터 필드에 대한 분류를 수동으로 추가합니다. 
- 자세한 내용은 [SQL 데이터 검색 & 분류](https://docs.microsoft.com/sql/relational-databases/security/sql-data-discovery-and-classification)를 참조하십시오.

**모범 사례**:

- 데이터베이스의 분류 상태를 정확하게 평가하기 위해 정기적으로 분류 대시보드를 모니터링합니다. 규정 준수 및 감사 목적으로 데이터베이스 분류 상태에 대한 보고서를 내보내거나 인쇄하여 공유할 수 있습니다.

- SQL 취약성 평가에서 권장되는 중요한 데이터의 상태를 지속적으로 모니터링합니다. 중요한 데이터 검색 규칙을 추적하고 분류에 권장되는 열의 드리프트를 식별합니다.  

- 조직의 특정 요구에 맞는 방식으로 분류를 사용합니다. Azure 보안 센터의 [SQL 정보](../security-center/security-center-info-protection-policy.md) 보호 정책에서 정보 보호 정책(민감도 레이블, 정보 유형, 검색 논리)을 사용자 지정합니다. 

### <a name="track-access-to-sensitive-data"></a>중요한 데이터에 대한 액세스 추적 
중요한 데이터에 액세스하는 사람을 모니터링하고 감사 로그의 중요한 데이터에 대한 쿼리를 캡처합니다.

**구현 방법**:

- SQL 감사 및 데이터 분류를 함께 사용합니다. 
  - SQL [데이터베이스 감사](sql-database-auditing.md) 로그에서 중요한 데이터에 대한 액세스를 추적할 수 있습니다. 액세스한 데이터와 같은 정보와 민감도 레이블을 볼 수도 있습니다. 자세한 내용은 [데이터 검색 & 분류](sql-database-data-discovery-and-classification.md) 및 중요한 데이터에 대한 감사 [액세스를](sql-database-data-discovery-and-classification.md#audit-sensitive-data)참조하십시오. 

**모범 사례**:

- 감사 및 데이터 분류에 대한 모범 사례 섹션을 참조하십시오. 
  - [중요한 보안 이벤트 감사](#audit-critical-security-events) 
  - [중요한 데이터 식별 및 태그](#identify-and-tag-sensitive-data) 

### <a name="visualize-security-and-compliance-status"></a>보안 및 규정 준수 상태 시각화 

SQL Database를 포함하여 데이터 센터의 보안 상태를 강화하는 통합 인프라 보안 관리 시스템을 사용합니다. 데이터베이스 보안 및 규정 준수 상태와 관련된 권장 사항 목록을 봅니다.

**구현 방법**:

- [Azure 보안 센터에서](https://azure.microsoft.com/documentation/services/security-center/)SQL 관련 보안 권장 사항 및 활성 위협을 모니터링합니다.

## <a name="common-security-threats-and-potential-mitigations"></a>일반적인 보안 위협 및 잠재적 완화

이 섹션에서는 특정 공격 벡터로부터 보호하기 위한 보안 조치를 찾는 데 도움이 됩니다. 위의 보안 지침 중 하나 이상을 따르면 대부분의 완화 작업을 수행할 수 있습니다.

### <a name="security-threat-data-exfiltration"></a>보안 위협: 데이터 유출

데이터 유출은 컴퓨터 나 서버에서 데이터를 무단으로 복사, 전송 또는 검색하는 것입니다. 위키백과에서 [데이터 유출에](https://en.wikipedia.org/wiki/Data_exfiltration) 대한 정의를 참조하십시오.

공용 끝점을 통해 Azure SQL Database 서버에 연결하면 고객이 공용 IP에 방화벽을 열어야 하므로 데이터 유출 위험이 있습니다.  

**시나리오 1**: Azure VM의 응용 프로그램이 Azure SQL Database 서버의 데이터베이스에 연결합니다. 불량 행위자VM에 액세스하여 이를 손상시면 됩니다. 이 시나리오에서 데이터 유출은 Rogue VM을 사용하는 외부 엔터티가 데이터베이스에 연결하고 개인 데이터를 복사하여 Blob 저장소 또는 다른 구독에 다른 SQL Database에 저장한다는 것을 의미합니다.

**시나리오 2**: 루즈 DBA. 이 시나리오는 종종 규제 산업에서 보안에 민감한 고객에 의해 발생 합니다. 이 시나리오에서 높은 권한 사용자는 Azure SQL Database에서 데이터 소유자가 제어하지 않는 다른 구독으로 데이터를 복사할 수 있습니다.

**잠재적 완화:**

오늘날 Azure SQL Database는 데이터 유출 위협을 완화하기 위한 다음 기술을 제공합니다. 

- Azure VM의 NSG에서 허용 및 거부 규칙의 조합을 사용하여 VM에서 액세스할 수 있는 지역을 제어합니다. 
- Azure SQL Database 서버(단일 데이터베이스 또는 탄력적 풀 포함)를 사용하는 경우 다음 옵션을 설정합니다.
  - Azure 서비스가 꺼지도록 허용합니다.
  - VNet 방화벽 규칙을 설정하여 Azure VM을 포함하는 서브넷의 트래픽만 허용합니다.
  - [비공개 링크](sql-database-private-endpoint-overview.md) 사용
- 관리되는 인스턴스의 경우 기본적으로 개인 IP 액세스를 사용하면 불량 VM의 첫 번째 데이터 유출 문제가 해결됩니다. 서브넷의 서브넷 위임 기능을 켜면 관리되는 인스턴스 서브넷에서 가장 제한적인 정책을 자동으로 설정합니다.
- Rogue DBA 문제는 더 큰 표면적을 가지며 네트워킹 요구 사항이 고객에게 표시되기 때문에 관리되는 인스턴스에 더 많이 노출됩니다. 이에 대한 가장 좋은 방법은 불량 DBA 시나리오를 처음부터 방지하기 위해 이 보안 가이드의 모든 방법을 적용하는 것입니다(데이터 유출뿐만 아니라). 항상 암호화된 것은 중요한 데이터를 암호화하고 DBA에서 키에 액세스할 수 없도록 유지하여 보호하는 한 가지 방법입니다.

## <a name="security-aspects-of-business-continuity-and-availability"></a>비즈니스 연속성 및 가용성의 보안 측면

대부분의 보안 표준은 단일 장애 지점을 피하기 위해 중복 및 장애 조치 기능을 구현하여 운영 연속성 측면에서 데이터 가용성을 다룹니다. 재해 시나리오의 경우 데이터 및 로그 파일의 백업을 유지하는 것이 일반적입니다.다음 섹션에서는 Azure에 기본 제공 된 기능에 대 한 상위 개요를 제공 합니다. 또한 특정 요구 사항을 충족하도록 구성할 수 있는 추가 옵션도 제공합니다. 

- Azure는 기본 제공 고가용성: [고가용성 및 Azure SQL Database를](sql-database-high-availability.md) 제공합니다. 

- 비즈니스 크리티컬 계층에는 장애 조치 그룹, 다중 가용성 영역, 전체 및 차등 로그 백업 및 기본적으로 활성화된 시점 복원 백업이 포함됩니다.  
  - [고가용성 및 Azure SQL 데이터베이스 - 영역 중복 구성](sql-database-high-availability.md#zone-redundant-configuration)
  - [자동화된 백업](sql-database-automated-backups.md)
  - [자동화된 데이터베이스 백업을 사용하여 Azure SQL 데이터베이스 복구 - 시점 복원](sql-database-recovery-using-backups.md#point-in-time-restore)

- 다른 Azure 지리적 에서 자동 장애 조치 그룹과 같은 추가 비즈니스 연속성 기능은 여기에 설명된 대로 구성할 수 [있습니다.](sql-database-business-continuity.md)

## <a name="next-steps"></a>다음 단계

- [Azure SQL Database 보안 기능 개요](sql-database-security-overview.md) 보기
