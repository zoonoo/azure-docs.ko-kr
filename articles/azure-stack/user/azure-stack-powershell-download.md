---
title: GitHub에서 Azure Stack 도구 다운로드 | Microsoft Docs
description: Azure Stack과 함께 작동 하는 데 필요한 도구를 다운로드 하는 방법에 알아봅니다.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2018
ms.author: mabrigg
ms.openlocfilehash: 9c1e4abe50b06db58a4ca05a99e1ae4a531b2294
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/20/2018
ms.locfileid: "39187454"
---
# <a name="download-azure-stack-tools-from-github"></a>GitHub에서 Azure Stack 도구 다운로드

*적용 대상: Azure Stack 통합 시스템 및 Azure Stack 개발 키트*

Azurestack의 경우 도구는 관리 및 Azure Stack에 리소스를 배포 하는 데 사용할 수 있는 PowerShell 모듈을 호스팅하는 GitHub 리포지토리입니다.

## <a name="download-targets"></a>대상 다운로드

다운로드 하 고 Azure Stack 개발 키트에 이러한 PowerShell 모듈을 사용할 수 있습니다 또는 VPN 연결을 사용 하는 Windows 기반 외부 클라이언트를 합니다.

## <a name="how-to-get-the-tools"></a>도구를 가져오는 방법

이러한 도구를 얻으려면 azurestack의 경우 도구 GitHub 리포지토리를 복제 하거나 AzureStack 도구 폴더에서 다음 스크립트를 실행 하 여 다운로드:

```PowerShell
# Change directory to the root directory
cd \

# Download the tools archive
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
invoke-webrequest `
  https://github.com/Azure/AzureStack-Tools/archive/master.zip `
  -OutFile master.zip

# Expand the downloaded files
expand-archive master.zip `
  -DestinationPath . `
  -Force

# Change to the tools directory
cd AzureStack-Tools-master

```

## <a name="functionalities-provided-by-the-modules"></a>모듈에 의해 제공 되는 기능

AzureStack 도구 저장소에는 Azure Stack에 대 한 다음과 같은 기능을 지 원하는 PowerShell 모듈이 포함 됩니다.

| 기능 | 설명 | 이 모듈을 사용할 수는? |
| --- | --- | --- |
| [클라우드 기능](https://github.com/Azure/AzureStack-Tools/tree/master/CloudCapabilities) | 이 모듈을 클라우드의 클라우드 기능을 사용 합니다. 예를 들어이 모듈을 사용 하 여 Azure 클라우드 및 Azure Stack에 대 한 API 버전, Azure Resource Manager 리소스, VM 확장 등과 같은 클라우드 기능을 가져올 수 있습니다. | 클라우드 관리자 및 사용자입니다. |
| [Azure Stack에 대 한 resource Manager 정책](azure-stack-policy-module.md) | Azure Stack으로 동일한 버전 관리 및 서비스 가용성을 사용 하 여 Azure 구독 또는 Azure 리소스 그룹을 구성 하려면이 모듈을 사용 합니다. | 클라우드 관리자 및 사용자 |
| [Azure Stack에 연결](azure-stack-connect-azure-stack.md) | 이 모듈을 사용 하 여 PowerShell 통해 Azure Stack 인스턴스에 연결 하 고 Azure Stack에 VPN 연결을 구성 합니다. | 클라우드 관리자 및 사용자 |
| [템플릿 유효성 검사기](azure-stack-validate-templates.md) | 이 모듈을 사용 하 여 Azure Stack에 기존 또는 새 템플릿을 배포 하는 경우 확인 합니다. | 클라우드 관리자 및 사용자 |

## <a name="next-steps"></a>다음 단계

- [Azure Stack 사용자의 PowerShell 환경 구성](azure-stack-powershell-configure-user.md)
- [VPN을 통해 Azure Stack 개발 키트에 연결](azure-stack-connect-azure-stack.md)
