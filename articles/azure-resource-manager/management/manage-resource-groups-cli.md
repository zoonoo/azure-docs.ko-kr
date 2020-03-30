---
title: 리소스 그룹 관리 - Azure CLI
description: Azure CLI를 사용하여 Azure 리소스 관리자를 통해 리소스 그룹을 관리합니다. 리소스 그룹을 만들고, 나열하고, 삭제하는 방법을 보여 주면 됩니다.
author: mumian
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: 7face572f545153ea92efbdb345bbaabda5dd126
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248334"
---
# <a name="manage-azure-resource-manager-resource-groups-by-using-azure-cli"></a>Azure CLI를 사용하여 Azure 리소스 관리자 리소스 그룹 관리

Azure [리소스 관리자와](overview.md) 함께 Azure CLI를 사용하여 Azure 리소스 그룹을 관리하는 방법을 알아봅니다. Azure 리소스를 관리하려는 경우 [Azure CLI를 사용하여 Azure 리소스 관리를](manage-resources-cli.md)참조하십시오.

리소스 그룹 관리에 대한 기타 문서:

- [Azure 포털을 사용하여 Azure 리소스 그룹 관리](manage-resources-portal.md)
- [Azure PowerShell을 사용하여 Azure 리소스 그룹 관리](manage-resources-powershell.md)

## <a name="what-is-a-resource-group"></a>리소스 그룹이란 무엇입니까?

리소스 그룹은 Azure 솔루션에 관련된 리소스를 보유하는 컨테이너입니다. 리소스 그룹에는 솔루션에 대한 모든 리소스 또는 그룹으로 관리하려는 해당 리소스만 포함될 수 있습니다. 사용자의 조직에 가장 적합한 내용에 따라 리소스 그룹에 리소스를 어떻게 할당할지 결정합니다. 일반적으로 쉽게 배포, 업데이트하고 그룹으로 삭제할 수 있도록 동일한 리소스 그룹에 대해 동일한 수명 주기를 공유하는 리소스를 추가합니다.

리소스 그룹은 리소스에 대한 메타데이터를 저장합니다. 따라서 리소스 그룹의 위치를 지정하면 메타데이터가 저장된 위치를 지정하게 됩니다. 규정 준수 때문에 특정 지역에 데이터가 저장되는지 확인해야 합니다.

리소스 그룹은 리소스에 대한 메타데이터를 저장합니다. 리소스 그룹의 위치를 지정하면 메타데이터가 저장되는 위치를 지정하게 됩니다.

## <a name="create-resource-groups"></a>리소스 그룹 만들기

다음 CLI 스크립트는 리소스 그룹을 만들고 리소스 그룹을 표시합니다.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
az group create --name $resourceGroupName --location $location
```

## <a name="list-resource-groups"></a>리소스 그룹 나열

다음 CLI 스크립트는 구독 아래에 리소스 그룹을 나열합니다.

```azurecli-interactive
az group list
```

하나의 리소스 그룹을 얻으려면 다음을 수행하십시오.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group show --name $resourceGroupName
```

## <a name="delete-resource-groups"></a>리소스 그룹 삭제

다음 CLI 스크립트는 리소스 그룹을 삭제합니다.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName
```

Azure 리소스 관리자가 리소스 삭제를 명령하는 방법에 대한 자세한 내용은 [Azure 리소스 관리자 리소스 그룹 삭제를](delete-resource-group.md)참조하십시오.

## <a name="deploy-resources-to-an-existing-resource-group"></a>기존 리소스 그룹에 리소스 배포

[기존 리소스 그룹에 리소스 배포를 참조합니다.](manage-resources-cli.md#deploy-resources-to-an-existing-resource-group)

## <a name="deploy-a-resource-group-and-resources"></a>리소스 그룹 및 리소스 배포

리소스 관리자 템플릿을 사용하여 리소스 그룹을 만들고 그룹에 리소스를 배포할 수 있습니다. 자세한 내용은 [리소스 그룹 만들기 및 리소스 배포](../templates/deploy-to-subscription.md#resource-group-and-resources)를 참조하세요.

## <a name="redeploy-when-deployment-fails"></a>배포 실패 시 다시 배포

이 기능은 *오류에 대한 롤백이라고도*합니다. 자세한 내용은 [배포가 실패할 때 재배포를](../templates/rollback-on-error.md)참조하십시오.

## <a name="move-to-another-resource-group-or-subscription"></a>다른 리소스 그룹 또는 구독으로 이동

그룹의 리소스를 다른 리소스 그룹으로 이동할 수 있습니다. 자세한 내용은 [리소스 이동](manage-resources-cli.md#move-resources)을 참조하십시오.

## <a name="lock-resource-groups"></a>리소스 그룹 잠금

잠금을 사용하면 조직의 다른 사용자가 Azure 구독, 리소스 그룹 또는 리소스와 같은 중요한 리소스를 실수로 삭제하거나 수정할 수 없습니다. 

다음 스크립트는 리소스 그룹을 삭제할 수 없도록 리소스 그룹을 잠그습니다.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az lock create --name LockGroup --lock-type CanNotDelete --resource-group $resourceGroupName  
```

다음 스크립트는 리소스 그룹에 대한 모든 잠금을 가져옵니다.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az lock list --resource-group $resourceGroupName  
```

다음 스크립트는 잠금을 삭제합니다.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the lock name:" &&
read lockName &&
az lock delete --name $lockName --resource-group $resourceGroupName
```

자세한 내용은 [Azure 리소스 관리자를 사용 하 여 리소스 잠금](lock-resources.md)을 참조 합니다.

## <a name="tag-resource-groups"></a>리소스 그룹에 태그

리소스 그룹 및 리소스에 태그를 적용하여 논리적으로 자산을 구성할 수 있습니다. 자세한 내용은 [태그 사용을 사용하여 Azure 리소스를 구성합니다.](tag-resources.md#azure-cli)

## <a name="export-resource-groups-to-templates"></a>리소스 그룹을 템플릿으로 내보내기

리소스 그룹을 성공적으로 설정한 후 리소스 그룹에 대한 리소스 관리자 템플릿을 볼 수 있습니다. 템플릿을 내보내면 다음과 같은 두 가지 이점이 있습니다.

- 템플릿에 모든 전체 인프라가 포함되어 있으므로 솔루션의 향후 배포를 자동화합니다.
- 솔루션을 나타내는 JSON(자바스크립트 개체 표기법)을 확인하여 템플릿 구문을 알아봅니다.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group export --name $resourceGroupName  
```

스크립트는 콘솔에 템플릿을 표시합니다.  JSON을 복사하고 파일로 저장합니다.

내보내기 템플릿 기능은 Azure 데이터 팩토리 리소스 내보내기를 지원하지 않습니다. 데이터 팩터리 리소스를 내보내는 방법에 대해 자세히 알아보려면 [Azure Data Factory에서 데이터 팩터리 복사 또는 복제를](https://aka.ms/exportTemplateViaAdf)참조하세요.

클래식 배포 모델을 통해 생성된 리소스를 내보내려면 [리소스 관리자 배포 모델로 마이그레이션해야](https://aka.ms/migrateclassicresourcetoarm)합니다.

자세한 내용은 [Azure 포털의 템플릿으로의 단일 및 다중 리소스 내보내기를](../templates/export-template-portal.md)참조하십시오.

## <a name="manage-access-to-resource-groups"></a>리소스 그룹에 대한 액세스 관리

[RBAC(역할 기반 액세스 제어)](../../role-based-access-control/overview.md)는 Azure에서 리소스에 대한 액세스를 관리하는 방법입니다. 자세한 내용은 [RBAC 및 Azure CLI를 사용하여 액세스 관리를](../../role-based-access-control/role-assignments-cli.md)참조하십시오.

## <a name="next-steps"></a>다음 단계

- Azure 리소스 관리자에 대해 알아보려면 [Azure 리소스 관리자 개요를](overview.md)참조하십시오.
- 리소스 관리자 템플릿 구문을 알아보려면 [Azure Resource Manager 템플릿의 구조 및 구문 이해를](../templates/template-syntax.md)참조하십시오.
- 템플릿을 개발하는 방법에 대한 자세한 내용은 [단계별 자습서를](/azure/azure-resource-manager/)참조하십시오.
- Azure 리소스 관리자 템플릿 스키마를 보려면 [템플릿 참조를](/azure/templates/)참조하십시오.