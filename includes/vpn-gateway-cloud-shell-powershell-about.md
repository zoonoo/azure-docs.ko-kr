---
title: 포함 파일
description: 포함 파일
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 06/04/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: f6af5e5e42a16548b1997845ea5076ddd4dd3be5
ms.sourcegitcommit: 1aefdf876c95bf6c07b12eb8c5fab98e92948000
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/06/2019
ms.locfileid: "66735919"
---
이 문서에서는 PowerShell cmdlet을 사용합니다. Cmdlet을 실행 하려면 Azure Cloud Shell을 Azure에서 호스팅되며 브라우저를 통해 사용 되는 대화형 셸 환경을 사용할 수 있습니다. 미리 설치 된 Azure PowerShell cmdlet을 사용 하 여 azure Cloud Shell 제공 됩니다.

Azure Cloud Shell에서이 문서에 포함 된 모든 코드를 실행 하려면 Cloud Shell 세션을 열고, 사용 합니다 **복사본** 코드를 복사 하 여 사용 하 여 Cloud Shell 세션에 붙여 넣습니다 코드 블록에서 단추 __Ctrl + Shift + V__ 에서 Windows 및 Linux에서 또는 __Cmd + Shift + V__ macOS에서. 붙여넣은 텍스트 자동으로 실행 되지 않습니다, 따라서 키를 눌러 **Enter** 코드를 실행 합니다.

사용 하 여 Azure Cloud Shell을 시작할 수 있습니다.

|  |   |
|-----------------------------------------------|---|
| 코드 블록의 오른쪽 위 모서리에서 **사용**을 선택합니다. 이렇게 __하지__ 자동으로 Cloud Shell에 텍스트를 복사 합니다. | ![Azure Cloud Shell에 대한 사용 예제](./media/cloud-shell-try-it/cli-try-it.png) |
| 오픈 [shell.azure.com](https://shell.azure.com) 브라우저에서 합니다. | [![Azure Cloud Shell 단추 시작](./media/cloud-shell-try-it/launchcloudshell.png)](https://shell.azure.com) |
| [Azure Portal](https://portal.azure.com) 오른쪽 위에 있는 메뉴에서 **Cloud Shell** 단추를 선택합니다. | ![Azure Portal의 Cloud Shell 단추](./media/cloud-shell-try-it/cloud-shell-menu.png) |

**PowerShell을 로컬로 실행**

또한 설치 하 고 컴퓨터에 Azure PowerShell cmdlet을 로컬로 실행할 수 있습니다. PowerShell cmdlet은 자주 업데이트 됩니다. 최신 버전을 실행 하지 않는 경우 지침에 지정 된 값이 실패할 수 있습니다. 로컬로 실행 하는 PowerShell의 버전을 확인 하려면 사용 된 `Get-Module -ListAvailable Az` cmdlet. 를 설치 하거나 업데이트를 참조 하세요 [Azure PowerShell 모듈을 설치](/powershell/azure/install-az-ps)합니다.
