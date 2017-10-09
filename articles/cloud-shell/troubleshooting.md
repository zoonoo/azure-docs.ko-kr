---
title: "Azure Cloud Shell(미리 보기) 문제 해결 | Microsoft Docs"
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
ms.date: 09/25/2017
ms.author: damaerte
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 32d4d4d10e5d8986e2dfe94430f52db8f038e245
ms.contentlocale: ko-kr
ms.lasthandoff: 09/25/2017

---

# <a name="troubleshooting-azure-cloud-shell"></a>Azure Cloud Shell 문제 해결
Azure Cloud Shell의 문제에 대해 알려진 해결책은 다음과 같습니다.

## <a name="error-400-disallowedoperation"></a>오류: 400 DisallowedOperation
 - **세부 정보**: Azure Active Directory 구독을 사용할 때는 저장소를 만들 수 없습니다.
 - **해결 방법**: 저장소 리소스를 만들 수 있는 Azure 구독을 사용하세요. Azure AD 구독으로는 Azure 리소스를 만들 수 없습니다.

## <a name="powershell-resolutions"></a>PowerShell 해결 방법

### <a name="no-home-directory-persistence"></a>$Home 디렉터리 지속성 없음
  - **세부 정보**: $Home에 데이터를 쓰는 모든 응용 프로그램(예: git, vim, 및 기타)은 PowerShell 세션 간에 유지되지 않습니다.
  - **해결 방법**: PowerShell 프로필에서 `clouddrive`에 있는 응용 프로그램 특정 폴더인 $Home에 대한 바로 가기 링크를 만듭니다.

### <a name="ctrlc-doesnt-exit-out-of-a-cmdlet-prompt"></a>Ctrl+C는 Cmdlet 프롬프트를 종료하지 않습니다.
 - **세부 정보**: Cmdlet 프롬프트를 종료하고자 할 때 `Ctrl+C`은 프롬프트를 종료하지 않습니다.
 - **해결 방법**: 프롬프트를 종료하려면 `Ctrl+C` 다음 `Enter`를 누릅니다.

### <a name="gui-applications-are-not-supported"></a>GUI 응용 프로그램은 지원되지 않습니다.
  - **세부 정보**: 사용자가 GUI 앱을 시작하는 경우 프롬프트는 반환하지 않습니다.  예를 들어 사용자가 `git`을 사용하여 2단계 인증을 사용할 수 있는 개인 리포지토리를 닫고자 할 경우 2단계 인증 코드에 대한 대화 상자가 표시됩니다.
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
 [Azure 사용자 지정 스크립트 확장][customex]를 사용하면 새 방화벽 규칙을 추가하기 위해 원격 VM에 로그온하는 것을 피할 수 있습니다.
 위의 스크립트를 예를 들어 `addfirerule.ps1`이란 파일에 저장하고, Azure 저장소 컨테이너로 업로드할 수 있습니다.
 그런 후에 다음 명령을 시돱니다.

 ``` Powershell
 Get-AzureRmVM -Name MyVM1 -ResourceGroupName MyResourceGroup | Set-AzureRmVMCustomScriptExtension -VMName MyVM1 -FileUri https://mystorageaccount.blob.core.windows.net/mycontainer/addfirerule.ps1 -Run 'addfirerule.ps1' -Name myextension
 ```
