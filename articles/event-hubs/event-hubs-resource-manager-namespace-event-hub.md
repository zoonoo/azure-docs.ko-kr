---
title: 소비자 그룹을 사용하여 이벤트 허브 만들기 - Azure Event Hubs | Microsoft Docs
description: Azure Resource Manager 템플릿을 사용하여 이벤트 허브 및 소비자 그룹이 있는 Event Hubs 네임스페이스 만들기
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: 28bb4591-1fd7-444f-a327-4e67e8878798
ms.service: event-hubs
ms.devlang: tbd
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 06/14/2019
ms.author: shvija
ms.openlocfilehash: 007e016672f8548956b37b961805183a504d6bf0
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/17/2019
ms.locfileid: "67154080"
---
# <a name="quickstart-create-an-event-hub-by-using-an-azure-resource-manager-template"></a>빠른 시작: Azure Resource Manager 템플릿을 사용 하 여 이벤트 허브 만들기

Azure Event Hubs는 초당 수백만 개의 이벤트를 수신하여 처리할 수 있는 빅 데이터 스트리밍 플랫폼이자 이벤트 수집 서비스입니다. Event Hubs는 분산된 소프트웨어와 장치에서 생성된 이벤트, 데이터 또는 원격 분석을 처리하고 저장할 수 있습니다. Event Hub로 전송된 데이터는 실시간 분석 공급자 또는 일괄 처리/저장소 어댑터를 사용하여 변환하고 저장할 수 있습니다. Event Hubs에 대한 자세한 개요는 [Event Hubs 개요](event-hubs-about.md) 및 [Event Hubs 기능](event-hubs-features.md)을 참조하세요.

이 빠른 시작에서 만든 이벤트 허브를 사용 하 여는 [Azure Resource Manager 템플릿을](../azure-resource-manager/resource-group-overview.md)합니다. 형식의 네임 스페이스를 만드는 Azure Resource Manager 템플릿을 배포할 [Event Hubs](event-hubs-what-is-event-hubs.md), 하나의 이벤트 허브가 있는 합니다. 또한 어떤 리소스를 배포할지 정의하는 방법 및 배포를 실행할 때 매개 변수를 지정하는 방법을 설명합니다. 배포를 위해 이 템플릿을 사용하거나 요구 사항에 맞게 사용자 지정을 할 수 있습니다. 템플릿을 만드는 더 자세한 내용은 [Azure Resource Manager 템플릿 작성][Authoring Azure Resource Manager templates]을 참조하세요. 템플릿에서 사용할 JSON 구문 및 속성은 [Microsoft.EventHub 리소스 종류](/azure/templates/microsoft.eventhub/allversions)를 참조하세요.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="create-an-event-hub"></a>이벤트 허브 만들기

이 빠른 시작에서 사용 하는 [기존 Resource Manager 템플릿을](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/101-eventhubs-create-namespace-and-eventhub/azuredeploy.json)합니다. 자세한 템플릿 샘플을 참조 하세요 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/?term=eventhub&pageNumber=1&sort=Popular)합니다.

에 템플릿을 배포 합니다.

1. 선택 **사용해** 에서 다음 코드 블록을 및 Azure Cloud shell에 로그인 하 고 지침을 따릅니다.

   ```azurepowershell-interactive
   $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
   $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
   $resourceGroupName = "${projectName}rg"
   $templateUri = "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/101-eventhubs-create-namespace-and-eventhub/azuredeploy.json"

   New-AzResourceGroup -Name $resourceGroupName -Location $location
   New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -projectName $projectName

   Write-Host "Press [ENTER] to continue ..."
   ```

   이벤트 허브를 만들려면 몇 분 정도 걸립니다.

1. **복사**를 선택하여 PowerShell 스크립트를 복사합니다.
1. 셸 콘솔을 마우스 오른쪽 단추로 클릭 한 다음 선택 **붙여넣기**합니다.

## <a name="verify-the-deployment"></a>배포 확인

배포를 확인 하거나 열면에서 리소스 그룹을 [Azure portal](https://portal.azure.com), 또는 다음 Azure PowerShell 스크립트를 사용 합니다.  Cloud shell가 계속 열려 있으면 첫 번째 줄 (읽기-호스트)에 대 한 복사/실행 필요가 없습니다.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that you used in the last procedure"
$resourceGroupName = "${projectName}rg"
$namespaceName = "${projectName}ns"

Get-AzEventHub -ResourceGroupName $resourceGroupName -Namespace $namespaceName

Write-Host "Press [ENTER] to continue ..."
```

## <a name="clean-up-resources"></a>리소스 정리

Azure 리소스가 더 이상 필요하지 않은 경우 리소스 그룹을 삭제하여 배포한 리소스를 정리합니다. Cloud shell가 계속 열려 있으면 첫 번째 줄 (읽기-호스트)에 대 한 복사/실행 필요가 없습니다.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that you used in the last procedure"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -ResourceGroupName $resourceGroupName

Write-Host "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>다음 단계

이 문서는 Event Hubs 네임 스페이스 및 이벤트 허브 네임 스페이스에서를 만들었습니다. 이벤트 허브에서 이벤트를 보내거나 받기 위한 단계별 지침은 **이벤트 보내기 및 받기** 자습서를 참조하세요.

- [.NET Core](event-hubs-dotnet-standard-getstarted-send.md)
- [.NET Framework](event-hubs-dotnet-framework-getstarted-send.md)
- [Java](event-hubs-java-get-started-send.md)
- [Python](event-hubs-python-get-started-send.md)
- [Node.JS](event-hubs-node-get-started-send.md)
- [Go](event-hubs-go-get-started-send.md)
- [C(보내기 전용)](event-hubs-c-getstarted-send.md)
- [Apache Storm(받기 전용)](event-hubs-storm-getstarted-receive.md)


[3]: ./media/event-hubs-quickstart-powershell/sender1.png
[4]: ./media/event-hubs-quickstart-powershell/receiver1.png
[5]: ./media/event-hubs-quickstart-powershell/metrics.png

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/resource-group-authoring-templates.md
[Azure Quickstart Templates]:  https://azure.microsoft.com/documentation/templates/?term=event+hubs
[Using Azure PowerShell with Azure Resource Manager]: ../powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../xplat-cli-azure-resource-manager.md
[Event hub and consumer group template]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-event-hubs-create-event-hub-and-consumer-group/
