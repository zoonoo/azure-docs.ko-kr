---
title: 자습서 - Azure CLI를 사용하여 기존 가상 네트워크에 배포 - Azure Dedicated HSM | Microsoft Docs
description: CLI를 사용하여 전용 HSM을 기존 가상 네트워크에 배포하는 방법을 보여 주는 자습서입니다.
services: dedicated-hsm
documentationcenter: na
author: msmbaldwin
manager: rkarlin
editor: ''
ms.service: key-vault
ms.topic: tutorial
ms.custom: mvc, seodec18, devx-track-azurecli
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/20/2020
ms.author: mbaldwin
ms.openlocfilehash: d175ac75ce76836d012cdd04d4dbd7d81ffda584
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/23/2020
ms.locfileid: "92460702"
---
# <a name="tutorial-deploying-hsms-into-an-existing-virtual-network-using-the-azure-cli"></a>자습서: Azure CLI를 사용하여 기존 가상 네트워크에 HSM 배포

Azure Dedicated HSM은 단일 고객이 사용할 수 있는 완전한 관리 컨트롤과 전체적인 관리 기능을 지원하는 물리적 디바이스를 제공합니다. 물리적 디바이스를 사용하려면 용량을 효과적으로 관리할 수 있도록 Microsoft에서 디바이스 할당을 제어해야 합니다. 따라서 Azure 구독 내에서 Dedicated HSM 서비스는 일반적으로 리소스 프로비전에 표시되지 않습니다. Dedicated HSM 서비스에 액세스해야 하는 Azure 고객은 먼저 Microsoft 계정 담당자에게 연락하여 Dedicated HSM 서비스에 대한 등록을 요청해야 합니다. 이 프로세스가 성공적으로 완료되어야만 프로비전이 가능해집니다. 

이 자습서에서는 다음과 같은 일반적인 프로비전 프로세스를 보여줍니다.

- 고객에게 이미 가상 네트워크가 있음
- 가상 머신이 있음
- 기존 환경에 HSM 리소스를 추가해야 합니다.

일반적인 고가용성의 다중 지역 배포 아키텍처는 다음과 유사합니다.

![다중 지역 배포](media/tutorial-deploy-hsm-cli/high-availability-architecture.png)

이 자습서에서는 기존의 가상 네트워크(위의 VNET 1 참조)로 통합되는 HSM과 필요한 ExpressRoute 게이트웨이(위의 Subnet 1 참조)의 쌍을 중점적으로 살펴봅니다.  다른 모든 리소스는 표준 Azure 리소스입니다. 위의 VNET 3에서 서브넷 4에 있는 HSM에 동일한 통합 프로세스를 사용할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

Azure Dedicated HSM을 현재 Azure Portal에서 사용할 수 없습니다. 서비스와의 모든 상호 작용은 명령줄이나 PowerShell을 통해 이루어집니다. 이 자습서에서는 Azure Cloud Shell의 명령줄(CLI) 인터페이스를 사용합니다. Azure CLI를 처음 사용하는 경우 [Azure CLI 2.0 시작](/cli/azure/get-started-with-azure-cli?view=azure-cli-latest&preserve-view=true)에 있는 시작 지침을 따릅니다.

가정:

- Azure Dedicated HSM 등록 프로세스를 완료했습니다.
- 서비스 사용 승인을 받았습니다. 이에 해당되지 않을 경우 Microsoft 계정 담당자에게 자세한 내용을 문의하세요.
- 이러한 리소스에 대한 리소스 그룹을 만들었으며, 이 자습서에서 배포된 새 리소스가 해당 그룹에 가입됩니다.
- 위의 다이어그램에 따라 필요한 가상 네트워크, 서브넷, 가상 머신을 이미 만들었으며, 이제 해당 배포판에 2개의 HSM을 통합하려고 합니다.

아래의 모든 지침에서는 이미 Azure Portal로 이동했고 Cloud Shell(포털 오른쪽 위에서 "\>\_" 선택)을 열었다고 가정합니다.

## <a name="provisioning-a-dedicated-hsm"></a>Dedicated HSM 프로비전

HSM을 프로비전하고 ExpressRoute 게이트웨이를 통해 기존 가상 네트워크에 통합하면 ssh를 사용한 유효성 검사가 수행됩니다. 이 유효성 검사는 추가 구성 작업을 위해 HSM 디바이스의 연결 가능성과 기본 가용성을 보장하는 데 도움이 됩니다.

### <a name="validating-feature-registration"></a>기능 등록 유효성 검사

위에서 설명한 대로 프로비전 작업을 수행하려면 Dedicated HSM 서비스가 구독에 등록되어 있어야 합니다. 이에 대한 유효성 검사를 수행하려면 Azure Portal Cloud Shell에서 다음 명령을 실행합니다.

```azurecli
az feature show \
   --namespace Microsoft.HardwareSecurityModules \
   --name AzureDedicatedHSM
```

이 명령은 아래에 표시된 것처럼 "등록됨" 상태를 반환해야 합니다. 명령이 "등록됨" 상태를 반환하지 않을 경우 Microsoft 계정 담당자에게 문의하여 이 서비스에 등록해야 합니다.

![구독 상태](media/tutorial-deploy-hsm-cli/subscription-status.png)

### <a name="creating-hsm-resources"></a>HSM 리소스 만들기

HSM 리소스를 만들기 전에 필요한 몇 가지 사전 필수 구성 요소 리소스가 있습니다. 컴퓨팅, HSM 및 게이트웨이에 대한 서브넷 범위를 지원하는 가상 네트워크가 있어야 합니다. 다음 명령은 이러한 가상 네트워크를 만드는 요소에 대한 예를 보여줍니다.

```azurecli
az network vnet create \
  --name myHSM-vnet \
  --resource-group myRG \
  --address-prefix 10.2.0.0/16 \
  --subnet-name compute \
  --subnet-prefix 10.2.0.0/24
```

```azurecli
az network vnet subnet create \
  --vnet-name myHSM-vnet \
  --resource-group myRG \
  --name hsmsubnet \
  --address-prefixes 10.2.1.0/24 \
  --delegations Microsoft.HardwareSecurityModules/dedicatedHSMs
```

```azurecli
az network vnet subnet create \
  --vnet-name myHSM-vnet \
  --resource-group myRG \
  --name GatewaySubnet \
  --address-prefixes 10.2.255.0/26
```

>[!NOTE]
>가상 네트워크에서 주목해야 할 가장 중요한 구성은 HSM 디바이스의 서브넷에 "Microsoft.HardwareSecurityModules/dedicatedHSMs"로 설정된 위임이 있어야 한다는 것입니다.  이 옵션이 설정되지 않으면 HSM 프로비전이 작동하지 않습니다.

네트워크를 구성한 후에는 이러한 Azure CLI 명령을 사용하여 HSM을 프로비저닝합니다.

1. [az dedicated-hsm create](/cli/azure/ext/hardware-security-modules/dedicated-hsm#ext_hardware_security_modules_az_dedicated_hsm_create) 명령을 사용하여 첫 번째 HSM을 프로비저닝합니다. HSM의 이름은 hsm1입니다. 구독을 대체합니다.

   ```azurecli
   az dedicated-hsm create --location westus --name hsm1 --resource-group myRG --network-profile-network-interfaces \
        /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Network/virtualNetworks/MyHSM-vnet/subnets/MyHSM-vnet
   ```

   이 배포를 완료하는 데는 약 25~30분이 소요됩니다. HSM 디바이스에서 대부분의 시간이 소요됩니다.

1. 현재 HSM을 보려면 [az dedicated-hsm show](/cli/azure/ext/hardware-security-modules/dedicated-hsm#ext_hardware_security_modules_az_dedicated_hsm_show) 명령을 실행합니다.

   ```azurecli
   az dedicated-hsm show --resource group myRG --name hsm1
   ```

1. 다음 명령을 사용하여 두 번째 HSM을 프로비저닝합니다.

   ```azurecli
   az dedicated-hsm create --location westus --name hsm2 --resource-group myRG --network-profile-network-interfaces \
        /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Network/virtualNetworks/MyHSM-vnet/subnets/MyHSM-vnet
   ```

1. [az dedicated-hsm list](/cli/azure/ext/hardware-security-modules/dedicated-hsm#ext_hardware_security_modules_az_dedicated_hsm_list) 명령을 실행하여 현재 HSM에 대한 세부 정보를 확인합니다.

   ```azurecli
   az dedicated-hsm list --resource-group myRG
   ```

유용하게 사용할 수 있는 몇 가지 다른 명령이 있습니다. [az dedicated-hsm update](/cli/azure/ext/hardware-security-modules/dedicated-hsm#ext_hardware_security_modules_az_dedicated_hsm_update) 명령을 사용하여 HSM을 업데이트합니다.

```azurecli
az dedicated-hsm update --resource-group myRG –name hsm1
```

HSM을 삭제하려면 [az dedicated-hsm delete](/cli/azure/ext/hardware-security-modules/dedicated-hsm#ext_hardware_security_modules_az_dedicated_hsm_delete) 명령을 사용합니다.

```azurecli
az dedicated-hsm delete --resource-group myRG –name hsm1
```

## <a name="verifying-the-deployment"></a>배포 확인

디바이스가 프로비전되었는지 확인하고 디바이스 특성을 보려면 다음 명령 집합을 실행합니다. 리소스 그룹이 적절하게 설정되었고 리소스 이름이 매개 변수 파일과 똑같은지 확인합니다.

```azurecli
subid=$(az account show --query id --output tsv)
az resource show \
   --ids /subscriptions/$subid/resourceGroups/myRG/providers/Microsoft.HardwareSecurityModules/dedicatedHSMs/HSM1
az resource show \
   --ids /subscriptions/$subid/resourceGroups/myRG/providers/Microsoft.HardwareSecurityModules/dedicatedHSMs/HSM2
```

출력은 다음 출력과 유사합니다.

```json
{
    "id": n/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/HSM-RG/providers/Microsoft.HardwareSecurityModules/dedicatedHSMs/HSMl",
    "identity": null,
    "kind": null,
    "location": "westus",
    "managedBy": null,
    "name": "HSM1",
    "plan": null,
    "properties": {
        "networkProfile": {
            "networkInterfaces": [
            {
            "id": n/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/HSM-RG/providers/Microsoft.Network/networkInterfaces/HSMl_HSMnic", "privatelpAddress": "10.0.2.5",
            "resourceGroup": "HSM-RG"
            }
            L
            "subnet": {
                "id": n/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/HSM-RG/providers/Microsoft.Network/virtualNetworks/demo-vnet/subnets/hsmsubnet", "resourceGroup": "HSM-RG"
            }
        },
        "provisioningState": "Succeeded",
        "stampld": "stampl",
        "statusMessage": "The Dedicated HSM device is provisioned successfully and ready to use."
    },
    "resourceGroup": "HSM-RG",
    "sku": {
        "capacity": null,
        "family": null,
        "model": null,
        "name": "SafeNet Luna Network HSM A790",
        "size": null,
        "tier": null
    },
    "tags": {
        "Environment": "prod",
        "resourceType": "Hsm"
    },
    "type": "Microsoft.HardwareSecurityModules/dedicatedHSMs"
}
```

또한 이제 [Azure 리소스 탐색기](https://resources.azure.com/)를 사용하여 리소스를 볼 수 있습니다.   탐색기에서 왼쪽에 있는 "구독", Dedicated HSM의 특정 구독, "리소스 그룹", 사용한 리소스 그룹을 차례로 확장하고, 마지막으로 "리소스" 항목을 선택합니다.

## <a name="testing-the-deployment"></a>배포 테스트

배포를 테스트할 경우 HSM에 액세스할 수 있는 가상 머신에 연결한 후 HSM 디바이스에 직접 연결합니다. 이러한 작업은 HSM에 연결할 수 있는지 확인합니다.
ssh 도구를 사용하여 가상 머신에 연결합니다. 이 명령은 다음과 유사하지만 매개 변수에 지정된 관리자 이름과 dns 이름을 사용합니다.

`ssh adminuser@hsmlinuxvm.westus.cloudapp.azure.com`

또한 위의 명령에서 DNS 이름 대신 VM의 IP 주소를 사용할 수도 있습니다. 이 명령이 성공하면 경우 암호를 묻는 메시지가 표시되고, 암호를 입력해야 합니다. 가상 머신에 로그온되면 포털에서 HSM과 연결된 네트워크 인터페이스 리소스에 대해 확인된 개인 IP 주소를 사용하여 HSM에 로그인할 수 있습니다.

![구성 요소 목록](media/tutorial-deploy-hsm-cli/resources.png)

>[!NOTE]
>선택할 경우 HSM 리소스가 표시되는 "숨겨진 형식 표시" 확인란에 주목하세요.

위의 스크린샷에서 "HSM1_HSMnic" 또는 "HSM2_HSMnic"를 클릭하면 적절한 개인 IP 주소가 표시됩니다. 그렇지 않을 경우 위에서 사용된 `az resource show` 명령으로 올바른 IP 주소를 확인할 수 있습니다. 

올바른 IP 주소를 확인한 후에는 해당 주소를 대체하는 다음 명령을 실행합니다.

`ssh tenantadmin@10.0.2.4`

성공하면 암호를 묻는 메시지가 나타납니다. 기본 암호는 PASSWORD이며, HSM은 먼저 강력한 암호를 설정할 수 있도록 암호를 변경하고, 조직에서 선호하는 메커니즘으로 암호를 저장하여 분실을 방지하라는 메시지를 표시합니다.

>[!IMPORTANT]
>이 암호를 분실할 경우 HSM을 다시 설정해야 하고, 그러면 키가 분실됩니다.

ssh를 사용하여 HSM에 연결하는 경우 HSM이 작동하도록 다음 명령을 실행합니다.

`hsm show`

출력은 아래 이미지에 표시된 것과 같이 표시됩니다.

![스크린샷은 PowerShell 창의 출력을 보여줍니다.](media/tutorial-deploy-hsm-cli/hsm-show-output.png)

이제 고가용성의 두 HSM 배포에 모든 리소스가 할당되고, 액세스 및 작동 상태에 대한 유효성 검사가 완료된 상태여야 합니다. 추가 구성 또는 테스트를 위해서는 HSM 디바이스 자체로 추가 작업을 수행해야 합니다. 이를 위해서는 Gemalto Luna Network HSM 7 Administration Guide 7장의 지침을 따라 HSM을 초기화하고 파티션을 만들어야 합니다. Gemalto 고객 지원 포털에 등록되어 있고 고객 ID가 있으면 모든 설명서 및 소프트웨어를 Gemalto에서 직접 다운로드할 수 있습니다. 모든 필수 구성 요소를 받으려면 클라이언트 소프트웨어 7.2 버전을 다운로드하세요.

## <a name="delete-or-clean-up-resources"></a>리소스 삭제 또는 정리

HSM 디바이스로만 완료한 경우 리소스로 삭제하고 사용 가능 풀로 반환할 수 있습니다. 이 작업을 수행할 때 확실한 문제는 디바이스에 있는 모든 중요한 고객 데이터입니다. 디바이스를 "제로화"하는 가장 좋은 방법은 HSM 관리자 암호를 3번 잘못 가져오는 것입니다(참고: 이는 어플라이언스 관리자가 아니라 실제 HSM 관리자임). 핵심 자료를 보호하는 차원에서 디바이스는 초기화 상태가 될 때까지 Azure 리소스로 간주하며, 삭제할 수 없습니다.

> [!NOTE]
> Gemalto 디바이스 구성과 관련하여 문제가 발생할 경우 [Gemalto 고객 지원팀](https://safenet.gemalto.com/technical-support/)에 연락하시기 바랍니다.

이 리소스 그룹의 모든 리소스를 완료한 경우 다음 명령을 사용하여 모두 제거할 수 있습니다.

```azurecli
az group delete \
   --resource-group myRG \
   --name HSMdeploy \
   --verbose
```

## <a name="next-steps"></a>다음 단계

이 자습서의 단계를 완료하면 Dedicated HSM 리소스가 프로비전되고, 필요한 HSM 및 추가 네트워크 구성 요소가 있는 가상 네트워크를 사용하여 HSM과 통신할 수 있습니다.  이제 기본 배포 아키텍처에 필요한 만큼 리소스를 추가하여 이러한 배포판을 보완할 수 있습니다. 배포 계획에 도움이 되는 자세한 내용은 개념 문서를 참조하세요.
기본 지역에 있는 HSM 두 개가 랙 수준에서 가용성을 지원하고, 보조 지역에 있는 HSM 두 개가 지역 가용성을 지원하는 디자인이 권장됩니다. 

* [고가용성](high-availability.md)
* [물리적 보안](physical-security.md)
* [네트워킹](networking.md)
* [지원 가능성](supportability.md)
* [Monitoring](monitoring.md)
