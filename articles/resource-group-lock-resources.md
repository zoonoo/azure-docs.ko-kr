<properties 
	pageTitle="리소스 관리자로 리소스 잠금 | Microsoft Azure" 
	description="모든 사용자 및 역할에 제한을 적용하여 사용자가 특정 리소스를 업데이트 또는 삭제하지 못하도록 합니다." 
	services="azure-resource-manager" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="timlt" 
	editor="tysonn"/>

<tags 
	ms.service="azure-resource-manager" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/26/2016" 
	ms.author="tomfitz"/>

# Azure 리소스 관리자를 사용하여 리소스 잠그기

관리자는 구독, 리소스 그룹 또는 리소스에 잠금을 설정하여 조직의 다른 사용자가 실수로 중요한 리소스를 삭제 또는 수정하지 못하게 할 수 있습니다. 잠금 수준을 **CanNotDelete** 또는 **ReadOnly**로 설정할 수 있습니다.

- **CanNotDelete**는 권한이 있는 사용자가 여전히 리소스를 읽고 수정할 수 있지만 삭제할 수 없음을 의미합니다. 
- **ReadOnly**는 권한이 있는 사용자가 리소스에서 읽을 수 있지만 해당 리소스를 삭제하거나 어떤 작업을 수행할 수 없음을 의미합니다. 리소스에 대한 사용 권한이 **판독기** 역할로 제한됩니다. **ReadOnly**를 적용하면 읽기 작업처럼 보이는 일부 작업에 실제로 추가 작업이 필요하기 때문에 예기치 않은 결과가 발생할 수 있습니다. 예를 들어 저장소 계정에 **ReadOnly** 잠금을 설정하면 모든 사용자가 키를 나열하지 않도록 방지합니다. 반환되는 키를 쓰기 작업에 사용할 수 있기 때문에 목록 키 작업은 POST 요청을 통해 처리됩니다. 또 다른 예로 앱 서비스 리소스에 **ReadOnly** 잠금을 설정하면 해당 상호 작용이 쓰기 액세스를 필요로 하기 때문에 Visual Studio 서버 탐색기가 리소스에 대한 파일을 표시하지 않도록 방지합니다.

역할 기반 액세스 제어와 달리 관리 잠금을 사용하여 모든 사용자와 역할에 걸쳐 제한을 적용합니다. 사용자 및 역할에 대한 권한 설정에 대해 알아보려면 [Azure 역할 기반 액세스 제어](./active-directory/role-based-access-control-configure.md)를 참조하세요.

부모 범위에서 잠금을 적용하면 모든 자식 리소스가 동일한 잠금을 상속합니다. 상속의 가장 제한적인 잠금이 우선 적용됩니다.

## 조직에서 잠금을 만들거나 삭제할 수 있는 사람

관리 잠금을 만들거나 삭제하려면 **Microsoft.Authorization/*** 또는 **Microsoft.Authorization/locks/*** 작업에 대한 액세스 권한이 있어야 합니다. 기본 제공 역할의 경우 **소유자** 및 **사용자 액세스 관리자**에게만 이러한 작업의 권한이 부여됩니다.

## 포털을 통해 잠금 만들기

1. 잠그려는 리소스, 리소스 그룹 또는 구독에 대한 설정 블레이드에서 **잠금**을 선택합니다.

      ![잠금 선택](./media/resource-group-lock-resources/select-lock.png)

2. 잠금을 추가하려면 **추가**를 선택합니다. 현재 선택한 리소스에서 상속될 부모 수준에서 잠금을 만들려면 부모(예: 아래 표시된 구독)를 선택합니다.

      ![잠금 추가](./media/resource-group-lock-resources/add-lock.png)

3. 잠금에 이름과 잠금 수준을 지정합니다. 필요한 경우 잠금이 필요한 이유를 설명하는 참고 사항을 추가할 수 있습니다.

      ![잠금 설정](./media/resource-group-lock-resources/set-lock.png)

4. 잠금을 삭제하려면 사용 가능한 옵션에서 줄임표와 **삭제**를 선택합니다.

      ![잠금 삭제](./media/resource-group-lock-resources/delete-lock.png)

## 템플릿에서 잠금 만들기

아래 예제에서는 저장소 계정에 대한 잠금을 만드는 템플릿을 보여 줍니다. 잠금을 적용할 저장소 계정은 매개 변수로 제공됩니다. 잠금 이름은 리소스 이름을 **/Microsoft.Authorization/**과 연결하여 만들어집니다. 이 예제의 경우 잠금 이름은 **myLock**입니다.

제공된 형식은 리소스 형식에 따라 다릅니다. 저장소의 경우 이 형식은 "Microsoft.Storage/storageaccounts/providers/locks"입니다.

    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "lockedResource": {
          "type": "string"
        }
      },
      "resources": [
        {
          "name": "[concat(parameters('lockedResource'), '/Microsoft.Authorization/myLock')]",
          "type": "Microsoft.Storage/storageAccounts/providers/locks",
          "apiVersion": "2015-01-01",
          "properties": {
            "level": "CannotDelete"
          }
        }
      ]
    }

## REST API를 사용하여 잠금 만들기

[관리 잠금을 위한 REST API](https://msdn.microsoft.com/library/azure/mt204563.aspx)를 사용하여 배포된 리소스를 잠글 수 있습니다. REST API를 사용하여 잠금을 만들고, 삭제하고, 기존 잠금에 대한 정보를 검색할 수 있습니다.

잠금을 만들려면 다음을 실행합니다.

    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/locks/{lock-name}?api-version={api-version}

범위는 구독, 리소스 그룹 또는 리소스일 수 있습니다. lock-name은 원하는 잠금 이름입니다. api-version에는 **2015-01-01**을 사용합니다.

요청에서 잠금에 대한 속성을 지정하는 JSON 개체를 포함합니다.

    {
      "properties": {
        "level": "CanNotDelete",
        "notes": "Optional text notes."
      }
    } 

예제를 보려면 [관리 잠금을 위한 REST API](https://msdn.microsoft.com/library/azure/mt204563.aspx)를 참조하세요.

## Azure PowerShell을 사용하여 잠금 만들기

아래와 같이 **New-AzureRmResourceLock**을 사용하여 Azure PowerShell을 통해 배포된 리소스를 잠글 수 있습니다.

    New-AzureRmResourceLock -LockLevel CanNotDelete -LockName LockSite -ResourceName examplesite -ResourceType Microsoft.Web/sites

Azure PowerShell은 잠금을 업데이트하기 위한 **Set-AzureRmResourceLock**, 잠금을 삭제하기 위한 **Remove-AzureRmResourceLock**와 같은 잠금 사용을 위한 다른 명령도 제공합니다.

## 다음 단계

- 리소스 잠금 작업에 대한 자세한 내용은 [Azure 리소스 잠금](http://blogs.msdn.com/b/cloud_solution_architect/archive/2015/06/18/lock-down-your-azure-resources.aspx)을 참조하세요.
- 리소스를 논리적으로 구성하는 방법에 대한 자세한 내용은 [태그를 사용하여 리소스 구성](resource-group-using-tags.md)을 참조하세요.
- 리소스가 존재하는 리소스 그룹을 변경하려면 [새 리소스 그룹으로 리소스 이동](resource-group-move-resources.md)을 참조하세요.
- 사용자 지정된 정책을 사용하여 구독을 통해 제한 사항 및 규칙을 적용할 수 있습니다. 자세한 내용은 [정책을 사용하여 리소스 및 컨트롤 액세스 관리](resource-manager-policy.md)를 참조하세요.

<!---HONumber=AcomDC_0601_2016-->