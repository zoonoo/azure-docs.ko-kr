---
title: Azure 보안 제어-데이터 복구
description: 보안 제어 데이터 복구
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: 1cd3e39fd504b5095a83192a4a6314c71d3ca980
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/31/2019
ms.locfileid: "75564297"
---
# <a name="security-control-data-recovery"></a>보안 제어: 데이터 복구

모든 시스템 데이터, 구성 및 비밀이 정기적으로 자동 백업 되는지 확인 합니다.

## <a name="91-ensure-regular-automated-back-ups"></a>9.1: 정기 자동 백업 확인

| Azure ID | CIS Id | 책임 |
|--|--|--|
| 9.1 | 10.1 | Customer |

Azure Backup를 사용 하도록 설정 하 고 원하는 빈도 및 보존 기간 뿐만 아니라 백업 원본 (Azure Vm, SQL Server 또는 파일 공유)을 구성 합니다.

Azure Backup를 사용 하도록 설정 하는 방법: https://docs.microsoft.com/azure/backup/

## <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: 전체 시스템 백업을 수행 하 고 고객 관리 키를 백업 합니다.

| Azure ID | CIS Id | 책임 |
|--|--|--|
| 9.2 | 10.2 | Customer |

원하는 주파수와 보존 기간 뿐만 아니라 Azure Backup 및 대상 VM을 사용 하도록 설정 합니다. Azure Key Vault 내에서 고객 관리 키를 백업 합니다.

Azure Backup를 사용 하도록 설정 하는 방법: https://docs.microsoft.com/azure/backup/

Azure에서 주요 자격 증명 모음 키를 백업 하는 방법: https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0

## <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: 고객 관리 키를 포함 한 모든 백업의 유효성을 검사 합니다.

| Azure ID | CIS Id | 책임 |
|--|--|--|
| 9.3 | 10.3 | Customer |

Azure Backup 내에서 콘텐츠를 정기적으로 복원 하는 기능을 보장 합니다. 필요한 경우 격리 된 VLAN에 대 한 복원을 테스트 합니다. 백업 된 고객 관리 키의 복원을 테스트 합니다.

Azure 가상 컴퓨터 백업에서 파일을 복구 하는 방법:

https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm

Azure에서 주요 자격 증명 모음 키를 복원 하는 방법:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0

## <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: 백업 및 고객 관리 키의 보호 보장

| Azure ID | CIS Id | 책임 |
|--|--|--|
| 9.4 | 10.4 | Customer |

온-프레미스 백업의 경우 미사용 데이터 암호화 기능은 Azure에 백업할 때 제공한 암호를 사용하여 제공됩니다. Azure VM의 경우 SSE(스토리지 서비스 암호화)를 사용하여 미사용 데이터가 암호화됩니다. Key Vault에서 일시 삭제를 사용 하도록 설정 하 여 실수로 또는 악의적인 삭제 로부터 키를 보호할 수 있습니다.

Key Vault에서 일시 삭제를 사용 하도록 설정 하는 방법:

https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

## <a name="next-steps"></a>다음 단계

다음 보안 제어: [인시던트 응답](security-control-incident-response.md) 을 참조 하세요.
