---
title: TDE - Azure Key Vault 통합 또는 BYOK(Bring Your Own Key) - Azure SQL Database| Microsoft Docs
description: SQL Database 및 Data Warehouse에서 Azure Key Vault를 통해 BYOK(Bring Your Own Key)를 TDE(투명한 데이터 암호화)에 지원합니다. BYOK를 통한 TDE 개요, 이점, 작동 방법, 고려 사항 및 권장 사항을 설명합니다.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aliceku
ms.author: aliceku
ms.reviewer: vanto
manager: craigg
ms.date: 04/19/2019
ms.openlocfilehash: c3a29c6b4d0308b41e29f38fc29d79634727d593
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64926013"
---
# <a name="azure-sql-transparent-data-encryption-with-customer-managed-keys-in-azure-key-vault-bring-your-own-key-support"></a>Azure Key Vault의 고객 관리형 키를 사용하여 Azure SQL 투명한 데이터 암호화: Bring Your Own Key 지원

[TDE(투명한 데이터 암호화)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption)와 Azure Key Vault를 통합하면 TDE 보호기라는 고객 관리형 비대칭 키를 사용하여 DEK(데이터베이스 암호화 키)를 암호화할 수 있습니다. 일반적으로 투명한 데이터 암호화를 위한 BYOK(Bring Your Own Key) 지원이라고도 합니다.  BYOK 시나리오에서 TDE 보호기는 고객이 소유하고 관리하는 Azure 클라우드 기반 외부 키 관리 시스템인 [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault)에 저장됩니다. TDE 보호기 수 [생성](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates) key vault에서 또는 [전송](https://docs.microsoft.com/azure/key-vault/key-vault-hsm-protected-keys) 온-프레미스 HSM 장치에서 키 자격 증명 모음입니다. 데이터베이스의 부팅 페이지에 저장된 TDE DEK는 항상 Azure Key Vault에 저장되어 있는 TDE 보호기를 통해 암호화 및 암호 해독됩니다.  SQL Database가 DEK를 암호화 및 암호 해독하려면 고객 소유의 Key Vault에 대한 권한이 부여되어야 합니다. Key Vault에 대한 논리 SQL 서버의 권한이 철회되면 데이터베이스에 액세스할 수 없게 되고 모든 데이터가 암호화됩니다. Azure SQL Database의 경우 TDE 보호기는 논리 SQL 서버 수준에서 설정되며 해당 서버와 연결된 모든 데이터베이스에서 상속됩니다. [Azure SQL Managed Instance](https://docs.microsoft.com/azure/sql-database/sql-database-howto-managed-instance)의 경우 TDE 보호기는 인스턴스 수준에서 설정되며 해당 인스턴스의 모든 *암호화된* 데이터베이스에 의해 상속됩니다. *서버*라는 용어는 달리 언급하지 않는 한, 이 문서 전체에서 서버와 인스턴스를 모두 나타냅니다.

> [!NOTE]
> Azure SQL Database Managed Instance에 Azure Key Vault 통합 (Bring Your Own Key)를 사용 하 여 투명 한 데이터 암호화 미리 보기입니다.

TDE와 Azure Key Vault가 통합되면 사용자는 키 회전, Key Vault 권한, 키 백업을 포함한 키 관리 작업을 제어하고, Azure Key Vault 기능을 사용하여 모든 TDE 보호기에 감사/보고를 사용하도록 설정할 수 있습니다. Key Vault는 중앙 집중식 키 관리를 제공하고, 철저하게 모니터링되는 HSM(하드웨어 보안 모듈)을 활용하고, 보안 정책 규정을 준수하도록 키 관리와 데이터 관리의 책임을 분리합니다.  

TDE와 Azure Key Vault를 통합하면 다음과 같은 이점이 있습니다.

- TDE 보호기를 자체 관리하는 기능을 통해 향상된 투명성과 세분화된 제어
- 언제든지 데이터베이스에 액세스할 수 없도록 권한을 철회하는 기능
- Key Vault에 호스팅하여 TDE 보호기를 중앙에서 관리(다른 Azure 서비스에서 사용되는 다른 키 및 비밀 포함)
- 조직 내에서 키 및 데이터 관리 책임을 분리하여 역할 분리 지원
- Key Vault가 Microsoft에서 암호화 키를 보거나 추출하지 못하도록 설계되었으므로 사용자 고유의 클라이언트에서 더 많은 신뢰 획득
- 키 회전 지원

> [!IMPORTANT]
> Key Vault를 사용하여 시작하려는 사용자가 서비스 관리 TDE를 사용하는 경우 Key Vault에서 TDE 보호기로 전환하는 과정에서 TDE는 활성화된 상태로 유지됩니다. 데이터베이스 파일의 가동 중지 시간 또는 다시 암호화가 없습니다. 서비스 관리 키에서 Key Vault 키로 전환하는 경우 빠른 온라인 작업인 DEK(데이터베이스 암호화 키)만 다시 암호화하면 됩니다.

## <a name="how-does-tde-with-azure-key-vault-integration-support-work"></a>Azure Key Vault와 통합된 TDE는 어떻게 작업을 지원하나요?

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Azure SQL Database, Azure Resource Manager PowerShell 모듈은 계속 지원 하지만 Az.Sql 모듈에 대 한 모든 향후 개발 됩니다. 이러한 cmdlet에 대 한 참조 [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)합니다. Az 모듈에는 AzureRm 모듈의 명령에 대 한 인수를 실질적으로 동일합니다.

![Key Vault에 대한 서버 인증](./media/transparent-data-encryption-byok-azure-sql/tde-byok-server-authentication-flow.PNG)

TDE가 처음으로 Key Vault에서 TDE 보호기를 사용하도록 구성되면 서버에서 키 래핑을 요청하기 위해 TDE 사용 데이터베이스 각각의 DEK를 Key Vault에 보냅니다. Key Vault는 사용자 데이터베이스에 저장된 암호화된 데이터베이스 암호화 키를 반환합니다.  

> [!IMPORTANT]
> **TDE 보호기가 Azure Key Vault에 저장되면 Azure Key Vault를 벗어나지 않는 것**이 중요합니다. 서버는 Key Vault 내의 TDE 보호기 키 자료에만 키 작업 요청을 보낼 수 있으며 **TDE 보호기에 액세스하거나 캐시하지 않습니다**. Key Vault 관리자는 언제든지 서버의 Key Vault 권한을 철회할 수 있습니다. 이 경우 서버에 대한 모든 연결이 끊어집니다.

## <a name="guidelines-for-configuring-tde-with-azure-key-vault"></a>Azure Key Vault와 통합된 TDE 구성 지침

### <a name="general-guidelines"></a>일반적인 지침

- Azure Key Vault와 Azure SQL Database/Managed Instance는 동일한 테넌트에 있어야 합니다.  테넌트 간 키 자격 증명 모음 및 서버 상호 작용은 **지원되지 않습니다**.
- 필요한 리소스에 사용할 구독을 결정합니다. 나중에 구독 간에 서버를 이동하려면 BYOK 기반 TDE를 새로 설정해야 합니다. [리소스 이동](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)에 대해 자세히 알아보세요.
- Azure Key Vault와 통합된 TDE를 구성할 때 반복되는 래핑/래핑 해제 작업을 통해 자격 증명 모음에 배치되는 로드를 고려해야 합니다. 예를 들어 SQL Database 서버와 연결된 모든 데이터베이스에서 동일한 TDE 보호기를 사용하므로 해당 서버의 장애 조치(failover)는 서버에 있는 데이터베이스만큼 자격 증명 모음에 대한 키 작업을 트리거합니다. 경험과 문서화된 [키 자격 증명 모음 서비스 제한](https://docs.microsoft.com/azure/key-vault/key-vault-service-limits)을 기반으로 하여 단일 구독에서 최대 500개의 표준/범용 또는 200개의 프리미엄/중요 비즈니스용 데이터베이스를 하나의 Azure Key Vault에 연결하여 자격 증명 모음의 TDE 보호기에 액세스할 때 고가용성을 일관되게 보장하는 것이 좋습니다.
- 권장: 온-프레미스에 TDE 보호기 복사본을 유지합니다.  이렇게 하려면 HSM 디바이스에서 TDE 보호기를 로컬로 만들고, 키 에스크로 시스템에서 TDE 보호기의 로컬 복사본을 저장해야 합니다.  [로컬 HSM에서 Azure Key Vault로 키를 전송하는 방법](https://docs.microsoft.com/azure/key-vault/key-vault-hsm-protected-keys)을 알아봅니다.
- 기존 구성 문제 참조 [문제를 해결 하는 TDE](https://docs.microsoft.com/sql/relational-databases/security/encryption/troubleshoot-tde)

### <a name="guidelines-for-configuring-azure-key-vault"></a>Azure Key Vault 구성 지침

- 키 또는 키 자격 증명 모음을 실수로 삭제하는 경우 데이터가 손실되지 않도록 방지하기 위해 [일시 삭제](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)를 사용하도록 설정된 키 자격 증명 모음을 만듭니다.  [PowerShell을 사용하여 키 자격 증명 모음에서 "일시 삭제" 속성을 사용하도록 설정](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell)해야 합니다. 이 옵션은 AKV 포털에서 아직 사용할 수 없지만 SQL에서 필요합니다.  
  - 일시 삭제된 리소스는 복구되거나 제거되지 않는 한 설정된 기간(90일) 동안 보존됩니다.
  - **복구** 및 **제거** 작업은 Key Vault 액세스 정책에 연결된 자체 권한이 있습니다.
- 이 중요한 리소스를 삭제할 수 있는 사용자를 제어하고 실수 또는 무단으로 삭제하지 못하도록 방지하기 위해 키 자격 증명 모음에 리소스 잠금을 설정합니다.  [리소스 잠금에 대한 자세한 정보](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources)

- Azure AD(Azure Active Directory) ID를 사용하여 키 자격 증명 모음에 대한 액세스 권한을 SQL Database 서버에 부여합니다.  포털 UI를 사용하면 Azure AD ID가 자동으로 만들어지고 키 자격 증명 모음 액세스 권한이 서버에 부여됩니다.  PowerShell을 사용하여 BYOK 기반 TDE를 구성하면 Azure AD ID가 만들어져야 하고 완료가 확인되어야 합니다. PowerShell을 사용하는 경우 자세한 단계별 지침은 [BYOK 기반 TDE 구성](transparent-data-encryption-byok-azure-sql-configure.md) 및 [Managed Instance에 대해 BYOK 기반 TDE 구성](https://aka.ms/sqlmibyoktdepowershell)을 참조하세요.

  > [!NOTE]
  > 하는 경우 Azure AD Id **실수로 삭제 되거나 서버의 권한이 철회 되 면** 키 자격 증명 모음의 액세스 정책을 사용 하 여 실수로 서버를 다른 구독으로 이동 하 여 서버를 잃을 key vault에 대 한 액세스 또는 및 24 시간 이내 TDE로 암호화 된 데이터베이스에 액세스할 수 없습니다.  

- Azure Key Vault와 함께 방화벽 및 가상 네트워크를 사용하는 경우 다음을 구성해야 합니다. 
  - 신뢰할 수 있는 Microsoft 서비스가 이 방화벽을 바이패스하도록 허용 - 예 선택

  > [!NOTE]
  > TDE 암호화 된 SQL database 방화벽을 우회 하지 때문에 key vault에 액세스 하지 못할 경우 24 시간 내의 데이터베이스에 액세스할 수 없습니다.

- 모든 암호화 키에 대한 감사 및 보고 사용: Key Vault는 다른 SIEM(보안 정보 및 이벤트 관리) 도구에 쉽게 삽입되는 로그를 제공합니다. Operations Management Suite (OMS) [Azure Monitor 로그](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-key-vault) 는 이미 통합 되어 있는 서비스의 한 예입니다.
- 암호화된 데이터베이스의 고가용성을 보장하려면 서로 다른 지역에 있는 두 개의 Azure Key Vault로 각 SQL Database 서버를 구성합니다.

### <a name="guidelines-for-configuring-the-tde-protector-asymmetric-key"></a>TDE 보호기(비대칭 키) 구성을 위한 지침

- 로컬 HSM 디바이스에 로컬로 암호화 키를 만듭니다. Azure Key Vault에 저장할 수 있도록 비대칭 RSA 2048 키인지 확인합니다.
- 키 에스크로 시스템에서 키를 에스크로합니다.  
- 암호화 키 파일(.pfx, .byok 또는 .backup)을 Azure Key Vault로 가져옵니다.

   > [!NOTE]
   > 그러나 테스트를 위해 Azure Key Vault로 키를 만들 수는 있지만 프라이빗 키가 키 자격 증명 모음을 벗어날 수 없으므로 이 키는 에스크로할 수 없습니다.  키가 손실되면(키 자격 증명 모음에서 실수로 인한 삭제, 만료 등) 영구적인 데이터 손실이 발생하므로 프로덕션 데이터를 암호화하는 데 사용되는 키는 항상 백업하고 에스크로합니다.

- 만료 날짜가 없는 키를 사용하고, 이미 사용 중인 키에는 만료 날짜를 설정하지 마세요. **키가 만료되면 암호화된 데이터베이스는 TDE 보호기에 대한 액세스 권한을 잃고 24시간 내에 액세스할 수 없게 됩니다**.
- 키를 사용하도록 설정되어 있고 *가져오기*, *키 래핑* 및 *키 래핑 해제* 작업을 수행할 수 있는 권한이 있는지 확인합니다.
- Azure Key Vault에서 처음으로 키를 사용하기 전에 Azure Key Vault 키 백업을 만듭니다. 에 대 한 자세한 정보를 [백업 AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey) 명령입니다.
- 키가 변경될 때마다(예: ACL 추가, 태그 추가, 키 특성 추가) 새 백업을 만듭니다.
- 키를 회전할 때 키 자격 증명 모음에서 **이전 버전의 키를 유지**합니다. 그러면 이전 데이터베이스 백업을 복원할 수 있습니다. 데이터베이스에 대한 TDE 보호기가 변경되면 데이터베이스의 이전 백업이 최신 TDE 보호기를 사용하도록 **업데이트되지 않습니다**.  각 백업에는 복원할 때 만든 TDE 보호기가 필요합니다. 키 회전은 [PowerShell을 사용하여 투명한 데이터 암호화 방지 프로그램 회전](transparent-data-encryption-byok-azure-sql-key-rotation.md)에 나와 있는 지침에 따라 수행할 수 있습니다.
- 서비스 관리 키로 다시 변경한 후에는 이전에 사용한 모든 키를 Azure Key Vault에 유지합니다.  이렇게 하면 Azure Key Vault에 저장된 TDE 보호기를 사용하여 데이터베이스 백업을 복원할 수 있습니다.  Azure Key Vault에서 만든 TDE 보호기는 서비스 관리 키를 사용하여 저장된 모든 백업이 만들어질 때까지 유지해야 합니다.  
- 사용 하 여 이러한 키의 복구 가능한 백업 복사본을 만듭니다 [백업 AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey)합니다.
- 데이터가 손실될 위험 없이 보안 인시던트 중에 잠재적으로 손상될 수 있는 키를 제거하려면 [잠재적으로 손상될 수 있는 키 제거](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md)의 단계를 따릅니다.

## <a name="high-availability-geo-replication-and-backup--restore"></a>고가용성, 지역 복제 및 백업/복원

### <a name="high-availability-and-disaster-recovery"></a>고가용성 및 재해 복구

Azure Key Vault를 사용하여 고가용성을 구성하는 방법은 데이터베이스와 SQL Database 서버의 구성에 따라 다르며, 다음 두 가지 경우에 권장되는 구성입니다.  첫 번째 경우는 지리적 중복이 구성되지 않은 독립 실행형 데이터베이스 또는 SQL Database 서버입니다.  두 번째 경우는 장애 조치(failover) 그룹 또는 지리적 중복으로 구성된 데이터베이스 또는 SQL Database 서버이며, 각 지리적 중복 복사본에는 지역 장애 조치(failover) 작업을 보장하기 위해 장애 조치(failover) 그룹 내에 로컬 Azure Key Vault가 있어야 합니다.

첫 번째 경우에서 지리적 중복이 구성되지 않은 데이터베이스와 SQL Database 서버의 고가용성이 필요한 경우 동일한 키 자료를 사용하여 서로 두 지역에서 서로 다른 두 개의 키 자격 증명 모음을 사용하도록 서버를 구성하는 것이 좋습니다. 이 작업은 SQL Database 서버와 동일한 지역에 공동 배치된 기본 Key Vault를 통해 TDE 보호기를 만들고 다른 Azure 지역의 키 자격 증명 모음에 키를 복제하여, 데이터베이스가 가동되어 실행되는 동안 기본 키 자격 증명 모음이 중단되면 서버에서 두 번째 키 자격 증명 모음에 액세스할 수 있도록 함으로써 수행할 수 있습니다. 백업 AzKeyVaultKey cmdlet을 사용 하 여 기본 key vault에서 암호화 된 형식으로 키를 검색 및 그런 다음 복원 AzKeyVaultKey cmdlet을 사용 하 고 두 번째 지역에서 key vault를 지정 합니다.

![단일 서버 HA 및 Geo-DR 없음](./media/transparent-data-encryption-byok-azure-sql/SingleServer_HA_Config.PNG)

## <a name="how-to-configure-geo-dr-with-azure-key-vault"></a>Azure Key Vault를 사용하여 Geo-DR(지역 재해 복구)을 구성하는 방법

암호화된 데이터베이스에 대한 TDE 보호기의 고가용성을 유지하려면 기존 또는 원하는 SQL Database 장애 조치 그룹 또는 활성 지역 복제 인스턴스를 기반으로 하여 중복 Azure Key Vault를 구성해야 합니다.  각 지역 복제 서버에는 동일한 Azure 지역의 서버와 공동 배치해야 하는 별도의 키 자격 증명 모음이 필요합니다. 한 지역에서 중단으로 인해 주 데이터베이스에 액세스할 수 없게 되고 장애 조치가 트리거되면, 보조 데이터베이스에서 보조 키 자격 증명 모음을 사용하여 인수할 수 있습니다.

지역 복제된 Azure SQL 데이터베이스에 필요한 Azure Key Vault 구성은 다음과 같습니다.

- 지역에 하나의 키 자격 증명 모음이 있는 주 데이터베이스 및 지역에 하나의 키 자격 증명 모음이 있는 하나의 보조 데이터베이스가 있습니다.
- 하나 이상의 보조 데이터베이스가 필요합니다(최대 4개 지원).
- 보조 데이터베이스의 보조 데이터베이스(체인)는 지원되지 않습니다.

다음 섹션에서는 설치 및 구성 단계에 대해 자세히 살펴봅니다

### <a name="azure-key-vault-configuration-steps"></a>Azure Key Vault 구성 단계

- 설치 [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps)
- PowerShell을 사용하여 서로 다른 두 역에 두 개의 Azure Key Vault를 만들어 [키 자격 증명 모음에서 "일시 삭제" 속성을 사용하도록 설정](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell)합니다. 이 옵션은 AKV 포털에서 아직 사용할 수 없지만 SQL에서 필요합니다.
- 키의 백업 및 복원이 작동하려면 두 개의 Azure Key Vault가 모두 동일한 Azure 지역에서 사용할 수 있는 두 지역에 있어야 합니다.  SQL Geo-DR 요구 사항에 따라 두 개의 Key Vault를 서로 다른 지역에 배치해야 하는 경우 온-프레미스 HSM에서 키를 가져올 수 있는 [BYOK 프로세스](https://docs.microsoft.com/azure/key-vault/key-vault-hsm-protected-keys)를 따릅니다.
- 첫 번째 키 자격 증명 모음에 다음과 같은 새 키를 만듭니다.  
  - RSA/RSA-HSA 2048 키
  - 만료 날짜 없음
  - 사용하도록 설정되고 가져오기, 키 래핑 및 키 래핑 해제 작업을 수행할 수 있는 권한이 있는 키입니다.
- 기본 키를 백업하고, 해당 키를 두 번째 키 자격 증명 모음에 복원합니다.  참조 [BackupAzureKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey) 하 고 [복원 AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultkey)합니다.

### <a name="azure-sql-database-configuration-steps"></a>Azure SQL Database 구성 단계

다음 구성 단계는 새 SQL 배포를 시작하는지, 아니면 기존 SQL Geo-DR 배포를 사용하여 작업하는지 여부에 따라 달라집니다.  먼저 새 배포의 구성 단계를 간략히 설명한 다음, Azure Key Vault에 저장된 TDE 보호기를 이미 Geo-DR 링크가 설정되어 있는 기존 배포에 할당하는 방법을 설명합니다.

**새 배포 단계**:

- 이전에 만든 키 자격 증명 모음과 동일한 두 지역에 두 개의 SQL Database 서버를 만듭니다.
- SQL Database 서버 TDE 창을 선택하고, 각 SQL Database 서버에 대해 다음을 수행합니다.  
  - 동일한 지역에서 AKV를 선택합니다
  - TDE 보호기로 사용할 키를 선택합니다. 각 서버는 TDE 보호기의 로컬 복사본을 사용합니다.
  - 포털에서 이 작업을 수행하면 SQL Database 서버에 대한 [AppID](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview)가 만들어집니다. 이 ID는 키 자격 증명 모음에 액세스할 수 있는 SQL Database 서버 권한을 할당하는 데 사용되므로 삭제하지 마세요. 액세스 권한은 SQL Database 서버 대신 Azure Key Vault에서 제거하여 철회할 수 있습니다. 이 권한은 키 자격 증명 모음에 액세스할 수 있는 SQL Database 서버 권한을 할당하는 데 사용됩니다.
- 주 데이터베이스를 만듭니다.
- [활성 지역 복제 지침](sql-database-geo-replication-overview.md)에 따라 시나리오를 완료합니다. 이 단계에서는 보조 데이터베이스가 만들어집니다.

![장애 조치 그룹 및 Geo-DR](./media/transparent-data-encryption-byok-azure-sql/Geo_DR_Config.PNG)

> [!NOTE]
> 데이터베이스 간에 지리적 연결을 설정하기 전에 동일한 TDE 보호기가 두 키 자격 증명 모음에 모두 있는지 확인해야 합니다.

**Geo-DR 배포가 있는 기존 SQL DB에 대한 단계**:

SQL Database 서버가 이미 있고 주 및 보조 데이터베이스가 이미 할당되어 있으므로 Azure Key Vault를 구성하는 단계는 다음 순서로 수행해야 합니다.

- 보조 데이터베이스를 호스트하는 SQL Database 서버에서 시작합니다.
  - 동일한 지역에 있는 키 자격 증명 모음 할당
  - TDE 보호기 할당
- 이제 주 데이터베이스를 호스트하는 SQL Database 서버로 이동합니다.
  - 보조 DB에 사용된 것과 동일한 TDE 보호기를 선택합니다.

![장애 조치 그룹 및 Geo-DR](./media/transparent-data-encryption-byok-azure-sql/geo_DR_ex_config.PNG)

>[!NOTE]
>키 자격 증명 모음을 서버에 할당할 때 보조 서버에서 시작해야 합니다.  두 번째 단계에서는 키 자격 증명 모음을 주 서버에 할당하고, TDE 보호기를 업데이트합니다. 이 시점에서 복제된 데이터베이스에서 사용되는 TDE 보호기를 두 서버에서 모두 사용할 수 있으므로 Geo-DR 링크가 계속 작동합니다.

Azure Key Vault에서 SQL Database Geo-DR 시나리오를 위해 고객 관리 키를 사용하여 TDE를 활성화하기 전에 SQL Database 지역 복제에 사용될 동일한 지역에 동일한 콘텐츠가 있는 두 개의 Azure Key Vault를 만들고 유지 관리해야 합니다.  "동일한 콘텐츠"는 특히 두 서버가 모든 데이터베이스에서 사용하는 TDE 보호기에 액세스할 수 있도록 두 개의 키 자격 증명 모음에 동일한 TDE 보호기 복사본이 있어야 한다는 것을 의미합니다.  더 나아가서, 두 개의 키 자격 증명 모음을 동기화 상태로 유지해야 합니다. 즉 키 회전 후 TDE 보호기의 동일한 복사본을 포함하고, 로그 파일 또는 백업에 사용된 이전 버전의 키를 유지해야 합니다. TDE 보호기는 동일한 키 속성을 유지해야 하며, 키 자격 증명 모음은 SQL에 대해 동일한 액세스 권한을 유지해야 합니다.  

[활성 지역 복제 개요](sql-database-geo-replication-overview.md)의 단계에 따라 장애 조치를 테스트하고 트리거합니다. 이 작업은 정기적으로 수행되어 두 개의 키 자격 증명 모음 모두에 대한 SQL 액세스 권한을 유지하고 있는지 확인해야 합니다.

### <a name="backup-and-restore"></a>Backup 및 Restore 메서드

Key Vault의 키를 사용하여 데이터베이스가 TDE를 통해 암호화되면 생성된 모든 백업도 동일한 TDE 보호기로 암호화됩니다.

Key Vault에서 TDE 보호기로 암호화된 백업을 복원하려면 키 자료가 원래의 자격 증명 모음에 원래의 키 이름으로 있는지 확인합니다. 데이터베이스에 대한 TDE 보호기가 변경되면 데이터베이스의 이전 백업이 최신 TDE 보호기를 사용하도록 **업데이트되지 않습니다**. 따라서 이전 버전의 모든 TDE 보호기를 Key Vault에 유지하여 데이터베이스 백업을 복원하는 것이 좋습니다.

백업을 복원하는 데 필요할 수 있는 키가 더 이상 원래의 키 자격 증명 모음에 없으면 다음 오류 메시지가 반환됩니다. "대상 서버 `<Servername>`은 <Timestamp #1> ~ <Timestamp #2> 사이에 생성된 모든 AKV Uris에 액세스할 수 없습니다. 모든 AKV URI를 복원한 후 작업을 다시 시도하십시오."

이 문제를 완화 하려면를 실행 합니다 [Get AzSqlServerKeyVaultKey](/powershell/module/az.sql/get-azsqlserverkeyvaultkey) (하지 않는 한 사용자가 삭제) 서버에 추가 된 Key Vault에서 키 목록을 반환 하는 cmdlet입니다. 모든 백업을 복원할 수 있도록 하려면 백업 대상 서버에서 이러한 모든 키에 액세스할 수 있는지 확인합니다.

```powershell
Get-AzSqlServerKeyVaultKey `
  -ServerName <LogicalServerName> `
  -ResourceGroup <SQLDatabaseResourceGroupName>
```

SQL Database 백업 복구에 대한 자세한 내용은 [Azure SQL 데이터베이스 복구](sql-database-recovery-using-backups.md)를 참조하세요. SQL Data Warehouse 백업 복구에 대한 자세한 내용은 [Azure SQL Data Warehouse 복구](../sql-data-warehouse/backup-and-restore.md)를 참조하세요.

백업된 로그 파일에 대한 추가 고려 사항: TDE 보호기가 회전되었고 데이터베이스에서 현재 새 TDE 보호기를 사용하고 있는 경우에도 백업된 로그 파일은 원래 TDE 암호기로 암호화된 상태로 유지됩니다.  복원할 때 데이터베이스를 복원하려면 두 키가 모두 필요합니다.  로그 파일에서 이 Azure Key Vault에 저장된 TDE 보호기를 사용하는 동안 데이터베이스에서 서비스 관리 TDE를 사용하도록 변경한 경우에도 복원할 때 이 키가 필요합니다.
