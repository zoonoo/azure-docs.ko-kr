---
title: 볼트 진단 설정 규모 구성
description: Azure 정책을 사용하여 지정된 범위의 모든 볼트에 대해 로그 분석 진단 설정 구성
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: c92957cab3e1ed745e7031e3c6f32e7ecda550a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77584509"
---
# <a name="configure-vault-diagnostics-settings-at-scale"></a>볼트 진단 설정 규모 구성

Azure Backup에서 제공하는 보고 솔루션은 LA(로그 분석)를 활용합니다. 지정된 볼트의 데이터를 LA로 보내려면 해당 자격 증명 모음에 대한 [진단 설정을](https://docs.microsoft.com/azure/backup/backup-azure-diagnostic-events) 만들어야 합니다.

종종 볼트당 진단 설정을 수동으로 추가하는 것은 번거로운 작업일 수 있습니다. 또한 이 자격 증명 모음에 대한 보고서를 볼 수 있으려면 새로 만든 자격 증명 모음도 진단 설정을 사용하도록 설정해야 합니다. 

확장(LA를 대상으로 하는 경우)에서 진단 설정 만들기를 간소화하기 위해 Azure Backup은 기본 제공 [Azure 정책을](https://docs.microsoft.com/azure/governance/policy/)제공합니다. 이 정책은 지정된 구독 또는 리소스 그룹의 모든 자격 증명 모음에 LA 진단 설정을 추가합니다. 다음 섹션에서는 이 정책을 사용하는 방법에 대한 지침을 제공합니다.

## <a name="supported-scenarios"></a>지원되는 시나리오

* 이 정책은 특정 구독의 모든 복구 서비스 자격 증명 모음(또는 구독 내의 리소스 그룹에 한 번에 적용할 수 있음)에 한 번에 적용할 수 있습니다. 정책을 할당하는 사용자는 정책이 할당된 구독에 '소유자' 액세스 권한이 있어야 합니다.

* 사용자가 지정한 LA 작업 영역(진단 데이터가 전송되는 경우)은 정책이 할당된 볼트와 다른 구독에 있을 수 있습니다. 사용자는 지정된 LA 작업 영역이 있는 구독에 '리더', '기여자' 또는 '소유자' 액세스 권한이 있어야 합니다.

* 관리 그룹 범위는 현재 지원되지 않습니다.

* 기본 제공 정책은 현재 국가 클라우드에서 사용할 수 없습니다.

## <a name="assigning-the-built-in-policy-to-a-scope"></a>기본 제공 정책을 범위에 할당

필요한 범위에서 볼트에 대한 정책을 할당하려면 다음 단계를 따르십시오.

1. Azure 포털에 로그인하고 **정책** 대시보드로 이동합니다.
2. 왼쪽 메뉴에서 **정의를** 선택하여 Azure Resources에서 모든 기본 제공 정책 목록을 가져옵니다.
3. **범주=모니터링에**대한 목록을 필터링합니다. **[미리 보기]: 리소스별 범주에 대한 복구 서비스 볼트에 대한 진단 설정을 로그 분석 작업 영역에 배포합니다.**

![정책 정의 블레이드](./media/backup-azure-policy-configure-diagnostics/policy-definition-blade.png)

4. 정책 이름을 클릭합니다. 이 정책에 대한 자세한 정의로 리디렉션됩니다.

![자세한 정책 정의](./media/backup-azure-policy-configure-diagnostics/detailed-policy-definition.png)

5. 블레이드 상단의 **할당** 버튼을 클릭합니다. 그러면 **정책 할당** 블레이드로 리디렉션됩니다.

6. **기본**항목에서 **범위** 필드 옆에 있는 세 개의 점을 클릭합니다. 그러면 적용할 정책에 대한 구독을 선택할 수 있는 올바른 컨텍스트 블레이드가 열립니다. 정책은 특정 리소스 그룹의 볼트에만 적용되도록 리소스 그룹을 선택적으로 선택할 수도 있습니다.

![정책 할당 기본 사항](./media/backup-azure-policy-configure-diagnostics/policy-assignment-basics.png)

7. 매개 변수 아래에서 다음 정보를 **입력합니다.**

* **프로필 이름** - 정책에서 만든 진단 설정에 할당될 이름입니다.
* **로그 분석 작업 영역** - 진단 설정을 연결해야 하는 로그 분석 작업 영역입니다. 정책 할당 범위의 모든 볼트의 진단 데이터는 지정된 LA 작업 영역으로 푸시됩니다.

* **제외 태그 이름(선택 사항) 및 제외 태그 값(선택 사항)** - 정책 할당에서 특정 태그 이름 및 값이 포함된 볼트를 제외하도록 선택할 수 있습니다. 예를 들어 'isTest' 값이 'yes'로 설정된 볼트에 진단 설정을 **추가하지** 않으려면 **제외 태그 이름** 필드에 'isTest'를 입력하고 제외 태그 **값** 필드에 'yes'를 입력해야 합니다. 이 두 필드 중 모두 비어 있는 경우 정책이 포함된 태그에 관계없이 모든 관련 자격 증명 모음에 적용됩니다.

![정책 할당 매개 변수](./media/backup-azure-policy-configure-diagnostics/policy-assignment-parameters.png)

8. **업데이트 관리 작업 만들기** - 정책이 범위에 할당되면 해당 범위에서 생성된 모든 새 볼트는 자동으로 LA 진단 설정을 구성합니다(볼트 생성 시점으로부터 30분 이내). 범위의 기존 볼트에 진단 설정을 추가하려면 정책 할당 시 수정 작업을 트리거할 수 있습니다. 업데이트 적용 작업을 트리거하려면 업데이트 적용 **작업 만들기 확인란을**선택합니다. 

![정책 할당 수정](./media/backup-azure-policy-configure-diagnostics/policy-assignment-remediation.png)

9. **검토+만들기** 탭으로 이동하여 **에서 만들기를**클릭합니다.

## <a name="under-what-conditions-will-the-remediation-task-apply-to-a-vault"></a>수정 작업은 볼트에 어떤 조건에서 적용됩니까?

업데이트 적용 작업은 정책 정의에 따라 비준수된 볼트에 적용됩니다. 볼트는 다음 조건 중 하나를 준수하는 경우 비규정을 준수합니다.

* 볼트에 대한 진단 설정이 없습니다.
* 볼트에 대한 진단 설정이 있지만 두 설정 모두 LA를 대상으로 사용하도록 설정하고 토글에서 선택한 **리소스 특정** 이벤트를 **모두** 사용할 수 없습니다. 

따라서 사용자가 AzureBackupReport 이벤트가 AzureDiagnostics 모드에서 활성화된 볼트(백업 보고서에서 지원됨)가 있는 경우에도 리소스 별 모드가 진단 설정을 만드는 권장 되는 방법이기 때문에 업데이트 관리 작업은 이 자격 증명 모음에 계속 [적용됩니다.](https://docs.microsoft.com/azure/backup/backup-azure-diagnostic-events#legacy-event)

또한 사용자가 6개의 Resource 특정 이벤트의 하위 집합만 사용하도록 설정한 볼트가 있는 경우 백업 보고서는 6개의 리소스 특정 이벤트가 모두 활성화된 경우에만 예상대로 작동하므로 업데이트 관리 작업이 이 자격 증명 모음에 적용됩니다.

> [!NOTE]
>
> 볼트에 **리소스 특정 범주의 하위 집합이** 활성화된 기존 진단 설정이 있는 경우 특정 LA 작업 영역으로 데이터를 보내도록 구성된 '작업 영역 X'라고 하면 정책 할당에 제공된 대상 LA 작업 영역이 **동일한** 경우 수정 작업이 실패합니다(해당 볼트만 해당 작업 영역). 
>
>동일한 리소스에서 두 개의 서로 다른 진단 설정으로 활성화된 이벤트가 어떤 형태로 **겹치는** 경우 설정이 대상과 동일한 LA 작업 영역을 가질 수 없기 때문입니다. 관련 자격 증명 모음으로 이동하여 다른 LA 작업 영역을 대상으로 진단 설정을 구성하여 이 오류를 수동으로 해결해야 합니다.
>
> 이 경우 기존 설정에서 활성화된 이벤트와 업데이트 관리 작업에서 만든 설정으로 활성화된 이벤트 사이에 겹치지 않으므로 기존 진단 설정이 작업 영역 X에서 만 사용하도록 설정된 경우 업데이트 작업이 **실패하지 않습니다.**

## <a name="next-steps"></a>다음 단계

* [백업 보고서 사용 방법 알아보기](https://docs.microsoft.com/azure/backup/configure-reports)
* [Azure 정책에 대해 자세히 알아보기](https://docs.microsoft.com/azure/governance/policy/)
* [Azure 정책을 사용하여 제공 범위의 모든 VM에 대한 백업을 자동으로 활성화합니다.](https://docs.microsoft.com/azure/backup/backup-azure-auto-enable-backup)
