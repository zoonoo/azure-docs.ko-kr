---
title: "리소스 잠금에 대한 Resource Manager 템플릿 | Microsoft Docs"
description: "템플릿을 통해 리소스 잠금을 배포하기 위한 리소스 관리자 스키마를 보여 줍니다."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: 
ms.assetid: de6c0c57-e33a-4960-98e0-900901592003
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/03/2016
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: e841c21a15c47108cbea356172bffe766003a145
ms.openlocfilehash: 16f87b8859a5d98c2328388ef12b13ef0e261861


---
# <a name="resource-locks-template-schema"></a>리소스 잠금 템플릿 스키마
리소스 및 해당 자식 리소스에 잠금을 만듭니다.

## <a name="schema-format"></a>스키마 형식
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



## <a name="values"></a>값
다음 표에서는 스키마에 설정해야 하는 값에 대해 설명합니다.

| 이름 | 필수 | 설명 |
| --- | --- | --- |
| type |예 |만들려는 리소스 종류입니다.<br /><br />리소스의 경우:<br />**{namespace}/{type}/providers/locks**<br /><br/>리소스 그룹의 경우:<br />**Microsoft.Authorization/locks** |
| apiVersion |예 |리소스를 만들 때 사용하는 API 버전입니다.<br /><br />사용:<br />**2015-01-01**<br /><br /> |
| 이름 |예 |잠글 리소스와 잠금의 이름을 지정하는 값입니다. 최대 64자까지 가능하고, <, > %, &, ? 또는 제어 문자는 포함할 수 없습니다.<br /><br />리소스의 경우:<br />**{resource}/Microsoft.Authorization/{lockname}**<br /><br />리소스 그룹의 경우:<br />**{lockname}** |
| dependsOn |아니요 |리소스 이름 또는 리소스 고유 식별자의 쉼표로 구분된 목록입니다.<br /><br />이 잠금에 따라 달라지는 리소스 컬렉션입니다. 잠그려는 리소스가 동일한 템플릿에서 배포되면 해당 리소스 이름을 이 요소에 포함하여 리소스가 먼저 배포되도록 합니다. |
| properties |예 |잠금 유형을 식별하고 잠금에 대해 설명하는 개체입니다.<br /><br />[properties 개체](#properties-object)를 참조하세요. |

### <a name="properties-object"></a>properties 개체
| 이름 | 필수 | 설명 |
| --- | --- | --- |
| level |예 |범위에 적용되는 잠금 유형입니다.<br /><br />**CannotDelete** - 사용자가 리소스를 수정할 수 있지만 삭제할 수 없습니다.<br />**ReadOnly** - 사용자가 리소스에서 읽을 수 있지만 해당 리소스를 삭제하거나 어떤 작업을 수행할 수 없습니다. |
| 정보 |아니요 |잠금에 대한 설명입니다. 최대 512자까지 가능합니다. |

## <a name="how-to-use-the-lock-resource"></a>잠금 리소스를 사용하는 방법
이 리소스를 템플릿에 추가하여 리소스에서 지정된 작업을 수행하지 못하도록 할 수 있습니다. 모든 사용자 및 그룹에 잠금을 적용합니다.

관리 잠금을 만들거나 삭제하려면 **Microsoft.Authorization/*** 또는 **Microsoft.Authorization/locks/*** 작업에 대한 액세스 권한이 있어야 합니다. 기본 제공 역할의 경우 **소유자** 및 **사용자 액세스 관리자**에게만 이러한 작업의 권한이 부여됩니다. 역할 기반 액세스 제어에 대한 자세한 내용은 [Azure 역할 기반 Access Control](../active-directory/role-based-access-control-configure.md)을 참조하세요.

지정된 리소스 및 모든 하위 리소스에 잠금을 적용합니다.

PowerShell 명령 **Remove-AzureRmResourceLock** 또는 REST API의 [삭제 작업](https://msdn.microsoft.com/library/azure/mt204562.aspx) 으로 잠금을 제거할 수 있습니다.

## <a name="examples"></a>예
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

## <a name="next-steps"></a>다음 단계
* 템플릿 구조에 대한 자세한 내용은 [Azure 리소스 관리자 템플릿 작성](resource-group-authoring-templates.md)을 참조하세요.
* 잠금에 대한 자세한 내용은 [Azure 리소스 관리자를 사용하여 리소스 잠그기](resource-group-lock-resources.md)를 참조하세요.




<!--HONumber=Nov16_HO3-->


