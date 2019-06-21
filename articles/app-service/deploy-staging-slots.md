---
title: Azure App Service에서 웹앱에 대한 스테이징 환경 설정| Microsoft Docs
description: Azure App Service에서 웹앱에 대한 준비된 개시를 사용하는 방법에 대해 알아봅니다.
services: app-service
documentationcenter: ''
author: cephalin
writer: cephalin
manager: jpconnoc
editor: mollybos
ms.assetid: e224fc4f-800d-469a-8d6a-72bcde612450
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/18/2019
ms.author: cephalin
ms.openlocfilehash: cbf287aef2c1792033a198070da605014a7b6281
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67272844"
---
# <a name="set-up-staging-environments-in-azure-app-service"></a>Azure App Service에서 스테이징 환경 설정
<a name="Overview"></a>

웹앱, Linux의 웹앱, 모바일 백 엔드 및 API 앱을 [App Service](https://go.microsoft.com/fwlink/?LinkId=529714)에 배포할 때 **표준**, **프리미엄** 또는 **격리** App Service 계획 계층에서 실행하면 기본 프로덕션 슬롯 대신 별도의 배포 슬롯에 배포할 수 있습니다. 배포 슬롯은 실제로 고유한 호스트 이름이 있는 라이브 앱입니다. 앱 콘텐츠 및 구성 요소는 프로덕션 슬롯을 포함하여 두 배포 슬롯 간에 교환될 수 있습니다. 애플리케이션을 비프로덕션 슬롯에 배포하면 다음과 같은 이점이 있습니다.

* 프로덕션 슬롯과 교환하기 전에 준비 배포 슬롯에서 앱 변경 사항의 유효성을 검사할 수 있습니다.
* 먼저 슬롯으로 앱을 배포하고 프로덕션으로 교환하기 때문에 프로덕션으로 교환되기 전에 슬롯에 있는 모든 인스턴스가 준비되어 있는 상태입니다. 따라서 앱을 배포할 때 가동 중지가 발생하지 않습니다. 트래픽 리디렉션은 중단 없이 원활하게 수행되며 교환 작업으로 인해 삭제되는 요청은 없습니다. 사전 교환 유효성 검사가 필요하지 않은 경우 [자동 교환](#Auto-Swap)을 구성하여 이 전체 워크플로를 자동화할 수 있습니다.
* 교환 후에는 이전의 준비된 앱이 들어 있던 슬롯 안에 이전의 프로덕션 앱이 들어갑니다. 프로덕션 슬롯과 교환한 변경 내용이 예상과 다른 경우 같은 교환 작업을 즉시 수행하여 "마지막 양호 상태"로 돌아갈 수 있습니다.

각 App Service 계획 계층은 여러 배포 슬롯을 지원 하 고 배포 슬롯 사용에 대 한 추가 비용은 없습니다. 앱 계층이 지원하는 슬롯의 수를 알아보려면 [App Service 제한](https://docs.microsoft.com/azure/azure-subscription-service-limits#app-service-limits)을 참조하세요. 다른 계층으로 앱의 크기를 조정하려면 대상 계층은 앱에서 이미 사용하는 슬롯 수를 지원해야 합니다. 예를 들어 앱에 5개 이상의 슬롯이 있는 경우 **표준** 계층은 5개의 배포 슬롯만을 지원하므로 **표준** 계층으로 축소할 수 없습니다. 

<a name="Add"></a>

## <a name="add-slot"></a>슬롯 추가
여러 배포 슬롯을 사용하려면 앱이 **표준**, **프리미엄** 또는 **격리** 계층에서 실행 중이어야 합니다.

1. [Azure Portal](https://portal.azure.com/)에서 앱의 [리소스 페이지](../azure-resource-manager/manage-resources-portal.md#manage-resources)를 엽니다.

2. 왼쪽된 탐색 영역에서 선택 합니다 **배포 슬롯** 옵션을 선택한 다음 클릭 **슬롯 추가**합니다.
   
    ![새 배포 슬롯 추가](./media/web-sites-staged-publishing/QGAddNewDeploymentSlot.png)
   
   > [!NOTE]
   > 앱이 **표준**, **프리미엄** 또는 **격리** 계층이 아닌 경우 준비된 게시를 사용하려면 지원 계층을 나타내는 메시지를 받게 됩니다. 이때 **업그레이드**를 선택할 수 있는 옵션이 제공되고 계속하기 전에 앱의 **크기 조정** 탭으로 이동합니다.
   > 

3. **슬롯 추가** 대화 상자에서 슬롯에 이름을 지정하고, 다른 기존 배포 슬롯으로부터 앱 구성을 복제할 것인지 여부를 선택합니다. **추가**를 클릭하여 계속합니다.
   
    ![구성 소스](./media/web-sites-staged-publishing/ConfigurationSource1.png)
   
    기존 슬롯에서 구성을 복제할 수 있습니다. 복제할 수 있는 설정에는 앱 설정, 연결 문자열, 언어 프레임워크 버전, 웹 소켓, HTTP 버전 및 플랫폼 비트 수가 포함됩니다.

4. 슬롯이 추가되면 **닫기**를 클릭하여 대화 상자를 닫습니다. 새 슬롯은 이제에 표시 된 **배포 슬롯** 페이지입니다. 기본적으로 **트래픽 %** 는 모든 고객 트래픽이 프로덕션 슬롯으로 라우팅되는 새 슬롯에서 0으로 설정됩니다.

5. 새 배포 슬롯을 클릭하여 해당 슬롯의 리소스 페이지를 엽니다.
   
    ![배포 슬롯 제목](./media/web-sites-staged-publishing/StagingTitle.png)

    스테이징 슬롯에는 다른 App Service 앱과 마찬가지로 관리 페이지가 있습니다. 슬롯의 구성을 변경할 수 있습니다. 배포 슬롯을 보고 있다는 사실을 상기시키기 위해 페이지 상단에 슬롯 이름이 표시됩니다.

6. 슬롯의 리소스 페이지에서 앱 URL을 클릭합니다. 배포 슬롯은 고유의 호스트 이름을 가지고 있고 라이브 앱이기도 합니다. 배포 슬롯에 대한 공용 액세스를 제한하려면 [Azure App Service IP 제한 사항](app-service-ip-restrictions.md)을 참조하세요.

다른 슬롯에서 설정을 복제하더라도 새 배포 슬롯에는 내용이 없습니다. 예를 들어 [git를 사용하여 이 슬롯에 게시](app-service-deploy-local-git.md)할 수 있습니다. 다른 리포지토리 분기 또는 다른 리포지토리로부터 슬롯에 배포할 수 있습니다. 

<a name="AboutConfiguration"></a>

## <a name="what-happens-during-swap"></a>스왑 중 진행 상황

[작업 단계를 교환할](#swap-operation-steps)
[어떤 설정이 교환 되나요?](#which-settings-are-swapped)

### <a name="swap-operation-steps"></a>스왑 작업 단계

(일반적으로 프로덕션 슬롯과 스테이징 슬롯)에서 두 슬롯을 교환 하는 경우 App Service는 대상 슬롯 가동 중지 시간이 발생 하지는 않도록 다음을 수행 합니다.

1. 대상 슬롯 (예: 프로덕션 슬롯)에서 다음 설정을 원본 슬롯의 모든 인스턴스에 적용 됩니다. 
    - [슬롯 별](#which-settings-are-swapped) 앱 설정 및 연결 문자열을 해당 하는 경우.
    - [연속 배포](deploy-continuous-deployment.md) 설정이 사용 하도록 설정 합니다.
    - [App Service 인증](overview-authentication-authorization.md) 설정이 사용 하도록 설정 합니다.
    위의 경우 중 하나를 다시 시작 원본 슬롯의 모든 인스턴스를 트리거합니다. 하는 동안 [미리 보기가 있는 교환](#Multi-Phase),이 첫 번째 단계, 여기서 스왑 작업을 일시 중지 하 고 대상 슬롯의 설정을 사용 하 여 원본 슬롯 제대로 작동 하는지 확인할 수 있습니다의 끝을 표시 합니다.

1. 해당 다시 시작을 완료 하려면 원본 슬롯의 모든 인스턴스에 대 한 대기 합니다. 모든 인스턴스를 다시 시작 하지 못하는 경우 교환 작업이 원본 슬롯에 모든 변경 내용이 되돌립니다 및 작업을 중단 합니다.

1. 하는 경우 [로컬 캐시](overview-local-cache.md) 는 HTTP 응용 프로그램 루트 ("/") 각 인스턴스의 원본 슬롯을 요청 하 고 각 인스턴스는 모든 HTTP 응답을 반환 될 때까지 대기 하 여 로컬 캐시 초기화를 트리거 사용 하도록 설정 합니다. 로컬 캐시 초기화는 각 인스턴스에서 다른 다시 시작을 하면 됩니다.

1. 하는 경우 [자동 교환](#Auto-Swap) 사용 하도록 설정 되었는지 [사용자 지정 준비](#custom-warm-up), 트리거 [응용 프로그램 시작](https://docs.microsoft.com/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) 응용 프로그램 루트 ("/") 원본의 각 인스턴스에서 HTTP 요청을 수행 하 여 슬롯입니다. 모든 HTTP 응답을 반환 하는 인스턴스 준비를 간주 합니다.

    없으면 `applicationInitialization` 는 각 인스턴스에서 원본 슬롯의 응용 프로그램 루트에 HTTP 요청 트리거를 지정 합니다. 모든 HTTP 응답을 반환 하는 인스턴스 준비를 간주 합니다.

1. 원본 슬롯의 모든 인스턴스에 성공적으로 준비 됩니다을 하는 경우 두 슬롯에 대 한 라우팅 규칙을 전환 하 여 두 슬롯을 교환 합니다. 이 단계를 수행한 후 대상 슬롯 (예: 프로덕션 슬롯)에 원본 슬롯에 이전에 준비 하는 앱.

1. 원본 슬롯에 사전 교환 앱을 이전에 대상 슬롯의는 이제 모든 설정을 적용 하는 인스턴스를 다시 시작 하 여 동일한 작업을 수행 합니다.

언제 든 지 교환 작업의 교체 된 앱을 초기화 하는 모든 작업은 원본 슬롯에서 수행 됩니다. 대상 슬롯을 온라인 상태로 유지 된 원본 슬롯 되는 동안 준비 및 준비 위쪽에 관계 없이 교환을 성공 또는 실패를 합니다. 프로덕션 슬롯과 스테이징 슬롯을 교환 하려면 프로덕션 슬롯은 항상 대상 슬롯 해야 합니다. 이러한 방식으로 프로덕션 앱 교환 작업이 영향을 받지 않습니다.

### <a name="which-settings-are-swapped"></a>어떤 설정이 교환되나요?
다른 배포 슬롯으로부터 구성을 복제할 때 복제된 구성을 편집할 수 있습니다. 또한, 교환 후(특정 슬롯) 다른 구성 요소는 동일한 슬롯에 남아 있지만 일부 구성 요소는 교환(특정 슬롯 아님)에 따라 콘텐츠를 따릅니다. 다음 목록은 슬롯을 교환할 때 변경되는 설정을 보여줍니다.

**교환된 설정**:

* 프레임워크 버전, 32/64비트, 웹 소켓과 같은 일반 설정
* 앱 설정(슬롯에 맞도록 구성할 수 있음)
* 연결 설정(슬롯에 맞도록 구성할 수 있음)
* 처리기 매핑
* 모니터링 및 진단 설정
* 공용 인증서
* WebJob 콘텐츠
* 하이브리드 연결 *
* VNet 통합 *
* 서비스 끝점 *
* Azure CDN *

기능으로 표시 된 * 슬롯에 고정 될 예정입니다. 

**교환되지 않은 설정**:

* 게시 끝점
* 사용자 지정 도메인 이름
* 프라이빗 인증서 및 SSL 바인딩
* 크기 조정 설정
* WebJob 스케줄러
* IP 제한
* 항상 설정됨
* 프로토콜 설정 (HTTP**S**, TLS 버전, 클라이언트 인증서)
* 진단 로그 설정
* CORS

<!-- VNET and hybrid connections not yet sticky to slot -->

(교환 되지 않음) 특정 슬롯에 맞도록 앱 설정 또는 연결 문자열을 구성 하려면로 이동 합니다 **구성** 해당 슬롯에 대 한 페이지, 추가 또는 설정을 편집한 다음 선택 된 **배포 슬롯 설정**상자입니다. 이 확인란을 선택은 App Service는 설정이 스왑 가능 하지 않습니다. 

![슬롯 설정](./media/web-sites-staged-publishing/SlotSetting.png)

<a name="Swap"></a>

## <a name="swap-two-slots"></a>두 슬롯 교환 
앱의 배포 슬롯을 교환할 수 있습니다 **배포 슬롯** 페이지와 **개요** 페이지입니다. 슬롯 교환에서 기술 세부 정보를 참조 하세요. [스왑 중 진행 상황](#what-happens-during-swap)

> [!IMPORTANT]
> 프로덕션 환경에 배포 슬롯 으로부터 앱을 교환 하기 전에 프로덕션 대상 슬롯이 고 프로덕션에서 원하는 대로 정확 하 게 원본 슬롯의 모든 설정이 구성 되어 있는지 확인 합니다.
> 
> 

배포 슬롯을 교환하려면 다음 단계를 수행합니다.

1. 앱의 이동할 **배포 슬롯** 페이지를 클릭 **교환**합니다.
   
    ![교환 단추](./media/web-sites-staged-publishing/SwapButtonBar.png)

    **교환** 대화 상자에는 변경할 선택한 원본 및 대상 슬롯의 설정이 표시됩니다.

2. 원하는 **원본** 및 **대상** 슬롯을 선택합니다. 일반적으로 대상은 프로덕션 슬롯입니다. 또한 **원본 변경** 및 **대상 변경** 탭을 클릭하고 구성 변경이 예상대로 수행되었는지 확인합니다. 완료되면 **교환**을 클릭하여 슬롯을 즉시 교환할 수 있습니다.

    ![교환 완료](./media/web-sites-staged-publishing/SwapImmediately.png)

    교환이 실제로 발생하기 전에 대상 슬롯을 새 설정으로 실행하는 방법을 보려면 **교환**을 클릭하지 말고 [미리 보기가 있는 교환](#Multi-Phase)의 지침을 따릅니다.

3. 완료되면 **닫기**를 클릭하여 대화 상자를 닫습니다.

문제를 실행 하는 경우 참조 [스왑 해결](#troubleshoot-swaps)합니다.

<a name="Multi-Phase"></a>

### <a name="swap-with-preview-multi-phase-swap"></a>미리 보기가 있는 교환(다단계 교환)

> [!NOTE]
> 미리 보기가 있는 교환은 Linux의 웹앱에서 지원되지 않습니다.

프로덕션에 대상 슬롯으로 교환하기 전에 교환된 설정을 사용한 앱 실행이 유효한지 확인합니다. 원본 슬롯도 교환 완료 전에 준비됩니다. 이 작업은 업무용 애플리케이션에 유용합니다.

미리 보기를 사용 하 여 교체를 수행 하면 App Service 동일한 작업을 수행 [스왑 작업을](#what-happens-during-swap) 하지만 첫 번째 단계를 수행한 후 일시 중지 합니다. 교환을 완료 하기 전에 스테이징 슬롯에서 결과 확인할 수 있습니다. 

교환을 취소한 경우 App Service는 원본 슬롯의 구성 요소를 원본 슬롯에 다시 적용합니다.

미리 보기가 있는 교환을 수행하려면 다음 단계를 수행합니다.

1. [배포 슬롯 교환](#Swap)의 단계를 수행하되 **미리 보기가 있는 교환 수행**을 선택합니다.

    ![미리 보기가 있는 교환](./media/web-sites-staged-publishing/SwapWithPreview.png)

    이 대화 상자에는 1단계에서 원본 슬롯의 구성이 변경되는 방식과 2단계에서 원본 및 대상 슬롯이 변경되는 방식이 표시됩니다.

2. 교환을 시작할 준비가 되면 **교환 시작**을 클릭합니다.

    1단계가 완료되면 대화 상자에서 알림이 표시됩니다. `https://<app_name>-<source-slot-name>.azurewebsites.net`으로 이동하여 원본 슬롯의 교환을 미리 봅니다. 

3. 보류 중인 교환을 완료할 준비가 되면 **교환 작업**에서 **교환 완료**를 선택하고 **교환 완료**를 클릭합니다.

    보류 중인 교환을 취소하려면 **교환 취소**를 대신 선택하고 **교환 취소**를 클릭합니다.

4. 완료되면 **닫기**를 클릭하여 대화 상자를 닫습니다.

문제를 실행 하는 경우 참조 [스왑 해결](#troubleshoot-swaps)합니다.

다단계 교환을 자동화하려면 PowerShell을 사용하여 자동화를 참조하세요.

<a name="Rollback"></a>

## <a name="roll-back-swap"></a>교환 롤백
슬롯 교환 후 대상 슬롯(예: 프로덕션 슬롯)에서 오류가 발생하면 2개의 동일한 슬롯을 즉시 교환하여 슬롯을 교환 전 상태로 복원합니다.

<a name="Auto-Swap"></a>

## <a name="configure-auto-swap"></a>자동 교환 구성

> [!NOTE]
> 자동 교환은 Linux의 web apps에서 지원 되지 않습니다.

자동 교환은 앱의 최종 사용자에 대 한 콜드 및 가동 중지 시간이 0으로 지속적으로 앱을 배포 하려는 DevOps 시나리오를 간소화 합니다. 자동 교환은 사용 되는 경우 슬롯에서 프로덕션 환경으로 해당 슬롯에 App Service의 코드 변경 내용을 자동으로 푸시 될 때마다 [앱을 프로덕션으로 교환](#swap-operation-steps) 원본 슬롯에 준비 된 후입니다.

   > [!NOTE]
   > 프로덕션 슬롯에 대해 자동 교환을 구성 하기 전에 비프로덕션 대상 슬롯에 자동 교체를 먼저 테스트 하는 것이 좋습니다.
   > 

자동 교환은 구성 하려면 다음이 단계를 수행 합니다.

1. 앱의 리소스 페이지로 이동합니다. 선택 **배포 슬롯** >  *\<원하는 소스 슬롯 >*  > **Configuration**  >  **일반 설정**합니다.
   
2. **자동 전환을 사용 하도록 설정**를 선택 **에**를 원하는 대상 슬롯을 선택 **배포 슬롯 자동 전환**, 클릭 **저장** 에 명령 모음입니다. 
   
    ![](./media/web-sites-staged-publishing/AutoSwap02.png)

3. 소스 슬롯에 대해 코드 푸시를 실행합니다. 자동 교환은 짧은 시간 후에 발생 하 고 업데이트는 대상 슬롯의 URL에 반영 됩니다.

문제를 실행 하는 경우 참조 [스왑 해결](#troubleshoot-swaps)합니다.

<a name="Warm-up"></a>

## <a name="custom-warm-up"></a>사용자 지정 준비
[자동 교환](#Auto-Swap)을 사용할 경우 교환 전에 일부 앱에서 사용자 지정 준비 작업이 필요할 수 있습니다. web.config의 `applicationInitialization` 구성 요소를 사용하면 수행할 사용자 지정 초기화 작업을 지정할 수 있습니다. 합니다 [스왑 작업을](#what-happens-during-swap) 대상 슬롯으로 교환 하기 전에 완료 해야이 사용자 지정 준비 될 때까지 기다립니다. 샘플 web.config 조각은 다음과 같습니다.

    <system.webServer>
        <applicationInitialization>
            <add initializationPage="/" hostName="[app hostname]" />
            <add initializationPage="/Home/About" hostName="[app hostname]" />
        </applicationInitialization>
    </system.webServer>

사용자 지정 하는 방법은 합니다 `applicationInitialization` 요소를 참조 하세요 [가장 일반적인 배포 슬롯 교환 오류 및 해결 방법](https://ruslany.net/2017/11/most-common-deployment-slot-swap-failures-and-how-to-fix-them/)합니다.

또한 다음 [앱 설정](configure-common.md) 중 하나 이상을 사용하여 준비 동작을 사용자 지정할 수 있습니다.

- `WEBSITE_SWAP_WARMUP_PING_PATH`: 사이트 준비를 ping할 경로입니다. 슬래시로 시작하는 사용자 지정 경로를 값으로 지정하여 이 앱 설정을 추가합니다. 예: `/statuscheck`. 기본값은 `/`입니다. 
- `WEBSITE_SWAP_WARMUP_PING_STATUSES`: 준비 작업에 대한 유효한 HTTP 응답 코드입니다. HTTP 코드의 쉼표로 구분된 목록을 사용하여 이 앱 설정을 추가합니다. `200,202`를 예로 들 수 있습니다. 반환된 상태 코드가 목록에 없는 경우 준비 및 교환 작업이 중지됩니다. 기본적으로 모든 응답 코드는 유효합니다.

문제를 실행 하는 경우 참조 [스왑 해결](#troubleshoot-swaps)합니다.

## <a name="monitor-swap"></a>교환 모니터링

경우는 [스왑 작업을](#what-happens-during-swap) 를 완료 하려면 시간이 오래 걸리는 스왑 작업에 대 한 정보를 가져올 수 있습니다 합니다 [활동 로그](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)합니다.

포털의 앱 리소스 페이지에서 왼쪽 탐색의 **활동 로그**를 선택합니다.

교환 작업은 `Swap Web App Slots`으로 로그 쿼리에 표시됩니다. 이를 확장하고 하위 작업 또는 오류 중 하나를 선택하여 세부 정보를 볼 수 있습니다.

## <a name="route-traffic"></a>트래픽 라우팅

기본적으로 앱의 프로덕션 URL에 대한 모든 클라이언트 요청(`http://<app_name>.azurewebsites.net`)은 프로덕션 슬롯으로 라우팅됩니다. 트래픽의 일부를 다른 슬롯으로 라우팅할 수 있습니다. 이 기능은 새 업데이트에 대한 사용자 피드백이 필요하지만 프로덕션 환경으로 릴리스할 준비가 되지 않은 경우에 유용합니다.

### <a name="route-production-traffic-automatically"></a>자동으로 프로덕션 트래픽 라우팅

프로덕션 트래픽을 자동으로 라우팅하려면 다음 단계를 수행합니다.

1. 앱의 리소스 페이지를 탐색 및 선택 **배포 슬롯**합니다.

2. 라우팅하려는 슬롯의 **트래픽 %** 열에서 라우팅할 트래픽의 총량을 나타내는 백분율(0~100)을 지정합니다. **저장**을 클릭합니다.

    ![](./media/web-sites-staged-publishing/RouteTraffic.png)

설정이 저장되면 지정된 클라이언트 비율이 비프로덕션 슬롯에 임의로 라우팅됩니다. 

클라이언트가 특정 슬롯에 자동으로 라우팅되면 해당 슬롯에 클라이언트 세션의 수명 동안 "고정"됩니다. 클라이언트 브라우저에서 HTTP 헤더의 `x-ms-routing-name` 쿠키를 확인하여 세션이 고정된 슬롯을 볼 수 있습니다. "스테이징" 슬롯에 라우팅되는 요청에는 쿠키 `x-ms-routing-name=staging`이 있습니다. 프로덕션 슬롯으로 라우팅되는 요청에는 쿠키 `x-ms-routing-name=self`가 있습니다.

### <a name="route-production-traffic-manually"></a>수동으로 프로덕션 트래픽 라우팅

자동 트래픽 라우팅 외에도 App Service는 특정 슬롯에 요청을 라우팅할 수 있습니다. 사용자가 베타 앱에 참여 또는 탈퇴를 선택할 수 있도록 하려는 경우 유용합니다. 수동으로 프로덕션 트래픽을 라우팅하려면 `x-ms-routing-name` 쿼리 매개 변수를 사용합니다.

예를 들어 사용자가 베타 앱에 참여하지 않도록 하려면 웹 페이지에 이 링크를 둘 수 있습니다.

```HTML
<a href="<webappname>.azurewebsites.net/?x-ms-routing-name=self">Go back to production app</a>
```

문자열 `x-ms-routing-name=self` 는 프로덕션 슬롯을 지정합니다. 클라이언트 브라우저가 링크에 액세스하면 프로덕션 슬롯으로 리디렉션될 뿐만 아니라 모든 후속 요청에 프로덕션 슬롯으로 세션을 고정하는 `x-ms-routing-name=self` 쿠키가 있습니다.

예를 들어 사용자가 베타 앱에 참여하게 하려면 비-프로덕션 슬롯의 이름에 동일한 쿼리 매개 변수를 설정합니다.

```
<webappname>.azurewebsites.net/?x-ms-routing-name=staging
```

새 슬롯은 라우팅 규칙을 지정 하는 데 기본적으로 `0%`, 회색으로 표시 합니다. 이 값을 명시적으로 설정 하 여 `0%` (검은색 텍스트로 표시 됨), 사용자 수 스테이징 슬롯에 직접 액세스를 사용 하 여는 `x-ms-routing-name` 쿼리 매개 변수가 있지만 라우팅되지 것입니다 슬롯에 자동으로 라우팅 백분율은 0으로 설정 되어 있으므로. "을 숨길 수 있는"에 스테이징 슬롯 으로부터 내부 팀 슬롯에 대 한 변경 내용을 테스트할 수 있도록 하는 동안 고급 시나리오입니다.

<a name="Delete"></a>

## <a name="delete-slot"></a>슬롯 삭제

앱의 리소스 페이지로 이동합니다. 선택 **배포 슬롯** >  *\<슬롯을 삭제 >*  > **개요**합니다. 명령 모음에서 **삭제**를 클릭합니다.  

![배포 슬롯 삭제](./media/web-sites-staged-publishing/DeleteStagingSiteButton.png)

<!-- ======== AZURE POWERSHELL CMDLETS =========== -->

<a name="PowerShell"></a>

## <a name="automate-with-powershell"></a>PowerShell을 사용하여 자동화

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Azure PowerShell은 Windows PowerShell을 통해 Azure를 관리하기 위한 cmdlet을 제공하는 모듈로, Azure App Service에서 배포 슬롯을 관리하는 기능도 지원합니다.

Azure PowerShell을 설치 및 구성하는 방법과 Azure 구독에 Azure PowerShell을 인증하는 방법에 대한 자세한 내용은 [Microsoft Azure PowerShell 설치 및 구성 방법](/powershell/azure/overview)을 참조하세요.  

---
### <a name="create-web-app"></a>웹 앱 만들기
```powershell
New-AzWebApp -ResourceGroupName [resource group name] -Name [app name] -Location [location] -AppServicePlan [app service plan name]
```

---
### <a name="create-slot"></a>슬롯 만들기
```powershell
New-AzWebAppSlot -ResourceGroupName [resource group name] -Name [app name] -Slot [deployment slot name] -AppServicePlan [app service plan name]
```

---
### <a name="initiate-swap-with-preview-multi-phase-swap-and-apply-destination-slot-configuration-to-source-slot"></a>미리 보기가 있는 교환(다단계 교환) 시작 및 대상 슬롯 구성을 원본 슬롯에 적용
```powershell
$ParametersObject = @{targetSlot  = "[slot name – e.g. “production”]"}
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action applySlotConfig -Parameters $ParametersObject -ApiVersion 2015-07-01
```

---
### <a name="cancel-pending-swap-swap-with-review-and-restore-source-slot-configuration"></a>보류 중인 교환(검토가 있는 교환) 취소 및 원본 슬롯 구성 복원
```powershell
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action resetSlotConfig -ApiVersion 2015-07-01
```

---
### <a name="swap-deployment-slots"></a>배포 슬롯 교환
```powershell
$ParametersObject = @{targetSlot  = "[slot name – e.g. “production”]"}
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action slotsswap -Parameters $ParametersObject -ApiVersion 2015-07-01
```

### <a name="monitor-swap-events-in-the-activity-log"></a>활동 로그에서 교환 이벤트 모니터링
```powershell
Get-AzLog -ResourceGroup [resource group name] -StartTime 2018-03-07 -Caller SlotSwapJobProcessor  
```

---
### <a name="delete-slot"></a>슬롯 삭제
```powershell
Remove-AzResource -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots –Name [app name]/[slot name] -ApiVersion 2015-07-01
```

---
<!-- ======== Azure CLI =========== -->

<a name="CLI"></a>

## <a name="automate-with-cli"></a>CLI로 자동화

배포 슬롯에 대한 [Azure CLI](https://github.com/Azure/azure-cli) 명령은 [az webapp deployment slot](/cli/azure/webapp/deployment/slot)을 참조하세요.

## <a name="troubleshoot-swaps"></a>교환 문제 해결

하는 동안 오류가 발생 하는 경우는 [슬롯 전환](#what-happens-during-swap)에 기록 됩니다 *D:\home\LogFiles\eventlog.xml*, 응용 프로그램별 오류 로그와 합니다.

몇 가지 일반적인 스왑 오류는 다음과 같습니다.

- 응용 프로그램 루트에 HTTP 요청은 시간 초과 되었습니다. 스왑 작업을 최대 5 번 각 HTTP 요청을 다시 시도 대 한 90 초 동안 대기합니다. 모든 재시도가 시간 초과 된 경우 교환 작업이 중단 됩니다.

- 로컬 캐시 초기화 앱 콘텐츠의 로컬 캐시에 대 한 지정 된 로컬 디스크 할당량을 초과 하는 경우 실패할 수 있습니다. 자세한 내용은 [로컬 캐시 개요](overview-local-cache.md)합니다.

- 하는 동안 [사용자 지정 준비](#custom-warm-up)내부적으로 (거치지 않고 외부 URL), HTTP 요청이 수행 되 고 특정 URL 사용 하 여 실패를 다시 작성할 수에 규칙 *Web.config*합니다. 예를 들어, 도메인 이름 리디렉션 또는 HTTPS를 적용 하는 것에 대 한 규칙 앱 코드를 전혀 도달 하지 못하도록 준비 요청을 방지할 수 있습니다. 이 문제를 해결 하려면 다음 조건을 추가 하 여 다시 쓰기 규칙을 수정 합니다.

    ```xml
    <conditions>
      <add input="{WARMUP_REQUEST}" pattern="1" negate="true" />
      <add input="{REMOTE_ADDR}" pattern="^100?\." negate="true" />
      ...
    </conditions>
    ```
- 사용자 지정 준비 하지 않고 HTTP 요청을 계속 보유할 수 URL 다시 쓰기 규칙. 이 문제를 해결 하려면 다음 조건을 추가 하 여 다시 쓰기 규칙을 수정 합니다.

    ```xml
    <conditions>
      <add input="{REMOTE_ADDR}" pattern="^100?\." negate="true" />
      ...
    </conditions>
    ```
- 일부 [IP 제한 규칙](app-service-ip-restrictions.md) 에서 앱에 HTTP 요청을 보내는 교환 작업을 하지 못할 수 있습니다. IPv4 주소 범위를 시작 하는 `10.` 고 `100.` 내부에 배포 하 고 앱에 연결할 수 있어야 합니다.

## <a name="next-steps"></a>다음 단계
[비프로덕션 슬롯에 대한 액세스 차단](app-service-ip-restrictions.md)
