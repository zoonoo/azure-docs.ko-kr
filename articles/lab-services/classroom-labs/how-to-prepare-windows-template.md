---
title: Windows 템플릿 컴퓨터 설정 가이드 | 마이크로 소프트 문서
description: 랩 서비스에서 Windows 템플릿 컴퓨터를 준비하는 일반적인 단계입니다.  이러한 단계에는 Windows 업데이트 일정 설정, OneDrive 설치 및 Office 설치가 포함됩니다.
services: lab-services
documentationcenter: na
author: EMaher
manager: ''
editor: ''
ms.service: lab-services
ms.topic: article
ms.date: 11/21/2019
ms.author: enewman
ms.openlocfilehash: c52a1212d160adce3a0a0638164833bc2907a856
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76515006"
---
# <a name="guide-to-setting-up-a-windows-template-machine-in-azure-lab-services"></a>Azure 랩 서비스에서 Windows 템플릿 컴퓨터를 설정하는 가이드

Azure Lab 서비스에 대한 Windows 10 템플릿 컴퓨터를 설정하는 경우 고려해야 할 몇 가지 모범 사례 및 팁이 있습니다. 아래 구성 단계는 모두 선택 사항입니다.  그러나 이러한 준비 단계는 학생들의 생산성을 높이고 수업 시간 중단을 최소화하며 최신 기술을 사용하고 있는지 확인하는 데 도움이 될 수 있습니다.

>[!IMPORTANT]
>이 문서에는 컴퓨터 템플릿 수정 프로세스를 간소화하기 위한 PowerShell 스니펫이 포함되어 있습니다.  표시된 모든 PowerShell 스크립트의 경우 관리자 권한으로 Windows PowerShell에서 실행해야 합니다. 윈도우에서 10, 그 일을 하는 빠른 방법은 오른쪽 단추로 클릭 시작 메뉴를 선택 하 고 "윈도우 PowerShell (관리자)"를 선택.

## <a name="install-and-configure-onedrive"></a>원드라이브 설치 및 구성

가상 컴퓨터를 재설정하면 학생 데이터가 손실되지 않도록 하려면 학생이 데이터를 클라우드로 백업하는 것이 좋습니다.  Microsoft OneDrive는 학생들이 데이터를 보호하는 데 도움을 줄 수 있습니다.  

### <a name="install-onedrive"></a>원드라이브 설치

OneDrive를 수동으로 다운로드하여 설치하려면 [OneDrive](https://onedrive.live.com/about/download/) 또는 [OneDrive For Business](https://onedrive.live.com/about/business/) 다운로드 페이지를 참조하십시오.

다음 PowerShell 스크립트를 사용할 수도 있습니다.  그것은 자동으로 다운로드 하 고 OneDrive의 최신 버전을 설치 합니다.  OneDrive 클라이언트가 설치되면 설치 관리자를 실행합니다.  이 예제에서는 스위치를 `/allUsers` 사용하여 기기의 모든 사용자에게 OneDrive를 설치합니다. 우리는 또한 `/silent` 자동으로 OneDrive를 설치 하는 스위치를 사용.

```powershell
Write-Host "Downloading OneDrive Client..."
$DownloadPath = "$env:USERPROFILE/Downloads/OneDriveSetup.exe"
if((Test-Path $DownloadPath) -eq $False )
{
    Write-Host "Downloading OneDrive..."
    $web = new-object System.Net.WebClient
    $web.DownloadFile("https://go.microsoft.com/fwlink/p/?LinkId=248256",$DownloadPath)
} else {
    Write-Host "OneDrive installer already exists at " $DownloadPath
}

Write-Host "Installing OneDrive..."
& $env:USERPROFILE/Downloads/OneDriveSetup.exe /allUsers /silent
```

### <a name="onedrive-customizations"></a>원 드라이브 사용자 지정

[OneDrive에 수행 할 수있는 많은 사용자 정의가 있습니다.](https://docs.microsoft.com/onedrive/use-group-policy) 좀 더 일반적인 사용자 지정 중 일부를 살펴보겠습니다.

#### <a name="silently-move-windows-known-folders-to-onedrive"></a>자동으로 OneDrive로 알려진 Windows 알려진 폴더를 이동

문서, 다운로드 및 그림과 같은 폴더는 학생 파일을 저장하는 데 자주 사용됩니다. 이러한 폴더가 OneDrive에 백업되도록 하려면 이러한 폴더를 OneDrive로 이동하는 것이 좋습니다.

Active Directory를 사용하지 않는 컴퓨터에 있는 경우 사용자는 OneDrive로 인증한 후 해당 폴더를 OneDrive로 수동으로 이동할 수 있습니다.

1. 파일 탐색기 열기
2. 문서, 다운로드 또는 그림 폴더를 마우스 오른쪽 단추로 클릭합니다.
3. 속성 > 위치로 이동합니다.  OneDrive 디렉터리에서 폴더를 새 폴더로 이동합니다.

가상 시스템이 Active Directory에 연결되어 있는 경우 템플릿 컴퓨터를 설정하여 학생이 알려진 폴더를 OneDrive로 이동하라는 메시지를 자동으로 표시할 수 있습니다.  

먼저 Office 테넌트 ID를 검색해야 합니다.  자세한 내용은 [Office 365 테넌트 ID를 참조하십시오.](https://docs.microsoft.com/onedrive/find-your-office-365-tenant-id)  다음 PowerShell을 사용하여 Office 365 테넌트 ID를 얻을 수도 있습니다.

```powershell
Install-Module MSOnline -Confirm
Connect-MsolService
$officeTenantID = Get-MSOLCompanyInformation |
    Select-Object -expand objectID |
    Select-Object -expand Guid
```

Office 365 테넌트 ID가 있으면 다음 PowerShell을 사용하여 알려진 폴더를 OneDrive로 이동하라는 메시지를 표시하도록 OneDrive를 설정합니다.

```powershell
if ($officeTenantID -eq $null)
{
        Write-Error "Variable `$officeTenantId must be set to your Office Tenant Id before continuing."
}
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
    -Name "KFMSilentOptIn" -Value $officeTenantID -PropertyType STRING
```

### <a name="use-onedrive-files-on-demand"></a>주문형 OneDrive 파일 사용

학생은 OneDrive 계정에 많은 파일이 있을 수 있습니다. 컴퓨터의 공간을 절약하고 다운로드 시간을 줄이려면 학생의 OneDrive 계정에 저장된 모든 파일을 온디맨드방식으로 만드는 것이 좋습니다.  주문형 파일은 사용자가 파일에 액세스한 후에만 다운로드됩니다.

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive" -Force
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
    -Name "FilesOnDemandEnabled" -Value "00000001" -PropertyType DWORD
```

### <a name="silently-sign-in-users-to-onedrive"></a>OneDrive에 사용자를 자동으로 로그인

OneDrive는 로그온한 사용자의 Windows 자격 증명으로 자동으로 로그인하도록 설정할 수 있습니다.  자동 로그인은 학생이 Office 365 학교 자격 증명으로 로그인하는 수업에 유용합니다.

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
    -Name "SilentAccountConfig" -Value "00000001" -PropertyType DWORD
```

### <a name="disable-the-tutorial-that-appears-at-the-end-of-onedrive-setup"></a>OneDrive 설정의 끝에 나타나는 자습서를 사용하지 않도록 설정

이 설정을 사용하면 OneDrive 설정이 끝날 때 웹 브라우저에서 자습서가 시작되지 않도록 할 수 있습니다.

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive" -Force
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
    -Name "DisableTutorial" -Value "00000001" -PropertyType DWORD -Force
```

### <a name="set-the-maximum-size-of-a-file-that-to-be-download-automatically"></a>자동으로 다운로드할 파일의 최대 크기를 설정합니다.

이 설정은 OneDrive 파일 온디맨드를 사용하도록 설정하지 않은 장치에서 Windows 자격 증명을 사용하여 OneDrive 동기화 클라이언트에 자동으로 로그인하는 것과 함께 사용됩니다. 지정된 임계값(MB)보다 큰 OneDrive를 가진 사용자는 OneDrive 동기화 클라이언트(OneDrive.exe)가 파일을 다운로드하기 전에 동기화할 폴더를 선택하라는 메시지가 표시됩니다.  이 예제에서 "1111-2222-3333-4444"는 Office 365 테넌트 ID이고 0005000은 5GB의 임계값을 설정합니다.

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive\DiskSpaceCheckThresholdMB"
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive\DiskSpaceCheckThresholdMB"
    -Name "1111-2222-3333-4444" -Value "0005000" -PropertyType DWORD
```

## <a name="install-and-configure-microsoft-office-365"></a>설치 및 마이크로 소프트 오피스 를 구성 365

### <a name="install-microsoft-office-365"></a>마이크로 소프트 오피스 를 설치 365

템플릿 컴퓨터에 Office가 필요한 경우 [ODT(Office 배포 도구)를](https://www.microsoft.com/download/details.aspx?id=49117 )통해 Office를 설치하는 것이 좋습니다. [Office 365 클라이언트 구성 서비스를](https://config.office.com/) 사용하여 재사용 가능한 구성 파일을 만들어 어떤 아키텍처, Office에서 필요한 기능 및 업데이트 빈도를 선택해야 합니다.

1. Office [365 클라이언트 구성 서비스로](https://config.office.com/) 이동하여 사용자 고유의 구성 파일을 다운로드합니다.
2. [사무실 배포 도구를 다운로드합니다.](https://www.microsoft.com/download/details.aspx?id=49117)  다운로드한 파일은 . `setup.exe`
3. Office `setup.exe /download configuration.xml` 구성 요소를 다운로드하려면 실행합니다.
4. Office `setup.exe /configure configuration.xml` 구성 요소를 설치하려면 실행합니다.

### <a name="change-the-microsoft-office-365-update-channel"></a>마이크로소프트 오피스 변경 365 업데이트 채널

Office 구성 도구를 사용하여 Office에서 업데이트를 받는 빈도를 설정할 수 있습니다. 그러나 설치 후 Office에서 업데이트를 받는 빈도를 수정해야 하는 경우 업데이트 채널 URL을 변경할 수 있습니다. 업데이트 채널 URL 주소는 [조직의 장치에 대한 Office 365 ProPlus 업데이트 채널 변경에서](https://docs.microsoft.com/deployoffice/change-update-channels)찾을 수 있습니다. 아래 예제에서는 Office 365를 월별 업데이트 채널을 사용하도록 설정하는 방법을 보여 주습니다.

```powershell
# Update to the Office 365 Monthly Channel
Set-ItemProperty
    -Path "HKLM:\SOFTWARE\Microsoft\Office\ClickToRun\Configuration\CDNBaseUrl"
    -Name "CDNBaseUrl"
    -Value "http://officecdn.microsoft.com/pr/492350f6-3a01-4f97-b9c0-c7c6ddf67d60"
```

## <a name="install-and-configure-updates"></a>업데이트 설치 및 구성

### <a name="install-the-latest-windows-updates"></a>최신 Windows 업데이트 설치

템플릿 VM을 게시하기 전에 보안을 위해 템플릿 컴퓨터에 최신 Microsoft 업데이트를 설치하는 것이 좋습니다.  또한 예기치 않은 시간에 업데이트가 실행될 때 학생이 작업에 방해가 되는 것을 방지할 수 있습니다.

1. 시작 메뉴에서 **설정** 시작
2. 보안 **& 업데이트를 클릭합니다.**
3. **업데이트 확인을 클릭합니다.**
4. 업데이트가 다운로드및 설치됩니다.

PowerShell을 사용하여 템플릿 컴퓨터를 업데이트할 수도 있습니다.

```powershell
Set-ExecutionPolicy Bypass -Scope Process -Force
Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Confirm
Install-Module PSWindowsUpdate -Confirm
Install-WindowsUpdate -MicrosoftUpdate
Set-ExecutionPolicy default -Force
```

>[!NOTE]
>일부 업데이트에서는 컴퓨터를 다시 시작해야 할 수 있습니다.  재부팅이 필요한 경우 메시지가 표시됩니다.

### <a name="install-the-latest-updates-for-microsoft-store-apps"></a>마이크로소프트 스토어 애플 리 케이 션에 대 한 최신 업데이트 설치

모든 Microsoft 스토어 앱을 최신 버전으로 업데이트하는 것이 좋습니다.  다음은 Microsoft 스토어에서 응용 프로그램을 수동으로 업데이트하는 지침입니다.  

1. **마이크로소프트 스토어** 응용 프로그램을 시작 합니다.
2. 응용 프로그램의 상단 모서리에 있는 사용자 사진 옆에 있는 타원(...)을 클릭합니다.
3. 드롭다운 메뉴에서 **다운로드** 및 업데이트를 선택합니다.
4. 업데이트 받기 버튼을 **클릭합니다.**

Powershell을 사용하여 이미 설치된 Microsoft 스토어 응용 프로그램을 업데이트할 수도 있습니다.

```powershell
(Get-WmiObject -Namespace "root\cimv2\mdm\dmmap" -Class "MDM_EnterpriseModernAppManagement_AppManagement01").UpdateScanMethod()
```

### <a name="stop-automatic-windows-updates"></a>자동 Windows 업데이트 중지

Windows를 최신 버전으로 업데이트한 후 Windows 업데이트를 중지하는 것이 좋습니다.  자동 업데이트는 예약된 수업 시간을 방해할 수 있습니다.  코스가 더 오래 실행되는 경우 학생에게 수동으로 업데이트를 확인하도록 요청하거나 예정된 수업 시간 이외에 는 시간에 대한 자동 업데이트를 설정하는 것이 좋습니다.  Windows 업데이트에 대한 사용자 지정 옵션에 대한 자세한 내용은 [추가 Windows 업데이트 설정 관리를](https://docs.microsoft.com/windows/deployment/update/waas-wu-settings)참조하십시오.

다음 PowerShell 스크립트를 사용하여 자동 Windows 업데이트를 중지할 수 있습니다.

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\Windows\AU"
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\Windows\AU"
    -Name "NoAutoUpdate" -Value "1" -PropertyType DWORD
```

## <a name="install-foreign-language-packs"></a>외국어 팩 설치

가상 컴퓨터에 추가 언어가 설치되어 있는 경우 Microsoft 스토어를 통해 언어를 추가할 수 있습니다.

1. 마이크로소프트 스토어 시작
2. "언어 팩" 검색
3. 설치할 언어 선택

템플릿 VM에 이미 로그온한 경우 ["언어 팩 설치" 바로 가기를](ms-settings:regionlanguage?activationSource=SMC-IA-4027670) 사용하여 적절한 설정 페이지로 바로 이동합니다.

## <a name="remove-unneeded-built-in-apps"></a>불필요한 기본 제공 앱 제거

Windows 10에는 특정 클래스에 필요하지 않은 많은 기본 제공 응용 프로그램이 함께 제공됩니다. 학생용 컴퓨터 이미지를 단순화하려면 템플릿 컴퓨터에서 일부 응용 프로그램을 제거할 수 있습니다.  설치된 응용 프로그램 목록을 보려면 PowerShell `Get-AppxPackage` cmdlet을 사용합니다.  아래 예제에서는 제거할 수 있는 설치된 모든 응용 프로그램을 보여 주며 있습니다.

```powershell
Get-AppxPackage | Where {$_.NonRemovable -eq $false} | select Name
```

응용 프로그램을 제거하려면 제거-Appx cmdlet을 사용합니다.  아래 예제에서는 XBox와 관련된 모든 것을 제거하는 방법을 보여 주며, 이와 관련된 모든 것을 제거하는 방법을 보여 주실 수 있습니다.

```powershell
Get-AppxPackage -Name *xbox* | foreach { if (-not $_.NonRemovable) { Remove-AppxPackage $_} }
```

## <a name="install-common-teaching-related-applications"></a>일반적인 교육 관련 응용 프로그램 설치

Windows 스토어 앱을 통해 가르치는 데 일반적으로 사용되는 다른 앱을 설치합니다. 제안에는 [마이크로 소프트 화이트 보드 응용 프로그램,](https://www.microsoft.com/store/productId/9MSPC6MP8FM4)마이크로 소프트 [팀](https://www.microsoft.com/store/productId/9MSPC6MP8FM4)및 [마인 크래프트 교육 에디션과](https://education.minecraft.net/)같은 응용 프로그램이 포함됩니다. 이러한 응용 프로그램은 Windows 스토어를 통해 또는 템플릿 VM의 해당 웹 사이트를 통해 수동으로 설치해야 합니다.

## <a name="conclusion"></a>결론

이 문서에서는 효과적인 클래스에 대 한 Windows 템플릿 VM을 준비 하는 선택적 단계를 보여 주었다.  단계에는 OneDrive 설치 및 Office 365 설치, Windows용 업데이트 설치 및 Microsoft 스토어 앱에 대 한 업데이트 설치 가 포함 됩니다.  또한 클래스에 가장 적합한 일정에 대한 업데이트를 설정하는 방법에 대해서도 설명했습니다.  
