---
title: PremiumV3 계층 구성
description: 새 PremiumV3 가격 책정 계층으로 확장 하 여 Azure App Service에서 웹, 모바일 및 API 앱의 성능을 향상 시키는 방법에 대해 알아봅니다.
keywords: 앱 서비스, Azure 앱 서비스, 규모, 확장성, 앱 서비스 계획, 앱 서비스 비용
ms.assetid: ff00902b-9858-4bee-ab95-d3406018c688
ms.topic: article
ms.date: 10/01/2020
ms.custom: seodec18
ms.openlocfilehash: 0030a9340d874d94b9876e23f372e97655c145da
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91742707"
---
# <a name="configure-premiumv3-tier-for-azure-app-service"></a>Azure App Service에 대 한 PremiumV3 계층 구성

새 **PremiumV3** 가격 책정 계층은 더 빠른 프로세서, SSD 저장소 및 기존 가격 책정 계층의 메모리 대 코어 비율을 4 배로 제공 합니다 ( **PremiumV2** 계층 두 개). 성능상의 이점 덕분에 더 적은 인스턴스에서 앱을 실행하여 비용을 절감할 수 있습니다. 이 문서에서는 **PremiumV3** 계층에서 앱을 만들거나 **PremiumV3** 계층으로 앱을 확장 하는 방법에 대해 알아봅니다.

## <a name="prerequisites"></a>필수 구성 요소

앱을 **PremiumV3**로 강화 하려면 **PremiumV3**보다 낮은 가격 책정 계층에서 실행 되는 Azure App Service 앱이 있어야 하 고, PremiumV3를 지 원하는 App Service 배포에서 앱이 실행 되 고 있어야 합니다.

<a name="availability"></a>

## <a name="premiumv3-availability"></a>PremiumV3 가용성

**PremiumV3** 계층은 Windows 컨테이너 및 Linux 컨테이너를 포함 하 여 기본 및 컨테이너 앱 모두에 사용할 수 있습니다.

> [!NOTE]
> Preview 기간 동안 **프리미엄 컨테이너** 계층에서 실행 되는 모든 Windows 컨테이너는 계속 해 서 그대로 작동 하지만 **프리미엄 컨테이너** 계층은 계속 미리 보기로 유지 됩니다. **PremiumV3** 계층은 **프리미엄 컨테이너** 계층의 공식 대체 항목입니다. 

**PremiumV3** 는 일부 Azure 지역에서 사용할 수 있으며 추가 지역의 가용성은 계속 추가 됩니다. 해당 지역에서 사용할 수 있는지 확인하려면 [Azure Cloud Shell](../cloud-shell/overview.md)에서 다음 Azure CLI 명령을 실행합니다.

```azurecli-interactive
az appservice list-locations --sku P1V3
```

<a name="create"></a>

## <a name="create-an-app-in-premiumv3-tier"></a>PremiumV3 계층에서 앱 만들기

App Service 앱의 가격 책정 계층은 해당 앱이 실행되는 [App Service 계획](overview-hosting-plans.md)에 정의되어 있습니다. App Service 계획은 단독으로 또는 앱 만들기의 일부로 만들 수 있습니다.

<a href="https://portal.azure.com" target="_blank">Azure Portal</a>에서 App Service 계획을 구성하는 경우 **가격 책정 계층**을 선택합니다. 

**프로덕션**을 선택 하 고 **P1V3**, **P2V3**또는 **P3V3**를 선택한 다음 **적용**을 클릭 합니다.

![앱에 대해 권장 되는 가격 책정 계층을 보여 주는 스크린샷](media/app-service-configure-premium-tier/scale-up-tier-select.png)

> [!IMPORTANT] 
> **P1V3**, **P2V3**및 **P3V3** 가 옵션으로 표시 되지 않거나 옵션이 회색으로 표시 된 경우에는 App Service 계획을 포함 하는 기본 App Service 배포에서 **PremiumV3** 를 사용할 수 없습니다. 자세한 내용은 [지원되지 않는 리소스 그룹 및 지역 조합에서 강화](#unsupported)를 참조합니다.

## <a name="scale-up-an-existing-app-to-premiumv3-tier"></a>기존 앱을 PremiumV3 계층으로 확장

기존 앱을 **PremiumV3** 계층으로 확장 하기 전에 **PremiumV3** 를 사용할 수 있는지 확인 합니다. 자세한 내용은 [PremiumV3 availability](#availability)을 참조 하세요. 사용할 수 없는 경우 [지원되지 않는 리소스 그룹 및 지역 조합에서 강화](#unsupported)를 참조합니다.

호스팅 환경에 따라 강화하는 데 추가 단계가 필요할 수 있습니다. 

<a href="https://portal.azure.com" target="_blank">Azure Portal</a>에서 App Service 앱 페이지를 엽니다.

App Service 앱 페이지의 왼쪽 탐색 영역에서 **강화(App Service 계획)** 를 선택합니다.

![App service 계획을 확장 하는 방법을 보여 주는 스크린샷](media/app-service-configure-premium-tier/scale-up-tier-portal.png)

**프로덕션**을 선택 하 고 **P1V3**, **P2V3**또는 **P3V3**를 선택한 다음 **적용**을 클릭 합니다.

![앱에 대해 권장 되는 가격 책정 계층을 보여 주는 스크린샷](media/app-service-configure-premium-tier/scale-up-tier-select.png)

작업이 성공적으로 완료 되 면 앱의 개요 페이지에 이제 **PremiumV3** 계층에 표시 되는 것을 볼 수 있습니다.

![앱의 개요 페이지에서 PremiumV3 가격 책정 계층을 보여 주는 스크린샷](media/app-service-configure-premium-tier/finished.png)

### <a name="if-you-get-an-error"></a>오류가 발생하는 경우

기본 App Service 배포에서 PremiumV3을 지원 하지 않는 경우 일부 App Service 계획을 PremiumV3 계층으로 확장할 수 없습니다. 자세한 내용은 [지원되지 않는 리소스 그룹 및 지역 조합에서 강화](#unsupported)를 참조합니다.

<a name="unsupported"></a>

## <a name="scale-up-from-an-unsupported-resource-group-and-region-combination"></a>지원되지 않는 리소스 그룹 및 지역 조합에서 강화

**PremiumV3** 를 사용할 수 없는 App Service 배포에서 앱이 실행 되는 경우 또는 현재 **PremiumV3**을 지원 하지 않는 지역에서 앱이 실행 되는 경우 **PremiumV3**를 활용 하기 위해 앱을 다시 배포 해야 합니다.  다음과 같은 두 가지 옵션이 있습니다.

- 새 리소스 그룹에 새 App Service 계획으로 앱을 만듭니다. App Service 계획을 만들 때 **PremiumV3** 계층을 선택 합니다. 이 단계를 수행 하면 App Service 계획이 **PremiumV3**을 지 원하는 배포 단위로 배포 됩니다. 그런 다음 새로 만든 앱에 응용 프로그램 코드를 다시 배포 합니다. App Service 계획을 하위 계층으로 확장 하 여 비용을 절감 하는 경우에도 배포 단위에서 지원 하므로 항상 **PremiumV3** 로 크기를 조정할 수 있습니다.
- 앱이 이미 기존 **프리미엄** 계층에서 실행 되는 경우 모든 앱 설정, 연결 문자열 및 배포 구성을 사용 하 여 앱을 **PremiumV3**를 사용 하는 새 app service 계획으로 복제할 수 있습니다.

    ![앱을 복제 하는 방법을 보여 주는 스크린샷](media/app-service-configure-premium-tier/clone-app.png)

    **앱 복제** 페이지에서 원하는 지역에 **PremiumV3** 를 사용 하 여 App Service 계획을 만들고 복제 하려는 앱 설정 및 구성을 지정할 수 있습니다.

## <a name="moving-from-premium-container-to-premium-v3-sku"></a>프리미엄 컨테이너에서 프리미엄 V3 SKU로 이동

Preview Premium 컨테이너 SKU를 사용 하는 앱이 있고 새 프리미엄 V3 SKU로 이동 하려는 경우 **PremiumV3**를 활용 하려면 앱을 다시 배포 해야 합니다. 이렇게 하려면 [지원 되지 않는 리소스 그룹 및 지역 조합에서 확장](#scale-up-from-an-unsupported-resource-group-and-region-combination) 의 첫 번째 옵션을 참조 하세요.

## <a name="automate-with-scripts"></a>스크립트를 사용하여 자동화

[Azure CLI](/cli/azure/install-azure-cli) 또는 [Azure PowerShell](/powershell/azure/)를 사용 하 여 스크립트를 통해 **PremiumV3** 계층에서 앱 생성을 자동화할 수 있습니다.

### <a name="azure-cli"></a>Azure CLI

다음 명령은 _P1V2_에서 App Service 계획을 만듭니다. Cloud Shell에서 실행할 수 있습니다. 의 옵션은 `--sku` P1V3, _P2V3_및 _P3V3_입니다.

```azurecli-interactive
az appservice plan create \
    --resource-group <resource_group_name> \
    --name <app_service_plan_name> \
    --sku P1V3
```

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

다음 명령은 _P1V3_에서 App Service 계획을 만듭니다. `-WorkerSize`에 대한 옵션은 _Small_, _Medium_ 및 _Large_입니다.

```powershell
New-AzAppServicePlan -ResourceGroupName <resource_group_name> `
    -Name <app_service_plan_name> `
    -Location <region_name> `
    -Tier "PremiumV3" `
    -WorkerSize "Small"
```

## <a name="more-resources"></a>추가 리소스

Azure에서 앱 강화 [Scale up an app in Azure](manage-scale-up.md) 
 [수동 또는 자동으로 인스턴스 개수 조정](../azure-monitor/platform/autoscale-get-started.md)