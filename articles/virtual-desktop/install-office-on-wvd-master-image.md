---
title: 마스터 VHD 이미지에 사무실 설치 - Azure
description: 설치 하 고 Azure에 Windows 가상 데스크톱 마스터 이미지에 Office를 사용자 지정 하는 방법.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: b93f26a6799a50868feb1f3350a3dc4a73a0b2e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127845"
---
# <a name="install-office-on-a-master-vhd-image"></a>마스터 VHD 이미지에 Office 설치

이 문서에서는 Azure에 업로드하기 위해 마스터 가상 하드 디스크(VHD) 이미지에 Office 365 ProPlus, OneDrive 및 기타 일반적인 응용 프로그램을 설치하는 방법을 설명합니다. 사용자가 특정 LOB(업무 용) 응용 프로그램에 액세스해야 하는 경우 이 문서의 지침을 완료한 후 설치하는 것이 좋습니다.

이 문서에서는 VM(가상 시스템)을 이미 만들었다고 가정합니다. 그렇지 않은 경우 [마스터 VHD 이미지 준비 및 사용자 지정](set-up-customize-master-image.md#create-a-vm) 을 참조하십시오.

또한 이 문서에서는 Azure 또는 Hyper-V Manager에서 프로비전된 VM에 대한 액세스 가 높아졌다고 가정합니다. 그렇지 않은 경우 [액세스 상승을 참조하여 모든 Azure 구독 및 관리 그룹을 관리합니다.](../role-based-access-control/elevate-access-global-admin.md)

>[!NOTE]
>이러한 지침은 조직의 기존 프로세스에 사용할 수 있는 Windows Virtual Desktop 관련 구성에 대한 것입니다.

## <a name="install-office-in-shared-computer-activation-mode"></a>공유 컴퓨터 활성화 모드에서 Office 설치

공유 컴퓨터 활성화를 사용하면 여러 사용자가 액세스하는 조직의 컴퓨터에 Office 365 ProPlus를 배포할 수 있습니다. 공유 컴퓨터 정품 인증에 대한 자세한 내용은 [Office 365 ProPlus의 공유 컴퓨터 정품 인증 개요를](/deployoffice/overview-of-shared-computer-activation-for-office-365-proplus/)참조하십시오.

Office [배포 도구를](https://www.microsoft.com/download/details.aspx?id=49117) 사용하여 Office를 설치합니다. Windows 10 엔터프라이즈 다중 세션은 다음과 같은 Office 버전만 지원합니다.
- Office 365 ProPlus
- 마이크로소프트 와 함께 제공 되는 사무실 365 비즈니스 365 비즈니스 구독

Office 배포 도구에는 구성 XML 파일이 필요합니다. 다음 샘플을 사용자 지정하려면 [Office 배포 도구에 대한 구성 옵션을](/deployoffice/configuration-options-for-the-office-2016-deployment-tool/)참조하십시오.

우리가 제공 한이 샘플 구성 XML은 다음과 같은 작업을 수행합니다 :

- 월별 채널에서 Office를 설치하고 실행 될 때 월별 채널에서 업데이트를 제공합니다.
- x64 아키텍처를 사용합니다.
- 자동 업데이트를 사용하지 않도록 설정합니다.
- Office의 기존 설치를 제거하고 설정을 마이그레이션합니다.
- 공유 컴퓨터 활성화를 사용하도록 설정합니다.

>[!NOTE]
>Visio의 스텐실 검색 기능이 Windows 가상 데스크톱에서 예상대로 작동하지 않을 수 있습니다.

이 샘플 구성 XML은 다음과 같습니다.

- 비즈니스를 위한 Skype 설치
- 사용자당 모드에서 OneDrive를 설치합니다. 자세한 내용은 [컴퓨터별 모드에서 OneDrive 설치를](#install-onedrive-in-per-machine-mode)참조하십시오.

>[!NOTE]
>공유 컴퓨터 활성화는 그룹 정책 개체(GPO) 또는 레지스트리 설정을 통해 설정할 수 있습니다. GPO는 컴퓨터 **구성\\정책\\관리\\템플릿 Microsoft Office\\2016 (컴퓨터) 라이선스 설정에** 있습니다.

Office 배포 도구에는 setup.exe가 포함되어 있습니다. Office를 설치하려면 명령줄에서 다음 명령을 실행합니다.

```batch
Setup.exe /configure configuration.xml
```

#### <a name="sample-configurationxml"></a>샘플 구성.xml

다음 XML 샘플은 월별 릴리스를 설치합니다.

```xml
<Configuration>
  <Add OfficeClientEdition="64" Channel="Monthly">
    <Product ID="O365ProPlusRetail">
      <Language ID="en-US" />
      <Language ID="MatchOS" />
      <ExcludeApp ID="Groove" />
      <ExcludeApp ID="Lync" />
      <ExcludeApp ID="OneDrive" />
      <ExcludeApp ID="Teams" />
    </Product>
  </Add>
  <RemoveMSI/>
  <Updates Enabled="FALSE"/>
  <Display Level="None" AcceptEULA="TRUE" />
  <Logging Level=" Standard" Path="%temp%\WVDOfficeInstall" />
  <Property Name="FORCEAPPSHUTDOWN" Value="TRUE"/>
  <Property Name="SharedComputerLicensing" Value="1"/>
</Configuration>
```

>[!NOTE]
>Office 팀은 **OfficeClientEdition** 매개 변수에 대해 64비트 설치를 사용하는 것이 좋습니다.

Office를 설치한 후 기본 Office 동작을 업데이트할 수 있습니다. 다음 명령을 개별적으로 또는 일괄 처리 파일에서 실행하여 동작을 업데이트합니다.

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

## <a name="install-onedrive-in-per-machine-mode"></a>기계당 모드에서 OneDrive 설치

OneDrive는 일반적으로 사용자당 설치됩니다. 이 환경에서는 컴퓨터당 설치해야 합니다.

기계당 모드에서 OneDrive를 설치하는 방법은 다음과 같습니다.

1. 먼저 OneDrive 설치 프로그램을 단계화할 위치를 만듭니다. 로컬 디스크 폴더\\\\또는 [unc] (file://unc) 위치는 괜찮습니다.

2. 이 링크와 함께 준비된 위치에 OneDriveSetup.exe를 다운로드하십시오.<https://aka.ms/OneDriveWVD-Installer>

3. ** \<제외 앱 ID="OneDrive"/를\>** 생략하여 OneDrive로 사무실을 설치한 경우 다음 명령을 실행하여 높은 명령 프롬프트에서 사용자당 기존 OneDrive 설치를 제거합니다.
    
    ```batch
    "[staged location]\OneDriveSetup.exe" /uninstall
    ```

4. 높은 명령 프롬프트에서 이 명령을 실행하여 **AllUsersInstall** 레지스트리 값을 설정합니다.

    ```batch
    REG ADD "HKLM\Software\Microsoft\OneDrive" /v "AllUsersInstall" /t REG_DWORD /d 1 /reg:64
    ```

5. 이 명령을 실행하여 컴퓨터당 모드에서 OneDrive를 설치합니다.

    ```batch
    Run "[staged location]\OneDriveSetup.exe" /allusers
    ```

6. 이 명령을 실행하여 모든 사용자에 대해 로그인할 때 시작하도록 OneDrive를 구성합니다.

    ```batch
    REG ADD "HKLM\Software\Microsoft\Windows\CurrentVersion\Run" /v OneDrive /t REG_SZ /d "C:\Program Files (x86)\Microsoft OneDrive\OneDrive.exe /background" /f
    ```

7. 다음 명령을 실행하여 **사용자 계정을 자동으로 구성하도록** 설정합니다.

    ```batch
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\OneDrive" /v "SilentAccountConfig" /t REG_DWORD /d 1 /f
    ```

8. 다음 명령을 실행하여 Windows 로 알려진 폴더를 OneDrive로 리디렉션하고 이동합니다.

    ```batch
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\OneDrive" /v "KFMSilentOptIn" /t REG_SZ /d "<your-AzureAdTenantId>" /f
    ```

## <a name="teams-and-skype"></a>팀과 스카이프

Windows 가상 데스크톱은 비즈니스 및 팀을 위한 Skype를 지원하지 않습니다.

## <a name="next-steps"></a>다음 단계

이제 이미지에 Office를 추가한 후 마스터 VHD 이미지를 계속 사용자 지정할 수 있습니다. [마스터 VHD 이미지 준비 및 사용자 지정을](set-up-customize-master-image.md)참조하십시오.
