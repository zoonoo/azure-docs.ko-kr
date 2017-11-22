---
title: "Azure Cloud Shell 문제 해결 | Microsoft Docs"
description: "Azure Cloud Shell 문제 해결"
services: azure
documentationcenter: 
author: maertendMSFT
manager: angelc
tags: azure-resource-manager
ms.assetid: 
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 11/2/2017
ms.author: damaerte
ms.openlocfilehash: 59aec1cb6433e90caf683b4bce98c92e9f79fd18
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2017
---
# <a name="troubleshooting-azure-cloud-shell"></a>Azure Cloud Shell 문제 해결

Azure Cloud Shell의 문제에 대해 알려진 해결책은 다음과 같습니다.

## <a name="general-resolutions"></a>일반적인 해결 방법

### <a name="storage-dialog---error-403-requestdisallowedbypolicy"></a>저장소 대화 상자 - 오류: 403 RequestDisallowedByPolicy
- **세부 정보**: Cloud Shell에서 저장소 계정을 만들 경우 관리자가 배치한 Azure 정책 때문에 실패합니다. 오류 메시지에는 다음이 포함됩니다. `The resource action 'Microsoft.Storage/storageAccounts/write' is disallowed by one or more policies.`
- **해결 방법**: Azure 관리자에게 문의하여 저장소 생성을 거부하는 Azure 정책을 제거하거나 업데이트하세요.

### <a name="storage-dialog---error-400-disallowedoperation"></a>저장소 대화 상자 - 오류: 400 DisallowedOperation
 - **세부 정보**: Azure Active Directory 구독을 사용할 때는 저장소를 만들 수 없습니다.
 - **해결 방법**: 저장소 리소스를 만들 수 있는 Azure 구독을 사용하세요. Azure AD 구독으로는 Azure 리소스를 만들 수 없습니다.

### <a name="terminal-output---error-failed-to-connect-terminal-websocket-cannot-be-established-press-enter-to-reconnect"></a>터미널 출력 - 오류: 터미널에 연결할 수 없습니다. websocket을 설정할 수 없습니다. `Enter` 키를 눌러 다시 연결하세요.
 - **세부 정보**: Cloud Shell에는 Cloud Shell 인프라에 대한 websocket 연결을 설정하기 위한 기능이 필요합니다.
 - **해결 방법**: *.console.azure.com의 도메인으로 https 요청 및 websocket 요청을 전송할 수 있게 네트워크 설정을 구성했는지 확인합니다.

## <a name="bash-resolutions"></a>Bash 해결 방법

### <a name="cannot-run-az-login"></a>az 로그인을 실행할 수 없음

- **세부 정보**: Cloud Shell 또는 Azure Portal에 로그인하는 데 사용된 계정으로 이미 인증되어 `az login` 실행이 작동하지 않습니다.
- **해결 방법**: 로그인 또는 로그아웃에 사용되는 계정을 활용하고 원하는 Azure 계정으로 다시 인증합니다.

### <a name="cannot-run-the-docker-daemon"></a>Docker 디먼을 실행할 수 없음

- **세부 정보**: Cloud Shell은 컨테이너를 활용하여 셸 환경을 호스트하므로 결과적으로 디먼 실행이 허용되지 않습니다.
- **해결 방법**: 기본적으로 설치된 [docker-machine](https://docs.docker.com/machine/overview/)을 활용하여 원격 Docker 호스트에서 docker 컨테이너를 관리합니다.

## <a name="powershell-resolutions"></a>PowerShell 해결 방법

### <a name="no-home-directory-persistence"></a>$Home 디렉터리 지속성 없음

- **세부 정보**: 응용 프로그램(예: git, vim 및 기타)이 `$Home`에 쓰는 모든 데이터가 PowerShell 세션 간에 유지되지 않습니다.
- **해결 방법**: PowerShell 프로필에서 `clouddrive`에 있는 응용 프로그램 특정 폴더인 $Home에 대한 바로 가기 링크를 만듭니다.

### <a name="ctrlc-doesnt-exit-out-of-a-cmdlet-prompt"></a>Ctrl+C는 Cmdlet 프롬프트를 종료하지 않습니다.

- **세부 정보**: Cmdlet 프롬프트를 종료하고자 할 때 `Ctrl+C`은 프롬프트를 종료하지 않습니다.
- **해결 방법**: 프롬프트를 종료하려면 `Ctrl+C` 다음 `Enter`를 누릅니다.

### <a name="gui-applications-are-not-supported"></a>GUI 응용 프로그램은 지원되지 않습니다.

- **세부 정보**: 사용자가 GUI 앱을 시작하는 경우 프롬프트는 반환하지 않습니다. 예를 들어 사용자가 2단계 인증을 사용할 수 있는 개인 GitHub 리포지토리를 복제할 경우 2단계 인증을 완료하기 위한 대화 상자가 표시됩니다.
- **해결 방법**: 명령을 종료하려면 `Ctrl+C`을 합니다.

### <a name="get-help--online-does-not-open-the-help-page"></a>Get-help-온라인은 도움말 페이지를 열지 않습니다.

- **세부 정보**: 사용자가 `Get-Help Find-Module -online`을 입력하는 경우 `Starting a browser to display online Help failed. No program or browser is associated to open the URI http://go.microsoft.com/fwlink/?LinkID=398574.`와 같은 오류 메시지를 볼 수 있습니다.
- **해결 방법**: url을 복사하여 브라우저에서 수동으로 엽니다.

### <a name="troubleshooting-remote-management-of-azure-vms"></a>Azure VM의 원격 관리 문제 해결

- **세부 정보**: WinRM에 대한 기본 Windows 방화벽 설정으로 인해 사용자가 다음 오류를 보게 될 수도 있습니다.`Ensure the WinRM service is running. Remote Desktop into the VM for the first time and ensure it can be discovered.`
- **해결 방법**: VM이 실행되고 있는지 확인합니다. VM 상태를 알아보려면 `Get-AzureRmVM -Status`을 실행할 수 있습니다.  다음으로 모든 서브넷에서 WinRM 연결을 허용하도록 원격 VM에 새 방화벽 규칙을 추가합니다. 예를 들어

 ``` Powershell
 New-NetFirewallRule -Name 'WINRM-HTTP-In-TCP-PSCloudShell' -Group 'Windows Remote Management' -Enabled True -Protocol TCP -LocalPort 5985 -Direction Inbound -Action Allow -DisplayName 'Windows Remote Management - PSCloud (HTTP-In)' -Profile Public
 ```
 [Azure 사용자 지정 스크립트 확장](https://docs.microsoft.com/azure/virtual-machines/windows/extensions-customscript)을 사용하면 새 방화벽 규칙을 추가하기 위해 원격 VM에 로그온하는 것을 피할 수 있습니다.
 위의 스크립트를 예를 들어 `addfirerule.ps1`이란 파일에 저장하고, Azure Storage 컨테이너로 업로드할 수 있습니다.
 그런 후에 다음 명령을 시돱니다.

 ``` Powershell
 Get-AzureRmVM -Name MyVM1 -ResourceGroupName MyResourceGroup | Set-AzureRmVMCustomScriptExtension -VMName MyVM1 -FileUri https://mystorageaccount.blob.core.windows.net/mycontainer/addfirerule.ps1 -Run 'addfirerule.ps1' -Name myextension
 ```

### <a name="dir-caches-the-result-in-azure-drive"></a>`dir`은 결과를 Azure 드라이브로 캐시합니다.

- **세부 정보**: `dir`의 결과가 Azure 드라이브로 캐시됩니다.
- **해결**: Azure 드라이브 보기에서 리소스를 만들거나 제거한 후 `dir -force`를 실행하여 업데이트합니다.
