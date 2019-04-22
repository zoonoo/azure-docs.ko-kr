---
title: 기존 Azure Service Bus 표준 네임 스페이스 프리미엄 계층으로 마이그레이션 | Microsoft Docs
description: 기존 Azure Service Bus 표준 네임 스페이스 Premium으로의 마이그레이션 허용 하는 가이드
services: service-bus-messaging
documentationcenter: ''
author: axisc
manager: darosa
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/18/2019
ms.author: aschhab
ms.openlocfilehash: 7b153c36e10f1d4e2be2a0cf42f998c31cb6473a
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/18/2019
ms.locfileid: "58896584"
---
# <a name="migrate-existing-azure-service-bus-standard-namespaces-to-premium-tier"></a>프리미엄 계층으로 기존 Azure Service Bus 표준 네임 스페이스 마이그레이션

이전에 Azure Service Bus 네임 스페이스의 표준 계층 에서만 제공 됩니다. 이러한 낮은 처리량 및 개발자 환경에 최적화 된 다중 테 넌 트 설정 되었습니다.

가장 최근에 Azure Service Bus 확장 된 예측 가능한 대기 시간 및 높은 처리량 및 프로덕션 환경에 최적화 된 고정된 가격으로 향상 된 처리량에 대 한 네임 스페이스 당 전용된 리소스를 제공 하는 프리미엄 계층을 제공 합니다. 추가 엔터프라이즈 기능을 요구합니다.

기존 표준 계층 네임 스페이스를 프리미엄 계층으로 마이그레이션할 수 있도록 도구 아래.

>[!WARNING]
> 마이그레이션하려는 Service Bus 표준 네임 스페이스에 대 한 되도록 ***업그레이드*** 프리미엄 계층으로 합니다.
>
> 마이그레이션 도구 ***하지 않습니다*** 다운 그레이드를 지원 합니다.
>[!NOTE]
> 이 마이그레이션을 수행 하려는 ***곳에서***합니다.
>
> 즉, 기존 발신자와 수신자 응용 프로그램 코드 또는 구성을 변경할 필요 하지 않습니다.
>
> 기존 연결 문자열을 자동으로 새 프리미엄 네임 스페이스를 가리킵니다.
>
> 표준 네임 스페이스의 모든 엔터티에 또한 **복사한** 마이그레이션 프로세스 중에 프리미엄 네임 스페이스에서입니다.
>
>
> 지원 ***메시징 단위 1,000 개 엔터티*** Premium에 얼마나 많은 메시징 단위를 식별 하려면 다음을 수행 해야 합니다를 시작 하세요 현재 표준 네임 스페이스에 있는 엔터티의 수를 사용 하 여 합니다.

## <a name="migration-steps"></a>마이그레이션 단계

>[!IMPORTANT]
> 마이그레이션 프로세스와 관련 된 몇 가지 주의 사항이 있습니다. 오류의 가능성을 줄이기 위해 관련 된 단계를 완전히 숙지 하 요청.

구체적인 단계별 마이그레이션 프로세스는 아래 가이드에 자세히 설명 되어 있습니다.

논리적 단계는

1. 새 프리미엄 네임 스페이스를 만듭니다.
2. 표준 및 프리미엄 네임 스페이스를 서로 연결 합니다.
3. 표준에서 프리미엄 네임 스페이스에 동기화 (복사-over) 엔터티
4. 마이그레이션 커밋
5. 네임 스페이스의 마이그레이션 후 이름을 사용 하 여 표준 네임 스페이스의 엔터티를 드레이닝
6. 표준 네임 스페이스를 삭제 합니다.

>[!NOTE]
> 마이그레이션에 커밋된 후에 이전 표준 네임 스페이스에 액세스 하 여 큐 및 구독 드레이닝 매우 중요 합니다.
>
> 메시지가 종료 된 후 이러한 전송할 수 있습니다 새 프리미엄 네임 스페이스 수신자 응용 프로그램에서 처리 되도록 합니다.
>
> 큐 및 구독 종료 된 후에 이전 표준 네임 스페이스를 삭제 하는 것이 좋습니다. 이 이후에 하 필요 없습니다!

### <a name="migrate-using-azure-cli-or-powershell"></a>Azure CLI 또는 PowerShell을 사용 하 여 마이그레이션

프리미엄 Azure CLI 또는 PowerShell 도구를 사용 하 여 Service Bus 표준 네임 스페이스를 마이그레이션하려면 참조는 아래 가이드입니다.

1. 새 Service Bus 프리미엄 네임 스페이스를 만듭니다. 참조할 수 있습니다 합니다 [Azure Resource Manager 템플릿](service-bus-resource-manager-namespace.md) 또는 [Azure portal을 사용 하 여](service-bus-create-namespace-portal.md)입니다. 에 대 한 "프리미엄"를 선택 해야 합니다 **serviceBusSku** 매개 변수입니다.

2. 설정 된 아래 마이그레이션 명령을 단순화 하기 위해 환경 변수입니다.
   ```
   resourceGroup = <resource group for the standard namespace>
   standardNamespace = <standard namespace to migrate>
   premiumNamespaceArmId = <Azure Resource Manager ID of the Premium namespace to migrate to>
   postMigrationDnsName = <post migration DNS name entry to access the Standard namespace>
   ```

    >[!IMPORTANT]
    > 마이그레이션 후 이름은 (post_migration_dns_name) 이전 표준 네임 스페이스 마이그레이션 후 액세스에 사용 됩니다. 큐 및 구독을 소비 하 고 다음 네임 스페이스를 삭제 하려면이 사용 해야 합니다.

3. **쌍** 표준 및 프리미엄 네임 스페이스 및 **동기화 시작** 를 사용 하는 명령-아래

    ```
    az servicebus migration start --resource-group $resourceGroup --name $standardNamespace --target-namespace $premiumNamespaceArmId --post-migration-name $postMigrationDnsName
    ```


4. 사용 하 여 마이그레이션 상태를 확인 합니다 명령-아래
    ```
    az servicebus migration show --resource-group $resourceGroup --name $standardNamespace
    ```

    마이그레이션 완료 되었다고 간주 될 때
    * MigrationState = "활성"
    * pendingReplicationsOperationsCount = 0
    * provisioningState "성공" =

    또한이 명령은 마이그레이션 구성을 표시합니다. 값이 이전으로 설정 되어 있는지 확인 하려면 다시 확인 선언 하세요.

    또한 모든 큐와 토픽을 만든 표준 네임 스페이스에 존재 무엇과 일치 하는지 확인 하려면 포털에서 프리미엄 네임 스페이스를 확인할 수도 있습니다.

5. 아래 전체 명령을 실행 하 여 마이그레이션 커밋
   ```
   az servicebus migration complete --resource-group $resourceGroup --name $standardNamespace
   ```

### <a name="migrate-using-azure-portal"></a>Azure portal을 사용 하 여 마이그레이션

Azure portal 통해 마이그레이션 명령을 사용 하 여 마이그레이션할 때와 동일한 논리 흐름을 있습니다. 참조는 아래 포털을 사용 하 여 마이그레이션하려면 단계별 프로세스에 대 한 가이드입니다.

1. 선택 된 **Premium으로 마이그레이션'** 왼쪽된 창에서 탐색 메뉴에서 메뉴 옵션입니다. 클릭 합니다 **' 시작 '** 다음 페이지로 이동 하려면 단추입니다.
    ![마이그레이션 방문 페이지][]

2. 전체 **설치**합니다.
   ![네임 스페이스를 설정 합니다.][]
   1. 기존 표준 네임 스페이스를 마이그레이션하는 프리미엄 네임 스페이스를 만들고 설정 합니다.
        ![네임 스페이스 설정-프리미엄 네임 스페이스 만들기][]
   2. 선택는 **'마이그레이션 후 name'** 마이그레이션이 완료 된 후 표준 네임 스페이스에서 액세스할 수 있습니다.
        ![네임 스페이스 설정-사후 마이그레이션 이름 선택][]
   3. 클릭 **'다음'** 진행 합니다.
3. **동기화** 표준 및 프리미엄 네임 스페이스 간 엔터티.
    ![네임 스페이스 동기화 엔터티에-시작 설정][]

   1. 클릭 **동기화 시작 '** 엔터티 동기화를 시작 합니다.
   2. 클릭 **'예'** 동기화를 시작 하려면 확인 팝업에서.
   3. 될 때까지 대기 합니다 **동기화** 완료 된 합니다. 상태는 상태 표시줄에 가능 합니다.
        ![설치 네임 스페이스 동기화 엔터티에-진행률][]
        >[!IMPORTANT]
        > 해야 할 경우 **중단** 어떤 이유로이 문서의 FAQ 섹션에서 중단 흐름을 검토 하세요.
   4. 동기화 완료 되 면 클릭 합니다 **'다음'** 페이지의 맨 위에 있는 단추입니다.

4. 요약 페이지에서 변경 내용을 검토 합니다.
    ![네임 스페이스-스위치 메뉴 전환][]

5. 클릭할 **마이그레이션 ' 완료 '** 네임 스페이스를 전환 하 고 마이그레이션을 완료 합니다.
    ![네임 스페이스 스위치-성공][]

## <a name="faqs"></a>FAQ

### <a name="what-happens-when-the-migration-is-committed"></a>마이그레이션 커밋될 때 어떻게 되나요?

마이그레이션을 커밋한 후 표준 네임 스페이스를 가리키는 연결 문자열 프리미엄 네임 스페이스를 가리킵니다.

발신자와 수신자 응용 프로그램은 표준 Namespace에서 끊고 프리미엄 네임 스페이스를 자동으로 다시 연결 합니다.

### <a name="what-do-i-do-after-the-standard-to-premium-migration-is-complete"></a>무엇을 표준-프리미엄 마이그레이션 완료 된 후 합니까?

표준-프리미엄 마이그레이션은 엔터티 메타 데이터 (항목, 구독, 필터, et al) 복사 됩니다 표준에서 프리미엄 네임 스페이스를 확인 합니다. 표준 네임 스페이스에 커밋된 메시지 데이터는 복사 되지 표준에서 프리미엄 네임 스페이스에.

이 인해 표준 네임 스페이스에는 전송 된 커밋된 마이그레이션이 진행 되는 동안 일부 메시지가 있을 수 있습니다. 이러한 메시지는 수동으로 표준 Namespace에서 배출할를 수동으로 Premium Namespace을 통해 전송 수 해야 합니다.

이렇게 하려면 있습니다 ***해야 합니다*** 콘솔 앱 또는 스크립트를 사용 하 여 표준 네임 스페이스 엔터티를 드레이닝을 사용 합니다 **Post 마이그레이션 DNS 이름** 마이그레이션 명령에 지정 된 다음 보냅니다 하에서 메시지 Premium Namespace 수신자가 처리할 수 있도록 합니다.

메시지를이 종료 된 후에 표준 네임 스페이스를 삭제를 진행 하십시오.

>[!IMPORTANT]
> 참고는 표준 네임 스페이스에서 메시지를이 종료 된 후 있습니다 **해야** 표준 네임 스페이스를 삭제 합니다.
>
> 이 연결 문자열 처음에 이제 표준 네임 스페이스를 참조 하는 실제로 참조 하는 프리미엄 네임 스페이스에 중요 합니다. 있습니다 필요 표준 Namespace이 더 이상.
>
> 나중에 혼동을 줄일 수 있습니다 마이그레이션한 표준 네임 스페이스를 삭제 합니다. 

### <a name="how-much-downtime-do-i-expect"></a>가동 중지 시간이 얼마나 부담이 있습니까?
위에서 설명한 마이그레이션 프로세스는 응용 프로그램에 대 한 예상된 가동 중지 시간을 줄이기 위해 것입니다. 이 새 프리미엄 네임 스페이스를 가리키도록 발신자와 수신자 응용 프로그램을 사용 하는 연결 문자열을 활용 하 여 이루어집니다.

응용 프로그램에서 숙련 된 가동 중지 시간 프리미엄 네임 스페이스를 가리키도록 DNS 항목을 업데이트 하는 데 걸리는 시간으로 제한 됩니다.

이 것으로 간주 될 수 있습니다 ***약 5 분***합니다.

### <a name="do-i-have-to-make-any-configuration-changes-while-performing-the-migration"></a>마이그레이션을 수행 하는 동안 구성을 변경 해야 합니까?
아니요,이 마이그레이션을 수행 하는 데 필요한 코드/구성 변경 내용이 있습니다. 연결 문자열을 표준 Namespace 액세스를 사용 하 여 송신자와 수신자 응용 프로그램 역할을 하도록 자동으로 매핑되는 **별칭** Premium Namespace에 있습니다.

### <a name="what-happens-when-i-abort-the-migration"></a>I 마이그레이션을 중단 하 고 어떻게 되나요?
마이그레이션 '중단' 명령을 사용 하 여 또는 Azure portal을 통해 중단 될 수 있습니다. 

#### <a name="azure-cli-or-powershell"></a>Azure CLI 또는 PowerShell

    az servicebus migration abort --resource-group $resourceGroup --name $standardNamespace

#### <a name="azure-portal"></a>Azure portal

![흐름을 중단-동기화 중단][]
![흐름 중단-중단 완료][]

마이그레이션 프로세스 중단 될 때 실제로 표준에서 프리미엄 네임 스페이스에 엔터티 (항목, 구독 및 필터)를 통해 복사 프로세스를 중단 하 고 연결이 중단 합니다.

연결 문자열 **아닙니다** 프리미엄 네임 스페이스를 가리키도록 업데이트 합니다. 기존 응용 프로그램 계속 마이그레이션을 시작 하기 전에 마찬가지로 작동 합니다.

그러나 그 **하지 않습니다** 프리미엄 네임 스페이스에서 엔터티를 삭제 하거나 자체 프리미엄 네임 스페이스를 삭제 합니다. 이 앞으로 이동 하지는 마이그레이션을 사용 하 여 결국 하기로 결정 했습니다 수동으로 수행 해야 합니다.

>[!IMPORTANT]
> 마이그레이션을 중단 하려는 경우 리소스에 대 한 요금이 부과 되지 않습니다 있도록 마이그레이션에 대 한 프로 비전 했습니다 Premium Namespace를 삭제 하십시오.

#### <a name="i-dont-want-to-have-to-drain-the-messages-what-do-i-do"></a>메시지를 비울 필요가 안 함 어떻게 해야 합니까?

메시지 보낸 사람 응용 프로그램에서 전송 되 고 마이그레이션이 진행 되는 동안, 표준 Namespace에 저장소에 커밋에 있을 수 있으며 오른쪽 마이그레이션 전에 커밋됩니다.

마이그레이션 중에 실제 메시지 데이터 페이로드 복사 되지를 통해 표준에서 Premium으로는 이러한 경우 수동으로 종료 되어 다음 프리미엄 네임 스페이스에 전송

그러나 계획 된 유지 관리/관리 기간 동안 마이그레이션할 수 있습니다 하 고 수동으로 비우고 메시지를 전송 하지 않으려는 경우 따르세요는 아래 단계-

1. 보낸 사람 응용 프로그램을 중지 하 고 현재 표준 네임 스페이스에 큐를 비울 하는 메시지를 처리 하는 수신기를 허용 합니다.
2. 큐 및 구독에서 표준 Namespace 빈 되 면 표준에서 프리미엄 네임 스페이스 마이그레이션을 실행 위에 설명 된 절차를 따릅니다.
3. 마이그레이션이 완료 되 면 발신자 응용 프로그램을 다시 시작할 수 있습니다.
4. 발신자와 수신자가 프리미엄 네임 스페이스를 사용 하 여 자동으로 연결 이제 됩니다.

    >[!NOTE]
    > 마이그레이션에 대 한 수신기를 중지할 수 필요 합니다.
    >
    > 마이그레이션이 완료 되 면 수신자는 표준 네임 스페이스에서 끊고 프리미엄 네임 스페이스에 자동으로 연결 합니다.

## <a name="next-steps"></a>다음 단계

* 에 대 한 자세한 정보는 [표준 및 프리미엄 메시징 간의 차이점](./service-bus-premium-messaging.md)
* Service Bus 프리미엄에 대 한 고가용성 및 지역 재해 복구 측면을 알아봅니다 [여기](service-bus-outages-disasters.md#protecting-against-outages-and-disasters---service-bus-premium)

[마이그레이션 방문 페이지]: ./media/service-bus-standard-premium-migration/1.png
[네임 스페이스를 설정 합니다.]: ./media/service-bus-standard-premium-migration/2.png
[네임 스페이스 설정-프리미엄 네임 스페이스 만들기]: ./media/service-bus-standard-premium-migration/3.png
[네임 스페이스 설정-사후 마이그레이션 이름 선택]: ./media/service-bus-standard-premium-migration/4.png
[네임 스페이스 동기화 엔터티에-시작 설정]: ./media/service-bus-standard-premium-migration/5.png
[설치 네임 스페이스 동기화 엔터티에-진행률]: ./media/service-bus-standard-premium-migration/8.png
[네임 스페이스-스위치 메뉴 전환]: ./media/service-bus-standard-premium-migration/9.png
[네임 스페이스 스위치-성공]: ./media/service-bus-standard-premium-migration/12.png

[흐름을 중단-동기화를 중단 합니다.]: ./media/service-bus-standard-premium-migration/abort1.png
[흐름을 중단-중단 완료]: ./media/service-bus-standard-premium-migration/abort3.png
