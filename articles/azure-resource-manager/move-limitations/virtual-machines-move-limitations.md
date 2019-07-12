---
title: Azure Virtual Machines를 새 구독 또는 리소스 그룹 이동 | Microsoft Docs
description: Azure Resource Manager를 사용 하 여 가상 컴퓨터를 새 리소스 그룹 또는 구독으로 이동할 수 있습니다.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: tomfitz
ms.openlocfilehash: 095ed1c8d2328b1eb391042125526696ba8cda49
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67723546"
---
# <a name="move-guidance-for-virtual-machines"></a>가상 머신에 대 한 지침을 이동

이 문서는 현재 지원 되지 않는 시나리오 및 백업 사용 하 여 가상 컴퓨터를 이동 하는 단계를 설명 합니다.

## <a name="scenarios-not-supported"></a>지원 되지 않는 시나리오

다음 시나리오는 아직 지원되지 않습니다.

* 가용성 영역에서 관리 디스크를 다른 구독으로 이동할 수 없습니다.
* Key Vault에 저장된 인증서가 있는 Virtual Machines는 동일한 구독에서 새 리소스 그룹으로 이동할 수 있지만 구독 간에는 이동할 수 없습니다.
* 표준 SKU 부하 분산 장치 또는 표준 SKU 공용 IP를 사용 하 여 Virtual Machine Scale Sets는 이동할 수 없습니다.
* 연결된 계획이 있는 Marketplace 리소스에서 만든 가상 머신은 리소스 그룹 또는 구독 간에 이동할 수 없습니다. 현재 구독의 가상 머신을 프로비전 해제하고 새 구독에 다시 배포합니다.
* 기존 가상 네트워크 에서만 가상 컴퓨터 가상 네트워크의 모든 리소스를 이동 되지 않습니다.

## <a name="virtual-machines-with-azure-backup"></a>Azure Backup을 사용 하 여 가상 컴퓨터

Azure Backup으로 구성한 가상 머신을 이동하려면 다음 해결 방법을 사용합니다.

* 가상 머신의 위치를 찾습니다.
* 명명 패턴이 `AzureBackupRG_<location of your VM>_1`인 리소스 그룹을 찾습니다(예: AzureBackupRG_westus2_1).
* Azure Portal에서 작업하는 경우 “숨겨진 형식 표시”를 선택합니다.
* PowerShell에서 작업하는 경우에는 `Get-AzResource -ResourceGroupName AzureBackupRG_<location of your VM>_1` cmdlet을 사용합니다.
* CLI에서 작업하는 경우에는 `az resource list -g AzureBackupRG_<location of your VM>_1`을 사용합니다.
* 종류가 `Microsoft.Compute/restorePointCollections`이고 명명 패턴이 `AzureBackup_<name of your VM that you're trying to move>_###########`인 리소스를 찾습니다.
* 이 리소스를 삭제합니다. 이 작업은 자격 증명 모음의 백업된 데이터가 아니라 인스턴트 복구 지점만 삭제합니다.
* 삭제를 완료 한 후 대상 구독에 자격 증명 모음 및 가상 컴퓨터를 이동할 수 있습니다. 이동 후 데이터 손실 없이 백업을 계속할 수 있습니다.
* 백업용 Recovery Service 자격 증명 모음 이동에 대한 내용은 [Recovery Services 제한 사항](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)을 참조하세요.

## <a name="next-steps"></a>다음 단계

리소스를 이동하는 명령은 [새 리소스 그룹 또는 구독으로 리소스 이동](../resource-group-move-resources.md)을 참조하세요.
