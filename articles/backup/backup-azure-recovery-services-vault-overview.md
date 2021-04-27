---
title: Recovery Services 자격 증명 모음 개요
description: Recovery Services 자격 증명 모음에 대한 개요입니다.
ms.topic: conceptual
ms.date: 08/17/2020
ms.openlocfilehash: 2f2018f0f3d3135d632418c2e591e6ad938d62d2
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2021
ms.locfileid: "107517456"
---
# <a name="recovery-services-vaults-overview"></a>Recovery Services 자격 증명 모음 개요

이 문서에서는 Recovery Services 자격 증명 모음의 기능을 설명합니다. Recovery Services 자격 증명 모음은 Azure에서 데이터를 저장하는 스토리지 엔터티입니다. 데이터는 일반적으로 데이터의 복사본 또는 가상 머신(VM), 워크로드, 서버 또는 워크스테이션에 대한 구성 정보입니다. Recovery Services 자격 증명 모음을 사용하여 IaaS VM(Linux 또는 Windows) 및 Azure SQL 데이터베이스와 같은 다양한 Azure 서비스에 대한 백업 데이터를 저장할 수 있습니다. Recovery Services 자격 증명 모음은 System Center DPM, Windows Server, Azure Backup Server 등을 지원합니다. Recovery Services 자격 증명 모음을 사용하면 관리 오버헤드를 최소화하면서 백업 데이터를 쉽게 구성할 수 있습니다. Recovery Services 자격 증명 모음은 다음과 같은 기능을 제공하는 Azure의 Azure Resource Manager 모델을 기반으로 합니다.

- **백업 데이터 보호 기능 향상**: Recovery Services 자격 증명 모음에서 Azure Backup은 클라우드 백업을 보호하는 보안 기능을 제공합니다. 이러한 보안 기능을 통해 백업을 보호하고 프로덕션 및 백업 서버가 손상된 경우에도 데이터를 안전하게 복구할 수 있습니다. [자세한 정보](backup-azure-security-feature.md)

- **하이브리드 IT 환경을 위한 중심 모니터링**: Recovery Services 자격 증명 모음에서 [Azure IaaS VM](backup-azure-manage-vms.md)뿐만 아니라 중앙 포털에서 [온-프레미스 자산](backup-azure-manage-windows-server.md#manage-backup-items)도 모니터링할 수 있습니다. [자세한 정보](backup-azure-monitoring-built-in-monitor.md)

- **Azure RBAC(Azure 역할 기반 액세스 제어)** : Azure RBAC는 Azure에서 세밀한 액세스 관리 제어를 제공합니다. [Azure는 다양한 기본 제공 역할을 제공](../role-based-access-control/built-in-roles.md)하고 Azure Backup에는 3가지 [복구 지점을 관리하는 기본 제공 역할](backup-rbac-rs-vault.md)이 포함됩니다. Recovery Services 자격 증명 모음은 Azure RBAC와 호환되어 백업을 제한하고 정의된 집합의 사용자 역할에 대한 액세스를 복원합니다. [자세한 정보](backup-rbac-rs-vault.md)

- **일시 삭제**: 일시 삭제를 사용하면 악의적인 행위자가 백업을 삭제하거나 백업 데이터가 실수로 삭제된 경우에도 백업 데이터가 추가로 14일 동안 보관되므로 데이터 없이 해당 백업 항목을 복구할 수 있습니다. "일시 삭제" 상태의 백업 데이터에 대한 추가 보존 기간은 14일이며 비용이 발생하지 않습니다. [자세한 정보를 알아보세요](backup-azure-security-feature-cloud.md).

- **지역 간 복원**: CRR(지역 간 복원)을 사용하면 Azure 쌍을 이루는 지역인 보조 지역에서 Azure VM을 복원할 수 있습니다. [자격 증명 모음 수준](backup-create-rs-vault.md#set-cross-region-restore)에서 이 기능을 사용하면 언제든지 원하는 경우 보조 지역에서 복제된 데이터를 복원할 수 있습니다. 이렇게 하면 자격 증명 모음의 GRS 설정과 달리 Azure에서 재해를 선언할 때까지 기다리지 않고 감사 준수 및 중단 시나리오 동안 보조 지역 데이터를 복원할 수 있습니다. [자세한 정보를 알아보세요](backup-azure-arm-restore-vms.md#cross-region-restore).

## <a name="storage-settings-in-the-recovery-services-vault"></a>Recovery Services 자격 증명 모음의 스토리지 설정

Recovery Services 자격 증명 모음은 시간에 따라 생성된 모든 백업과 복구 지점을 저장하는 엔터티입니다. Recovery Services 자격 증명 모음에는 보호된 가상 머신과 연결된 백업 정책도 포함됩니다.

- Azure Backup는 자격 증명 모음에 대한 스토리지를 자동으로 처리합니다. [스토리지 설정 변경](./backup-create-rs-vault.md#set-storage-redundancy) 방법을 참조하세요.

- 스토리지 중복성에 대한 자세한 내용은 [지역](../storage/common/storage-redundancy.md#geo-zone-redundant-storage), [로컬](../storage/common/storage-redundancy.md#locally-redundant-storage) 및 [영역](../storage/common/storage-redundancy.md#zone-redundant-storage) 중복성에 대한 문서를 참조하세요.

## <a name="encryption-settings-in-the-recovery-services-vault"></a>Recovery Services 자격 증명 모음의 암호화 설정

이 섹션에서는 Recovery Services 자격 증명 모음에 저장된 백업 데이터를 암호화하는 데 사용할 수 있는 옵션을 설명합니다.

### <a name="encryption-of-backup-data-using-platform-managed-keys"></a>플랫폼 관리 키를 사용한 백업 데이터 암호화

기본적으로 모든 데이터는 플랫폼 관리 키를 사용하여 암호화됩니다. 이 암호화를 사용하도록 설정하기 위해 사용자 쪽에서 명시적인 작업을 수행할 필요는 없습니다. Recovery Services 자격 증명 모음에 백업되는 모든 워크로드에 적용됩니다.

### <a name="encryption-of-backup-data-using-customer-managed-keys"></a>고객 관리형 키를 사용하여 백업 데이터 암호화

사용자가 소유하고 관리하는 암호화 키를 사용하여 데이터를 암호화하도록 선택할 수 있습니다. Azure Backup를 사용하면 Azure Key Vault에 저장된 RSA 키를 사용하여 백업을 암호화할 수 있습니다. 백업 암호화에 사용되는 암호화 키는 원본에 사용된 것과 다를 수 있습니다. 데이터는 AES 256 기반 DEK(데이터 암호화 키)를 사용하여 보호되며, 이는 다시 사용자의 키를 사용하여 보호됩니다. 이를 통해 데이터와 키에 대한 모든 권한을 제공합니다. 암호화를 허용하려면 Recovery Services 자격 증명 모음에 Azure Key Vault의 암호화 키에 대한 액세스 권한을 부여해야 합니다. 필요할 때마다 키를 사용하지 않도록 설정하거나 액세스를 취소할 수 있습니다. 그러나 자격 증명 모음에 대한 항목을 보호하기 전에 키를 사용하여 암호화를 사용하도록 설정해야 합니다.

[고객 관리형 키를 사용하여](encryption-at-rest-with-cmk.md) 백업 데이터를 암호화하는 방법에 대해 자세히 알아보세요.

## <a name="azure-advisor"></a>Azure Advisor

[Azure Advisor](../advisor/index.yml)는 Azure 사용을 최적화하는 데 도움이 되는 개인화된 클라우드 컨설턴트입니다. Azure 사용량을 분석하고 배포를 최적화하고 보호하는 데 도움이 되는 시기적절한 권장 사항을 제공합니다. 고가용성, 보안, 성능 및 비용의 네 가지 범주로 권장 사항을 제공합니다.

Azure Advisor는 백업되지 않은 VM에 대해 매 시간 [권장 사항](../advisor/advisor-high-availability-recommendations.md#protect-your-virtual-machine-data-from-accidental-deletion)을 제공하므로 중요한 VM 백업을 놓치지 않습니다. 권장 사항을 다시 알려 제어할 수도 있습니다.  권장 사항을 선택하고 자격 증명 모음(백업이 저장될 위치) 및 백업 정책(백업 일정 및 백업 복사본 보존)을 지정하여 VM에서 인라인으로 백업을 사용하도록 설정할 수 있습니다.

![Azure Advisor](./media/backup-azure-recovery-services-vault-overview/azure-advisor.png)

## <a name="additional-resources"></a>추가 자료

- [자격 증명 모음을 지원하는 시나리오 및 지원하지 않는 시나리오](backup-support-matrix.md#vault-support)
- [자격 증명 모음에 대한 질문과 대답](backup-azure-backup-faq.yml)

## <a name="next-steps"></a>다음 단계

다음 문서를 사용하여 해당 작업을 수행하세요.

- [IaaS VM 백업](backup-azure-arm-vms-prepare.md)
- [Azure Backup Server 백업](backup-azure-microsoft-azure-backup.md)
- [Windows Server 백업](backup-windows-with-mars-agent.md)
