<properties
   pageTitle="Azure RemoteApp에서 Powershell 시작하기"
   description="Azure RemoteApp에서 Powershell을 시작하는 방법을 알아봅니다."
   services="remoteapp"
   documentationCenter=""
   authors="guscatalano"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="remoteapp"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="compute"
   ms.date="07/07/2015"
   ms.author="guscatal;spatnaik;elizapo"/>



# Azure RemoteApp에서 Powershell 시작하기
=====================================


## cmdlet 받기 
-------------
먼저 [여기](http://go.microsoft.com/?linkid=9811175)에서 Azure Powershell cmdlet을 받아야 합니다. RemoteApp도 여기에 포함되어 있습니다.

## 구독을 사용하도록 Azure cmdlet 구성
------------------
Azure 구독에 대해 cmdlet을 사용할 수 있도록 [이 가이드](../powershell-install-configure.md)를 따릅니다.

## 클라우드 컬렉션 만들기
--------------------
간단히 다음 명령을 수행하면 됩니다.

    New-AzureRemoteAppCollection -Collectionname RAppO365Col1 -ImageName "Office 365 ProPlus (Subscription required)" -Plan Basic -Location "West US" - Description "Office 365 Collection."

위의 명령은 Microsoft Office 365 응용 프로그램(Excel, OneNote, Outlook, PowerPoint, Visio, Word)을 자동으로 게시합니다.

컬렉션을 만드는 데 30분 이상 걸릴 수 있습니다. 따라서 이 명령은 다음과 같이 사용할 수 있는 추적 ID를 반환합니다.


    Get-AzureRemoteAppOperationResult -TrackingId xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx

컬렉션이 완료되면 다음 명령을 사용하여 컬렉션에 사용자를 추가할 수 있습니다.

    Add-AzureRemoteAppUser -CollectionName RAppO365Col1 -Type microsoftAccount -UserUpn someone@domain.com

완료되었습니다! 사용자는 [여기에](https://www.remoteapp.windowsazure.com/) 있는 Azure RemoteApp 클라이언트를 사용하여 응용 프로그램에 연결할 수 있습니다.

## 사용할 수 있는 cmdlet
설명서에서는 일부만 다루지만 사용할 수 있는 많은 명령이 있습니다.

기본 RemoteApp 컬렉션 cmdlet:

- New-AzureRemoteAppCollection
- Get-AzureRemoteAppCollection
- Set-AzureRemoteAppCollection
- Update-AzureRemoteAppCollection
- Remove-AzureRemoteAppCollection
- Add-AzureRemoteAppUser
- Get-AzureRemoteAppUser
- Remove-AzureRemoteAppUser
- Get-AzureRemoteAppSession
- Disconnect-AzureRemoteAppSession
- Invoke-AzureRemoteAppSessionLogoff
- Send-AzureRemoteAppSessionMessage
- Get-AzureRemoteAppProgram
- Get-AzureRemoteAppStartMenuProgram
- Publish-AzureRemoteAppProgram
- Unpublish-AzureRemoteAppProgram
- Get-AzureRemoteAppCollectionUsageDetails
- Get-AzureRemoteAppCollectionUsageSummary
- Get-AzureRemoteAppPlan

RemoteApp 가상 네트워크 cmdlet:

- New-AzureRemoteAppVNet
- Get-AzureRemoteAppVNet
- Set-AzureRemoteAppVNet
- Remove-AzureRemoteAppVNet
- Get-AzureRemoteAppVpnDevice
- Get-- AzureRemoteAppVpnDeviceConfigScript
- Reset-AzureRemoteAppVpnSharedKey

RemoteApp 템플릿 이미지 cmdlet:

- New-AzureRemoteAppTemplateImage
- Get-AzureRemoteAppTemplateImage
- Rename-AzureRemoteAppTemplateImage
- Remove-AzureRemoteAppTemplateImage

기타 RemoteApp cmdlet:

- Get-AzureRemoteAppLocation
- Get-AzureRemoteAppWorkspace
- Set-AzureRemoteAppWorkspace
- Get-AzureRemoteAppOperationResult
 

<!---HONumber=August15_HO6-->