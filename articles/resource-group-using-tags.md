<properties
    pageTitle="태그를 사용하여 Azure 리소스 구성 | Microsoft Azure"
    description="태그를 적용하여 대금 청구 및 관리를 위해 리소스를 구성하는 방법을 보여 줍니다."
    services="azure-resource-manager"
    documentationCenter=""
    authors="tfitzmac"
    manager="timlt"
    editor="tysonn"/>

<tags
    ms.service="azure-resource-manager"
    ms.workload="multiple"
    ms.tgt_pltfrm="AzurePortal"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/08/2016"
    ms.author="tomfitz"/>



# <a name="using-tags-to-organize-your-azure-resources"></a>태그를 사용하여 Azure 리소스 구성

리소스 관리자를 사용하면 태그를 적용하여 리소스를 논리적으로 구성할 수 있습니다. 태그는 정의하는 속성을 가진 리소스를 식별하는 키/값 쌍으로 구성됩니다. 동일한 범주에 속하는 것으로 리소스를 표시하려면 이러한 리소스에 동일한 태그를 적용합니다.

특정 태그를 사용하여 리소스를 보는 경우 모든 리소스 그룹에서 리소스를 참조합니다. 배포 관계와 무관하게 연결되는 방식으로 리소스를 구성할 수 있도록 하는 동일한 리소스 그룹의 리소스에 제한되지는 않습니다. 태그는 청구 또는 관리에 대한 리소스를 구성해야 하는 경우에 유용할 수 있습니다.

리소스 또는 리소스 그룹에 추가한 각 태그는 구독 전체의 분류에 자동으로 추가됩니다. 나중에 리소스에 태그를 지정할 때 사용하려는 태그 이름 및 값으로 구독을 위한 분류를 미리 채울 수도 있습니다.

각 리소스 또는 리소스 그룹에는 최대 15개의 태그가 포함될 수 있습니다. 태그 이름은 512자로 제한되며 태그 값은 256자로 제한됩니다.

> [AZURE.NOTE] 리소스 관리자 작업을 지원하는 리소스에만 태그를 적용할 수 있습니다. 클래식 배포 모델을 통해(예: 클래식 포털을 통해) 가상 컴퓨터, 가상 네트워크 또는 저장소를 만든 경우 해당 리소스에 태그를 적용할 수 없습니다. 태그 지정을 지원하려면 Resource Manager를 통해 이러한 리소스를 다시 배포해야 합니다. 다른 모든 리소스는 태그 지정을 지원합니다.

## <a name="templates"></a>템플릿

배포 중 리소스에 태그를 지정하려면 배포할 리소스에 **tags** 요소를 추가하고 태그 이름 및 값을 제공하기만 하면 됩니다. 태그 이름 및 값은 구독에 미리 존재할 필요가 없습니다. 각 리소스에 최대 15개의 태그를 제공할 수 있습니다.

다음 예제에서는 태그가 있는 저장소 계정을 보여 줍니다.

    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2015-06-15",
            "name": "[concat('storage', uniqueString(resourceGroup().id))]",
            "location": "[resourceGroup().location]",
            "tags": {
                "dept": "Finance"
            },
            "properties": 
            {
                "accountType": "Standard_LRS"
            }
        }
    ]

현재 리소스 관리자는 태그 이름 및 값에 대한 개체를 처리하도록 지원하지 않습니다. 대신, 태그 값에 대한 개체를 전달할 수 있지만 아래의 예와 같이 태그 이름을 지정해야 합니다.

    {
      "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "tagvalues": {
          "type": "object",
          "defaultValue": {
            "dept": "Finance",
            "project": "Test"
          }
        }
      },
      "resources": [
      {
        "apiVersion": "2015-06-15",
        "type": "Microsoft.Storage/storageAccounts",
        "name": "examplestorage",
        "tags": {
          "dept": "[parameters('tagvalues').dept]",
          "project": "[parameters('tagvalues').project]"
        },
        "location": "[resourceGroup().location]",
        "properties": {
          "accountType": "Standard_LRS"
        }
      }]
    }


## <a name="portal"></a>포털

[AZURE.INCLUDE [resource-manager-tag-resource](../includes/resource-manager-tag-resources.md)]

## <a name="powershell"></a>PowerShell

[AZURE.INCLUDE [resource-manager-tag-resources](../includes/resource-manager-tag-resources-powershell.md)]

## <a name="azure-cli"></a>Azure CLI

[AZURE.INCLUDE [resource-manager-tag-resources-cli](../includes/resource-manager-tag-resources-cli.md)]

## <a name="rest-api"></a>REST API

포털 및 PowerShell 둘 다 백그라운드에서 [리소스 관리자 REST API](https://msdn.microsoft.com/library/azure/dn848368.aspx) 를 사용합니다. 태그를 다른 환경으로 통합해야 하는 경우 리소스 ID에 대해 GET을 사용하여 태그를 얻고 PATCH 호출을 통해 태그 집합을 업데이트할 수 있습니다.


## <a name="tags-and-billing"></a>태그 및 청구

지원 되는 서비스에 대한 청구 데이터를 그룹화하는 데 태그를 사용할 수 있습니다. 예를 들어, [Azure 리소스 관리자와 통합된 가상 컴퓨터](./virtual-machines/virtual-machines-windows-compare-deployment-models.md) 를 사용하면 가상 컴퓨터에 대한 청구 사용을 구성하는 태그를 정의하고 적용할 수 있습니다. 서로 다른 조직에 여러 VM을 실행하는 경우 비용 센터에 따라 그룹 사용에 대한 태그를 사용할 수 있습니다.  
또한 프로덕션 환경에서 실행 중인 VM에 대한 청구 사용과 같이 런타임 환경으로 비용을 분류하는 데 태그를 사용할 수 있습니다.

[Azure 리소스 사용 및 RateCard API](billing-usage-rate-card-overview.md) 또는 사용 CSV(쉼표로 구분된 값) 파일을 통해 태그에 대한 정보를 검색할 수 있습니다. [Azure 계정 포털](https://account.windowsazure.com/) 또는 [EA 포털](https://ea.azure.com)에서 사용 현황 파일을 다운로드할 수 있습니다. 대금 청구 정보에 프로그래밍 방식으로 액세스하는 방법은 [Microsoft Azure 리소스 소비에 대한 통찰력 얻기](billing-usage-rate-card-overview.md)를 참조하세요. REST API 작업에 대한 내용은 [Azure 청구 REST API 참조](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c)를 참조하세요.

대금 청구에 태그를 지원하는 서비스용 사용 CSV를 다운로드하면 **태그** 열에 태그가 나타납니다. 더 자세한 내용은 [Microsoft Azure 청구서 이해](billing-understand-your-bill.md)를 참조하십시오.

![요금 청구에 대한 태그를 참조하십시오.](./media/resource-group-using-tags/billing_csv.png)

## <a name="next-steps"></a>다음 단계

- 사용자 지정된 정책을 사용하여 구독을 통해 제한 사항 및 규칙을 적용할 수 있습니다. 정의한 정책을 사용하려면 모든 리소스에 특정 태그 값이 있어야 할 수도 있습니다. 자세한 내용은 [정책을 사용하여 리소스 및 컨트롤 액세스 관리](resource-manager-policy.md)를 참조하세요.
- 리소스 배포 시 Azure PowerShell 사용에 대한 소개는 [Azure 리소스 관리자에서 Azure PowerShell 사용](./powershell-azure-resource-manager.md)을 참조하세요.
- 리소스 배포 시 Azure CLI 사용에 대한 소개는 [Azure 리소스 관리에서 Mac, Linux 및 Windows용 Azure CLI 사용](./xplat-cli-azure-resource-manager.md)을 참조하세요.
- 포털 사용에 대한 소개는 [Azure 포털을 사용하여 Azure 리소스 관리](./azure-portal/resource-group-portal.md)  



<!--HONumber=Oct16_HO2-->


