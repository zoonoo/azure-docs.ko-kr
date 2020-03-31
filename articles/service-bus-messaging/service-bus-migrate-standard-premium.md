---
title: Azure Service 버스 네임스페이스 마이그레이션 - 표준에서 프리미엄으로
description: 기존 Azure Service 버스 표준 네임스페이스를 프리미엄으로 마이그레이션할 수 있는 가이드
services: service-bus-messaging
documentationcenter: ''
author: axisc
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2019
ms.author: aschhab
ms.openlocfilehash: 27e3260b91bebee14ff12188a7dbd6c7cf76355c
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2020
ms.locfileid: "80385030"
---
# <a name="migrate-existing-azure-service-bus-standard-namespaces-to-the-premium-tier"></a>기존 Azure 서비스 버스 표준 네임스페이스를 프리미엄 계층으로 마이그레이션

이전에는 Azure Service Bus에서 표준 계층에서만 네임스페이스를 제공했습니다. 네임스페이스는 낮은 처리량 및 개발자 환경에 최적화된 다중 테넌트 설정입니다. 프리미엄 계층은 예측 가능한 대기 시간 및 고정 된 가격으로 증가 된 처리량을 위해 네임 스페이스당 전용 리소스를 제공합니다. 프리미엄 계층은 추가 엔터프라이즈 기능이 필요한 높은 처리량 및 프로덕션 환경에 최적화되어 있습니다.

이 문서에서는 기존 표준 계층 네임스페이스를 프리미엄 계층으로 마이그레이션하는 방법에 대해 설명합니다.  

>[!WARNING]
> 마이그레이션은 서비스 버스 표준 네임스페이스를 프리미엄 계층으로 업그레이드하기 위한 것입니다. 마이그레이션 도구는 다운그레이드를 지원하지 않습니다.

주의해야 할 몇 가지 사항은 다음과 같은 점입니다.

- 이 마이그레이션은 기존 보낸 사람이나 수신자 응용 프로그램에서 **코드 또는 구성을 변경할 필요가 없다는 것을 의미합니다.** 기존 연결 문자열은 새 프리미엄 네임스페이스를 자동으로 가리킵니다.
- **마이그레이션이** 성공하려면 프리미엄 네임스페이스에 **엔터티가 없어야** 합니다.
- 표준 네임스페이스의 모든 **엔터티는** 마이그레이션 프로세스 중에 프리미엄 네임스페이스에 **복사됩니다.**
- 마이그레이션은 프리미엄 계층의 **메시징 단위당 1,000개의 엔터티를** 지원합니다. 필요한 메시징 단위 수를 확인하려면 현재 표준 네임스페이스에 있는 엔터티 수로 시작합니다.
- 기본 계층에서 **프리미엄 계층으로**직접 마이그레이션할 수는 없지만 기본 **계층에서** 표준 계층으로 먼저 마이그레이션한 다음 다음 단계에서 표준에서 프리미엄으로 간접적으로 마이그레이션할 수 있습니다.

## <a name="migration-steps"></a>마이그레이션 단계

일부 조건은 마이그레이션 프로세스와 연결됩니다. 오류 가능성을 줄이려면 다음 단계를 숙지하십시오. 다음 단계에는 마이그레이션 프로세스가 간략하게 설명되며 단계별 세부 정보가 다음 섹션에 나열됩니다.

1. 새 프리미엄 네임스페이스를 만듭니다.
1. 표준 및 프리미엄 네임스페이스를 서로 페어링합니다.
1. 표준에서 프리미엄 네임스페이스로 엔터티를 동기화(복사-오버) 합니다.
1. 마이그레이션을 커밋합니다.
1. 네임스페이스의 마이그레이션 후 이름을 사용하여 표준 네임스페이스에서 엔터티를 드레인합니다.
1. 표준 네임스페이스를 삭제합니다.

>[!IMPORTANT]
> 마이그레이션이 커밋된 후 이전 표준 네임스페이스에 액세스하고 큐 및 구독을 드레인합니다. 메시지가 드레인된 후 수신자 응용 프로그램에서 처리할 새 프리미엄 네임스페이스로 전송될 수 있습니다. 큐와 구독이 소진된 후에는 이전 표준 네임스페이스를 삭제하는 것이 좋습니다.

### <a name="migrate-by-using-the-azure-cli-or-powershell"></a>Azure CLI 또는 PowerShell을 사용하여 마이그레이션

Azure CLI 또는 PowerShell 도구를 사용하여 서비스 버스 표준 네임스페이스를 프리미엄으로 마이그레이션하려면 다음 단계를 따르십시오.

1. 새 Service Bus 프리미엄 네임스페이스를 만듭니다. Azure 리소스 [관리자 템플릿을](service-bus-resource-manager-namespace.md) 참조하거나 [Azure 포털](service-bus-create-namespace-portal.md)을 사용할 수 있습니다. **serviceBusSku** 매개 변수에 대 한 **프리미엄을** 선택 해야 합니다.

1. 마이그레이션 명령을 단순화하려면 다음 환경 변수를 설정합니다.

   ```
   resourceGroup = <resource group for the standard namespace>
   standardNamespace = <standard namespace to migrate>
   premiumNamespaceArmId = <Azure Resource Manager ID of the premium namespace to migrate to>
   postMigrationDnsName = <post migration DNS name entry to access the standard namespace>
   ```

    >[!IMPORTANT]
    > 마이그레이션 후 별칭/이름(post_migration_dns_name)은 이전 표준 네임스페이스 마이그레이션에 액세스하는 데 사용됩니다. 이를 사용하여 큐와 구독을 빼낸 다음 네임스페이스를 삭제합니다.

1. 표준 및 프리미엄 네임스페이스를 페어링하고 다음 명령을 사용하여 동기화를 시작합니다.

    ```azurecli-interactive
    az servicebus migration start --resource-group $resourceGroup --name $standardNamespace --target-namespace $premiumNamespaceArmId --post-migration-name $postMigrationDnsName
    ```

1. 다음 명령을 사용하여 마이그레이션 상태를 확인합니다.

    ```azurecli-interactive
    az servicebus migration show --resource-group $resourceGroup --name $standardNamespace
    ```

    마이그레이션은 다음 값이 표시되면 완료된 것으로 간주됩니다.

    * 마이그레이션 상태 = "활성"
    * 보류 중인 복제본작업Count = 0
    * 프로비저닝 상태 = "성공"

    이 명령에는 마이그레이션 구성도 표시됩니다. 값이 올바르게 설정되었는지 확인합니다. 또한 포털의 프리미엄 네임스페이스를 확인하여 모든 큐와 토픽이 만들어졌는지, 표준 네임스페이스에 있는 것과 일치하는지 확인합니다.

1. 다음 의 완전한 명령을 실행하여 마이그레이션을 커밋합니다.

   ```azurecli-interactive
   az servicebus migration complete --resource-group $resourceGroup --name $standardNamespace
   ```

### <a name="migrate-by-using-the-azure-portal"></a>Azure 포털을 사용하여 마이그레이션

Azure 포털을 사용 하 여 마이그레이션 명령을 사용 하 여 마이그레이션으로 마이그레이션과 동일한 논리 흐름을 가있습니다. 다음 단계에 따라 Azure 포털을 사용하여 마이그레이션합니다.

1. 왼쪽 창의 **탐색** 메뉴에서 **프리미엄으로 마이그레이션을**선택합니다. 시작 **하기** 단추를 클릭하여 다음 페이지로 계속합니다.
    ![마이그레이션 방문 페이지][]

1. **전체 설정**.
   ![설치 네임스페이스][]
   1. 프리미엄 네임스페이스를 작성하고 할당하여 기존 표준 네임스페이스를 마이그레이션합니다.
        ![설치 네임스페이스 - 프리미엄 네임스페이스 만들기][]
   1. 마이그레이션 **후 이름을**선택합니다. 마이그레이션이 완료된 후 이 이름을 사용하여 표준 네임스페이스에 액세스합니다.
        ![설치 네임스페이스 - 포스트 마이그레이션 이름 선택][]
   1. **계속하려면 '다음'을** 선택합니다.
1. 표준 네임스페이스와 프리미엄 네임스페이스 간에 엔터티를 동기화합니다.
    ![설치 네임스페이스 - 동기화 엔터티 - 시작][]

   1. 엔터티 **동기화를** 시작하려면 동기화 시작을 선택합니다.
   1. 대화 상자에서 **예를** 선택하여 동기화를 확인하고 시작합니다.
   1. 동기화가 완료될 때까지 기다립니다. 상태는 상태 표시줄에서 사용할 수 있습니다.
        ![설치 네임스페이스 - 동기화 엔터티 - 진행률][]
        >[!IMPORTANT]
        > 어떤 이유로든 마이그레이션을 중단해야 하는 경우 이 문서의 FAQ 섹션의 중단 흐름을 검토하십시오.
   1. 동기화가 완료되면 페이지 하단에서 **다음을** 선택합니다.

1. 요약 페이지에서 변경 내용을 검토합니다. 전체 **마이그레이션을** 선택하여 네임스페이스를 전환하고 마이그레이션을 완료합니다.
    ![네임스페이스 전환 - 스위치 메뉴][]  
    마이그레이션이 완료되면 확인 페이지가 나타납니다.
    ![네임스페이스 전환 - 성공][]

## <a name="caveats"></a>제한 사항

Azure 서비스 버스 표준 계층에서 제공하는 일부 기능은 Azure 서비스 버스 프리미엄 계층에서 지원되지 않습니다. 프리미엄 계층은 예측 가능한 처리량 및 대기 시간에 전용 리소스를 제공하므로 이러한 리소스는 의도적으로 설계되었습니다.

다음은 프리미엄 및 해당 완화에서 지원되지 않는 기능 목록입니다.

### <a name="express-entities"></a>Express 엔터티

   메시지 데이터를 저장소에 커밋하지 않는 익스프레스 엔터티는 Premium에서 지원되지 않습니다. 전용 리소스는 모든 엔터프라이즈 메시징 시스템에서 예상되는 대로 데이터가 유지되도록 하면서 상당한 처리량 향상을 제공했습니다.

   마이그레이션하는 동안 표준 네임스페이스의 모든 익스프레스 엔터티가 프리미엄 네임스페이스에 비익스프레스 엔터티로 만들어집니다.

   ARM(Azure Resource Manager) 템플릿을 사용하는 경우 자동화된 워크플로가 오류 없이 실행되도록 배포 구성에서 'enableExpress' 플래그를 제거해야 합니다.

### <a name="partitioned-entities"></a>분할된 엔터티

   분할된 엔터티는 다중 테넌트 설정에서 더 나은 가용성을 제공하기 위해 표준 계층에서 지원되었습니다. Premium 계층의 네임스페이스당 사용할 수 있는 전용 리소스를 제공하면 더 이상 필요하지 않습니다.

   마이그레이션하는 동안 표준 네임스페이스의 분할된 엔터티는 Premium 네임스페이스에서 분할되지 않은 엔터티로 만들어집니다.

   ARM 템플릿이 특정 큐 또는 토픽에 대해 'true'로 '인파티션'을 설정하면 브로커가 무시합니다.

## <a name="faqs"></a>FAQ

### <a name="what-happens-when-the-migration-is-committed"></a>마이그레이션이 커밋되면 어떻게 됩니까?

마이그레이션이 커밋된 후 표준 네임스페이스를 가리키는 연결 문자열은 프리미엄 네임스페이스를 가리킵니다.

발신자 및 수신자 응용 프로그램은 표준 네임스페이스에서 연결을 끊고 프리미엄 네임스페이스에 자동으로 다시 연결됩니다.

### <a name="what-do-i-do-after-the-standard-to-premium-migration-is-complete"></a>프리미엄 마이그레이션에 대한 표준이 완료된 후에는 어떻게 해야 합니까?

프리미엄 마이그레이션표준은 토픽, 구독 및 필터와 같은 엔터티 메타데이터가 표준 네임스페이스에서 프리미엄 네임스페이스로 복사되도록 합니다. 표준 네임스페이스에 커밋된 메시지 데이터는 표준 네임스페이스에서 프리미엄 네임스페이스로 복사되지 않습니다.

표준 네임스페이스에는 마이그레이션이 진행되는 동안 전송되고 커밋된 일부 메시지가 있을 수 있습니다. 이러한 메시지를 표준 네임스페이스에서 수동으로 드레인하고 프리미엄 네임스페이스로 수동으로 보냅니다. 메시지를 수동으로 드레인하려면 마이그레이션 명령에 지정한 마이그레이션 후 DNS 이름을 사용하여 표준 네임스페이스 엔터티를 제거하는 콘솔 앱 또는 스크립트를 사용합니다. 이러한 메시지를 수신자가 처리할 수 있도록 프리미엄 네임스페이스로 보냅니다.

메시지가 드레인된 후 표준 네임스페이스를 삭제합니다.

>[!IMPORTANT]
> 표준 네임스페이스의 메시지가 드레인된 후 표준 네임스페이스를 삭제합니다. 처음에 표준 네임스페이스를 참조한 연결 문자열이 프리미엄 네임스페이스를 참조하기 때문에 중요합니다. 더 이상 표준 네임스페이스가 필요하지 않습니다. 마이그레이션한 표준 네임스페이스를 삭제하면 나중에 혼동을 줄일 수 있습니다.

### <a name="how-much-downtime-do-i-expect"></a>얼마나 많은 가동 중지 시간을 예상합니까?

마이그레이션 프로세스는 응용 프로그램에 대한 예상 가동 중지 시간을 줄이기 위한 것입니다. 보낸 사람이와 받는 사람이 새 프리미엄 네임스페이스를 가리키는 데 사용하는 연결 문자열을 사용하면 가동 중지 시간이 줄어듭니다.

응용 프로그램에서 경험하는 가동 중지 시간은 프리미엄 네임스페이스를 가리키도록 DNS 항목을 업데이트하는 데 걸리는 시간으로 제한됩니다. 가동 중지 시간은 약 5분입니다.

### <a name="do-i-have-to-make-any-configuration-changes-while-doing-the-migration"></a>마이그레이션을 수행하는 동안 구성을 변경해야 합니까?

아니요, 마이그레이션을 수행하는 데 필요한 코드 나 구성 변경 사항이 없습니다. 보낸 사람이와 수신자 응용 프로그램이 표준 네임스페이스에 액세스하는 데 사용하는 연결 문자열은 프리미엄 네임스페이스의 별칭역할을 하도록 자동으로 매핑됩니다.

### <a name="what-happens-when-i-abort-the-migration"></a>마이그레이션을 중단하면 어떻게 되나요?

`Abort` 명령을 사용하거나 Azure 포털을 사용하여 마이그레이션을 중단할 수 있습니다.

#### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az servicebus migration abort --resource-group $resourceGroup --name $standardNamespace
```

#### <a name="azure-portal"></a>Azure portal

![중단 흐름 - 중단 동기화][]
![중단 흐름 - 중단 완료][]

마이그레이션 프로세스가 중단되면 엔터티(토픽, 구독 및 필터)를 표준에서 프리미엄 네임스페이스로 복사하는 프로세스를 중단하고 페어링을 중단합니다.

프리미엄 네임스페이스를 가리키도록 연결 문자열이 업데이트되지 않습니다. 기존 응용 프로그램은 마이그레이션을 시작하기 전에와 마찬가지로 계속 작동합니다.

그러나 프리미엄 네임스페이스의 엔터티를 삭제하거나 프리미엄 네임스페이스를 삭제하지는 않습니다. 마이그레이션을 진행하지 않기로 결정한 경우 엔터티를 수동으로 삭제합니다.

>[!IMPORTANT]
> 마이그레이션을 중단하기로 결정한 경우 리소스에 대한 요금이 부과되지 않도록 마이그레이션에 프로비전한 프리미엄 네임스페이스를 삭제합니다.

#### <a name="i-dont-want-to-have-to-drain-the-messages-what-do-i-do"></a>메시지를 빼내고 싶지 않습니다. 어떻게 해야 합니까?

마이그레이션이 이루어지는 동안 및 마이그레이션이 커밋되기 직전에 보낸 사람 응용 프로그램에서 보내고 표준 네임스페이스의 저장소에 커밋되는 메시지가 있을 수 있습니다.

마이그레이션하는 동안 실제 메시지 데이터/페이로드는 표준에서 프리미엄 네임스페이스로 복사되지 않습니다. 메시지를 수동으로 드레인한 다음 프리미엄 네임스페이스로 보내야 합니다.

그러나 계획된 유지 관리/하우스키핑 기간 동안 마이그레이션할 수 있고 수동으로 메시지를 드레인하고 보내지 않으려면 다음 단계를 따르십시오.

1. 보낸 사람의 응용 프로그램을 중지합니다. 수신기 응용 프로그램은 현재 표준 네임스페이스에 있는 메시지를 처리하고 큐를 드레인합니다.
1. 표준 네임스페이스의 큐와 구독이 비어 있으면 앞에서 설명한 절차를 따라 표준에서 프리미엄 네임스페이스로의 마이그레이션을 실행합니다.
1. 마이그레이션이 완료되면 보낸 사람의 응용 프로그램을 다시 시작할 수 있습니다.
1. 이제 발신자와 수신자가 프리미엄 네임스페이스에 자동으로 연결됩니다.

    >[!NOTE]
    > 마이그레이션을 위해 수신기 응용 프로그램을 중지할 필요가 없습니다.
    >
    > 마이그레이션이 완료되면 수신기 응용 프로그램이 표준 네임스페이스에서 연결을 끊고 프리미엄 네임스페이스에 자동으로 연결됩니다.

## <a name="next-steps"></a>다음 단계

* [표준 메시징과 프리미엄 메시징의 차이점에](./service-bus-premium-messaging.md)대해 자세히 알아보십시오.
* [서비스 버스 프리미엄에 대한 고가용성 및 지리적 재해 복구 측면에 대해](service-bus-outages-disasters.md#protecting-against-outages-and-disasters---service-bus-premium)알아봅니다.

[마이그레이션 방문 페이지]: ./media/service-bus-standard-premium-migration/1.png
[설치 네임스페이스]: ./media/service-bus-standard-premium-migration/2.png
[설치 네임스페이스 - 프리미엄 네임스페이스 만들기]: ./media/service-bus-standard-premium-migration/3.png
[설치 네임스페이스 - 포스트 마이그레이션 이름 선택]: ./media/service-bus-standard-premium-migration/4.png
[설치 네임스페이스 - 동기화 엔터티 - 시작]: ./media/service-bus-standard-premium-migration/5.png
[설치 네임스페이스 - 동기화 엔터티 - 진행률]: ./media/service-bus-standard-premium-migration/8.png
[네임스페이스 전환 - 스위치 메뉴]: ./media/service-bus-standard-premium-migration/9.png
[네임스페이스 전환 - 성공]: ./media/service-bus-standard-premium-migration/12.png

[중단 흐름 - 중단 동기화]: ./media/service-bus-standard-premium-migration/abort1.png
[중단 흐름 - 중단 완료]: ./media/service-bus-standard-premium-migration/abort3.png
