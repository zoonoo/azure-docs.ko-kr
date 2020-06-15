---
title: Windows Virtual Desktop MSIX app attach - Azure
description: Windows Virtual Desktop에 맞게 MSIX app attach을 설정하는 방법입니다.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: a222e5a0602a676872eb8119e565f243f2ecc1b4
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83742931"
---
# <a name="set-up-msix-app-attach"></a>MSIX 앱 연결 설정

> [!IMPORTANT]
> MSIX app attach는 현재 공개 미리 보기 상태입니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며, 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

이 항목에서는 Windows Virtual Desktop 환경에서 MSIX app attach를 설정하는 방법을 안내합니다.

## <a name="requirements"></a>요구 사항

시작하기 전에 MSIX app attach를 구성하는 데 필요한 사항은 다음과 같습니다.

- MSIX app attach API를 지원하는 Windows 10 버전을 확보하기 위한 Windows 참가자 포털에 대한 액세스 권한
- 작동하는 Windows Virtual Desktop 배포 자세한 내용은 [Windows Virtual Desktop에서 테넌트 만들기](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md)를 참조하세요.
- MSIX 패키징 도구
- MSIX 패키지를 저장할 Windows Virtual Desktop 배포의 네트워크 공유

## <a name="get-the-os-image"></a>OS 이미지 가져오기

먼저 MSIX 앱에 사용할 OS 이미지를 가져와야 합니다. OS 이미지를 가져오려면 다음을 수행합니다.

1. [Windows 참가자 포털](https://www.microsoft.com/software-download/windowsinsiderpreviewadvanced?wa=wsignin1.0)을 열고 로그인합니다.

     >[!NOTE]
     >Windows 참가자 포털에 액세스 하려면 Windows 참가자 프로그램의 멤버여야 합니다. Windows 참가자 프로그램에 대해 자세히 알아보려면 [Windows 참가자 설명서](/windows-insider/at-home/)를 참조하세요.

2. **Select edition**(버전 선택) 섹션까지 아래로 스크롤하여 **Windows 10 Insider Preview Enterprise(FAST) – 빌드 19041** 이상을 선택합니다.

3. **확인**을 선택한 다음, 사용할 언어를 선택하고 **확인**을 다시 선택합니다.
    
     >[!NOTE]
     >현재 이 기능으로 테스트한 유일한 언어는 영어입니다. 다른 언어를 선택할 수 있지만 의도한대로 표시되지 않을 수 있습니다.
    
4. 다운로드 링크가 생성되면 **64비트 다운로드**를 선택하여 로컬 하드 디스크에 저장합니다.

## <a name="prepare-the-vhd-image-for-azure"></a>Azure용 VHD 이미지 준비 

시작하기 전에 마스터 VHD 이미지를 만들어야 합니다. 마스터 VHD 이미지를 아직 만들지 않은 경우 [마스터 VHD 이미지 준비 및 사용자 지정](set-up-customize-master-image.md)으로 이동하여 지침을 따르세요. 

마스터 VHD 이미지를 만든 후에는 MSIX app attach 애플리케이션에 대한 자동 업데이트를 사용하지 않도록 설정해야 합니다. 자동 업데이트를 사용하지 않도록 설정하려면 관리자 권한 명령 프롬프트에서 다음 명령을 실행해야 합니다.

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

자동 업데이트를 사용하지 않도록 설정한 후에는 Hyper-V를 사용하도록 설정해야 합니다. Mound-VHD 명령을 사용하여 스테이징하고 Dismount-VHD를 사용하여 스테이징을 취소하기 때문입니다. 

```powershell
Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Hyper-V-All
```
>[!NOTE]
>이렇게 변경하면 가상 머신을 다시 시작해야 합니다.

그런 다음, Azure용 VM VHD를 준비하고 결과 VHD 디스크를 Azure에 업로드합니다. 자세한 내용은 [마스터 VHD 이미지 준비 및 사용자 지정](set-up-customize-master-image.md)을 참조하세요.

VHD를 Azure에 업로드한 후에는 [Azure Marketplace를 사용하여 호스트 풀 만들기](create-host-pools-azure-marketplace.md) 자습서의 지침에 따라 새 이미지에 기반하는 호스트 풀을 만듭니다.

## <a name="prepare-the-application-for-msix-app-attach"></a>MSIX app attach용 애플리케이션 준비 

MSIX 패키지가 이미 있는 경우 [Windows Virtual Desktop 인프라 구성](#configure-windows-virtual-desktop-infrastructure)으로 건너뜁니다. 레거시 애플리케이션을 테스트하려면 [VM의 데스크톱 설치 관리자에서 MSIX 패키지 만들기](/windows/msix/packaging-tool/create-app-package-msi-vm/)의 지침에 따라 레거시 애플리케이션을 MSIX 패키지로 변환합니다.

## <a name="generate-a-vhd-or-vhdx-package-for-msix"></a>MSIX용 VHD 또는 VHDX 패키지 생성

패키지는 성능을 최적화하기 위해 VHD 또는 VHDX 형식입니다. MSIX가 제대로 작동하려면 VHD 또는 VHDX 패키지가 필요합니다.

MSIX용 VHD 또는 VHDX 패키지를 생성하려면 다음을 수행합니다.

1. [msixmgr 도구를 다운로드](https://aka.ms/msixmgr)하고 .zip 폴더를 세션 호스트 VM 내 폴더에 저장합니다.

2. msixmgr 도구 .zip 폴더의 압축을 풉니다.

3. msixmgr 도구의 압축을 푼 폴더에 소스 MSIX 패키지를 넣습니다.

4. PowerShell에서 다음 cmdlet을 실행하여 VHD를 만듭니다.

    ```powershell
    New-VHD -SizeBytes <size>MB -Path c:\temp\<name>.vhd -Dynamic -Confirm:$false
    ```

    >[!NOTE]
    >VHD의 크기가 확장된 MSIX를 포함할 수 있을 만큼 큰지 확인해야 합니다.*

5. 다음 cmdlet을 실행하여 새로 만든 VHD를 탑재합니다.

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

9. 탑재된 VHD에 부모 폴더를 만듭니다. MSIX app attach에 부모 폴더가 필요하므로 이 단계는 필수입니다. 부모 폴더의 이름을 원하는 대로 지정합니다.

### <a name="expand-msix"></a>MSIX 확장

그런 다음, MSIX 이미지의 압축을 풀어서 "확장"해야 합니다. MSIX 이미지의 압축을 풀려면 다음을 수행합니다.

1. 관리자 권한으로 명령 프롬프트를 열고 msixmgr 도구를 다운로드하고 압축을 푼 폴더로 이동합니다.

2. 다음 cmdlet을 실행하여 이전 섹션에서 만들고 마운트한 VHD에 MSIX의 압축을 풉니다.

    ```powershell
    msixmgr.exe -Unpack -packagePath <package>.msix -destination "f:\<name of folder you created earlier>" -applyacls
    ```

    압축 풀기기 완료되면 다음 메시지가 나타납니다.

    `Successfully unpacked and applied ACLs for package: <package name>.msix`

    >[!NOTE]
    > 네트워크 내에서 비즈니스(또는 교육)용 Microsoft 스토어의 패키지를 사용하는 경우 또는 인터넷에 연결되지 않은 디바이스에서 패키지를 사용하는 경우에는 스토어에서 패키지 라이선스를 확보하여 설치해야 앱을 성공적으로 실행할 수 있습니다. [오프라인으로 패키지 사용](#use-packages-offline)을 참조하세요.

3. 탑재된 VHD로 이동하여 앱 폴더를 열고 패키지 콘텐츠가 있는지 확인합니다.

4. VHD를 분리합니다.

## <a name="configure-windows-virtual-desktop-infrastructure"></a>Windows Virtual Desktop 인프라 구성

기본적으로, 단일 MSIX 확장 패키지(이전 섹션에서 만든 VHD)는 VHD가 읽기 전용 모드로 연결되어 있기 때문에 여러 세션 호스트 VM 간에 공유할 수 있습니다.

시작하기 전에 네트워크 공유가 다음 요구 사항을 충족하는지 확인합니다.

- 공유가 SMB와 호환됩니다.
- 세션 호스트 풀의 일부인 VM에 공유할 수 있는 NTFS 권한이 있습니다.

### <a name="set-up-an-msix-app-attach-share"></a>MSIX app attach 공유 설정 

Windows Virtual Desktop 환경에서 네트워크 공유를 만들고 여기로 패키지를 옮깁니다.

>[!NOTE]
> MSIX 네트워크 공유를 만드는 가장 좋은 방법은 NTFS 읽기 전용 권한으로 네트워크 공유를 설정하는 것입니다.

## <a name="install-certificates"></a>인증서 설치

앱에 공개적으로 신뢰되지 않았거나 자체 서명된 인증서가 사용되는 경우 설치 방법은 다음과 같습니다.

1. 패키지를 마우스 오른쪽 단추로 클릭하고 **속성**을 선택합니다.
2. 창이 나타나면 **디지털 서명** 탭을 선택합니다. 다음 이미지와 같이, 탭의 목록에는 항목이 하나만 있어야 합니다. 해당 항목을 선택하여 항목을 강조 표시한 다음, **세부 정보**를 선택합니다.
3. 디지털 서명 정보 창이 나타나면 **일반** 탭을 선택한 다음, **인증서 설치**를 선택합니다.
4. 설치 관리자가 열리면 **로컬 머신**을 스토리지 위치로 선택하고 **다음**을 선택합니다.
5. 설치 관리자에 앱이 디바이스를 변경하도록 허용할지 묻는 메시지가 표시되면 **예**를 선택합니다.
6. **모든 인증서를 다음 저장소에 저장**을 선택한 다음, **찾아보기**를 선택합니다.
7. 인증서 저장소 선택 창이 나타나면 **신뢰할 수 있는 사용자**를 선택한 후 **확인**을 선택합니다.
8. **마침**을 선택합니다.

## <a name="prepare-powershell-scripts-for-msix-app-attach"></a>MSIX app attach용 PowerShell 스크립트 준비

MSIX app attach에는 다음 순서로 수행해야 하는 네 가지 단계가 있습니다.

1. 단계
2. 등록
3. 등록 취소
4. 스테이징 취소

각 단계는 PowerShell 스크립트를 만듭니다. 각 단계에 대한 샘플 스크립트는 [여기](https://github.com/Azure/RDS-Templates/tree/master/msix-app-attach)에 있습니다.

### <a name="stage-the-powershell-script"></a>PowerShell 스크립트 스테이징

PowerShell 스크립트를 업데이트하기 전에 VHD에 볼륨의 볼륨 GUID가 있는지 확인해야 합니다. 볼륨 GUID를 가져오려면 다음을 수행합니다.

1.  스크립트를 실행할 VM 내에서 VHD가 있는 네트워크 공유를 엽니다.

2.  VHD를 마우스 오른쪽 단추로 클릭하고 **탑재**를 선택합니다. 그러면 VHD가 드라이브 문자에 탑재됩니다.

3.  VHD를 탑재하면 **파일 탐색기** 창이 열립니다. 부모 폴더를 캡처하고 **$parentFolder** 변수를 업데이트합니다.

    >[!NOTE]
    >부모 폴더가 보이지 않으면 MSIX가 제대로 확장되지 않았다는 의미입니다. 이전 섹션을 다시 실행하고 다시 시도합니다.

4.  부모 폴더를 엽니다. 올바르게 확장된 경우 패키지와 이름이 같은 폴더가 보입니다. 이 폴더의 이름과 일치하도록 **$packageName** 변수를 업데이트합니다.

    `VSCodeUserSetup-x64-1.38.1_1.38.1.0_x64__8wekyb3d8bbwe`)을 입력합니다.

5.  명령 프롬프트를 열고 **mountvol**을 입력합니다. 이 명령은 볼륨 목록과 해당 GUID를 표시합니다. 2단계에서 드라이브 문자가 VHD를 마운트한 드라이브와 일치하는 볼륨의 GUID를 복사합니다.

    예를 들어, mountvol 명령에 대한 예제 출력에서 VHD를 C 드라이브에 탑재한 경우 `C:\` 위의 값을 복사합니다.

    ```cmd
    Possible values for VolumeName along with current mount points are:

    \\?\Volume{a12b3456-0000-0000-0000-10000000000}\
    *** NO MOUNT POINTS ***

    \\?\Volume{c78d9012-0000-0000-0000-20000000000}\
        E:\

    \\?\Volume{d34e5678-0000-0000-0000-30000000000}\
        C:\

    ```


6.  방금 복사한 볼륨 GUID를 사용하여 **$volumeGuid** 변수를 업데이트합니다.

7. 관리 PowerShell 프롬프트를 열고 환경에 적용되는 변수로 다음 PowerShell 스크립트를 업데이트합니다.

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

    Mount-VHD -Path $vhdSrc -NoDriveLetter -ReadOnly

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

### <a name="register-powershell-script"></a>PowerShell 스크립트 등록

등록 스크립트를 실행하려면 다음 PowerShell cmdlet에서 자리 표시자 값을 사용자 환경에 적용되는 값으로 대체하여 실행합니다.

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

### <a name="deregister-powershell-script"></a>PowerShell 스크립트 등록 취소

이 스크립트의 경우 **$packageName**의 자리 표시자를 테스트 중인 패키지의 이름으로 바꿉니다.

```powershell
#MSIX app attach deregistration sample

#region variables

$packageName = "<package name>"

#endregion

#region deregister

Remove-AppxPackage -PreserveRoamableApplicationData $packageName

#endregion
```

### <a name="destage-powershell-script"></a>PowerShell 스크립트 스테이징 취소

이 스크립트의 경우 **$packageName**의 자리 표시자를 테스트 중인 패키지의 이름으로 바꿉니다.

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

## <a name="set-up-simulation-scripts-for-the-msix-app-attach-agent"></a>MSIX app attach 에이전트에 대한 시뮬레이션 스크립트 설정

스크립트를 만들면 이 스크립트를 사용자가 수동으로 실행하거나 시작, 로그온, 로그오프, 종료 스크립트로 자동 실행되도록 설정할 수 있습니다. 이러한 유형의 스크립트에 대해 자세히 알아보려면 [그룹 정책에서 시작, 종료, 로그온, 로그오프 스크립트 사용](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn789196(v=ws.11)/)을 참조하세요.

이러한 자동 스크립트 각각은 app attach 스크립트의 한 단계를 실행합니다.

- 시작 스크립트는 스테이징 스크립트를 실행합니다.
- 로그온 스크립트는 등록 스크립트를 실행합니다.
- 로그오프 스크립트는 등록 취소 스크립트를 실행합니다.
- 종료 스크립트는 스테이징 취소 스크립트를 실행합니다.

## <a name="use-packages-offline"></a>오프라인으로 패키지 사용

네트워크 내에서 [비즈니스용 Microsoft 스토어](https://businessstore.microsoft.com/) 또는 [교육용 Microsoft 스토어](https://educationstore.microsoft.com/)의 패키지를 사용하는 경우 또는 인터넷에 연결되지 않은 디바이스에서 패키지를 사용하는 경우에는 Microsoft Store에서 패키지 라이선스를 확보하여 디바이스에 설치해야 앱을 성공적으로 실행할 수 있습니다. 디바이스가 온라인 상태이고 비즈니스용 Microsoft 스토어에 연결할 수 있으면 필요한 라이선스가 자동으로 다운로드되지만 오프라인이면 라이선스를 수동으로 설정해야 합니다. 

라이선스 파일을 설치하려면 WMI 브리지 공급자에서 MDM_EnterpriseModernAppManagement_StoreLicenses02_01 클래스를 호출하는 PowerShell 스크립트를 사용해야 합니다.  

오프라인 사용을 위해 라이선스를 설정하는 방법은 다음과 같습니다. 

1. 비즈니스용 Microsoft 스토어에서 앱 패키지, 라이선스 및 필수 프레임워크를 다운로드합니다. 인코딩된 라이선스 파일과 인코딩되지 않은 라이선스 파일이 모두 필요합니다. 자세한 다운로드 지침은 [여기](/microsoft-store/distribute-offline-apps#download-an-offline-licensed-app)에서 찾을 수 있습니다.
2. 3단계의 스크립트에서 다음 변수를 업데이트합니다.
      1. `$contentID`는 인코딩되지 않은 라이선스 파일(.xml)의 ContentID 값입니다. 원하는 텍스트 편집기에서 라이선스 파일을 열 수 있습니다.
      2. `$licenseBlob`은 인코딩된 라이선스 파일(. bin)의 라이선스 Blob에 대한 전체 문자열입니다. 원하는 텍스트 편집기에서 인코딩된 라이선스 파일을 열 수 있습니다. 
3. 관리자 PowerShell 프롬프트에서 다음 스크립트를 실행합니다. 라이선스 설치를 수행하기에 좋은 위치는 [스테이징 스크립트](#stage-the-powershell-script)의 끝이며 관리자 프롬프트에서도 실행해야 합니다.

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

이 기능은 현재 지원되지 않지만 [Windows Virtual Desktop TechCommunity](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)에서 커뮤니티에 질문할 수 있습니다.

[Windows Virtual Desktop 피드백 허브](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app)에서 Windows Virtual Desktop에 대한 피드백을 남길 수도 있습니다.
