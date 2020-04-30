---
title: 마스터 VHD 이미지 준비 및 사용자 지정-Azure
description: Azure에 Windows 가상 데스크톱 마스터 이미지를 준비, 사용자 지정 및 업로드 하는 방법입니다.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: aba444e618a6ee2c6808b7f6d9d706fdf18fe546
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82209046"
---
# <a name="prepare-and-customize-a-master-vhd-image"></a>마스터 VHD 이미지 준비 및 사용자 지정

이 문서에서는 Vm (가상 머신)을 만들고 소프트웨어를 설치 하는 방법을 비롯 하 여 Azure에 업로드할 마스터 VHD (가상 하드 디스크) 이미지를 준비 하는 방법을 설명 합니다. 이러한 지침은 조직의 기존 프로세스에 사용할 수 있는 Windows Virtual Desktop 관련 구성에 대한 것입니다.

## <a name="create-a-vm"></a>VM 만들기

Windows 10 Enterprise 다중 세션은 Azure 이미지 갤러리에서 사용할 수 있습니다. 이 이미지를 사용자 지정 하는 두 가지 옵션이 있습니다.

첫 번째 옵션은 [관리 되는 이미지에서 Vm 만들기](../virtual-machines/windows/create-vm-generalized-managed.md)의 지침에 따라 AZURE에서 vm (가상 머신)을 프로 비전 한 다음 [소프트웨어 준비 및 설치](set-up-customize-master-image.md#software-preparation-and-installation)로 건너뜁니다.

두 번째 옵션은 이미지를 다운로드 하 고, Hyper-v VM을 프로 비전 하 고, 요구 사항에 맞게 사용자 지정 하 여 로컬로 이미지를 만드는 것입니다 .이에 대해서는 다음 섹션에서 설명 합니다.

### <a name="local-image-creation"></a>로컬 이미지 만들기

로컬 위치에 이미지를 다운로드 한 후 **Hyper-v 관리자** 를 열어 복사한 VHD를 사용 하 여 VM을 만듭니다. 다음 지침은 간단한 버전 이지만 [hyper-v에서 가상 컴퓨터 만들기](/windows-server/virtualization/hyper-v/get-started/create-a-virtual-machine-in-hyper-v/)에서 더 자세한 지침을 찾을 수 있습니다.

복사 된 VHD를 사용 하 여 VM을 만들려면 다음을 수행 합니다.

1. **새 가상 머신 마법사**를 엽니다.

2. 세대 지정 페이지에서 **1 세대**를 선택 합니다.

    ![세대 지정 페이지의 스크린샷 "1 세대" 옵션이 선택 되어 있습니다.](media/a41174fd41302a181e46385e1e701975.png)

3. 검사점 유형에서 확인란의 선택을 취소 하 여 검사점을 사용 하지 않도록 설정 합니다.

    ![검사점 페이지의 검사점 유형 섹션에 대 한 스크린샷](media/20c6dda51d7cafef33251188ae1c0c6a.png)

PowerShell에서 다음 cmdlet을 실행 하 여 검사점을 사용 하지 않도록 설정할 수도 있습니다.

```powershell
Set-VM -Name <VMNAME> -CheckpointType Disabled
```

### <a name="fixed-disk"></a>고정 디스크

기존 VHD에서 VM을 만드는 경우 기본적으로 동적 디스크를 만듭니다. 다음 그림에 표시 된 것 처럼 **디스크 편집 ...** 을 선택 하 여 고정 디스크로 변경할 수 있습니다. 자세한 지침은 [Azure에 업로드할 WINDOWS VHD 또는 VHDX 준비](../virtual-machines/windows/prepare-for-upload-vhd-image.md)를 참조 하세요.

![디스크 편집 옵션의 스크린샷](media/35772414b5a0f81f06f54065561d1414.png)

다음 PowerShell cmdlet을 실행 하 여 디스크를 고정 디스크로 변경할 수도 있습니다.

```powershell
Convert-VHD –Path c:\test\MY-VM.vhdx –DestinationPath c:\test\MY-NEW-VM.vhd -VHDType Fixed
```

## <a name="software-preparation-and-installation"></a>소프트웨어 준비 및 설치

이 섹션에서는 FSLogix 및 Windows Defender를 준비 하 고 설치 하는 방법 뿐만 아니라 앱 및 이미지 레지스트리의 몇 가지 기본 구성 옵션을 설명 합니다. 

VM에 Office 365 ProPlus 및 OneDrive를 설치 하는 경우 [마스터 VHD 이미지에 Office 설치](install-office-on-wvd-master-image.md) 로 이동 하 고 여기에 있는 지침에 따라 앱을 설치 합니다. 완료 되 면이 문서로 돌아옵니다.

사용자가 특정 LOB 응용 프로그램에 액세스 해야 하는 경우이 섹션의 지침을 완료 한 후에 설치 하는 것이 좋습니다.

### <a name="set-up-user-profile-container-fslogix"></a>사용자 프로필 컨테이너 설정 (FSLogix)

FSLogix 컨테이너를 이미지의 일부로 포함 하려면 [파일 공유를 사용 하 여 호스트 풀에 대 한 프로필 컨테이너 만들기](create-host-pools-user-profile.md#configure-the-fslogix-profile-container)의 지침을 따르세요. [이 빠른](/fslogix/configure-cloud-cache-tutorial/)시작을 사용 하 여 FSLogix 컨테이너의 기능을 테스트할 수 있습니다.

### <a name="configure-windows-defender"></a>Windows Defender 구성

Windows Defender가 VM에 구성 된 경우 첨부 파일 중에 VHD 및 VHDX 파일의 전체 콘텐츠를 검색 하지 않도록 구성 되어 있는지 확인 합니다.

이 구성은 첨부 파일 중에 VHD 및 VHDX 파일의 검색만 제거 하 고 실시간 검색에는 영향을 주지 않습니다.

Windows Server에서 Windows Defender를 구성 하는 방법에 대 한 자세한 내용은 [Windows server에서 Windows Defender 바이러스 백신 제외 구성](/windows/security/threat-protection/windows-defender-antivirus/configure-server-exclusions-windows-defender-antivirus/)을 참조 하세요.

검색에서 특정 파일을 제외 하도록 Windows Defender를 구성 하는 방법에 대 한 자세한 내용은 [파일 확장명 및 폴더 위치에 따라 제외 구성 및 유효성 검사](/windows/security/threat-protection/windows-defender-antivirus/configure-extension-file-exclusions-windows-defender-antivirus/)를 참조 하세요.

### <a name="disable-automatic-updates"></a>자동 업데이트 사용 안 함

로컬 그룹 정책를 통해 자동 업데이트를 사용 하지 않도록 설정 하려면:

1. **Windows 구성\\요소\\\\Windows 업데이트 관리 템플릿 로컬 그룹 정책 편집기**을 엽니다.
2. **자동 업데이트 구성** 을 마우스 오른쪽 단추로 클릭 하 고 **사용 안 함**으로 설정 합니다.

명령 프롬프트에서 다음 명령을 실행 하 여 자동 업데이트를 사용 하지 않도록 설정할 수도 있습니다.

```batch
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU" /v NoAutoUpdate /t REG_DWORD /d 1 /f
```

### <a name="specify-start-layout-for-windows-10-pcs-optional"></a>Windows 10 Pc의 시작 레이아웃 지정 (선택 사항)

Windows 10 Pc에 대 한 시작 레이아웃을 지정 하려면이 명령을 실행 합니다.

```batch
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer" /v SpecialRoamingOverrideAllowed /t REG_DWORD /d 1 /f
```

### <a name="set-up-time-zone-redirection"></a>표준 시간대 리디렉션 설정

호스트 풀의 모든 Vm이 동일한 보안 그룹에 속해 있으므로 표준 시간대 리디렉션이 그룹 정책 수준에 적용 될 수 있습니다.

표준 시간대를 리디렉션하려면:

1. Active Directory 서버에서 **그룹 정책 관리 콘솔**를 엽니다.
2. 도메인을 확장 하 고 개체를 그룹 정책 합니다.
3. 그룹 정책 설정에 대해 만든 **그룹 정책 개체** 를 마우스 오른쪽 단추로 클릭 하 고 **편집**을 선택 합니다.
4. **그룹 정책 관리 편집기**에서 **컴퓨터 구성** > **정책** > **관리 템플릿** > **Windows 구성 요소** > **원격 데스크톱 서비스** > **Remote Desktop Session Host**원격 데스크톱 세션 호스트 > **장치 및 리소스 리디렉션**으로 이동 합니다.
5. **표준 시간대 리디렉션 허용** 설정을 사용 하도록 설정 합니다.

마스터 이미지에서이 명령을 실행 하 여 표준 시간대를 리디렉션할 수도 있습니다.

```batch
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fEnableTimeZoneRedirection /t REG_DWORD /d 1 /f
```

### <a name="disable-storage-sense"></a>저장소 센스 사용 안 함

Windows 10 Enterprise 또는 Windows 10 Enterprise 다중 세션을 사용 하는 Windows 가상 데스크톱 세션 호스트의 경우 저장소 센스를 사용 하지 않도록 설정 하는 것이 좋습니다. 다음 스크린샷에 표시 된 것 처럼 **저장소**의 설정 메뉴에서 저장소 센스를 사용 하지 않도록 설정할 수 있습니다.

![설정 아래의 저장소 메뉴 스크린샷 "저장소 센스" 옵션이 꺼져 있습니다.](media/storagesense.png)

다음 명령을 실행 하 여 레지스트리를 사용 하 여 설정을 변경할 수도 있습니다.

```batch
reg add "HKCU\Software\Microsoft\Windows\CurrentVersion\StorageSense\Parameters\StoragePolicy" /v 01 /t REG_DWORD /d 0 /f
```

### <a name="include-additional-language-support"></a>추가 언어 지원 포함

이 문서에서는 언어 및 국가별 지원을 구성 하는 방법에 대해 다루지 않습니다. 자세한 내용은 다음 아티클을 참조하세요.

- [Windows 이미지에 언어 추가](/windows-hardware/manufacture/desktop/add-language-packs-to-windows/)
- [주문형 기능](/windows-hardware/manufacture/desktop/features-on-demand-v2--capabilities/)
- [언어 및 지역 기능 (주문형)](/windows-hardware/manufacture/desktop/features-on-demand-language-fod/)

### <a name="other-applications-and-registry-configuration"></a>기타 응용 프로그램 및 레지스트리 구성

이 섹션에서는 응용 프로그램 및 운영 체제 구성에 대해 설명 합니다. 이 섹션의 모든 구성은 명령줄 및 regedit 도구를 통해 실행할 수 있는 레지스트리 항목을 통해 수행 됩니다.

>[!NOTE]
>그룹 정책 개체 (Gpo) 또는 레지스트리 가져오기를 사용 하 여 구성에서 모범 사례를 구현할 수 있습니다. 관리자는 조직의 요구 사항에 따라 옵션 중 하나를 선택할 수 있습니다.

Windows 10 Enterprise 다중 세션에서 원격 분석 데이터의 피드백 허브 컬렉션을 보려면 다음 명령을 실행 합니다.

```batch
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\DataCollection" /v AllowTelemetry /t REG_DWORD /d 3 /f
```

다음 명령을 실행 하 여 Watson 충돌을 해결 합니다.

```batch
remove CorporateWerServer* from Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\Windows Error Reporting
```

레지스트리 편집기에서 다음 명령을 입력 하 여 5k resolution 지원을 수정 합니다. Side-by-side 스택을 사용 하도록 설정 하려면 먼저 명령을 실행 해야 합니다.

```batch
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MaxMonitors /t REG_DWORD /d 4 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MaxXResolution /t REG_DWORD /d 5120 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MaxYResolution /t REG_DWORD /d 2880 /f

reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" /v MaxMonitors /t REG_DWORD /d 4 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" /v MaxXResolution /t REG_DWORD /d 5120 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" /v MaxYResolution /t REG_DWORD /d 2880 /f
```

## <a name="prepare-the-image-for-upload-to-azure"></a>Azure에 업로드할 이미지 준비

구성을 완료 하 고 모든 응용 프로그램을 설치한 후에 [는 WINDOWS VHD 또는 VHDX 준비를 Azure에 업로드](../virtual-machines/windows/prepare-for-upload-vhd-image.md) 하 여 이미지를 준비 하는 지침을 따르세요.

업로드할 이미지를 준비한 후 VM이 꺼짐 또는 할당 취소 상태로 유지 되는지 확인 합니다.

## <a name="upload-master-image-to-a-storage-account-in-azure"></a>Azure의 저장소 계정에 마스터 이미지 업로드

이 섹션은 마스터 이미지가 로컬로 생성 된 경우에만 적용 됩니다.

다음 지침에서는 마스터 이미지를 Azure storage 계정에 업로드 하는 방법을 설명 합니다. 아직 Azure storage 계정이 없는 경우 [이 문서의](/azure/javascript/tutorial-vscode-static-website-node-03) 지침에 따라 새로 만듭니다.

1. 아직 없는 경우 VM 이미지 (VHD)를 고정으로 변환 합니다. 이미지를 고정으로 변환 하지 않으면 이미지를 만들 수 없습니다.

2. 저장소 계정의 blob 컨테이너에 VHD를 업로드 합니다. [Storage 탐색기 도구](https://azure.microsoft.com/features/storage-explorer/)를 사용 하 여 빠르게 업로드할 수 있습니다. Storage 탐색기 도구에 대해 자세히 알아보려면 [이 문서](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows)를 참조 하세요.

    ![Microsoft Azure Storage 탐색기 도구의 검색 창 스크린샷 "Vhd 또는 vhdx 파일을 페이지 blob으로 업로드 (권장)" 확인란이 선택 되어 있습니다.](media/897aa9a9b6acc0aa775c31e7fd82df02.png)

3. 그런 다음 브라우저에서 Azure Portal로 이동 하 고 "이미지"를 검색 합니다. 다음 스크린샷에 표시 된 것 처럼 검색에서 **이미지 만들기** 페이지로 안내 합니다.

    ![이미지에 대 한 예제 값으로 채워진 Azure Portal의 이미지 만들기 페이지의 스크린샷](media/d3c840fe3e2430c8b9b1f44b27d2bf4f.png)

4. 이미지를 만든 후에는 다음 스크린샷에 표시 된 것과 같은 알림이 표시 됩니다.

    !["성공적으로 생성 된 이미지" 알림의 스크린샷](media/1f41b7192824a2950718a2b7bb9e9d69.png)

## <a name="next-steps"></a>다음 단계

이제 이미지가 있으므로 호스트 풀을 만들거나 업데이트할 수 있습니다. 호스트 풀을 만들고 업데이트 하는 방법에 대해 자세히 알아보려면 다음 문서를 참조 하세요.

- [Azure Resource Manager 템플릿으로 호스트 풀 만들기](create-host-pools-arm-template.md)
- [자습서: Azure Marketplace를 사용 하 여 호스트 풀 만들기](create-host-pools-azure-marketplace.md)
- [PowerShell을 사용한 호스트 풀 만들기](create-host-pools-powershell.md)
- [파일 공유를 사용하여 호스트 풀에 대한 프로필 컨테이너 만들기](create-host-pools-user-profile.md)
- [Windows Virtual Desktop 부하 분산 방법 구성](configure-host-pool-load-balancing.md)
