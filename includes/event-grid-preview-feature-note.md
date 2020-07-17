---
title: 포함 파일
description: 포함 파일
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: include
ms.date: 11/06/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: d32beb2d799a60cb9c5be061c39e4ec834da8dcf
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/26/2020
ms.locfileid: "66814737"
---
이 기능은 미리 보기 상태입니다. 이 기능을 사용하려면 미리 보기 확장 또는 모듈을 설치해야 합니다.

### <a name="install-extension-for-azure-cli"></a>Azure CLI 확장 설치

Azure CLI의 경우 [Event Grid 확장](/cli/azure/azure-cli-extensions-list)이 필요합니다.

[CloudShell](/azure/cloud-shell/quickstart)에서:

* 이전에 확장을 설치한 경우 `az extension update -n eventgrid`로 업데이트합니다.
* 이전에 확장을 설치하지 않은 경우 `az extension add -n eventgrid`로 업데이트합니다.

로컬 설치의 경우:

1. [Azure CLI를 설치합니다](/cli/azure/install-azure-cli). `az --version`으로 확인하여 최신 버전이 있는지 알아봅니다.
1. 이전 버전의 확장 `az extension remove -n eventgrid`를 제거합니다.
1. `az extension add -n eventgrid`를 사용하여 `eventgrid` 확장을 설치합니다.

### <a name="install-module-for-powershell"></a>PowerShell 모듈 설치

PowerShell의 경우 [AzureRM.EventGrid 모듈](https://www.powershellgallery.com/packages/AzureRM.EventGrid/0.4.1-preview)이 필요합니다.

[CloudShell](/azure/cloud-shell/quickstart-powershell)에서:

* 모듈 설치 `Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`

로컬 설치의 경우:

1. PowerShell 콘솔을 관리자로 열기
1. 모듈 설치 `Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`

`-AllowPrerelease` 매개 변수를 사용할 수 없는 경우 다음 단계를 사용합니다.

1. `Install-Module PowerShellGet -Force`을 실행합니다.
1. `Update-Module PowerShellGet`을 실행합니다.
1. PowerShell 콘솔 닫기
1. PowerShell을 관리자로 다시 시작
1. 모듈 설치 `Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`
