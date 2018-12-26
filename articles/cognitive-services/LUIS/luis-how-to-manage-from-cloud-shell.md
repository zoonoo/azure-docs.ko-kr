---
title: Azure Cloud Shell의 LUIS 사용량 데이터
titleSuffix: Azure Cognitive Services
description: Azure Cloud Shell에서 LUIS에 대한 사용량 정보를 가져오는 방법을 알아봅니다.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 11/27/2018
ms.author: diberry
ms.openlocfilehash: aca81bd529fe6151dce7aba6754874355189fc59
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/28/2018
ms.locfileid: "52444395"
---
# <a name="usage-data-for-luis-service-from-azure-cloud-shell"></a>Azure Cloud Shell의 LUIS 서비스 사용량 데이터
Azure Portal에서 PowerShell cmdlet을 사용하여 LUIS 리소스 작업을 수행할 수 있습니다. 

이러한 cmdlet을 사용하여 LUIS 구독을 [만들고](https://docs.microsoft.com/powershell/module/azurerm.cognitiveservices/new-azurermcognitiveservicesaccount?view=azurermps-6.0.0), [사용량](https://docs.microsoft.com/powershell/module/azurerm.cognitiveservices/get-azurermcognitiveservicesaccountusage?view=azurermps-6.0.0)을 비롯한 구독 정보를 가져오고, 구독을 [제거](https://docs.microsoft.com/powershell/module/azurerm.cognitiveservices/remove-azurermcognitiveservicesaccount?view=azurermps-6.0.0)할 수 있습니다. 

## <a name="cloud-shell-storage-account-and-authentication"></a>Cloud Shell 저장소 계정 및 인증
Azure Portal [Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/quickstart-powershell)에서 PowerShell을 사용하려면 Azure Storage 계정이 있어야 합니다. [저장소 계정](https://docs.microsoft.com/azure/cloud-shell/persisting-shell-storage)이 없는 경우 계정을 만들라는 메시지가 표시됩니다. 저장소 계정을 사용하면 Cloud Shell에 PowerShell 스크립트를 저장할 수 있습니다.  

또한 리소스에 액세스하려면 Cloud Shell에서 Azure에 인증해야 합니다. 

저장소 계정이 있고 인증되면 PowerShell cmdlet을 실행할 수 있습니다.

## <a name="open-cloud-shell"></a>Cloud Shell 열기
Azure Portal Cloud Shell을 사용하는 경우 항상 최신 PowerShell 버전이 있습니다. 

**Cloud Shell 시작** 단추를 사용하여 Cloud Shell을 열거나, 브라우저에서 [https://shell.azure.com](https://shell.azure.com)을 엽니다. 환경으로 Power Shell을 선택합니다. Azure Storage 계정이 없는 경우 계정을 만들어야 합니다. 

<a style="cursor:pointer" onclick='javascript:window.open("https://shell.azure.com", "_blank", "toolbar=no,scrollbars=yes,resizable=yes,menubar=no,location=no,status=no")'><image src="https://shell.azure.com/images/launchcloudshell.png" /></a>

## <a name="luis-endpoint-usage-information"></a>LUIS 엔드포인트 사용량 정보

PowerShell 6.x cmdlet인 `Get-AzureRmCognitiveServicesAccountUsage`는 LUIS를 비롯한 Microsoft Cognitive Services에 대한 사용량 정보를 제공합니다. [Get-AzureRmCognitiveServicesAccountUsage](https://docs.microsoft.com/powershell/module/azurerm.cognitiveservices/get-azurermcognitiveservicesaccountusage?view=azurermps-6.0.0)에는 서비스의 리소스 그룹 및 리소스 이름이 필요합니다. 

명령 구문은 다음과 같습니다.

```
Get-AzureRmCognitiveServicesAccountUsage -ResourceGroupName my-resource-group -Name my-luis-service-name
```

다음 예제에서 리소스 그룹 이름은 `luis-westus-rg`이고, LUIS 서비스 구독 이름은 `luis-westus-1`입니다. 이러한 이름은 둘 다 LUIS 서비스를 만들 때 선택됩니다. 

cmdlet은 6월 7일에 종료된 30일 기간 동안 10,000개의 엔드포인트 적중 중 16개가 사용되었다는 사용량 정보를 반환합니다.

```
CurrentValue  : 16
Name          : LUIS.Calls
Limit         : 10000
Status        : Included
Unit          : Count
QuotaPeriod   : 30.00:00:00
NextResetTime : 2018-06-07T18:28:52Z
```

Cloud Shell과 연결된 Azure Storage 계정에 이 명령을 PowerShell 파일 *.ps1로 저장하고 언제든지 실행합니다. 

![저장소에서 스크립트 실행](./media/luis-how-to-manage-from-powershell/run-script-from-storage.png)

스크립트가 클라우드 드라이브에 저장되면 Azure 휴대폰 앱의 Cloud Shell에서 PowerShell 스크립트를 실행할 수 있습니다. 

![휴대폰 앱의 저장소에서 스크립트 실행](./media/luis-how-to-manage-from-powershell/phone-app.png)