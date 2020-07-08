---
title: 마스터 VHD 이미지에 Office 설치-Azure
description: Windows 가상 데스크톱 마스터 이미지에서 Office를 설치 하 고 Azure에 사용자 지정 하는 방법을 설명 합니다.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 05/02/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 3e213ac7a4d0436cf904a8104cea7e76eabaece4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85200531"
---
# <a name="install-office-on-a-master-vhd-image"></a>마스터 VHD 이미지에 Office 설치

이 문서에서는 Azure에 업로드할 수 있도록 마스터 VHD (가상 하드 디스크) 이미지에 enterprise, OneDrive 및 기타 일반 응용 프로그램에 대 한 Microsoft 365 앱을 설치 하는 방법을 설명 합니다. 사용자가 특정 LOB (기간 업무) 응용 프로그램에 액세스 해야 하는 경우이 문서의 지침을 완료 한 후에 설치 하는 것이 좋습니다.

이 문서에서는 VM (가상 머신)을 이미 만들었다고 가정 합니다. 그렇지 않은 경우에 [는 마스터 VHD 이미지 준비 및 사용자 지정](set-up-customize-master-image.md#create-a-vm) 을 참조 하세요.

또한이 문서에서는 Azure 또는 Hyper-v 관리자에서 프로 비전 된 VM에 대 한 관리자 액세스 권한이 있다고 가정 합니다. 그렇지 않은 경우 [모든 Azure 구독 및 관리 그룹을 관리 하기 위해 액세스 권한 상승](../role-based-access-control/elevate-access-global-admin.md)을 참조 하세요.

>[!NOTE]
>이러한 지침은 조직의 기존 프로세스에 사용할 수 있는 Windows Virtual Desktop 관련 구성에 대한 것입니다.

## <a name="install-office-in-shared-computer-activation-mode"></a>공유 컴퓨터 정품 인증 모드로 Office 설치

공유 컴퓨터 활성화를 사용 하면 여러 사용자가 액세스 하는 조직의 컴퓨터에 엔터프라이즈 용 Microsoft 365 앱을 배포할 수 있습니다. 공유 컴퓨터 정품 인증에 대 한 자세한 내용은 [Microsoft 365 앱에 대 한 공유 컴퓨터 정품 인증 개요](/deployoffice/overview-shared-computer-activation)를 참조 하세요.

Office [배포 도구](https://www.microsoft.com/download/details.aspx?id=49117) 를 사용 하 여 office를 설치 합니다. Windows 10 Enterprise 다중 세션은 다음 버전의 Office만 지원 합니다.

   - 엔터프라이즈용 Microsoft 365 앱
   - Microsoft 365 Business Premium 구독과 함께 제공 되는 비즈니스용 앱 Microsoft 365

Office 배포 도구를 사용 하려면 구성 XML 파일이 필요 합니다. 다음 샘플을 사용자 지정 하려면 [Office 배포 도구에 대 한 구성 옵션](/deployoffice/configuration-options-for-the-office-2016-deployment-tool/)을 참조 하세요.

제공 된이 샘플 구성 XML은 다음 작업을 수행 합니다.

   - 월별 엔터프라이즈 채널에서 Office를 설치 하 고 월별 엔터프라이즈 채널에서 업데이트를 제공 합니다.
   - X64 아키텍처를 사용 합니다.
   - 자동 업데이트를 사용 하지 않습니다.
   - 기존 Office 설치를 제거 하 고 설정을 마이그레이션합니다.
   - 공유 컴퓨터 활성화를 사용 하도록 설정 합니다.

>[!NOTE]
>Visio의 스텐실 검색 기능이 Windows 가상 데스크톱에서 예상 대로 작동 하지 않을 수 있습니다.

이 샘플 구성 XML은 다음과 같습니다.

   - 비즈니스용 Skype 설치
   - 사용자 단위 모드에서 OneDrive를 설치 합니다. 자세한 내용은 [컴퓨터별 모드로 OneDrive 설치](#install-onedrive-in-per-machine-mode)를 참조 하세요.

>[!NOTE]
>공유 컴퓨터 활성화는 Gpo (그룹 정책 개체) 또는 레지스트리 설정을 통해 설정할 수 있습니다. GPO는 **컴퓨터 구성 \\ 정책 \\ 관리 템플릿 \\ Microsoft Office 2016 (컴퓨터) \\ 라이선스 설정** 에 있습니다.

Office 배포 도구에는 setup.exe 포함 되어 있습니다. Office를 설치 하려면 명령줄에서 다음 명령을 실행 합니다.

```batch
Setup.exe /configure configuration.xml
```

#### <a name="sample-configurationxml"></a>샘플 configuration.xml

다음 XML 샘플에서는 월간 엔터프라이즈 채널 릴리스를 설치 합니다.

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
  <Logging Level=" Standard" Path="%temp%\WVDOfficeInstall" />
  <Property Name="FORCEAPPSHUTDOWN" Value="TRUE"/>
  <Property Name="SharedComputerLicensing" Value="1"/>
</Configuration>
```

>[!NOTE]
>Office 팀은 **OfficeClientEdition** 매개 변수에 64 비트 설치를 사용 하는 것을 권장 합니다.

Office를 설치한 후 기본 Office 동작을 업데이트할 수 있습니다. 다음 명령을 개별적으로 실행 하거나 배치 파일을 실행 하 여 동작을 업데이트 합니다.

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

## <a name="install-onedrive-in-per-machine-mode"></a>컴퓨터 단위 모드에서 OneDrive 설치

OneDrive는 일반적으로 사용자별로 설치 됩니다. 이 환경에서 컴퓨터 별로 설치 되어야 합니다.

다음은 컴퓨터 단위 모드에서 OneDrive를 설치 하는 방법입니다.

1. 먼저, OneDrive 설치 관리자를 준비할 위치를 만듭니다. 로컬 디스크 폴더 또는 [ \\ \\ unc] (file://unc) 위치에 문제가 없습니다.

2. 이 링크를 사용 하 여 준비 위치로 OneDriveSetup.exe를 다운로드 합니다.<https://aka.ms/OneDriveWVD-Installer>

3. 생략 하 여 OneDrive에 office를 설치한 경우 **\<ExcludeApp ID="OneDrive" /\>** 다음 명령을 실행 하 여 관리자 권한 명령 프롬프트에서 기존 OneDrive 사용자별 설치를 제거 합니다.

    ```batch
    "[staged location]\OneDriveSetup.exe" /uninstall
    ```

4. 관리자 권한 명령 프롬프트에서이 명령을 실행 하 여 **Allusers 설치** 레지스트리 값을 설정 합니다.

    ```batch
    REG ADD "HKLM\Software\Microsoft\OneDrive" /v "AllUsersInstall" /t REG_DWORD /d 1 /reg:64
    ```

5. 다음 명령을 실행 하 여 컴퓨터 단위 모드에서 OneDrive를 설치 합니다.

    ```batch
    Run "[staged location]\OneDriveSetup.exe" /allusers
    ```

6. 다음 명령을 실행 하 여 모든 사용자에 대해 로그인 할 때 OneDrive를 시작 하도록 구성 합니다.

    ```batch
    REG ADD "HKLM\Software\Microsoft\Windows\CurrentVersion\Run" /v OneDrive /t REG_SZ /d "C:\Program Files (x86)\Microsoft OneDrive\OneDrive.exe /background" /f
    ```

7. 다음 명령을 실행 하 여 **사용자 계정 자동 구성** 을 사용 하도록 설정 합니다.

    ```batch
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\OneDrive" /v "SilentAccountConfig" /t REG_DWORD /d 1 /f
    ```

8. 다음 명령을 실행 하 여 Windows의 알려진 폴더를 OneDrive로 리디렉션하고 이동 합니다.

    ```batch
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\OneDrive" /v "KFMSilentOptIn" /t REG_SZ /d "<your-AzureAdTenantId>" /f
    ```

## <a name="microsoft-teams-and-skype-for-business"></a>Microsoft 팀과 비즈니스용 Skype

Windows 가상 데스크톱은 비즈니스용 Skype를 지원 하지 않습니다.

Microsoft 팀 설치에 대 한 도움말은 [Windows 가상 데스크톱에서 Microsoft 팀 사용](teams-on-wvd.md)을 참조 하세요. Windows 가상 데스크톱의 Microsoft 팀에 대 한 미디어 최적화는 미리 보기로 제공 됩니다.

## <a name="next-steps"></a>다음 단계

이제 이미지에 Office를 추가 했으므로 마스터 VHD 이미지를 계속 사용자 지정할 수 있습니다. [마스터 VHD 이미지 준비 및 사용자 지정을](set-up-customize-master-image.md)참조 하세요.
