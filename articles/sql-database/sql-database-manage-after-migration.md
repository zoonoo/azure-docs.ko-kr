---
title: "마이그레이션 후 관리 - Azure SQL Database | Microsoft Docs"
description: "Azure SQL Database로 마이그레이션 후 데이터베이스를 관리하는 방법에 대해 알아봅니다."
services: sql-database
documentationcenter: 
author: joesackmsft
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: migrate
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Inactive
ms.date: 10/14/2016
ms.author: Joe.Sack
ms.suite: sql
ms.prod_service: sql-database
ms.component: migration
ms.openlocfilehash: e562c33cabc7d39d1f6a911c21343f85da205c0b
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/28/2017
---
# <a name="how-should-i-manage-my-azure-sql-database-after-migration"></a>마이그레이션 후 Azure SQL 데이터베이스를 어떻게 관리해야 합니까?

*Azure SQL Database 관리에 관해 자주 묻는 질문*

최근에 SQL Server 데이터베이스를 Azure SQL Database로 이동했거나, 또는 곧 이동할 계획 중입니다. 이동했다면 다음 단계는 무엇입니까? SQL Database가 *Platform as a Service*라고 한다면, Microsoft는 사용자를 대신하여 여러 영역을 처리합니다. 하지만 이 때문에 보안, 비즈니스 연속성, 데이터베이스 유지 관리, 성능 조정, 모니터링 등과 같은 주요 영역에 대한 회사의 업무를 정확히 어떻게 변경됩니까? 

이 문서의 목적은 리소스 및 Microsoft Azure SQL Database 투자를 관리하는 데 있어서 변화를 가져오기 위해 사용자가 필요로 하는 리소스 및 지침을 간결하게 정리하는 것입니다. 이 문서의 주요 영역은 비즈니스 연속성, 보안, 데이터베이스 유지 관리와 모니터링, 성능과 데이터 이동 등을 다룹니다. Microsoft SQL Server와 Microsoft Azure SQL Database 간에 서로 다른 주요 영역을 다루고, 혜택을 최대화하고 위험을 최소화하는 데 도움이 되는 작업 모범 사례를 호출할 것입니다. 

## <a name="manage-business-continuity-after-migration"></a>마이그레이션 후 비즈니스 연속성을 관리합니다.

### <a name="how-do-i-create-and-manage-backups-on-sql-database"></a>Microsoft Azure SQL Database에서 백업을 만들고 관리하려면 어떻게 해야 합니까? 
Microsoft Azure SQL Database는 자동으로 사용자의 데이터베이스를 백업하고 재방문 주기 내 임의의 시점으로 복원하는 기능을 제공합니다. 재방문 주기는 표준 및 프리미엄 데이터베이스인 경우 35일, 기본 데이터베이스인 경우 7일입니다. 또한 Long-Term Retention 기능을 사용하면 더 긴 기간(최대 10년) 동안 백업 파일을 보유하고 언제든지 이러한 백업에서 복원할 수 있습니다. 또한 데이터베이스 백업은 재해 또는 지역 참사 사건이 일어난 지역에서 지역 복원하는 능력을 확보하기 위해 지역에서 복제됩니다. [비즈니스 연속성 개요](sql-database-business-continuity.md)를 참조하세요.

### <a name="how-do-i-ensure-business-continuity-in-the-event-of-a-datacenter-level-disaster-or-a-regional-catastrophe"></a>데이터 센터 수준의 재해 또는 지역 참사 발생 시 비즈니스 연속성을 확인하려면 어떻게 해야 합니까? 

데이터베이스 백업은 재해 또는 지역 참사 사건이 일어난 지역에서 지역 복원하는 능력을 확보하기 위해 지역에서 복제됩니다. [비즈니스 연속성 개요](sql-database-business-continuity.md)를 참조하세요. 또한 Microsoft Azure SQL Database는 또 다른 지역에 적극적으로 지역에서 복제된 보조 데이터베이스를 유지 관리하는 기능을 제공합니다. 자동 장애 조치 그룹에서 구성하면 데이터베이스가 재해 시나리오에서 자동으로 보조 데이터베이스를 장애 조치합니다. 자동 장애 조치 그룹이 구성되지 않은 경우 응용 프로그램은 재해에 관해 적극적으로 모니터링하고 보조에 대해 장애 조치를 시작해야 합니다. 
### <a name="sql-server-provided-me-readable-secondary-replicas-can-i-access-the-secondaries-on-sql-database"></a>Microsoft SQL Server가 읽기 가능한 보조 복제본을 제공했습니다. Microsoft Azure SQL Database에서 보조 복제본에 액세스할 수 있습니까? 

예, '활성 지리적 복제' 기능은 읽기 가능한 보조 복제본을 만드는 사용됩니다. 

### <a name="how-does-my-disaster-recovery-plan-change-from-on-premise-to-sql-database"></a>재해 복구 계획이 온-프레미스에서 Microsoft Azure SQL Database로 어떻게 변경됩니까? 
Microsoft SQL Server를 구현하려면 장애 조치 클러스터링, 데이터베이스 미러링, 복제, 로그 전달 또는 일반 바닐라 BACPAC 백업 등의 기능을 사용하여 적극적으로 백업을 관리해야 했습니다. 그러나 SQL Database에서 백업은 전적으로 Microsoft에서 관리하며 사용자는 Azure Portal에서 단 몇 번의 클릭(또는 PowerShell에서 몇 가지 명령)으로 백업 및 재해 복구를 구성하고 작업하도록 할 수 있습니다. ‌
### <a name="in-the-event-of-disaster-how-do-i-recover-my-databases"></a>재해 발생 시 데이터베이스를 복구하려면 어떻게 해야 합니까? 
Microsoft Azure SQL Database는 자동으로 사용자가 지난 35일간의 어느 시점으로 데이터베이스를 복원할 수 있게 합니다. 데이터가 손실되거나 또는 응용 프로그램 관련 재해가 발생할 경우 하나의 옵션입니다. 

지역적 재해를 직면하는 경우, 지역에서 복제된 보조 데이터베이스가 구성되어 있다면, 또 다른 지역에서 지리적 보조 데이터베이스로부터 복구할 수 있습니다. 응용 프로그램에 대한 실시간 액세스의 경우 다른 지역에서 수동으로 지역 보조 복제본에 대해 장애 조치할 수 있습니다. 또는 자동 장애 조치 그룹이 구성된 경우, 지역 보조 복제본에 대한 장애 조치는 재해 시나리오에서 자동으로 이루어집니다. 지역에서 복제된 보조 데이터베이스가 구성되어 있지 않더라도, 자동 복제된 백업 파일로부터 여전히 데이터베이스를 복구할 수 있습니다.(기본 제공 기능, 구성이 필요하지 않음) 그러나 상대적으로 긴 복구 시간(12시간 RTO) 및 시간 가량의 데이터 손실을 감안해야 합니다. 

### <a name="are-the-failovers-to-secondary-transparent-how-does-my-application-handle-database-failovers"></a>보조에 대한 장애 조치는 투명합니까? 응용 프로그램은 데이터베이스 장애 조치를 어떻게 처리합니까? 
자동 장애 조치 그룹이 구성되었다면, 보조 복제본에 대한 장애 조치는 투명합니다. 하지만 그러지 않은 경우 응용 프로그램은 기본의 가용성을 모니터링한 다음 수동으로 보조 복제본을 장애 조치하기 위해 논리를 통합해야 합니다. 
 
## <a name="manage-security-after-migration"></a>마이그레이션 후 보안을 관리합니다.

### <a name="how-can-i-restrict-access-to-my-sql-database"></a>Microsoft Azure SQL Database에 대한 액세스를 제한하려면 어떻게 해야 합니까? 
 
SQL 데이터베이스에 대한 연결 액세스를 잠글 몇 가지 방법이 있습니다. 
1. 인터넷 기본 경로를 통해 트래픽을 제한하면 Azure 네트워크에 전용 파이버를 제공하여 데이터가 인터넷을 통해 전달되지 않습니다. 또한 기본 경로를 사용하여 크로스 영역 연결을 구성하는 것도 가능합니다. 다음 링크는 기본 경로를 자세히 설명합니다. 
 - [기본 경로 소개](../expressroute/expressroute-introduction.md)
 - [필수 구성 요소](../expressroute/expressroute-prerequisites.md) 
 - [워크플로](../expressroute/expressroute-workflows.md) 
 
2. Microsoft Azure SQL Database에 어떤 리소스를 연결할지 선택합니다. 

   기본적으로 SQL 데이터베이스는 "모든 Azure 서비스 허용"으로 구성되는데, 이는 Azure의 모든 VM이 데이터베이스에 연결을 시도할 것임을 의미합니다.  모든 로그인의 인증은 여전히 발생해야 합니다. 모든 Azure IP에서 데이터베이스에 액세스할 수 있도록 하기를 원하지 않는다면 "모든 Azure 서비스 허용"을 사용하지 않도록 설정하고 [VNET 서비스 끝점](sql-database-vnet-service-endpoint-rule-overview.md)을 사용하여 데이터베이스에 대한 인바운드 액세스를 주어진 Azure VNET 서브넷 안에 있는 Azure 리소스에만 제한을 할 수 있습니다. 

   ![VNET 서비스 끝점](./media/sql-database-manage-after-migration/vnet-service-endpoints.png) 

   다른 옵션은 VM에 대해 [예약된 IP](../virtual-network/virtual-networks-reserved-public-ip.md)를 프로비전하고, 서버 방화벽 설정에 있는 특정 VM IP 주소를 허용목록에 넣습니다. (Azure Portal의 예로 아래 스크린샷을 참조하세요.) 예약된 IP를 할당하면 IP 주소 변경에 따라 방화벽 규칙을 직접 업데이트해야 할 필요가 없습니다. 

3. Azure 외부에서 포트 1433을 노출하지 않음

   [Azure RemoteApp](https://www.microsoft.com/cloud-platform/azure-remoteapp-client-apps)을 사용하여 Azure에서 SSMS를 실행합니다. 포트 1433에 대한 나가는 연결을 시작할 필요가 없습니다. IP가 고정적이라 DB가 RemoteApp에만 열릴 수 있습니다. 다중 단계 인증(MFA)을 지원하고 다중 사용자용입니다. 

### <a name="what-authentication-methods-are-offered-in-sql-database"></a>어떤 인증 방법이 Microsoft Azure SQL Database에서 제공됩니까?

SQL Database 및 SQL Data Warehouse에서 제공되는 기본 인증 방법은 Azure Active Directory 인증 및 SQL 인증입니다. Azure Active Directory(AD)는 중앙 집중화된 ID 및 액세스 관리 서비스이며, SQL은 단지 Azure AD와 통합된 여러 Azure 서비스 중 하나일 뿐입니다. 중앙 집중식으로 관리되는 서비스의 이점은 더 간단한 인증을 위해 사용자의 자격 증명이 사용되는 모든 Azure 서비스에서 공유된다는 점입니다. 또한 Microsoft Azure SQL Database 및 SQL Data Warehouse에서 Azure AD 도메인 내에서 다단계 인증 및 게스트 사용자 계정을 제공할 수 있습니다. 

이미 Active Directory 온-프레미스가 있는 경우, Azure Active Directory로 디렉터리를 페더레이션하여 디렉터리를 Azure로 확장할 수 있습니다. 


|||
|---|---|
| 다음과 같은 경우입니다.|Azure SQL Database / Azure SQL Data Warehouse|
| Azure에서 Azure Active Directory(AD)를 사용하지 않는 것을 선호함|[SQL 인증](sql-database-security-overview.md) 사용|
| AD를 SQL Server 온-프레미스에 사용함|[AD를 Microsoft Azure Active Directory와 페더레이션](../active-directory/connect/active-directory-aadconnect.md)하고, Azure AD 인증을 사용합니다. 이런 경우에 Single Sign-On을 사용할 수 있습니다.|
| 다단계 인증(MFA)을 강제해야 합니다.|MFA를 [Microsoft Conditional Access](sql-database-conditional-access.md)를 통한 정책으로 필요하며 [MFA를 지원하는 Azure AD Universal 인증](sql-database-ssms-mfa-authentication.md)을 사용합니다.|
| Microsoft 계정(live.com, outlook.com) 또는 다른 도메인(gmail.com)의 게스트 계정이 있습니다.|Microsoft Azure SQL Database/Data Warehouse에서 [Azure AD Universal 인증](sql-database-ssms-mfa-authentication.md)을 사용하며, 이는 [Azure AD B2B Collaboration](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md)을 활용합니다.|
| 페더레이션된 도메인에서 Microsoft Azure Active Directory 자격 증명을 사용하여 Windows에 로그인|[Azure AD 통합 인증](sql-database-aad-authentication-configure.md)을 사용합니다.|
| Azure와 페더레이션되지 않은 도메인에서 자격 증명을 사용하여 Windows에 로그인|[Azure AD 암호 인증](sql-database-aad-authentication-configure.md)을 사용합니다.|
| 중간 계층 서비스를 Azure SQL Database 또는 Data Warehouse에 연결되게 합니다.|[Azure AD 토큰 인증](sql-database-aad-authentication-configure.md)을 사용합니다.
|||

### <a name="how-can-i-limit-access-to-sensitive-data-in-my-databases-from-the-application-side"></a>응용 프로그램 쪽에서 데이터베이스 내의 중요한 데이터에 액세스하는 것을 제한하려면 어떻게 할 수 있습니까? 

Microsoft Azure SQL Database에는 권한이 없는 사용자가 중요한 데이터를 보지 못하도록 방지하기 위한 몇 가지 옵션이 있습니다. 

- [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine)는 데이터베이스의 중요한 열을 암호화하는 클라이언트 쪽 암호화의 양식입니다. (따라서 데이터베이스 관리자와 권한이 없는 사용자에게 그것은 ciphertext에 있습니다.) Always Encrypted를 위한 키는 클라이언트 쪽에 저장되어 권한 있는 클라이언트만이 중요한 열을 암호 해독할 수 있습니다. Always Encrypted는 같음 비교를 지원하므로 DBA는 계속해서 암호화된 열을 해당 SQL 명령의 일환으로 쿼리할 수 있습니다. Always Encrypted는 [Azure Key Vault](sql-database-always-encrypted-azure-key-vault.md), Windows 인증서 저장소, 및 로컬 하드웨어 보안 모듈과 같은 다양한 키 저장소 옵션과 함께 사용될 수 있습니다.
- [동적 데이터 마스킹](sql-database-dynamic-data-masking-get-started.md)은 응용 프로그램 계층에서 권한이 없는 사용자로 마스킹하여 중요한 데이터 노출을 제한하는 데이터 마스킹 기능입니다. 마스킹 패턴(예를 들어, 국적 ID의 마지막 4자리만을 보여주고 나머지를 X로 표시함)을 만들 수 있는 마스킹 규칙을 정의하고 마스킹 규칙에서 어떤 사용자를 제외할 수 있는지 식별합니다.
- [Row-Level Security](/sql/relational-databases/security/row-level-security)를 사용하면 쿼리를 실행하는 사용자(그룹 멤버 자격 또는 실행 컨텍스트)에 기초하여 데이터베이스 테이블의 행에 대한 액세스를 제어할 수 있습니다. 액세스 제한은 앱 논리를 간소화하기 위해 응용 프로그램 계층 대신에 데이터베이스 계층에서 수행됩니다. 

### <a name="what-encryption-options-do-i-have-in-sql-database-and-what-actors-does-the-encryption-protect-from"></a>Microsoft Azure SQL Database에는 어떤 암호화 옵션이 있으며 암호화는 어떤 행위자로부터 보호합니까?
Microsoft Azure SQL Database에서 사용할 수 있는 세 가지 주요 암호화 기술이 있습니다. 
- [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine)(위의 질문에서 언급됨): 권한이 없는 클라이언트부터 실제 디스크에 이르기까지, 테이블 종단 간에 중요한 열을 암호화합니다. 암호화 키가 클라이언트에 저장되기 때문에 서버 관리자 및 데이터 관리자는 중요한 데이터를 볼 수 없습니다. 
- [투명한 데이터 암호화](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)(TDE): 휴지 상태의 암호화는 데이터베이스 수준에서 암호화하고 데이터 파일, 로그 파일 및 물리적 미디어 도용으로부터 연결된 백업을 보호합니다. 투명한 데이터 암호화는 새로 만들어진 모든 데이터베이스에 대해 기본적으로 사용하도록 설정됩니다.
 
  다음 다이어그램은 암호화 기술 선택의 개요를 보여줍니다.

   ![암호화 개요](./media/sql-database-manage-after-migration/overview-encryption.png)

### <a name="how-should-i-manage-encryption-keys-in-the-cloud"></a>클라우드의 암호화 키를 어떻게 관리해야 합니까? 
Always Encrypted (클라이언트 쪽 암호화) 및 투명한 데이터 암호화 (휴지 상태의 암호화)에 대한 키 관리 옵션이 있습니다. 암호화 키를 정기적으로 그리고 내부 규정 및 준수 요건과 일치하는 빈도로 회전하는 것이 좋습니다.

- **Always Encrypted**: Always Encrypted에는 [두 키 계층](/sql/relational-databases/security/encryption/overview-of-key-management-for-always-encrypted)이 있습니다 ‑ 중요한 데이터의 열은 AES 256 열 암호화 키(CEK)로 암호화되고, 이것은 다시 열 마스터 키(CMK)로 암호화됩니다. Always Encrypted에 대해 제공된 클라이언트 드라이버는 CMK 길이에 제한이 없습니다.

  암호화된 CEK 값은 데이터베이스에 저장되고, CMK는 Windows Certificate Store, Azure Key Vault 또는 하드웨어 보안 모듈 등과 같은 신뢰할 수 있는 키 저장소에 저장됩니다. 
  
  [CEK와 CMK](/sql/relational-databases/security/encryption/rotate-always-encrypted-keys-using-powershell)는 모두 회전할 수 있습니다. CEK 회전은 암호화된 열을 포함하는 테이블의 크기에 따라 시간이 많이 걸릴 수 있습니다. 따라서 CEK 회전을 매우 신중하게 계획합니다. 반면 CMK 회전은 데이터베이스 성능을 방해하지 않으며, 분리된 역할과 함께 수행될 수 있습니다.

  다음 다이어그램은 Always Encrypted에서 열 마스터 키에 대한 키 저장소 옵션을 보여줍니다. 

   ![Always Encrypted CMK 저장소 공급자](./media/sql-database-manage-after-migration/always-encrypted.png)

- **투명한 데이터 암호화 (TDE)**: TDE에는 두 키 계층이 있습니다 – 각 사용자 데이터베이스의 데이터는 대칭 AES-256 데이터베이스 고유 데이터베이스 암호화 키(DEK)에 의해 암호화되어 있으며, 또한 이 키는 서버 고유 비대칭 RSA 2048 마스터 키에 의해 암호화됩니다. 

  기본적으로 투명한 데이터 암호화를 위한 마스터 키는 편의를 위해 Microsoft Azure SQL Database 서비스에서 관리합니다. 조직에서 마스터 키를 제어하고 싶어 하는 경우, [Azure Key Vault](/sql/relational-databases/security/encryption/transparent-data-encryption-byok-azure-sql)를 키 저장소로 사용하는 옵션이 있습니다. 

  Azure Key Vault를 사용하여 조직은 키 프로비저닝, 회전 및 권한 제어에 관한 제어권을 갖습니다. [회전 또는 TDE 마스터 키 형식 전환](/sql/relational-databases/security/encryption/transparent-data-encryption-byok-azure-sql-key-rotation)은 DEK을 다시 암호화함으로 빠릅니다. 

  보안과 데이터 관리 간의 역할 구분이 있는 조직인 경우, 보안 관리자는 Azure Key Vault에서 TDE 마스터 키에 대한 핵심 자료를 프로비전하고, 서버에서 휴지 상태의 암호화를 사용하도록 데이터베이스 관리자에게 Azure Key Vault 키 식별자를 제공합니다. 

## <a name="monitoring-and-compliance-after-migration"></a>마이그레이션 후 모니터링 및 규정 준수

### <a name="how-do-i-monitor-database-activities-in-sql-database"></a>Microsoft Azure SQL Database에서 데이터베이스 작업을 모니터링 하려면 어떻게 해야 합니까?
Microsoft Azure SQL Database에 기본 제공되는 모니터링 기능이 몇 가지 있으며, 데이터베이스에서 보안 및 기타 여러 이벤트를 트랙합니다.
- [SQL 감사](sql-database-auditing.md)를 통해 자신의 Azure Storage 계정에서 데이터베이스 이벤트의 감사 로그를 수집할 수 있습니다.
- [SQL 위협 요소 탐지](sql-database-threat-detection.md)를 사용하면 데이터베이스의 데이터를 액세스, 침범 또는 악용하려는 가능한 악의적인 의도를 나타내는 의심스러운 활동을 탐지할 수 있습니다. SQL Database 위협 요소 탐지는 잠재적인 취약점 및 SQL 삽입 공격뿐만 아니라 비정상 데이터베이스 액세스 패턴(예: 비정상적인 위치에서나 알 수 없는 보안 주체의 액세스)을 탐지하는 여러 알고리즘 집합을 실행합니다. 보안 책임자 또는 지정된 다른 관리자는 데이터베이스에서 위협이 감지되면 전자 메일 알림을 받게 됩니다. 각 알림에서는 의심스러운 활동에 대한 세부 정보와 해당 위협을 자세히 조사하고 완화하는 방법에 대한 권장 사항을 제공합니다. 
- [SQL 취약점 평가](sql-vulnerability-assessment.md)는 데이터베이스의 보안 상태를 대규모로 모니터링하고 보안 위험을 파악하고 사용자가 정의한 보안 기준에서 드리프트할 수 있도록 하는 데이터베이스 검사 및 보고 서비스입니다. 모든 검사 후 실행 가능한 단계 및 재구성 스크립트의 사용자 지정된 목록이 규정 준수를 충족하는 데 사용할 수 있는 평가 보고서와 함께 제공됩니다. 
- [SQL-OMS 보안 동기화 응용 프로그램](https://github.com/Microsoft/Azure-SQL-DB-auditing-OMS-integration)은 Log Analytics 및 다음을 포함한 사용자 지정 감지 경고를 정의하는 기능에 대한 SQL 감사 로그를 OMS에 푸시하기 위해 Operations Management Suite(OMS) 공용 API를 활용합니다. 
 - SQL Database Audit 타일 및 대시보드는 데이터베이스 작업에 관한 분명하고 일관성 있는 보고서를 제공합니다. 
 - SQL Log Analytics는 데이터베이스 작업을 분석하고 의심스러운 보안 위반을 나타낼 수 있는 불일치 및 이상 활동을 조사합니다.
 - 전자 메일, Webhook 및 Azure Automation Runbook 경고(즉, 암호 변경, 업무 시간 외, 특정 SQL 명령)를 트리거하는 관찰된 이벤트에 대한 고급 경고 특정 규칙.
- [Azure Security Center](../security-center/security-center-intro.md)는 Azure, 온-프레미스 및 기타 클라우드에서 실행되는 작업 전반에 걸친 중앙 집중식 보안 관리를 제공합니다. 감사, 투명한 데이터 암호화와 같은 필수 SQL Database 보호가 모든 리소스에 구성되었는지 보고, 고유의 요구 사항에 따라 정책을 만들 수 있습니다. 

### <a name="is-sql-database-compliant-with-any-regulatory-requirements-and-how-does-that-help-with-my-own-organizations-compliance"></a>Microsoft Azure SQL Database는 모든 규격 요건을 준수하며, 그것이 나만의 조직 규정 준수에 어떤 도움이 됩니까? 
Azure SQL Database는 다양한 규격을 준수합니다. 충족된 최신 규격 집합을 보려면 [Microsoft Trust Center](https://www.microsoft.com/trustcenter/compliance/complianceofferings)를 방문하여 Azure SQL Database가 호환되는 Azure 서비스에 포함되어 있는지 확인하기 위해 조직에 중요한 규정 준수에 대해 드릴다운합니다. Azure SQL Database가 규격 서비스로 인증된다 하더라도, 조직 서비스의 준수를 지원하지만 자동으로 보장하지는 않는다는 점을 확인하는 것이 중요합니다. 

## <a name="database-maintenance-and-monitoring-after-migration"></a>마이그레이션 후 데이터베이스 유지 관리 및 모니터링

### <a name="how-do-i-monitor-growth-in-data-size-and-resource-utilization"></a>데이터 크기 및 리소스 사용률의 증가를 모니터링하려면 어떻게 해야 합니까?

- Azure Portal의 '모니터링' 차트에서 데이터베이스 크기 및 리소스 사용률에 관한 메트릭을 모니터링할 수 있습니다. 

  ![모니터링 차트](./media/sql-database-manage-after-migration/monitoring-chart.png)

- 더 깊은 통찰력을 얻고 쿼리의 세부 정보를 심층적으로 분석하기 위해 Azure Portal에서 제공되는 'Query Performance Insight'를 사용할 수 있습니다. '쿼리 저장소'가 데이터베이스에 활성화되어 있어야 합니다.

  ![Query Performance Insight](./media/sql-database-manage-after-migration/query-performance-insight.png)

- 또는 DMV(동적 관리 뷰)를 사용하여 ‑ [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) 및 [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)를 사용하여 메트릭을 볼 수 있습니다. 

### <a name="how-often-do-i-need-to-run-consistency-checks-like-dbcccheckdb"></a>DBCC_CHECKDB와 같은 일관성 검사는 얼마나 자주 실행해야 합니까?
DBCC_CHECKDB는 데이터베이스에 있는 모든 개체의 논리적 및 물리적 무결성을 검사합니다. Azure에서 Microsoft가 관리하므로 이러한 검사를 더 이상 수행할 필요가 없습니다. 자세한 내용은 [Azure SQL Database에서 데이터 무결성](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/)을 참조하세요.

## <a name="monitor-performance-and-resource-utilization-after-migration"></a>마이그레이션 후 성능 및 리소스 사용률 모니터링

### <a name="how-do-i-monitor-performance-and-resource-utilization-in-azure-sql-database"></a>Azure SQL Database에서 성능 및 리소스 사용률을 모니터링하려면 어떻게 해야 합니까?
다음 방법을 사용하여 Azure SQL Database에서 성능 및 리소스 사용률을 모니터링할 수 있습니다.

- **Azure Portal**: Azure Portal은 개요 창에서 데이터베이스를 선택하고 차트를 클릭하여 단일 데이터베이스의 사용률을 보여줍니다. CPU 비율, DTU 비율, 데이터 IO 비율, 세션 비율 및 데이터베이스 크기 비율을 포함하는, 여러 가지 메트릭을 표시하도록 차트를 수정할 수 있습니다. 
  ![리소스 사용률](./media/sql-database-manage-after-migration/resource-utilization.png)

  또한 이 차트로부터 리소스로 경고를 구성할 수도 있습니다. 이러한 경고를 통해 사용자는 전자 메일로 리소스 조건에 응하거나, HTTPS/HTTP 끝점에 적거나, 작업을 수행할 수 있습니다. 자세한 내용은 [Azure SQL Database에서 데이터베이스 성능 모니터링](sql-database-single-database-monitor.md)을 참조하세요.

- **뷰**: 지난 1시간 동안의 리소스 소비 통계 기록을 반환하려면 [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) 동적 관리 뷰를, 그리고 지난 14일에 대한 기록을 반환하려면 [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) 시스템 카탈로그 뷰를 쿼리할 수 있습니다. 

- **Query Performance Insight**: [Query Performance Insight](sql-database-query-performance.md)를 사용하면 상위 리소스 소비량 쿼리 및 특정 데이터베이스에 대한 장기 실행 쿼리 기록을 볼 수 있습니다. 이 기능을 사용하려면 [쿼리 저장소](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store)가 데이터베이스에 대해 사용할 수 있도록 설정되고 활성화되어야 합니다.

- **Log Analytics에서 Azure SQL 분석(미리 보기)**: [Azure Log Analytics](../log-analytics/log-analytics-azure-sql.md)를 사용하면 작업 영역당 최대 150,000 Azure SQL Database 및 5,000 SQL Elastic Pools에 달하기까지 지원하는 주요 Azure SQL Azure 성능 메트릭을 수집하고 시각화할 수 있습니다. 그것을 사용하여 알림을 모니터링하고 받을 수 있습니다. 여러 Azure 구독 및 탄력적 풀에서 Azure SQL Database 및 탄력적 풀 메트릭을 모니터링할 수 있으며 응용 프로그램 스택의 각 계층에서 문제를 식별하는 데 사용할 수 있습니다. 

### <a name="how-do-i-ensure-i-am-using-the-appropriate-service-tier-and-performance-level"></a>적절한 서비스 계층 및 성능 수준을 사용하고 있는지 확인하려면 어떻게 해야 합니까?
CPU, I/O 및 메모리 사용량을 이해하기 위해 [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) 및 [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) 동적 관리 뷰를 모니터링합니다. 또한 SQL Database [Query Performance Insight](sql-database-query-performance.md)를 사용하면 리소스 사용량을 볼 수 있습니다. 사용 가능한 리소스를 일관되게 높은 비율로 실행하는 경우 기존 서비스 계층 내에서 더 높은 성능 수준으로 이동하거나 더 높은 서비스 계층으로 이동해야 합니다. 반대로 사용 가능한 리소스를 일관되게 낮은 비율로만 사용할 경우 더 낮은 성능 수준이나 서비스 계층으로 이동하는 것이 좋습니다.

### <a name="i-am-seeing-performance-issues-how-does-my-azure-sql-database-troubleshooting-methodology-differ-from-sql-server"></a>성능 문제를 표시합니다. 사용자의 Azure SQL Database 문제 해결 방법은 SQL Server와는 어떻게 다릅니까?
성능 문제 해결 방법의 많은 측면이 Azure SQL Database에서 그대로 유지되지만, 약간의 차이는 있을 것입니다. 예를 들어 전반적인 성능 저하가 보인다면 CPU, I/O 및 메모리 사용량을 이해하기 위해 [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) 및 [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) 동적 관리 뷰를 모니터링합니다. 워크로드 수요에 따라 성능 수준 및/또는 서비스 계층을 변경해야 합니다.
성능 문제 조정에 대한 포괄적인 권장 사항은 [Azure SQL Database의 성능 조정](sql-database-performance-guidance.md)을 참조하세요. 

### <a name="do-i-need-to-maintain-indexes-and-statistics"></a>인덱스와 통계를 유지 관리해야 합니까?
Azure SQL Database는 인덱스와 통계를 서비스의 일환으로 자동 유지하지 않습니다. 사용자에게 인덱스 및 통계의 유지 관리를 예약할 책임이 있습니다. 다음 문서 Azure Automation Methods는 Azure SQL Database에 대한 유지 관리 작업을 예약하기 위한 몇 가지 옵션을 자세히 설명합니다.

## <a name="data-movement-after-migration"></a>마이그레이션 후 데이터 이동

### <a name="how-do-i-export-and-import-data-as-bacpac-files-from-azure-sql-database"></a>Azure SQL Database에서 데이터를 BACPAC 파일로 내보내기 및 가져오기를 하려면 어떻게 해야 합니까? 

- **내보내기**: Azure Portal에서 Azure SQL Database를 BACPAC 파일로 내보낼 수 있습니다.

  ![BACPAC으로 내보내기](./media/sql-database-export/database-export.png)

- **가져오기**: Azure Portal을 사용하여 데이터베이스를 BACPAC 파일로 가져올 수 있습니다.

  ![BACPAC 가져오기](./media/sql-database-import/import.png)

### <a name="how-do-i-synchronize-data-between-azure-sql-database-sql-server-2016--2012"></a>Azure SQL Database SQL Server 2016 / 2012 간의 데이터 동기화를 어떻게 해야 합니까?
[데이터 동기화](sql-database-sync-data.md) 기능을 사용하면 여러 온-프레미스 SQL Server 데이터베이스와 Azure SQL Database 간에 양방향으로 데이터를 동기화할 수 있습니다. 그러나 트리거 기반이므로, 결과적 일관성은 보장되지만(데이터 손실 없음), 트랜잭션 일관성은 보장되지 않습니다. 

## <a name="next-steps"></a>다음 단계
[Azure SQL Database](sql-database-technical-overview.md)에 대해 알아봅니다.
