<properties 
	pageTitle="Azure 리소스 관리자를 사용하여 리소스 잠그기" 
	description="사용자가 특정 리소스를 업데이트하거나 삭제하지 못하도록 리소스를 잠급니다." 
	services="azure-resource-manager" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="azure-resource-manager" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/15/2015" 
	ms.author="tomfitz"/>

# Azure 리소스 관리자를 사용하여 리소스 잠그기

관리자는 리소스 또는 리소스 그룹에 잠금을 설정하여 조직의 다른 사용자가 쓰기 동작을 커밋하거나 실수로 중요한 리소스를 삭제하지 못하게 하려는 경우가 있을 수 있습니다.

Azure 리소스 관리자는 리소스 관리 잠금을 통해 리소스에 대한 작업을 제한하는 기능을 제공합니다. 리소스 잠금은 특정 범위에서 잠금 수준을 적용하는 정책입니다. 잠금 수준은 정책에 대한 적용 유형을 나타내며, 현재 **CanNotDelete** 및 **ReadOnly**의 두 값을 갖습니다. 범위는 URI로 표현되며 리소스 또는 리소스 그룹일 수 있습니다.

잠금은 특정 동작을 수행할 수 있는 사용자 권한을 할당하는 것과는 다릅니다. 사용자 및 역할에 대한 권한을 설정하는 방법에 대한 자세한 내용은 [미리 보기 포털에서 역할 기반 액세스 제어](role-based-access-control-configure.md) 및 [리소스에 대한 액세스 관리 및 감사](resource-group-rbac.md)를 참조하세요.

## 일반적인 시나리오

일반적인 시나리오 중 하나는 일부 리소스가 설정 및 해제 패턴으로 사용되는 리소스 그룹을 유지하는 경우입니다. VM 리소스는 지정된 시간 간격 동안 데이터를 처리하기 위해 주기적으로 켜졌다가 꺼집니다. 이 시나리오에서는 VM의 종료를 활성화할 수 있지만 저장소 계정이 삭제되지 않도록 해야 합니다. 이 시나리오에서는 저장소 계정에 대해 **CanNotDelete** 잠금 수준의 리소스 잠금을 사용할 수 있습니다.

다른 시나리오의 경우 회사에서 업데이트를 프로덕션에 적용하지 않으려는 기간이 있을 수 있습니다. **ReadOnly** 잠금 수준은 생성 또는 업데이트를 중지합니다. 소매 회사라면 휴일 쇼핑 기간 동안 업데이트를 허용하지 않으려고 할 수 있습니다. 금융 서비스 회사라면 특정 시장 시간 동안 배포와 관련된 제약 조건을 적용할 수 있습니다. 리소스 잠금은 리소스를 적절히 잠그는 정책을 제공할 수 있습니다. 이 기능은 특정 리소스 또는 리소스 그룹 전체에 적용될 수 있습니다.

## 템플릿에서 잠금 만들기

아래 예제에서는 저장소 계정에 대한 잠금을 만드는 템플릿을 보여 줍니다. 잠금을 적용할 저장소 계정은 매개 변수로 제공되어 concat() 함수와 함께 사용됩니다. 결과적으로 리소스 이름 앞에 'Microsoft.Authorization'이 나오고, 그 다음에 잠금의 이름(이 경우 **myLock**)이 나옵니다.

제공된 형식은 리소스 형식에 따라 다릅니다. 저장소의 경우 이 형식은 "Microsoft.Storage/storageaccounts/providers/locks"입니다.

범위 수준은 리소스의 **level** 속성을 사용하여 설정됩니다. 이 예제에서는 부주의한 삭제를 방지하는 데 도움을 주는 데 초점을 맞추므로 수준은 **CannotDelete**로 설정되어 있습니다.

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
            "level": {lock-level},
            "notes": "Optional text notes."
        }
    } 

lock-level로 **CanNotDelete** 또는 **ReadOnly**를 지정합니다.

예제를 보려면 [관리 잠금을 위한 REST API](https://msdn.microsoft.com/library/azure/mt204563.aspx)를 참조하세요.

## Azure PowerShell을 사용하여 잠금 만들기

아래와 같이 **New-AzureResourceLock**을 사용하여 Azure PowerShell을 통해 배포된 리소스를 잠글 수 있습니다.

    PS C:\> New-AzureResourceLock -LockLevel CanNotDelete -LockName LockSite -ResourceName examplesite -ResourceType Microsoft.Web/sites -ResourceGroupName ExampleGroup

PowerShell은 잠금을 업데이트하기 위한 **Set-AzureResourceLock**, 잠금을 삭제하기 위한 **Remove-AzureResourceLock**와 같은 잠금 사용을 위한 다른 명령도 제공합니다.

## 다음 단계

- [태그를 사용하여 리소스 구성](resource-group-using-tags.md)
- [새 리소스 그룹으로 리소스 이동](resource-group-move-resources.md)

<!---HONumber=July15_HO4-->