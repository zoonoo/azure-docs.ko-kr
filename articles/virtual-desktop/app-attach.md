---
title: 윈도우 가상 데스크톱 MSIX 응용 프로그램 연결 - Azure
description: Windows 가상 데스크톱에 대 한 MSIX 응용 프로그램 연결 설정 하는 방법.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 12/14/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 20a82cbd7de4b5678648bac19ab9b59bf557b0ff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79128326"
---
# <a name="set-up-msix-app-attach"></a>MSIX 앱 연결 설정

> [!IMPORTANT]
> MSIX 앱 첨부는 현재 공개 미리 보기 상태입니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

이 항목에서는 Windows 가상 데스크톱 환경에서 MSIX 앱 연결 설정 방법을 설명합니다.

## <a name="requirements"></a>요구 사항

시작하기 전에 MSIX 앱 첨부를 구성하는 데 필요한 내용은 다음과 같습니다.

- Windows 참가자 포털에 액세스하여 MSIX 앱에 대한 지원을 통해 Windows 10 버전을 가져오면 API가 연결됩니다.
- 작동하는 Windows 가상 데스크톱 배포입니다. 자세한 내용은 [Windows 가상 데스크톱에서 테넌트 만들기를](tenant-setup-azure-active-directory.md)참조하십시오.
- MSIX 패키징 도구
- MSIX 패키지가 저장되는 Windows 가상 데스크톱 배포의 네트워크 공유

## <a name="get-the-os-image"></a>OS 이미지 받기

먼저 MSIX 앱에 사용할 OS 이미지를 얻어야 합니다. OS 이미지를 얻으려면 다음을 수행하십시오.

1. Windows [참가자 포털을](https://www.microsoft.com/software-download/windowsinsiderpreviewadvanced?wa=wsignin1.0) 열고 로그인합니다.

     >[!NOTE]
     >Windows 참가자 포털에 액세스하려면 Windows 참가자 프로그램의 구성원이어야 합니다. Windows 참가자 프로그램에 대한 자세한 내용은 [Windows 참가자 설명서를](/windows-insider/at-home/)참조하십시오.

2. 아래로 스크롤하여 **에디션 선택** 섹션으로 스크롤하고 Windows **10 내부자 미리 보기 엔터프라이즈(FAST) – 빌드 19035** 이상을 선택합니다.

3. **확인을**선택한 다음 사용할 언어를 선택한 다음 다시 **확인을** 선택합니다.
    
     >[!NOTE]
     >현재 영어는 이 기능으로 테스트된 유일한 언어입니다. 다른 언어를 선택할 수 있지만 의도한 대로 표시되지 않을 수 있습니다.
    
4. 다운로드 링크가 생성되면 **64비트 다운로드를** 선택하고 로컬 하드 디스크에 저장합니다.

## <a name="prepare-the-vhd-image-for-azure"></a>Azure용 VHD 이미지 준비 

시작하기 전에 마스터 VHD 이미지를 만들어야 합니다. 아직 마스터 VHD 이미지를 만들지 않은 경우 [마스터 VHD 이미지 준비 및 사용자 지정으로](set-up-customize-master-image.md) 이동하여 해당 지침에 따라 진행합니다. 

마스터 VHD 이미지를 만든 후에는 MSIX 앱 연결 응용 프로그램에 대한 자동 업데이트를 비활성화해야 합니다. 자동 업데이트를 사용하지 않도록 설정하려면 다음 명령을 높은 명령 프롬프트에서 실행해야 합니다.

```cmd
rem Disable Store auto update:

reg add HKLM\Software\Policies\Microsoft\WindowsStore /v AutoDownload /t REG_DWORD /d 0 /f
Schtasks /Change /Tn "\Microsoft\Windows\WindowsUpdate\Automatic app update" /Disable
Schtasks /Change /Tn "\Microsoft\Windows\WindowsUpdate\Scheduled Start" /Disable

rem Disable Content Delivery auto download apps that they want to promote to users:

reg add HKCU\Software\Microsoft\Windows\CurrentVersion\ContentDeliveryManager /v PreInstalledAppsEnabled /t REG_DWORD /d 0 /f

reg add HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\ContentDeliveryManager\Debug /v ContentDeliveryAllowedOverride /t REG_DWORD /d 0x2 /f

rem Disable Windows Update:

sc config wuauserv start=disabled
```

다음으로 Azure용 VM VHD를 준비하고 결과 VHD 디스크를 Azure에 업로드합니다. 자세한 내용은 [마스터 VHD 이미지 준비 및 사용자 지정을](set-up-customize-master-image.md)참조하십시오.

VHD를 Azure에 업로드한 후에는 Azure Marketplace 자습서를 [사용하여 호스트 풀 만들기의](create-host-pools-azure-marketplace.md) 지침에 따라 이 새 이미지를 기반으로 하는 호스트 풀을 만듭니다.

## <a name="prepare-the-application-for-msix-app-attach"></a>MSIX 앱 연결에 대한 응용 프로그램 준비 

이미 MSIX 패키지가 있는 경우 [Windows 가상 데스크톱 인프라 구성을](#configure-windows-virtual-desktop-infrastructure)건너뛰고. 레거시 응용 프로그램을 테스트하려면 [VM의 데스크톱 설치 프로그램에서 MSIX 패키지 만들기의 지침에](/windows/msix/packaging-tool/create-app-package-msi-vm/) 따라 레거시 응용 프로그램을 MSIX 패키지로 변환합니다.

## <a name="generate-a-vhd-or-vhdx-package-for-msix"></a>MSIX용 VHD 또는 VHDX 패키지 생성

패키지는 성능을 최적화하기 위해 VHD 또는 VHDX 형식으로 되어 있습니다. MSIX는 VHD 또는 VHDX 패키지가 제대로 작동하도록 요구합니다.

MSIX용 VHD 또는 VHDX 패키지를 생성하려면 다음을 수행하십시오.

1. [msixmgr 도구를 다운로드하고](https://aka.ms/msixmgr) 세션 호스트 VM 내의 폴더에 .zip 폴더를 저장합니다.

2. msixmgr 도구 .zip 폴더의 압축을 풀습니다.

3. msixmgr 도구를 압축 해제한 동일한 폴더에 소스 MSIX 패키지를 넣습니다.

4. PowerShell에서 다음 cmdlet을 실행하여 VHD를 만듭니다.

    ```powershell
    New-VHD -SizeBytes <size>MB -Path c:\temp\<name>.vhd -Dynamic -Confirm:$false
    ```

    >[!NOTE]
    >확장된 MSIX를 담을 수 있을 만큼 VHD 의 크기가 커야 합니다.*

5. 다음 cmdlet을 실행하여 새로 만든 VHD를 마운트합니다.

    ```powershell
    $vhdObject = Mount-VHD c:\temp\<name>.vhd -Passthru
    ```

6. 이 cmdlet을 실행하여 VHD를 초기화합니다.

    ```powershell
    $disk = Initialize-Disk -Passthru -Number $vhdObject.Number
    ```

7. 이 cmdlet을 실행하여 새 파티션을 만듭니다.

    ```powershell
    $partition = New-Partition -AssignDriveLetter -UseMaximumSize -DiskNumber $disk.Number
    ```

8. 이 cmdlet을 실행하여 파티션을 포맷합니다.

    ```powershell
    Format-Volume -FileSystem NTFS -Confirm:$false -DriveLetter $partition.DriveLetter -Force
    ```

9. 마운트된 VHD에 상위 폴더를 만듭니다. MSIX 앱 첨부에는 상위 폴더가 필요하므로 이 단계는 필수입니다. 원하는 대로 상위 폴더의 이름을 지정할 수 있습니다.

### <a name="expand-msix"></a>MSIX 확장

그런 다음 MSIX 이미지의 압축을 풀어 "확장"해야 합니다. MSIX 이미지의 압축을 풀려면 다음을 수행합니다.

1. 관리자로 명령 프롬프트를 열고 msixmgr 도구를 다운로드하고 압축을 풀었던 폴더로 이동합니다.

2. 다음 cmdlet을 실행하여 이전 섹션에서 생성하고 장착한 VHD에 MSIX의 압축을 풀수 있습니다.

    ```powershell
    msixmgr.exe -Unpack -packagePath <package>.msix -destination "f:\<name of folder you created earlier>" -applyacls
    ```

    압축 을 풀면 다음 메시지가 나타납니다.

    `Successfully unpacked and applied ACLs for package: <package name>.msix`

    >[!NOTE]
    > 네트워크 내에서 또는 인터넷에 연결되지 않은 장치에서 Microsoft 비즈니스 용 스토어(또는 교육용)의 패키지를 사용하는 경우 스토어에서 패키지 라이선스를 가져와 앱을 성공적으로 실행하도록 설치해야 합니다. [오프라인 패키지 사용을](#use-packages-offline)참조하십시오.

3. 탑재된 VHD로 이동하여 앱 폴더를 열고 패키지 콘텐츠가 있는지 확인합니다.

4. VHD를 분리합니다.

## <a name="configure-windows-virtual-desktop-infrastructure"></a>Windows 가상 데스크톱 인프라 구성

VHD가 읽기 전용 모드로 연결됨에 따라 설계상 단일 MSIX 확장 패키지(이전 섹션에서 만든 VHD)를 여러 세션 호스트 VM 간에 공유할 수 있습니다.

시작하기 전에 네트워크 공유가 다음 요구 사항을 충족하는지 확인합니다.

- 공유가 SMB와 호환됩니다.
- 세션 호스트 풀의 일부인 VM에는 공유에 대한 NTFS 권한이 있습니다.

### <a name="set-up-an-msix-app-attach-share"></a>MSIX 앱 연결 공유 설정 

Windows 가상 데스크톱 환경에서 네트워크 공유를 만들고 패키지를 이동합니다.

>[!NOTE]
> MSIX 네트워크 공유를 만드는 가장 좋은 방법은 NTFS 읽기 전용 권한으로 네트워크 공유를 설정하는 것입니다.

## <a name="install-certificates"></a>인증서 설치

앱에서 공개적으로 신뢰할 수 없거나 자체 서명된 인증서를 사용하는 경우 설치 방법은 다음과 같습니다.

1. 패키지를 마우스 오른쪽 단추로 클릭하고 **속성**을 선택합니다.
2. 표시되는 창에서 **디지털 서명 탭을 선택합니다.** 다음 이미지와 같이 탭의 목록에 항목이 하나만 있어야 합니다. 항목을 강조 표시할 항목을 선택한 다음 **세부 정보를**선택합니다.
3. 디지털 서명 세부 정보 창이 나타나면 **일반** 탭을 선택한 다음 **인증서 설치를**선택합니다.
4. 설치 프로그램이 열리면 **로컬 컴퓨터를** 저장소 위치로 선택한 다음 **다음**을 선택합니다.
5. 설치 프로그램에서 앱이 기기를 변경할 수 있도록 허용할지 묻는 메시지가 표시되면 **예.**
6. **다음 저장소에 모든 인증서 배치를**선택한 다음 **찾아보기를**선택합니다.
7. 선택된 인증서 저장소 창이 나타나면 **신뢰할 수 있는 사람을**선택한 다음 **확인을**선택합니다.
8. **마침**을 선택합니다.

## <a name="prepare-powershell-scripts-for-msix-app-attach"></a>MSIX 앱 첨부를 위한 PowerShell 스크립트 준비

MSIX 앱 연결에는 다음 순서로 수행해야 하는 네 가지 단계가 있습니다.

1. 단계
2. 등록
3. 등록 취소
4. 디스테이지 (것)이

각 단계는 PowerShell 스크립트를 만듭니다. 각 단계에 대한 샘플 스크립트는 [여기에서](https://github.com/Azure/RDS-Templates/tree/master/msix-app-attach)확인할 수 있습니다.

### <a name="stage-the-powershell-script"></a>파워쉘 스크립트 스테이지

PowerShell 스크립트를 업데이트하기 전에 VHD에 볼륨 의 볼륨 GUID가 있는지 확인하십시오. 볼륨 GUID를 얻으려면 다음을 수행하십시오.

1.  스크립트를 실행하는 VM 내부에 VHD가 있는 네트워크 공유를 엽니다.

2.  VHD를 마우스 오른쪽 단추로 클릭하고 **마운트를**선택합니다. 이렇게 하면 VHD가 드라이브 문자에 탑재됩니다.

3.  VHD를 탑재하면 **파일 탐색기** 창이 열립니다. 상위 폴더를 캡처하고 **$parentFolder** 변수를 업데이트합니다.

    >[!NOTE]
    >상위 폴더가 표시되지 않으면 MSIX가 제대로 확장되지 않았음을 의미합니다. 이전 섹션을 다시 보고 다시 시도하십시오.

4.  상위 폴더를 엽니다. 올바르게 확장된 경우 패키지와 이름이 같은 폴더가 표시됩니다. 이 폴더의 이름과 일치하도록 **$packageName** 변수를 업데이트합니다.

    `VSCodeUserSetup-x64-1.38.1_1.38.1.0_x64__8wekyb3d8bbwe`)을 입력합니다.

5.  명령 프롬프트를 열고 **mountvol을**입력합니다. 이 명령에는 볼륨 및 GUID 목록이 표시됩니다. 드라이브 문자가 VHD를 탑재한 드라이브와 2단계에서 일치하는 볼륨의 GUID를 복사합니다.

    예를 들어 mountvol 명령에 대한 이 예제 출력에서 VHD를 드라이브 C에 장착한 경우 위의 `C:\`값을 복사할 수 있습니다.

    ```cmd
    Possible values for VolumeName along with current mount points are:

    \\?\Volume{a12b3456-0000-0000-0000-10000000000}\
    *** NO MOUNT POINTS ***

    \\?\Volume{c78d9012-0000-0000-0000-20000000000}\
        E:\

    \\?\Volume{d34e5678-0000-0000-0000-30000000000}\
        C:\

    ```


6.  방금 복사한 볼륨 GUID로 **$volumeGuid** 변수를 업데이트합니다.

7. 관리자 PowerShell 프롬프트를 열고 환경에 적용되는 변수로 다음 PowerShell 스크립트를 업데이트합니다.

    ```powershell
    #MSIX app attach staging sample

    #region variables

    $vhdSrc="<path to vhd>"

    $packageName = "<package name>"

    $parentFolder = "<package parent folder>"

    $parentFolder = "\" + $parentFolder + "\"

    $volumeGuid = "<vol guid>"

    $msixJunction = "C:\temp\AppAttach\"

    #endregion

    #region mountvhd

    try

    {

    Mount-Diskimage -ImagePath $vhdSrc -NoDriveLetter -Access ReadOnly

    Write-Host ("Mounting of " + $vhdSrc + " was completed!") -BackgroundColor Green

    }

    catch

    {

    Write-Host ("Mounting of " + $vhdSrc + " has failed!") -BackgroundColor Red

    }

    #endregion

    #region makelink

    $msixDest = "\\?\Volume{" + $volumeGuid + "}\"

    if (!(Test-Path $msixJunction))

    {

    md $msixJunction

    }

    $msixJunction = $msixJunction + $packageName

    cmd.exe /c mklink /j $msixJunction $msixDest

    #endregion

    #region stage

    [Windows.Management.Deployment.PackageManager,Windows.Management.Deployment,ContentType=WindowsRuntime]
    | Out-Null

    Add-Type -AssemblyName System.Runtime.WindowsRuntime

    $asTask = ([System.WindowsRuntimeSystemExtensions].GetMethods() | Where {
    $_.ToString() -eq 'System.Threading.Tasks.Task`1[TResult]
    AsTask[TResult,TProgress](Windows.Foundation.IAsyncOperationWithProgress`2[TResult,TProgress])'})[0]

    $asTaskAsyncOperation =
    $asTask.MakeGenericMethod([Windows.Management.Deployment.DeploymentResult],
    [Windows.Management.Deployment.DeploymentProgress])

    $packageManager = [Windows.Management.Deployment.PackageManager]::new()

    $path = $msixJunction + $parentFolder + $packageName # needed if we do the pbisigned.vhd

    $path = ([System.Uri]$path).AbsoluteUri

    $asyncOperation = $packageManager.StagePackageAsync($path, $null, "StageInPlace")

    $task = $asTaskAsyncOperation.Invoke($null, @($asyncOperation))

    $task

    #endregion
    ```

### <a name="register-powershell-script"></a>파워쉘 스크립트 등록

레지스터 스크립트를 실행하려면 자리 표시자 값이 환경에 적용되는 값으로 대체된 다음 PowerShell cmdlet을 실행합니다.

```powershell
#MSIX app attach registration sample

#region variables

$packageName = "<package name>"

$path = "C:\Program Files\WindowsApps\" + $packageName + "\AppxManifest.xml"

#endregion

#region register

Add-AppxPackage -Path $path -DisableDevelopmentMode -Register

#endregion
```

### <a name="deregister-powershell-script"></a>파워쉘 스크립트 등록 취소

이 스크립트의 경우 **$packageName** 대한 자리 표시자를 테스트하는 패키지의 이름으로 바꿉니다.

```powershell
#MSIX app attach deregistration sample

#region variables

$packageName = "<package name>"

#endregion

#region deregister

Remove-AppxPackage -PreserveRoamableApplicationData $packageName

#endregion
```

### <a name="destage-powershell-script"></a>스테이지 해제 파워쉘 스크립트

이 스크립트의 경우 **$packageName** 대한 자리 표시자를 테스트하는 패키지의 이름으로 바꿉니다.

```powershell
#MSIX app attach de staging sample

#region variables

$packageName = "<package name>"

$msixJunction = "C:\temp\AppAttach\"

#endregion

#region deregister

Remove-AppxPackage -AllUsers -Package $packageName

cd $msixJunction

rmdir $packageName -Force -Verbose

#endregion
```

## <a name="set-up-simulation-scripts-for-the-msix-app-attach-agent"></a>MSIX 앱 연결 에이전트에 대한 시뮬레이션 스크립트 설정

스크립트를 만든 후 사용자가 수동으로 실행하거나 시작, 로그온, 로그오프 및 종료 스크립트로 자동으로 실행되도록 설정할 수 있습니다. 이러한 유형의 스크립트에 대한 자세한 내용은 [그룹 정책의 시작, 종료, 로그온 및 로그오프 스크립트 사용을](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn789196(v=ws.11)/)참조하십시오.

이러한 각 자동 스크립트는 앱 연결 스크립트의 한 단계를 실행합니다.

- 시작 스크립트는 스테이지 스크립트를 실행합니다.
- 로그온 스크립트는 레지스터 스크립트를 실행합니다.
- 로그오프 스크립트는 등록 취소 스크립트를 실행합니다.
- 종료 스크립트는 스테이지 해제 스크립트를 실행합니다.

## <a name="use-packages-offline"></a>오프라인 패키지 사용

[비즈니스용 Microsoft Store](https://businessstore.microsoft.com/) 또는 네트워크 내또는 인터넷에 연결되어 있지 않은 장치에서 Microsoft [스토어에서](https://educationstore.microsoft.com/) 패키지를 사용하는 경우 Microsoft Store에서 패키지 라이선스를 얻고 장치에 설치하여 앱을 성공적으로 실행해야 합니다. 장치가 온라인 상태입니다 및 비즈니스용 Microsoft 스토어에 연결할 수 있는 경우 필요한 라이선스가 자동으로 다운로드되어야 하지만 오프라인 인 경우 라이선스를 수동으로 설정해야 합니다. 

라이센스 파일을 설치하려면 WMI 브리지 공급자에서 MDM_EnterpriseModernAppManagement_StoreLicenses02_01 클래스를 호출하는 PowerShell 스크립트를 사용해야 합니다.  

오프라인 사용을 위해 라이선스를 설정하는 방법은 다음과 같습니다. 

1. 비즈니스용 Microsoft Store에서 앱 패키지, 라이선스 및 필수 프레임워크를 다운로드합니다. 인코딩된 라이센스 파일과 인코딩되지 않은 라이센스 파일이 모두 필요합니다. 자세한 다운로드 지침은 [여기에서](/microsoft-store/distribute-offline-apps#download-an-offline-licensed-app)찾을 수 있습니다.
2. 3단계에 대한 스크립트에서 다음 변수를 업데이트합니다.
      1. `$contentID`은 인코딩되지 않은 라이센스 파일(.xml)의 ContentID 값입니다. 원하는 텍스트 편집기에서 라이센스 파일을 열 수 있습니다.
      2. `$licenseBlob`는 인코딩된 라이센스 파일(.bin)의 라이센스 Blob의 전체 문자열입니다. 원하는 텍스트 편집기에서 인코딩된 라이센스 파일을 열 수 있습니다. 
3. 관리자 PowerShell 프롬프트에서 다음 스크립트를 실행합니다. 라이센스 설치를 수행하기에 좋은 장소는 관리자 프롬프트에서 실행해야 하는 [스테이징 스크립트의](#stage-the-powershell-script) 끝에 있습니다.

```powershell
$namespaceName = "root\cimv2\mdm\dmmap"
$className = "MDM_EnterpriseModernAppManagement_StoreLicenses02_01"
$methodName = "AddLicenseMethod"
$parentID = "./Vendor/MSFT/EnterpriseModernAppManagement/AppLicenses/StoreLicenses"

#TODO - Update $contentID with the ContentID value from the unencoded license file (.xml)
$contentID = "{'ContentID'_in_unencoded_license_file}"

#TODO - Update $licenseBlob with the entire String in the encoded license file (.bin)
$licenseBlob = "{Entire_String_in_encoded_license_file}"

$session = New-CimSession 

#The final string passed into the AddLicenseMethod should be of the form <License Content="encoded license blob" />
$licenseString = '<License Content='+ '"' + $licenseBlob +'"' + ' />' 

$params = New-Object Microsoft.Management.Infrastructure.CimMethodParametersCollection
$param = [Microsoft.Management.Infrastructure.CimMethodParameter]::Create("param",$licenseString ,"String", "In")
$params.Add($param) 


try
{
   $instance = New-CimInstance -Namespace $namespaceName -ClassName $className -Property @{ParentID=$parentID;InstanceID=$contentID}
   $session.InvokeMethod($namespaceName, $instance, $methodName, $params)

}
catch [Exception]
{
     write-host $_ | out-string
}  
```

## <a name="next-steps"></a>다음 단계

이 기능은 현재 지원되지 않지만 [Windows 가상 데스크톱 기술 커뮤니티에서](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)커뮤니티에 질문을 할 수 있습니다.

또한 Windows 가상 데스크톱 [피드백 허브에](https://aka.ms/MRSFeedbackHub)Windows 가상 데스크톱에 대한 피드백을 남기거나 MSIX 앱 첨부 피드백 허브 및 [MSIX 패키징 도구 피드백 허브에서](https://aka.ms/msixtoolfeedback) [MSIX 앱](https://aka.ms/msixappattachfeedback) 및 패키징 도구에 대한 피드백을 남길 수 있습니다.
