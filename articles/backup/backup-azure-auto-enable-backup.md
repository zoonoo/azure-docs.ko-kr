---
title: Azure Policy를 사용하여 VM 생성 시 백업 자동 활성화
description: Azure Policy를 사용 하 여 지정 된 범위에서 만든 모든 Vm에 대 한 백업을 자동으로 활성화 하는 방법을 설명 하는 문서입니다.
ms.topic: conceptual
ms.date: 11/08/2019
ms.openlocfilehash: dfa4364eeaa9f5b60af3f5d6a19aaeb188d4f65e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101707305"
---
# <a name="auto-enable-backup-on-vm-creation-using-azure-policy"></a>Azure Policy를 사용하여 VM 생성 시 백업 자동 활성화

조직에서 백업 또는 규정 준수 관리자의 주요 업무 중 하나는 업무상 중요 한 모든 컴퓨터가 적절 한 보존으로 백업 되도록 하는 것입니다.

현재 Azure Backup에서는 [Azure Policy](../governance/policy/overview.md)를 사용 하 여 다양 한 기본 제공 정책을 제공 하므로 Azure virtual machines를 백업 하도록 자동으로 구성할 수 있습니다. 백업 팀 및 리소스를 구성 하는 방법에 따라 아래 정책 중 하나를 사용할 수 있습니다.

## <a name="policy-1---configure-backup-on-vms-without-a-given-tag-to-an-existing-recovery-services-vault-in-the-same-location"></a>정책 1-동일한 위치의 기존 recovery services 자격 증명 모음에 대 한 지정 된 태그 없이 Vm에 대 한 백업 구성

조직에 응용 프로그램 팀 간에 백업을 관리 하는 중앙 백업 팀이 있는 경우이 정책을 사용 하 여 관리 되는 Vm과 동일한 구독 및 위치에 있는 기존 중앙 Recovery Services 자격 증명 모음에 백업을 구성할 수 있습니다. 이 정책의 범위에서 특정 태그를 포함 하는 vm을 **제외** 하도록 선택할 수 있습니다.

## <a name="policy-2---preview-configure-backup-on-vms-with-a-given-tag-to-an-existing-recovery-services-vault-in-the-same-location"></a>정책 2-[미리 보기] 동일한 위치의 기존 recovery services 자격 증명 모음에 대 한 지정 된 태그를 사용 하 여 Vm에 백업 구성
이 정책은 위의 정책 1과 동일 하 게 작동 합니다. 유일한 차이점은이 정책을 사용 하 여 특정 태그를 포함 하는 vm을이 정책 범위에 **포함** 하는 것입니다. 

## <a name="policy-3---preview-configure-backup-on-vms-without-a-given-tag-to-a-new-recovery-services-vault-with-a-default-policy"></a>정책 3-[미리 보기] 기본 정책을 사용 하 여 새 recovery services 자격 증명 모음에 지정 된 태그 없이 Vm에 대 한 백업 구성
전용 리소스 그룹의 응용 프로그램을 구성 하 고 동일한 자격 증명 모음에서 백업 하려는 경우이 정책을 사용 하 여이 작업을 자동으로 관리할 수 있습니다. 이 정책의 범위에서 특정 태그를 포함 하는 vm을 **제외** 하도록 선택할 수 있습니다.

## <a name="policy-4---preview-configure-backup-on-vms-with-a-given-tag-to-a-new-recovery-services-vault-with-a-default-policy"></a>정책 4-[미리 보기] 기본 정책을 사용 하 여 새 recovery services 자격 증명 모음에 지정 된 태그를 가진 Vm에 대 한 백업 구성
이 정책은 위의 정책 3과 동일 하 게 작동 합니다. 유일한 차이점은이 정책을 사용 하 여 특정 태그를 포함 하는 vm을이 정책 범위에 **포함** 하는 것입니다. 

위의 외에도 Azure Backup [감사 전용](../governance/policy/concepts/effects.md#audit) 정책을 제공 합니다. **Azure Backup Virtual Machines에 대해 사용 하도록 설정 해야 합니다**. 이 정책은 백업을 사용 하도록 설정 되지 않은 가상 머신을 식별 하지만 이러한 Vm에 대 한 백업을 자동으로 구성 하지는 않습니다. 이는 Vm의 전반적인 호환성을 평가 하려는 경우에 유용 하지만 즉시 조치를 취하지는 않으려는 경우에 유용 합니다.

## <a name="supported-scenarios"></a>지원되는 시나리오

* 기본 제공 정책은 현재 Azure Vm에 대해서만 지원 됩니다. 사용자는 할당 중에 지정 된 보존 정책이 VM 보존 정책 인지 확인 해야 합니다. 이 정책에서 지 원하는 모든 VM Sku를 보려면 [이](./backup-azure-policy-supported-skus.md) 문서를 참조 하세요.

* 정책 1과 2는 한 번에 하나의 위치 및 구독에 할당 될 수 있습니다. 위치 및 구독의 Vm에 대 한 백업을 사용 하도록 설정 하려면 각 위치 및 구독의 조합에 대해 하나씩, 정책 할당의 여러 인스턴스를 만들어야 합니다.

* 정책 1 및 2의 경우 관리 그룹 범위가 현재 지원 되지 않습니다.

* 정책 1 및 2의 경우 지정 된 자격 증명 모음 및 백업용으로 구성 된 Vm은 다른 리소스 그룹에 있을 수 있습니다.

* 정책 1, 2, 3 및 4는 현재 국가별 클라우드에서 사용할 수 없습니다.

* 정책 3과 4는 한 번에 하나의 구독 (또는 구독 내의 리소스 그룹)에 할당할 수 있습니다.

[!INCLUDE [backup-center.md](../../includes/backup-center.md)]

## <a name="using-the-built-in-policies"></a>기본 제공 정책 사용

아래 단계에서는 정책 1을 할당 하는 종단 간 프로세스를 설명 합니다. 지정 된 범위와 **동일한 위치에 있는 기존 recovery services 자격 증명 모음에 지정 된 태그 없이 vm에 대 한 백업을 구성** 합니다. 다른 정책에도 유사한 지침이 적용 됩니다. 할당 되 면 범위에서 만든 모든 새 VM이 자동으로 백업에 대해 구성 됩니다.

1. Azure Portal에 로그인 하 여 **정책** 대시보드로 이동 합니다.
2. 왼쪽 메뉴에서 **정의** 를 선택 하 여 Azure 리소스의 모든 기본 제공 정책 목록을 가져옵니다.
3. **Category = Backup** 의 목록을 필터링 하 고 ' 동일한 위치의 기존 중앙 자격 증명 모음에 대 한 vm에서 백업 구성 ' 이라는 정책을 선택 합니다.
![정책 대시보드](./media/backup-azure-auto-enable-backup/policy-dashboard.png)
4. 정책 이름을 선택 합니다. 이 정책에 대 한 세부 정의로 리디렉션됩니다.
![정책 정의 창](./media/backup-azure-auto-enable-backup/policy-definition-blade.png)
5. 창 위쪽에서 **할당** 단추를 선택 합니다. 그러면 **정책 할당** 창으로 리디렉션됩니다.
6. **기본** 에서 **범위** 필드 옆에 있는 세 개의 점을 선택 합니다. 그러면 적용할 정책의 구독을 선택할 수 있는 올바른 컨텍스트 창이 열립니다. 또한 필요에 따라 리소스 그룹을 선택 하 여 특정 리소스 그룹의 Vm에 대해서만 정책이 적용 되도록 할 수 있습니다.
![정책 할당 기본 사항](./media/backup-azure-auto-enable-backup/policy-assignment-basics.png)
7. **매개 변수** 탭의 드롭다운에서 위치를 선택 하 고 범위의 vm이 연결 되어야 하는 자격 증명 모음 및 백업 정책을 선택 합니다. 태그 이름 및 태그 값의 배열을 지정 하도록 선택할 수도 있습니다. 지정 된 태그에 지정 된 값을 포함 하는 VM은 정책 할당의 범위에서 제외 됩니다.
![정책 할당 매개 변수](./media/backup-azure-auto-enable-backup/policy-assignment-parameters.png)
8. **효과가** deployIfNotExists로 설정 되었는지 확인 합니다.
9. **검토 + 만들기** 로 이동 하 고 **만들기** 를 선택 합니다.

> [!NOTE]
>
> Azure Policy는 [관리](../governance/policy/how-to/remediate-resources.md)를 사용 하 여 기존 vm 에서도 사용할 수 있습니다.

> [!NOTE]
>
> 이 정책은 한 번에 200 개 이상의 Vm에 할당 되는 것이 좋습니다. 정책이 200 개 이상의 Vm에 할당 되 면 백업이 일정에 지정 된 시간 보다 몇 시간 후에 트리거될 수 있습니다.

## <a name="next-steps"></a>다음 단계

[Azure Policy에 대 한 자세한 정보](../governance/policy/overview.md)