---
title: "Azure 관리되는 응용 프로그램 사용 | Microsoft Docs"
description: "고객이 게시된 파일에서 Azure 관리되는 응용 프로그램을 만드는 방법에 대해 설명합니다."
services: azure-resource-manager
author: ravbhatnagar
manager: rjmax
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 08/23/2017
ms.author: gauravbh; tomfitz
ms.translationtype: HT
ms.sourcegitcommit: 646886ad82d47162a62835e343fcaa7dadfaa311
ms.openlocfilehash: ed8fbaf2a4546c8e31eeced11cd0b5627fd62c0c
ms.contentlocale: ko-kr
ms.lasthandoff: 08/25/2017

---
# <a name="consume-an-internal-managed-application"></a>내부 관리되는 응용 프로그램 사용

조직의 구성원을 위한 Azure [관리 응용 프로그램](managed-application-overview.md)을 사용할 수 있습니다. 예를 들어 조직 표준을 준수하도록 하는 IT 부서에서 관리되는 응용 프로그램을 선택할 수 있습니다. 이러한 관리되는 응용 프로그램은 Azure Marketplace가 아닌 서비스 카탈로그를 통해 사용할 수 있습니다.

이 문서를 계속하기 전에 구독을 위한 서비스 카탈로그에서 사용할 수 있는 관리되는 응용 프로그램이 있어야 합니다. 조직의 다른 사용자가 관리되는 응용 프로그램을 가지고 있지 않은 경우 [내부 사용을 위한 관리되는 응용 프로그램 게시](managed-application-publishing.md)를 참조하세요.

현재 Azure CLI 또는 Azure Portal 중 하나를 사용하여 관리되는 응용 프로그램을 사용할 수 있습니다.

## <a name="create-the-managed-application-by-using-the-portal"></a>포털을 사용하여 관리되는 응용 프로그램 만들기

포털을 통해 관리되는 응용 프로그램을 배포하려면 다음 단계를 수행합니다.

1. Azure Portal로 이동합니다. **서비스 카탈로그 관리되는 응용 프로그램**을 검색합니다.

   ![서비스 카탈로그 관리되는 응용 프로그램](./media/managed-application-consumption/create-service-catalog-managed-application.png)

1. 사용 가능한 솔루션의 목록에서 만들려는 관리되는 응용 프로그램을 선택합니다. **만들기**를 선택합니다.

   ![관리되는 응용 프로그램 선택](./media/managed-application-consumption/select-offer.png)

1. 리소스를 프로비전하는 데 필요한 매개 변수의 값을 입력합니다. 위치에 **미국 중서부**를 선택합니다. **확인**을 선택합니다.

   ![관리되는 응용 프로그램 매개 변수](./media/managed-application-consumption/input-parameters.png)

1. 템플릿에서 사용자가 제공한 값의 유효성을 검사합니다. 유효성 검사에 성공하면 **확인**을 선택하여 배포를 시작합니다.

   ![관리되는 응용 프로그램 유효성 검사](./media/managed-application-consumption/validation.png)

배포가 완료되면 템플릿에 정의된 해당 리소스를 사용자가 제공한 관리되는 리소스 그룹에 프로비전합니다.

## <a name="create-the-managed-application-by-using-azure-cli"></a>Azure CLI를 사용하여 관리되는 응용 프로그램 만들기

Azure CLI를 사용하여 관리되는 응용 프로그램을 만드는 두 가지 방법이 있습니다.

* 관리되는 응용 프로그램을 만들기 위한 명령을 사용합니다.
* 일반 템플릿 배포 명령을 사용합니다.

### <a name="use-the-template-deployment-command"></a>템플릿 배포 명령 사용

공급업체에서 만든 applianceMainTemplate.json 파일을 배포합니다.

그런 다음 두 개의 리소스 그룹을 만듭니다. 첫 번째 리소스 그룹은 관리되는 응용 프로그램 리소스가 생성되는 위치입니다(Microsoft.Solutions/appliances). 두 번째 리소스 그룹은 mainTemplate.json에 정의된 모든 리소스를 포함합니다. 이 리소스 그룹은 ISV에서 관리합니다.

```azurecli
az group create --name mainResourceGroup --location westcentralus
az group create --name managedResourceGroup --location westcentralus
```

> [!NOTE]
> `westcentralus`를 리소스 그룹의 위치로 지정하세요.
>

mainResourceGroup에 applianceMainTemplate.json을 배포하려면 다음 명령을 사용합니다.

```azurecli
az group deployment create --name managedAppDeployment --resourceGroup mainResourceGroup --templateUri
```

위의 템플릿이 실행된 후 템플릿에 정의된 매개 변수 값을 묻는 메시지가 표시됩니다. 템플릿의 리소스를 프로비전하는 데 필요한 매개 변수 외에도 다음의 두 가지 주요 매개 변수 값이 필요합니다.

- **managedResourceGroupId**: applianceMainTemplate.json에 정의된 리소스를 구성하는 리소스 그룹의 ID입니다. ID의 형식은 `/subscriptions/{subscriptionId}/resourceGroups/{resoureGroupName}`입니다. 위의 예제에서는 `managedResourceGroup`의 ID입니다.
- **applianceDefinitionId**: 관리되는 응용 프로그램 정의 리소스의 ID입니다. 이 값은 ISV에서 제공합니다.

> [!NOTE]
> 게시자는 관리되는 응용 프로그램 정의를 포함하는 리소스 그룹에 대한 액세스를 부여해야 합니다. 정의 리소스는 게시자 구독에 만들어집니다. 따라서 고객 테넌트의 사용자, 사용자 그룹 또는 응용 프로그램은 이 리소스에 대한 읽기 액세스 권한이 필요합니다.

배포가 성공적으로 완료되면 mainResourceGroup에 관리되는 응용 프로그램이 만들어졌음을 알 수 있습니다. storageAccount 리소스는 managedResourceGroup에 만들어집니다.

### <a name="use-the-create-command"></a>만들기 명령 사용

`az managedapp create` 명령을 사용하여 관리되는 응용 프로그램 정의에서 관리되는 응용 프로그램을 만들 수 있습니다.

```azurecli
az managedapp create --name ravtestappliance401 --location "westcentralus"
    --kind "Servicecatalog" --resource-group "ravApplianceCustRG401"
    --managedapp-definition-id "/subscriptions/{guid}/resourceGroups/ravApplianceDefRG401/providers/Microsoft.Solutions/applianceDefinitions/ravtestAppDef401"
    --managed-rg-id "/subscriptions/{guid}/resourceGroups/ravApplianceCustManagedRG401"
    --parameters "{\"storageAccountName\": {\"value\": \"ravappliancedemostore1\"}}"
    --debug
```

* **appliance-definition-Id**: 이전 단계에서 만든 관리되는 응용 프로그램 정의의 리소스 ID입니다. 이 ID를 얻으려면 다음 명령을 실행합니다.

  ```azurecli
  az appliance definition show -n ravtestAppDef1 -g ravApplianceRG2
  ```

  이 명령은 관리되는 응용 프로그램 정의를 반환합니다. ID 속성의 값이 필요합니다.

* **managed-rg-id**: applianceMainTemplate.json에 정의된 리소스를 포함하는 리소스 그룹의 이름입니다. 이 리소스 그룹은 관리되는 리소스 그룹입니다. 게시자가 관리합니다. 리소스 그룹이 없는 경우 현재 사용자에 대해 만들어집니다.
* **resource-group**: 관리되는 응용 프로그램 리소스가 생성되는 리소스 그룹입니다. Microsoft.Solutions/appliance 리소스가 이 리소스 그룹에 있습니다.
* **parameters**: applianceMainTemplate.json에 정의된 리소스에 필요한 매개 변수입니다.

## <a name="known-issues"></a>알려진 문제

이 미리 보기 릴리스에는 다음과 같은 문제가 있습니다.

* 관리되는 응용 프로그램을 만드는 동안 500 내부 서버 오류가 나타납니다. 이 문제가 발생하는 경우 일시적일 가능성이 있습니다. 작업을 다시 시도하세요.
* 관리되는 리소스 그룹으로 새 리소스 그룹이 필요합니다. 기존 리소스 그룹을 사용하는 경우 배포가 실패합니다.
* Microsoft.Solutions/appliances 리소스가 포함된 리소스 그룹은 **westcentralus** 위치에 만들어야 합니다.

## <a name="next-steps"></a>다음 단계

* 관리되는 응용 프로그램에 대한 소개는 [관리되는 응용 프로그램 개요](managed-application-overview.md)를 참조하세요.
* 서비스 카탈로그 관리되는 응용 프로그램을 게시하는 방법에 대한 자세한 내용은 [서비스 카탈로그 관리되는 응용 프로그램 만들기 및 게시](managed-application-publishing.md)를 참조하세요.
* Azure Marketplace에 관리되는 응용 프로그램을 게시하는 방법에 대한 자세한 내용은 [Marketplace의 Azure 관리되는 응용 프로그램](managed-application-author-marketplace.md)을 참조하세요.
* Marketplace의 관리되는 응용 프로그램을 사용하는 방법에 대한 자세한 내용은 [Marketplace의 Azure 관리되는 응용 프로그램 사용](managed-application-consume-marketplace.md)을 참조하세요.

