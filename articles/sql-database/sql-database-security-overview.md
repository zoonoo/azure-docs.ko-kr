---
title: 보안 개요
description: 클라우드와 온-프레미스 SQL Server 간의 차이점을 포함 하 여 Azure SQL Database 및 SQL Server 보안에 대해 알아봅니다.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto, carlrab, emlisa
ms.date: 05/14/2019
ms.openlocfilehash: c9f59eb8c299eb9319694d392c2b5d1d814ed9b8
ms.sourcegitcommit: d614a9fc1cc044ff8ba898297aad638858504efa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74997329"
---
# <a name="an-overview-of-azure-sql-database-security-capabilities"></a>Azure SQL Database 보안 기능 개요

이 문서에서는 Azure SQL Database를 사용 하 여 응용 프로그램의 데이터 계층을 보호 하는 기본 사항을 간략하게 설명 합니다. 설명 된 보안 전략은 아래 그림에 표시 된 것 처럼 계층화 된 심층 방어 방식을 따르고의 외부에서 이동 합니다.

![sql-security-layer](media/sql-database-security-overview/sql-security-layer.png)

## <a name="network-security"></a>Hálózati biztonság

Microsoft Azure SQL Database는 클라우드 및 엔터프라이즈 응용 프로그램에 대 한 관계형 데이터베이스 서비스를 제공 합니다. 방화벽은 고객 데이터를 보호 하기 위해 IP 주소 또는 Azure 가상 네트워크 트래픽 원본에 따라 액세스가 명시적으로 부여 될 때까지 데이터베이스 서버에 대 한 네트워크 액세스를 차단 합니다.

### <a name="ip-firewall-rules"></a>IP 방화벽 규칙

IP 방화벽 규칙은 각 요청의 원래 IP 주소에 따라 데이터베이스에 대 한 액세스 권한을 부여 합니다. 자세한 내용은 [Azure SQL Database 개요 및 SQL Data Warehouse 방화벽 규칙](sql-database-firewall-configure.md)을 참조 하세요.

### <a name="virtual-network-firewall-rules"></a>Virtuális hálózat tűzfalszabályai

[가상 네트워크 서비스 끝점](../virtual-network/virtual-network-service-endpoints-overview.md) 은 Azure 백본을 통해 가상 네트워크 연결을 확장 하 고 Azure SQL Database를 사용 하도록 설정 하 여 트래픽이 발생 하는 가상 네트워크 서브넷을 식별 합니다. 트래픽이 Azure SQL Database 도달 하도록 허용 하려면 SQL [서비스 태그](../virtual-network/security-overview.md) 를 사용 하 여 네트워크 보안 그룹을 통한 아웃 바운드 트래픽을 허용 합니다.

[가상 네트워크 규칙](sql-database-vnet-service-endpoint-rule-overview.md) 을 사용 하면 가상 네트워크 내에서 선택한 서브넷에서 전송 된 통신만 수락 하도록 Azure SQL Database 수 있습니다.

> [!NOTE]
> 방화벽 규칙을 사용 하 여 액세스를 제어 하는 것은 **관리 되는 인스턴스에**적용 *되지* 않습니다. 필요한 네트워킹 구성에 대 한 자세한 내용은 [관리 되는 인스턴스에 연결](sql-database-managed-instance-connect-app.md) 을 참조 하세요.

## <a name="access-management"></a>Hozzáférés-kezelés

> [!IMPORTANT]
> Azure 내에서 데이터베이스 및 데이터베이스 서버를 관리 하는 것은 포털 사용자 계정의 역할 할당에 의해 제어 됩니다. 이 문서에 대 한 자세한 내용은 [Azure Portal의 역할 기반 액세스 제어](../role-based-access-control/overview.md)를 참조 하세요.

### <a name="authentication"></a>Hitelesítés

인증은 사용자를 증명 하는 사용자를 증명 하는 프로세스입니다. Azure SQL Database는 두 가지 유형의 인증을 지원 합니다.

- **SQL 인증**:

    SQL database 인증은 사용자 이름 및 암호를 사용 하 여 [Azure SQL Database](sql-database-technical-overview.md) 에 연결할 때 사용자의 인증을 나타냅니다. 데이터베이스에 대 한 데이터베이스 서버를 만드는 동안 사용자 이름 및 암호를 사용 하는 "서버 관리자" 로그인을 지정 해야 합니다. "서버 관리자"는 이러한 자격 증명을 사용 하 여 해당 데이터베이스 서버의 모든 데이터베이스를 데이터베이스 소유자로 인증할 수 있습니다. 그런 다음 사용자가 사용자 이름 및 암호를 사용 하 여 연결할 수 있도록 서버 관리자가 추가 SQL 로그인 및 사용자를 만들 수 있습니다.

- **Azure Active Directory 인증**:

    Azure Active Directory 인증은 Azure Active Directory (Azure AD)에서 id를 사용 하 여 [Azure SQL Database](sql-database-technical-overview.md) 및 [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) 에 연결 하는 메커니즘입니다. 관리자는 Azure AD 인증을 사용 하 여 하나의 중앙 위치에서 다른 Microsoft 서비스와 함께 데이터베이스 사용자의 id 및 사용 권한을 중앙에서 관리할 수 있습니다. 여기에는 암호 저장소의 최소화 포함 되며 중앙 집중화 된 암호 순환 정책이 사용 됩니다.

     SQL Database에서 Azure AD 인증을 사용 하려면 **Active Directory 관리자** 라는 서버 관리자를 만들어야 합니다. 자세한 내용은 [Azure Active Directory 인증을 사용 하 여 SQL Database에 연결](sql-database-aad-authentication.md)을 참조 하세요. Azure AD 인증은 관리 되는 계정과 페더레이션된 계정을 모두 지원 합니다. 페더레이션된 계정은 Azure AD와 페더레이션된 고객 도메인에 대 한 Windows 사용자 및 그룹을 지원 합니다.

    사용 가능한 추가 Azure AD 인증 옵션은 [Multi-Factor Authentication](../active-directory/authentication/concept-mfa-howitworks.md) 및 [조건부 액세스](sql-database-conditional-access.md)를 포함 하 여 [SQL Server Management Studio 연결에 대 한 Active Directory 범용 인증](sql-database-ssms-mfa-authentication.md) 입니다.

> [!IMPORTANT]
> Azure 내에서 데이터베이스 및 서버를 관리 하는 것은 포털 사용자 계정의 역할 할당에 의해 제어 됩니다. 이 문서에 대 한 자세한 내용은 [Azure Portal의 역할 기반 액세스 제어](../role-based-access-control/overview.md)를 참조 하세요. 방화벽 규칙을 사용 하 여 액세스를 제어 하는 것은 **관리 되는 인스턴스에**적용 *되지* 않습니다. 필요한 네트워킹 구성에 대 한 자세한 내용은 [관리 되는 인스턴스에 연결 하](sql-database-managed-instance-connect-app.md) 는 다음 문서를 참조 하세요.

## <a name="authorization"></a>Engedélyezés

권한 부여는 Azure SQL Database 내에서 사용자에 게 할당 된 사용 권한을 나타내며 사용자가 수행할 수 있는 작업을 결정 합니다. 권한은 [데이터베이스 역할](/sql/relational-databases/security/authentication-access/database-level-roles) 에 사용자 계정을 추가 하 고 해당 역할에 데이터베이스 수준 사용 권한을 할당 하거나 사용자에 게 특정 [개체 수준 사용 권한을](/sql/relational-databases/security/permissions-database-engine)부여 하 여 제어 됩니다. 자세한 내용은 [로그인 및 사용자](sql-database-manage-logins.md) 를 참조 하세요.

필요한 경우 사용자 지정 역할을 만드는 것이 가장 좋습니다. 작업 기능을 수행 하는 데 필요한 최소한의 권한만 가진 사용자를 역할에 추가 합니다. 사용자에 게 직접 사용 권한을 할당 하지 마십시오. 서버 관리자 계정은 다양 한 권한이 있는 기본 제공 db_owner 역할의 구성원이 며, 관리 업무를 사용 하는 소수의 사용자 에게만 부여 되어야 합니다. Azure SQL Database 응용 프로그램의 경우 [EXECUTE AS](/sql/t-sql/statements/execute-as-clause-transact-sql) 를 사용 하 여 호출 된 모듈의 실행 컨텍스트를 지정 하거나 제한 된 권한으로 [응용 프로그램 역할](/sql/relational-databases/security/authentication-access/application-roles) 을 사용 합니다. 이렇게 하면 데이터베이스에 연결 하는 응용 프로그램에 응용 프로그램에 필요한 최소한의 권한만 부여 됩니다. 이러한 모범 사례를 따르면 발전시키도 구분 됩니다.

### <a name="row-level-security"></a>Sorszintű biztonság

행 수준 보안을 통해 고객은 쿼리를 실행 하는 사용자의 특성 (예: 그룹 멤버 자격 또는 실행 컨텍스트)에 따라 데이터베이스 테이블의 행에 대 한 액세스를 제어할 수 있습니다. 행 수준 보안을 사용 하 여 사용자 지정 레이블 기반 보안 개념을 구현할 수도 있습니다. További információkat a [sorszintű biztonsággal kapcsolatos](/sql/relational-databases/security/row-level-security) részben találhat.

![azure-database-rls](media/sql-database-security-overview/azure-database-rls.png)

## <a name="threat-protection"></a>Fenyegetések elleni védelem

SQL Database는 감사 및 위협 검색 기능을 제공 하 여 고객 데이터를 보호 합니다.

### <a name="sql-auditing-in-azure-monitor-logs-and-event-hubs"></a>Azure Monitor 로그 및 Event Hubs의 SQL 감사

SQL Database 감사는 데이터베이스 작업을 추적 하 고 고객 소유 Azure storage 계정의 감사 로그에 데이터베이스 이벤트를 기록 하 여 보안 표준 준수를 유지 하는 데 도움이 됩니다. 감사를 통해 사용자는 진행 중인 데이터베이스 활동을 모니터링 하 고 기록 활동을 분석 및 조사 하 여 잠재적인 위협이 나 의심 스러운 악용 및 보안 위반을 식별할 수 있습니다. 자세한 내용은 [SQL Database 감사](sql-database-auditing.md)시작을 참조 하세요.  

### <a name="advanced-threat-protection"></a>Komplex veszélyforrások elleni védelem

Advanced Threat Protection은 SQL Server 로그를 분석 하 여 비정상적인 동작을 감지 하 고 잠재적으로 유해한 데이터베이스 액세스 또는 악용 시도를 감지 합니다. 경고는 SQL 삽입, 잠재적 데이터 침입 및 무차별 암호 대입 공격과 같은 의심 스러운 활동에 대해 만들어지거나 권한 상승 및 위반 된 자격 증명 사용을 포착 하기 위한 액세스 패턴의 이상에서 발생 합니다. 경고는 [Azure Security Center](https://azure.microsoft.com/services/security-center/)에서 볼 수 있습니다. 여기에서 의심 스러운 활동에 대 한 세부 정보를 제공 하 고, 위협을 완화 하는 작업과 함께 제공 된 추가 조사에 대 한 권장 사항을 제공 합니다. 추가 요금을 위해 서버당 Advanced Threat Protection을 사용 하도록 설정할 수 있습니다. 자세한 내용은 [SQL Database Advanced Threat Protection 시작](sql-database-threat-detection.md)을 참조 하세요.

![azure-database-td](media/sql-database-security-overview/azure-database-td.jpg)

## <a name="information-protection-and-encryption"></a>정보 보호 및 암호화

### <a name="transport-layer-security-tls-encryption-in-transit"></a>전송 계층 보안 TLS (암호화 전송 중)

SQL Database [전송 계층 보안](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server)을 사용 하 여 동작의 데이터를 암호화 하 여 고객 데이터를 보호 합니다.

Sql Server는 모든 연결에 대해 항상 암호화 (SSL/TLS)를 적용 합니다. 이렇게 하면 연결 문자열에서 **Encrypt** 또는 **trustservercertificate** 의 설정에 관계 없이 모든 데이터가 클라이언트와 서버 간에 "전송 중"으로 암호화 됩니다.

응용 프로그램의 연결 문자열에서 암호화 된 연결을 지정 하 고 서버 인증서를 신뢰 _**하지**_ 않는 것이 좋습니다. 이렇게 하면 응용 프로그램이 서버 인증서를 확인 하 여 응용 프로그램이 중간 유형의 공격을 받을 수 없게 됩니다.

예를 들어 ADO.NET 드라이버를 사용 하는 경우 **Encrypt = True** 및 **Trustservercertificate = False**를 통해이를 수행 합니다. Azure Portal에서 연결 문자열을 가져오는 경우 올바른 설정을 갖게 됩니다.

> [!IMPORTANT]
> 일부 타사 드라이버는 기본적으로 TLS를 사용 하지 않거나 작동 하기 위해 이전 버전의 TLS (< 1.2)를 사용 하지 않을 수 있습니다. 이 경우 SQL Server를 사용 하 여 데이터베이스에 연결할 수 있습니다. 그러나 특히 중요 한 데이터를 저장 하는 경우 이러한 드라이버와 응용 프로그램이 SQL Database에 연결 하도록 허용 하는 보안 위험을 평가 하는 것이 좋습니다. 
>
> TLS 및 연결에 대 한 자세한 내용은 [tls 고려 사항](sql-database-connect-query.md#tls-considerations-for-sql-database-connectivity) 을 참조 하세요.

### <a name="transparent-data-encryption-encryption-at-rest"></a>투명한 데이터 암호화 (미사용 암호화)

[Azure SQL Database에 대 한 TDE (투명한 데이터 암호화)](transparent-data-encryption-azure-sql.md) 는 미사용 데이터를 원시 파일이 나 백업에 대 한 무단 또는 오프 라인 액세스 로부터 보호할 수 있도록 보안 계층을 추가 합니다. 일반적인 시나리오에는 데이터 센터 도난 또는 하드웨어 또는 미디어 (예: 디스크 드라이브 및 백업 테이프)의 안전 하지 않은 삭제가 포함 됩니다. TDE는 응용 프로그램 개발자가 기존 응용 프로그램을 변경할 필요가 없는 AES 암호화 알고리즘을 사용 하 여 전체 데이터베이스를 암호화 합니다.

Azure에서 새로 만든 모든 SQL 데이터베이스는 기본적으로 암호화 되며 데이터베이스 암호화 키는 기본 제공 서버 인증서로 보호 됩니다.  인증서 유지 관리 및 회전이 서비스에 의해 관리 되며 사용자의 입력이 필요 하지 않습니다. 암호화 키에 대 한 제어를 선호 하는 고객은 [Azure Key Vault](../key-vault/key-vault-secure-your-key-vault.md)의 키를 관리할 수 있습니다.

### <a name="key-management-with-azure-key-vault"></a>Azure Key Vault를 사용한 키 관리

[Bring Your Own Key](transparent-data-encryption-byok-azure-sql.md) (byok [투명한 데이터 암호화](/sql/relational-databases/security/encryption/transparent-data-encryption) )를 사용 하 여 고객은 Azure의 클라우드 기반 외부 키 관리 시스템 [Azure Key Vault](../key-vault/key-vault-secure-your-key-vault.md)를 사용 하 여 키 관리 및 회전의 소유권을 가져올 수 있습니다. 키 자격 증명 모음에 대 한 데이터베이스의 액세스가 취소 된 경우 데이터베이스의 암호를 해독 하 고 메모리로 읽어 올 수 없습니다. Azure Key Vault 중앙 키 관리 플랫폼을 제공 하 고, 긴밀 하 게 모니터링 되는 Hsm (하드웨어 보안 모듈)을 활용 하며, 키 및 데이터 관리 간에 의무를 분리 하 여 보안 규정 준수 요구 사항을 충족할 수 있습니다.

### <a name="always-encrypted-encryption-in-use"></a>Always Encrypted (암호화 사용)

![azure-database-ae](media/sql-database-security-overview/azure-database-ae.png)

[Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) 는 특정 데이터베이스 열에 저장 된 중요 한 데이터 (예: 신용 카드 번호, 국가 식별 번호 또는 정보를 _알고 있어야 하_ 는 데이터)를 보호 하기 위해 설계 된 기능입니다. 여기에는 데이터베이스 관리자 또는 관리 작업을 수행 하기 위해 데이터베이스에 액세스할 수 있는 권한이 있지만 암호화 된 열의 특정 데이터에 액세스할 필요가 없는 기타 권한 있는 사용자도 포함 됩니다. 데이터는 항상 암호화 됩니다. 즉, 암호화 된 데이터는 암호화 키에 대 한 액세스 권한이 있는 클라이언트 응용 프로그램에서 처리 하는 경우에만 해독 됩니다.  암호화 키는 SQL에 노출 되지 않으며 [Windows 인증서 저장소](sql-database-always-encrypted.md) 또는 [Azure Key Vault](sql-database-always-encrypted-azure-key-vault.md)에 저장할 수 있습니다.

### <a name="dynamic-data-masking"></a>Dinamikus adatmaszkolás

![azure-database-ddm](media/sql-database-security-overview/azure-database-ddm.png)

SQL Database 동적 데이터 마스킹은 권한이 없는 사용자에 게 데이터를 마스킹 하 여 중요 한 데이터 노출을 제한 합니다. 동적 데이터 마스킹은 Azure SQL Database에서 잠재적으로 중요 한 데이터를 자동으로 검색 하 고 응용 프로그램 계층에 미치는 영향을 최소화 하면서 이러한 필드를 마스킹할 조치 가능한 권장 사항을 제공 합니다. Rejtjelezi a bizalmas adatokat egy kijelölt adatbázismezőkön végrehajtott lekérdezés eredményhalmazában, miközben az adatbázis adatait nem módosítja. 자세한 내용은 [SQL Database 동적 데이터 마스킹 시작](sql-database-dynamic-data-masking-get-started.md)을 참조 하세요.

## <a name="security-management"></a>Biztonságkezelés

### <a name="vulnerability-assessment"></a>Sebezhetőségi felmérés

[취약성 평가](sql-vulnerability-assessment.md) 는 전체 데이터베이스 보안을 사전에 향상 시킬 수 있는 목적으로 잠재적인 데이터베이스 취약성을 검색, 추적 및 수정할 수 있는 쉽게 구성할 수 있는 서비스입니다. VA (취약성 평가)는 고급 SQL 보안 기능을 위한 통합 패키지인 광고 (advanced data security) 제품의 일부입니다. 취약성 평가는 중앙 SQL ADS 포털을 통해 액세스 하 고 관리할 수 있습니다.

### <a name="data-discovery--classification"></a>Adatfelderítés és besorolás

데이터 검색 & 분류 (현재 미리 보기 상태)는 데이터베이스의 중요 한 데이터를 검색, 분류, 레이블 지정 및 보호 하기 위한 고급 기능을 제공 Azure SQL Database 합니다. 가장 중요 한 데이터 (비즈니스/재무, 의료, 개인 데이터 등)를 검색 하 고 분류 하면 조직 정보 보호에서 pivotal 역할을 수행할 수 있습니다. Infrastruktúraként alkalmas lehet az alábbiakra:

- 모니터링 (감사) 및 중요 한 데이터에 대 한 비정상적인 액세스 경고 등의 다양 한 보안 시나리오
- 매우 중요 한 데이터를 포함 하는 데이터베이스에 대 한 액세스 제어 및 보안 강화.
- Segíthet megfelelni az adatvédelmi szabványoknak és a szabályozási megfelelőség követelményeinek.

자세한 내용은 [데이터 검색 & 분류 시작](sql-database-data-discovery-and-classification.md)을 참조 하세요.

### <a name="compliance"></a>Megfelelőség

응용 프로그램에서 다양 한 보안 요구 사항을 충족 하는 데 도움이 되는 위의 기능 외에도, Azure SQL Database는 정기적인 감사에 참여 하 고 여러 규정 준수 표준에 대해 인증 되었습니다. 자세한 내용은 SQL Database 준수 인증의 최신 목록을 찾을 수 있는 [Microsoft Azure 보안 센터](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) 를 참조 하세요.

## <a name="next-steps"></a>Következő lépések

- Az SQL Database említett hozzáférés-vezérlési funkcióinak használatával kapcsolatban lásd a [hozzáférés-vezérléssel](sql-database-control-access.md) foglalkozó témakört.
- 데이터베이스 감사에 대 한 설명은 [SQL Database 감사](sql-database-auditing.md)를 참조 하세요.
- 위협 검색에 대 한 자세한 내용은 [SQL Database 위협 감지](sql-database-threat-detection.md)를 참조 하세요.
