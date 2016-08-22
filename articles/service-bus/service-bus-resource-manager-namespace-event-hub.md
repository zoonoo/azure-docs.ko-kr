<properties
    pageTitle="Azure Resource Manager 템플릿을 사용하여 이벤트 허브 및 소비자 그룹이 있는 이벤트 허브 네임스페이스 만들기 | Microsoft Azure"
    description="Azure Resource Manager 템플릿을 사용하여 이벤트 허브 및 소비자 그룹이 있는 이벤트 허브 네임스페이스 만들기"
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
    ms.date="07/11/2016"
    ms.author="sethm;shvija"/>

# Azure Resource Manager 템플릿을 사용하여 이벤트 허브 및 소비자 그룹이 있는 이벤트 허브 네임스페이스 만들기

이 문서에서는 이벤트 허브 및 소비자 그룹이 있는 이벤트 허브 네임스페이스를 만드는 Azure Resource Manager 템플릿을 사용하는 방법을 보여 줍니다. 어떤 리소스를 배포할지 정의하는 방법 및 배포를 실행할 때 매개 변수를 지정하는 방법을 알게 됩니다. 자체 배포를 위해 이 템플릿을 사용하거나 요구 사항에 맞게 사용자 지정할 수 있습니다.

템플릿을 만들기에 대한 자세한 내용은 [Azure Resource Manager 템플릿 작성][]을 참조하세요.

전체 템플릿은 GitHub에서 [서비스 버스 이벤트 허브 및 소비자 그룹 템플릿][]을 참조하세요.

>[AZURE.NOTE] 다음 Azure Resource Manager 템플릿은 다운로드하여 배포할 수 있습니다.
>
>-    [큐 및 권한 부여 규칙이 있는 서비스 버스 네임스페이스 만들기](service-bus-resource-manager-namespace-auth-rule.md)
>-    [큐가 있는 서비스 버스 네임스페이스 만들기](service-bus-resource-manager-namespace-queue.md)
>-    [토픽 및 구독이 있는 서비스 버스 네임스페이스 만들기](service-bus-resource-manager-namespace-topic.md)
>-    [서비스 버스 네임스페이스 만들기](service-bus-resource-manager-namespace.md)
>
>최신 템플릿을 확인하려면 서비스 버스에 대한 [Azure 빠른 시작 템플릿][] 갤러리 및 검색을 방문하세요.

## 배포할 항목

이 템플릿으로 이벤트 허브 및 소비자 그룹이 있는 이벤트 허브 네임스페이스를 배포합니다.

[이벤트 허브](../event-hubs/event-hubs-what-is-event-hubs.md)는 짧은 대기 시간 및 높은 안정성으로 이벤트 및 원격 분석을 엄청난 규모의 Azure에 제공하는 데 사용되는 이벤트 ingestor 서비스입니다.

배포를 자동으로 실행하려면 다음 단추를 클릭합니다.

[![Azure에 배포](./media/service-bus-resource-manager-namespace-event-hub/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-servicebus-create-eventhub-and-consumergroup%2Fazuredeploy.json)로 바꿉니다.

## 매개 변수

Azure 리소스 관리자와 함께 템플릿을 배포할 때 지정하고자 하는 값으로 매개 변수를 정의합니다. 템플릿은 모든 매개 변수 값이 포함된 `Parameters`라는 섹션을 포함합니다. 배포하는 프로젝트에 따라 또는 환경에 따라 달라지는 이러한 값에 대한 매개 변수를 정의해야 합니다. 항상 동일하게 유지되는 값으로 매개 변수를 정의하지 마십시오. 각 매개 변수 값은 배포되는 리소스를 정의하는 템플릿에 사용됩니다.

템플릿은 다음 매개 변수를 정의합니다.

### eventHubNamespaceName

만들 이벤트 허브 네임스페이스의 이름입니다.

```
"eventHubNamespaceName": {
"type": "string"
}
```

### eventHubName

이벤트 허브 네임스페이스에서 만든 이벤트 허브의 이름입니다.

```
"eventHubName": {
"type": "string"
}
```

### eventHubConsumerGroupName

서비스 버스 네임스페이스에서 이벤트 허브에 대해 만든 소비자 그룹의 이름입니다.

```
"eventHubConsumerGroupName": {
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

이벤트 허브 및 소비자 그룹이 있는 **이벤트 허브** 형식의 서비스 버스 네임스페이스를 만듭니다.

```
"resources": [
        {
            "apiVersion": "[variables('ehVersion')]",
            "name": "[parameters('eventHubNamespaceName')]",
            "type": "Microsoft.EventHub/Namespaces",
            "location": "[variables('location')]",
            "kind": "EventHub",
            "sku": {
                "name": "StandardSku",
                "tier": "Standard"
            },
            "resources": [
                {
                    "apiVersion": "[variables('ehVersion')]",
                    "name": "[parameters('eventHubName')]",
                    "type": "EventHubs",
                    "dependsOn": [
                        "[concat('Microsoft.EventHub/namespaces/', parameters('eventHubNamespaceName'))]"
                    ],
                    "properties": {
                        "path": "[parameters('eventHubName')]"
                    },
                    "resources": [
                        {
                            "apiVersion": "[variables('ehVersion')]",
                            "name": "[parameters('eventHubConsumerGroupName')]",
                            "type": "ConsumerGroups",
                            "dependsOn": [
                                "[parameters('eventHubName')]"
                            ],
                            "properties": {
                            }
                        }
                    ]
                }
            ]
        }
    ]
```

## 배포 실행 명령

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## PowerShell

```
New-AzureRmResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateFile https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-eventhub-and-consumergroup/azuredeploy.json
```

## Azure CLI

```
azure config mode arm

azure group deployment create <my-resource-group> <my-deployment-name> --template-uri [https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-eventhub-and-consumergroup/azuredeploy.json][]
```

## 다음 단계

이제 Azure Resource Manager를 사용하여 리소스를 만들고 배포했으므로 다음 문서를 참조하여 이러한 리소스를 관리하는 방법에 대해 알아봅니다.

- [Azure 자동화를 사용하여 Azure 서비스 버스 관리](service-bus-automation-manage.md)
- [PowerShell로 이벤트 허브 관리](service-bus-powershell-how-to-provision.md)
- [서비스 버스 탐색기로 이벤트 허브 리소스 관리](https://code.msdn.microsoft.com/Service-Bus-Explorer-f2abca5a)

  [Azure Resource Manager 템플릿 작성]: ../resource-group-authoring-templates.md
  [Azure 빠른 시작 템플릿]: https://azure.microsoft.com/documentation/templates/?term=service+bus
  [Using Azure PowerShell with Azure Resource Manager]: ../powershell-azure-resource-manager.md
  [Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../xplat-cli-azure-resource-manager.md
  [서비스 버스 이벤트 허브 및 소비자 그룹 템플릿]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-eventhub-and-consumergroup/

<!---HONumber=AcomDC_0810_2016-->