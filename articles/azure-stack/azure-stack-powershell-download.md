---
title: GitHub에서 Azure Stack 도구 다운로드 | Microsoft Docs
description: Azure Stack을 사용 하 여 작업에 필요한 도구를 다운로드 하는 방법에 알아봅니다.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: article
ms.date: 09/28/2018
ms.author: mabrigg
ms.reviewer: thoroet
ms.openlocfilehash: 5b90ebc554738c89816cf88f8984ffab01c87d4d
ms.sourcegitcommit: f31bfb398430ed7d66a85c7ca1f1cc9943656678
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/28/2018
ms.locfileid: "47451323"
---
# <a name="download-azure-stack-tools-from-github"></a>GitHub에서 Azure Stack 도구 다운로드

*적용 대상: Azure Stack 통합 시스템 및 Azure Stack 개발 키트*

**Azurestack의 경우 도구** 되는 [GitHub 리포지토리](https://github.com/Azure/AzureStack-Tools) 관리 및 Azure Stack에 리소스를 배포 하기 위한 PowerShell 모듈을 호스팅하는 합니다. VPN 연결을 설정 하려는 경우 Azure Stack 개발 키트와 Windows 기반 외부 클라이언트에 이러한 PowerShell 모듈을 다운로드할 수 있습니다. 이러한 도구를 얻으려면 GitHub 리포지토리를 복제 하거나 다운로드 합니다 **azurestack의 경우 도구** 다음 스크립트를 실행 하 여 폴더:

```PowerShell
# Change directory to the root directory. 
cd \

# Download the tools archive.
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12 
invoke-webrequest `
  https://github.com/Azure/AzureStack-Tools/archive/master.zip `
  -OutFile master.zip

# Expand the downloaded files.
expand-archive master.zip `
  -DestinationPath . `
  -Force

# Change to the tools directory.
cd AzureStack-Tools-master

```

## <a name="functionality-provided-by-the-modules"></a>모듈에서 제공 하는 기능

합니다 **azurestack의 경우 도구** 리포지토리는 Azure Stack에 대 한 다음과 같은 기능을 지 원하는 PowerShell 모듈을 포함 합니다.  

| 기능 | 설명 | 이 모듈을 사용할 수는? |
| --- | --- | --- |
| [클라우드 기능](user/azure-stack-validate-templates.md) | 이 모듈을 클라우드의 클라우드 기능을 사용 합니다. 예를 들어이 모듈을 사용 하 여 API 버전 및 Azure Resource Manager 리소스와 같은 클라우드 기능을 가져올 수 있습니다. 또한이 모듈을 사용 하 여 Azure Stack 및 Azure 클라우드에 대 한 VM 확장을 가져올 수 있습니다. | 클라우드 운영자 및 사용자 |
| [Azure Stack에 대 한 resource Manager 정책](user/azure-stack-policy-module.md) | Azure Stack으로 동일한 버전 관리 및 서비스 가용성을 사용 하 여 Azure 구독 또는 Azure 리소스 그룹을 구성 하려면이 모듈을 사용 합니다. | 클라우드 운영자 및 사용자 |
| [Azure를 사용 하 여 등록](azure-stack-register.md) | 이 모듈을 사용 하 여 Azure를 사용 하 여 개발 키트 인스턴스를 등록 합니다. 를 등록 한 후 Azure에서 마켓플레이스 항목을 다운로드할 수 있으며 Azure Stack에서 사용할 수 있습니다. | 클라우드 운영자 |
| [Azure Stack 배포](azure-stack-run-powershell-script.md) | 이 모듈을 사용 하 여 Azure Stack 호스트 컴퓨터를 배포 하 여 Azure Stack 가상 하드 디스크 (VHD) 이미지를 사용 하 여 다시 배포할 준비를 합니다. | 클라우드 운영자|
| [Azure Stack에 연결](azure-stack-connect-powershell.md) | 이 모듈을 사용 하 여 Azure Stack에 VPN 연결을 구성 합니다. | 클라우드 운영자 및 사용자 |
| [템플릿 유효성 검사기](user/azure-stack-validate-templates.md) | 이 모듈을 사용 하 여 Azure Stack에 기존 또는 새 템플릿을 배포 하는 경우 확인 합니다. | 클라우드 운영자 및 사용자|


## <a name="next-steps"></a>다음 단계
* [Azure Stack 사용자의 PowerShell 환경 구성](user/azure-stack-powershell-configure-user.md)   
* [VPN을 통해 Azure Stack 개발 키트에 연결](azure-stack-connect-azure-stack.md)  
