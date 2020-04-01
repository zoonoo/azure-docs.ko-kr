---
title: Azure VM을 새 구독 또는 리소스 그룹으로 이동
description: Azure 리소스 관리자를 사용하여 가상 컴퓨터를 새 리소스 그룹 또는 구독으로 이동합니다.
ms.topic: conceptual
ms.date: 03/31/2020
ms.openlocfilehash: df34268b7741f76621c290e9979cf24d828ddc09
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478655"
---
# <a name="move-guidance-for-virtual-machines"></a>가상 시스템에 대한 지침 이동

이 문서에서는 현재 지원되지 않는 시나리오와 백업을 사용하여 가상 컴퓨터를 이동하는 단계에 대해 설명합니다.

## <a name="scenarios-not-supported"></a>지원되지 않는 시나리오

다음 시나리오는 아직 지원되지 않습니다.

* 가용성 영역에서 관리되는 디스크는 다른 구독으로 이동할 수 없습니다.
* 표준 SKU 로드 밸런서 또는 표준 SKU 공용 IP가 있는 가상 시스템 스케일 세트는 이동할 수 없습니다.
* 연결된 계획이 있는 Marketplace 리소스에서 만든 가상 머신은 리소스 그룹 또는 구독 간에 이동할 수 없습니다. 현재 구독에서 가상 컴퓨터를 프로비전 해제하고 새 구독에 다시 배포합니다.
* 가상 네트워크의 모든 리소스를 이동하지 않을 때기존 가상 네트워크의 가상 컴퓨터를 새 구독으로 이동할 수 없습니다.
* 우선 순위가 낮은 가상 컴퓨터와 우선 순위가 낮은 가상 시스템 크기 집합은 리소스 그룹 또는 구독 간에 이동할 수 없습니다.
* 가용성 집합의 가상 컴퓨터를 개별적으로 이동할 수 없습니다.

## <a name="virtual-machines-with-azure-backup"></a>Azure 백업이 있는 가상 시스템

Azure Backup으로 구성된 가상 컴퓨터를 이동하려면 볼트에서 복원 지점을 삭제해야 합니다.

가상 시스템에 [대해 소프트 삭제가](../../../backup/backup-azure-security-feature-cloud.md) 활성화된 경우 복원 지점이 유지되는 동안가상 컴퓨터를 이동할 수 없습니다. [소프트 삭제를 사용하지 않도록 설정하거나](../../../backup/backup-azure-security-feature-cloud.md#disabling-soft-delete) 복원 지점을 삭제한 후 14일 정도 기다립니다.

### <a name="portal"></a>포털

1. 백업을 위해 구성된 가상 컴퓨터를 선택합니다.

1. 왼쪽 창에서 **백업**을 선택합니다.

1. **백업 중지를**선택합니다.

1. **데이터 뒤로 삭제를 선택합니다.**

1. 삭제가 완료되면 자격 증명 모음 및 가상 컴퓨터를 대상 구독으로 이동할 수 있습니다. 이동 후 백업을 계속할 수 있습니다.

### <a name="powershell"></a>PowerShell

* 가상 머신의 위치를 찾습니다.
* 명명 패턴이 `AzureBackupRG_<location of your VM>_1`인 리소스 그룹을 찾습니다(예: AzureBackupRG_westus2_1).
* PowerShell에서 작업하는 경우에는 `Get-AzResource -ResourceGroupName AzureBackupRG_<location of your VM>_1` cmdlet을 사용합니다.
* 종류가 `Microsoft.Compute/restorePointCollections`이고 명명 패턴이 `AzureBackup_<name of your VM that you're trying to move>_###########`인 리소스를 찾습니다.
* 이 리소스를 삭제합니다. 이 작업은 자격 증명 모음의 백업된 데이터가 아니라 인스턴트 복구 지점만 삭제합니다.

### <a name="azure-cli"></a>Azure CLI

* 가상 머신의 위치를 찾습니다.
* 명명 패턴이 `AzureBackupRG_<location of your VM>_1`인 리소스 그룹을 찾습니다(예: AzureBackupRG_westus2_1).
* CLI에서 작업하는 경우에는 `az resource list -g AzureBackupRG_<location of your VM>_1`을 사용합니다.
* 종류가 `Microsoft.Compute/restorePointCollections`이고 명명 패턴이 `AzureBackup_<name of your VM that you're trying to move>_###########`인 리소스를 찾습니다.
* 이 리소스를 삭제합니다. 이 작업은 자격 증명 모음의 백업된 데이터가 아니라 인스턴트 복구 지점만 삭제합니다.

## <a name="next-steps"></a>다음 단계

* 리소스를 이동하는 명령은 [새 리소스 그룹 또는 구독으로 리소스 이동](../move-resource-group-and-subscription.md)을 참조하세요.

* 백업용 Recovery Service 자격 증명 모음 이동에 대한 내용은 [Recovery Services 제한 사항](../../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)을 참조하세요.
