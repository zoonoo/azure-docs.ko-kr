---
title: "Azure RemoteApp 컬렉션의 개별 사용자에게 응용 프로그램 게시(미리 보기) | Microsoft Docs"
description: "Azure RemoteApp에서 그룹 대신 개별 사용자에게 앱을 게시할 수 있는 방법에 대해 알아봅니다."
services: remoteapp-preview
documentationcenter: 
author: piotrci
manager: mbaldwin
editor: 
ms.assetid: 1fd0539d-fa65-4ea5-a98e-0be0cf580690
ms.service: remoteapp
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: compute
ms.date: 11/23/2016
ms.author: piotrci
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 6dcadbfb99d4d111ab9ddde9d74db65b5542a8f5


---
# <a name="publish-applications-to-individual-users-in-an-azure-remoteapp-collection-preview"></a>Azure RemoteApp 컬렉션의 개별 사용자에게 응용 프로그램 게시(미리 보기)
> [!IMPORTANT]
> Azure RemoteApp은 중단될 예정입니다. 자세한 내용은 [알림](https://go.microsoft.com/fwlink/?linkid=821148) 을 읽어보세요.
> 
> 

이 문서에서는 Azure RemoteApp 컬렉션의 개별 사용자에게 응용 프로그램을 게시하는 방법을 설명합니다. Azure RemoteApp의 새로운 기능은 현재 비공개 미리 보기 상태이며 평가 목적으로 선택된 초기 채택자만이 사용할 수 있습니다.

원래 Azure RemoteApp을 사용하면 응용 프로그램을 게시하는 한 가지 방법만이 가능합니다. 관리자는 이미지에서 앱을 게시하며 컬렉션의 모든 사용자에게 표시됩니다.

일반적인 시나리오는 단일 이미지에 많은 응용 프로그램을 포함하고 관리 비용을 줄이기 위해 하나의 컬렉션을 배포합니다. 종종 일부 응용 프로그램은 일부 사용자와 관련이 없습니다. 관리자는 개별 사용자가 응용 프로그램 피드에서 불필요한 응용 프로그램을 확인하지 못하도록 개별 사용자에게 앱을 게시하려 합니다.

이제 Azure RemoteApp에서 현재 제한된 미리 보기 기능으로 가능합니다. 다음은 새로운 기능의 간략한 요약입니다.

1. 컬렉션은 두 가지 모드 중 하나로 설정할 수 있습니다.
   
   * 원래 컬렉션 모드에서는 컬렉션의 모든 사용자가 게시된 응용 프로그램을 모두 볼 수 있습니다. 이것이 기본 모드입니다.
   * 새로운 응용 프로그램 모드에서는 사용자가 명시적으로 할당된 응용 프로그램만을 볼 수 있습니다.
2. 현재 응용 프로그램 모드는 Azure RemoteApp PowerShell cmdlet을 사용하여 사용할 수 있습니다.
   
   * 응용 프로그램 모드로 설정하면 컬렉션의 사용자 할당은 Azure 포털을 통해 관리될 수 없습니다. 사용자 할당은 PowerShell cmdlet을 통해 관리될 수 있습니다.
3. 사용자는 자신에게 직접 게시된 응용 프로그램만을 봅니다. 그러나 사용자가 운영 체제에 직접 액세스하여 이미지에서 사용할 수 있는 다른 응용 프로그램을 시작할 수 있습니다.
   
   * 이 기능은 응용 프로그램의 보안 잠금 기능을 제공하지 않으며 응용 프로그램 피드에서 표시 유형을 제한합니다.
   * 응용 프로그램에서 사용자를 격리해야 하는 경우 이에 대한 별도 컬렉션을 사용해야 합니다.

## <a name="how-to-get-azure-remoteapp-powershell-cmdlets"></a>Azure RemoteApp PowerShell cmdlet을 가져오는 방법
새 미리 보기 기능을 실행하려면 Azure PowerShell cmdlet을 사용해야 합니다. 현재 Azure 관리 포털을 사용하여 새 응용 프로그램 게시 모드를 사용하도록 설정할 수 없습니다.

우선 [Azure PowerShell 모듈](/powershell/azureps-cmdlets-docs) 을 설치했는지 확인합니다.

그런 다음 관리자 모드에서 PowerShell 콘솔을 시작하고 다음 cmdlet을 실행합니다.

        Add-AzureAccount

Azure 사용자 이름 및 암호를 묻는 메시지가 나타납니다. 로그인하면 Azure 구독에 대해 Azure RemoteApp cmdlet을 실행할 수 있습니다.

## <a name="how-to-check-which-mode-a-collection-is-in"></a>컬렉션이 있는 모드를 확인하는 방법
다음 cmdlet을 실행합니다.

        Get-AzureRemoteAppCollection <collectionName>

![컬렉션 모드를 확인합니다.](./media/remoteapp-perapp/araacllelvel.png)

AclLevel 속성은 다음 값을 가질 수 있습니다.

* 컬렉션: 원래 게시 모드입니다. 모든 사용자는 게시된 앱을 모두 볼 수 있습니다.
* 응용 프로그램: 새 게시 모드입니다. 사용자는 자신에게 직접 게시된 앱만 볼 수 있습니다.

## <a name="how-to-switch-to-application-publishing-mode"></a>응용 프로그램 게시 모드로 전환하는 방법
다음 cmdlet을 실행합니다.

        Set-AzureRemoteAppCollection -CollectionName -AclLevel Application

응용 프로그램 게시 상태를 그대로 유지합니다. 처음에 모든 사용자는 원래 게시된 앱을 모두 볼 수 있습니다.

## <a name="how-to-list-users-who-can-see-a-specific-application"></a>특정 응용 프로그램을 볼 수 있는 사용자를 나열하는 방법
다음 cmdlet을 실행합니다.

        Get-AzureRemoteAppUser -CollectionName <collectionName> -Alias <appAlias>

응용 프로그램을 볼 수 있는 모든 사용자를 나열합니다.

참고: Get-AzureRemoteAppProgram -CollectionName <collectionName>을 실행하여 응용 프로그램 별칭(위의 구문에서 "앱 별칭"이라고 함)을 볼 수 있습니다.

## <a name="how-to-assign-an-application-to-a-user"></a>사용자에게 응용 프로그램을 할당하는 방법
다음 cmdlet을 실행합니다.

        Add-AzureRemoteAppUser -CollectionName <collectionName> -UserUpn <user@domain.com> -Type <OrgId|MicrosoftAccount> -Alias <appAlias>

이제 사용자가 Azure RemoteApp 클라이언트에서 응용 프로그램을 보고 여기에 연결할 수 있습니다.

## <a name="how-to-remove-an-application-from-a-user"></a>사용자의 응용 프로그램을 제거하는 방법
다음 cmdlet을 실행합니다.

        Remove-AzureRemoteAppUser -CollectionName <collectionName> -UserUpn <user@domain.com> -Type <OrgId|MicrosoftAccount> -Alias <appAlias>

## <a name="providing-feedback"></a>사용자 의견 제공
이 미리 보기 기능에 대한 제안과 의견에 감사합니다. [설문 조사](http://www.instant.ly/s/FDdrb) 를 입력하여 의견을 알려주세요.

## <a name="havent-had-a-chance-to-try-the-preview-feature"></a>미리 보기 기능을 시도해 본 적이 있나요?
미리 보기에 아직 참여하지 않은 경우 이 [설문 조사](http://www.instant.ly/s/AY83p) 를 사용하여 액세스를 요청하세요.




<!--HONumber=Dec16_HO2-->


