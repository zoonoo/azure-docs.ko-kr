---
title: Windows 가상 데스크톱 MSIX 앱 연결 미리 보기 PowerShell-Azure
description: PowerShell을 사용 하 여 Windows 가상 데스크톱에 대 한 MSIX 앱 연결을 설정 하는 방법입니다.
author: Heidilohr
ms.topic: how-to
ms.date: 12/14/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 8aa6a2168bff6e90d636770804900fa93f081ced
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/14/2020
ms.locfileid: "97425875"
---
# <a name="set-up-msix-app-attach-preview-using-powershell"></a>PowerShell을 사용 하 여 MSIX 앱 연결 (미리 보기) 설정

> [!IMPORTANT]
> MSIX 앱 연결은 현재 공개 미리 보기로 제공 됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

Azure Portal 이외에 PowerShell을 사용 하 여 MSIX 앱 연결 (미리 보기)을 수동으로 설정할 수도 있습니다. 이 문서에서는 PowerShell을 사용 하 여 MSIX 앱 연결을 설정 하는 방법을 안내 합니다.

## <a name="requirements"></a>요구 사항

>[!IMPORTANT]
>시작 하기 전에 구독에서 MSIX 앱 연결을 사용할 수 있도록 [이 양식을](https://aka.ms/enablemsixappattach) 작성 하 고 제출 해야 합니다. 승인 된 요청이 없으면 MSIX 앱 연결이 작동 하지 않습니다. 요청 승인은 업무 시간 동안 최대 24 시간이 걸릴 수 있습니다. 요청이 수락 되 고 완료 되 면 전자 메일을 받게 됩니다.

MSIX 앱 연결을 구성 하는 데 필요한 사항은 다음과 같습니다.

- 작동하는 Windows Virtual Desktop 배포 Windows 가상 데스크톱 (클래식)을 배포 하는 방법을 알아보려면 [Windows 가상 데스크톱에서 테 넌 트 만들기](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md)를 참조 하세요. Azure Resource Manager 통합을 사용 하 여 Windows 가상 데스크톱을 배포 하는 방법을 알아보려면 [Azure Portal를 사용 하 여 호스트 풀 만들기](./create-host-pools-azure-marketplace.md)를 참조 하세요.
- 하나 이상의 활성 세션 호스트를 포함 하는 Windows 가상 데스크톱 호스트 풀
- 데스크톱 원격 앱 그룹입니다.
- MSIX 패키징 도구입니다.
- MSIX 패키지 응용 프로그램은 파일 공유에 업로드 된 MSIX 이미지로 확장 됩니다.
- MSIX 패키지가 저장 되는 Windows 가상 데스크톱 배포의 파일 공유입니다.
- MSIX 이미지를 업로드 한 파일 공유에도 호스트 풀의 모든 Vm (가상 머신)에 액세스할 수 있어야 합니다. 사용자는 이미지에 액세스 하기 위한 읽기 전용 권한이 필요 합니다.
- PowerShell Core를 다운로드 하 여 설치 합니다.
- 공개 미리 보기 Azure PowerShell 모듈을 다운로드 하 고 로컬 폴더로 확장 합니다.
- 다음 cmdlet을 실행 하 여 Azure 모듈을 설치 합니다.

    ```powershell
    Install-Module -Name Az -Force
    ```

## <a name="sign-in-to-azure-and-import-the-module"></a>Azure에 로그인 하 여 모듈을 가져옵니다.

모든 요구 사항을 준비 했으면 관리자 권한 명령 프롬프트에서 PowerShell core를 열고 다음 cmdlet을 실행 합니다.

```powershell
Connect-AzAccount
```

실행 한 후 자격 증명을 사용 하 여 계정을 인증 합니다. 이 경우 장치 URL 또는 토큰을 묻는 메시지가 표시 될 수 있습니다.

## <a name="import-the-azwindowsvirtualdesktop-module"></a>Az. WindowsVirtualDesktop 모듈을 가져옵니다.

이 문서의 지침을 따르려면 Az. DesktopVirtualization 모듈이 필요 합니다.

>[!NOTE]
>공개 미리 보기의 경우 모듈을 수동으로 가져와야 하는 별도의 ZIP 파일로 제공 합니다.

시작 하기 전에 다음 cmdlet을 실행 하 여 Az. DesktopVirtualization 모듈이 세션 또는 VM에 이미 설치 되어 있는지 확인할 수 있습니다.

```powershell
Get-Module | Where-Object { $_.Name -Like "desktopvirtualization" }
```

Wan에서 기존 모듈의 복사본을 제거 하 고 다시 시작 하려면 다음 cmdlet을 실행 합니다.

```powershell
Uninstall-Module Az.DesktopVirtualization
```

모듈이 VM에서 차단 되는 경우이 cmdlet을 실행 하 여 차단을 해제 합니다.

```powershell
Unblock-File "<path>\Az.DesktopVirtualization.psm1"
```

이렇게 정리 하는 것은 모듈을 가져올 때입니다.

1. 다음 cmdlet을 실행 하 고, 사용자 지정 코드 실행에 동의 하 라는 메시지가 표시 되 면 **R** 키를 누릅니다.

   ```powershell
   Import-Module -Name "<path>\Az.DesktopVirtualization.psm1" -Verbose
   ```

2. Import cmdlet을 실행 한 후 다음 cmdlet을 실행 하 여 MSIX 용 cmdlet이 있는지 확인 합니다.

   ```powershell
   Get-Command -Module Az.DesktopVirtualization | Where-Object { $_.Name -match "MSIX" }
   ```

   Cmdlet이 있는 경우 출력은 다음과 같습니다.

   ```powershell
   CommandType     Name                                               Version    Source

   -----------     ----                                               -------    ------

   Function        Expand-AzWvdMsixImage                              0.0        Az.DesktopVirtualization

   Function        Get-AzWvdMsixPackage                               0.0        Az.DesktopVirtualization

   Function        New-AzWvdMsixPackage                               0.0        Az.DesktopVirtualization

   Function        Remove-AzWvdMsixPackage                            0.0        Az.DesktopVirtualization

   Function        Update-AzWvdMsixPackage                            0.0        Az.DesktopVirtualization
   ```

   이 출력이 표시 되지 않으면 모든 PowerShell 및 PowerShell Core 세션을 닫고 다시 시도 합니다.

## <a name="set-up-helper-variables"></a>도우미 변수 설정

모듈을 가져온 후에는 도우미 변수를 설정 해야 합니다. 다음 예에서는 각 작업을 수행 하는 방법을 보여 줍니다.

구독 ID를 가져오려면:

```powershell
Get-AzContext -ListAvailable | fl
```

이름으로 Azure 테 넌 트 및 구독의 컨텍스트를 선택 하려면:

```powershell
$obj = Select-AzContext -Name "<Name>"
```

구독 변수를 설정 하려면:

```powershell
$subId = $obj.Subscription.Id
```

작업 영역 이름을 설정 하려면:

```powershell
$ws = "<WorksSpaceName>"
```

호스트 풀 이름을 설정 하려면:

```powershell
$hp = "<HostPoolName>"
```

세션 호스트 Vm이 구성 된 리소스 그룹을 설정 하려면:

```powershell
$rg = "<ResourceGroupName>"
```

마지막으로 모든 변수를 올바르게 설정 했는지 확인 하려면 다음을 수행 합니다.

```powershell
Get-AzWvdWorkspace -Name $ws -ResourceGroupName $rg -SubscriptionId $subID
```

## <a name="add-an-msix-package-to-a-host-pool"></a>호스트 풀에 MSIX 패키지 추가

모든 항목을 설정한 후에는 호스트 풀에 MSIX 패키지를 추가할 시간입니다. 이렇게 하려면 먼저 MSIX 이미지에 대 한 UNC 경로를 가져와야 합니다.

UNC 경로를 사용 하 여이 cmdlet을 실행 하 여 MSIX 이미지를 확장 합니다.

```powershell
$obj = Expand-AzWvdMsixImage -HostPoolName $hp -ResourceGroupName $rg -SubscriptionId $subID -Uri <UNCPath>
```

이 cmdlet을 실행 하 여 MSIX 패키지를 원하는 호스트 풀에 추가할 수 있습니다.

```powershell
New-AzWvdMsixPackage -HostPoolName $hp -ResourceGroupName $rg -SubscriptionId $subId -PackageAlias $obj.PackageAlias -DisplayName <DisplayName> -ImagePath <UNCPath> -IsActive:$true
```

완료 되 면이 cmdlet을 사용 하 여 패키지를 만들었는지 확인 합니다.

```powershell
Get-AzWvdMsixPackage -HostPoolName $hp -ResourceGroupName $rg -SubscriptionId $subId | Where-Object {$_.PackageFamilyName -eq $obj.PackageFamilyName}
```

## <a name="remove-an-msix-package-from-a-host-pool"></a>호스트 풀에서 MSIX 패키지 제거

호스트 풀에서 패키지를 제거 하려면 다음을 수행 합니다.

이 cmdlet을 사용 하 여 호스트 풀과 연결 된 모든 패키지 목록을 가져온 다음 출력에서 제거 하려는 패키지의 이름을 찾습니다.

```powershell
Get-AzWvdMsixPackage -HostPoolName $hp -ResourceGroupName $rg -SubscriptionId $subId 
```

또는이 cmdlet을 사용 하 여 표시 이름에 따라 특정 패키지를 가져올 수도 있습니다.

```powershell
Get-AzWvdMsixPackage -HostPoolName $hp -ResourceGroupName $rg -SubscriptionId $subId | Where-Object { $_.Name -like "Power" }
```

패키지를 제거 하려면 다음 cmdlet을 실행 합니다.

```powershell
Remove-AzWvdMsixPackage -FullName $obj.PackageFullName -HostPoolName $hp -ResourceGroupName $rg
```

## <a name="publish-msix-apps-to-an-app-group"></a>앱 그룹에 MSIX 앱 게시

이전 섹션의 지침에 따라 작업을 완료 한 경우에만이 섹션의 지침을 따를 수 있습니다. 활성 세션 호스트를 포함 하는 호스트 풀, 하나 이상의 데스크톱 앱 그룹 및 MSIX 패키지를 호스트 풀에 추가한 경우 준비가 완료 된 것입니다.

앱을 MSIX 패키지에서 앱 그룹으로 게시 하려면 해당 이름을 찾은 다음 게시 cmdlet에서 해당 이름을 사용 해야 합니다.

앱을 게시 하려면:

이 cmdlet을 실행 하 여 사용 가능한 모든 앱 그룹을 나열 합니다.

```powershell
Get-AzWvdApplicationGroup -ResourceGroupName $rg -SubscriptionId $subId
```

앱을 게시 하려는 앱 그룹의 이름을 찾았으면이 cmdlet에 해당 이름을 사용 합니다.

```powershell
$grName = "<AppGroupName>"
```

마지막으로 앱을 게시 해야 합니다.

- MSIX 응용 프로그램을 데스크톱 앱 그룹에 게시 하려면이 cmdlet을 실행 합니다.

   ```powershell
   New-AzWvdApplication -ResourceGroupName $rg -SubscriptionId $subId -Name PowerBi -ApplicationType MsixApplication -ApplicationGroupName $grName -MsixPackageFamilyName $obj.PackageFamilyName -CommandLineSetting 0
   ```

- 앱을 원격 앱 그룹에 게시 하려면이 cmdlet을 대신 실행 합니다.

   ```powershell
   New-AzWvdApplication -ResourceGroupName $rg -SubscriptionId $subId -Name PowerBi -ApplicationType MsixApplication -ApplicationGroupName $grName -MsixPackageFamilyName $obj.PackageFamilyName -CommandLineSetting 0 -MsixPackageApplicationId $obj.PackageApplication.AppId
   ```

>[!NOTE]
>사용자가 동일한 호스트 풀에 있는 원격 앱 그룹과 데스크톱 앱 그룹에 모두 할당 된 경우 사용자가 원격 데스크톱에 연결 하면 두 그룹의 MSIX 개 앱이 표시 됩니다.

## <a name="next-steps"></a>다음 단계

[Windows 가상 데스크톱 TechCommunity](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)에서이 기능에 대 한 커뮤니티 질문을 요청 합니다.

[Windows Virtual Desktop 피드백 허브](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app)에서 Windows Virtual Desktop에 대한 피드백을 남길 수도 있습니다.

다음은 유용 하 게 사용할 수 있는 몇 가지 문서입니다.

- [MSIX 앱 연결 용어집](app-attach-glossary.md)
- [MSIX 앱 연결 FAQ](app-attach-faq.md)