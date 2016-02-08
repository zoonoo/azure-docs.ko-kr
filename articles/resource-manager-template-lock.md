<properties
   pageTitle="리소스 잠금에 대한 리소스 관리자 템플릿 | Microsoft Azure"
   description="템플릿을 통해 리소스 잠금을 배포하기 위한 리소스 관리자 스키마를 보여 줍니다."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="01/21/2016"
   ms.author="tomfitz"/>

# 리소스 잠금 템플릿 스키마

리소스 및 해당 자식 리소스에 새 잠금을 만듭니다.

## 스키마 형식

잠금을 만들려면 템플릿의 리소스 섹션에 다음 스키마를 추가합니다.
    
    {
        "type": enum,
        "apiVersion": "2015-01-01",
        "name": string,
        "dependsOn": [ array values ],
        "properties":
        {
            "level": enum,
            "notes": string
        }
    }



## 값

다음 표에서는 스키마에 설정해야 하는 값에 대해 설명합니다.

| 이름 | 형식 | 필수 | 허용된 값 | 설명 |
| ---- | ---- | -------- | ---------------- | ----------- |
| type | enum | 예 | 리소스의 경우: <br />**{namespace}/{type}/providers/locks**<br /><br />리소스 그룹의 경우:<br />**Microsoft.Authorization/locks** | 만들려는 리소스 종류입니다. |
| apiVersion | enum | 예 | **2015-01-01** | 리소스를 만들 때 사용하는 API 버전입니다. |  
| name | string | 예 | 리소스의 경우:<br />**{resouce}/Microsoft.Authorization/{lockname}**<br /><br />리소스 그룹의 경우:<br />**{lockname}****<br /><br />최대 64자<br /><, > %, &, ? 또는 제어 문자를 포함할 수 없습니다. | 잠글 리소스 및 잠금 이름을 지정하는 값입니다. | | dependsOn | array| 아니요 | 쉼표로 구분된 리소스 이름 및 리소스 고유 식별자 목록입니다. | 이 잠금에 따라 달라지는 리소스 컬렉션입니다. 잠근 리소스가 동일한 템플릿으로 배포되는 경우 해당 리소스 이름을 이 요소에 포함하여 먼저 배포되도록 해야 합니다.| properties | object | 예 | (아래 참조) | 잠금 유형을 식별하고 잠금에 대해 설명하는 개체입니다. | 

### properties 개체

| 이름 | 형식 | 필수 | 허용된 값 | 설명 |
| ------- | ---- | ---------------- | -------- | ----------- |
| level | enum | 예 | **CannotDelete** | 범위에 적용되는 잠금 유형입니다. CanNotDelete을 사용하면 수정할 수 있지만 삭제는 할 수 없습니다. |
| 정보 | string | 아니요 | 512자 | 잠금에 대한 설명입니다. |


## 잠금 리소스를 사용하는 방법

이 리소스를 템플릿에 추가하여 리소스에서 지정된 작업을 수행하지 못하도록 할 수 있습니다. 모든 사용자 및 그룹에 잠금을 적용합니다. 일반적으로 제한된 기간(예: 프로세스가 실행되는 중에 조직의 누군가가 실수로 리소스를 수정하거나 삭제하지 못하도록 하려는 경우) 동안만 잠금을 적용합니다.

관리 잠금을 만들거나 삭제하려면 **Microsoft.Authorization/*** 또는 **Microsoft.Authorization/locks/*** 작업에 대한 액세스 권한이 있어야 합니다. 기본 제공 역할의 경우 **소유자** 및 **사용자 액세스 관리자**에게만 이러한 작업의 권한이 부여됩니다. 역할 기반 액세스 제어에 대한 자세한 내용은 [Azure 역할 기반 액세스 제어](./active-directory/role-based-access-control-configure.md)를 참조하세요.

지정된 리소스 및 모든 하위 리소스에 잠금을 적용합니다.

PowerShell 명령 **Remove-AzureRmResourceLock** 또는 REST API의 [삭제 작업](https://msdn.microsoft.com/library/azure/mt204562.aspx)으로 잠금을 제거할 수 있습니다.

## 예

다음 예제에서는 웹앱에 삭제 불가 잠금을 적용합니다.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "hostingPlanName": {
      			"type": "string"
            }
        },
        "variables": {
            "siteName": "[concat('site',uniqueString(resourceGroup().id))]"
        },
        "resources": [
            {
                "apiVersion": "2015-08-01",
                "name": "[variables('siteName')]",
                "type": "Microsoft.Web/sites",
                "location": "[resourceGroup().location]",
                "properties": {
                    "serverFarmId": "[parameters('hostingPlanName')]"
                },
            },
            {
                "type": "Microsoft.Web/sites/providers/locks",
                "apiVersion": "2015-01-01",
                "name": "[concat(variables('siteName'),'/Microsoft.Authorization/MySiteLock')]",
                "dependsOn": [ "[variables('siteName')]" ],
                "properties":
                {
                    "level": "CannotDelete",
                    "notes": "my notes"
                }
             }
        ],
        "outputs": {}
    }

다음 예제에서는 리소스 그룹에 삭제 불가 잠금을 적용합니다.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {},
        "variables": {},
        "resources": [
            {
                "type": "Microsoft.Authorization/locks",
                "apiVersion": "2015-01-01",
                "name": "MyGroupLock",
                "properties":
                {
                    "level": "CannotDelete",
                    "notes": "my notes"
                }
            }
        ],
        "outputs": {}
    }

## 다음 단계

- 템플릿 구조에 대한 자세한 내용은 [Azure 리소스 관리자 템플릿 작성](resource-group-authoring-templates.md)을 참조하세요.
- 잠금에 대한 자세한 내용은 [Azure 리소스 관리자를 사용하여 리소스 잠그기](resource-group-lock-resources.md)를 참조하세요.

<!---HONumber=AcomDC_0128_2016-->