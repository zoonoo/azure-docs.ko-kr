---
title: Azure Cloud Shell 개요 | Microsoft Docs
description: Azure Cloud Shell의 개요입니다.
services: ''
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/20/2020
ms.author: damaerte
ms.openlocfilehash: f824bddf833a1e2c01a3b779abc2c5252d8e0547
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89468660"
---
# <a name="overview-of-azure-cloud-shell"></a>Azure Cloud Shell 개요

Azure Cloud Shell은 Azure 리소스를 관리하기 위해 브라우저에서 액세스할 수 있는 인증된 대화형 셸입니다. Bash 또는 PowerShell 중에서 작업 방식에 가장 적합한 셸 환경을 유연하게 선택할 수 있습니다.

다음 세 가지 방법으로 Cloud Shell에 액세스할 수 있습니다.

- **직접 링크**: 브라우저를 엽니다 [https://shell.azure.com](https://shell.azure.com) .

- **Azure Portal**: [Azure Portal](https://portal.azure.com)에서 Cloud Shell 아이콘을 선택 합니다.

    ![Azure Portal에서 Cloud Shell를 시작 하는 아이콘](media/overview/portal-launch-icon.png)

- **코드 조각**: [Docs.microsoft.com]() 및 [Microsoft Learn](/learn/)에서 Azure CLI 및 Azure PowerShell 코드 조각과 함께 표시 되는 **사용해 보기** 단추를 선택 합니다.

    ```azurecli-interactive
    az account show
    ```

    ```azurepowershell-interactive
    Get-AzSubscription
    ```

    **사용해 보기** 단추를 클릭 하면 Bash (Azure CLI 코드 조각) 또는 PowerShell (Azure PowerShell 코드 조각)을 사용 하 여 설명서와 함께 Cloud Shell 열립니다.

    명령을 실행 하려면 코드 조각에서 **복사** 를 사용 하 고, **Ctrl** + **Shift** + **v** (Windows/Linux) 또는 **Cmd** + **shift** + **v** (macos)를 사용 하 여 명령을 붙여넣은 다음 **enter**키를 누릅니다.

## <a name="features"></a>기능

### <a name="browser-based-shell-experience"></a>브라우저 기반 셸 환경

Cloud Shell은 Azure 관리 작업을 사용하여 빌드된 브라우저 기반 명령줄 환경에 액세스할 수 있습니다. Cloud Shell을 활용하여 클라우드가 제공할 수 있는 방식으로 로컬 컴퓨터에서 제한되지 않고 작업할 수 있습니다.

### <a name="choice-of-preferred-shell-experience"></a>기본 설정된 셸 환경 선택

사용자는 Bash 또는 PowerShell 중에서 선택할 수 있습니다.

1. **Cloud Shell**를 선택 합니다.

    ![Cloud Shell 아이콘](media/overview/overview-cloudshell-icon.png)

2. **Bash** 또는 **PowerShell**을 선택 합니다.

    ![Bash 또는 PowerShell 중 하나를 선택 합니다.](media/overview/overview-choices.png)

    처음 시작한 후에는 shell 유형 드롭다운 컨트롤을 사용 하 여 Bash와 PowerShell 사이를 전환할 수 있습니다.

    ![Bash 또는 PowerShell을 선택 하는 드롭다운 컨트롤](media/overview/select-shell-drop-down.png)

### <a name="authenticated-and-configured-azure-workstation"></a>인증 및 구성된 Azure 워크스테이션

Cloud Shell은 Microsoft에서 관리되므로 널리 사용되는 명령줄 도구 및 언어 지원을 제공합니다. 또한 Cloud Shell은 Azure CLI 또는 Azure PowerShell cmdlet을 통해 리소스에 즉시 액세스하도록 자동으로 안전하게 인증합니다.

[Cloud Shell에 설치된 전체 도구 목록](features.md#tools)을 봅니다.

### <a name="integrated-cloud-shell-editor"></a>Cloud Shell 편집기 통합

Cloud Shell은 오픈 소스 Monaco 편집기에 따라 통합된 그래픽 텍스트 편집기를 제공합니다. Azure CLI 또는 Azure PowerShell을 통해 원활하게 배포하기 위해 `code .`를 실행하여 간단하게 구성 파일을 만들고 편집합니다.

[Cloud Shell 편집기에 대해 자세히 알아보세요](using-cloud-shell-editor.md).

### <a name="multiple-access-points"></a>다중 액세스 포인트

Cloud Shell은 다음에서 사용할 수 있는 유연한 도구입니다.

* [portal.azure.com](https://portal.azure.com)
* [shell.azure.com](https://shell.azure.com)
* [Azure CLI 설명서](/cli/azure)
* [Azure PowerShell 설명서](/powershell/azure/)
* [Azure 모바일 앱](https://azure.microsoft.com/features/azure-portal/mobile-app/)
* [Visual Studio Code Azure 계정 확장](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)

### <a name="connect-your-microsoft-azure-files-storage"></a>Microsoft Azure Files 스토리지 연결

Cloud Shell 컴퓨터는 임시 이지만 파일은 디스크 이미지와 이라는 탑재 된 파일 공유를 통해 두 가지 방법으로 유지 `clouddrive` 됩니다. 처음 시작하면 Cloud Shell은 사용자를 대신하여 리소스 그룹, 스토리지 계정 및 Azure Files 공유를 만들라는 메시지를 표시합니다. 이는 일회성 단계이며 모든 세션에서 자동으로 연결됩니다. Azure Cloud Shell의 Bash 및 PowerShell 모두에 의해 단일 파일 공유가 매핑될 수 있으며 사용될 것입니다.

자세한 내용은 [새 또는 기존 저장소 계정을](persisting-shell-storage.md) 탑재 하거나 [Cloud Shell에 사용 되는 지 속성 메커니즘](persisting-shell-storage.md#how-cloud-shell-storage-works)에 대해 알아보는 방법을 알아보세요.

> [!NOTE]
> Azure storage 방화벽은 cloud shell 저장소 계정에 대해 지원 되지 않습니다.

## <a name="concepts"></a>개념

* Cloud Shell은 세션 별, 사용자 단위 기준으로 제공된 임시 호스트에서 실행됩니다.
* Cloud Shell은 대화형 작업 없이 20분 후에 시간이 초과됩니다.
* Cloud Shell은 Azure 파일 공유를 탑재해야 합니다.
* Cloud Shell은 Bash 및 PowerShell 모두에 동일한 Azure 파일 공유를 사용합니다.
* Cloud Shell은 사용자 계정 별로 하나의 컴퓨터를 할당합니다.
* Cloud Shell은 파일 공유에 보관된 5GB 이미지를 사용하여 $Home을 유지합니다.
* 사용 권한은 Bash의 일반적인 Linux 사용자로 설정됩니다.

[Azure Cloud Shell의 Bash](features.md)와 [Azure Cloud Shell의 PowerShell](./features.md)에 들어 있는 기능에 대해 자세히 알아 보세요.

## <a name="pricing"></a>가격 책정

탑재된 Azure Files 공유의 필수 구성 요소를 포함하여 Azure Cloud Shell을 호스트하는 컴퓨터는 추가 비용 없이 사용할 수 있습니다. 일반 스토리지 비용이 적용됩니다.

## <a name="next-steps"></a>다음 단계

[Cloud Shell의 Bash 빠른 시작](quickstart.md) <br>
[Cloud Shell의 PowerShell 빠른 시작](quickstart-powershell.md)