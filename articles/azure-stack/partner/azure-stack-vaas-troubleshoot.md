---
title: 서비스로 Azure Stack 유효성 검사 문제 해결 | Microsoft Docs
description: Azure Stack에 대 한 서비스와 유효성 검사 문제를 해결 합니다.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/26/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 11/26/2018
ROBOTS: NOINDEX
ms.openlocfilehash: a6fea6962c5e8864398cd80635d997407cf1d22e
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55250302"
---
# <a name="troubleshoot-validation-as-a-service"></a>서비스 유효성 검사 문제 해결

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

관련이 없는 소프트웨어 릴리스 및 해결 하는 일반적인 문제는 다음과 같습니다.

## <a name="local-agent"></a>로컬 에이전트

### <a name="the-portal-shows-local-agent-in-debug-mode"></a>포털을 디버그 모드에서 로컬 에이전트를 보여 줍니다.

에이전트는 불안정 한 네트워크 연결으로 인해 서비스에 하트 비트를 보낼 수 없기 때문에 가능성이 있습니다. 하트 비트를 5 분 마다 전송 됩니다. 서비스 15 분 동안 하트 비트를 받지 못하면 서비스 에이전트를 비활성으로 간주 하 고 테스트에 더 이상 예약 됩니다. 오류 메시지를 확인 합니다 *Agenthost.log* 파일 디렉터리에 있는 에이전트를 시작 합니다.

> [!Note]
> 에이전트에서 이미 실행 되는 모든 테스트를 실행 하려면 계속 아닌 경우 하트 비트는 테스트 종료 되기 전에 복원 후 에이전트 테스트 상태를 업데이트 하거나 로그 업로드 되지 것입니다. 으로 테스트 항상 표시 **실행** 취소 해야 합니다.

### <a name="agent-process-on-machine-was-shut-down-while-executing-test-what-to-expect"></a>테스트를 실행 하는 동안 컴퓨터에서 에이전트 프로세스가 종료 되었습니다. 고려해야 할 사항

에이전트 프로세스가 종료 된 경우 비정상적으로 예를 들어, 컴퓨터를 다시 시작, 프로세스 종료 (CTRL + C 에이전트 창에서 정상 종료를 간주 됨) 다음에 실행 중이 던 테스트로 표시 계속 됩니다 **실행**합니다. 에이전트를 다시 시작할 경우 에이전트 테스트의 상태를 업데이트할 **취소**합니다. 에이전트 다시 시작 되지 않습니다 경우는 테스트로 표시 되며 **실행** 및 테스트를 수동으로 취소 해야 합니다.

> [!Note]
> 워크플로 내에서 테스트는 순차적으로 실행 되도록 예약 됩니다. **보류 중인** 테스트에서 테스트 될 때까지 실행 되지 것입니다 합니다 **실행** 완료 하 고 같은 워크플로에 상태입니다.

## <a name="vm-images"></a>VM 이미지

### <a name="handle-slow-network-connectivity"></a>저속 네트워크 연결을 처리 합니다.

공유에 로컬 데이터 센터에서 PIR 이미지를 다운로드할 수 있습니다. 및 다음 이미지를 확인할 수 있습니다.

<!-- This is from the appendix to the Deploy local agent topic. -->

#### <a name="download-pir-image-to-local-share-in-case-of-slow-network-traffic"></a>느린 네트워크 트래픽이 발생할 경우 로컬 공유 PIR 이미지 다운로드

1. AzCopy를 다운로드 합니다. [vaasexternaldependencies(AzCopy)](https://vaasexternaldependencies.blob.core.windows.net/prereqcomponents/AzCopy.zip)

2. AzCopy.zip 압축을 풀고 AzCopy.exe를 포함 하는 디렉터리 변경

3. 관리자 권한 프롬프트에서 Windows PowerShell을 엽니다. 다음 명령을 실행합니다.

```PowerShell  
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'Server2016DatacenterFullBYOL.vhd' /NC:12 /V:azcopylog.log /Y
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'Server2016DatacenterCoreBYOL.vhd' /NC:12 /V:azcopylog.log /Y
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'WindowsServer2012R2DatacenterBYOL.vhd' /NC:12 /V:azcopylog.log /Y
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'Ubuntu1404LTS.vhd' /NC:12 /V:azcopylog.log /Y
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'Ubuntu1604-20170619.1.vhd' /NC:12 /V:azcopylog.log /Y
```

> [!Note]  
> LocalFileShare 공유 경로 또는 로컬 경로입니다.

#### <a name="verifying-pir-image-file-hash-value"></a>PIR 이미지 파일 해시 값 검사

사용할 수 있습니다 **Get-HashFile** cmdlet을 이미지 파일 이미지의 무결성을 확인 하려면 다운로드 한 공용 이미지 리포지토리에 대 한 해시 값을 가져옵니다.

| 파일 이름 | SHA256 |
|---------------------------------------|------------------------------------------------------------------|
| Server2016DatacenterFullBYOL.vhd | 6ED58DCA666D530811A1EA563BA509BF9C29182B902D18FCA03C7E0868F733E9 |
| WindowsServer2012R2DatacenterBYOL.vhd | 9792CBF742870B1730B9B16EA814C683A8415EFD7601DDB6D5A76D0964767028 |
| Server2016DatacenterCoreBYOL.vhd | 5E80E1A6721A48A10655E6154C1B90E320DF5558487D6A0D7BFC7DCD32C4D9A5 |
| Ubuntu1404LTS.vhd | B24CDD12352AAEBC612A4558AB9E80F031A2190E46DCB459AF736072742E20E0 |
| Ubuntu1604-20170619.1.vhd | C481B88B60A01CBD5119A3F56632A2203EE5795678D3F3B9B764FFCA885E26CB |

### <a name="failure-occurs-when-uploading-vm-image-in-the-vaasprereq-script"></a>VM 이미지를 업로드 하는 동안 오류가 발생 합니다 `VaaSPreReq` 스크립트

먼저 환경 상태가 정상 인지 확인 합니다.

1. dvm이에서 점프 상자 / 관리자 자격 증명을 사용 하 여 관리 포털 로그인 할 수를 확인 하세요.
1. 알림 또는 경고 없이 되는지 확인 합니다.

환경 정상 상태 이면 수동으로 VaaS 테스트 실행에 필요한 5 VM 이미지를 업로드 합니다.

1. 관리 포털에 서비스 관리자로 로그인 합니다. 관리 포털을 찾을 수 있습니다 ECE 저장소나에서 스탬프 정보 파일의 URL입니다. 자세한 내용은 [환경 매개 변수](azure-stack-vaas-parameters.md#environment-parameters)합니다.
1. 선택 **더 많은 서비스** > **리소스 공급자** > **계산** > **VM 이미지**합니다.
1. 선택 된 **+ 추가** 맨 위에 있는 단추를 **VM 이미지** 블레이드입니다.
1. 수정 하거나 첫 번째 VM 이미지에 대 한 필드의 값을 확인 합니다.
    > [!IMPORTANT]
    > 모든 기본값은 기존 Marketplace 항목에 대 한 올바른입니다.

    | 필드  | 값  |
    |---------|---------|
    | 게시자 | MicrosoftWindowsServer |
    | 제안 | WindowsServer |
    | OS 종류 |  Windows |
    | SKU | 2012-R2-Datacenter |
    | 버전 | 1.0.0 |
    | OS 디스크 Blob URI | https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container/WindowsServer2012R2DatacenterBYOL.vhd |

1. **만들기** 단추를 선택합니다.
1. 나머지 VM 이미지에 대해 반복 합니다.

5 개의 모든 VM 이미지의 속성은 다음과 같습니다.

| 게시자  | 제안  | OS 종류 | SKU | 버전 | OS 디스크 Blob URI |
|---------|---------|---------|---------|---------|---------|
| MicrosoftWindowsServer| WindowsServer |  Windows | 2012-R2-Datacenter | 1.0.0 | https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container/WindowsServer2012R2DatacenterBYOL.vhd |
| MicrosoftWindowsServer | WindowsServer |  Windows | 2016-Datacenter | 1.0.0 | https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container/Server2016DatacenterFullBYOL.vhd |
| MicrosoftWindowsServer | WindowsServer |  Windows | 2016-Datacenter-Server-Core | 1.0.0 | https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container/Server2016DatacenterCoreBYOL.vhd |
| Canonical | UbuntuServer | Linux | 14.04.3-LTS | 1.0.0 | https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container/Ubuntu1404LTS.vhd |
| Canonical | UbuntuServer | Linux | 16.04-LTS | 16.04.20170811 | https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container/Ubuntu1604-20170619.1.vhd |

## <a name="next-steps"></a>다음 단계

- 검토 [서비스로 유효성 검사에 대 한 릴리스 정보](azure-stack-vaas-release-notes.md) 최신 릴리스에서 변경 합니다.