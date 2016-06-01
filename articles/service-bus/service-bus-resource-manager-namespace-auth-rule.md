<properties
    pageTitle="Azure Resource Manager 템플릿을 사용하여 서비스 버스 권한 부여 규칙 만들기 | Microsoft Azure"
    description="Azure Resource Manager 템플릿을 사용하여 네임스페이스 및 큐에 대한 서비스 버스 권한 부여 규칙 만들기"
    services="service-bus"
    documentationCenter=".net"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.devlang="tbd"
    ms.topic="article"
    ms.tgt_pltfrm="dotnet"
    ms.workload="na"
    ms.date="04/15/2016"
    ms.author="sethm;shvija"/>

# Azure Resource Manager 템플릿을 사용하여 네임스페이스 및 큐에 대한 서비스 버스 권한 부여 규칙 만들기

이 문서에서는 서비스 버스 네임스페이스 및 큐에 대한 권한 부여 규칙을 만드는 Azure Resource Manager 템플릿을 사용하는 방법을 보여 줍니다. 어떤 리소스를 배포할지 정의하는 방법 및 배포를 실행할 때 매개 변수를 지정하는 방법을 알게 됩니다. 배포를 위해 이 템플릿을 사용하거나 요구 사항에 맞게 사용자 지정을 할 수 있습니다.

템플릿을 만들기에 대한 자세한 내용은 [Azure Resource Manager 템플릿 작성][]을 참조하세요.

전체 템플릿은 GitHub에서 [서비스 버스 인증 규칙 템플릿][]을 참조하세요.

>[AZURE.NOTE] 다음 Azure Resource Manager 템플릿은 다운로드하여 배포할 수 있습니다.
>
>-    [이벤트 허브 및 소비자 그룹이 있는 서비스 버스 네임스페이스 만들기](service-bus-resource-manager-namespace-event-hub.md)
>-    [큐가 있는 서비스 버스 네임스페이스 만들기](service-bus-resource-manager-namespace-queue.md)
>-    [토픽 및 구독이 있는 서비스 버스 네임스페이스 만들기](service-bus-resource-manager-namespace-topic.md)
>-    [서비스 버스 네임스페이스 만들기](service-bus-resource-manager-namespace.md)
>
>최신 템플릿을 확인하려면 서비스 버스에 대한 [Azure 빠른 시작 템플릿][] 및 검색을 참조하세요.

## 배포할 항목

이 템플릿을 사용하여 네임스페이스 및 메시징 엔터티(이 경우에는 큐)에 대한 서비스 버스 권한 부여 규칙을 배포합니다.

이 템플릿은 인증에 SAS(공유 액세스 서명)를 사용합니다. SAS를 사용하면 응용 프로그램을 네임스페이스 또는 특정 권한이 연관된 메시징 엔터티(큐 또는 토픽)에서 구성된 액세스 키를 사용하여 서비스 버스에 인증할 수 있습니다. 그런 다음 이 키를 사용하여 클라이언트가 서비스 버스를 인증하는 데 차례로 사용할 수 있는 SAS 토큰을 생성할 수 있습니다.

배포를 자동으로 실행하려면 다음 단추를 클릭합니다.

[![Azure에 배포](./media/service-bus-resource-manager-namespace-auth-rule/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F301-servicebus-create-authrule-namespace-and-queue%2Fazuredeploy.json)로 바꿉니다.

## 매개 변수

Azure 리소스 관리자와 함께 템플릿을 배포할 때 지정하고자 하는 값으로 매개 변수를 정의합니다. 템플릿은 모든 매개 변수 값이 포함된 `Parameters`라는 섹션을 포함합니다. 배포하는 프로젝트에 따라 또는 환경에 따라 달라지는 이러한 값에 대한 매개 변수를 정의해야 합니다. 항상 동일하게 유지되는 값으로 매개 변수를 정의하지 마십시오. 각 매개 변수 값은 배포되는 리소스를 정의하는 템플릿에 사용됩니다.

템플릿에서 각 매개 변수에 대해 설명할 것입니다.

### serviceBusNamespaceName

만들 서비스 버스 네임스페이스 이름입니다.

```
"serviceBusNamespaceName": {
"type": "string"
}
```

### namespaceAuthorizationRuleName 

네임스페이스에 대한 권한 부여 규칙 이름입니다.

```
"namespaceAuthorizationRuleName ": {
"type": "string"
}
```

### serviceBusQueueName

서비스 버스 네임스페이스에 있는 큐의 이름입니다.

```
"serviceBusQueueName": {
"type": "string"
}
```

### serviceBusApiVersion

템플릿의 서비스 버스 API 버전입니다.

```
"serviceBusApiVersion": {
"type": "string"
}
```

## 배포할 리소스

**메시징** 형식의 표준 서비스 버스 네임스페이스와 네임스페이스 및 엔터티에 대한 서비스 버스 권한 부여 규칙을 만듭니다.

```
"resources": [
        {
            "apiVersion": "[variables('sbVersion')]",
            "name": "[parameters('serviceBusNamespaceName')]",
            "type": "Microsoft.ServiceBus/namespaces",
            "location": "[variables('location')]",
            "kind": "Messaging",
            "sku": {
                "name": "StandardSku",
                "tier": "Standard"
            },
            "resources": [
                {
                    "apiVersion": "[variables('sbVersion')]",
                    "name": "[parameters('serviceBusQueueName')]",
                    "type": "Queues",
                    "dependsOn": [
                        "[concat('Microsoft.ServiceBus/namespaces/', parameters('serviceBusNamespaceName'))]"
                    ],
                    "properties": {
                        "path": "[parameters('serviceBusQueueName')]"
                    },
                    "resources": [
                        {
                            "apiVersion": "[variables('sbVersion')]",
                            "name": "[parameters('queueAuthorizationRuleName')]",
                            "type": "authorizationRules",
                            "dependsOn": [
                                "[parameters('serviceBusQueueName')]"
                            ],
                            "properties": {
                                "Rights": ["Listen"]
                            }
                        }
                    ]
                }
            ]
        }, {
            "apiVersion": "[variables('sbVersion')]",
            "name": "[variables('namespaceAuthRuleName')]",
            "type": "Microsoft.ServiceBus/namespaces/authorizationRules",
            "dependsOn": ["[concat('Microsoft.ServiceBus/namespaces/', parameters('serviceBusNamespaceName'))]"],
            "location": "[resourceGroup().location]",
            "properties": {
                "Rights": ["Send"]
            }
        }
    ]
```

## 배포 실행 명령

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### PowerShell

```
New-AzureRmResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateFile <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/301-servicebus-create-authrule-namespace-and-queue/azuredeploy.json>
```

## Azure CLI

```
azure config mode arm

azure group deployment create <my-resource-group> <my-deployment-name> --template-uri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/301-servicebus-create-authrule-namespace-and-queue/azuredeploy.json>
```

## 다음 단계

이제 Azure Resource Manager를 사용하여 리소스를 만들고 배포했으므로 다음 문서를 참조하여 이러한 리소스를 관리하는 방법에 대해 알아봅니다.

- [Azure 자동화를 사용하여 Azure 서비스 버스 관리](service-bus-automation-manage.md)
- [PowerShell을 사용하여 서비스 버스 관리](service-bus-powershell-how-to-provision.md)
- [서비스 버스 탐색기로 서비스 버스 리소스 관리](https://code.msdn.microsoft.com/Service-Bus-Explorer-f2abca5a)
- [서비스 버스 인증 및 권한 부여](service-bus-authentication-and-authorization.md)

  [Azure Resource Manager 템플릿 작성]: ../resource-group-authoring-templates.md
  [Azure 빠른 시작 템플릿]: https://azure.microsoft.com/documentation/templates/
  [Using Azure PowerShell with Azure Resource Manager]: ../powershell-azure-resource-manager.md
  [Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../xplat-cli-azure-resource-manager.md
  [서비스 버스 인증 규칙 템플릿]: https://github.com/Azure/azure-quickstart-templates/blob/master/301-servicebus-create-authrule-namespace-and-queue/

<!---HONumber=AcomDC_0518_2016-->