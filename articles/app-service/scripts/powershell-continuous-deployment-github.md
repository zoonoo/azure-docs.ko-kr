---
title: Azure PowerShell 스크립트 샘플 - GitHub 지속적인 배포를 사용하여 앱 만들기 | Microsoft Docs
description: Azure PowerShell 스크립트 샘플 - GitHub의 연속 배포를 사용하여 웹앱 만들기
services: app-service\web
documentationcenter: ''
author: cephalin
manager: erikre
editor: ''
tags: azure-service-management
ms.assetid: 42f901f8-02f7-4869-b22d-d99ef59f874c
ms.service: app-service-web
ms.workload: web
ms.topic: sample
ms.date: 03/20/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: bc8be33ede80070b8e9928e01d07b6066f1c887c
ms.sourcegitcommit: 8e1fb03a9c3ad0fc3fd4d6c111598aa74e0b9bd4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70113568"
---
# <a name="create-a-web-app-with-continuous-deployment-from-github"></a>GitHub의 연속 배포를 사용하여 웹앱 만들기

이 샘플 스크립트는 관련된 리소스를 사용하여 App Service에서 웹앱을 만든 다음, GitHub 리포지토리의 [연속 배포](../deploy-continuous-deployment.md)를 설정합니다. 연속 배포를 사용하지 않는 GitHub 배포는 [웹앱 만들기 및 GitHub의 코드 배포](powershell-deploy-github.md)를 참조하세요.

필요한 경우 [Azure PowerShell 가이드](/powershell/azure/overview)에 있는 지침을 사용하여 Azure PowerShell을 설치한 다음, `Connect-AzAccount`를 실행하여 Azure에 연결합니다. 또한 다음 사항을 확인합니다.

- 애플리케이션 코드는 사용자가 소유한 공용 또는 프라이빗 GitHub 리포지토리에 있습니다. 자동 빌드를 가져오려면 [리포지토리 준비](../deploy-continuous-deployment.md#prepare-your-repository) 테이블에 따라 리포지토리를 구성합니다.
- [GitHub 계정에서 개인용 액세스 토큰을 만들](https://help.github.com/en/articles/creating-a-personal-access-token-for-the-command-line)었습니다.

## <a name="sample-script"></a>샘플 스크립트

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/deploy-github-continuous/deploy-github-continuous.ps1?highlight=1-2 "Create a web app with continuous deployment from GitHub")]

## <a name="clean-up-deployment"></a>배포 정리 

스크립트 샘플을 실행한 후에는 다음 명령을 사용하여 리소스 그룹, 웹앱 및 모든 관련된 리소스를 제거할 수 있습니다.

```powershell
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용합니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | 모든 리소스가 저장되는 리소스 그룹을 만듭니다. |
| [New-AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) | App Service 계획을 만듭니다. |
| [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) | 웹앱을 만듭니다. |
| [Set-AzResource](/powershell/module/az.resources/set-azresource) | 리소스 그룹에서 리소스를 수정합니다. |

## <a name="next-steps"></a>다음 단계

Azure PowerShell 모듈에 대한 자세한 내용은 [Azure PowerShell 설명서](/powershell/azure/overview)를 참조하세요.

Azure App Service Web Apps에 대한 추가 Azure PowerShell 샘플은 [Azure PowerShell 샘플](../samples-powershell.md)에서 확인할 수 있습니다.
