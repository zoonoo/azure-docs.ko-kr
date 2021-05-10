---
title: Azure 보안 제어 - 데이터 복구
description: Azure 보안 제어 데이터 복구
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 5006a592b410685ad808ff01112234bc48f5d267
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105728099"
---
# <a name="security-control-data-recovery"></a>보안 제어: 데이터 복구

모든 시스템 데이터, 구성 및 비밀이 정기적으로 자동 백업되는지 확인합니다.

## <a name="91-ensure-regular-automated-back-ups"></a>9.1: 자동화된 정기 백업 보장

| Azure ID | CIS ID | 책임 |
|--|--|--|
| 9.1 | 10.1 | Customer |

Azure Backup을 사용하도록 설정하고 백업 원본(Azure VM, SQL Server 또는 파일 공유)과 원하는 빈도 및 보존 기간을 구성합니다.

- [Azure Backup을 사용으로 설정하는 방법](../../backup/index.yml)

## <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: 전체 시스템 백업 수행 및 고객 관리형 키 백업

| Azure ID | CIS ID | 책임 |
|--|--|--|
| 9.2 | 10.2 | Customer |

Azure Backup과 대상 VM, 원하는 빈도 및 보존 기간을 사용하도록 설정합니다. Azure Key Vault 내에서 고객 관리형 키를 백업합니다.

- [Azure Backup을 사용으로 설정하는 방법](../../backup/index.yml)

- [Azure에서 키 자격 증명 모음 키를 백업하는 방법](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

## <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: 고객 관리형 키를 포함한 모든 백업의 유효성 검사

| Azure ID | CIS ID | 책임 |
|--|--|--|
| 9.3 | 10.3 | Customer |

Azure Backup 내에서 콘텐츠의 데이터 복원을 주기적으로 수행할 수 있는지 확인합니다. 백업된 고객 관리형 키의 복원을 테스트합니다.

- [Azure Virtual Machine 백업에서 파일 복구 방법](../../backup/backup-azure-restore-files-from-vm.md)

- [Azure에서 키 자격 증명 모음 키를 복원하는 방법](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey)

## <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: 백업 및 고객 관리형 키 보호 보장

| Azure ID | CIS ID | 책임 |
|--|--|--|
| 9.4 | 10.4 | Customer |

온-프레미스 백업의 경우 미사용 데이터 암호화 기능은 Azure에 백업할 때 제공한 암호를 사용하여 제공됩니다. Azure VM의 경우 SSE(스토리지 서비스 암호화)를 사용하여 미사용 데이터가 암호화됩니다. Azure 역할 기반 액세스 제어를 사용하여 백업과 고객 관리형 키를 보호합니다.  

Key Vault에서 일시 삭제 및 제거 방지를 사용하도록 설정하여 실수로 또는 악의적으로 삭제되지 않도록 키를 보호합니다.  Azure Storage를 사용하여 백업을 저장하는 경우 BLOB 또는 BLOB 스냅샷이 삭제될 때 일시 삭제를 사용하여 데이터를 저장하고 복구할 수 있습니다. 

- [Azure RBAC 이해](../../role-based-access-control/overview.md)

- [Key Vault에서 일시 삭제 및 제거 방지를 사용하도록 설정하는 방법](../../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

- [Azure Storage Blob에 대한 일시 삭제](../../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)


## <a name="next-steps"></a>다음 단계

- 다음 보안 제어: [인시던트 응답](security-control-incident-response.md)을 참조하세요.