---
title: 마스터 VHD 이미지-Azure에서 Office를 설치 합니다.
description: 설치 하 고 Azure에 Windows 가상 데스크톱 미리 보기 마스터 이미지에 Office를 사용자 지정 하는 방법입니다.
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 05/02/2019
ms.author: v-chjenk
ms.openlocfilehash: cb9edbb508ddd993dcefbf69eb06b4f0d4156485
ms.sourcegitcommit: 7042ec27b18f69db9331b3bf3b9296a9cd0c0402
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/06/2019
ms.locfileid: "66742542"
---
# <a name="install-office-on-a-master-vhd-image"></a>마스터 VHD 이미지에 Office 설치

이 문서에서는 Azure에 업로드할 마스터 가상 하드 디스크 (VHD) 이미지에서 Office 365 ProPlus, OneDrive 및 기타 일반 응용 프로그램을 설치 하는 방법을 알려줍니다. 사용자를 특정 기간 업무 (LOB) 응용 프로그램에 액세스 해야 하는 경우에이 문서의 지침을 완료 한 후 설치 하는 것이 좋습니다.

이 문서에서는 가상 머신 (VM)를 이미 만든 가정 합니다. 그렇지 않은 경우 [준비 하 고 마스터 VHD 이미지를 사용자 지정](set-up-customize-master-image.md#create-a-vm)

이 문서에서는 또한 가정 하면 액세스 권한이 상승 된 VM에서 Azure 또는 Hyper-v 관리자에서 프로 비전 될 지 여부를 합니다. 그렇지 않은 경우 [모든 Azure 구독 및 관리 그룹을 관리 하는 액세스 권한 상승](https://docs.microsoft.com/azure/role-based-access-control/elevate-access-global-admin)합니다.

>[!NOTE]
>이러한 지침은 조직의 기존 프로세스를 사용 하 여 사용할 수 있는 Windows 가상 데스크톱 미리 보기 전용 구성입니다.

## <a name="install-office-in-shared-computer-activation-mode"></a>Office 공유 컴퓨터 정품 인증 모드에서 설치

공유 컴퓨터 정품 인증을 사용 하면 여러 사용자가 액세스 하는 조직의 컴퓨터에 Office 365 ProPlus 배포를 수 있습니다. 공유 컴퓨터 정품 인증에 대 한 자세한 내용은 참조 하세요. [Office 365 ProPlus에 대 한 공유 컴퓨터 정품 인증 개요](https://docs.microsoft.com/DeployOffice/overview-of-shared-computer-activation-for-office-365-proplus)합니다.

사용 합니다 [Office 배포 도구](https://www.microsoft.com/download/details.aspx?id=49117) Office를 설치 합니다. Windows 10 Enterprise 다중 세션에는 다음 버전을의 Office만 지원합니다.
- Office 365 ProPlus
- Microsoft 365 Business 구독과 함께 제공 되는 office 365 비즈니스

Office 배포 도구 구성 XML 파일이 필요 합니다. 다음 샘플을 사용자 지정 하려면 참조는 [Office 배포 도구에 대 한 구성 옵션](https://docs.microsoft.com/deployoffice/configuration-options-for-the-office-2016-deployment-tool)합니다.

이 샘플 구성을 제공 하는 XML을 다음 작업을 수행 합니다.

- 내부 채널에서 Office를 설치 하 고 실행 하는 경우 내부 채널에서 업데이트를 제공 합니다.
- 사용 하 여 x64 아키텍처입니다.
- 자동 업데이트를 사용 하지 않도록 설정 합니다.
- Visio 및 프로젝트 설치 합니다.
- Office의 모든 기존 설치를 제거 하 고 해당 설정을 마이그레이션하십시오.
- 공유 컴퓨터 정품 인증을 사용 하도록 설정 합니다.

>[!NOTE]
>Visio에서 스텐실 검색 기능 미리 보기 구성 중에 Windows 가상 데스크톱에서 작동 하지 않습니다.

않습니다 샘플 구성 XML이 다음과 같습니다.

- 비즈니스용 Skype를 설치 합니다.
- 사용자별 모드로 OneDrive를 설치 합니다. 자세한 내용은 참조 하세요 [컴퓨터별 모드로 설치 OneDrive](#install-onedrive-in-per-machine-mode)합니다.

>[!NOTE]
>그룹 정책 개체 (Gpo) 또는 레지스트리 설정을 통해 공유 컴퓨터 정품 인증을 설정할 수 있습니다. GPO에 위치한 **컴퓨터 구성\\정책을\\관리 템플릿\\Microsoft Office 2016 (컴퓨터)\\라이선스 설정**

Office 배포 도구는 setup.exe를 포함합니다. Office를 설치 하려면 명령줄에서 다음 명령을 실행 합니다.

```batch
Setup.exe /configure configuration.xml
```

#### <a name="sample-configurationxml"></a>샘플 configuration.xml

다음 XML 샘플 참가자 릴리스에 설치 됩니다.

```xml
<Configuration>
    <Add OfficeClientEdition="64" SourcePath="http://officecdn.microsoft.com/pr/5440fd1f-7ecb-4221-8110-145efaa6372f">
        <Product ID="O365ProPlusRetail">
            <Language ID="en-US" />
            <Language ID="MatchOS" Fallback = "en-US"/>
            <Language ID="MatchPreviousMSI" />
            <ExcludeApp ID="Groove" />
            <ExcludeApp ID="Lync" />
            <ExcludeApp ID="OneDrive" />
            <ExcludeApp ID="Teams" />
        </Product>
        <Product ID="VisioProRetail">
            <Language ID="en-US" />
            <Language ID="MatchOS" Fallback = "en-US"/>
            <Language ID="MatchPreviousMSI" />
            <ExcludeApp ID="Teams" /> 
        </Product>
        <Product ID="ProjectProRetail">
            <Language ID="en-US" />
            <Language ID="MatchOS" Fallback = "en-US"/>
            <Language ID="MatchPreviousMSI" />
            <ExcludeApp ID="Teams" />
        </Product>
    </Add>
    <RemoveMSI All="True" />
    <Updates Enabled="FALSE" UpdatePath="http://officecdn.microsoft.com/pr/5440fd1f-7ecb-4221-8110-145efaa6372f" />
    <Display Level="None" AcceptEULA="TRUE" />
    <Logging Level="Verbose" Path="%temp%\WVDOfficeInstall" />
    <Property Value="TRUE" Name="FORCEAPPSHUTDOWN"/>
    <Property Value="1" Name="SharedComputerLicensing"/>
    <Property Value="TRUE" Name="PinIconsToTaskbar"/>
</Configuration>
```

>[!NOTE]
>Office 팀에 대 한 64 비트 설치를 사용 하도록 권장 합니다 **OfficeClientEdition** 매개 변수입니다.

Office를 설치한 후 기본 Office 동작을 업데이트할 수 있습니다. 개별적으로 또는 동작을 업데이트 하는 배치 파일에서 다음 명령을 실행 합니다.

```batch
rem Mount the default user registry hive
reg load HKU\TempDefault C:\Users\Default\NTUSER.DAT
rem Must be executed with default registry hive mounted.
reg add HKU\TempDefault\SOFTWARE\Policies\Microsoft\office\16.0\common /v InsiderSlabBehavior /t REG_DWORD /d 2 /f
rem Set Outlook's Cached Exchange Mode behavior
rem Must be executed with default registry hive mounted.
reg add "HKU\TempDefault\software\policies\microsoft\office\16.0\outlook\cached mode" /v enable /t REG_DWORD /d 1 /f
reg add "HKU\TempDefault\software\policies\microsoft\office\16.0\outlook\cached mode" /v syncwindowsetting /t REG_DWORD /d 1 /f
reg add "HKU\TempDefault\software\policies\microsoft\office\16.0\outlook\cached mode" /v CalendarSyncWindowSetting /t REG_DWORD /d 1 /f
reg add "HKU\TempDefault\software\policies\microsoft\office\16.0\outlook\cached mode" /v CalendarSyncWindowSettingMonths  /t REG_DWORD /d 1 /f
rem Unmount the default user registry hive
reg unload HKU\TempDefault

rem Set the Office Update UI behavior.
reg add HKLM\SOFTWARE\Policies\Microsoft\office\16.0\common\officeupdate /v hideupdatenotifications /t REG_DWORD /d 1 /f
reg add HKLM\SOFTWARE\Policies\Microsoft\office\16.0\common\officeupdate /v hideenabledisableupdates /t REG_DWORD /d 1 /f
```

## <a name="install-onedrive-in-per-machine-mode"></a>OneDrive 컴퓨터별 모드에서 설치

OneDrive는 사용자 단위로 정상적으로 설치 합니다. 이 환경에서는 것 컴퓨터별 설치 합니다.

OneDrive 컴퓨터별 모드로 설치 하는 방법을 다음과 같습니다.

1. 먼저 OneDrive 설치 관리자를 준비 하는 위치를 만듭니다. 로컬 디스크 폴더 또는 [\\\\unc] (file://unc) 위치 해도 괜찮습니다.

2. 이 링크를 사용 하 여 스테이징 위치로 OneDriveSetup.exe를 다운로드 합니다. <https://aka.ms/OneDriveWVD-Installer>

3. 생략 하 여 OneDrive를 사용 하 여 office를 설치 하는 경우  **\<ExcludeApp ID = "OneDrive" /\>** , 관리자 권한 명령 프롬프트에서 다음을 실행 하 여 설치 된 기존 OneDrive 사용자별 설치를 제거 명령:
    
    ```batch
    "[staged location]\OneDriveSetup.exe" /uninstall
    ```

4. 설정 하려면 관리자 권한 명령 프롬프트에서 다음이 명령을 실행 합니다 **AllUsersInstall** 레지스트리 값:

    ```batch
    REG ADD "HKLM\Software\Microsoft\OneDrive" /v "AllUsersInstall" /t REG_DWORD /d 1 /reg:64
    ```

5. 컴퓨터별 모드로 OneDrive를 설치 하려면이 명령을 실행 합니다.

    ```batch
    Run "[staged location]\OneDriveSetup.exe" /allusers
    ```

6. 모든 사용자에 대 한 로그인을 시작 하는 OneDrive를 구성 하려면이 명령을 실행 합니다.

    ```batch
    REG ADD "HKLM\Software\Microsoft\Windows\CurrentVersion\Run" /v OneDrive /t REG_SZ /d "C:\Program Files (x86)\Microsoft OneDrive\OneDrive.exe /background" /f
    ```

7. 사용 하도록 설정 **사용자 계정을 자동으로 구성** 다음 명령을 실행 하 여 합니다.

    ```batch
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\OneDrive" /v "SilentAccountConfig" /t REG_DWORD /d 1 /f
    ```

8. 리디렉션 하 고 다음 명령을 실행 하 여 OneDrive로 폴더를 알려진 Windows를 이동 합니다.

    ```batch
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\OneDrive" /v "KFMSilentOptIn" /t REG_SZ /d "<your-AzureAdTenantId>" /f
    ```

## <a name="teams-and-skype"></a>팀 및 Skype

Windows 가상 데스크톱은 비즈니스 및 팀에 대 한 Skype를 지원 하지 않습니다.

## <a name="next-steps"></a>다음 단계

이미지에 Office를 추가한 했으므로 마스터 VHD 이미지를 사용자 지정할 계속 수 있습니다. 참조 [준비 하 고 마스터 VHD 이미지를 사용자 지정](set-up-customize-master-image.md)합니다.
