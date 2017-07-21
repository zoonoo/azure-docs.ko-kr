---
title: "템플릿을 사용하여 Azure Event Hubs 네임스페이스를 만들고 캡처를 사용하도록 설정 | Microsoft Docs"
description: "Azure Resource Manager 템플릿을 사용하여 하나의 이벤트 허브가 있는 Azure Event Hubs 네임스페이스를 만들고 캡처를 사용하도록 설정"
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
editor: 
ms.assetid: 8bdda6a2-5ff1-45e3-b696-c553768f1090
ms.service: event-hubs
ms.devlang: tbd
ms.topic: get-started-article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 06/28/2017
ms.author: shvija;sethm
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: f19a3d9b323d75ae23480d0699d55b79bb7d2e84
ms.contentlocale: ko-kr
ms.lasthandoff: 06/28/2017


---
# Azure Resource Manager 템플릿을 사용하여 하나의 이벤트 허브가 있는 Event Hubs 네임스페이스를 만들고 캡처를 사용하도록 설정
<a id="create-an-event-hubs-namespace-with-an-event-hub-and-enable-capture-using-an-azure-resource-manager-template" class="xliff"></a>
이 문서에서는 Azure Resource Manager 템플릿을 사용하여 하나의 이벤트 허브 인스턴스가 있는 Event Hubs 네임스페이스를 만들고 해당 이벤트 허브에서 캡처 기능을 사용하도록 설정하는 방법을 보여 줍니다. 또한 어떤 리소스를 배포할지 정의하는 방법 및 배포를 실행할 때 매개 변수를 지정하는 방법을 설명합니다. 자체 배포를 위해 이 템플릿을 사용하거나 요구 사항에 맞게 사용자 지정할 수 있습니다.

템플릿 만들기에 대한 자세한 내용은 [Azure Resource Manager 템플릿 작성하기][Authoring Azure Resource Manager templates]를 참조하세요.

Azure 리소스 명명 규칙의 사례 및 패턴에 대한 자세한 내용은 [Azure 리소스 명명 규칙][Azure Resources Naming Conventions]을 참조하세요.

전체 템플릿은 GitHub에서 [이벤트 허브 및 캡처 템플릿 사용][Event Hub and enable Capture template]을 참조하세요.

> [!NOTE]
> 최신 템플릿을 확인하려면 [Azure 빠른 시작 템플릿][Azure Quickstart Templates] 갤러리를 방문하여 Event Hubs를 검색하세요.
> 
> 

## 배포할 항목
<a id="what-will-you-deploy" class="xliff"></a>
이 템플릿을 사용하면 하나의 이벤트 허브가 있는 Event Hubs 네임스페이스를 배포하고 [Event Hubs 캡처](event-hubs-capture-overview.md)도 사용할 수 있습니다.

[Event Hubs](event-hubs-what-is-event-hubs.md)는 짧은 대기 시간 및 높은 안정성으로 이벤트 및 원격 분석을 엄청난 규모의 Azure에 제공하는 데 사용되는 이벤트 ingestor 서비스입니다. Event Hubs 캡처를 사용하면 Event Hubs의 스트리밍 데이터를 지정한 시간이나 크기 간격 내에서 선택한 Azure Blob Storage에 자동으로 전달할 수 있습니다.

배포를 자동으로 실행하려면 다음 단추를 클릭합니다.

[![Azure에 배포](./media/event-hubs-resource-manager-namespace-event-hub/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-eventhubs-create-namespace-and-enable-capture%2Fazuredeploy.json)

## 매개 변수
<a id="parameters" class="xliff"></a>
Azure 리소스 관리자와 함께 템플릿을 배포할 때 지정하고자 하는 값으로 매개 변수를 정의합니다. 템플릿은 모든 매개 변수 값이 포함된 `Parameters` 라는 섹션을 포함합니다. 배포하는 프로젝트에 따라 또는 환경에 따라 달라지는 이러한 값에 대한 매개 변수를 정의해야 합니다. 항상 동일하게 유지되는 값으로 매개 변수를 정의하지 마십시오. 각 매개 변수 값은 배포되는 리소스를 정의하는 템플릿에 사용됩니다.

템플릿은 다음 매개 변수를 정의합니다.

### eventHubNamespaceName
<a id="eventhubnamespacename" class="xliff"></a>
만들 Event Hubs 네임스페이스의 이름입니다.

```json
"eventHubNamespaceName":{  
     "type":"string",
     "metadata":{  
         "description":"Name of the EventHub namespace"
      }
}
```

### eventHubName
<a id="eventhubname" class="xliff"></a>
Event Hubs 네임스페이스에서 만든 이벤트 허브의 이름입니다.

```json
"eventHubName":{  
    "type":"string",
    "metadata":{  
        "description":"Name of the event hub"
    }
}
```

### messageRetentionInDays
<a id="messageretentionindays" class="xliff"></a>
이벤트 허브에서 메시지를 보관할 기간(일수)입니다. 

```json
"messageRetentionInDays":{
    "type":"int",
    "defaultValue": 1,
    "minValue":"1",
    "maxValue":"7",
    "metadata":{
       "description":"How long to retain the data in event hub"
     }
 }
```

### partitionCount
<a id="partitioncount" class="xliff"></a>
이벤트 허브에서 만들 파티션 수입니다.

```json
"partitionCount":{
    "type":"int",
    "defaultValue":2,
    "minValue":2,
    "maxValue":32,
    "metadata":{
        "description":"Number of partitions chosen"
    }
 }
```

### captureEnabled
<a id="captureenabled" class="xliff"></a>
이벤트 허브에서 캡처를 사용하도록 설정합니다.

```json
"captureEnabled":{
    "type":"string",
    "defaultValue":"true",
    "allowedValues": [
    "false",
    "true"],
    "metadata":{
        "description":"Enable or disable the Capture for your event hub"
    }
 }
```
### captureEncodingFormat
<a id="captureencodingformat" class="xliff"></a>
이벤트 데이터를 직렬화하기 위해 지정하는 인코딩 형식입니다.

```json
"captureEncodingFormat":{
    "type":"string",
    "defaultValue":"Avro",
    "allowedValues":[
    "Avro"],
    "metadata":{
        "description":"The encoding format in which Capture serializes the EventData"
    }
}
```

### captureTime
<a id="capturetime" class="xliff"></a>
Event Hubs 캡처를 통해 Azure Blob Storage에 데이터를 캡처하기 시작하는 시간 간격입니다.

```json
"captureTime":{
    "type":"int",
    "defaultValue":300,
    "minValue":60,
    "maxValue":900,
    "metadata":{
         "description":"the time window in seconds for the capture"
    }
}
```

### captureSize
<a id="capturesize" class="xliff"></a>
캡처를 통해 Azure Blob Storage에 데이터를 캡처하기 시작하는 크기 간격입니다.

```json
"captureSize":{
    "type":"int",
    "defaultValue":314572800,
    "minValue":10485760,
    "maxValue":524288000,
    "metadata":{
        "description":"The size window in bytes for capture"
    }
}
```

### destinationStorageAccountResourceId
<a id="destinationstorageaccountresourceid" class="xliff"></a>
원하는 Storage 계정에 캡처하도록 설정하기 위해 캡처에 Azure Storage 계정 리소스 ID가 필요합니다.

```json
 "destinationStorageAccountResourceId":{
    "type":"string",
    "metadata":{
        "description":"Your existing Storage account resource id where you want the blobs be captured"
    }
 }
```

### blobContainerName
<a id="blobcontainername" class="xliff"></a>
이벤트 데이터를 캡처할 BLOB 컨테이너입니다.

```json
 "blobContainerName":{
    "type":"string",
    "metadata":{
        "description":"Your existing storage container in which you want the blobs captured"
    }
}
```


### apiVersion
<a id="apiversion" class="xliff"></a>
템플릿의 API 버전입니다.

```json
 "apiVersion":{  
    "type":"string",
    "defaultValue":"2015-08-01",
    "metadata":{  
        "description":"ApiVersion used by the template"
    }
 }
```

## 배포할 리소스
<a id="resources-to-deploy" class="xliff"></a>
하나의 이벤트 허브가 있는 **EventHubs** 형식의 네임스페이스를 만들고 캡처를 사용하도록 설정합니다.

```json
"resources":[  
      {  
         "apiVersion":"[variables('ehVersion')]",
         "name":"[parameters('eventHubNamespaceName')]",
         "type":"Microsoft.EventHub/Namespaces",
         "location":"[variables('location')]",
         "sku":{  
            "name":"Standard",
            "tier":"Standard"
         },
         "resources":[  
            {  
               "apiVersion":"[variables('ehVersion')]",
               "name":"[parameters('eventHubName')]",
               "type":"EventHubs",
               "dependsOn":[  
                  "[concat('Microsoft.EventHub/namespaces/', parameters('eventHubNamespaceName'))]"
               ],
               "properties":{  
                  "path":"[parameters('eventHubName')]",
                  "MessageRetentionInDays":"[parameters('messageRetentionInDays')]",
                  "PartitionCount":"[parameters('partitionCount')]",
                  "CaptureDescription":{
                        "enabled":"[parameters('captureEnabled')]",
                        "encoding":"[parameters('captureEncodingFormat')]",
                        "intervalInSeconds":"[parameters('captureTime')]",
                        "sizeLimitInBytes":"[parameters('captureSize')]",
                        "destination":{
                            "name":"EventHubCapture.AzureBlockBlob",
                            "properties":{
                                "StorageAccountResourceId":"[parameters('destinationStorageAccountResourceId')]",
                                "BlobContainer":"[parameters('blobContainerName')]"
                            }
                        } 
                  }

               }

            }
         ]
      }
   ]
```

## 배포 실행 명령
<a id="commands-to-run-deployment" class="xliff"></a>
[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## PowerShell
<a id="powershell" class="xliff"></a>
```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName \<resource-group-name\> -TemplateFile https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-eventhubs-create-namespace-and-enable-capture/azuredeploy.json
```

## Azure CLI
<a id="azure-cli" class="xliff"></a>
```cli
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri [https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-eventhubs-create-namespace-and-enable-capture/azuredeploy.json][]
```
## 다음 단계
<a id="next-steps" class="xliff"></a>

[Azure Portal](https://portal.azure.com)을 통해 Event Hubs 캡처를 구성할 수도 있습니다. 자세한 내용은 [Azure Portal을 사용하여 Event Hubs 캡처를 사용하도록 설정](event-hubs-capture-enable-through-portal.md)을 참조하세요.

Event Hubs에 대한 자세한 내용은 다음 링크를 참조하세요.

* [Event Hubs 개요](event-hubs-what-is-event-hubs.md)
* [이벤트 허브 만들기](event-hubs-create.md)
* [Event Hubs FAQ](event-hubs-faq.md)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/resource-group-authoring-templates.md
[Azure Quickstart Templates]:  https://azure.microsoft.com/documentation/templates/?term=event+hubs
[Using Azure PowerShell with Azure Resource Manager]: ../powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../xplat-cli-azure-resource-manager.md
[Azure Resources Naming Conventions]: https://azure.microsoft.com/documentation/articles/guidance-naming-conventions/
[Event hub and enable Capture template]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-namespace-and-enable-capture

