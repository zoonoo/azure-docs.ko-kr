---
title: 고객 관리 투명 데이터 암호화(TDE)
description: SQL 데이터베이스 및 Azure 시냅스용 Azure 키 자격 증명 모음을 사용하여 투명 데이터 암호화(TDE)에 대한 BYOK(사용자 고유의 키) 지원을 가져옵니다. BYOK를 통한 TDE 개요, 이점, 작동 방법, 고려 사항 및 권장 사항을 설명합니다.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-lt-2019, azure-synapse
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto
ms.date: 03/18/2020
ms.openlocfilehash: 462326fb16663a6f25ff4b51ea11791201086fd6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79528731"
---
# <a name="azure-sql-transparent-data-encryption-with-customer-managed-key"></a>고객 관리 키가 있는 Azure SQL 투명 데이터 암호화

고객 관리 키가 있는 Azure SQL [투명 데이터 암호화(TDE)를](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption) 사용하면 미사용 데이터 보호를 위한 BYOK(사용자 고유의 키) 시나리오를 가져올 수 있으며, 조직은 키 및 데이터 관리에서 업무 분리를 구현할 수 있습니다. 고객이 관리하는 투명한 데이터 암호화를 통해 고객은 키 수명 주기 관리(키 생성, 업로드, 회전, 삭제), 키 사용 권한 및 키에 대한 작업 감사를 전적으로 제어할 수 있습니다.

이 시나리오에서 TDE 프로텍터(TDE protector)라고 하는 데이터베이스 암호화 키(DEK)의 암호화에 사용되는 키는 클라우드 기반 외부 키 관리 시스템인 [AKV(고객](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault)소유 및 고객 관리 Azure Key Vault)에 저장된 고객 관리 비대칭 키입니다. Key Vault는 RSA 암호화 키에 대한 가용성과 확장 가능한 보안 스토리지로 FIPS 140-2 레벨 2 검증된 하드웨어 보안 모듈(HSM)에 의해 선택적으로 지원됩니다. 저장된 키에 대한 직접 액세스는 허용되지 않지만 권한이 있는 엔터티에 키를 사용하여 암호화/암호 해독 서비스를 제공합니다. 키는 키 자격 증명 모음에 의해 생성되거나, 가져오기하거나, [온-prem HSM 장치에서 키 자격 증명 모음으로 전송할](https://docs.microsoft.com/azure/key-vault/key-vault-hsm-protected-keys)수 있습니다.

Azure SQL 데이터베이스 및 Azure Synapse의 경우 TDE 보호기는 논리적 서버 수준에서 설정되며 해당 서버와 연결된 모든 암호화된 데이터베이스에 의해 상속됩니다. Azure SQL 관리 인스턴스의 경우 TDE 보호기는 인스턴스 수준에서 설정되며 해당 인스턴스의 모든 암호화된 데이터베이스에 의해 상속됩니다. *서버라는* 용어는 다르게 명시되지 않는 한 이 문서 전체에서 SQL Database 논리 서버와 관리되는 인스턴스를 모두 참조합니다.

> [!IMPORTANT]
> 고객 관리 TDE를 사용하기 를 원하는 서비스 관리 TDE를 사용하는 경우 전환 하는 과정에서 데이터가 암호화 된 상태로 유지 되며 데이터베이스 파일의 가동 중지 시간이나 다시 암호화가 없습니다. 서비스 관리 키에서 고객 관리 키로 전환하려면 DEK를 다시 암호화해야 하며, 이는 빠르고 온라인 작업입니다.

## <a name="benefits-of-the-customer-managed-tde"></a>고객 관리 TDE의 이점

고객 관리 TDE는 고객에게 다음과 같은 이점을 제공합니다.

- TDE 프로텍터의 사용 및 관리에 대한 완전하고 세분화된 제어;

- TDE 보호기 사용의 투명성;

- 조직 내의 키와 데이터의 관리에서 업무 분리를 구현할 수 있는 기능;

- 키 Vault 관리자는 키 액세스 권한을 취소하여 암호화된 데이터베이스에 액세스할 수 없도록 할 수 있습니다.

- AKV에서 키의 중앙 관리;

- AKV는 Microsoft가 암호화 키를 보거나 추출할 수 없도록 설계되었기 때문에 최종 고객으로부터 더 큰 신뢰를 받고 있습니다.

## <a name="how-customer-managed-tde-works"></a>고객 관리 TDE의 작동 방식

![고객 관리 TDE의 설정 및 작동](./media/transparent-data-encryption-byok-azure-sql/customer-managed-tde-with-roles.PNG)

서버가 DEK 암호화를 위해 AKV에 저장된 TDE 프로텍터를 사용할 수 있도록 하려면 키 자격 증명 모음 관리자는 고유한 AAD ID를 사용하여 서버에 다음과 같은 액세스 권한을 부여해야 합니다.

- **get** - 키 볼트에서 키의 공용 부품 및 속성을 검색하기 위한

- **wrapKey** - DEK를 보호 (암호화)할 수 있어야합니다.

- **언랩키** - DEK의 보호를 해제(암호 해독) 할 수 있음

키 볼트 관리자는 [키 볼트 감사 이벤트의 로깅을 활성화할](https://docs.microsoft.com/azure/azure-monitor/insights/azure-key-vault)수 있으므로 나중에 감사할 수 있습니다.

서버가 AKV에서 TDE 프로텍터를 사용하도록 구성된 경우 서버는 각 TDE 지원 데이터베이스의 DEK를 암호화를 위해 키 자격 증명 모음으로 보냅니다. 키 볼트는 암호화된 DEK를 반환한 다음 사용자 데이터베이스에 저장됩니다.

필요한 경우 서버는 암호 해독을 위해 보호된 DEK를 키 자격 증명 모음으로 보냅니다.

감사관은 Azure Monitor를 사용하여 로깅이 활성화된 경우 키 자격 증명 모음 감사Event 로그를 검토할 수 있습니다.

[!INCLUDE [sql-database-akv-permission-delay](includes/sql-database-akv-permission-delay.md)]

## <a name="requirements-for-configuring-customer-managed-tde"></a>고객 관리 TDE 구성 요구 사항

### <a name="requirements-for-configuring-akv"></a>AKV 구성 요구 사항

- 키 자격 증명 모음 및 SQL 데이터베이스/관리되는 인스턴스는 동일한 Azure Active Directory 테넌트에 속해야 합니다. 교차 테넌트 키 자격 증명 모음 및 서버 상호 작용은 지원되지 않습니다. 나중에 리소스를 이동하려면 AKV를 사용하여 TDE를 다시 구성해야 합니다. [리소스 이동에](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)대해 자세히 알아봅니다.

- 데이터 손실 실수로 키(또는 키 자격 증명 모음) 삭제가 발생하지 않도록 키 자격 증명 모음에서 [소프트 삭제](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) 기능을 활성화해야 합니다. 일시 삭제된 리소스는 그 동안 고객이 복구하거나 제거하지 않는 한 90일 동안 유지됩니다. *복구* 및 *제거* 작업에는 키 자격 증명 모음 액세스 정책과 연결된 고유 권한이 있습니다. 소프트 삭제 기능은 기본적으로 꺼져 있으며 [PowerShell](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell#enabling-soft-delete) 또는 [CLI를](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-cli#enabling-soft-delete)통해 활성화할 수 있습니다. Azure 포털을 통해 사용할 수 없습니다.  

- Azure Active Directory ID를 사용하여 SQL Database 서버 또는 관리되는 인스턴스 액세스를 키 자격 증명 모음(get, wrapKey, wrapKey) 해제키에 부여합니다. Azure 포털을 사용하는 경우 Azure AD ID가 자동으로 만들어집니다. PowerShell 또는 CLI를 사용하는 경우 Azure AD ID를 명시적으로 만들어야 하며 완료를 확인해야 합니다. PowerShell을 사용하는 경우 자세한 단계별 지침은 [BYOK 기반 TDE 구성](transparent-data-encryption-byok-azure-sql-configure.md) 및 [Managed Instance에 대해 BYOK 기반 TDE 구성](https://aka.ms/sqlmibyoktdepowershell)을 참조하세요.

- AKV와 방화벽을 사용하는 경우 신뢰할 수 있는 Microsoft 서비스 허용 옵션을 사용하여 *방화벽을 우회해야*합니다.

### <a name="requirements-for-configuring-tde-protector"></a>TDE 프로텍터 구성 요구 사항

- TDE 프로텍터는 비대칭, RSA 2048 또는 RSA HSM 2048 키일 수 있습니다.

- 키 활성화 날짜(설정된 경우)는 과거의 날짜및 시간여야 합니다. 만료 날짜(설정된 경우)는 미래의 날짜및 시간여야 합니다.

- 키는 *사용 가능한* 상태여야 합니다.

- 키 자격 증명 모음으로 기존 키를 가져오는 경우 지원되는 파일 형식(.pfx, .byok 또는 .backup)으로 제공해야 합니다.

## <a name="recommendations-when-configuring-customer-managed-tde"></a>고객 관리 TDE 를 구성할 때 권장 사항

### <a name="recommendations-when-configuring-akv"></a>AKV 를 구성할 때 권장 사항

- 서버가 키 자격 증명 모음에서 TDE 프로텍터에 액세스할 때 고가용성을 보장하기 위해 단일 구독의 키 자격 증명 모음과 총 500개의 범용 또는 200개의 비즈니스 크리티컬 데이터베이스를 연결합니다. 이 수치는 환경을 기반으로 하며 [키 자격 증명 모음 서비스 제한에](https://docs.microsoft.com/azure/key-vault/key-vault-service-limits)문서화되어 있습니다. 여기서의도는 서버 장애 조치 후 문제를 방지하는 것입니다.

- 키 자격 증명 모음에 리소스 잠금을 설정하여 이 중요한 리소스를 삭제하고 우발적이거나 무단으로 삭제되지 않도록 할 수 있는 사용자를 제어합니다. [리소스 잠금에](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources)대해 자세히 알아봅니다.

- 모든 암호화 키에 대한 감사 및 보고 사용: Key vault는 다른 보안 정보 및 이벤트 관리 도구에 쉽게 삽입할 수 있는 로그를 제공합니다. 운영 관리 제품군 [로그 분석은](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-key-vault) 이미 통합된 서비스의 한 예입니다.

- 각 서버를 서로 다른 지역에 상주하고 동일한 키 자료를 보유한 두 개의 키 자격 증명 모음으로 연결하여 암호화된 데이터베이스의 고가용성을 보장합니다. TDE 프로텍터와 동일한 지역의 키 자격 증명 모음의 키만 표시합니다. 같은 지역의 키 자격 증명 모음에 영향을 미치는 중단이 있는 경우 시스템이 원격 지역의 키 자격 증명 모음으로 자동으로 전환됩니다.

### <a name="recommendations-when-configuring-tde-protector"></a>TDE 프로텍터 구성 시 권장 사항
- TDE 보호기 사본을 안전한 장소에 보관하거나 에스크로 서비스로 에스크로합니다.

- 키 자격 증명 모음에서 키가 생성된 경우 AKV에서 키를 처음 사용하기 전에 키 백업을 만듭니다. 백업은 Azure 키 자격 증명 모음으로만 복원할 수 있습니다. [백업-AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey) 명령에 대해 자세히 알아보십시오.

- 키를 변경할 때마다(예: 키 속성, 태그, ACL)을 새로 만듭니다.

- 키를 회전할 때 키 자격 증명 모음에서 **이전 버전의 키를 유지**합니다. 그러면 이전 데이터베이스 백업을 복원할 수 있습니다. 데이터베이스에 대 한 TDE 프로텍터를 변경 하는 경우 데이터베이스의 이전 백업 최신 TDE 프로텍터를 사용 하 여 **업데이트 되지 않습니다.** 복원 시 각 백업에는 생성 시 암호화된 TDE 보호구가 필요합니다. 키 회전은 [PowerShell을 사용하여 투명한 데이터 암호화 방지 프로그램 회전](transparent-data-encryption-byok-azure-sql-key-rotation.md)에 나와 있는 지침에 따라 수행할 수 있습니다.

- 서비스 관리 키로 전환한 후에도 이전에 사용한 모든 키를 AKV에 보관합니다. AKV에 저장된 TDE 프로텍터를 사용하여 데이터베이스 백업을 복원할 수 있습니다.  Azure Key Vault로 만든 TDE 보호기는 서비스 관리 키로 저장된 모든 백업이 만들어질 때까지 유지 관리해야 합니다. [Backup-AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey)를 사용하여 이러한 키를 복구 가능한 백업 복사본으로 만듭니다.

- 데이터 손실의 위험 없이 보안 인시던트 중에 잠재적으로 손상될 수 있는 키를 제거하려면 [손상될 수 있는 키 제거의](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md)단계를 따릅니다.

## <a name="inaccessible-tde-protector"></a>액세스할 수 없는 TDE 프로텍터

투명한 데이터 암호화가 고객 관리 키를 사용하도록 구성된 경우 데이터베이스가 온라인 상태를 유지하려면 TDE 보호기에 대한 지속적인 액세스가 필요합니다. 서버가 AKV에서 고객 관리 TDE 보호기에 액세스할 수 없게 되면 최대 10분 안에 데이터베이스가 해당 오류 메시지와 의 모든 연결을 거부하기 시작하고 해당 상태를 *액세스할 수 없음으로*변경합니다. 액세스할 수 없는 상태의 데이터베이스에서 허용되는 유일한 작업은 삭제하는 것입니다.

> [!NOTE]
> 간헐적인 네트워킹 중단으로 인해 데이터베이스에 액세스할 수 없는 경우 아무 작업도 필요하지 않으며 데이터베이스가 자동으로 다시 온라인 상태가 됩니다.

키에 대한 액세스가 복원된 후 데이터베이스를 다시 온라인 으로 되돌리려면 추가 시간과 단계가 필요하며, 이는 키에 액세스하지 않고 경과된 시간과 데이터베이스의 데이터 크기에 따라 달라질 수 있습니다.

- 키 액세스가 8시간 이내에 복원되면 데이터베이스는 다음 시간 내에 자동으로 복구됩니다.

- 8시간 이상 이후에 키 액세스가 복원되면 자동 복구가 불가능하며 데이터베이스를 다시 가져오려면 포털에 대한 추가 단계가 필요하며 데이터베이스 크기에 따라 상당한 시간이 걸릴 수 있습니다. 데이터베이스가 다시 온라인 상태가 되면 [장애 조치 그룹](https://docs.microsoft.com/azure/sql-database/sql-database-auto-failover-group) 구성, 시간 내 복원 기록 및 태그와 같은 이전에 구성된 서버 수준 설정이 **손실됩니다.** 따라서 8시간 이내에 기본 키 액세스 문제를 식별하고 해결할 수 있는 알림 시스템을 구현하는 것이 좋습니다.

### <a name="accidental-tde-protector-access-revocation"></a>우발적인 TDE 보호기 액세스 해지

키 자격 증명 모음에 대한 충분한 액세스 권한을 가진 사람이 실수로 키에 대한 서버 액세스를 비활성화할 수 있습니다.

- 서버에서 키 볼트의 *get*, *wrapKey,* 랩키 권한 *의 래핑 해제 키* 권한 취소

- 키 삭제

- 키 볼트 삭제

- 키 볼트의 방화벽 규칙 변경

- Azure Active Directory에서 서버의 관리되는 ID 삭제

[데이터베이스에 액세스할 수 없게 되는 일반적인 원인에 대해](https://docs.microsoft.com/sql/relational-databases/security/encryption/troubleshoot-tde?view=azuresqldb-current#common-errors-causing-databases-to-become-inaccessible)자세히 알아봅니다.

## <a name="monitoring-of-the-customer-managed-tde"></a>고객 관리 TDE 모니터링

데이터베이스 상태를 모니터링하고 TDE 보호기 액세스 손실에 대한 경고를 사용하려면 다음 Azure 기능을 구성합니다.
- [Azure 리소스 상태](https://docs.microsoft.com/azure/service-health/resource-health-overview). TDE 보호기에 대한 액세스 권한을 잃은 액세스할 수 없는 데이터베이스는 데이터베이스에 대한 첫 번째 연결이 거부된 후 "사용할 수 없음"으로 표시됩니다.
- 고객 관리 키 자격 증명 모음의 TDE 보호기에 대한 액세스가 실패하면 [활동 로그가](https://docs.microsoft.com/azure/service-health/alerts-activity-log-service-notifications) 활동 로그에 추가됩니다.  이러한 이벤트에 대한 경고를 만들면 가능한 한 빨리 액세스를 복원할 수 있습니다.
- [작업 그룹은](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups) 환경 설정(예: 이메일/SMS/푸시/음성, 로직 앱, 웹후크, ITSM 또는 자동화 Runbook)에 따라 알림 및 알림을 전송하도록 정의할 수 있습니다.

## <a name="database-backup-and-restore-with-customer-managed-tde"></a>고객 관리 TDE로 데이터베이스 백업 및 복원

키 볼트의 키를 사용하여 데이터베이스가 TDE로 암호화되면 새로 생성된 백업도 동일한 TDE 프로텍터로 암호화됩니다. TDE 프로텍터가 변경되면 최신 TDE 프로텍터를 사용하도록 데이터베이스의 이전 **백업이 업데이트되지 않습니다.**

키 볼트에서 TDE 보호기로 암호화된 백업을 복원하려면 키 자료를 대상 서버에서 사용할 수 있는지 확인합니다. 따라서 데이터베이스 백업을 복원할 수 있도록 모든 이전 버전의 TDE 프로텍터를 키 자격 증명 모음에 보관하는 것이 좋습니다.

> [!IMPORTANT]
> 언제든지 서버에 대해 하나 이상의 TDE 프로텍터 집합이 있을 수 없습니다. Azure 포털 블레이드에서 "키를 기본 TDE 보호기로 만들기"로 표시된 키입니다. 그러나 여러 개의 추가 키가 TDE 프로텍터로 표시하지 않고 서버에 연결할 수 있습니다. 이러한 키는 DEK를 보호하는 데 사용되지 않지만 백업 파일이 해당 지문으로 키로 암호화된 경우 백업에서 복원하는 동안 사용할 수 있습니다.

백업을 복원하는 데 필요한 키가 대상 서버에서 더 이상 사용할 수 없는 경우 복원 시도에서 `<Servername>`> #2> \< \<#1 다음과 같은 오류 메시지가 반환됩니다. 모든 AKV URI를 복원한 후 작업을 다시 시도하십시오."

이를 완화하려면 대상 SQL Database 논리 서버에 대한 [Get-AzSqlServerKeyVaultKey](/powershell/module/az.sql/get-azsqlserverkeyvaultkey) cmdlet을 실행하거나 대상 관리 인스턴스에 대해 [Get-AzSqlInstanceKeyKeyKey를](/powershell/module/az.sql/get-azsqlinstancekeyvaultkey) 실행하여 사용 가능한 키 목록을 반환하고 누락된 키를 식별합니다. 모든 백업을 복원할 수 있도록 복원할 대상 서버가 필요한 모든 키에 액세스할 수 있는지 확인합니다. 이러한 키는 TDE 보호기로 표시할 필요가 없습니다.

SQL Database 백업 복구에 대한 자세한 내용은 [Azure SQL 데이터베이스 복구](sql-database-recovery-using-backups.md)를 참조하세요. SQL 풀에 대한 백업 복구에 대한 자세한 내용은 [SQL Pool 복구](../synapse-analytics/sql-data-warehouse/backup-and-restore.md)를 참조하십시오. 관리되는 인스턴스를 사용하여 SQL Server의 기본 백업/복원에 대한 [빠른 시작: 관리되는 인스턴스로 데이터베이스 복원](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started-restore)

로그 파일에 대한 추가 고려 사항: 백업된 로그 파일은 원래 TDE 프로텍터로 암호화된 상태로 유지되며, 로그 파일이 회전되었고 데이터베이스가 새 TDE 프로텍터를 사용하는 경우에도 마찬가지입니다.  복원할 때 데이터베이스를 복원하려면 두 키가 모두 필요합니다.  로그 파일이 Azure Key Vault에 저장된 TDE 보호기를 사용하는 경우, 그 동안 서비스 관리 TDE를 사용하도록 데이터베이스가 변경된 경우에도 복원 시 이 키가 필요합니다.

## <a name="high-availability-with-customer-managed-tde"></a>고객 관리 TDE로 고가용성

서버에 대해 구성된 지리적 중복성이 없는 경우에도 동일한 키 자료를 가진 두 개의 서로 다른 영역에서 두 개의 서로 다른 키 자격 증명 모음을 사용하도록 서버를 구성하는 것이 좋습니다. 서버와 동일한 지역에 있는 기본 키 자격 증명 모음을 사용하여 TDE 프로텍터를 만들고 키를 다른 Azure 지역의 키 자격 증명 모음으로 복제하여 서버가 기본 키 자격 증명 모음에 액세스할 수 있도록 할 수 있습니다. 데이터베이스가 가동되고 실행되는 동안 가동 이 중단될 수 있습니다.

Backup-AzKeyVaultKey cmdlet을 사용하여 기본 키 자격 증명 모음에서 암호화된 형식으로 키를 검색한 다음 Restore-AzKeyVaultKey cmdlet을 사용하고 두 번째 영역에서 키 자격 증명 모음을 지정하여 키를 복제합니다. 또는 Azure 포털을 사용하여 키를 백업하고 복원합니다. se 다른 지역의 보조 키 자격 증명 모음의 키는 TDE 프로텍터로 표시되어서는 안 되며 허용되지도 않습니다.

 주 키 자격 증명 모음에 영향을 미치는 중단이 있는 경우 시스템이 보조 키 자격 증명 모음에 동일한 지문이 있는 다른 연결된 키로 자동으로 전환됩니다( 있는 경우). TDE 프로텍터가 해지된 액세스 권한으로 인해 액세스할 수 없거나 고객이 의도적으로 서버의 키 액세스 권한을 제한하려고 했음을 나타낼 수 있으므로 키 또는 키 자격 증명 모음이 삭제된 경우 스위치가 발생하지 않습니다.

![단일 서버 HA](./media/transparent-data-encryption-byok-azure-sql/customer-managed-tde-with-ha.png)

## <a name="geo-dr-and-customer-managed-tde"></a>지오 DR 및 고객 관리 TDE

활성 [지역 복제](https://docs.microsoft.com/azure/sql-database/sql-database-active-geo-replication) 및 [장애 조치 그룹](https://docs.microsoft.com/azure/sql-database/sql-database-auto-failover-group) 시나리오에서 관련된 각 서버에는 동일한 Azure 지역의 서버와 함께 배치해야 하는 별도의 키 자격 증명 모음이 필요합니다. 고객은 키 볼트 전체에서 키 자료를 일관되게 유지하여 지역 내 정전으로 인해 기본 키에 액세스할 수 없게 되고 장애 조치(failover)가 트리거되는 경우 지역 키 자격 증명 모음에서 동일한 키를 사용할 수 있도록 합니다. . 최대 4개의 보조 를 구성할 수 있으며 체인(보조 보조 데이터베이스)은 지원되지 않습니다.

불완전한 키 자료로 인해 지역 복제를 설정하는 동안 또는 지역 복제 중에 문제를 방지하려면 고객 관리 TDE를 구성할 때 다음 규칙을 따르는 것이 중요합니다.

- 관련된 모든 키 자격 증명 모음에는 동일한 속성과 각 서버에 대한 동일한 액세스 권한이 있어야 합니다.

- 관련된 모든 키 볼트에는 동일한 키 재질이 포함되어야 합니다. 현재 TDE 프로텍터뿐만 아니라 백업 파일에 사용될 수 있는 모든 이전 TDE 보호기에도 적용됩니다.

- TDE 프로텍터의 초기 설정 및 회전은 보조 먼저 수행한 다음 기본에서 수행해야 합니다.

![장애 조치 그룹 및 Geo-DR](./media/transparent-data-encryption-byok-azure-sql/customer-managed-tde-with-bcdr.png)

장애 조치(failover)를 테스트하려면 [활성 지역 복제 개요의](sql-database-geo-replication-overview.md)단계를 따릅니다. 두 키 자격 증명 모음에 대한 SQL에 대한 액세스 권한이 유지되었는지 확인하기 위해 정기적으로 수행해야 합니다.

## <a name="next-steps"></a>다음 단계

또한 고객 관리 TDE를 사용하여 일반적인 작업에 대해 다음 PowerShell 샘플 스크립트를 확인할 수도 있습니다.

- [powerShell을 사용하여 SQL 데이터베이스에 대한 투명 데이터 암호화 프로텍터 회전](transparent-data-encryption-byok-azure-sql-key-rotation.md)

- [PowerShell을 사용하여 SQL 데이터베이스에 대한 투명 데이터 암호화(TDE) 보호기 제거](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-byok-azure-sql-remove-tde-protector)

- [PowerShell을 사용하여 자신의 키로 관리되는 인스턴스에서 투명한 데이터 암호화 관리](https://docs.microsoft.com/azure/sql-database/scripts/transparent-data-encryption-byok-sql-managed-instance-powershell?toc=%2fpowershell%2fmodule%2ftoc.json)
