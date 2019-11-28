---
title: Az Powershell을 사용하도록 Visual Studio의 템플릿 배포 스크립트 업데이트
description: AzureRM에서 Az Powershell로 Visual Studio 템플릿 배포 스크립트 업데이트
author: cweining
ms.topic: quickstart
ms.date: 09/27/2019
ms.author: cweining
ms.openlocfilehash: c34cde5593b48c301826be3dd2641dc2490ee88d
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74149056"
---
# <a name="updating-the-visual-studio-template-deployment-script-to-use-az-powershell"></a>Az Powershell을 사용하도록 Visual Studio 템플릿 배포 스크립트 업데이트

Visual Studio 16.4는 템플릿 배포 스크립트에서 Az Powershell 사용을 지원합니다. Visual Studio는 Az Powershell을 설치하거나 리소스 그룹 프로젝트에서 배포 스크립트를 자동으로 업데이트하지 않습니다. 최신 Az Powershell을 사용하려면 다음 네 가지 작업을 수행해야 합니다.
1. AzureRM Powershell 제거
1. Az Powershell 설치
1. Visual Studio 16.4로 업데이트
1. 프로젝트의 배포 스크립트를 업데이트합니다.

## <a name="uninstall-azurerm-powershell"></a>AzureRM Powershell 제거
AzureRM Powershell을 제거하려면 다음 [지침](https://docs.microsoft.com/powershell/azure/uninstall-az-ps?view=azps-2.7.0#uninstall-the-azurerm-module)을 따르세요.

## <a name="install-az-powershell"></a>Az Powershell 설치
Az Powershell을 설치하려면 다음 [지침](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-2.7.0)을 따르세요.

## <a name="update-visual-studio-to-164"></a>16.4로 Visual Studio 업데이트
Visual Studio 16.4을 사용할 수 있는 경우 업데이트합니다. 업그레이드하는 동안 Azure Powershell 구성 요소가 선택되지 않았는지 확인합니다. 갤러리를 통해 Az Powershell을 설치한 후에는 Visual Studio와 함께 설치되는 AzureRM 버전의 Powershell을 사용하지 않으려고 합니다.

이미 16.4로 업그레이드했으며 Azure Powershell 구성 요소가 선택된 경우 Visual Studio 설치 관리자를 실행하고 Azure Workload 또는 개별 구성 요소 페이지에서 Azure Powershell 구성 요소를 선택 취소하여 제거할 수 있습니다.

## <a name="update-the-deployment-script-in-your-project"></a>프로젝트의 배포 스크립트 업데이트
배포 스크립트에서 ‘AzureRm’ 문자열의 모든 항목을 ‘Az’로 바꿉니다. 이 [gist](https://gist.github.com/cweining/d2da2479418ea403499c4306dcf4f619)의 수정 버전을 참조하여 변경 내용을 볼 수 있습니다. 스크립트를 Az Powershell로 업그레이드하는 방법에 대한 자세한 내용은 이 [설명서](https://docs.microsoft.com/powershell/azure/migrate-from-azurerm-to-az?view=azps-2.5.0)를 참조하세요.


