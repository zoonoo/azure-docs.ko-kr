---
title: 보안 개요
description: 클라우드와 온-프레미스 SQL Server 간 차이를 포함하여 Azure SQL Database 및 SQL Server 보안에 대해 알아보세요.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aliceku
ms.author: aliceku
ms.reviewer: vanto, carlrab, emlisa
ms.date: 05/14/2019
ms.openlocfilehash: b318d4b5076ff24612d5b5ce0ba619f0b38ac280
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/25/2019
ms.locfileid: "74483840"
---
# <a name="an-overview-of-azure-sql-database-security-capabilities"></a>Azure SQL Database 보안 기능의 개요

이 문서에서는 Azure SQL Database를 사용하여 애플리케이션의 데이터 계층에 보안을 설정하기 위한 기본 사항을 간략하게 설명합니다. 여기서 설명하는 보안 전략은 아래 그림에 나와 있는 계층형 심층 방어 방식을 따르며 외부에서 내부로 적용됩니다.

![sql-security-layer.png](media/sql-database-security-overview/sql-security-layer.png)

## <a name="network-security"></a>네트워크 보안

Microsoft Azure SQL Database는 클라우드 및 엔터프라이즈 애플리케이션용 관계형 데이터베이스 서비스를 제공합니다. 고객 데이터를 보호하기 위해 방화벽은 Azure Virtual Network 트래픽 출처 또는 IP 주소를 기준으로 액세스 권한이 명시적으로 부여될 때까지 네트워크에서 데이터베이스 서버에 액세스할 수 없도록 차단합니다.

### <a name="ip-firewall-rules"></a>IP 방화벽 규칙

IP 방화벽 규칙은 각 요청이 시작된 IP 주소를 기준으로 하여 데이터베이스 액세스 권한을 부여합니다. 자세한 내용은 [Azure SQL Database 및 SQL Data Warehouse 방화벽 규칙 개요](sql-database-firewall-configure.md)를 참조하세요.

### <a name="virtual-network-firewall-rules"></a>Virtual Network 방화벽 규칙

[가상 네트워크 서비스 엔드포인트](../virtual-network/virtual-network-service-endpoints-overview.md)는 Azure 백본을 통해 가상 네트워크 연결을 확장하며, 트래픽이 생성되는 가상 네트워크 서브넷을 Azure SQL Database가 식별할 수 있도록 합니다. 트래픽이 Azure SQL Database로 전송되도록 하려면 SQL [서비스 태그](../virtual-network/security-overview.md)를 사용해 네트워크 보안 그룹을 통한 아웃바운드 트래픽을 허용합니다.

Azure SQL Database는 [가상 네트워크 규칙](sql-database-vnet-service-endpoint-rule-overview.md)을 통해 Virtual Network 내의 선택한 서브넷에서 전송된 통신만 수락할 수 있습니다.

> [!NOTE]
> 방화벽 규칙을 사용한 액세스 제어는 **관리되는 인스턴스**에 적용되지 *않습니다*. 필요한 네트워킹 구성에 대한 자세한 내용은 [관리되는 인스턴스에 연결](sql-database-managed-instance-connect-app.md)을 참조하세요.

## <a name="access-management"></a>액세스 관리

> [!IMPORTANT]
> Azure 내에서 데이터베이스와 데이터베이스 서버를 관리하는 작업은 포털 사용자 계정의 역할 할당을 통해 제어됩니다. 이 아티클에 대한 자세한 내용은 [Azure Portal의 역할 기반 액세스 제어](../role-based-access-control/overview.md)를 참조하세요.

### <a name="authentication"></a>Authentication

인증은 사용자의 신원을 증명하는 과정입니다. Azure SQL Database는 두 가지 인증 유형을 지원합니다.

- **SQL 인증**:

    SQL 데이터베이스 인증은 사용자 이름과 암호를 사용하여 [Azure SQL Database](sql-database-technical-overview.md)에 연결할 때 사용자가 수행하는 인증을 지칭합니다. 데이터베이스용 데이터베이스 서버를 만들 때 사용자 이름과 암호를 사용하는 "서버 관리자" 로그인을 지정해야 합니다. "서버 관리자"는 이러한 자격 증명을 사용하여 해당 데이터베이스 서버의 모든 데이터베이스에 데이터베이스 소유자로 인증할 수 있습니다. 그리고 나면 서버 관리자는 추가 SQL 로그인 및 사용자를 만들 수 있으며, 그러면 사용자가 사용자 이름과 암호를 사용하여 연결할 수 있습니다.

- **Azure Active Directory 인증**:

    Azure Active Directory 인증은 Azure AD(Azure Active Directory)의 ID를 사용하여 [Azure SQL Database](sql-database-technical-overview.md) 및 [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md)에 연결하는 메커니즘입니다. 관리자는 Azure AD 인증을 통해 데이터베이스 사용자의 ID 및 권한과 기타 Microsoft 서비스를 중앙 위치 한 곳에서 관리할 수 있습니다. 그러면 스토리지해야 하는 암호를 최소화하고 중앙 집중식 암호 순환 정책을 사용할 수 있습니다.

     SQL Database를 사용한 Azure AD 인증을 사용하려면 서버 관리자 **Active Directory 관리자**를 만들어야 합니다. 자세한 내용은 [Azure Active Directory 인증을 사용하여 SQL Database에 연결](sql-database-aad-authentication.md)을 참조하세요. Azure AD 인증에서는 관리 계정과 페더레이션된 계정이 모두 지원됩니다. 페더레이션된 계정은 Azure AD와 페더레이션된 고객 도메인용 Windows 사용자 및 그룹을 지원합니다.

    사용 가능한 추가 Azure AD 인증 옵션으로는 [다단계 인증](../active-directory/authentication/concept-mfa-howitworks.md) 및 [조건부 액세스](sql-database-conditional-access.md)를 비롯한 [SQL Server Management Studio용 Active Directory 유니버설 인증](sql-database-ssms-mfa-authentication.md) 연결이 있습니다.

> [!IMPORTANT]
> Azure 내에서 데이터베이스와 서버를 관리하는 작업은 포털 사용자 계정의 역할 할당을 통해 제어됩니다. 이 아티클에 대한 자세한 내용은 [Azure Portal의 역할 기반 액세스 제어](../role-based-access-control/overview.md)를 참조하세요. 방화벽 규칙을 사용한 액세스 제어는 **관리되는 인스턴스**에 적용되지 *않습니다*. 필요한 네트워킹 구성에 대한 자세한 내용은 [관리되는 인스턴스에 연결](sql-database-managed-instance-connect-app.md)에 관한 다음 문서를 참조하세요.

## <a name="authorization"></a>권한 부여

Azure SQL Database 내에서 사용자에게 할당되는 권한을 지칭하는 권한 부여는 사용자가 수행할 수 있는 작업을 결정합니다. Permissions are controlled by adding user accounts to [database roles](/sql/relational-databases/security/authentication-access/database-level-roles) and assigning database-level permissions to those roles or by granting the user certain [object-level permissions](/sql/relational-databases/security/permissions-database-engine). 자세한 내용은 [로그인 및 사용자](sql-database-manage-logins.md)를 참조하세요.

As a best practice, create custom roles when needed. Add users to the role with the least privileges required to do their job function. Do not assign permissions directly to users. The server admin account is a member of the built-in db_owner role, which has extensive permissions and should only be granted to few users with administrative duties. For Azure SQL Database applications, use the [EXECUTE AS](/sql/t-sql/statements/execute-as-clause-transact-sql) to specify the execution context of the called module or use [Application Roles](/sql/relational-databases/security/authentication-access/application-roles) with limited permissions. This practice ensures that the application that connects to the database has the least privileges needed by the application. Following these best practices also fosters separation of duties.

### <a name="row-level-security"></a>행 수준 보안

행 수준 보안을 통해 고객은 쿼리를 실행하는 사용자의 특성(예: 그룹 멤버 자격 또는 실행 컨텍스트)을 기반으로 하여 데이터베이스 테이블의 행에 대한 액세스를 제어할 수 있습니다. Row-Level Security can also be used to implement custom Label-based security concepts. 자세한 내용은 [행 수준 보안](/sql/relational-databases/security/row-level-security)을 참조하세요.

![azure-database-rls.png](media/sql-database-security-overview/azure-database-rls.png)

## <a name="threat-protection"></a>위협 보호

SQL Database는 감사 및 위협 검색 기능을 제공하여 고객 데이터를 보호합니다.

### <a name="sql-auditing-in-azure-monitor-logs-and-event-hubs"></a>SQL auditing in Azure Monitor logs and Event Hubs

SQL Database 감사는 데이터베이스 활동을 추적하며 고객이 소유한 Azure Storage 계정의 감사 로그에 데이터베이스 이벤트를 기록하여 보안 표준 규정 준수 상태를 유지할 수 있도록 지원합니다. 사용자는 감사를 통해 진행 중인 데이터베이스 활동을 모니터링하고 이전 활동을 분석 및 조사하여 잠재적 위협이나 악용 의심 사례 및 보안 위반을 식별할 수 있습니다. 자세한 내용은 [SQL Database 감사 시작](sql-database-auditing.md)을 참조하세요.  

### <a name="advanced-threat-protection"></a>Advanced Threat Protection

Advanced Threat Protection is analyzing your SQL Server logs to detect unusual behavior and potentially harmful attempts to access or exploit databases. Alerts are created for suspicious activities such as SQL injection, potential data infiltration, and brute force attacks or for anomalies in access patterns to catch privilege escalations and breached credentials use. Alerts are viewed from the  [Azure Security Center](https://azure.microsoft.com/services/security-center/), where the details of the suspicious activities are provided and recommendations for further investigation given along with actions to mitigate the threat. Advanced Threat Protection can be enabled per server for an additional fee. For more information, see [Get started with SQL Database Advanced Threat Protection](sql-database-threat-detection.md).

![azure-database-td.jpg](media/sql-database-security-overview/azure-database-td.jpg)

## <a name="information-protection-and-encryption"></a>정보 보호 및 암호화

### <a name="transport-layer-security-tls-encryption-in-transit"></a>TLS(전송 계층 보안)(전송 중 암호화)

SQL Database는 [전송 계층 보안](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server)을 사용하여 이동 중인 데이터를 암호화해 고객 데이터를 보호합니다.

Sql Server enforces encryption (SSL/TLS) at all times for all connections. This ensures all data is encrypted "in transit" between the client and server irrespective of the setting of **Encrypt** or **TrustServerCertificate** in the connection string.

As a best practice, recommend that in your application's connection string you specify an encrypted connection and _**not**_ trust the server certificate. This forces your application to verify the server certificate and thus prevents your application from being vulnerable to man in the middle type attacks.

For example when using the ADO.NET driver this is accomplished via  **Encrypt=True** and **TrustServerCertificate=False**. Azure Portal에서 연결 문자열을 얻는 경우 올바른 설정이 사용됩니다.

> [!IMPORTANT]
> Note that some non-Microsoft drivers may not use TLS by default or rely on an older version of TLS (<1.2) in order to function. In this case SQL Server still allows you to connect to your database. However, we recommend that you evaluate the security risks of allowing such drivers and application to connect to SQL Database, especially if you store sensitive data. 
>
> For further information about TLS and connectivity, see [TLS considerations](sql-database-connect-query.md#tls-considerations-for-sql-database-connectivity)

### <a name="transparent-data-encryption-encryption-at-rest"></a>투명한 데이터 암호화(미사용 데이터 암호화)

[Azure SQL Database용 TDE(투명한 데이터 암호화)](transparent-data-encryption-azure-sql.md)는 원시 파일이나 백업에 무단/오프라인으로 액세스할 수 없도록 미사용 데이터를 보호하기 위해 보안 계층을 추가합니다. 무단/오프라인 액세스의 일반적인 시나리오에는 데이터 센터 도난, 안전하지 않은 하드웨어 또는 미디어(예: 디스크 드라이브 및 백업 테이프) 폐기 등이 포함됩니다. TDE encrypts the entire database using an AES encryption algorithm, which doesn’t require application developers to make any changes to existing applications.

Azure에서는 새로 만드는 모든 SQL Database가 기본적으로 암호화되며, 기본 제공 서버 인증서를 통해 데이터베이스 암호화 키가 보호됩니다.  서비스에서 인증서 유지 관리 및 순환을 관리하므로 사용자 입력은 필요하지 않습니다. 암호화 키를 직접 제어하려는 고객은 [Azure Key Vault](../key-vault/key-vault-secure-your-key-vault.md)에서 키를 관리할 수 있습니다.

### <a name="key-management-with-azure-key-vault"></a>Azure Key Vault으로 키 관리

고객은 TDE( [투명한 데이터 암호화](/sql/relational-databases/security/encryption/transparent-data-encryption))용으로 지원되는 BYOK([Bring Your Own Key](transparent-data-encryption-byok-azure-sql.md))를 활용해 Azure의 클라우드 기반 외부 키 관리 시스템인  [Azure Key Vault](../key-vault/key-vault-secure-your-key-vault.md)를 사용하여 키 관리 및 순환을 직접 제어할 수 있습니다. 데이터베이스의 키 자격 증명 모음 액세스 권한이 철회되면 데이터베이스를 암호 해독하여 메모리로 읽어들일 수 없습니다. 중앙 키 관리 플랫폼을 제공하며 철저하게 모니터링되는 HSM(하드웨어 보안 모듈)을 활용하는 Azure Key Vault를 사용하면 키와 데이터 관리 작업을 분리하여 보안 규정 준수 요구 사항을 충족할 수 있습니다.

### <a name="always-encrypted-encryption-in-use"></a>Always Encrypted(사용 중인 데이터 암호화)

![azure-database-ae.png](media/sql-database-security-overview/azure-database-ae.png)

[Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine)는 신용 카드 번호, 주민 등록 번호 또는 _확인이 필요_한 데이터와 같이 특정 데이터베이스 열에 저장된 중요한 데이터를 액세스할 수 없도록 보호하는 기능입니다. 예를 들어 이 기능을 통해 데이터베이스에 액세스하여 관리 작업을 수행할 권한은 부여되었지만 업무상 암호화된 열의 특정 데이터에는 액세스할 필요가 없는 데이터베이스 관리자 또는 기타 권한 있는 사용자로부터 데이터를 보호할 수 있습니다. 데이터는 항상 암호화되므로 암호화 키 액세스 권한이 있는 클라이언트 애플리케이션에서 처리해야 하는 경우에만 암호화된 데이터의 암호가 해독됩니다.  암호화 키는 SQL에 표시되지 않으며 [Windows 인증서 저장소](sql-database-always-encrypted.md) 또는 [Azure Key Vault](sql-database-always-encrypted-azure-key-vault.md)에 저장할 수 있습니다.

### <a name="dynamic-data-masking"></a>동적 데이터 마스킹

![azure-database-ddm.png](media/sql-database-security-overview/azure-database-ddm.png)

SQL Database 동적 데이터 마스킹에서는 권한이 없는 사용자에 대해 중요한 데이터를 마스킹해 표시함으로써 데이터 노출을 제한합니다. 동적 데이터 마스킹은 Azure SQL Database에서 잠재적으로 중요한 데이터를 자동으로 검색하고 애플리케이션 계층에 미치는 영향을 최소화하면서 이러한 필드를 마스킹할 수 있는 실행 가능한 권장 사항을 제공합니다. 이 기능은 지정된 데이터베이스 필드를 통해 쿼리의 결과 집합에 있는 중요한 데이터를 혼란스럽게 만들면서 작동하지만 데이터베이스의 데이터를 변경하지는 않습니다. 자세한 내용은 [SQL 데이터베이스 동적 데이터 마스킹](sql-database-dynamic-data-masking-get-started.md) 시작을 참조하세요.

## <a name="security-management"></a>보안 관리

### <a name="vulnerability-assessment"></a>취약점 평가

[취약성 평가](sql-vulnerability-assessment.md)는 전반적인 데이터베이스 보안을 사전에 개선하기 위해 잠재적인 데이터베이스 취약성을 검색, 추적 및 수정할 수 있는 손쉽게 구성 가능한 서비스입니다. VA(취약성 평가)는 고급 SQL 보안 기능용 통합 패키지인 ADS(Advanced Data Security) 제품에 포함되어 있습니다. 중앙 SQL ADS 포털을 통해 취약성 평가에 액세스하고 관리할 수 있습니다.

### <a name="data-discovery--classification"></a>데이터 검색 및 분류

데이터 검색 및 분류(현재 미리 보기)는 데이터베이스에 있는 중요한 데이터를 검색, 분류, 레이블 지정 및 보호하기 위한 Azure SQL Database에 기본 제공된 고급 기능을 제공합니다. 업무/금융, 의료, 개인 데이터 등의 매우 중요한 데이터를 검색하고 분류하는 작업은 조직 정보 보호 상태를 유지하는 데 중추적인 역할을 할 수 있습니다. 그것은 다음에 대한 인프라 역할을 할 수 있습니다.

- 중요한 데이터에 대한 비정상적인 엑세스 모니터링(감사) 및 경고하는 것과 같은 다양한 보안 시나리오.
- 매우 중요한 데이터가 들어 있는 데이터베이스에 대한 액세스 제어 및 보안 강화.
- 데이터 프라이버시 표준 및 규정 준수 요구 사항을 충족하도록 지원.

자세한 내용은 [데이터 검색 및 분류 시작](sql-database-data-discovery-and-classification.md)을 참조하세요.

### <a name="compliance"></a>규정 준수

Azure SQL Database는 위의 기능 및 애플리케이션이 다양한 보안 요구 사항을 충족하는 데 도움이 될 수 있는 기능을 포함할 뿐 아니라, 정기 감사도 받고 있으며 다수의 규정 준수 표준 충족 인증도 취득했습니다. For more information, see the [Microsoft Azure Trust Center](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) where you can find the most current list of SQL Database compliance certifications.

## <a name="next-steps"></a>다음 단계

- SQL Database에서 액세스 제어 기능을 사용하는 방법에 대한 설명은 [액세스 제어](sql-database-control-access.md)를 참조하세요.
- 데이터베이스 감사 내용은 [SQL Database 감사](sql-database-auditing.md)를 참조하세요.
- 위협 요소 탐지 내용은 [SQL Database 위협 요소 탐지](sql-database-threat-detection.md)를 참조하세요.
