---
title: Windows에서 Azure CLI 사용 | Microsoft Docs
description: Windows에서 Azure CLI 사용
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 02/14/2017
ms.author: cynthn
ms.openlocfilehash: ece7806dd757ee5169a25ac83590aa4a740d6dcc
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/18/2018
---
# <a name="using-the-azure-cli-on-windows"></a>Windows에서 Azure CLI 사용

Azure CLI(명령줄 인터페이스)는 Azure 리소스를 만들고 관리하는 명령줄 및 스크립팅 환경을 제공합니다. Azure CLI는 macOS, Linux 및 Windows 운영 체제에서 사용할 수 있습니다. 하지만 이러한 운영 체제에서 CLI 명령은 동일하지만 운영 체제 특정 스크립팅 구문은 다를 수 있습니다.

이 문서에서는 Windows에서 Azure CLI를 설치하고 실행할 수 있는 방법 및 각각에 대한 구문 관련 고려 사항을 자세히 설명합니다. Azure CLI 설명서에 대한 자세한 내용은 [Azure CLI 설명서]( https://docs.microsoft.com/cli/azure)를 참조하세요.

## <a name="windows-subsystem-for-linux"></a>Linux용 Windows 하위 시스템

Linux용 Windows 하위 시스템(WSL)는 Windows 10 Anniversary 및 이후 버전에서 Ubuntu Linux 환경을 제공합니다. WSL를 사용하도록 설정하면 네이티브 Bash 환경을 제공합니다. 이 기능은 Azure CLI 스크립트를 만들고 실행하는 데 사용할 수 있습니다. WSL에서 네이티브 Bash 환경을 제공하기 때문에 Azure CLI 스크립트를 수정하지 않고 macOS, Linux 및 Windows 간에 공유할 수 있습니다.

WSL에서 Azure CLI를 사용하려면 다음을 완료합니다.

|Task | 지침 |
|---|---|
| WSL 사용 | [WSL 설명서 설치](https://msdn.microsoft.com/en-us/commandline/wsl/install_guide) |
| Azure CLI 설치 |[WSL/Ubuntu 14.04에서 CLI 설치](https://docs.microsoft.com/cli/azure/install-az-cli2#ubuntu)|

## <a name="powershell"></a>PowerShell

Windows에서 기본적으로 Azure CLI를 실행할 수 있습니다. 이 구성에서는 Windows 운영 체제에서 Azure CLI 패키지를 설치하고 PowerShell의 명령을 실행할 수 있습니다. 이 구성에서는 지원되는 버전의 Windows에서 Azure CLI 명령 및 스크립트를 실행할 수 있지만 플랫폼 지정 스크립트 구문이 필요합니다. 이런 이유로 macOS, Linux 및 Windows 스크립트를 모두 수정하지 않고 공유할 수 있는 것은 아닙니다.

Windows에서 Azure CLI를 사용하려면 [Windows에서 CLI 설치](https://docs.microsoft.com/cli/azure/install-az-cli2#windows)의 지침을 사용하여 패키지를 설치합니다.

## <a name="docker-image"></a>Docker 이미지

Windows용 Docker를 사용하여 Azure CLI를 포함하는 Docker 이미지를 시작할 수 있습니다. 이 이미지는 Linux에서 기반으로 하며 네이티브 Bash 환경을 포함합니다.  Windows용 Docker 및 Azure CLI 이미지를 사용하면 스크립트를 macOS, Linux 및 Windows 간에 공유할 수 있습니다. 

Windows용 Docker에서 Azure CLI를 사용하려면 Windows용 Docker가 실행되고 있는지 확인하고 다음 명령을 실행합니다.

```bash
docker run -it azuresdk/azure-cli-python:latest bash
```

Bash 세션을 완료하면 Azure CLI 도구를 사용하여 미리 로드되기 시작합니다.

## <a name="next-steps"></a>다음 단계

[Azure Virtual Machines에 대한 CLI 샘플](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Azure Web Apps에 대한 CLI 샘플](../../app-service/app-service-cli-samples.md)

[Azure SQL에 대한 CLI 샘플](../../sql-database/sql-database-cli-samples.md)
