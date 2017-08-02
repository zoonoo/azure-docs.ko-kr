---
title: "Azure Managed Application 사용 | Microsoft Docs"
description: "고객이 게시된 파일에서 Azure Managed Application을 만드는 방법에 대해 설명합니다."
services: azure-resource-manager
author: ravbhatnagar
manager: rjmax
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 05/17/2017
ms.author: gauravbh; tomfitz
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 8745db3abe6ddec6da1f92da6b7cf8bf82b96d79
ms.contentlocale: ko-kr
ms.lasthandoff: 07/21/2017

---
# <a name="consume-a-service-catalog-managed-application"></a>서비스 카탈로그 관리되는 응용 프로그램 사용

[Managed Application 개요](managed-application-overview.md) 문서에서 설명한 대로 종단 간 환경에는 두 가지 시나리오가 있습니다. 하나는 관리되는 응용 프로그램을 만들어 고객이 사용할 수 있게 하려는 게시자 또는 ISV입니다. 다른 하나는 관리되는 응용 프로그램의 최종 고객 또는 소비자입니다. 이 문서에서는 두 번째 시나리오를 다루며, 최종 고객이 ISV에서 제공하는 관리되는 응용 프로그램을 사용하는 방법에 대해 설명합니다.

현재 Azure CLI 또는 Azure Portal 중 하나를 사용하여 관리되는 응용 프로그램을 사용할 수 있습니다. 

## <a name="create-the-managed-application-using-cli"></a>CLI를 사용하여 관리되는 응용 프로그램 만들기 

사용하려고 하는 어플라이언스에 대한 어플라이언스 정의 ID를 가져와야 합니다.

Azure CLI를 사용하여 관리되는 응용 프로그램을 만드는 두 가지 방법이 있습니다. 하나는 일반 템플릿 배포 명령을 사용하고, 다른 하나는 이 용도로만 제공되는 새로운 명령을 사용합니다.

### <a name="create-using-template-deployment-command"></a>템플릿 배포 명령을 사용하여 만들기

공급업체에서 만든 applianceMainTemplate.json 파일을 배포합니다.

두 개의 리소스 그룹을 만듭니다. 첫 번째 리소스 그룹은 어플라이언스 리소스를 만드는 위치입니다(Microsoft.Solutions/appliances). 두 번째 리소스 그룹은 mainTemplate.json에 정의된 모든 리소스를 포함합니다. 이 리소스 그룹은 ISV에서 관리합니다.

```azurecli
az group create --name mainResourceGroup --location westcentralus    
az group create --name managedResourceGroup --location westcentralus
```

> [!NOTE]
> `westcentralus`를 리소스 그룹의 위치로 지정하세요.
>


다음으로 다음 명령을 사용하여 mainResourceGroup에 applianceMainTemplate.json을 배포합니다.

```azurecli
az group deployment create --name managedAppDeployment --resourceGroup mainResourceGroup --templateUri  
```

위의 템플릿이 실행되면 템플릿에 정의된 매개 변수 값을 묻는 메시지가 표시됩니다. 템플릿의 리소스를 프로비전하는 데 필요한 매개 변수 외에도 다음의 두 가지 주요 매개 변수 값이 필요합니다.

- managedResourceGroupId - applianceMainTemplate.json에 정의된 리소스를 만드는 리소스 그룹의 ID입니다. ID의 형식은 `/subscriptions/{subscriptionId}/resourceGroups/{resoureGroupName}`입니다. 위의 예제에서는 `managedResourceGroup`의 ID입니다.
- applianceDefinitionId - 관리되는 응용 프로그램 정의 리소스의 ID입니다. 이 값은 ISV에서 제공합니다. 

> [!NOTE] 
> ISV는 어플라이언스 정의 리소스를 만드는 리소스 그룹에 대한 액세스 권한을 부여해야 합니다. 어플라이언스 정의 리소스는 ISV 구독에 만들어집니다. 따라서 고객 테넌트의 사용자, 사용자 그룹 또는 응용 프로그램은 이 리소스에 대한 읽기 액세스 권한이 필요합니다. 

배포가 완료되면 **mainResourceGroup**에 해당 어플라이언스 리소스가 만들어졌음을 알 수 있습니다. storageAccount 리소스는 **managedResourceGroup**에 만들어집니다.

### <a name="create-the-managed-application-using-create-command"></a>create 명령을 사용하여 관리되는 응용 프로그램 만들기

`az managedapp create` 명령을 사용하여 관리되는 응용 프로그램 정의에서 관리되는 응용 프로그램을 만들 수 있습니다. 

```azurecli
az managedapp create --name ravtestappliance401 --location "westcentralus" 
    --kind "Servicecatalog" --resource-group "ravApplianceCustRG401" 
    --managedapp-definition-id "/subscriptions/{guid}/resourceGroups/ravApplianceDefRG401/providers/Microsoft.Solutions/applianceDefinitions/ravtestAppDef401" 
    --managed-rg-id "/subscriptions/{guid}/resourceGroups/ravApplianceCustManagedRG401" 
    --parameters "{\"storageAccountName\": {\"value\": \"ravappliancedemostore1\"}}" 
    --debug
```

**appliance-definition-Id** - 이전 단계에서 만든 어플라이언스 정의의 리소스 ID입니다. 이 ID를 얻으려면 다음 명령을 실행합니다.

```azurecli
az appliance definition show -n ravtestAppDef1 -g ravApplianceRG2
```

이 명령은 어플라이언스 정의를 반환합니다. **Id** 속성의 값이 필요합니다.

**managed-rg-id** - applianceMainTemplate.json에 정의된 모든 리소스를 만드는 리소스 그룹의 이름입니다. 이 리소스 그룹은 관리되는 리소스 그룹이며 게시자가 관리합니다. 리소스 그룹이 없는 경우 현재 사용자에 대해 만들어집니다.

**resource-group** - 어플라이언스 리소스를 만드는 리소스 그룹입니다. Microsoft.Solutions/appliance 리소스가 이 리소스 그룹에 있습니다. 

**parameters** - defined in the applianceMainTemplate.json에 정의된 리소스에 필요한 매개 변수입니다.

## <a name="create-the-managed-application-using-portal"></a>포털을 사용하여 관리되는 응용 프로그램 만들기

ISV에서 게시한 관리되는 응용 프로그램을 사용하기 위한 지원도 포털에 있습니다. 수행할 수 있는 단계는 다음과 같습니다.

Azure Portal의 [만들기] 블레이드에서 [서비스 카탈로그 관리되는 응용 프로그램 만들기]를 선택합니다.

![](./media/managed-application-consumption/create-service-catalog-managed-application.png)

다음으로 다양한 ISV/파트너의 제품 목록을 표시합니다. 만들려는 응용 프로그램을 선택하고 "만들기"를 클릭합니다.

![](./media/managed-application-consumption/select-offer.png)

만들기를 클릭한 후에 열리는 블레이드에서 리소스를 프로비전하는 데 필요한 매개 변수를 제공합니다. 

![](./media/managed-application-consumption/input-parameters.png)

값을 입력한 다음 [확인]을 클릭합니다. 입력과 대조하여 템플릿에 대한 유효성을 검사합니다. 유효성 검사가 성공하면 템플릿 배포를 시작합니다. 배포가 완료되면 템플릿에 정의된 해당 리소스를 사용자가 제공한 관리되는 리소스 그룹에 프로비전합니다.

## <a name="known-issues"></a>알려진 문제

이 미리 보기 릴리스에는 다음과 같은 문제가 있습니다.

* 어플라이언스를 만드는 중에 500 내부 서버 오류가 발생하면 간헐적인 문제일 수 있습니다. 이 문제가 발생하면 작업을 다시 시도합니다.
* 관리되는 리소스 그룹으로 새 리소스 그룹이 필요합니다. 기존 리소스 그룹을 사용하면 배포가 실패합니다.
* Microsoft.Solutions/appliances 리소스가 포함된 리소스 그룹은 **westcentralus** 위치에 만들어야 합니다.

## <a name="next-steps"></a>다음 단계

* 관리되는 응용 프로그램에 대한 소개는 [Azure Managed Application 개요](managed-application-overview.md)를 참조하세요.
* 서비스 카탈로그 관리되는 응용 프로그램을 게시하는 방법에 대한 자세한 내용은 [서비스 카탈로그 관리되는 응용 프로그램 만들기 및 게시](managed-application-publishing.md)를 참조하세요.
* Marketplace에 관리되는 응용 프로그램을 게시하는 방법에 대한 자세한 내용은 [Marketplace의 Azure Managed Application](managed-application-author-marketplace.md)을 참조하세요.
* Marketplace의 관리되는 응용 프로그램을 사용하는 방법에 대한 자세한 내용은 [Marketplace의 Azure Managed Application 사용](managed-application-consume-marketplace.md)을 참조하세요.
