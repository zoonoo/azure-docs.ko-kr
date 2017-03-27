---
title: "Azure PowerShell 스크립트 샘플 - 웹앱 만들기 및 GitHub의 코드 배포 | Microsoft Docs"
description: "Azure PowerShell 스크립트 샘플 - 웹앱 만들기 및 GitHub의 코드 배포"
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: 0f9c8bc5-3789-4eb3-8deb-ae6e2200795a
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: article
ms.date: 03/08/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 97acd09d223e59fbf4109bc8a20a25a2ed8ea366
ms.openlocfilehash: 7ee385060bbb3800e8a37f2ae4ba7d06fb31b521
ms.lasthandoff: 03/10/2017

---

# <a name="create-a-web-app-and-deploy-code-from-github"></a>웹앱 만들기 및 GitHub의 코드 배포

이 샘플 스크립트는 관련된 리소스를 사용하여 App Service에서 웹앱을 만든 다음 연속 배포를 사용하지 않고 공용 GitHub 리포지토리에서 웹앱 코드를 배포합니다. 연속 배포를 사용하는 GitHub 배포는 [GitHub의 연속 배포를 사용하여 웹앱 만들기](app-service-powershell-continuous-deployment-github.md)를 참조하세요.

이 스크립트를 실행하기 전에 `Login-AzureRmAccount` cmdlet을 사용하여 Azure와 연결이 생성되었는지 확인하고 배포할 GitHub 리포지토리의 URL이 있는지 확인합니다.

## <a name="sample-script"></a>샘플 스크립트

[!code-powershell[기본](../../../powershell_scripts/app-service/deploy-github/deploy-github.ps1?highlight=1-2 "웹앱 만들기 및 GitHub의 코드 배포")]

## <a name="clean-up-deployment"></a>배포 정리 

스크립트 샘플을 실행한 후에는 다음 명령을 사용하여 리소스 그룹, 웹앱 및 모든 관련된 리소스를 제거할 수 있습니다.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용합니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 참고 사항 |
|---|---|
| [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/resourcemanager/AzureRM.Resources/v3.5.0/new-azurermresourcegroup) | 모든 리소스가 저장되는 리소스 그룹을 만듭니다. |
| [New-AzureRmAppServicePlan](https://docs.microsoft.com/powershell/resourcemanager/azurerm.websites/v2.5.0/new-azurermappserviceplan) | App Service 계획을 만듭니다. |
| [New-AzureRmWebApp](https://docs.microsoft.com/powershell/resourcemanager/azurerm.websites/v2.5.0/new-azurermwebapp) | 웹앱을 만듭니다. |
| [Set-AzureRmResource](https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.5.0/set-azurermresource) | 리소스 그룹에서 리소스를 수정합니다. |

## <a name="next-steps"></a>다음 단계

Azure PowerShell 모듈에 대한 자세한 내용은 [Azure PowerShell 설명서](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/)를 참조하세요.

Azure App Service Web Apps에 대한 추가 Azure PowerShell 샘플은 [Azure PowerShell 샘플](../app-service-powershell-samples.md)에서 확인할 수 있습니다.

