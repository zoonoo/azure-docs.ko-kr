<properties 
	pageTitle="Azure 리소스 관리자에서 리소스 연결" 
	description="Azure 리소스 관리자에서 여러 다른 리소스 그룹의 리소스 간에 링크를 만듭니다." 
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
	ms.date="07/16/2015" 
	ms.author="tomfitz"/>

# Azure 리소스 관리자에서 리소스 연결

배포 후에 리소스 간 관계 또는 링크를 쿼리하려고 할 수 있습니다. 종속성을 통해 배포되었는지 알 수 있지만 해당 수명 주기는 배포에서 끝납니다. 배포가 완료되면 종속 리소스 간에 관계가 식별되지 않습니다.

대신, Azure 리소스 관리자는 리소스 간에 관계를 설정하고 쿼리하기 위해 리소스 연결이라는 새로운 기능을 제공합니다. 한 리소스로 연결되는 리소스 또는 한 리소스로부터 연결되는 리소스를 확인할 수 있습니다.

리소스 링크의 범위는 구독, 리소스 그룹 또는 특정 리소스일 수 있습니다. 즉, 리소스 링크는 여러 리소스 그룹에 걸쳐 있는 관계를 문서화할 수 있습니다. 솔루션을 여러 템플릿 및 여러 리소스 그룹으로 분해할 때 이러한 리소스 링크를 식별하는 메커니즘이 있으면 유용합니다. 예를 들어 자체 수명 주기를 갖는 데이터베이스를 하나의 리소스 그룹에 두고 다른 주기를 갖는 앱을 다른 리소스 그룹에 두는 것이 일반적입니다. 앱은 데이터베이스에 연결되므로 여러 다른 리소스 그룹의 리소스 간에 링크가 존재합니다.

연결된 모든 리소스는 동일한 구독에 속해야 합니다. 각 리소스는 다른 50개의 리소스에 연결될 수 있습니다. 연결된 리소스 중 하나가 삭제되거나 이동되면 링크 소유자는 나머지 링크를 청정리해야 합니다.

## 템플릿 안에서 연결

아래 예제에서는 웹 사이트, 알림 허브 및 SQL 데이터베이스에 대한 단방향 관계 집합으로 "Microsoft.AppService/apiapps" 형식의 리소스를 만드는 템플릿을 보여 줍니다.

    {
        "$schema": "http://schemas.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "$system": {
                "type": "Object"
            }
        },
        "resources": [
            {
                "apiVersion": "2014-11-01",
                "type": "Microsoft.Web/sites",
                "name": "[parameters('$system').siteName]",
                "location": "[parameters('$system').location]",
                "resources": [
                    {
                        "apiVersion": "2014-11-01",
                        "name": "appsettings",
                        "type": "config",
                        "dependsOn": [
                            "[resourceId('Microsoft.NotificationHubs/namespaces/NotificationHubs', variables('notificationHubNamespace'), variables('notificationHubName'))]"
                        ],
                        "properties": {
                            "MS_MobileServiceName": "[parameters('$system').apiAppName]",
                            "MS_NotificationHubName": "[variables('notificationHubName')]",
                            "MS_NotificationHubConnectionString": "[listkeys(resourceId('Microsoft.NotificationHubs/namespaces/notificationHubs/authorizationRules', variables('notificationHubNamespace'), variables('notificationHubName'), 'DefaultFullSharedAccessSignature'), '2014-09-01').primaryConnectionString]"
                        }
                    }
                ]
            },
            {
                "apiVersion": "[parameters('$system').apiVersion]",
                "type": "Microsoft.AppService/apiapps",
                "name": "[parameters('$system').apiAppName]",
                "properties": {
                    "accessLevel": "PublicAnonymous"
                },
                "resources": [
                    {
                        "apiVersion": "2015-01-01",
                        "type": "providers/links",
                        "name": "Microsoft.Resources/mobile-codesite",
                        "dependsOn": [
                            "[resourceId('Microsoft.AppService/apiapps', parameters('$system').apiAppName)]",
                            "[resourceId('Microsoft.Web/Sites', variables('userSiteName'))]"
                        ],
                        "properties": {
                            "targetId": "[resourceId('Microsoft.Web/sites', variables('userSiteName'))]"
                        }
                    },
                    {
                        "apiVersion": "2015-01-01",
                        "type": "providers/links",
                        "name": "Microsoft.Resources/mobile-notificationhub",
                        "dependsOn": [
                            "[resourceId('Microsoft.AppService/apiapps', parameters('$system').apiAppName)]",
                            "[resourceId('Microsoft.NotificationHubs/namespaces/NotificationHubs', variables('notificationHubNamespace'), variables('notificationHubName'))]"
                        ],
                        "properties": {
                            "targetId": "[resourceId('Microsoft.NotificationHubs/namespaces/NotificationHubs', variables('notificationHubNamespace'), variables('notificationHubName'))]"
                        }
                    },
                    {
                        "apiVersion": "2015-01-01",
                        "type": "providers/links",
                        "name": "Microsoft.Resources/mobile-sqlserver",
                        "dependsOn": [
                            "[resourceId('Microsoft.AppService/apiapps', parameters('$system').apiAppName)]"
                        ],
                        "properties": {
                            "targetId": "[concat('/subscriptions/', parameters('userDatabase').subscriptionId, '/resourcegroups/', parameters('userDatabase').resourceGroupName, '/providers/Microsoft.Sql/servers/', parameters('userDatabase').serverName)]"
                        }
                    },
                    {
                        "apiVersion": "2015-01-01",
                        "type": "providers/links",
                        "name": "Microsoft.Resources/mobile-sqldb",
                        "dependsOn": [
                            "[resourceId('Microsoft.AppService/apiapps', parameters('$system').apiAppName)]"
                        ],
                        "properties": {
                            "targetId": "[concat('/subscriptions/', parameters('userDatabase').subscriptionId, '/resourcegroups/', parameters('userDatabase').resourceGroupName, '/providers/Microsoft.Sql/servers/', parameters('userDatabase').serverName, '/databases/', parameters('userDatabase').databaseName)]"
                        }
                    }
                ]
            }
        ]
    }

## REST API를 사용하여 연결

배포된 리소스 간 링크를 정의하려면 다음을 실행합니다.

    PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/{provider-namespace}/{resource-type}/{resource-name}/providers/Microsoft.Resources/links/{link-name}?api-version={api-version}

{subscription-id}를 구독 ID로 바꿉니다. {resource-group}, {provider-namespace, {resource-type} 및 {resource-name}을 링크의 첫 번째 리소스를 식별하는 값으로 바꿉니다. {link-name}을 만들려는 링크의 이름으로 바꿉니다. api-version으로 2015-01-01을 사용합니다.

요청에서 다음과 같이 링크의 두 번째 리소스를 정의하는 개체를 포함합니다.

    {
        "name": "{new-link-name}",
        "properties": {
            "targetId": "subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/{provider-namespace}/{resource-type}/{resource-name}/",
            "notes": "{link-description}",
        }
    }

properties 요소는 두 번째 리소스에 대한 식별자를 포함합니다.

링크에 대한 정보를 검색하는 방법을 비롯한 추가 예제를 보려면 [연결된 리소스](https://msdn.microsoft.com/library/azure/mt238499.aspx)를 참조하세요.

## 다음 단계

- 태그를 사용하여 리소스를 구성할 수도 있습니다. 리소스에 태그를 지정하는 방법에 대한 자세한 내용은 [태그를 사용하여 리소스 구성](resource-group-using-tags.md)을 참조하세요.
- 템플릿을 만들고 배포할 리소스를 정의하는 방법에 대한 설명을 보려면 [템플릿 작성](resource-group-authoring-templates.md)을 참조하세요.

<!---HONumber=July15_HO4-->