---
title: Windows 가상 데스크톱에서 Windows 10 Vm에 언어 팩 설치-Azure
description: Windows 가상 데스크톱에서 Windows 10 다중 세션 Vm 용 언어 팩을 설치 하는 방법
author: Heidilohr
ms.topic: how-to
ms.date: 11/06/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 10d79d08e3f6ed422f0354074ebc6e0acc125553
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/07/2020
ms.locfileid: "94354039"
---
# <a name="add-language-packs-to-a-windows-10-multi-session-image"></a>Windows 10 다중 세션 이미지에 언어 팩 추가

Windows 가상 데스크톱은 사용자가 언제 어디서 나 배포할 수 있는 서비스입니다. 따라서 사용자가 Windows 10 Enterprise 다중 세션 이미지가 표시 하는 언어를 사용자 지정할 수 있는 것이 중요 합니다.

사용자의 언어 요구를 수용할 수 있는 두 가지 방법이 있습니다.

- 각 언어에 대 한 사용자 지정 이미지를 사용 하 여 전용 호스트 풀을 빌드합니다.
- 동일한 호스트 풀에 다양 한 언어 및 지역화 요구 사항이 있지만 사용자가 필요한 언어를 선택할 수 있도록 해당 이미지를 사용자 지정 합니다.

후자의 방법은 훨씬 효율적이 고 비용 효율적입니다. 그러나 사용자의 요구에 가장 적합 한 방법을 결정 하는 것은 사용자의 결정입니다. 이 문서에서는 이미지에 대 한 언어를 사용자 지정 하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>사전 요구 사항

여러 언어를 추가 하려면 Windows 10 Enterprise 다중 세션 이미지를 사용자 지정 하려면 다음 항목이 필요 합니다.

- Windows 10 Enterprise 다중 세션 버전 1903 이상을 사용 하는 Azure VM (가상 머신)

- 이미지에서 사용 하는 OS 버전의 언어 ISO, OD (주문형 기능) 디스크 1 및 수신함 앱 ISO 에서 다운로드할 수 있습니다.
     
     - 언어 ISO:
        - [Windows 10, 버전 1903 또는 1909 언어 팩 ISO](https://software-download.microsoft.com/download/pr/18362.1.190318-1202.19h1_release_CLIENTLANGPACKDVD_OEM_MULTI.iso)
        - [Windows 10, 버전 2004 언어 팩 ISO](https://software-download.microsoft.com/download/pr/19041.1.191206-1406.vb_release_CLIENTLANGPACKDVD_OEM_MULTI.iso)

     - D 디스크 1 ISO:
        - [Windows 10, 버전 1903 또는 1909 OD 디스크 1 ISO](https://software-download.microsoft.com/download/pr/18362.1.190318-1202.19h1_release_amd64fre_FOD-PACKAGES_OEM_PT1_amd64fre_MULTI.iso)
        - [Windows 10, 버전 2004 OD 디스크 1 ISO](https://software-download.microsoft.com/download/pr/19041.1.191206-1406.vb_release_amd64fre_FOD-PACKAGES_OEM_PT1_amd64fre_MULTI.iso)
        
     - 수신함 앱 ISO:
        - [Windows 10, 버전 1903 또는 1909 수신함 앱 ISO](https://software-download.microsoft.com/download/pr/18362.1.190318-1202.19h1_release_amd64fre_InboxApps.iso)
        - [Windows 10, 버전 2004 수신함 앱 ISO](https://software-download.microsoft.com/download/pr/19041.1.191206-1406.vb_release_amd64fre_InboxApps.iso)
        - [Windows 10, 버전 20H2 수신함 앱 ISO](https://software-download.microsoft.com/download/pr/19041.508.200905-1327.vb_release_svc_prod1_amd64fre_InboxApps.iso)
     
     - Windows 10, 버전 2004 또는 20H2를 사용 하는 경우 LXP (Local Experience Pack) Iso를 사용 하 여 새 언어를 다운로드할 수 있습니다. [Windows 10에서 언어 추가:](/windows-hardware/manufacture/desktop/language-packs-known-issue) 9B 했으면 또는 9C 버전을 다운로드 해야 하는지 여부를 파악 하기 위한 알려진 문제에 대 한 정보를 사용 합니다.
        - [Windows 10, 버전 2004 또는 20H2 **9b 했으면** LXP ISO](https://software-download.microsoft.com/download/pr/Win_10_2004_64_ARM64_MultiLang_LangPckAll_LIP_LXP_ONLY)
        - [Windows 10, 버전 2004 또는 20H2 **9C** LXP ISO](https://software-download.microsoft.com/download/pr/Win_10_2004_32_64_ARM64_MultiLng_LngPkAll_LIP_9C_LXP_ONLY) 

- Windows 파일 서버 가상 컴퓨터의 Azure Files 공유 또는 파일 공유

>[!NOTE]
>사용자 지정 이미지를 만드는 데 사용할 Azure VM에서 파일 공유 (리포지토리)에 액세스할 수 있어야 합니다.

## <a name="create-a-content-repository-for-language-packages-and-features-on-demand"></a>언어 패키지 및 주문형 기능에 대 한 콘텐츠 리포지토리 만들기

언어 패키지에 대 한 콘텐츠 리포지토리와 수신함 앱 패키지에 대 한 리포지토리를 만들려면 다음을 수행 합니다.

1. Azure VM에서 [필수 구성 요소](#prerequisites)링크의 Windows 10 Enterprise 다중 세션, 버전 1903/1909 및 2004 이미지에 대 한 Windows 10 다국어 ISO, Ods 및 수신함 앱을 다운로드 합니다.

2. VM에서 ISO 파일을 열고 탑재 합니다.

3. 언어 팩 ISO로 이동 하 여 **LocalExperiencePacks** 및 **x64 \\ langpacks** 폴더에서 콘텐츠를 복사한 다음 파일 공유에 내용을 붙여넣습니다.

4. 해당 콘텐츠를 모두 복사 하 여 파일 공유에 붙여 넣는 방법으로는 **d ISO 파일로** 이동 합니다.
5. 받은 편지함 앱 ISO의 **amd64fre** 폴더로 이동 하 고 준비한 수신함 앱에 대 한 저장소의 콘텐츠를 복사 합니다.

     >[!NOTE]
     > 제한 된 저장소로 작업 하는 경우 사용자에 게 필요한 언어용 파일만 복사 합니다. 파일 이름에서 언어 코드를 살펴보면 파일을 구분할 수 있습니다. 예를 들어 프랑스어 파일의 이름에는 "fr-fr" 코드가 있습니다. 사용 가능한 모든 언어에 대 한 언어 코드의 전체 목록은 [Windows 용 사용 가능한 언어 팩](/windows-hardware/manufacture/desktop/available-language-packs-for-windows)을 참조 하세요.

     >[!IMPORTANT]
     > 일부 언어에는 다른 명명 규칙을 따르는 위성 패키지에 추가 글꼴이 포함 되어야 합니다. 예를 들어 일본어 글꼴 파일 이름에는 "Jpan"이 포함 됩니다.
     >
     > [!div class="mx-imgBorder"]
     > ![파일 이름에 "Jpan" 언어 태그가 있는 일본어 언어 팩의 예입니다.](media/language-pack-example.png)

6. 사용자 지정 이미지를 작성 하는 데 사용할 VM에서 읽기 권한이 있도록 언어 콘텐츠 리포지토리 공유에 대 한 사용 권한을 설정 합니다.

## <a name="create-a-custom-windows-10-enterprise-multi-session-image-manually"></a>수동으로 사용자 지정 Windows 10 Enterprise 다중 세션 이미지 만들기

사용자 지정 Windows 10 Enterprise 다중 세션 이미지를 수동으로 만들려면 다음을 수행 합니다.

1. Azure VM을 배포 하 고 Azure 갤러리로 이동 하 여 사용 중인 Windows 10 Enterprise 다중 세션의 현재 버전을 선택 합니다.
2. VM을 배포한 후 로컬 관리자로 RDP를 사용 하 여 연결 합니다.
3. VM에 최신 Windows 업데이트가 모두 있는지 확인 합니다. 업데이트를 다운로드 하 고 필요한 경우 VM을 다시 시작 합니다.
4. 언어 패키지, 패키지 및 수신함 앱 파일 공유 리포지토리에 연결 하 여 문자 드라이브 (예: E 드라이브)에 탑재 합니다.

## <a name="create-a-custom-windows-10-enterprise-multi-session-image-automatically"></a>사용자 지정 Windows 10 Enterprise 다중 세션 이미지 자동 생성

자동화 된 프로세스를 통해 언어를 설치 하려는 경우 PowerShell에서 스크립트를 설정할 수 있습니다. 다음 스크립트 샘플을 사용 하 여 스페인어 (스페인), 프랑스어 (프랑스) 및 중국어 (PRC) 언어 팩과 Windows 10 Enterprise 다중 세션 버전 2004 용 위성 패키지를 설치할 수 있습니다. 스크립트는 언어 인터페이스 팩과 필요한 모든 위성 패키지를 이미지에 통합 합니다. 그러나이 스크립트를 수정 하 여 다른 언어를 설치할 수도 있습니다. 관리자 권한 PowerShell 세션에서 스크립트를 실행 해야 합니다. 그렇지 않으면 작동 하지 않습니다.

```powershell
########################################################
## Add Languages to running Windows Image for Capture##
########################################################

##Disable Language Pack Cleanup##
Disable-ScheduledTask -TaskPath "\Microsoft\Windows\AppxDeploymentClient\" -TaskName "Pre-staged app cleanup"

##Set Language Pack Content Stores##
[string]$LIPContent = "E:"

##Spanish##
Add-AppProvisionedPackage -Online -PackagePath $LIPContent\es-es\LanguageExperiencePack.es-es.Neutral.appx -LicensePath $LIPContent\es-es\License.xml
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Client-Language-Pack_x64_es-es.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Basic-es-es-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Handwriting-es-es-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-OCR-es-es-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Speech-es-es-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-TextToSpeech-es-es-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-NetFx3-OnDemand-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-InternetExplorer-Optional-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-MSPaint-FoD-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Notepad-FoD-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-PowerShell-ISE-FOD-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Printing-WFS-FoD-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-StepsRecorder-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-WordPad-FoD-Package~31bf3856ad364e35~amd64~es-es~.cab
$LanguageList = Get-WinUserLanguageList
$LanguageList.Add("es-es")
Set-WinUserLanguageList $LanguageList -force

##French##
Add-AppProvisionedPackage -Online -PackagePath $LIPContent\fr-fr\LanguageExperiencePack.fr-fr.Neutral.appx -LicensePath $LIPContent\fr-fr\License.xml
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Client-Language-Pack_x64_fr-fr.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Basic-fr-fr-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Handwriting-fr-fr-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-OCR-fr-fr-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Speech-fr-fr-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-TextToSpeech-fr-fr-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-NetFx3-OnDemand-Package~31bf3856ad364e35~amd64~fr-fr~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-InternetExplorer-Optional-Package~31bf3856ad364e35~amd64~fr-FR~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-MSPaint-FoD-Package~31bf3856ad364e35~amd64~fr-FR~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Notepad-FoD-Package~31bf3856ad364e35~amd64~fr-FR~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-PowerShell-ISE-FOD-Package~31bf3856ad364e35~amd64~fr-FR~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Printing-WFS-FoD-Package~31bf3856ad364e35~amd64~fr-FR~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-StepsRecorder-Package~31bf3856ad364e35~amd64~fr-FR~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-WordPad-FoD-Package~31bf3856ad364e35~amd64~fr-FR~.cab
$LanguageList = Get-WinUserLanguageList
$LanguageList.Add("fr-fr")
Set-WinUserLanguageList $LanguageList -force

##Chinese(PRC)##
Add-AppProvisionedPackage -Online -PackagePath $LIPContent\zh-cn\LanguageExperiencePack.zh-cn.Neutral.appx -LicensePath $LIPContent\zh-cn\License.xml
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Client-Language-Pack_x64_zh-cn.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Basic-zh-cn-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Fonts-Hans-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Handwriting-zh-cn-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-OCR-zh-cn-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Speech-zh-cn-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-TextToSpeech-zh-cn-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-NetFx3-OnDemand-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-InternetExplorer-Optional-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-MSPaint-FoD-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Notepad-FoD-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-PowerShell-ISE-FOD-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Printing-WFS-FoD-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-StepsRecorder-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-WordPad-FoD-Package~31bf3856ad364e35~amd64~zh-cn~.cab
$LanguageList = Get-WinUserLanguageList
$LanguageList.Add("zh-cn")
Set-WinUserLanguageList $LanguageList -force
```

>[!IMPORTANT]
>Windows 10 Enterprise 버전 1903 및 1909에는 패키지 파일이 필요 하지 않습니다 `Microsoft-Windows-Client-Language-Pack_x64_<language-code>.cab` .

스크립트는 설치 해야 하는 언어의 수에 따라 다소 시간이 걸릴 수 있습니다.

스크립트 실행이 완료 되 면 **시작**  >  **설정**  >  **시간 & 언어**  >  **언어** 로 이동 하 여 언어 팩이 올바르게 설치 되었는지 확인 합니다. 언어 파일이 있으면 모든 설정이 완료 됩니다.

Windows 이미지에 언어를 추가 하 고 나면 추가 된 언어를 지원 하도록 수신함 앱도 업데이트 해야 합니다. 이 작업은 수신함 앱 ISO의 콘텐츠로 사전 설치 된 앱을 새로 고쳐 수행할 수 있습니다. 연결 되지 않은 환경에서 새로 고침을 수행 하려면 (VM에서 인터넷에 액세스할 수 없음) 다음 PowerShell 스크립트 샘플을 사용 하 여 프로세스를 자동화할 수 있습니다.

```powershell
#########################################
## Update Inbox Apps for Multi Language##
#########################################
##Set Inbox App Package Content Stores##
[string]$InboxApps = "F:\"
##Update Inbox Store Apps##
$AllAppx = Get-Item $inboxapps\*.appx | Select-Object name
$AllAppxBundles = Get-Item $inboxapps\*.appxbundle | Select-Object name
$allAppxXML = Get-Item $inboxapps\*.xml | Select-Object name
foreach ($Appx in $AllAppx) {
    $appname = $appx.name.substring(0,$Appx.name.length-5)
    $appnamexml = $appname + ".xml"
    $pathappx = $InboxApps + "\" + $appx.Name
    $pathxml = $InboxApps + "\" + $appnamexml
    
    if($allAppxXML.name.Contains($appnamexml)){
    
    Write-Host "Handeling with xml $appname"  
  
    Add-AppxProvisionedPackage -Online -PackagePath $pathappx -LicensePath $pathxml
    } else {
      
      Write-Host "Handeling without xml $appname"
      
      Add-AppxProvisionedPackage -Online -PackagePath $pathappx -skiplicense
    }
}
foreach ($Appx in $AllAppxBundles) {
    $appname = $appx.name.substring(0,$Appx.name.length-11)
    $appnamexml = $appname + ".xml"
    $pathappx = $InboxApps + "\" + $appx.Name
    $pathxml = $InboxApps + "\" + $appnamexml
    
    if($allAppxXML.name.Contains($appnamexml)){
    Write-Host "Handeling with xml $appname"
    
    Add-AppxProvisionedPackage -Online -PackagePath $pathappx -LicensePath $pathxml
    } else {
       Write-Host "Handeling without xml $appname"
      Add-AppxProvisionedPackage -Online -PackagePath $pathappx -skiplicense
    }
}
```

>[!IMPORTANT]
>ISO에 포함 된 수신함 앱은 미리 설치 된 Windows 앱의 최신 버전이 아닙니다. 모든 앱의 최신 버전을 가져오려면 Windows 스토어 앱을 사용 하 여 앱을 업데이트 하 고 추가 언어를 설치한 후 업데이트를 수동으로 검색 해야 합니다.

완료 되 면 공유의 연결을 끊어야 합니다.

## <a name="finish-customizing-your-image"></a>이미지 사용자 지정 완료

언어 팩을 설치한 후에는 사용자 지정 이미지에 추가 하려는 다른 소프트웨어를 설치할 수 있습니다.

이미지 사용자 지정을 완료 한 후에는 시스템 준비 도구 (sysprep)를 실행 해야 합니다.

Sysprep를 실행 하려면:

1. 관리자 권한 명령 프롬프트를 열고 다음 명령을 실행 하 여 이미지를 일반화 합니다.  
   
     ```cmd
     C:\Windows\System32\Sysprep\sysprep.exe /oobe /generalize /shutdown
     ```

2. [Azure에서 일반화 된 vm의 관리 되는 이미지 만들기](../virtual-machines/windows/capture-image-resource.md)의 지침에 따라 VM을 중지 하 고 관리 되는 이미지에서 캡처합니다.

3. 이제 사용자 지정 이미지를 사용 하 여 Windows 가상 데스크톱 호스트 풀을 배포할 수 있습니다. 호스트 풀을 배포 하는 방법에 대 한 자세한 내용은 [자습서: Azure Portal 사용 하 여 호스트 풀 만들기](create-host-pools-azure-marketplace.md)를 참조 하세요.

## <a name="enable-languages-in-windows-settings-app"></a>Windows 설정 앱에서 언어 사용

마지막으로, 호스트 풀을 배포한 후 설정 메뉴에서 원하는 언어를 선택할 수 있도록 각 사용자의 언어 목록에 언어를 추가 해야 합니다.

사용자가 설치한 언어를 선택할 수 있도록 하려면 사용자로 로그인 한 후 다음 PowerShell cmdlet을 실행 하 여 설치 된 언어 팩을 언어 메뉴에 추가 합니다. 사용자가 세션에 로그인 할 때 활성화 되는 자동화 된 태스크 또는 로그온 스크립트로이 스크립트를 설정할 수도 있습니다.

```powershell
$LanguageList = Get-WinUserLanguageList
$LanguageList.Add("es-es")
$LanguageList.Add("fr-fr")
$LanguageList.Add("zh-cn")
Set-WinUserLanguageList $LanguageList -force
```

사용자가 언어 설정을 변경한 후 변경 내용을 적용 하려면 Windows 가상 데스크톱 세션에서 로그 아웃 하 고 다시 로그인 해야 합니다. 

## <a name="next-steps"></a>다음 단계

언어 팩에 대 한 알려진 문제에 대 한 자세한 내용은 [Windows 10에서 언어 팩 추가, 버전 1803 이상 버전: 알려진 문제](/windows-hardware/manufacture/desktop/language-packs-known-issue)를 참조 하세요.

Windows 10 Enterprise 다중 세션에 대해 다른 질문이 있는 경우 [FAQ](windows-10-multisession-faq.md)를 확인 하세요.
