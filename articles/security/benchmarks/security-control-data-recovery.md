---
title: Azure 보안 제어 - 데이터 복구
description: 보안 제어 데이터 복구
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: c585ebd903d4070f6247456e06efffbc6ec45270
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75934501"
---
# <a name="security-control-data-recovery"></a>보안 제어: 데이터 복구

모든 시스템 데이터, 구성 및 비밀이 정기적으로 자동으로 백업되도록 합니다.

## <a name="91-ensure-regular-automated-back-ups"></a>9.1: 정기적인 자동 백업 보장

| Azure ID | CIS 아이디 | 책임 |
|--|--|--|
| 9.1 | 10.1 | Customer |

Azure 백업을 활성화하고 원하는 빈도 및 보존 기간뿐만 아니라 백업 소스(Azure VM, SQL Server 또는 파일 공유)를 구성합니다.

Azure 백업을 활성화하는 방법:

https://docs.microsoft.com/azure/backup/

## <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: 전체 시스템 백업 수행 및 고객 관리 키 백업

| Azure ID | CIS 아이디 | 책임 |
|--|--|--|
| 9.2 | 10.2 | Customer |

Azure 백업을 활성화하고 원하는 빈도 및 보존 기간뿐만 아니라 VM을 대상으로 합니다. Azure 키 볼트 내에서 백업 고객 관리 키.

Azure 백업을 활성화하는 방법:

https://docs.microsoft.com/azure/backup/

Azure에서 키 자격 증명 모음 키를 백업하는 방법:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0

## <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: 고객 관리 키를 포함한 모든 백업의 유효성 검사

| Azure ID | CIS 아이디 | 책임 |
|--|--|--|
| 9.3 | 10.3 | Customer |

Azure Backup 내에서 콘텐츠의 데이터 복원을 주기적으로 수행할 수 있는 기능을 보장합니다. 필요한 경우 격리된 VLAN으로 복원을 테스트합니다. 백업된 고객 관리 키의 복원을 테스트합니다.

Azure 가상 시스템 백업에서 파일을 복구 하는 방법:

https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm

Azure에서 키 자격 증명 모음 키를 복원 하는 방법:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0

## <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: 백업 및 고객 관리 키 보호 보장

| Azure ID | CIS 아이디 | 책임 |
|--|--|--|
| 9.4 | 10.4 | Customer |

온-프레미스 백업의 경우 미사용 데이터 암호화 기능은 Azure에 백업할 때 제공한 암호를 사용하여 제공됩니다. Azure VM의 경우 SSE(스토리지 서비스 암호화)를 사용하여 미사용 데이터가 암호화됩니다. 키 자격 증명 모음에서 소프트 삭제를 활성화하여 실수로 또는 악의적인 삭제로부터 키를 보호할 수 있습니다.

키 자격 증명 모음에서 소프트 삭제를 사용하도록 설정하는 방법:

https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

## <a name="next-steps"></a>다음 단계

다음 보안 제어: [인시던트 대응](security-control-incident-response.md) 보기
