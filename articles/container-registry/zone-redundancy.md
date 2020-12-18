---
title: 고가용성을 위한 영역 중복 레지스트리
description: Azure 가용성 영역에서 컨테이너 레지스트리 또는 복제를 만들어 Azure Container Registry에서 영역 중복성을 사용 하도록 설정 하는 방법에 대해 알아봅니다. 영역 중복성은 프리미엄 서비스 계층의 기능입니다.
ms.topic: article
ms.date: 12/11/2020
ms.openlocfilehash: f94d5a8d61c42e8833e21f035303be173c81764d
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/18/2020
ms.locfileid: "97681818"
---
# <a name="enable-zone-redundancy-in-azure-container-registry-for-resiliency-and-high-availability"></a>복원 력 및 고가용성을 위해 Azure Container Registry에서 영역 중복성 사용

하나 이상의 Azure 지역에서 레지스트리 데이터를 복제 하 여 가용성을 제공 하 고 지역 운영에 대 한 대기 시간을 줄이는 [지역 복제](container-registry-geo-replication.md)외에도 Azure Container Registry 선택적 *영역 중복성* 을 지원 합니다. [영역 중복성](../availability-zones/az-overview.md#availability-zones) 은 특정 지역의 레지스트리 또는 복제 리소스 (복제본)에 대 한 복원 력 및 고가용성을 제공 합니다.

이 문서에서는 Azure Portal 또는 Azure Resource Manager 템플릿을 사용 하 여 영역 중복 컨테이너 레지스트리 또는 영역 중복 복제본을 설정 하는 방법을 보여 줍니다. 

영역 중복성은 프리미엄 컨테이너 레지스트리 서비스 계층의 **미리 보기** 기능입니다. 레지스트리 서비스 계층 및 제한에 대한 자세한 내용은 [Azure Container Registry 서비스 계층](container-registry-skus.md)을 참조하세요.

## <a name="preview-limitations"></a>미리 보기 제한 사항

* 현재 미국 동부, 미국 동부 2, 미국 서 부 2 지역에서 지원 됩니다.
* 가용성 영역에 대 한 지역 변환은 현재 지원 되지 않습니다. 지역에서 가용성 영역 지원을 사용 하도록 설정 하려면 원하는 지역에 레지스트리를 만들어야 합니다. 가용성 영역 지원을 사용 하도록 설정 하거나 가용성 영역 지원을 사용 하도록 설정 하 여 복제 된 영역을 추가 해야 합니다.
* 영역 중복성은 지역에서 사용 하지 않도록 설정할 수 없습니다.
* [ACR 작업](container-registry-tasks-overview.md) 은 아직 가용성 영역을 지원 하지 않습니다.
* 현재 Azure Resource Manager 템플릿 또는 Azure Portal를 통해 지원 됩니다. Azure CLI 지원은 이후 릴리스에서 사용 하도록 설정 됩니다.
* 현재 영역 중복 컨테이너 레지스트리를 다른 리소스 그룹으로 이동 하는 경우 영역 중복 설정은로 표시 `Disabled` 됩니다.

## <a name="about-zone-redundancy"></a>영역 중복성 정보

Azure [가용성 영역](../availability-zones/az-overview.md) 을 사용 하 여 azure 지역 내에서 복원 력 및 고가용성 azure container registry를 만듭니다. 예를 들어 조직은 지역 내에서 고가용성을 제공 하는 동시에 다른 [지원 되는 azure 리소스](../availability-zones/az-region.md) 를 사용 하 여 영역 중복 azure container registry를 설정 하 여 데이터 상주 또는 기타 규정 준수 요구 사항을 충족할 수 있습니다.

또한 Azure Container Registry는 여러 지역에 걸쳐 서비스를 복제 하는 [지역에서 복제](container-registry-geo-replication.md)를 지원 하 여 중복성 및 집약성이 다른 위치에 있는 리소스를 계산할 수 있도록 합니다. 지역 내 중복성에 대 한 가용성 영역을 조합 하 고 여러 지역에서 지역에서 복제를 조합 하 여 레지스트리의 안정성과 성능을 향상 시킵니다.

가용성 영역은 Azure 지역 내의 고유한 물리적 위치입니다. 복원력을 보장하려면 활성화된 모든 지역에서 최소한 세 개의 별도 영역이 필요합니다. 각 영역에는 독립 된 전원, 냉각 및 네트워킹을 갖춘 하나 이상의 데이터 센터가 있습니다. 영역 중복성에 대해 구성 된 경우 레지스트리 (또는 다른 지역에 있는 레지스트리 복제본)가 해당 지역의 모든 가용성 영역에 복제 되므로 데이터 센터 오류가 발생 하는 경우에도 사용할 수 있습니다.

## <a name="create-a-zone-redundant-registry---portal"></a>영역 중복 레지스트리 만들기-포털

1. [https://portal.azure.com](https://portal.azure.com)에서 Azure Portal에 로그인합니다.
1. **리소스 만들기** > **컨테이너** > **컨테이너 레지스트리** 를 선택합니다.
1. **기본 사항** 탭에서 리소스 그룹을 선택 하거나 만들고 고유한 레지스트리 이름을 입력 합니다. 
1. **위치** 에서 *미국 동부* 와 같이 Azure Container Registry에 대 한 영역 중복성을 지 원하는 지역을 선택 합니다.
1. **SKU** 에서 **프리미엄** 을 선택합니다.
1. **가용성 영역** 에서 **사용** 을 선택 합니다.
1. 필요에 따라 추가 레지스트리 설정을 구성한 다음 **검토 + 만들기** 를 선택 합니다.
1. **만들기** 를 선택하여 레지스트리 인스턴스를 배포합니다.

    :::image type="content" source="media/zone-redundancy/enable-availability-zones-portal.png" alt-text="Azure Portal에서 영역 중복성 사용":::

영역 중복 복제를 만들려면 다음을 수행 합니다.

1. 프리미엄 계층 컨테이너 레지스트리로 이동 하 고 **복제** 를 선택 합니다.
1. 표시 되는 맵에 Azure Container Registry에 대 한 영역 중복성을 지 원하는 지역에서 녹색 육각형을 선택 합니다 (예: **미국 서 부 2**). 그런 다음 **만들기** 를 선택합니다.
1. **복제 만들기** 창의 **가용성 영역** 에서 **사용** 을 선택 하 고 **만들기** 를 선택 합니다.

## <a name="create-a-zone-redundant-registry---template"></a>영역 중복 레지스트리 만들기-템플릿

### <a name="create-a-resource-group"></a>리소스 그룹 만들기

필요한 경우 [az group create](/cli/az/group#az_group_create) 명령을 실행 하 여 *에서는 eastus* 와 같이 Azure Container Registry에 대 한 [가용성 영역을 지 원하는](../availability-zones/az-region.md) 지역에 레지스트리에 대 한 리소스 그룹을 만듭니다.

```azurecli
az group create --name <resource-group-name> --location <location>
```

### <a name="deploy-the-template"></a>템플릿 배포 

다음 리소스 관리자 템플릿을 사용 하 여 영역 중복 지역에서 복제 된 레지스트리를 만들 수 있습니다. 기본적으로 템플릿은 레지스트리의 영역 중복성 및 추가 지역 복제본을 사용 하도록 설정 합니다. 

다음 내용을 새 파일에 복사하고 `registryZone.json`과 같은 파일 이름을 사용하여 저장합니다.

```JSON
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "acrName": {
        "type": "string",
        "defaultValue": "[concat('acr', uniqueString(resourceGroup().id))]",
        "minLength": 5,
        "maxLength": 50,
        "metadata": {
          "description": "Globally unique name of your Azure Container Registry"
        }
      },
      "location": {
        "type": "string",
        "defaultValue": "[resourceGroup().location]",
        "metadata": {
          "description": "Location for registry home replica."
        }
      },
      "acrSku": {
        "type": "string",
        "defaultValue": "Premium",
        "allowedValues": [
          "Premium"
        ],
        "metadata": {
          "description": "Tier of your Azure Container Registry. Geo-replication and zone redundancy require Premium SKU."
        }
      },
      "acrZoneRedundancy": {
        "type": "string",
        "defaultValue": "Enabled",
        "metadata": {
          "description": "Enable zone redundancy of registry's home replica. Requires registry location to support availability zones."
        }
      },
      "acrReplicaLocation": {
        "type": "string",
        "metadata": {
          "description": "Short name for registry replica location."
        }
      },
      "acrReplicaZoneRedundancy": {
        "type": "string",
        "defaultValue": "Enabled",
        "metadata": {
          "description": "Enable zone redundancy of registry replica. Requires replica location to support availability zones."
        }
      }
    },
    "resources": [
      {
        "comments": "Container registry for storing docker images",
        "type": "Microsoft.ContainerRegistry/registries",
        "apiVersion": "2020-11-01-preview",
        "name": "[parameters('acrName')]",
        "location": "[parameters('location')]",
        "sku": {
          "name": "[parameters('acrSku')]",
          "tier": "[parameters('acrSku')]"
        },
        "tags": {
          "displayName": "Container Registry",
          "container.registry": "[parameters('acrName')]"
        },
        "properties": {
          "adminUserEnabled": "[parameters('acrAdminUserEnabled')]",
          "zoneRedundancy": "[parameters('acrZoneRedundancy')]"
        }
      },
      {
        "type": "Microsoft.ContainerRegistry/registries/replications",
        "apiVersion": "2020-11-01-preview",
        "name": "[concat(parameters('acrName'), '/', parameters('acrReplicaLocation'))]",
        "location": "[parameters('acrReplicaLocation')]",
          "dependsOn": [
          "[resourceId('Microsoft.ContainerRegistry/registries/', parameters('acrName'))]"
        ],
        "properties": {
          "zoneRedundancy": "[parameters('acrReplicaZoneRedundancy')]"
        }
      }
    ],
    "outputs": {
      "acrLoginServer": {
        "value": "[reference(resourceId('Microsoft.ContainerRegistry/registries',parameters('acrName')),'2019-12-01-preview').loginServer]",
        "type": "string"
      }
    }
  }
```

다음 [az deployment group create](/cli/az/deployment#az_group_deployment_create) 명령을 실행 하 여 이전 템플릿 파일을 사용 하 여 레지스트리를 만듭니다. 표시 되는 경우 다음을 제공 합니다.

* 고유한 레지스트리 이름 또는 매개 변수 없이 템플릿을 배포 하 고 고유한 이름을 만듭니다.
* 가용성 영역을 지 원하는 복제본의 위치 (예: *westus2* )

```azurecli
az deployment group create \
  --resource-group <resource-group-name> \
  --template-file registryZone.json \
  --parameters acrName=<registry-name> acrReplicaLocation=<replica-location>
```

명령 출력에서 `zoneRedundancy` 레지스트리 및 복제본에 대 한 속성을 확인 합니다. 사용 하도록 설정 된 경우 각 리소스는 영역 중복입니다.

```JSON
{
 [...]
"zoneRedundancy": "Enabled",
}
```

## <a name="next-steps"></a>다음 단계

* [가용성 영역을 지 원하는 지역](../availability-zones/az-region.md)에 대해 자세히 알아보세요.
* Azure에서 [안정성](/azure/architecture/framework/resiliency/overview) 을 위한 빌드에 대해 자세히 알아보세요.