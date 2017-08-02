---
title: "Azure RemoteApp에서 PowerShell cmdlet 사용 | Microsoft 문서"
description: "Azure RemoteApp에서 Windows PowerShell cmdlet을 사용하는 방법을 알아봅니다."
services: remoteapp
documentationcenter: 
author: guscatalano
manager: mbaldwin
editor: 
ms.assetid: 7d3d5ded-6f73-4de6-a8ac-c1d622e842a2
ms.service: remoteapp
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: compute
ms.date: 04/26/2017
ms.author: mbaldwin
ms.translationtype: HT
ms.sourcegitcommit: 2812039649f7d2fb0705220854e4d8d0a031d31e
ms.openlocfilehash: e6338dc283b5a26f20a467df9582181381967931
ms.contentlocale: ko-kr
ms.lasthandoff: 07/22/2017

---
# <a name="use-windows-powershell-cmdlets-with-azure-remoteapp"></a>Azure RemoteApp에서 Windows PowerShell cmdlet 사용
> [!IMPORTANT]
> Azure RemoteApp은 2017년 8월 31일에 중단되었습니다. 자세한 내용은 [알림](https://go.microsoft.com/fwlink/?linkid=821148) 을 읽어보세요.
> 
> 

 Azure RemoteApp PowerShell cmdlet을 사용하여 컬렉션을 유지 관리할 수 있습니다. 시작하려면 다음 정보를 사용합니다.

## <a name="get-the-cmdlets"></a>cmdlet 받기
- - -
먼저 RemoteApp cmdlet이 포함되어 있는 Azure Powershell cmdlet을 [여기](http://go.microsoft.com/?linkid=9811175)서 다운로드합니다. 

[Azure RemoteApp cmdlet 도움말](/powershell/module/azure?view=azuresmps-3.7.0)을 확인하세요.

## <a name="configure-azure-cmdlets-to-use-your-subscription"></a>구독을 사용하도록 Azure cmdlet 구성
- - -
Azure 구독에 대해 cmdlet을 사용할 수 있도록 [이 가이드](/powershell/azure/overview) 를 따릅니다.

다음 단계를 사용하여 빠르게 시작할 수 있습니다.

1. [Azure PowerShell cmdlet](http://go.microsoft.com/?linkid=9811175)을 다운로드하여 설치합니다.
2. Microsoft Azure PowerShell을 시작합니다.
3. **Add-AzureAccount** 를 실행하여 Azure 구독을 인증합니다. 메시지가 나타나면 Azure 포털에 로그인하는 데 사용하는 것과 동일한 사용자 이름 및 암호를 입력합니다.  
4. **Get-AzureSubscription** 을 실행하여 사용자 계정과 연경된 구독을 나열합니다. 
5. **Select-AzureSubscription -SubscriptionName &lt;구독 이름&gt;** 또는 **Select-AzureSubscription -SubscriptionId &lt;구독 ID&gt;**를 실행하여 사용할 구독을 지정합니다.

축하합니다. Azure PowerShell 콘솔이 사용할 수 있도록 구성되었습니다. Azure PowerShell 콘솔을 시작할 때마다 2~5단계를 반복해야 합니다.  


## <a name="list-all-collections"></a>모든 컬렉션 나열
- - -
     Get-AzureRemoteAppCollection

## <a name="delete-a-collection"></a>컬렉션 삭제
- - -
    Remove-AzureRemoteAppCollection <enter collection name>

예제: `Remove-AzureRemoteAppCollection ContosoProduction`.

## <a name="create-a-cloud-collection"></a>클라우드 컬렉션 만들기
- - -
간단히 다음 명령을 수행하면 됩니다.

    New-AzureRemoteAppCollection -Collectionname RAppO365Col1 -ImageName "Office 365 ProPlus (Subscription required)" -Plan Basic -Location "West US" - Description "Office 365 Collection."

위의 명령은 Microsoft Office 365 응용 프로그램(Excel, OneNote, Outlook, PowerPoint, Visio, Word)을 자동으로 게시합니다.

컬렉션을 만드는 데 30분 이상 걸릴 수 있습니다. 따라서 이 명령은 다음과 같이 사용할 수 있는 추적 ID를 반환합니다.

    Get-AzureRemoteAppOperationResult -TrackingId xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx

컬렉션이 완료되면 다음 명령을 사용하여 컬렉션에 사용자를 추가할 수 있습니다.

    Add-AzureRemoteAppUser -CollectionName RAppO365Col1 -Type microsoftAccount -UserUpn someone@domain.com

완료되었습니다! 사용자는 [여기에](https://www.remoteapp.windowsazure.com/)있는 Azure RemoteApp 클라이언트를 사용하여 응용 프로그램에 연결할 수 있습니다.

## <a name="available-cmdlets"></a>사용할 수 있는 cmdlet
설명서에서는 일부만 다루지만 사용할 수 있는 많은 명령이 있습니다.

기본 RemoteApp 컬렉션 cmdlet: 

* New-AzureRemoteAppCollection
* Get-AzureRemoteAppCollection
* Set-AzureRemoteAppCollection
* Update-AzureRemoteAppCollection
* Remove-AzureRemoteAppCollection
* Add-AzureRemoteAppUser
* Get-AzureRemoteAppUser
* Remove-AzureRemoteAppUser
* Get-AzureRemoteAppSession
* Disconnect-AzureRemoteAppSession
* Invoke-AzureRemoteAppSessionLogoff
* Send-AzureRemoteAppSessionMessage
* Get-AzureRemoteAppProgram
* Get-AzureRemoteAppStartMenuProgram
* Publish-AzureRemoteAppProgram
* Unpublish-AzureRemoteAppProgram
* Get-AzureRemoteAppCollectionUsageDetails
* Get-AzureRemoteAppCollectionUsageSummary
* Get-AzureRemoteAppPlan

RemoteApp 가상 네트워크 cmdlet:

* New-AzureRemoteAppVNet
* Get-AzureRemoteAppVNet
* Set-AzureRemoteAppVNet
* Remove-AzureRemoteAppVNet
* Get-AzureRemoteAppVpnDevice
* Get-- AzureRemoteAppVpnDeviceConfigScript
* Reset-AzureRemoteAppVpnSharedKey

RemoteApp 템플릿 이미지 cmdlet:

* New-AzureRemoteAppTemplateImage
* Get-AzureRemoteAppTemplateImage
* Rename-AzureRemoteAppTemplateImage
* Remove-AzureRemoteAppTemplateImage

기타 RemoteApp cmdlet:

* Get-AzureRemoteAppLocation
* Get-AzureRemoteAppWorkspace
* Set-AzureRemoteAppWorkspace
* Get-AzureRemoteAppOperationResult


