---
title: Azure 함수 배포 슬롯
description: Azure 함수를 사용하여 배포 슬롯을 만들고 사용하는 방법을 알아봅니다.
author: craigshoemaker
ms.topic: reference
ms.date: 08/12/2019
ms.author: cshoe
ms.openlocfilehash: 0e8c93ea6d5c2b525ccbea2af900f100afcc3d93
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75769220"
---
# <a name="azure-functions-deployment-slots"></a>Azure 함수 배포 슬롯

Azure Functions 배포 슬롯을 사용하면 함수 앱에서 "슬롯"이라는 다른 인스턴스를 실행할 수 있습니다. 슬롯은 공개적으로 사용 가능한 끝점을 통해 노출되는 다양한 환경입니다. 하나의 앱 인스턴스는 항상 프로덕션 슬롯에 매핑되며 요청 시 슬롯에 할당된 인스턴스를 교체할 수 있습니다. 앱 서비스 계획에서 실행되는 함수 앱에는 여러 슬롯이 있을 수 있지만 소비 계획에서는 하나의 슬롯만 허용됩니다.

다음은 슬롯 교환에 의해 함수가 영향을 받는 방식을 반영합니다.

- 트래픽 리디렉션이 원활합니다. 스왑으로 인해 요청이 삭제되지 않습니다.
- 스왑 중에 함수가 실행되는 경우 실행이 계속되고 후속 트리거가 교환된 앱 인스턴스로 라우팅됩니다.

> [!NOTE]
> 슬롯은 현재 Linux 소비 계획에 사용할 수 없습니다.

## <a name="why-use-slots"></a>왜 슬롯을 사용합니까?

배포 슬롯을 사용하면 여러 가지 이점이 있습니다. 다음 시나리오에서는 슬롯에 대한 일반적인 용도에 대해 설명합니다.

- **용도에 따라 환경이 다르기**때문에 다른 슬롯을 사용하면 프로덕션 또는 스테이징 슬롯으로 교환하기 전에 앱 인스턴스를 구분할 수 있습니다.
- **프리웜**: 프로덕션에 직접 배포하는 대신 슬롯에 배포하면 앱이 라이브로 진행되기 전에 워밍업할 수 있습니다. 또한 슬롯을 사용하면 HTTP 트리거 워크로드에 대한 대기 시간이 줄어듭니다. 인스턴스는 배포 하기 전에 워밍업 되어 새로 배포된 함수에 대 한 콜드 시작을 줄일 수 있습니다.
- **쉬운 대체**: 생산과 교환 한 후, 이전에 준비 된 응용 프로그램과 슬롯은 이제 이전 생산 응용 프로그램이 있습니다. 프로덕션 슬롯으로 교환된 변경 내용이 예상대로 진행되지 않으면 즉시 스왑을 반대로 하여 "마지막으로 알려진 좋은 인스턴스"를 되돌릴 수 있습니다.

## <a name="swap-operations"></a>스왑 작업

스왑 중에 한 슬롯은 소스로 간주되고 다른 슬롯은 대상으로 간주됩니다. 원본 슬롯에는 대상 슬롯에 적용되는 응용 프로그램의 인스턴스가 있습니다. 다음 단계는 대상 슬롯이 교체 중에 가동 중지 시간을 경험하지 않도록 합니다.

1. **설정 적용:** 대상 슬롯의 설정은 소스 슬롯의 모든 인스턴스에 적용됩니다. 예를 들어 프로덕션 설정은 스테이징 인스턴스에 적용됩니다. 적용된 설정에는 다음 범주가 포함됩니다.
    - [슬롯별](#manage-settings) 앱 설정 및 연결 문자열(해당하는 경우)
    - [연속 배포](../app-service/deploy-continuous-deployment.md) 설정(활성화된 경우)
    - [앱 서비스 인증](../app-service/overview-authentication-authorization.md) 설정(활성화된 경우)

1. **다시 시작 및 가용성을 기다립니다.** 스왑은 소스 슬롯의 모든 인스턴스가 다시 시작을 완료하고 요청에 사용할 수 있도록 기다립니다. 인스턴스를 다시 시작하지 못하면 스왑 작업이 모든 변경 내용을 소스 슬롯으로 되돌리고 작업을 중지합니다.

1. **라우팅 업데이트:** 소스 슬롯의 모든 인스턴스가 성공적으로 워밍업되면 두 슬롯이 라우팅 규칙을 전환하여 스왑을 완료합니다. 이 단계 후에 대상 슬롯(예: 프로덕션 슬롯)에는 소스 슬롯에서 이전에 워밍업된 앱이 있습니다.

1. **반복 작업:** 이제 소스 슬롯에 이전에 대상 슬롯에 사전 스왑 앱이 생겼으니 모든 설정을 적용하고 소스 슬롯에 대한 인스턴스를 다시 시작하여 동일한 작업을 수행합니다.

다음 사항을 주의하세요.

- 스왑 작업의 모든 지점에서 교환된 앱의 초기화가 소스 슬롯에서 발생합니다. 소스 슬롯이 준비되는 동안 스왑이 성공하거나 실패하는지 여부에 관계없이 대상 슬롯은 온라인 상태로 유지됩니다.

- 스테이징 슬롯을 프로덕션 슬롯과 교환하려면 프로덕션 슬롯이 *항상* 대상 슬롯인지 확인합니다. 이렇게 하면 스왑 작업이 프로덕션 앱에 영향을 주지 않습니다.

- *스왑을 시작하기 전에*이벤트 소스 및 바인딩과 관련된 설정을 [배포 슬롯 설정으로](#manage-settings) 구성해야 합니다. 미리 "스티커"로 표시하면 이벤트 및 출력이 적절한 인스턴스로 전달됩니다.

## <a name="manage-settings"></a>설정 관리

[!INCLUDE [app-service-deployment-slots-settings](../../includes/app-service-deployment-slots-settings.md)]

### <a name="create-a-deployment-setting"></a>배포 설정 만들기

설정을 배포 설정으로 표시하여 "고정"할 수 있습니다. 고정 설정은 앱 인스턴스와 교환되지 않습니다.

한 슬롯에 배포 설정을 만드는 경우 스왑과 관련된 다른 슬롯에 고유한 값을 사용하여 동일한 설정을 만들어야 합니다. 이렇게 하면 설정 값이 변경되지 않지만 설정 이름은 슬롯 간에 일관되게 유지됩니다. 이 이름 일관성을 사용하면 코드가 한 슬롯에 정의되었지만 다른 슬롯에 정의되지 않은 설정에 액세스하려고 시도하지 않습니다.

다음 단계를 사용하여 배포 설정을 만듭니다.

- 함수 앱의 *슬롯으로* 이동
- 슬롯 이름을 클릭합니다.
- *플랫폼 기능 > 일반 설정에서* **구성을** 클릭합니다.
- 현재 슬롯에 붙이려는 설정 이름을 클릭합니다.
- 배포 슬롯 설정 확인란을 **클릭합니다.**
- **확인**을 클릭합니다.
- 블레이드 설정이 사라지면 **저장을** 클릭하여 변경 내용을 유지합니다.

![배포 슬롯 설정](./media/functions-deployment-slots/azure-functions-deployment-slots-deployment-setting.png)

## <a name="deployment"></a>배포

슬롯을 만들 때 슬롯이 비어 있습니다. [지원되는 배포 기술을](./functions-deployment-technologies.md) 사용하여 응용 프로그램을 슬롯에 배포할 수 있습니다.

## <a name="scaling"></a>확장

모든 슬롯은 생산 슬롯과 동일한 수의 작업자수로 확장됩니다.

- 소비 계획의 경우 슬롯은 함수 앱의 배율로 확장됩니다.
- 앱 서비스 계획의 경우 앱은 고정된 수의 작업자로 확장됩니다. 슬롯은 앱 계획과 동일한 수의 작업자에서 실행됩니다.

## <a name="add-a-slot"></a>슬롯 추가

[CLI](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-create) 또는 포털을 통해 슬롯을 추가할 수 있습니다. 다음 단계에서는 포털에서 새 슬롯을 만드는 방법을 보여 줍니다.

1. 함수 앱으로 이동하여 *슬롯*옆에 있는 **더하기 기호를** 클릭합니다.

    ![Azure 함수 배포 슬롯 추가](./media/functions-deployment-slots/azure-functions-deployment-slots-add.png)

1. 텍스트 상자에 이름을 입력하고 **만들기** 단추를 누릅니다.

    ![이름 Azure 함수 배포 슬롯](./media/functions-deployment-slots/azure-functions-deployment-slots-add-name.png)

## <a name="swap-slots"></a>슬롯 교체

[CLI](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-swap) 또는 포털을 통해 슬롯을 교체할 수 있습니다. 다음 단계는 포털에서 슬롯을 교체하는 방법을 보여 줍니다.

1. 함수 앱으로 이동
1. 교환할 소스 슬롯 이름을 클릭합니다.
1. *개요* 탭에서 Azure Functions 배포 ![슬롯 **교환** 단추를 클릭합니다.](./media/functions-deployment-slots/azure-functions-deployment-slots-swap.png)
1. 스왑에 대한 구성 설정을 확인하고 **스왑** ![Azure Functions 배포 슬롯을 클릭합니다.](./media/functions-deployment-slots/azure-functions-deployment-slots-swap-config.png)

스왑 작업이 실행되는 동안 작업이 잠시 걸릴 수 있습니다.

## <a name="roll-back-a-swap"></a>스왑 롤백

스왑으로 인해 오류가 발생하거나 단순히 스왑을 "취소"하려는 경우 초기 상태로 롤백할 수 있습니다. 미리 교환된 상태로 돌아가려면 다른 스왑을 수행하여 스왑을 반대로 합니다.

## <a name="remove-a-slot"></a>슬롯 제거

[CLI를](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-delete) 통해 또는 포털을 통해 슬롯을 제거할 수 있습니다. 다음 단계는 포털에서 슬롯을 제거하는 방법을 보여 줍니다.

1. 함수 앱 개요로 이동

1. 삭제 버튼을 **클릭합니다.**

    ![Azure 함수 배포 슬롯 추가](./media/functions-deployment-slots/azure-functions-deployment-slots-delete.png)

## <a name="automate-slot-management"></a>슬롯 관리 자동화

Azure [CLI를](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest)사용하여 슬롯에 대해 다음 작업을 자동화할 수 있습니다.

- [만들](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-create)
- [삭제](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-delete)
- [목록](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-list)
- [스왑](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-swap)
- [자동 스왑](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-auto-swap)

## <a name="change-app-service-plan"></a>앱 서비스 계획 변경

앱 서비스 계획에서 실행 중인 함수 앱을 사용하면 슬롯에 대한 기본 앱 서비스 계획을 변경할 수 있습니다.

> [!NOTE]
> 소비 계획에 따라 슬롯의 앱 서비스 계획을 변경할 수 없습니다.

다음 단계를 사용하여 슬롯의 앱 서비스 계획을 변경합니다.

1. 슬롯으로 이동

1. *플랫폼 기능*에서 모든 설정을 **클릭합니다.**

    ![앱 서비스 요금제 변경](./media/functions-deployment-slots/azure-functions-deployment-slots-change-app-service-settings.png)

1. 앱 **서비스 플랜을 클릭합니다.**

1. 새 앱 서비스 요금제 선택 또는 새 요금제 만들기

1. **확인**을 클릭합니다.

    ![앱 서비스 요금제 변경](./media/functions-deployment-slots/azure-functions-deployment-slots-change-app-service-select.png)


## <a name="limitations"></a>제한 사항

Azure Functions 배포 슬롯에는 다음과 같은 제한 사항이 있습니다.

- 앱에서 사용할 수 있는 슬롯 수는 요금제에 따라 다릅니다. 소비 계획은 하나의 배포 슬롯만 허용됩니다. 앱 서비스 요금제에서 실행되는 앱에 추가 슬롯을 사용할 수 있습니다.
- 슬롯을 교환하면 앱 설정이 와 `AzureWebJobsSecretStorageType` 같은 앱의 `files`키를 재설정합니다.
- 슬롯은 Linux 소비 계획에 사용할 수 없습니다.

## <a name="support-levels"></a>지원 수준

배포 슬롯에 대한 지원 수준은 두 가지가 있습니다.

- **일반 공급(GA)**: 생산용으로 완전히 지원되고 승인되었습니다.
- **미리 보기**: 아직 지원되지 는 않지만 향후 GA 상태에 도달할 것으로 예상됩니다.

| OS/호스팅 계획           | 지원 수준     |
| ------------------------- | -------------------- |
| 윈도우 소비       | 일반 공급 |
| 윈도우 프리미엄           | 일반 공급  |
| 윈도우 전용         | 일반 공급 |
| 리눅스 소비         | 지원되지 않음          |
| 리눅스 프리미엄             | 일반 공급  |
| 리눅스 전용           | 일반 공급 |

## <a name="next-steps"></a>다음 단계

- [Azure 함수의 배포 기술](./functions-deployment-technologies.md)
