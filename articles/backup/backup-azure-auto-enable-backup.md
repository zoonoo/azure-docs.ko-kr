---
title: Azure Policy를 사용하여 VM 생성 시 백업 자동 활성화
description: Azure Policy를 사용하여 지정된 범위에서 생성된 모든 VM에 대해 백업을 자동으로 활성화하는 방법을 설명하는 문서입니다.
ms.topic: conceptual
ms.date: 11/08/2019
ms.openlocfilehash: 1d423371788f87155328e8f5c9334e47349a68e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77584271"
---
# <a name="auto-enable-backup-on-vm-creation-using-azure-policy"></a>Azure Policy를 사용하여 VM 생성 시 백업 자동 활성화

조직의 백업 또는 규정 준수 관리자의 주요 책임 중 하나는 모든 비즈니스 크리티컬 컴퓨터가 적절한 보존 상태로 백업되도록 하는 것입니다.

현재 Azure Backup은 **구독 또는 리소스 그룹 내의 지정된 위치에 있는 모든 Azure VM에**할당할 수 있는 기본 제공 정책(Azure Policy 사용)을 제공합니다. 이 정책이 지정된 범위에 할당되면 해당 범위에서 생성된 모든 새 VM이 동일한 **위치 및 구독의 기존 자격 증명 모음에**백업하도록 자동으로 구성됩니다. 사용자는 백업된 VM을 연결해야 하는 볼트 및 보존 정책을 지정할 수 있습니다.

## <a name="supported-scenarios"></a>지원되는 시나리오

* 기본 제공 정책은 현재 Azure VM에대해서만 지원됩니다. 사용자는 할당 중에 지정된 보존 정책이 VM 보존 정책인지 확인해야 합니다. [이](https://docs.microsoft.com/azure/backup/backup-azure-policy-supported-skus) 문서에서 지원되는 모든 VM SCO를 보려면 이 문서를 참조하십시오.

* 정책을 한 번에 단일 위치 및 구독에 할당할 수 있습니다. 위치 및 구독 간에 VM에 대한 백업을 사용하려면 위치 및 구독의 각 조합에 대해 하나씩 정책 할당의 여러 인스턴스를 만들어야 합니다.

* 백업을 위해 구성된 지정된 볼트 및 VM은 서로 다른 리소스 그룹 아래에 있을 수 있습니다.

* 관리 그룹 범위는 현재 지원되지 않습니다.

* 기본 제공 정책은 현재 국가 클라우드에서 사용할 수 없습니다.

## <a name="using-the-built-in-policy"></a>기본 제공 정책 사용

필요한 범위에 정책을 할당하려면 다음 단계를 따르십시오.

1. Azure 포털에 로그인하고 **정책** 대시보드로 이동합니다.
2. 왼쪽 메뉴에서 **정의를** 선택하여 Azure Resources에서 모든 기본 제공 정책 목록을 가져옵니다.
3. 범주=백업 에 대한 목록을 **필터링합니다.** '동일한 위치에 있는 기존 중앙 볼트에 위치의 VM에서 백업 구성'이라는 단일 정책으로 필터링된 목록이 표시됩니다.
![정책 대시보드](./media/backup-azure-auto-enable-backup/policy-dashboard.png)
4. 정책 이름을 클릭합니다. 이 정책에 대한 자세한 정의로 리디렉션됩니다.
![정책 정의 블레이드](./media/backup-azure-auto-enable-backup/policy-definition-blade.png)
5. 블레이드 상단의 **할당** 버튼을 클릭합니다. 그러면 **정책 할당** 블레이드로 리디렉션됩니다.
6. **기본**항목에서 **범위** 필드 옆에 있는 세 개의 점을 클릭합니다. 그러면 적용할 정책에 대한 구독을 선택할 수 있는 올바른 컨텍스트 블레이드가 열립니다. 정책은 특정 리소스 그룹의 VM에만 적용되도록 리소스 그룹을 선택적으로 선택할 수도 있습니다.
![정책 할당 기본 사항](./media/backup-azure-auto-enable-backup/policy-assignment-basics.png)
7. 매개 **변수** 탭에서 드롭다운에서 위치를 선택하고 범위의 VM을 연결해야 하는 자격 증명 모음 및 백업 정책을 선택합니다.
![정책 할당 매개 변수](./media/backup-azure-auto-enable-backup/policy-assignment-parameters.png)
8. **효과가** 배포하도록 설정되어 있는지 확인IfNotExists.
9. **검토+로** 이동하여 생성 및 **만들기를**클릭합니다.

> [!NOTE]
>
> Azure 정책은 [업데이트 적용을](https://docs.microsoft.com/azure/governance/policy/how-to/remediate-resources)사용하여 기존 VM에서도 사용할 수 있습니다.

> [!NOTE]
>
> 이 정책은 한 번에 200개 이상의 VM에 할당되지 않는 것이 좋습니다. 정책이 200개 이상의 VM에 할당된 경우 일정에 지정된 것보다 몇 시간 후에 백업이 트리거될 수 있습니다.

## <a name="next-steps"></a>다음 단계

[Azure 정책에 대해 자세히 알아보기](https://docs.microsoft.com/azure/governance/policy/overview)
