---
title: 마스터 VHD 이미지 준비 및 사용자 지정 - Azure
description: Windows 가상 데스크톱 마스터 이미지를 준비, 사용자 지정 및 Azure에 업로드하는 방법
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 867b327ac25d51cd3955e622da9d8067ae6d9ae9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127734"
---
# <a name="prepare-and-customize-a-master-vhd-image"></a>마스터 VHD 이미지 준비 및 사용자 지정

이 문서에서는 가상 컴퓨터(VM)를 만들고 소프트웨어를 설치하는 방법을 포함하여 Azure에 업로드할 마스터 가상 하드 디스크(VHD) 이미지를 준비하는 방법을 설명합니다. 이러한 지침은 조직의 기존 프로세스에 사용할 수 있는 Windows Virtual Desktop 관련 구성에 대한 것입니다.

## <a name="create-a-vm"></a>VM 만들기

Windows 10 엔터프라이즈 다중 세션은 Azure 이미지 갤러리에서 사용할 수 있습니다. 이 이미지를 사용자 지정하기 위한 두 가지 옵션이 있습니다.

첫 번째 옵션은 [관리되는 이미지에서 VM 만들기의](../virtual-machines/windows/create-vm-generalized-managed.md)지침에 따라 Azure에서 가상 시스템(VM)을 프로비전한 다음 [소프트웨어 준비 및 설치로](set-up-customize-master-image.md#software-preparation-and-installation)건너뛰는 것입니다.

두 번째 옵션은 이미지를 다운로드하고 Hyper-V VM을 프로비전하고 필요에 맞게 사용자 지정하여 이미지를 로컬로 만드는 것입니다.

### <a name="local-image-creation"></a>로컬 이미지 생성

이미지를 로컬 위치에 다운로드한 후 **Hyper-V 관리자를** 열어 복사한 VHD로 VM을 만듭니다. 다음 지침은 간단한 버전이지만 [Hyper-V의 가상 컴퓨터 만들기에서](/windows-server/virtualization/hyper-v/get-started/create-a-virtual-machine-in-hyper-v/)자세한 지침을 찾을 수 있습니다.

복사된 VHD로 VM을 만들려면 다음을 수행합니다.

1. 새 **가상 시스템 마법사를**엽니다.

2. 생성 지정 페이지에서 **1 세대를**선택합니다.

    ![생성 지정 페이지의 스크린샷입니다. "1세대" 옵션이 선택됩니다.](media/a41174fd41302a181e46385e1e701975.png)

3. 검사점 유형에서 확인란의 선택을 취소하여 검사점 확인을 사용하지 않도록 설정합니다.

    ![검사점 페이지의 검사점 유형 섹션의 스크린샷입니다.](media/20c6dda51d7cafef33251188ae1c0c6a.png)

PowerShell에서 다음 cmdlet을 실행하여 검사점 해제할 수도 있습니다.

```powershell
Set-VM -Name <VMNAME> -CheckpointType Disabled
```

### <a name="fixed-disk"></a>고정 디스크

기존 VHD에서 VM을 만드는 경우 기본적으로 동적 디스크를 만듭니다. 다음 이미지와 같이 **디스크 편집을** 선택하여 고정 디스크로 변경할 수 있습니다. 자세한 지침은 [Azure에 업로드할 Windows VHD 또는 VHDX 준비를](../virtual-machines/windows/prepare-for-upload-vhd-image.md)참조하십시오.

![디스크 편집 옵션의 스크린샷입니다.](media/35772414b5a0f81f06f54065561d1414.png)

다음 PowerShell cmdlet을 실행하여 디스크를 고정 디스크로 변경할 수도 있습니다.

```powershell
Convert-VHD –Path c:\\test\\MY-VM.vhdx –DestinationPath c:\\test\\MY-NEW-VM.vhd -VHDType Fixed
```

## <a name="software-preparation-and-installation"></a>소프트웨어 준비 및 설치

이 섹션에서는 FSLogix 및 Windows Defender를 준비하고 설치하는 방법과 앱 및 이미지 레지스트리에 대한 몇 가지 기본 구성 옵션을 설명합니다. 

VM에 Office 365 ProPlus 및 OneDrive를 설치하는 경우 [마스터 VHD 이미지에 Office 설치로](install-office-on-wvd-master-image.md) 이동하여 이 곳에 있는 지침에 따라 앱을 설치합니다. 작업이 완료되면 이 문서로 돌아갑니다.

사용자가 특정 LOB 응용 프로그램에 액세스해야 하는 경우 이 섹션의 지침을 완료한 후 설치하는 것이 좋습니다.

### <a name="set-up-user-profile-container-fslogix"></a>사용자 프로필 컨테이너 설정(FSLogix)

FSLogix 컨테이너를 이미지의 일부로 포함하려면 파일 [공유를 사용하여 호스트 풀에 대한 프로필 컨테이너 만들기의](create-host-pools-user-profile.md#configure-the-fslogix-profile-container)지침을 따릅니다. [이 빠른 시작을](/fslogix/configure-cloud-cache-tutorial/)통해 FSLogix 컨테이너의 기능을 테스트할 수 있습니다.

### <a name="configure-windows-defender"></a>윈도우 디펜더 구성

Windows Defender가 VM에 구성된 경우 첨부 하는 동안 VHD 및 VHDX 파일의 전체 내용을 검색 하지 않도록 구성 되어 있는지 확인 합니다.

이 구성은 첨부 하는 동안 VHD 및 VHDX 파일의 스캔만 제거 하지만 실시간 검색에 영향을 주지 않습니다.

Windows 서버에서 Windows Defender를 구성하는 방법에 대한 자세한 지침은 [Windows 서버에서 Windows Defender 바이러스 백신 제외 구성을](/windows/security/threat-protection/windows-defender-antivirus/configure-server-exclusions-windows-defender-antivirus/)참조하십시오.

특정 파일을 검색에서 제외하도록 Windows Defender를 구성하는 방법에 대한 자세한 내용은 [파일 확장자 및 폴더 위치에 따라 제외 구성 및 유효성 검사를](/windows/security/threat-protection/windows-defender-antivirus/configure-extension-file-exclusions-windows-defender-antivirus/)참조하십시오.

### <a name="disable-automatic-updates"></a>자동 업데이트 사용 안 함

로컬 그룹 정책을 통해 자동 업데이트를 사용하지 않도록 설정하려면 다음 을 따르세요.

1. **열기 로컬 그룹\\정책\\편집기\\관리 템플릿 Windows 구성 요소 Windows 업데이트**.
2. **자동 업데이트 구성을** 마우스 오른쪽 단추로 클릭하고 **비활성화로**설정합니다.

명령 프롬프트에서 다음 명령을 실행하여 자동 업데이트를 비활성화할 수도 있습니다.

```batch
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU" /v NoAutoUpdate /t REG_DWORD /d 1 /f
```

### <a name="specify-start-layout-for-windows-10-pcs-optional"></a>Windows 10 PC의 시작 레이아웃 지정(선택 사항)

이 명령을 실행하여 Windows 10 PC의 시작 레이아웃을 지정합니다.

```batch
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer" /v SpecialRoamingOverrideAllowed /t REG_DWORD /d 1 /f
```

### <a name="set-up-time-zone-redirection"></a>표준 시간대 리디렉션 설정

호스트 풀의 모든 VM이 동일한 보안 그룹의 일부이므로 표준 시간대 리디렉션은 그룹 정책 수준에서 적용할 수 있습니다.

표준 시간대를 리디렉션하려면 다음을 수행합니다.

1. Active Directory 서버에서 **그룹 정책 관리 콘솔을**엽니다.
2. 도메인 및 그룹 정책 개체를 확장합니다.
3. 그룹 정책 설정에 대해 만든 **그룹 정책 개체를** 마우스 오른쪽 단추로 클릭하고 **편집을**선택합니다.
4. 그룹 **정책 관리 편집기에서** **컴퓨터 구성** > **정책** > 관리**템플릿** > Windows 구성**요소** > **원격 데스크톱 서비스** > **원격 데스크톱 세션 호스트** > **장치 및 리소스 리디렉션으로**이동합니다.
5. 표준 **시간대 리디렉션 허용** 설정을 사용하도록 설정합니다.

마스터 이미지에서 이 명령을 실행하여 표준 시간대를 리디렉션할 수도 있습니다.

```batch
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fEnableTimeZoneRedirection /t REG_DWORD /d 1 /f
```

### <a name="disable-storage-sense"></a>스토리지 감지 비활성화

Windows 10 엔터프라이즈 또는 Windows 10 엔터프라이즈 다중 세션을 사용하는 Windows 가상 데스크톱 세션 호스트의 경우 저장소 감지를 사용하지 않도록 설정하는 것이 좋습니다. 다음 스크린샷과 같이 저장소 아래의 설정 메뉴에서 **저장소**감지를 사용하지 않도록 설정할 수 있습니다.

![설정에서 저장소 메뉴의 스크린샷입니다. "저장소 감지" 옵션이 꺼져 있습니다.](media/storagesense.png)

다음 명령을 실행하여 레지스트리를 사용하는 설정을 변경할 수도 있습니다.

```batch
reg add "HKCU\Software\Microsoft\Windows\CurrentVersion\StorageSense\Parameters\StoragePolicy" /v 01 /t REG_DWORD /d 0 /f
```

### <a name="include-additional-language-support"></a>추가 언어 지원 포함

이 문서에서는 언어 및 지역 지원을 구성하는 방법을 다루지 않습니다. 자세한 내용은 다음 문서를 참조하세요.

- [Windows 이미지에 언어 추가](/windows-hardware/manufacture/desktop/add-language-packs-to-windows/)
- [온디맨드 기능](/windows-hardware/manufacture/desktop/features-on-demand-v2--capabilities/)
- [주문형 언어 및 지역 기능(FOD)](/windows-hardware/manufacture/desktop/features-on-demand-language-fod/)

### <a name="other-applications-and-registry-configuration"></a>기타 응용 프로그램 및 레지스트리 구성

이 섹션에서는 응용 프로그램 및 운영 체제 구성을 다룹니다. 이 섹션의 모든 구성은 명령줄 및 regedit 도구에 의해 실행할 수 있는 레지스트리 항목을 통해 수행됩니다.

>[!NOTE]
>그룹 정책 개체(GPO) 또는 레지스트리 가져오기를 통해 구성에서 모범 사례를 구현할 수 있습니다. 관리자는 조직의 요구 사항에 따라 두 옵션 중 하나를 선택할 수 있습니다.

Windows 10 엔터프라이즈 다중 세션에서 원격 분석 데이터의 피드백 허브 컬렉션을 보려면 다음 명령을 실행하십시오.

```batch
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\DataCollection" /v AllowTelemetry /t REG_DWORD /d 3 /f
```

다음 명령을 실행하여 Watson 충돌을 수정합니다.

```batch
remove CorporateWerServer* from Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\Windows Error Reporting
```

레지스트리 편집기에 다음 명령을 입력하여 5k 해상도 지원을 수정합니다. 나란히 스택을 활성화하려면 먼저 명령을 실행해야 합니다.

```batch
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MaxMonitors /t REG_DWORD /d 4 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MaxXResolution /t REG_DWORD /d 5120 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MaxYResolution /t REG_DWORD /d 2880 /f

reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" /v MaxMonitors /t REG_DWORD /d 4 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" /v MaxXResolution /t REG_DWORD /d 5120 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" /v MaxYResolution /t REG_DWORD /d 2880 /f
```

## <a name="prepare-the-image-for-upload-to-azure"></a>Azure에 업로드할 이미지 준비

구성을 완료하고 모든 응용 프로그램을 설치한 후 [Windows VHD 또는 VHDX 준비의](../virtual-machines/windows/prepare-for-upload-vhd-image.md) 지침에 따라 Azure에 업로드하여 이미지를 준비합니다.

업로드할 이미지를 준비한 후 VM이 꺼져 있거나 할당 해제된 상태로 유지되는지 확인합니다.

## <a name="upload-master-image-to-a-storage-account-in-azure"></a>Azure의 저장소 계정에 마스터 이미지 업로드

이 섹션은 마스터 이미지가 로컬로 생성된 경우에만 적용됩니다.

다음 지침은 마스터 이미지를 Azure 저장소 계정에 업로드하는 방법을 설명합니다. Azure 저장소 계정이 아직 없는 경우 [이 문서의](/azure/javascript/tutorial-vscode-static-website-node-03) 지침에 따라 계정을 만듭니다.

1. VM 이미지(VHD)를 아직 수정하지 않은 경우 수정으로 변환합니다. 이미지를 Fixed로 변환하지 않으면 이미지를 성공적으로 만들 수 없습니다.

2. 저장소 계정의 Blob 컨테이너에 VHD를 업로드합니다. [저장소 탐색기 도구를](https://azure.microsoft.com/features/storage-explorer/)사용하여 빠르게 업로드할 수 있습니다. 저장소 탐색기 도구에 대한 자세한 내용은 [이 문서를](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows)참조하세요.

    ![Microsoft Azure 저장소 탐색기 도구의 검색 창의 스크린샷입니다. "페이지 Blob(권장)으로 .vhd 또는 vhdx 파일 업로드" 확인란이 선택됩니다.](media/897aa9a9b6acc0aa775c31e7fd82df02.png)

3. 다음으로 브라우저의 Azure 포털로 이동하여 "이미지"를 검색합니다. 다음 스크린샷과 같이 검색을 통해 **이미지 만들기** 페이지로 연결됩니다.

    ![Azure 포털의 이미지 만들기 페이지의 스크린샷으로 이미지에 대한 예제 값으로 채워져 있습니다.](media/d3c840fe3e2430c8b9b1f44b27d2bf4f.png)

4. 이미지를 만든 후에는 다음 스크린샷에 있는 것과 같은 알림이 표시됩니다.

    !["성공적으로 생성된 이미지" 알림의 스크린샷입니다.](media/1f41b7192824a2950718a2b7bb9e9d69.png)

## <a name="next-steps"></a>다음 단계

이제 이미지가 있으므로 호스트 풀을 만들거나 업데이트할 수 있습니다. 호스트 풀을 만들고 업데이트하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

- [Azure Resource Manager 템플릿으로 호스트 풀 만들기](create-host-pools-arm-template.md)
- [자습서: Azure 마켓플레이스를 사용하여 호스트 풀 만들기](create-host-pools-azure-marketplace.md)
- [PowerShell을 사용한 호스트 풀 만들기](create-host-pools-powershell.md)
- [파일 공유를 사용하여 호스트 풀에 대한 프로필 컨테이너 만들기](create-host-pools-user-profile.md)
- [Windows Virtual Desktop 부하 분산 방법 구성](configure-host-pool-load-balancing.md)
