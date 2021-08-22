---
title: 마스터 VHD 이미지에 Office 설치 - Azure
description: Azure Virtual Desktop 마스터 이미지의 Office를 Azure에 설치하고 사용자 지정하는 방법입니다.
author: Heidilohr
ms.topic: how-to
ms.date: 05/02/2019
ms.author: helohr
manager: femila
ms.openlocfilehash: ca466110a3ac876df444aa380a4a2dde0384f1fa
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114294123"
---
# <a name="install-office-on-a-master-vhd-image"></a>마스터 VHD 이미지에 Office 설치

이 문서에서는 Azure에 업로드할 수 있도록 마스터 VHD(가상 하드 디스크) 이미지에 엔터프라이즈, OneDrive 및 기타 일반 애플리케이션에 대한 Microsoft 365 앱을 설치하는 방법을 설명합니다. 사용자가 특정 LOB(기간 업무) 애플리케이션에 액세스해야 하는 경우 이 문서의 지침을 완료한 후 설치하는 것이 좋습니다.

이 문서에서는 VM(가상 머신)을 이미 만들었다고 가정합니다. 그렇지 않은 경우 [마스터 VHD 이미지 준비 및 사용자 지정](set-up-customize-master-image.md#create-a-vm)을 참조하세요.

또한 이 문서에서는 Azure 또는 Hyper-V 관리자에서 프로비전되었는지 여부에 관계없이 VM에 대한 관리자 액세스 권한이 있다고 가정합니다. 그렇지 않은 경우 [모든 Azure 구독 및 관리 그룹을 관리할 수 있도록 액세스 권한 상승](../role-based-access-control/elevate-access-global-admin.md)을 참조하세요.

>[!NOTE]
>이러한 지침은 조직의 기존 프로세스에 사용할 수 있는 Windows Virtual Desktop 관련 구성에 대한 것입니다.

## <a name="install-office-in-shared-computer-activation-mode"></a>공유 컴퓨터 인증 모드로 Office 설치

공유 컴퓨터 인증을 통해 여러 사용자가 액세스하는 조직의 컴퓨터에 엔터프라이즈용 Microsoft 365 앱을 배포할 수 있습니다. 공유 컴퓨터 인증에 대한 자세한 내용은 [Microsoft 365 앱의 공유 컴퓨터 인증 개요](/deployoffice/overview-shared-computer-activation)를 참조하세요.

[Office 배포 도구](https://www.microsoft.com/download/details.aspx?id=49117)를 사용하여 Office를 설치합니다. Windows 10 Enterprise 다중 세션은 다음 버전의 Office만 지원합니다.

   - 엔터프라이즈용 Microsoft 365 앱
   - Microsoft 365 Business Premium 구독 시 제공되는 비즈니스용 Microsoft 365 앱

Office 배포 도구를 사용하려면 구성 XML 파일이 필요합니다. 다음 샘플을 사용자 지정하려면 [Office 배포 도구에 대한 구성 옵션](/deployoffice/configuration-options-for-the-office-2016-deployment-tool/)을 참조하세요.

제공된 이 샘플 구성 XML은 다음 작업을 수행합니다.

   - 월간 엔터프라이즈 채널에서 Office를 설치하고 월간 엔터프라이즈 채널에서 업데이트를 제공합니다.
   - x64 아키텍처를 사용합니다.
   - 자동 업데이트를 비활성화합니다.
   - 기존 Office 설치를 제거하고 해당 설정을 마이그레이션합니다.
   - 공유 컴퓨터 인증을 활성화합니다.

>[!NOTE]
>Visio의 스텐실 검색 기능이 Azure Virtual Desktop에서 예상대로 작동하지 않을 수 있습니다.

이 샘플 구성 XML이 수행하지 않는 작업은 다음과 같습니다.

   - 비즈니스용 Skype 설치
   - 사용자 단위 모드에서 OneDrive를 설치합니다. 자세한 내용은 [머신 단위 모드에서 OneDrive 설치](#install-onedrive-in-per-machine-mode)를 참조하세요.

>[!NOTE]
>공유 컴퓨터 인증은 GPO(그룹 정책 개체) 또는 레지스트리 설정을 통해 설정할 수 있습니다. GPO는 **컴퓨터 구성\\정책\\관리 템플릿\\Microsoft Office 2016(머신)\\라이선스 설정** 에 있습니다.

Office 배포 도구에는 setup.exe가 포함되어 있습니다. Office를 설치하려면 명령줄에서 다음 명령을 실행합니다.

```cmd
Setup.exe /configure configuration.xml
```

#### <a name="sample-configurationxml"></a>샘플 configuration.xml

다음 XML 샘플에서는 월간 엔터프라이즈 채널 릴리스를 설치합니다.

```xml
<Configuration>
  <Add OfficeClientEdition="64" Channel="MonthlyEnterprise">
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
  <Logging Level="Standard" Path="%temp%\WVDOfficeInstall" />
  <Property Name="FORCEAPPSHUTDOWN" Value="TRUE"/>
  <Property Name="SharedComputerLicensing" Value="1"/>
</Configuration>
```

>[!NOTE]
>Office 팀은 **OfficeClientEdition** 매개 변수에 64비트 설치를 사용할 것을 권장합니다.

Office를 설치한 후 기본 Office 동작을 업데이트할 수 있습니다. 다음 명령을 개별적으로 실행하거나 배치 파일에서 실행하여 동작을 업데이트합니다.

```cmd
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

## <a name="install-onedrive-in-per-machine-mode"></a>머신 단위 모드에서 OneDrive 설치

OneDrive는 일반적으로 사용자별로 설치됩니다. 이 환경에서 머신별로 설치해야 합니다.

머신 단위 모드에서 OneDrive를 설치하는 방법은 다음과 같습니다.

1. 먼저, OneDrive 설치 관리자를 준비할 위치를 만듭니다. 로컬 디스크 폴더 또는 [\\\\unc](file://unc) 위치가 정상입니다.

2. <https://aka.ms/OneDriveWVD-Installer> 링크를 사용하여 준비된 위치에 OneDriveSetup.exe를 다운로드합니다.

3. **\<ExcludeApp ID="OneDrive" /\>** 를 생략하여 OneDrive에 Office를 설치한 경우 다음 명령을 실행하여 관리자 권한 명령 프롬프트에서 기존 OneDrive 사용자별 설치를 제거합니다.

    ```cmd
    "[staged location]\OneDriveSetup.exe" /uninstall
    ```

4. 관리자 권한 명령 프롬프트에서 이 명령을 실행하여 **AllUsersInstall** 레지스트리 값을 설정합니다.

    ```cmd
    REG ADD "HKLM\Software\Microsoft\OneDrive" /v "AllUsersInstall" /t REG_DWORD /d 1 /reg:64
    ```

5. 다음 명령을 실행하여 머신 단위 모드에서 OneDrive를 설치합니다.

    ```cmd
    Run "[staged location]\OneDriveSetup.exe" /allusers
    ```

6. 다음 명령을 실행하여 모든 사용자에 대해 로그인할 때 시작하도록 OneDrive를 구성합니다.

    ```cmd
    REG ADD "HKLM\Software\Microsoft\Windows\CurrentVersion\Run" /v OneDrive /t REG_SZ /d "C:\Program Files (x86)\Microsoft OneDrive\OneDrive.exe /background" /f
    ```

7. 다음 명령을 실행하여 **사용자 계정 자동 구성** 을 사용하도록 설정합니다.

    ```cmd
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\OneDrive" /v "SilentAccountConfig" /t REG_DWORD /d 1 /f
    ```

8. 다음 명령을 실행하여 Windows의 알려진 폴더를 OneDrive로 리디렉션하고 이동합니다.

    ```cmd
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\OneDrive" /v "KFMSilentOptIn" /t REG_SZ /d "<your-AzureAdTenantId>" /f
    ```

## <a name="microsoft-teams-and-skype-for-business"></a>Microsoft Teams 및 비즈니스용 Skype

Azure Virtual Desktop은 비즈니스용 Skype를 지원하지 않습니다.

Microsoft Teams 설치에 대한 도움말은 [Azure Virtual Desktop에서 Microsoft Teams 사용](./teams-on-avd.md)을 참조하세요. 

## <a name="next-steps"></a>다음 단계

이제 이미지에 Office를 추가했으므로 마스터 VHD 이미지를 계속 사용자 지정할 수 있습니다. [마스터 VHD 이미지 준비 및 사용자 지정](set-up-customize-master-image.md)을 참조하세요.