---
title: 새 구독 또는 리소스 그룹으로 Azure Virtual Machines 이동 | Microsoft Docs
description: Azure Resource Manager를 사용 하 여 가상 컴퓨터를 새 리소스 그룹 또는 구독으로 이동할 수 있습니다.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: tomfitz
ms.openlocfilehash: 443d6f2bcbb61d9106b079a4e63c48bb433d19c6
ms.sourcegitcommit: e0a1a9e4a5c92d57deb168580e8aa1306bd94723
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2019
ms.locfileid: "72286726"
---
# <a name="move-guidance-for-virtual-machines"></a>가상 컴퓨터에 대 한 이동 지침

이 문서에서는 현재 지원 되지 않는 시나리오와 백업을 사용 하 여 가상 컴퓨터를 이동 하는 단계에 대해 설명 합니다.

## <a name="scenarios-not-supported"></a>지원 되지 않는 시나리오

다음 시나리오는 아직 지원되지 않습니다.

* 가용성 영역 Managed Disks은 다른 구독으로 이동할 수 없습니다.
* 표준 SKU Load Balancer 또는 표준 SKU 공용 IP를 사용 하는 Virtual Machine Scale Sets는 이동할 수 없습니다.
* 연결된 계획이 있는 Marketplace 리소스에서 만든 가상 머신은 리소스 그룹 또는 구독 간에 이동할 수 없습니다. 현재 구독에서 가상 컴퓨터의 프로 비전을 해제 하 고 새 구독에 다시 배포 합니다.
* 가상 네트워크의 모든 리소스를 이동 하지 않는 경우 기존 가상 네트워크의 가상 컴퓨터를 새 구독으로 이동할 수 없습니다.
* 낮은 우선 순위의 가상 컴퓨터 및 우선 순위가 낮은 가상 컴퓨터 크기 집합은 리소스 그룹 또는 구독 간에 이동할 수 없습니다.
* 가용성 집합의 가상 머신은 개별적으로 이동할 수 없습니다.

## <a name="virtual-machines-with-azure-backup"></a>Azure Backup를 사용 하는 가상 머신

Azure Backup으로 구성한 가상 머신을 이동하려면 다음 해결 방법을 사용합니다.

* 가상 머신의 위치를 찾습니다.
* 명명 패턴이 `AzureBackupRG_<location of your VM>_1`인 리소스 그룹을 찾습니다(예: AzureBackupRG_westus2_1).
* Azure Portal에서 작업하는 경우 “숨겨진 형식 표시”를 선택합니다.
* PowerShell에서 작업하는 경우에는 `Get-AzResource -ResourceGroupName AzureBackupRG_<location of your VM>_1` cmdlet을 사용합니다.
* CLI에서 작업하는 경우에는 `az resource list -g AzureBackupRG_<location of your VM>_1`을 사용합니다.
* 종류가 `Microsoft.Compute/restorePointCollections`이고 명명 패턴이 `AzureBackup_<name of your VM that you're trying to move>_###########`인 리소스를 찾습니다.
* 이 리소스를 삭제합니다. 이 작업은 자격 증명 모음의 백업된 데이터가 아니라 인스턴트 복구 지점만 삭제합니다.
* 삭제가 완료 된 후 자격 증명 모음 및 가상 컴퓨터를 대상 구독으로 이동할 수 있습니다. 이동 후 데이터 손실 없이 백업을 계속할 수 있습니다.
* 백업용 Recovery Service 자격 증명 모음 이동에 대한 내용은 [Recovery Services 제한 사항](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)을 참조하세요.

## <a name="next-steps"></a>다음 단계

리소스를 이동하는 명령은 [새 리소스 그룹 또는 구독으로 리소스 이동](../resource-group-move-resources.md)을 참조하세요.
