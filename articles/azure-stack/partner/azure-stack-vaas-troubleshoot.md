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
ms.date: 07/24/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: 4372da2f1057c06761dd6abaf18c26df6e33640e
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2018
ms.locfileid: "40235315"
---
# <a name="troubleshoot-validation-as-a-service"></a>서비스 유효성 검사 문제 해결

[!INCLUDE[Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

관련이 없는 소프트웨어 릴리스 및 해결 하는 일반적인 문제는 다음과 같습니다.

## <a name="the-portal-shows-local-agent-in-debug-mode"></a>포털을 디버그 모드에서 로컬 에이전트를 보여 줍니다.

에이전트는 불안정 한 네트워크 연결으로 인해 서비스에 하트 비트를 보낼 수 없기 때문에 가능성이 있습니다. 하트 비트를 5 분 마다 전송 됩니다. 서비스 15 분 동안 하트 비트를 받지 못하면 서비스 에이전트를 비활성으로 간주 하 고 테스트에 더 이상 예약 됩니다. 오류 메시지를 확인 합니다 *Agenthost.log* 파일 디렉터리에 있는 에이전트를 시작 합니다.

> [!Note] 
> 에이전트에서 이미 실행 되는 모든 테스트를 실행 하려면 계속 아닌 경우 하트 비트는 테스트 종료 되기 전에 복원 후 에이전트 테스트 상태를 업데이트 하거나 로그 업로드 되지 것입니다. 으로 테스트 항상 표시 **실행** 취소 해야 합니다.

## <a name="agent-process-on-machine-was-shut-down-while-executing-test-what-to-expect"></a>테스트를 실행 하는 동안 컴퓨터에서 에이전트 프로세스가 종료 되었습니다. 고려해야 할 사항

에이전트 프로세스가 종료 된 경우 비정상적으로 예를 들어, 컴퓨터를 다시 시작, 프로세스 종료 (CTRL + C 에이전트 창에서 정상 종료를 간주 됨) 다음에 실행 중이 던 테스트로 표시 계속 됩니다 **실행**합니다. 에이전트를 다시 시작할 경우 에이전트 테스트의 상태를 업데이트할 **취소**합니다. 에이전트 다시 시작 되지 않습니다 경우는 테스트로 표시 되며 **실행** 및 테스트를 수동으로 취소 해야 합니다

> [!Note] 
> 워크플로 내에서 테스트는 순차적으로 실행 되도록 예약 됩니다. **보류 중인** 테스트에서 테스트 될 때까지 실행 되지 것입니다 합니다 **실행** 완료 하 고 같은 워크플로에 상태입니다.

## <a name="handle-slow-network-connectivity"></a>저속 네트워크 연결을 처리 합니다.

공유에 로컬 데이터 센터에서 PIR 이미지를 다운로드할 수 있습니다. 및 이미지를 확인할 수 있습니다.

<!-- This is from the appendix to the Deploy local agent topic. -->

### <a name="download-pir-image-to-local-share-in-case-of-slow-network-traffic"></a>느린 네트워크 트래픽이 발생할 경우 로컬 공유 PIR 이미지 다운로드

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

### <a name="verifying-pir-image-file-hash-value"></a>PIR 이미지 파일 해시 값 검사

사용할 수 있습니다 **Get-HashFile** cmdlet을 이미지 파일 이미지의 무결성을 확인 하려면 다운로드 한 공용 이미지 리포지토리에 대 한 해시 값을 가져옵니다.

| 파일 이름 | SHA256 |
|---------------------------------------|------------------------------------------------------------------|
| Server2016DatacenterFullBYOL.vhd | 6ED58DCA666D530811A1EA563BA509BF9C29182B902D18FCA03C7E0868F733E9 |
| WindowsServer2012R2DatacenterBYOL.vhd | 9792CBF742870B1730B9B16EA814C683A8415EFD7601DDB6D5A76D0964767028 |
| Server2016DatacenterCoreBYOL.vhd | 5E80E1A6721A48A10655E6154C1B90E320DF5558487D6A0D7BFC7DCD32C4D9A5 |
| Ubuntu1404LTS.vhd | B24CDD12352AAEBC612A4558AB9E80F031A2190E46DCB459AF736072742E20E0 |
| Ubuntu1604 20170619.1.vhd | C481B88B60A01CBD5119A3F56632A2203EE5795678D3F3B9B764FFCA885E26CB |

## <a name="next-steps"></a>다음 단계

- 에 대해 자세히 알아보려면 [서비스로 Azure Stack 유효성 검사](https://docs.microsoft.com/azure/azure-stack/partner)합니다.
