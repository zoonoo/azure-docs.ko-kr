---
title: Azure Virtual Desktop MSIX 앱 연결 PowerShell - Azure
description: PowerShell을 사용하여 Azure Virtual Desktop에 대한 MSIX 앱 연결을 설정하는 방법입니다.
author: Heidilohr
ms.topic: how-to
ms.date: 04/13/2021
ms.author: helohr
ms.custom: devx-track-azurepowershell
manager: femila
ms.openlocfilehash: d77dfb8883aac4e960274ce86de243f25df2f196
ms.sourcegitcommit: e6de87b42dc320a3a2939bf1249020e5508cba94
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/27/2021
ms.locfileid: "114707036"
---
# <a name="set-up-msix-app-attach-using-powershell"></a>PowerShell을 사용하여 MSIX 앱 연결 설정

Azure Portal 이외에 PowerShell을 사용하여 MSIX 앱 연결을 수동으로 설정할 수도 있습니다. 이 문서에서는 PowerShell을 사용하여 MSIX 앱 연결을 설정하는 방법을 안내합니다.

## <a name="requirements"></a>요구 사항

MSIX 앱 연결을 구성하는 데 필요한 사항은 다음과 같습니다.

- 작동하는 Azure Virtual Desktop 배포 Azure Virtual Desktop(클래식)을 배포하는 방법을 알아보려면 [Azure Virtual Desktop에서 테넌트 만들기](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md)를 참조하세요. Azure Resource Manager 통합을 사용하여 Azure Virtual Desktop을 배포하는 방법을 알아보려면 [Azure Portal을 사용하여 호스트 풀 만들기](./create-host-pools-azure-marketplace.md)를 참조하세요.
- 하나 이상의 활성 세션 호스트를 포함하는 Azure Virtual Desktop 호스트 풀
- 데스크톱 원격 앱 그룹
- MSIX 패키징 도구
- MSIX 패키지 애플리케이션은 파일 공유에 업로드된 MSIX 이미지로 확장됩니다.
- MSIX 패키지를 저장할 Azure Virtual Desktop 배포의 파일 공유.
- MSIX 이미지를 업로드한 파일 공유도 호스트 풀의 모든 VM(가상 머신)에 액세스할 수 있어야 합니다. 사용자는 이미지에 액세스하기 위한 읽기 전용 권한이 필요합니다.
- PowerShell Core를 다운로드하고 설치합니다.
- 공개 미리 보기 Azure PowerShell 모듈을 다운로드하고 로컬 폴더로 확장합니다.
- 다음 cmdlet을 실행하여 Azure 모듈을 설치합니다.

    ```powershell
    Install-Module -Name Az -Force
    ```

## <a name="sign-in-to-azure-and-import-the-module"></a>Azure에 로그인하여 모듈 가져오기

모든 요구 사항을 준비했으면 관리자 권한 명령 프롬프트에서 PowerShell core를 열고 다음 cmdlet을 실행합니다.

```powershell
Connect-AzAccount
```

실행한 후 자격 증명을 사용하여 계정을 인증합니다. 이 경우 디바이스 URL 또는 토큰을 묻는 메시지가 표시될 수 있습니다.

## <a name="import-the-azwindowsvirtualdesktop-module"></a>Az.WindowsVirtualDesktop 모듈 가져오기

이 문서의 지침을 따르려면 Az.DesktopVirtualization 모듈이 필요합니다.

>[!NOTE]
>공개 미리 보기의 경우 모듈을 수동으로 가져와야 하는 별도의 ZIP 파일로 제공합니다.

시작하기 전에 다음 cmdlet을 실행하여 Az.DesktopVirtualization 모듈이 세션 또는 VM에 이미 설치되어 있는지 확인할 수 있습니다.

```powershell
Get-Module | Where-Object { $_.Name -Like "desktopvirtualization" }
```

기존 모듈의 복사본을 제거하고 다시 시작하려면 다음 cmdlet을 실행합니다.

```powershell
Uninstall-Module Az.DesktopVirtualization
```

모듈이 VM에서 차단되는 경우 이 cmdlet을 실행하여 차단을 해제합니다.

```powershell
Unblock-File "<path>\Az.DesktopVirtualization.psm1"
```

정리 작업이 끝나면 모듈을 가져올 시간입니다.

1. 다음 cmdlet을 실행한 다음, 사용자 지정 코드 실행에 동의하라는 메시지가 표시되면 **R** 키를 누릅니다.

   ```powershell
   Import-Module -Name "<path>\Az.DesktopVirtualization.psm1" -Verbose
   ```

2. Import cmdlet을 실행한 후 다음 cmdlet을 실행하여 MSIX용 cmdlet이 있는지 확인합니다.

   ```powershell
   Get-Command -Module Az.DesktopVirtualization | Where-Object { $_.Name -match "MSIX" }
   ```

   cmdlet이 있는 경우 출력은 다음과 같습니다.

   ```powershell
   CommandType     Name                                               Version    Source

   -----------     ----                                               -------    ------

   Function        Expand-AzWvdMsixImage                              0.0        Az.DesktopVirtualization

   Function        Get-AzWvdMsixPackage                               0.0        Az.DesktopVirtualization

   Function        New-AzWvdMsixPackage                               0.0        Az.DesktopVirtualization

   Function        Remove-AzWvdMsixPackage                            0.0        Az.DesktopVirtualization

   Function        Update-AzWvdMsixPackage                            0.0        Az.DesktopVirtualization
   ```

   이 출력이 표시되지 않으면 모든 PowerShell 및 PowerShell Core 세션을 닫고 다시 시도합니다.

## <a name="set-up-helper-variables"></a>도우미 변수 설정

모듈을 가져온 후에는 도우미 변수를 설정해야 합니다. 다음 예제에서는 각 작업을 수행하는 방법을 보여 줍니다.

구독 ID를 가져오려면:

```powershell
Get-AzContext -ListAvailable | fl
```

이름으로 Azure 테넌트 및 구독의 컨텍스트를 선택하려면:

```powershell
$obj = Select-AzContext -Name "<Name>"
```

구독 변수를 설정하려면:

```powershell
$subId = $obj.Subscription.Id
```

작업 영역 이름을 설정하려면:

```powershell
$ws = "<WorksSpaceName>"
```

호스트 풀 이름을 설정하려면:

```powershell
$hp = "<HostPoolName>"
```

세션 호스트 VM이 구성된 리소스 그룹을 설정하려면:

```powershell
$rg = "<ResourceGroupName>"
```

마지막으로 모든 변수를 올바르게 설정했는지 확인하려면:

```powershell
Get-AzWvdWorkspace -Name $ws -ResourceGroupName $rg -SubscriptionId $subID
```

## <a name="add-an-msix-package-to-a-host-pool"></a>호스트 풀에 MSIX 패키지 추가

모든 항목을 설정한 후에는 호스트 풀에 MSIX 패키지를 추가할 시간입니다. 이렇게 하려면 먼저 MSIX 이미지에 대한 UNC 경로를 가져와야 합니다.

UNC 경로를 사용하여 이 cmdlet을 실행하여 MSIX 이미지를 확장합니다.

```powershell
$obj = Expand-AzWvdMsixImage -HostPoolName $hp -ResourceGroupName $rg -SubscriptionId $subID -Uri <UNCPath>
```

이 cmdlet을 실행하여 MSIX 패키지를 원하는 호스트 풀에 추가합니다.

```powershell
New-AzWvdMsixPackage -HostPoolName $hp -ResourceGroupName $rg -SubscriptionId $subId -PackageAlias $obj.PackageAlias -DisplayName <DisplayName> -ImagePath <UNCPath> -IsActive:$true
```

완료되면 이 cmdlet을 사용하여 패키지를 만들었는지 확인합니다.

```powershell
Get-AzWvdMsixPackage -HostPoolName $hp -ResourceGroupName $rg -SubscriptionId $subId | Where-Object {$_.PackageFamilyName -eq $obj.PackageFamilyName}
```

## <a name="remove-an-msix-package-from-a-host-pool"></a>호스트 풀에서 MSIX 패키지 제거

호스트 풀에서 패키지를 제거하려면:

이 cmdlet을 사용하여 호스트 풀과 연결된 모든 패키지 목록을 가져온 다음, 출력에서 제거하려는 패키지의 이름을 찾습니다.

```powershell
Get-AzWvdMsixPackage -HostPoolName $hp -ResourceGroupName $rg -SubscriptionId $subId 
```

또는 이 cmdlet을 사용하여 표시 이름에 따라 특정 패키지를 가져올 수도 있습니다.

```powershell
Get-AzWvdMsixPackage -HostPoolName $hp -ResourceGroupName $rg -SubscriptionId $subId | Where-Object { $_.Name -like "Power" }
```

패키지를 제거하려면 이 cmdlet을 실행합니다.

```powershell
Remove-AzWvdMsixPackage -FullName $obj.PackageFullName -HostPoolName $hp -ResourceGroupName $rg
```

## <a name="publish-msix-apps-to-an-app-group"></a>앱 그룹에 MSIX 앱 게시

이전 섹션의 지침에 따라 작업을 완료한 경우에만 이 섹션의 지침을 따를 수 있습니다. 활성 세션 호스트를 포함하는 호스트 풀, 하나 이상의 데스크톱 앱 그룹이 있고, MSIX 패키지를 호스트 풀에 추가한 경우 준비가 완료된 것입니다.

앱을 MSIX 패키지에서 앱 그룹으로 게시하려면 해당 이름을 찾은 다음, publishing cmdlet에서 해당 이름을 사용해야 합니다.

앱을 게시하려면:

이 cmdlet을 실행하여 사용 가능한 모든 앱 그룹을 나열합니다.

```powershell
Get-AzWvdApplicationGroup -ResourceGroupName $rg -SubscriptionId $subId
```

앱을 게시하려는 앱 그룹의 이름을 찾았으면 이 cmdlet에 해당 이름을 사용합니다.

```powershell
$grName = "<AppGroupName>"
```

마지막으로 앱을 게시해야 합니다.

- MSIX 애플리케이션을 데스크톱 앱 그룹에 게시하려면 이 cmdlet을 실행합니다.

   ```powershell
   New-AzWvdApplication -ResourceGroupName $rg -SubscriptionId $subId -Name PowerBi -ApplicationType MsixApplication -ApplicationGroupName $grName -MsixPackageFamilyName $obj.PackageFamilyName -CommandLineSetting 0
   ```

- 앱을 원격 앱 그룹에 게시하려면 이 cmdlet을 대신 실행합니다.

   ```powershell
   New-AzWvdApplication -ResourceGroupName $rg -SubscriptionId $subId -Name PowerBi -ApplicationType MsixApplication -ApplicationGroupName $grName -MsixPackageFamilyName $obj.PackageFamilyName -CommandLineSetting 0 -MsixPackageApplicationId $obj.PackageApplication.AppId
   ```

>[!NOTE]
>사용자가 동일한 호스트 풀에 있는 원격 앱 그룹과 데스크톱 앱 그룹에 모두 할당된 경우 사용자가 원격 데스크톱에 연결하면 두 그룹의 MSIX 앱이 표시됩니다.

## <a name="next-steps"></a>다음 단계

[Azure Virtual Desktop TechCommunity](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)에서 이 기능에 대해 커뮤니티에 질문합니다.

[Azure Virtual Desktop 피드백 허브](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app)에서 Azure Virtual Desktop에 대한 피드백을 남길 수도 있습니다.

다음은 도움이 될 수 있는 다른 문서입니다.

- [MSIX 앱 연결 용어](app-attach-glossary.md)
- [MSIX 앱 연결 FAQ](app-attach-faq.yml)
