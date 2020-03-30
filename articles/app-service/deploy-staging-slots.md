---
title: 스테이징 환경 설정
description: 비프로덕션 슬롯에 앱을 배포하고 프로덕션에 자동 스왑하는 방법을 알아봅니다. 배포에서 안정성을 높이고 앱 가동 중지 시간을 제거합니다.
ms.assetid: e224fc4f-800d-469a-8d6a-72bcde612450
ms.topic: article
ms.date: 03/04/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: 21e025088e59c7f65f848b332ecb393b05918261
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78300874"
---
# <a name="set-up-staging-environments-in-azure-app-service"></a>Azure App Service에서 스테이징 환경 설정
<a name="Overview"></a>

웹 앱, Linux, 모바일 백 엔드 또는 API 앱을 [Azure App Service에](https://go.microsoft.com/fwlink/?LinkId=529714)배포할 때 **표준,** **프리미엄**또는 **격리된** 앱 서비스 계획 계층에서 실행할 때 기본 프로덕션 슬롯 대신 별도의 배포 슬롯을 사용할 수 있습니다. 배포 슬롯은 자체 호스트 이름이 있는 라이브 앱입니다. 앱 콘텐츠 및 구성 요소는 프로덕션 슬롯을 포함하여 두 배포 슬롯 간에 교환될 수 있습니다. 

애플리케이션을 비프로덕션 슬롯에 배포하면 다음과 같은 이점이 있습니다.

* 프로덕션 슬롯과 교환하기 전에 준비 배포 슬롯에서 앱 변경 사항의 유효성을 검사할 수 있습니다.
* 먼저 슬롯으로 앱을 배포하고 프로덕션으로 교환하기 때문에 프로덕션으로 교환되기 전에 슬롯에 있는 모든 인스턴스가 준비되어 있는 상태입니다. 따라서 앱을 배포할 때 가동 중지가 발생하지 않습니다. 트래픽 리디렉션은 중단 없이 원활하게 수행되며 교환 작업으로 인해 삭제되는 요청은 없습니다. 사전 스왑 유효성 검사가 필요하지 않은 경우 [자동 스왑을](#Auto-Swap) 구성하여 이 전체 워크플로를 자동화할 수 있습니다.
* 교환 후에는 이전의 준비된 앱이 들어 있던 슬롯 안에 이전의 프로덕션 앱이 들어갑니다. 프로덕션 슬롯과 교환한 변경 내용이 예상과 다른 경우 같은 교환 작업을 즉시 수행하여 "마지막 양호 상태"로 돌아갈 수 있습니다.

각 App Service 계획 계층은 다양한 수의 배포 슬롯을 지원합니다. 배포 슬롯 사용에 대한 추가 요금은 없습니다. 앱 의 계층이 지원하는 슬롯 수를 확인하려면 [앱 서비스 제한을](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits)참조하세요. 

앱을 다른 계층으로 확장하려면 대상 계층이 앱에서 이미 사용하는 슬롯 수를 지원하는지 확인합니다. 예를 들어 앱에 슬롯이 5개 이상인 경우 **표준** 계층이 5개의 배포 슬롯만 지원하므로 **표준** 계층으로 축소할 수 없습니다. 

<a name="Add"></a>

## <a name="add-a-slot"></a>슬롯 추가
앱이 여러 배포 슬롯을 사용하도록 설정하려면 **표준,** **프리미엄**또는 **격리** 계층에서 실행되어야 합니다.


1. Azure [포털에서](https://portal.azure.com/) **앱 서비스를** 검색및 선택하고 앱을 선택합니다. 
   
    ![앱 서비스 검색](./media/web-sites-staged-publishing/search-for-app-services.png)
   

2. 왼쪽 창에서 배포 **슬롯** > **추가 슬롯을**선택합니다.
   
    ![새 배포 슬롯 추가](./media/web-sites-staged-publishing/QGAddNewDeploymentSlot.png)
   
   > [!NOTE]
   > 앱이 **표준,** **프리미엄**또는 **격리** 계층에 아직 없는 경우 준비된 게시를 사용하도록 설정하기 위해 지원되는 계층을 나타내는 메시지가 나타납니다. 이 시점에서 계속하기 전에 **업그레이드를** 선택하고 앱의 **배율** 탭으로 이동하는 옵션이 있습니다.
   > 

3. 슬롯 **대화 상자추가상자에서** 슬롯에 이름을 지정하고 다른 배포 슬롯에서 앱 구성을 복제할지 여부를 선택합니다. 계속하려면 **추가를** 선택합니다.
   
    ![구성 소스](./media/web-sites-staged-publishing/ConfigurationSource1.png)
   
    기존 슬롯에서 구성을 복제할 수 있습니다. 복제할 수 있는 설정에는 앱 설정, 연결 문자열, 언어 프레임워크 버전, 웹 소켓, HTTP 버전 및 플랫폼 비트 수가 포함됩니다.

4. 슬롯을 추가한 후 **닫기를** 선택하여 대화 상자를 닫습니다. 이제 배포 슬롯 **페이지에** 새 슬롯이 표시됩니다. 기본적으로 **트래픽 %는** 새 슬롯에 대해 0으로 설정되며 모든 고객 트래픽은 프로덕션 슬롯으로 라우팅됩니다.

5. 해당 슬롯의 리소스 페이지를 열려면 새 배포 슬롯을 선택합니다.
   
    ![배포 슬롯 제목](./media/web-sites-staged-publishing/StagingTitle.png)

    스테이징 슬롯에는 다른 App Service 앱과 마찬가지로 관리 페이지가 있습니다. 슬롯의 구성을 변경할 수 있습니다. 배포 슬롯을 보고 있다는 사실을 상기시키기 위해 페이지 상단에 슬롯 이름이 표시됩니다.

6. 슬롯의 리소스 페이지에서 앱 URL을 선택합니다. 배포 슬롯에는 자체 호스트 이름이 있으며 라이브 앱이기도 합니다. 배포 슬롯에 대한 공용 액세스를 제한하려면 [Azure App Service IP 제한을](app-service-ip-restrictions.md)참조하십시오.

다른 슬롯에서 설정을 복제하더라도 새 배포 슬롯에는 내용이 없습니다. 예를 들어 [Git 을 사용하여 이 슬롯에 게시할](app-service-deploy-local-git.md)수 있습니다. 다른 리포지토리 분기 또는 다른 리포지토리로부터 슬롯에 배포할 수 있습니다. 

<a name="AboutConfiguration"></a>

## <a name="what-happens-during-a-swap"></a>스왑 중에 발생하는 일

### <a name="swap-operation-steps"></a>스왑 작업 단계

일반적으로 스테이징 슬롯에서 프로덕션 슬롯으로 두 개의 슬롯을 교환하는 경우 App Service는 대상 슬롯에 가동 중지 시간이 발생하지 않도록 다음을 수행합니다.

1. 대상 슬롯(예: 프로덕션 슬롯)의 다음 설정을 소스 슬롯의 모든 인스턴스에 적용합니다. 
    - [슬롯별](#which-settings-are-swapped) 앱 설정 및 연결 문자열(해당하는 경우).
    - [연속 배포](deploy-continuous-deployment.md) 설정(활성화된 경우).
    - [앱 서비스 인증](overview-authentication-authorization.md) 설정(활성화된 경우)입니다.
    
    이러한 경우는 소스 슬롯의 모든 인스턴스를 트리거하여 다시 시작합니다. [미리 보기로 교환하는](#Multi-Phase)동안 첫 번째 단계의 끝을 표시합니다. 스왑 작업이 일시 중지되고 소스 슬롯이 대상 슬롯의 설정에서 올바르게 작동하는지 확인할 수 있습니다.

1. 소스 슬롯의 모든 인스턴스가 다시 시작될 때까지 기다립니다. 인스턴스를 다시 시작하지 못하면 스왑 작업이 모든 변경 내용을 소스 슬롯으로 되돌리고 작업을 중지합니다.

1. [로컬 캐시가](overview-local-cache.md) 활성화된 경우 원본 슬롯의 각 인스턴스에서 응용 프로그램 루트("/")에 HTTP 요청을 하여 로컬 캐시 초기화를 트리거합니다. 각 인스턴스가 HTTP 응답을 반환할 때까지 기다립니다. 로컬 캐시 초기화로 인해 각 인스턴스에서 다시 시작됩니다.

1. [사용자 지정 워밍업으로](#Warm-up) [자동 스왑을](#Auto-Swap) 사용하도록 설정하면 소스 슬롯의 각 인스턴스에서 응용 프로그램 루트("/")에 HTTP 요청을 하여 응용 프로그램 [시작을](https://docs.microsoft.com/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) 트리거합니다.

    `applicationInitialization` 지정하지 않으면 각 인스턴스의 소스 슬롯의 응용 프로그램 루트에 대한 HTTP 요청을 트리거합니다. 
    
    인스턴스가 HTTP 응답을 반환하면 워밍업된 것으로 간주됩니다.

1. 소스 슬롯의 모든 인스턴스가 성공적으로 워밍업되면 두 슬롯에 대한 라우팅 규칙을 전환하여 두 슬롯을 교체합니다. 이 단계 후에 대상 슬롯(예: 프로덕션 슬롯)에는 소스 슬롯에서 이전에 워밍업된 앱이 있습니다.

1. 이제 소스 슬롯에 이전에 대상 슬롯에 사전 스왑 앱이 생겼으니 모든 설정을 적용하고 인스턴스를 다시 시작하여 동일한 작업을 수행합니다.

스왑 작업의 모든 지점에서 교환된 앱을 초기화하는 모든 작업은 소스 슬롯에서 발생합니다. 소스 슬롯이 준비되고 워밍업되는 동안 대상 슬롯은 온라인 상태로 유지됩니다. 스테이징 슬롯을 프로덕션 슬롯과 교환하려면 프로덕션 슬롯이 항상 대상 슬롯인지 확인합니다. 이렇게 하면 스왑 작업이 프로덕션 앱에 영향을 주지 않습니다.

### <a name="which-settings-are-swapped"></a>어떤 설정이 교환되나요?

[!INCLUDE [app-service-deployment-slots-settings](../../includes/app-service-deployment-slots-settings.md)]

특정 슬롯에 충실하도록 앱 설정 또는 연결 문자열을 구성하려면 해당 슬롯의 **구성** 페이지로 이동합니다. 설정을 추가하거나 편집한 다음 **배포 슬롯 설정을**선택합니다. 이 확인란을 선택하면 앱 서비스에 설정을 교환할 수 없다는 것을 알려줍니다. 

![슬롯 설정](./media/web-sites-staged-publishing/SlotSetting.png)

<a name="Swap"></a>

## <a name="swap-two-slots"></a>두 슬롯 교환 
앱의 배포 슬롯 페이지와 **개요** 페이지에서 배포 **슬롯을** 교체할 수 있습니다. 슬롯 스왑에 대한 기술 적 세부 사항은 [스왑 중에 발생하는 일을](#AboutConfiguration)참조하십시오.

> [!IMPORTANT]
> 배포 슬롯에서 프로덕션으로 앱을 교체하기 전에 프로덕션이 대상 슬롯이고 원본 슬롯의 모든 설정이 프로덕션 환경에서 앱을 원하는 대로 정확하게 구성되었는지 확인합니다.
> 
> 

배포 슬롯을 교체하려면 다음을 수행합니다.

1. 앱의 **배포 슬롯** 페이지로 이동하여 **스왑을**선택합니다.
   
    ![스왑 버튼](./media/web-sites-staged-publishing/SwapButtonBar.png)

    **[교환** 대화 상자]에는 변경될 선택한 소스 및 대상 슬롯의 설정이 표시됩니다.

2. 원하는 **원본** 및 **대상** 슬롯을 선택합니다. 일반적으로 대상은 프로덕션 슬롯입니다. 또한 소스 **변경** 및 **대상 변경** 탭을 선택하고 구성 변경이 예상되는지 확인합니다. 작업이 완료되면 **스왑을**선택하여 슬롯을 즉시 교체할 수 있습니다.

    ![전체 교환](./media/web-sites-staged-publishing/SwapImmediately.png)

    스왑이 실제로 발생하기 전에 대상 슬롯이 새 설정으로 실행되는 방식을 보려면 **스왑을**선택하지 말고 [미리 보기로 스왑의](#Multi-Phase)지침을 따르십시오.

3. 완료되면 **닫기**를 선택하여 대화 상자를 닫습니다.

문제가 있는 경우 [문제 해결 스왑을](#troubleshoot-swaps)참조하십시오.

<a name="Multi-Phase"></a>

### <a name="swap-with-preview-multi-phase-swap"></a>미리 보기가 있는 교환(다단계 교환)

프로덕션을 대상 슬롯으로 전환하기 전에 앱이 교체된 설정으로 실행되는지 확인합니다. 소스 슬롯은 스왑 완료 전에 예열되어 미션 크리티컬 애플리케이션에 적합합니다.

미리 보기로 스왑을 수행하면 앱 서비스는 동일한 [스왑 작업을](#AboutConfiguration) 수행하지만 첫 번째 단계 이후에 일시 중지됩니다. 그런 다음 스왑을 완료하기 전에 스테이징 슬롯에서 결과를 확인할 수 있습니다. 

스왑을 취소하면 앱 서비스는 구성 요소를 소스 슬롯에 다시 적용합니다.

미리 보기로 바꾸려면 다음을 수행합니다.

1. [스왑 배포 슬롯의](#Swap) 단계를 따르되 **미리 보기로 스왑 수행을 선택합니다.**

    ![미리 보기가 있는 교환](./media/web-sites-staged-publishing/SwapWithPreview.png)

    대화 상자에는 소스 슬롯의 구성이 1단계에서 어떻게 변경되는지, 그리고 소스 슬롯과 대상 슬롯이 2단계에서 어떻게 변경되는지를 보여 주실 수 있습니다.

2. 스왑을 시작할 준비가 되면 **스왑 시작을**선택합니다.

    1단계가 끝나면 대화 상자에 알림이 표시됩니다. 로 이동하여 소스 슬롯의 스왑을 미리 봅슬입니다. `https://<app_name>-<source-slot-name>.azurewebsites.net` 

3. 보류 중인 스왑을 완료할 준비가 되면 **스왑 작업에서** **스왑 완료를** 선택하고 **전체 스왑을**선택합니다.

    보류 중인 스왑을 취소하려면 대신 **스왑 취소를** 선택합니다.

4. 완료되면 **닫기**를 선택하여 대화 상자를 닫습니다.

문제가 있는 경우 [문제 해결 스왑을](#troubleshoot-swaps)참조하십시오.

다상 스왑을 자동화하려면 [PowerShell으로 자동화를](#automate-with-powershell)참조하십시오.

<a name="Rollback"></a>

## <a name="roll-back-a-swap"></a>스왑 롤백
슬롯 교환 후 대상 슬롯(예: 프로덕션 슬롯)에서 오류가 발생하면 2개의 동일한 슬롯을 즉시 교환하여 슬롯을 교환 전 상태로 복원합니다.

<a name="Auto-Swap"></a>

## <a name="configure-auto-swap"></a>자동 교환 구성

> [!NOTE]
> 자동 스왑은 Linux의 웹 앱에서 지원되지 않습니다.

자동 스왑은 콜드 스타트가 없고 앱 고객의 가동 중지 시간이 0으로 앱을 지속적으로 배포하려는 Azure DevOps 시나리오를 간소화합니다. 슬롯에서 프로덕션으로 자동 스왑을 사용하도록 설정하면 코드 변경 내용을 해당 슬롯으로 푸시할 때마다 App Service는 소스 슬롯에서 워밍업한 후 [앱을 프로덕션으로](#swap-operation-steps) 자동으로 교환합니다.

   > [!NOTE]
   > 프로덕션 슬롯에 대한 자동 스왑을 구성하기 전에 비프로덕션 대상 슬롯에서 자동 스왑을 테스트하는 것이 좋습니다.
   > 

자동 스왑을 구성하려면 다음을 수행하십시오.

1. 앱의 리소스 페이지로 이동합니다.  >  **구성**일반  > *\<설정>원하는 소스 * **슬롯을** > 선택합니다.**General settings**
   
2. **자동 스왑을 사용하므로** **를 선택합니다.** 그런 다음 **자동 스왑 배포**슬롯에 대해 원하는 대상 슬롯을 선택하고 명령 모음에서 **저장을** 선택합니다. 
   
    ![자동 스왑 구성을 위한 선택](./media/web-sites-staged-publishing/AutoSwap02.png)

3. 소스 슬롯에 대해 코드 푸시를 실행합니다. 자동 스왑은 짧은 시간 후에 발생하며 업데이트는 대상 슬롯의 URL에 반영됩니다.

문제가 있는 경우 [문제 해결 스왑을](#troubleshoot-swaps)참조하십시오.

<a name="Warm-up"></a>

## <a name="specify-custom-warm-up"></a>사용자 지정 워밍업 지정

일부 앱은 스왑 전에 사용자 지정 워밍업 작업이 필요할 수 있습니다. web.config의 `applicationInitialization` 구성 요소를 사용하면 사용자 지정 초기화 작업을 지정할 수 있습니다. [스왑 작업은](#AboutConfiguration) 대상 슬롯으로 교환하기 전에 이 사용자 지정 워밍업이 완료될 때까지 기다립니다. 다음은 샘플 web.config 조각입니다.

    <system.webServer>
        <applicationInitialization>
            <add initializationPage="/" hostName="[app hostname]" />
            <add initializationPage="/Home/About" hostName="[app hostname]" />
        </applicationInitialization>
    </system.webServer>

요소 사용자 지정에 대한 자세한 내용은 [가장 일반적인 배포 슬롯 스왑 실패 및 이를 수정하는 방법을](https://ruslany.net/2017/11/most-common-deployment-slot-swap-failures-and-how-to-fix-them/)참조하십시오. `applicationInitialization`

다음 [앱 설정](configure-common.md)중 하나 또는 둘 다로 워밍업 동작을 사용자 지정할 수도 있습니다.

- `WEBSITE_SWAP_WARMUP_PING_PATH`: 사이트를 워밍업하기 위해 핑하는 경로입니다. 슬래시로 시작하는 사용자 지정 경로를 값으로 지정하여 이 앱 설정을 추가합니다. 예제는 `/statuscheck`입니다. 기본값은 `/`입니다. 
- `WEBSITE_SWAP_WARMUP_PING_STATUSES`: 워밍업 작업에 유효한 HTTP 응답 코드입니다. HTTP 코드의 쉼표로 구분된 목록을 사용하여 이 앱 설정을 추가합니다. 예를 들어 `200,202` . 반환된 상태 코드가 목록에 없는 경우 워밍업 및 스왑 작업이 중지됩니다. 기본적으로 모든 응답 코드는 유효합니다.

> [!NOTE]
> `<applicationInitialization>` 구성 요소는 각 앱 시작의 일부이지만 두 개의 워밍업 동작 앱 설정은 슬롯 스왑에만 적용됩니다.

문제가 있는 경우 [문제 해결 스왑을](#troubleshoot-swaps)참조하십시오.

## <a name="monitor-a-swap"></a>스왑 모니터링

[스왑 작업을](#AboutConfiguration) 완료하는 데 시간이 오래 걸리는 경우 [활동 로그에서](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)스왑 작업에 대한 정보를 얻을 수 있습니다.

포털의 앱 리소스 페이지에서 왼쪽 창에서 **활동 로그를**선택합니다.

교환 작업은 `Swap Web App Slots`으로 로그 쿼리에 표시됩니다. 이를 확장하고 하위 작업 또는 오류 중 하나를 선택하여 세부 정보를 볼 수 있습니다.

## <a name="route-traffic"></a>트래픽 라우팅

기본적으로 앱의 프로덕션 URL에 대한 모든 클라이언트 요청(`http://<app_name>.azurewebsites.net`)은 프로덕션 슬롯으로 라우팅됩니다. 트래픽의 일부를 다른 슬롯으로 라우팅할 수 있습니다. 이 기능은 새 업데이트에 대한 사용자 피드백이 필요하지만 프로덕션 환경으로 릴리스할 준비가 되지 않은 경우에 유용합니다.

### <a name="route-production-traffic-automatically"></a>자동으로 프로덕션 트래픽 라우팅

생산 트래픽을 자동으로 라우팅하려면 다음을 수행합니다.

1. 앱의 리소스 페이지로 이동하여 **배포 슬롯을**선택합니다.

2. 라우팅하려는 슬롯의 **트래픽 %** 열에서 라우팅할 트래픽의 총량을 나타내는 백분율(0~100)을 지정합니다. **저장**을 선택합니다.

    ![트래픽 비율 설정](./media/web-sites-staged-publishing/RouteTraffic.png)

설정을 저장하면 클라이언트의 지정된 백분율이 비프로덕션 슬롯으로 임의로 라우팅됩니다. 

클라이언트가 특정 슬롯으로 자동으로 라우팅되면 해당 클라이언트 세션의 수명 동안 해당 슬롯에 "고정"됩니다. 클라이언트 브라우저에서 HTTP 헤더의 `x-ms-routing-name` 쿠키를 확인하여 세션이 고정된 슬롯을 볼 수 있습니다. "스테이징" 슬롯에 라우팅되는 요청에는 쿠키 `x-ms-routing-name=staging`이 있습니다. 프로덕션 슬롯으로 라우팅되는 요청에는 쿠키 `x-ms-routing-name=self`가 있습니다.

   > [!NOTE]
   > Azure 포털 옆에 Azure CLI의 [`az webapp traffic-routing set`](/cli/azure/webapp/traffic-routing#az-webapp-traffic-routing-set) 명령을 사용하여 DevOps 파이프라인 또는 기타 자동화 시스템과 같은 CI/CD 도구의 라우팅 백분율을 설정할 수도 있습니다.
   > 

### <a name="route-production-traffic-manually"></a>수동으로 프로덕션 트래픽 라우팅

자동 트래픽 라우팅 외에도 App Service는 특정 슬롯에 요청을 라우팅할 수 있습니다. 이 기능은 사용자가 베타 앱을 옵트인하거나 옵트아웃할 수 있도록 하려는 경우에 유용합니다. 수동으로 프로덕션 트래픽을 라우팅하려면 `x-ms-routing-name` 쿼리 매개 변수를 사용합니다.

예를 들어 사용자가 베타 앱을 옵트아웃할 수 있도록 하려면 이 링크를 웹 페이지에 넣을 수 있습니다.

```HTML
<a href="<webappname>.azurewebsites.net/?x-ms-routing-name=self">Go back to production app</a>
```

문자열 `x-ms-routing-name=self` 는 프로덕션 슬롯을 지정합니다. 클라이언트 브라우저가 링크에 액세스하면 프로덕션 슬롯으로 리디렉션됩니다. 이후의 모든 `x-ms-routing-name=self` 요청에는 세션을 프로덕션 슬롯에 고정하는 쿠키가 있습니다.

사용자가 베타 앱에 옵트인할 수 있도록 하려면 동일한 쿼리 매개 변수를 비프로덕션 슬롯의 이름으로 설정합니다. 예를 들면 다음과 같습니다.

```
<webappname>.azurewebsites.net/?x-ms-routing-name=staging
```

기본적으로 새 슬롯에는 회색으로 표시된 라우팅 `0%`규칙이 지정됩니다. 이 값을 검정 `0%` 색 텍스트로 명시적으로 설정하면 사용자는 `x-ms-routing-name` 쿼리 매개 변수를 사용하여 스테이징 슬롯에 수동으로 액세스할 수 있습니다. 그러나 라우팅 백분율이 0으로 설정되어 있으므로 슬롯으로 자동으로 라우팅되지 않습니다. 이 시나리오는 내부 팀이 슬롯의 변경 내용을 테스트할 수 있도록 하면서 준비 슬롯을 공개에서 "숨길" 수 있는 고급 시나리오입니다.

<a name="Delete"></a>

## <a name="delete-a-slot"></a>슬롯 삭제

앱을 검색하고 선택합니다. 배포 >   >  **Overview** **슬롯을**선택하여 개요>삭제합니다.*\< * 명령 모음에서 **삭제를** 선택합니다.  

![배포 슬롯 삭제](./media/web-sites-staged-publishing/DeleteStagingSiteButton.png)

<!-- ======== AZURE POWERSHELL CMDLETS =========== -->

<a name="PowerShell"></a>

## <a name="automate-with-powershell"></a>PowerShell을 사용하여 자동화

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Azure PowerShell은 Windows PowerShell을 통해 Azure를 관리하기 위한 cmdlet을 제공하는 모듈로, Azure App Service에서 배포 슬롯을 관리하는 기능도 지원합니다.

Azure PowerShell을 설치 및 구성하는 방법과 Azure 구독에 Azure PowerShell을 인증하는 방법에 대한 자세한 내용은 [Microsoft Azure PowerShell 설치 및 구성 방법](/powershell/azure/overview)을 참조하세요.  

---
### <a name="create-a-web-app"></a>웹앱 만들기
```powershell
New-AzWebApp -ResourceGroupName [resource group name] -Name [app name] -Location [location] -AppServicePlan [app service plan name]
```

---
### <a name="create-a-slot"></a>슬롯 만들기
```powershell
New-AzWebAppSlot -ResourceGroupName [resource group name] -Name [app name] -Slot [deployment slot name] -AppServicePlan [app service plan name]
```

---
### <a name="initiate-a-swap-with-a-preview-multi-phase-swap-and-apply-destination-slot-configuration-to-the-source-slot"></a>미리 보기(다단계 스왑)로 스왑을 시작하고 소스 슬롯에 대상 슬롯 구성을 적용합니다.
```powershell
$ParametersObject = @{targetSlot  = "[slot name – e.g. "production"]"}
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action applySlotConfig -Parameters $ParametersObject -ApiVersion 2015-07-01
```

---
### <a name="cancel-a-pending-swap-swap-with-review-and-restore-the-source-slot-configuration"></a>보류 중인 스왑(검토와 함께 스왑)을 취소하고 소스 슬롯 구성을 복원합니다.
```powershell
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action resetSlotConfig -ApiVersion 2015-07-01
```

---
### <a name="swap-deployment-slots"></a>배포 슬롯 교환
```powershell
$ParametersObject = @{targetSlot  = "[slot name – e.g. "production"]"}
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action slotsswap -Parameters $ParametersObject -ApiVersion 2015-07-01
```

### <a name="monitor-swap-events-in-the-activity-log"></a>활동 로그의 스왑 이벤트 모니터링
```powershell
Get-AzLog -ResourceGroup [resource group name] -StartTime 2018-03-07 -Caller SlotSwapJobProcessor  
```

---
### <a name="delete-a-slot"></a>슬롯 삭제
```powershell
Remove-AzResource -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots –Name [app name]/[slot name] -ApiVersion 2015-07-01
```

## <a name="automate-with-resource-manager-templates"></a>리소스 관리자 템플릿으로 자동화

[Azure 리소스 관리자 템플릿은](https://docs.microsoft.com/azure/azure-resource-manager/template-deployment-overview) Azure 리소스의 배포 및 구성을 자동화하는 데 사용되는 선언적 JSON 파일입니다. 리소스 관리자 템플릿을 사용하여 슬롯을 교환하려면 *Microsoft.Web/사이트/슬롯* 및 *Microsoft.Web/사이트* 리소스에 두 개의 속성을 설정합니다.

- `buildVersion`: 슬롯에 배포된 앱의 현재 버전을 나타내는 문자열 속성입니다. 예: "v1", "1.0.0.1" 또는 "2019-09-20T11:53:25.2887393-07:00".
- `targetBuildVersion`: 슬롯에 있어야 하는 것을 `buildVersion` 지정하는 문자열 속성입니다. targetBuildVersion현재와 `buildVersion`같지 않으면 지정된 `buildVersion`슬롯을 찾아 스왑 작업을 트리거합니다.

### <a name="example-resource-manager-template"></a>리소스 관리자 템플릿 예제

다음 리소스 관리자 템플릿은 `buildVersion` 스테이징 슬롯을 업데이트하고 프로덕션 슬롯을 `targetBuildVersion` 설정합니다. 이렇게 하면 두 개의 슬롯이 교체됩니다. 템플릿은 "스테이징"이라는 슬롯으로 이미 웹랩을 만들었다고 가정합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "my_site_name": {
            "defaultValue": "SwapAPIDemo",
            "type": "String"
        },
        "sites_buildVersion": {
            "defaultValue": "v1",
            "type": "String"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Web/sites/slots",
            "apiVersion": "2018-02-01",
            "name": "[concat(parameters('my_site_name'), '/staging')]",
            "location": "East US",
            "kind": "app",
            "properties": {
                "buildVersion": "[parameters('sites_buildVersion')]"
            }
        },
        {
            "type": "Microsoft.Web/sites",
            "apiVersion": "2018-02-01",
            "name": "[parameters('my_site_name')]",
            "location": "East US",
            "kind": "app",
            "dependsOn": [
                "[resourceId('Microsoft.Web/sites/slots', parameters('my_site_name'), 'staging')]"
            ],
            "properties": {
                "targetBuildVersion": "[parameters('sites_buildVersion')]"
            }
        }        
    ]
}
```

이 리소스 관리자 템플릿은 idempotent이므로 반복적으로 실행하고 슬롯의 동일한 상태를 생성할 수 있습니다. 첫 번째 실행 `targetBuildVersion` 후 현재와 `buildVersion`일치하므로 스왑이 트리거되지 않습니다.

<!-- ======== Azure CLI =========== -->

<a name="CLI"></a>

## <a name="automate-with-the-cli"></a>CLI로 자동화

배포 슬롯에 대한 [Azure CLI](https://github.com/Azure/azure-cli) 명령은 [az webapp deployment slot](/cli/azure/webapp/deployment/slot)을 참조하세요.

## <a name="troubleshoot-swaps"></a>트러블슈팅 스왑

[슬롯 스왑](#AboutConfiguration)중에 오류가 발생하면 *D:\home\LogFiles\eventlog.xml에 기록됩니다.* 또한 응용 프로그램별 오류 로그에 기록됩니다.

다음은 몇 가지 일반적인 스왑 오류입니다.

- 응용 프로그램 루트에 대한 HTTP 요청의 시간 time이 정해지습니다. 스왑 작업은 각 HTTP 요청에 대해 90초 동안 대기하고 최대 5회 다시 시도합니다. 모든 재시도가 시간 만으로 지정되면 스왑 작업이 중지됩니다.

- 앱 콘텐츠가 로컬 캐시에 대해 지정된 로컬 디스크 할당량을 초과하면 로컬 캐시 초기화가 실패할 수 있습니다. 자세한 내용은 [로컬 캐시 개요를](overview-local-cache.md)참조하십시오.

- [사용자 지정 워밍업](#Warm-up)중에 HTTP 요청은 외부 URL을 거치지 않고 내부적으로 이루어집니다. *Web.config*에서 특정 URL 다시 쓰기 규칙으로 실패할 수 있습니다. 예를 들어 도메인 이름을 리디렉션하거나 HTTPS를 적용하는 규칙은 워밍업 요청이 앱 코드에 도달하지 못하도록 할 수 있습니다. 이 문제를 해결하려면 다음 두 가지 조건을 추가하여 다시 쓰기 규칙을 수정합니다.

    ```xml
    <conditions>
      <add input="{WARMUP_REQUEST}" pattern="1" negate="true" />
      <add input="{REMOTE_ADDR}" pattern="^100?\." negate="true" />
      ...
    </conditions>
    ```
- 사용자 지정 워밍업이 없으면 URL 다시 쓰기 규칙은 여전히 HTTP 요청을 차단할 수 있습니다. 이 문제를 해결하려면 다음 조건을 추가하여 다시 쓰기 규칙을 수정합니다.

    ```xml
    <conditions>
      <add input="{REMOTE_ADDR}" pattern="^100?\." negate="true" />
      ...
    </conditions>
    ```
- 일부 [IP 제한 규칙은](app-service-ip-restrictions.md) 스왑 작업이 앱에 HTTP 요청을 보내지 못하도록 할 수 있습니다. IPv4 주소 범위는 `10.` 배포에서 `100.` 시작되고 배포내부입니다. 앱에 연결할 수 있도록 허용해야 합니다.

- 슬롯 스왑 후 앱이 예기치 않은 다시 시작될 수 있습니다. 이는 스왑 후 호스트 이름 바인딩 구성이 동기화되지 않아 자체적으로 다시 시작되지 않기 때문입니다. 그러나 특정 기본 저장소 이벤트(예: 저장소 볼륨 장애 조치)는 이러한 불일치를 감지하고 모든 작업자 프로세스를 다시 시작하도록 할 수 있습니다. 이러한 유형의 다시 시작을 최소화하려면 *모든 슬롯에서* [ `WEBSITE_ADD_SITENAME_BINDINGS_IN_APPHOST_CONFIG=1` 앱 설정을](https://github.com/projectkudu/kudu/wiki/Configurable-settings#disable-the-generation-of-bindings-in-applicationhostconfig) 설정합니다. 그러나 이 앱 *설정은* WCF(Windows 통신 재단) 앱에서는 작동하지 않습니다.

## <a name="next-steps"></a>다음 단계
[비프로덕션 슬롯에 대한 액세스 차단](app-service-ip-restrictions.md)
