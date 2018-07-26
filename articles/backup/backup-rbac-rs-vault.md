---
title: Azure 역할 기반 액세스 제어를 사용하여 백업 관리
description: 역할 기반 Access Control을 사용하여 Recovery Services 자격 증명 모음의 백업 관리 작업에 대한 액세스를 관리합니다.
services: backup
author: trinadhk
manager: shreeshd
ms.service: backup
ms.topic: conceptual
ms.date: 7/11/2018
ms.author: trinadhk
ms.openlocfilehash: 855b75652fca421df12766f7711152d1e3ca2aeb
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/13/2018
ms.locfileid: "39012061"
---
# <a name="use-role-based-access-control-to-manage-azure-backup-recovery-points"></a>역할 기반 Access Control을 사용하여 Azure Backup 복구 지점 관리
Azure 역할 기반 Access Control(RBAC)을 통해 Azure에 대한 세밀한 액세스 관리가 가능합니다. RBAC를 사용하면 팀 내에서 업무를 분리하고 사용자에게 해당 작업을 수행하는 데 필요한 만큼의 권한만 부여할 수 있습니다.

> [!IMPORTANT]
> Azure Backup에서 제공하는 역할은 Azure Portal 또는 Recovery Services 자격 증명 모음 PowerShell cmdlet에서 수행할 수 있는 작업으로 제한됩니다. Azure 백업 에이전트 클라이언트 UI, System Center Data Protection Manager UI 또는 Azure Backup Server UI에서 실행되는 작업은 이러한 역할의 제어를 받지 않습니다.

Azure Backup은 백업 관리 작업을 제어할 수 있는 기본 제공 역할을 3개 제공합니다. [Azure RBAC 기본 제공 역할](../role-based-access-control/built-in-roles.md)에 대해 알아보기

* [Backup 참여자](../role-based-access-control/built-in-roles.md#backup-contributor) - 이 역할은 Recovery Services 자격 증명 모음을 만들고 다른 사용자에 대한 액세스 권한을 제공하는 권한을 제외하고, 백업을 만들고 관리하는 모든 권한을 갖습니다. 모든 백업 관리 작업을 수행할 수 있는 백업 관리 관리자 역할로 생각하시면 됩니다.
* [Backup 운영자](../role-based-access-control/built-in-roles.md#backup-operator) - 이 역할은 백업을 제거하고 백업 정책을 관리하는 권한을 제외하고, 참여자가 할 수 있는 모든 일을 할 수 있습니다. 이 역할은 온-프레미스 리소스의 데이터 삭제나 등록 제거를 통해 백업을 중지하는 작업처럼 안전하지 않은 작업을 수행할 수 없다는 점만 빼면 참여자와 똑같습니다.
* [Backup 읽기 권한자](../role-based-access-control/built-in-roles.md#backup-reader) - 이 역할은 모든 백업 관리 작업을 볼 수 있는 권한을 갖습니다. 이 역할을 모니터링 요원으로 생각하시면 됩니다.

더 많은 제어를 위해 사용자 고유의 역할을 정의하려는 경우 Azure RBAC에서 [사용자 지정 역할을 빌드](../role-based-access-control/custom-roles.md)하는 방법을 참조하세요.



## <a name="mapping-backup-built-in-roles-to-backup-management-actions"></a>Backup 기본 제공 역할을 관리 작업에 매핑
다음 테이블은 Backup 관리 작업과 해당 작업을 수행하는 데 필요한 최소 RBAC 역할을 캡처한 것입니다.

| 관리 작업 | 필요한 최소 RBAC 역할 |
| --- | --- |
| Recovery Services 자격 증명 모음 만들기 | 자격 증명 모음 리소스 그룹의 참여자 |
| Azure VM의 백업 활성화 | 자격 증명 모음 및 VM에 대한 가상 머신 기여자를 포함하는 리소스 그룹 범위에 정의된 백업 연산자 |
| VM의 주문형 백업 | Backup 운영자 |
| VM 복원 | Backup 운영자, VM이 배포될 리소스 그룹 기여자, Vnet에서 읽기 및 선택된 서브넷에 조인 |
| 디스크, VM 백업의 개별 파일 복원 | Backup 운영자, VM의 가상 머신 참여자 |
| Azure VM 백업에 대한 백업 정책 만들기 | Backup 참여자 |
| Azure VM 백업의 백업 정책 수정 | Backup 참여자 |
| Azure VM 백업의 백업 정책 삭제 | Backup 참여자 |
| VM 백업에서 백업 중지(데이터 보존 또는 데이터 삭제를 통해) | Backup 참여자 |
| 온-프레미스 Windows 서버/클라이언트/SCDPM 또는 Azure Backup Server 등록 | Backup 운영자 |
| 등록된 온-프레미스 Windows 서버/클라이언트/SCDPM 또는 Azure Backup Server 삭제 | Backup 참여자 |

## <a name="next-steps"></a>다음 단계
* [역할 기반 액세스 제어](../role-based-access-control/role-assignments-portal.md): Azure Portal에서 RBAC를 통해 시작합니다.
* 다음을 사용하여 액세스를 관리하는 방법에 대해 알아봅니다.
  * [PowerShell](../role-based-access-control/role-assignments-powershell.md)
  * [Azure CLI](../role-based-access-control/role-assignments-cli.md)
  * [REST API](../role-based-access-control/role-assignments-rest.md)
* [역할 기반 Access Control 문제 해결](../role-based-access-control/troubleshooting.md): 일반적인 문제를 수정하기 위한 제안 사항을 봅니다.
