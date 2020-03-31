---
title: Azure 기능 프리미엄 플랜
description: Azure Functions Premium 요금제에 대한 세부 정보 및 구성 옵션(VNet, 콜드 스타트 없음, 무제한 실행 기간).
author: jeffhollan
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: jehollan
ms.openlocfilehash: dd7f6d0760f2b848435e7c77657e261517d29dd8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276908"
---
# <a name="azure-functions-premium-plan"></a>Azure 기능 프리미엄 플랜

Azure Functions 프리미엄 플랜(탄력적 프리미엄 플랜이라고도 함)은 기능 앱의 호스팅 옵션입니다. 프리미엄 요금제는 VNet 연결, 콜드 스타트 없음 및 프리미엄 하드웨어와 같은 기능을 제공합니다.  여러 기능 앱을 동일한 Premium 요금제에 배포할 수 있으며, 이 계획을 통해 계산 인스턴스 크기, 기본 계획 크기 및 최대 계획 크기를 구성할 수 있습니다.  프리미엄 플랜 및 기타 플랜 및 호스팅 유형을 비교하면 [기능 규모 및 호스팅 옵션을](functions-scale.md)참조하십시오.

## <a name="create-a-premium-plan"></a>프리미엄 플랜 만들기

[!INCLUDE [functions-premium-create](../../includes/functions-premium-create.md)]

Azure CLI에서 az [함수 앱 계획을 만들어](/cli/azure/functionapp/plan#az-functionapp-plan-create) 프리미엄 계획을 만들 수도 있습니다. 다음 예제에서 _탄력적 프리미엄 1_ 계층 계획을 만듭니다.

```azurecli-interactive
az functionapp plan create --resource-group <RESOURCE_GROUP> --name <PLAN_NAME> \
--location <REGION> --sku EP1
```

이 예제에서는 `<RESOURCE_GROUP>` 리소스 그룹으로 바꾸고 `<PLAN_NAME>` 리소스 그룹에서 고유한 계획의 이름으로 바꿉니다. [지원되는 `<REGION>`을 지정합니다. ](https://azure.microsoft.com/global-infrastructure/services/?products=functions) Linux를 지원하는 프리미엄 요금제를 `--is-linux` 만들려면 옵션을 포함합니다.

계획을 만든, 함수 응용 프로그램을 만들 az [함수 앱 만들기를](/cli/azure/functionapp#az-functionapp-create) 사용할 수 있습니다. 포털에서 계획과 앱이 동시에 만들어집니다. 전체 Azure CLI 스크립트의 예는 [프리미엄 계획의 함수 앱 만들기를](scripts/functions-cli-create-premium-plan.md)참조하십시오.

## <a name="features"></a>기능

다음 기능은 프리미엄 요금제에 배포된 기능 앱에 사용할 수 있습니다.

### <a name="pre-warmed-instances"></a>예열된 인스턴스

현재 소비 계획에서 이벤트와 실행이 발생하지 않으면 앱이 인스턴스 0으로 확장될 수 있습니다. 새 이벤트가 발생하면 새 인스턴스를 앱에서 실행 중인 앱에 특수화해야 합니다.  새 인스턴스를 전문으로 하는 것은 앱에 따라 다소 시간이 걸릴 수 있습니다.  첫 번째 호출에서 이 추가 대기 시간을 앱 콜드 스타트라고 도합니다.

프리미엄 요금제에서는 최소 요금제 크기까지 지정된 수의 인스턴스에서 앱을 미리 웜화할 수 있습니다.  또한 미리 준비된 인스턴스를 사용하면 부하가 많이 발생하기 전에 앱을 미리 확장할 수 있습니다. 앱이 확장되면 먼저 미리 준비된 인스턴스로 확장됩니다. 추가 인스턴스는 다음 스케일 작업을 준비하기 위해 버퍼링이 계속되고 즉시 따뜻해지다. 미리 데운 인스턴스버퍼를 사용하면 콜드 스타트 대기 시간을 효과적으로 방지할 수 있습니다.  미리 준비된 인스턴스는 프리미엄 요금제의 기능이며, 계획이 활성화된 경우 항상 하나 이상의 인스턴스를 실행 중이고 사용할 수 있어야 합니다.

**기능 앱을**선택하고 **플랫폼 기능** 탭으로 이동한 다음 확장 **옵션을** 선택하여 Azure Portal에서 미리 준비된 인스턴스 수를 구성할 수 있습니다. 함수 앱 편집 창에서 미리 준비된 인스턴스는 해당 앱에 따라 다르지만 최소 및 최대 인스턴스는 전체 계획에 적용됩니다.

![탄력적 축척 설정](./media/functions-premium-plan/scale-out.png)

Azure CLI를 사용하면 앱에 대해 미리 준비된 인스턴스를 구성할 수도 있습니다.

```azurecli-interactive
az resource update -g <resource_group> -n <function_app_name>/config/web --set properties.preWarmedInstanceCount=<desired_prewarmed_count> --resource-type Microsoft.Web/sites
```

### <a name="private-network-connectivity"></a>개인 네트워크 연결

프리미엄 계획에 배포된 Azure 함수는 [웹 앱에 대한 새로운 VNet 통합을 활용합니다.](../app-service/web-sites-integrate-with-vnet.md)  구성되면 앱은 VNet 내의 리소스와 통신하거나 서비스 끝점을 통해 보호할 수 있습니다.  들어오는 트래픽을 제한하기 위해 앱에서도 IP 제한을 사용할 수 있습니다.

프리미엄 요금제에서 함수 앱에 서브넷을 할당할 때 각 잠재적 인스턴스에 대해 IP 주소가 충분한 서브넷이 필요합니다. 사용 가능한 주소가 100개 이상인 IP 블록이 필요합니다.

자세한 내용은 [기능 앱을 VNet 과 통합을](functions-create-vnet.md)참조하십시오.

### <a name="rapid-elastic-scale"></a>빠른 탄성 스케일

추가 계산 인스턴스는 소비 계획과 동일한 빠른 크기 조정 논리를 사용하여 앱에 자동으로 추가됩니다.  크기 조정작동 방식에 대한 자세한 내용은 [Function 확장 및 호스팅](./functions-scale.md#how-the-consumption-and-premium-plans-work)을 참조하십시오.

### <a name="longer-run-duration"></a>더 긴 실행 기간

소비 계획의 Azure 함수는 단일 실행에 대해 10분으로 제한됩니다.  프리미엄 플랜에서 실행 기간은 런어웨이 실행을 방지하기 위해 30분으로 기본설정됩니다. 그러나 [host.json 구성을 수정하여](./functions-host-json.md#functiontimeout) 프리미엄 요금제 앱에 대해 무한하게 만들 수 있습니다(60분 보장).

## <a name="plan-and-sku-settings"></a>계획 및 SKU 설정

계획을 만들 때 최소 인스턴스 수(또는 계획 크기)와 최대 버스트 제한이라는 두 가지 설정을 구성합니다.  최소 인스턴스는 예약되어 있으며 항상 실행됩니다.

> [!IMPORTANT]
> 함수가 실행중인지 여부에 관계없이 최소 인스턴스 수에 할당된 각 인스턴스에 대해 요금이 부과됩니다.

앱에 계획 크기를 초과하는 인스턴스가 필요한 경우 인스턴스 수가 최대 버스트 제한에 도달하면 계속 확장할 수 있습니다.  요금제 규모가 초과된 인스턴스에 대해 요금이 청구되며, 실행 중이며 대여하는 동안에만 요금이 청구됩니다.  앱에 대해 최소 계획 인스턴스가 보장되는 반면, 앱의 최대 한도까지 앱을 확장하는 데 최선을 다할 것입니다.

계획에서 **확장 옵션을** 선택하거나 해당 계획에 배포된 기능 앱(플랫폼 기능 아래)을 선택하여 Azure 포털에서 계획 크기와 최대값을 구성할 수 **있습니다.**

Azure CLI에서 최대 버스트 제한을 늘릴 수도 있습니다.

```azurecli-interactive
az resource update -g <resource_group> -n <premium_plan_name> --set properties.maximumElasticWorkerCount=<desired_max_burst> --resource-type Microsoft.Web/serverfarms 
```

### <a name="available-instance-skus"></a>사용 가능한 인스턴스 SUS

계획을 만들거나 확장할 때 세 가지 인스턴스 크기 중에서 선택할 수 있습니다.  초당 소비된 코어 및 메모리의 총 수에 대한 요금이 청구됩니다.  앱은 필요에 따라 자동으로 여러 인스턴스로 확장할 수 있습니다.  

|SKU|코어 수|메모리|스토리지|
|--|--|--|--|
|EP1|1|3.5GB|250gb|
|EP2|2|7GB|250gb|
|EP3|4|14GB|250gb|

### <a name="memory-utilization-considerations"></a>메모리 사용 고려 사항
메모리가 많은 컴퓨터에서 실행한다고 해서 함수 앱에서 사용 가능한 모든 메모리를 사용하는 것은 아닙니다.

예를 들어 JavaScript 함수 앱은 Node.js의 기본 메모리 제한에 의해 제한됩니다. 이 고정 메모리 제한을 늘리려면 `languageWorkers:node:arguments` `--max-old-space-size=<max memory in MB>`의 값으로 앱 설정을 추가합니다.

## <a name="region-max-scale-out"></a>리전 최대 배율

다음은 각 리전 및 OS 구성의 단일 계획에 대해 현재 지원되는 최대 확장 값입니다. 증가를 요청하려면 지원 티켓을 엽니다.

여기에서 기능의 전체 지역 가용성을 참조하십시오: [Azure.com](https://azure.microsoft.com/global-infrastructure/services/?products=functions)

|지역| Windows | Linux |
|--| -- | -- |
|오스트레일리아 중부| 20 | 사용할 수 없음 |
|오스트레일리아 중부 2| 20 | 사용할 수 없음 |
|오스트레일리아 동부| 100 | 20 |
|오스트레일리아 남동부 | 100 | 20 |
|브라질 남부| 60 | 20 |
|캐나다 중부| 100 | 20 |
|미국 중부| 100 | 20 |
|동아시아| 100 | 20 |
|미국 동부 | 100 | 20 |
|미국 동부 2| 100 | 20 |
|프랑스 중부| 100 | 20 |
|독일 웨스트 센트럴| 100 | 사용할 수 없음 |
|일본 동부| 100 | 20 |
|일본 서부| 100 | 20 |
|한국 중부| 100 | 20 |
|미국 중북부| 100 | 20 |
|북유럽| 100 | 20 |
|노르웨이 동부| 20 | 20 |
|미국 중남부| 100 | 20 |
|인도 남부 | 100 | 사용할 수 없음 |
|동남아시아| 100 | 20 |
|영국 남부| 100 | 20 |
|영국 서부| 100 | 20 |
|서유럽| 100 | 20 |
|인도 서부| 100 | 20 |
|미국 중서부| 20 | 20 |
|미국 서부| 100 | 20 |
|미국 서부 2| 100 | 20 |

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure 함수 확장 및 호스팅 옵션 이해](functions-scale.md)
