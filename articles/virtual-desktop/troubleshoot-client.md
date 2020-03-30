---
title: 원격 데스크톱 클라이언트 Windows 가상 데스크톱 문제 해결 - Azure
description: Windows 가상 데스크톱 테넌트 환경에서 클라이언트 연결을 설정할 때 문제를 해결하는 방법
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 12/13/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: e3a240901ffca2c126e2b61eaee0cf287cc31d6e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127498"
---
# <a name="troubleshoot-the-remote-desktop-client"></a>원격 데스크톱 클라이언트 문제 해결

이 문서에서는 원격 데스크톱 클라이언트의 일반적인 문제와 이를 해결하는 방법에 대해 설명합니다.

## <a name="remote-desktop-client-for-windows-7-or-windows-10-stops-responding-or-cannot-be-opened"></a>Windows 7 또는 Windows 10용 원격 데스크톱 클라이언트가 응답을 중지하거나 열 수 없습니다.

다음 PowerShell cmdlet을 사용하여 OOB(대역 외) 클라이언트 레지스트리를 정리합니다.

```PowerShell
Remove-ItemProperty 'HKCU:\Software\Microsoft\Terminal Server Client\Default' - Name FeedURLs

#Remove RdClientRadc registry key
Remove-Item 'HKCU:\Software\Microsoft\RdClientRadc' -Recurse

#Remove all files under %appdata%\RdClientRadc
Remove-Item C:\Users\pavithir\AppData\Roaming\RdClientRadc\* -Recurse
```

**%AppData%\RdClientRadc로** 이동하여 모든 콘텐츠를 삭제합니다.

제거 하 고 윈도 즈에 대 한 원격 데스크톱 클라이언트를 다시 설치 7 그리고 윈도우 10.

## <a name="web-client-wont-open"></a>웹 클라이언트가 열리지 않습니다.

먼저 브라우저에서 다른 웹 사이트를 열어 인터넷 연결을 테스트합니다. 예를 들어, [www.bing.com](https://www.bing.com).

**nslookup을** 사용하여 DNS가 FQDN을 해결할 수 있도록 합니다.

```cmd
nslookup rdweb.wvd.microsoft.com
```

Windows 7 또는 Windows 10용 원격 데스크톱 클라이언트와 같은 다른 클라이언트와 연결하여 웹 클라이언트를 열 수 있는지 확인합니다.

### <a name="opening-another-site-fails"></a>다른 사이트 열기 실패

이는 일반적으로 네트워크 연결 문제 또는 네트워크 중단으로 인해 발생합니다. 네트워크 지원에 문의하는 것이 좋습니다.

### <a name="nslookup-cannot-resolve-the-name"></a>Nslookup이 이름을 확인할 수 없습니다.

이는 일반적으로 네트워크 연결 문제 또는 네트워크 중단으로 인해 발생합니다. 네트워크 지원에 문의하는 것이 좋습니다.

### <a name="your-client-cant-connect-but-other-clients-on-your-network-can-connect"></a>클라이언트는 연결할 수 없지만 네트워크의 다른 클라이언트는 연결할 수 있습니다.

웹 클라이언트를 사용하는 동안 브라우저가 작동을 시작하거나 작동이 중지되는 경우 다음 지침을 따라 문제를 해결합니다.

1. 브라우저를 다시 시작합니다.
2. 브라우저 쿠키를 지웁히 지웁습니다. [인터넷 익스플로러에서 쿠키 파일을 삭제하는 방법을](https://support.microsoft.com/help/278835/how-to-delete-cookie-files-in-internet-explorer)참조하십시오.
3. 브라우저 캐시를 지웁니다. [브라우저의 클리어 브라우저 캐시를](https://binged.it/2RKyfdU)참조하십시오.
4. 개인 모드에서 브라우저를 엽니다.

## <a name="web-client-stops-responding-or-disconnects"></a>웹 클라이언트응답 중지 또는 연결 끊기

다른 브라우저 나 클라이언트를 사용하여 연결해 보십시오.

### <a name="other-browsers-and-clients-also-malfunction-or-fail-to-open"></a>다른 브라우저와 클라이언트도 오작동하거나 열지 못함

브라우저를 전환한 후에도 문제가 계속되면 브라우저가 아닌 네트워크에 문제가 있을 수 있습니다. 네트워크 지원에 문의하는 것이 좋습니다.

## <a name="web-client-keeps-prompting-for-credentials"></a>웹 클라이언트가 자격 증명에 대한 프롬프트를 계속 표시합니다.

웹 클라이언트가 자격 증명을 계속 묻는 경우 다음 지침을 따르십시오.

1. 웹 클라이언트 URL이 올바른지 확인합니다.
2. 사용 중인 자격 증명이 URL에 연결된 Windows 가상 데스크톱 환경에 대한 자격 증명이 있는지 확인합니다.
3. 브라우저 쿠키를 지웁히 지웁습니다. 자세한 내용은 [Internet Explorer에서 쿠키 파일을 삭제하는 방법을](https://support.microsoft.com/help/278835/how-to-delete-cookie-files-in-internet-explorer)참조하십시오.
4. 브라우저 캐시를 지웁니다. 자세한 내용은 [브라우저의 브라우저 캐시 지우기](https://binged.it/2RKyfdU)를 참조하십시오.
5. 비공개 모드에서 브라우저를 엽니다.

## <a name="next-steps"></a>다음 단계

- Windows 가상 데스크톱 및 에스컬레이션 트랙 문제 해결에 대한 개요는 [문제 해결 개요, 피드백 및 지원을](troubleshoot-set-up-overview.md)참조하십시오.
- Windows 가상 데스크톱 환경에서 테넌트 및 호스트 풀을 만드는 동안 문제를 해결하려면 [테넌트 및 호스트 풀 만들기를](troubleshoot-set-up-issues.md)참조하십시오.
- Windows 가상 데스크톱에서 가상 시스템(VM)을 구성하는 동안 문제를 해결하려면 [세션 호스트 가상 시스템 구성을](troubleshoot-vm-configuration.md)참조하십시오.
- Windows 가상 데스크톱에서 PowerShell을 사용할 때 문제를 해결하려면 [Windows 가상 데스크톱 PowerShell](troubleshoot-powershell.md)을 참조하십시오.
- 문제 해결 자습서를 진행하려면 [자습서: 리소스 관리자 템플릿 배포 문제를 해결 합니다.](../azure-resource-manager/templates/template-tutorial-troubleshoot.md)