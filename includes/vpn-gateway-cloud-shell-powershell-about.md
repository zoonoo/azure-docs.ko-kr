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
ms.openlocfilehash: 01358d13d30358a9950cbe35622df065fc5a6de5
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67133367"
---
이 문서에서는 PowerShell cmdlet을 사용합니다. cmdlet을 실행하려면 Azure Cloud Shell을 사용하면 됩니다. Azure Cloud Shell은 Azure에서 호스팅되고 브라우저를 통해 사용되는 대화형 셸 환경입니다. Azure Cloud Shell에는 Azure PowerShell cmdlet이 미리 설치되어 있습니다.

Azure Cloud Shell에서 이 문서에 포함된 코드를 실행하려면 Cloud Shell 세션을 열고 코드 블록의 **복사** 단추를 사용하여 코드를 복사한 다음, Windows 및 Linux에서는 __Ctrl+Shift+V__, macOS에서는 __Cmd+Shift+V__를 사용하여 Cloud Shell 세션에 붙여 넣습니다. 붙여넣은 텍스트는 자동으로 실행되지 않으므로, **Enter** 키를 눌러 코드를 실행합니다.

다음을 수행하여 Azure Cloud Shell을 시작할 수 있습니다.

|  |   |
|-----------------------------------------------|---|
| 코드 블록의 오른쪽 위 모서리에서 **사용**을 선택합니다. 이것으로 Cloud Shell로 텍스트가 자동 복사되는 것은 __아닙니다__. | ![Azure Cloud Shell에 대한 사용 예제](./media/cloud-shell-try-it/cli-try-it.png) |
| 브라우저에서 [shell.azure.com](https://shell.azure.com)을 엽니다. | [![Azure Cloud Shell 단추 시작](./media/cloud-shell-try-it/launchcloudshell.png)](https://shell.azure.com) |
| [Azure Portal](https://portal.azure.com) 오른쪽 위에 있는 메뉴에서 **Cloud Shell** 단추를 선택합니다. | ![Azure Portal의 Cloud Shell 단추](./media/cloud-shell-try-it/cloud-shell-menu.png) |

**PowerShell을 로컬로 실행**

또한 설치 하 고 컴퓨터에 Azure PowerShell cmdlet을 로컬로 실행할 수 있습니다. PowerShell cmdlet은 자주 업데이트 됩니다. 최신 버전을 실행 하지 않는 경우 지침에 지정 된 값이 실패할 수 있습니다. 사용 하 여 컴퓨터에 설치 된 Azure PowerShell의 버전을 찾으려면는 `Get-Module -ListAvailable Az` cmdlet. 를 설치 하거나 업데이트를 참조 하세요 [Azure PowerShell 모듈을 설치](/powershell/azure/install-az-ps)합니다.
