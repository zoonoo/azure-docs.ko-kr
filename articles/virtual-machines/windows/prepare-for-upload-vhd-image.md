---
title: Azure에 업로드할 Windows VHD 준비
description: Windows VHD 또는 VHDX를 준비하여 Azure에 업로드하는 방법에 대해 알아봅니다.
services: virtual-machines-windows
documentationcenter: ''
author: glimoli
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 7802489d-33ec-4302-82a4-91463d03887a
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 05/11/2019
ms.author: genli
ms.openlocfilehash: 719a1985aeb0db7b0cf7f55a10762bf3ebb3e045
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250193"
---
# <a name="prepare-a-windows-vhd-or-vhdx-to-upload-to-azure"></a>Azure에 업로드할 Windows VHD 또는 VHDX 준비

온-프레미스에서 Azure에 Windows 가상 머신(VM)을 업로드하기 전에 가상 하드 디스크(VHD 또는 VHDX)를 준비해야 합니다. Azure는 VHD 파일 형식에 있고 고정 크기 디스크가 있는 1세대 및 2세대 VM을 모두 지원합니다. VHD에 허용되는 최대 크기는 1,023GB입니다. 

1세대 VM에서는 VHDX 파일 시스템을 VHD로 변환할 수 있습니다. 동적으로 확장되는 디스크를 고정 크기 디스크로 변환할 수도 있습니다. 하지만 VM의 세대는 변경할 수 없습니다. 자세한 내용은 [하이퍼-V에서 1세대 또는 2VM을 만들어야 합니까?](https://technet.microsoft.com/windows-server-docs/compute/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v) 및 [2세대 VM(미리 보기)에 대한 Azure 지원을](generation-2.md)참조하십시오.

Azure VM에 대한 지원 정책에 대한 자세한 내용은 [Azure VM에 대한 Microsoft 서버 소프트웨어 지원을](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)참조하십시오.

> [!NOTE]
> 이 문서의 지침은 다음에 적용됩니다.
>1. 64비트 버전의 Windows Server 2008 R2 및 이후 Windows Server 운영 체제입니다. Azure에서 32비트 운영 체제를 실행하는 것에 대한 자세한 내용은 [Azure VM의 32비트 운영 체제 지원을](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines)참조하십시오.
>2. Azure 사이트 복구 또는 Azure 마이그레이션과 같은 워크로드를 마이그레이션하는 데 재해 복구 도구를 사용하는 경우에도 이 프로세스를 수행하고 게스트 OS에서 이 프로세스를 수행하여 마이그레이션 전에 이미지를 준비해야 합니다.

## <a name="system-file-checker-sfc-command"></a>시스템 파일 검사기(SFC) 명령

### <a name="run-windows-system-file-checker-utility-run-sfc-scannow-on-os-prior-to-generalization-step-of-creating-customer-os-image"></a>고객 OS 이미지를 만드는 일반화 단계 전에 OS에서 Windows 시스템 파일 검사기 유틸리티(sfc/scannow 실행)를 실행합니다.

시스템 파일 검사기(SFC) 명령은 Windows 시스템 파일을 확인하고 교체하는 데 사용됩니다.

SFC 명령을 실행하려면 다음을 수행하십시오.

1. 관리자로 상승된 CMD 프롬프트를 엽니다.
1. 입력하고 `sfc /scannow` **Enter를**선택합니다.

    ![시스템 파일 검사기](media/prepare-for-upload-vhd-image/system-file-checker.png)


SFC 검색이 완료되면 Windows 업데이트를 설치하고 컴퓨터를 다시 시작해 봅슬레이를 다시 시작하십시오.

## <a name="convert-the-virtual-disk-to-a-fixed-size-and-to-vhd"></a>가상 디스크를 고정 크기로 변환하고 VHD로 변환

가상 디스크를 Azure에 필요한 형식으로 변환해야 하는 경우 이 섹션의 메서드 중 하나를 사용합니다.

1. 가상 디스크 변환 프로세스를 실행하기 전에 VM을 백업합니다.

1. 로컬 서버에서 Windows VHD가 올바르게 작동하는지 확인합니다. Azure로 변환하거나 업로드하기 전에 VM 자체 내에서 오류를 해결해 보세요.

1. VHD의 크기에 관하여:

   1. Azure의 모든 VHD는 가상 크기가 1MB 단위로 조정되어야 합니다. 원시 디스크에서 VHD로 변환할 때는 원시 디스크 크기를 변환하기 전에 1MB의 배수인지 확인해야 합니다. 업로드된 VHD에서 이미지를 만들 때 메가바이트 의 분수로 인해 오류가 발생합니다.

   2. OS VHD에 허용되는 최대 크기는 2TB입니다.


디스크를 변환한 후 디스크를 사용하는 VM을 만듭니다. VM을 시작하고 로그인하여 업로드준비를 완료합니다.

### <a name="use-hyper-v-manager-to-convert-the-disk"></a>하이퍼 V 관리자를 사용하여 디스크 변환 
1. Hyper-V 관리자를 열고 왼쪽에서 로컬 컴퓨터를 선택합니다. 컴퓨터 목록 위의 메뉴에서 **작업** > **편집 디스크를**선택합니다.
2. 가상 **하드 디스크 찾기** 페이지에서 가상 디스크를 선택합니다.
3. 작업 **선택** 페이지에서**다음** **변환을** > 선택합니다.
4. VHDX에서 변환해야 하는 경우 **VHD** > 다음 을**선택합니다.**
5. 동적으로 확장되는 디스크에서 변환해야 하는 경우 **[다음] [고정 크기]를** > **선택합니다.**
6. 새 VHD 파일을 저장할 경로를 찾아 선택합니다.
7. **마침**을 선택합니다.

> [!NOTE]
> 높은 PowerShell 세션을 사용하여 이 문서의 명령을 실행합니다.

### <a name="use-powershell-to-convert-the-disk"></a>PowerShell을 사용하여 디스크 변환 
Windows PowerShell의 [Convert-VHD](https://technet.microsoft.com/library/hh848454.aspx) 명령을 사용하여 가상 디스크를 변환할 수 있습니다. PowerShell을 시작할 때 **관리자 권한으로 실행**을 선택합니다. 

다음 예제 명령은 디스크를 VHDX에서 VHD로 변환합니다. 또한 이 명령은 동적으로 확장되는 디스크에서 고정 크기 디스크로 디스크를 변환합니다.

```Powershell
Convert-VHD –Path c:\test\MY-VM.vhdx –DestinationPath c:\test\MY-NEW-VM.vhd -VHDType Fixed
```

이 명령에서 값을 변환하려는 가상 하드 디스크에 대한 `-Path` 경로로 바꿉습니다. 값을 `-DestinationPath` 변환된 디스크의 새 경로 및 이름으로 바꿉니다.

### <a name="convert-from-vmware-vmdk-disk-format"></a>VMware VMDK 디스크 형식에서 변환
[VMDK 파일 형식의](https://en.wikipedia.org/wiki/VMDK)Windows VM 이미지가 있는 경우 [Microsoft 가상 머신 변환기를](https://www.microsoft.com/download/details.aspx?id=42497) 사용하여 VHD 형식으로 변환합니다. 자세한 내용은 [VMware VMDK를 하이퍼 VMDK로 변환하는 방법을](https://blogs.msdn.com/b/timomta/archive/2015/06/11/how-to-convert-a-vmware-vmdk-to-hyper-v-vhd.aspx)참조하십시오.

## <a name="set-windows-configurations-for-azure"></a>Azure에 대한 Windows 구성 설정

> [!NOTE]
> Azure 플랫폼은 일반화된 이미지에서 Windows VM을 만들 때 ISO 파일을 DVD-ROM에 탑재합니다.
> 이러한 이유로 일반화된 이미지의 OS에서 DVD-ROM을 사용하도록 설정해야 합니다. 비활성화된 경우 Windows VM이 OOBE에 붙어 있습니다.

Azure에 업로드하려는 VM에서 [높은 명령 프롬프트 창에서](https://technet.microsoft.com/library/cc947813.aspx)다음 명령을 실행합니다.

1. 라우팅 테이블에서 정적 영구 경로를 제거합니다.
   
   * 경로 테이블을 보려면 명령 프롬프트에서 `route print`를 실행합니다.
   * 섹션을 `Persistence Routes` 확인합니다. 영구 경로가 있는 경우 `route delete` 명령을 사용하여 제거합니다.
2. WinHTTP 프록시를 제거합니다.
   
    ```PowerShell
    netsh winhttp reset proxy
    ```

    VM이 특정 프록시로 작업해야 하는 경우 VM이 Azure에 연결할 수 있도록 Azure IP[주소(168.63.129.16)에](https://blogs.msdn.microsoft.com/mast/2015/05/18/what-is-the-ip-address-168-63-129-16/
)프록시 예외를 추가합니다.
    ```
    $proxyAddress="<your proxy server>"
    $proxyBypassList="<your list of bypasses>;168.63.129.16"

    netsh winhttp set proxy $proxyAddress $proxyBypassList
    ```

3. 디스크 SAN 정책을 [`Onlineall`](https://technet.microsoft.com/library/gg252636.aspx)다음과 같은 것으로 설정합니다.
   
    ```PowerShell
    diskpart 
    ```
    열린 명령 프롬프트 창에서 다음 명령을 입력합니다.

     ```DISKPART
    san policy=onlineall
    exit   
    ```

4. Windows의 조정된 유니버설 타임(UTC) 시간을 설정합니다. 또한 Windows 시간 서비스 ()`w32time`의 `Automatic`시작 유형을 다음과 같은 것으로 설정합니다.
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\TimeZoneInformation' -Name "RealTimeIsUniversal" -Value 1 -Type DWord -Force

    Set-Service -Name w32time -StartupType Automatic
    ```
5. 전원 프로필을 고성능으로 설정합니다.

    ```PowerShell
    powercfg /setactive SCHEME_MIN
    ```
6. 환경 변수가 `TEMP` 기본값으로 `TMP` 설정되어 있는지 확인합니다.

    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Environment' -Name "TEMP" -Value "%SystemRoot%\TEMP" -Type ExpandString -Force

    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Environment' -Name "TMP" -Value "%SystemRoot%\TEMP" -Type ExpandString -Force
    ```

## <a name="check-the-windows-services"></a>Windows 서비스 확인
각 Windows 서비스가 Windows 기본값으로 설정되어 있는지 확인합니다. 이러한 서비스는 VM 연결을 보장하기 위해 설정해야 하는 최소 서비스입니다. 시작 설정을 재설정하려면 다음 명령을 실행합니다.
   
```PowerShell
Get-Service -Name bfe | Where-Object { $_.StartType -ne 'Automatic' } | Set-Service -StartupType 'Automatic'
Get-Service -Name dhcp | Where-Object { $_.StartType -ne 'Automatic' } | Set-Service -StartupType 'Automatic'
Get-Service -Name dnscache | Where-Object { $_.StartType -ne 'Automatic' } | Set-Service -StartupType 'Automatic'
Get-Service -Name IKEEXT | Where-Object { $_.StartType -ne 'Automatic' } | Set-Service -StartupType 'Automatic'
Get-Service -Name iphlpsvc | Where-Object { $_.StartType -ne 'Automatic' } | Set-Service -StartupType 'Automatic'
Get-Service -Name netlogon | Where-Object { $_.StartType -ne 'Manual' } | Set-Service -StartupType 'Manual'
Get-Service -Name netman | Where-Object { $_.StartType -ne 'Manual' } | Set-Service -StartupType 'Manual'
Get-Service -Name nsi | Where-Object { $_.StartType -ne 'Automatic' } | Set-Service -StartupType 'Automatic'
Get-Service -Name TermService | Where-Object { $_.StartType -ne 'Manual' } | Set-Service -StartupType 'Manual'
Get-Service -Name MpsSvc | Where-Object { $_.StartType -ne 'Automatic' } | Set-Service -StartupType 'Automatic'
Get-Service -Name RemoteRegistry | Where-Object { $_.StartType -ne 'Automatic' } | Set-Service -StartupType 'Automatic'
```
## <a name="update-remote-desktop-registry-settings"></a>원격 데스크톱 레지스트리 설정 업데이트
원격 액세스를 위해 다음 설정이 올바르게 구성되었는지 확인합니다.

>[!NOTE] 
>를 실행할 `Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services -Name <object name> -Value <value>`때 오류 메시지가 들릴 수 있습니다. 이 메시지는 무시해도 됩니다. 즉, 도메인이 그룹 정책 개체를 통해 해당 구성을 푸시하지 않는다는 의미입니다.

1. RDP(원격 데스크톱 프로토콜)이 활성화되어 있습니다.
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server' -Name "fDenyTSConnections" -Value 0 -Type DWord -Force

    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -Name "fDenyTSConnections" -Value 0 -Type DWord -Force
    ```
   
2. RDP 포트가 올바르게 설정되었습니다. 기본 포트는 3389입니다.
   
    ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name "PortNumber" -Value 3389 -Type DWord -Force
    ```
    VM을 배포할 때 기본 규칙은 포트 3389에 대해 생성됩니다. 포트 번호를 변경하려면 VM이 Azure에 배포된 후 변경합니다.

3. 수신기가 모든 네트워크 인터페이스에서 수신 대기합니다.
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name "LanAdapter" -Value 0 -Type DWord -Force
   ```
4. RDP 연결에 대한 NLA(네트워크 수준 인증) 모드를 구성합니다.
   
    ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name "UserAuthentication" -Value 1 -Type DWord -Force

    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name "SecurityLayer" -Value 1 -Type DWord -Force

    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name "fAllowSecProtocolNegotiation" -Value 1 -Type DWord -Force
     ```

5. 연결 유지 값을 설정합니다.
    
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -Name "KeepAliveEnable" -Value 1  -Type DWord -Force
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -Name "KeepAliveInterval" -Value 1  -Type DWord -Force
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name "KeepAliveTimeout" -Value 1 -Type DWord -Force
    ```
6. 다시 연결합니다.
    
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -Name "fDisableAutoReconnect" -Value 0 -Type DWord -Force
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name "fInheritReconnectSame" -Value 1 -Type DWord -Force
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name "fReconnectSame" -Value 0 -Type DWord -Force
    ```
7. 동시 연결 수를 제한합니다.
    
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name "MaxInstanceCount" -Value 4294967295 -Type DWord -Force
    ```
8. RDP 수신기에 연결된 자체 서명된 인증서를 제거합니다.
    
    ```PowerShell
    if ((Get-Item -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp').Property -contains "SSLCertificateSHA1Hash")
    {
        Remove-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name "SSLCertificateSHA1Hash" -Force
    }
    ```
    이 코드를 사용하면 VM을 배포할 때 처음에 연결할 수 있습니다. 나중에 이 작업을 검토해야 하는 경우 VM이 Azure에 배포된 후 검토할 수 있습니다.

9. VM이 도메인의 일부인 경우 다음 정책을 확인하여 이전 설정이 되돌리지 않았는지 확인합니다. 
    
    | 목표                                     | 정책                                                                                                                                                       | 값                                                                                    |
    |------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------|
    | RDP 사용                           | Computer Configuration\Policies\Windows Settings\Administrative Templates\Components\Remote Desktop Services\Remote Desktop Session Host\Connections         | 사용자가 원격 데스크톱을 사용하여 원격으로 연결하도록 허용                                  |
    | NLA 그룹 정책                         | Settings\Administrative Templates\Components\Remote Desktop Services\Remote Desktop Session Host\Security                                                    | NLA를 사용하여 원격 액세스에 대한 사용자 인증 필요 |
    | 유지 설정                      | Computer Configuration\Policies\Windows Settings\Administrative Templates\Windows Components\Remote Desktop Services\Remote Desktop Session Host\Connections | 연결 유지 연결 간격 구성                                                 |
    | 다시 연결 설정                       | Computer Configuration\Policies\Windows Settings\Administrative Templates\Windows Components\Remote Desktop Services\Remote Desktop Session Host\Connections | 자동으로 다시 연결                                                                   |
    | 제한된 수의 연결 설정 | Computer Configuration\Policies\Windows Settings\Administrative Templates\Windows Components\Remote Desktop Services\Remote Desktop Session Host\Connections | 연결 수 제한                                                              |

## <a name="configure-windows-firewall-rules"></a>Windows 방화벽 규칙 구성
1. 세 가지 프로필(도메인, 표준 및 공용)에서 Windows 방화벽 켜기:

   ```PowerShell
    Set-NetFirewallProfile -Profile Domain,Public,Private -Enabled True
   ```

2. PowerShell에서 다음 명령을 실행하여 세 개의 방화벽 프로필(도메인, 개인 및 공용)을 통해 WinRM을 허용하고 PowerShell 원격 서비스를 사용하도록 설정합니다.
   
   ```PowerShell
    Enable-PSRemoting -Force

    Set-NetFirewallRule -DisplayName "Windows Remote Management (HTTP-In)" -Enabled True
   ```
3. 다음 방화벽 규칙을 사용하도록 설정하여 RDP 트래픽을 허용합니다.

   ```PowerShell
    Set-NetFirewallRule -DisplayGroup "Remote Desktop" -Enabled True
   ```   
4. VM이 가상 네트워크 내부의 ping 명령에 응답할 수 있도록 파일 및 프린터 공유 규칙을 사용하도록 설정합니다.

   ```PowerShell
   Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -Enabled True
   ``` 
5. Azure 플랫폼 네트워크에 대한 규칙을 만듭니다.

   ```PowerShell
    New-NetFirewallRule -DisplayName "AzurePlatform" -Direction Inbound -RemoteAddress 168.63.129.16 -Profile Any -Action Allow -EdgeTraversalPolicy Allow
    New-NetFirewallRule -DisplayName "AzurePlatform" -Direction Outbound -RemoteAddress 168.63.129.16 -Profile Any -Action Allow
   ``` 
6. VM이 도메인의 일부인 경우 다음 Azure AD 정책을 확인하여 이전 설정이 되돌리지 않았는지 확인합니다. 

    | 목표                                 | 정책                                                                                                                                                  | 값                                   |
    |--------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------|
    | Windows 방화벽 프로필을 사용하도록 설정 | Computer Configuration\Policies\Windows Settings\Administrative Templates\Network\Network Connection\Windows Firewall\Domain Profile\Windows Firewall   | 모든 네트워크 연결 보호         |
    | RDP를 사용하도록 설정                           | Computer Configuration\Policies\Windows Settings\Administrative Templates\Network\Network Connection\Windows Firewall\Domain Profile\Windows Firewall   | 인바운드 원격 데스크톱 예외 허용 |
    |                                      | Computer Configuration\Policies\Windows Settings\Administrative Templates\Network\Network Connection\Windows Firewall\Standard Profile\Windows Firewall | 인바운드 원격 데스크톱 예외 허용 |
    | ICMP-V4를 사용하도록 설정                       | Computer Configuration\Policies\Windows Settings\Administrative Templates\Network\Network Connection\Windows Firewall\Domain Profile\Windows Firewall   | ICMP 예외 허용                   |
    |                                      | Computer Configuration\Policies\Windows Settings\Administrative Templates\Network\Network Connection\Windows Firewall\Standard Profile\Windows Firewall | ICMP 예외 허용                   |

## <a name="verify-the-vm"></a>VM 확인 

VM이 정상이고 안전하며 RDP에 액세스할 수 있는지 확인합니다. 

1. 디스크가 정상이고 일관된지 확인하려면 다음 VM 다시 시작 시 디스크를 확인하십시오.

    ```PowerShell
    Chkdsk /f
    ```
    보고서에 깨끗하고 건강한 디스크가 표시되는지 확인합니다.

2. BCD(부팅 구성 데이터) 설정을 지정합니다. 

    > [!NOTE]
    > 높은 PowerShell 창을 사용하여 이러한 명령을 실행합니다.
   
   ```powershell
    bcdedit /set "{bootmgr}" integrityservices enable
    bcdedit /set "{default}" device partition=C:
    bcdedit /set "{default}" integrityservices enable
    bcdedit /set "{default}" recoveryenabled Off
    bcdedit /set "{default}" osdevice partition=C:
    bcdedit /set "{default}" bootstatuspolicy IgnoreAllFailures

    #Enable Serial Console Feature
    bcdedit /set "{bootmgr}" displaybootmenu yes
    bcdedit /set "{bootmgr}" timeout 5
    bcdedit /set "{bootmgr}" bootems yes
    bcdedit /ems "{current}" ON
    bcdedit /emssettings EMSPORT:1 EMSBAUDRATE:115200
   ```
3. 덤프 로그는 Windows 충돌 문제를 해결하는 데 도움이 될 수 있습니다. 덤프 로그 수집 을 활성화합니다.

    ```powershell
    # Set up the guest OS to collect a kernel dump on an OS crash event
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -Name CrashDumpEnabled -Type DWord -Force -Value 2
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -Name DumpFile -Type ExpandString -Force -Value "%SystemRoot%\MEMORY.DMP"
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -Name NMICrashDump -Type DWord -Force -Value 1

    # Set up the guest OS to collect user mode dumps on a service crash event
    $key = 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps'
    if ((Test-Path -Path $key) -eq $false) {(New-Item -Path 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting' -Name LocalDumps)}
    New-ItemProperty -Path $key -Name DumpFolder -Type ExpandString -Force -Value "c:\CrashDumps"
    New-ItemProperty -Path $key -Name CrashCount -Type DWord -Force -Value 10
    New-ItemProperty -Path $key -Name DumpType -Type DWord -Force -Value 2
    Set-Service -Name WerSvc -StartupType Manual
    ```
4. WMI(Windows 관리 계측) 리포지토리가 일관된지 확인합니다.

    ```PowerShell
    winmgmt /verifyrepository
    ```
    리포지토리가 손상된 경우 [WMI: 저장소 손상 여부 참조.](https://blogs.technet.microsoft.com/askperf/2014/08/08/wmi-repository-corruption-or-not)

5. 포트 3389를 사용하는 다른 응용 프로그램이 없는지 확인합니다. 이 포트는 Azure의 RDP 서비스에 사용됩니다. VM에서 사용되는 포트를 보려면 다음을 실행합니다. `netstat -anob`

    ```PowerShell
    netstat -anob
    ```

6. 도메인 컨트롤러인 Windows VHD를 업로드하려면 다음을 수행하십시오.

   * [이러한 추가 단계](https://support.microsoft.com/kb/2904015)에 따라 디스크를 준비합니다.

   * DSRM에서 VM을 시작해야 하는 경우 DSRM(디렉터리 서비스 복원 모드) 암호를 알고 있어야 합니다. 자세한 내용은 [DSRM 암호 설정](https://technet.microsoft.com/library/cc754363(v=ws.11).aspx)을 참조하십시오.

7. 기본 제공 관리자 계정과 암호를 알고 있는지 확인합니다. 현재 로컬 관리자 암호를 재설정하고 이 계정을 사용하여 RDP 연결을 통해 Windows에 로그인할 수 있는지 확인할 수 있습니다. 이 액세스 권한은 "원격 데스크톱 서비스를 통한 로그온 허용" 그룹 정책 개체에 의해 제어됩니다. 로컬 그룹 정책 편집기에서 이 개체를 보려면 여기를 참조하세요.

    Computer Configuration\Windows Settings\Security Settings\Local Policies\User Rights Assignment

8. 다음 Azure AD 정책을 확인하여 RDP 또는 네트워크를 통해 RDP 액세스를 차단하지 않는지 확인합니다.

    - Computer Configuration\Windows Settings\Security Settings\Local Policies\User Rights Assignment\Deny access to this computer from the network

    - Computer Configuration\Windows Settings\Security Settings\Local Policies\User Rights Assignment\Deny log on through Remote Desktop Services


9. 다음 Azure AD 정책을 확인하여 필요한 액세스 계정을 제거하지 않는지 확인합니다.

   - 컴퓨터 구성\Windows 설정\보안 설정\로컬 정책\사용자 권한 할당\네트워크에서 이 컴퓨터에 액세스

   정책에는 다음 그룹이 나열되어야 합니다.

   - 관리자

   - 백업 운영자

   - 모든 사람

   - 사용자

10. VM을 다시 시작하여 Windows가 여전히 정상이고 RDP 연결을 통해 연결할 수 있는지 확인합니다. 이 시점에서 로컬 Hyper-V에서 VM을 만들어 VM이 완전히 시작되도록 할 수 있습니다. 그런 다음 RDP를 통해 VM에 도달할 수 있는지 테스트합니다.

11. 추가 전송 드라이버 인터페이스(TDI) 필터를 제거합니다. 예를 들어 TCP 패킷 이나 추가 방화벽을 분석 하는 소프트웨어를 제거 합니다. 나중에 이 작업을 검토해야 하는 경우 VM이 Azure에 배포된 후 검토할 수 있습니다.

12. 물리적 구성 요소 또는 기타 가상화 기술과 관련된 기타 타사 소프트웨어 또는 드라이버를 제거합니다.

### <a name="install-windows-updates"></a>Windows 업데이트 설치
이상적으로는 *패치 수준에서*기기를 업데이트해야 합니다. 그렇지 않은 경우 다음 업데이트가 설치되어 있는지 확인합니다. 최신 업데이트를 얻으려면 Windows 업데이트 기록 페이지를 참조하십시오 : [윈도우 10 및 윈도우 서버 2019](https://support.microsoft.com/help/4000825), [윈도우 8.1 및 윈도우 서버 2012 R2](https://support.microsoft.com/help/4009470) 및 [윈도우 7 SP1 및 윈도우 서버 2008 R2 SP1](https://support.microsoft.com/help/4009469).

| 구성 요소               | 이진         | 윈도우 7 SP1, 윈도우 서버 2008 R2 SP1 | 윈도우 8, 윈도우 서버 2012               | 윈도우 8.1, 윈도우 서버 2012 R2 | 윈도우 10 v1607, 윈도우 서버 2016 v1607 | 윈도우 10 v1703    | 윈도우 10 v1709, 윈도우 서버 2016 v1709 | 윈도우 10 v1803, 윈도우 서버 2016 v1803 |
|-------------------------|----------------|-------------------------------------------|---------------------------------------------|------------------------------------|---------------------------------------------------------|----------------------------|-------------------------------------------------|-------------------------------------------------|
| 스토리지                 | disk.sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.17638 / 6.2.9200.21757 - KB3137061 | 6.3.9600.18203 - KB3137061         | -                                                       | -                          | -                                               | -                                               |
|                         | storport.sys   | 6.1.7601.23403 - KB3125574                | 6.2.9200.17188 / 6.2.9200.21306 - KB3018489 | 6.3.9600.18573 - KB4022726         | 10.0.14393.1358 - KB4022715                             | 10.0.15063.332             | -                                               | -                                               |
|                         | ntfs.sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.17623 / 6.2.9200.21743 - KB3121255 | 6.3.9600.18654 - KB4022726         | 10.0.14393.1198 - KB4022715                             | 10.0.15063.447             | -                                               | -                                               |
|                         | Iologmsg.dll   | 6.1.7601.23403 - KB3125574                | 6.2.9200.16384 - KB2995387                  | -                                  | -                                                       | -                          | -                                               | -                                               |
|                         | Classpnp.sys   | 6.1.7601.23403 - KB3125574                | 6.2.9200.17061 / 6.2.9200.21180 - KB2995387 | 6.3.9600.18334 - KB3172614         | 10.0.14393.953 - KB4022715                              | -                          | -                                               | -                                               |
|                         | Volsnap.sys    | 6.1.7601.23403 - KB3125574                | 6.2.9200.17047 / 6.2.9200.21165 - KB2975331 | 6.3.9600.18265 - KB3145384         | -                                                       | 10.0.15063.0               | -                                               | -                                               |
|                         | partmgr.sys    | 6.1.7601.23403 - KB3125574                | 6.2.9200.16681 - KB2877114                  | 6.3.9600.17401 - KB3000850         | 10.0.14393.953 - KB4022715                              | 10.0.15063.0               | -                                               | -                                               |
|                         | volmgr.sys     |                                           |                                             |                                    |                                                         | 10.0.15063.0               | -                                               | -                                               |
|                         | Volmgrx.sys    | 6.1.7601.23403 - KB3125574                | -                                           | -                                  | -                                                       | 10.0.15063.0               | -                                               | -                                               |
|                         | Msiscsi.sys    | 6.1.7601.23403 - KB3125574                | 6.2.9200.21006 - KB2955163                  | 6.3.9600.18624 - KB4022726         | 10.0.14393.1066 - KB4022715                             | 10.0.15063.447             | -                                               | -                                               |
|                         | Msdsm.sys      | 6.1.7601.23403 - KB3125574                | 6.2.9200.21474 - KB3046101                  | 6.3.9600.18592 - KB4022726         | -                                                       | -                          | -                                               | -                                               |
|                         | Mpio.sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.21190 - KB3046101                  | 6.3.9600.18616 - KB4022726         | 10.0.14393.1198 - KB4022715                             | -                          | -                                               | -                                               |
|                         | vmstorfl.sys   | 6.3.9600.18907 - KB4072650                | 6.3.9600.18080 - KB3063109                  | 6.3.9600.18907 - KB4072650         | 10.0.14393.2007 - KB4345418                             | 10.0.15063.850 - KB4345419 | 10.0.16299.371 - KB4345420                      | -                                               |
|                         | Fveapi.dll     | 6.1.7601.23311 - KB3125574                | 6.2.9200.20930 - KB2930244                  | 6.3.9600.18294 - KB3172614         | 10.0.14393.576 - KB4022715                              | -                          | -                                               | -                                               |
|                         | Fveapibase.dll | 6.1.7601.23403 - KB3125574                | 6.2.9200.20930 - KB2930244                  | 6.3.9600.17415 - KB3172614         | 10.0.14393.206 - KB4022715                              | -                          | -                                               | -                                               |
| 네트워크                 | netvsc.sys     | -                                         | -                                           | -                                  | 10.0.14393.1198 - KB4022715                             | 10.0.15063.250 - KB4020001 | -                                               | -                                               |
|                         | mrxsmb10.sys   | 6.1.7601.23816 - KB4022722                | 6.2.9200.22108 - KB4022724                  | 6.3.9600.18603 - KB4022726         | 10.0.14393.479 - KB4022715                              | 10.0.15063.483             | -                                               | -                                               |
|                         | mrxsmb20.sys   | 6.1.7601.23816 - KB4022722                | 6.2.9200.21548 - KB4022724                  | 6.3.9600.18586 - KB4022726         | 10.0.14393.953 - KB4022715                              | 10.0.15063.483             | -                                               | -                                               |
|                         | mrxsmb.sys     | 6.1.7601.23816 - KB4022722                | 6.2.9200.22074 - KB4022724                  | 6.3.9600.18586 - KB4022726         | 10.0.14393.953 - KB4022715                              | 10.0.15063.0               | -                                               | -                                               |
|                         | tcpip.sys      | 6.1.7601.23761 - KB4022722                | 6.2.9200.22070 - KB4022724                  | 6.3.9600.18478 - KB4022726         | 10.0.14393.1358 - KB4022715                             | 10.0.15063.447             | -                                               | -                                               |
|                         | http.sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.17285 - KB3042553                  | 6.3.9600.18574 - KB4022726         | 10.0.14393.251 - KB4022715                              | 10.0.15063.483             | -                                               | -                                               |
|                         | vmswitch.sys   | 6.1.7601.23727 - KB4022719                | 6.2.9200.22117 - KB4022724                  | 6.3.9600.18654 - KB4022726         | 10.0.14393.1358 - KB4022715                             | 10.0.15063.138             | -                                               | -                                               |
| 핵심                    | ntoskrnl.exe   | 6.1.7601.23807 - KB4022719                | 6.2.9200.22170 - KB4022718                  | 6.3.9600.18696 - KB4022726         | 10.0.14393.1358 - KB4022715                             | 10.0.15063.483             | -                                               | -                                               |
| 원격 데스크톱 서비스 | rdpcorets.dll  | 6.2.9200.21506 - KB4022719                | 6.2.9200.22104 - KB4022724                  | 6.3.9600.18619 - KB4022726         | 10.0.14393.1198 - KB4022715                             | 10.0.15063.0               | -                                               | -                                               |
|                         | termsrv.dll    | 6.1.7601.23403 - KB3125574                | 6.2.9200.17048 - KB2973501                  | 6.3.9600.17415 - KB3000850         | 10.0.14393.0 - KB4022715                                | 10.0.15063.0               | -                                               | -                                               |
|                         | termdd.sys     | 6.1.7601.23403 - KB3125574                | -                                           | -                                  | -                                                       | -                          | -                                               | -                                               |
|                         | win32k.sys     | 6.1.7601.23807 - KB4022719                | 6.2.9200.22168 - KB4022718                  | 6.3.9600.18698 - KB4022726         | 10.0.14393.594 - KB4022715                              | -                          | -                                               | -                                               |
|                         | rdpdd.dll      | 6.1.7601.23403 - KB3125574                | -                                           | -                                  | -                                                       | -                          | -                                               | -                                               |
|                         | rdpwd.sys      | 6.1.7601.23403 - KB3125574                | -                                           | -                                  | -                                                       | -                          | -                                               | -                                               |
| 보안                | MS17-010       | KB4012212                                 | KB4012213                                   | KB4012213                          | KB4012606                                               | KB4012606                  | -                                               | -                                               |
|                         |                |                                           | KB4012216                                   |                                    | KB4013198                                               | KB4013198                  | -                                               | -                                               |
|                         |                | KB4012215                                 | KB4012214                                   | KB4012216                          | KB4013429                                               | KB4013429                  | -                                               | -                                               |
|                         |                |                                           | KB4012217                                   |                                    | KB4013429                                               | KB4013429                  | -                                               | -                                               |
|                         | CVE-2018-0886  | KB4103718               | KB4103730                | KB4103725       | KB4103723                                               | KB4103731                  | KB4103727                                       | KB4103721                                       |
|                         |                | KB4103712          | KB4103726          | KB4103715|                                                         |                            |                                                 |                                                 |
       
> [!NOTE]
> VM 프로비저닝 중에 실수로 재부팅되지 않도록 하려면 모든 Windows 업데이트 설치가 완료되고 업데이트가 보류 중이 지 확실하지 않은지 확인하는 것이 좋습니다. 이 작업을 수행하는 한 가지 방법은 Sysprep 명령을 실행하기 전에 가능한 모든 Windows 업데이트를 설치하고 한 번 재부팅하는 것입니다.

### <a name="determine-when-to-use-sysprep"></a>Sysprep 사용 시기 결정<a id="step23"></a>    

시스템 준비 도구(Sysprep)는 Windows 설치를 재설정하기 위해 실행할 수 있는 프로세스입니다. Sysprep은 모든 개인 데이터를 제거하고 여러 구성 요소를 재설정하여 "즉시" 환경을 제공합니다. 

일반적으로 Sysprep을 실행하여 특정 구성이 있는 여러 다른 VM을 배포할 수 있는 템플릿을 만듭니다. 템플릿을 *일반화된 이미지라고*합니다.

하나의 디스크에서 하나의 VM만 만들려면 Sysprep을 사용할 필요가 없습니다. 대신 *특수 이미지에서*VM을 만들 수 있습니다. 특수 디스크에서 VM을 만드는 방법에 대한 자세한 내용은 다음을 참조하십시오.

- [특수화된 디스크에서 VM 만들기](create-vm-specialized.md)
- [특수화된 VHD 디스크에서 VM 만들기](https://docs.microsoft.com/azure/virtual-machines/windows/create-vm-specialized-portal?branch=master)

일반화된 이미지를 만들려면 Sysprep을 실행해야 합니다. 자세한 내용은 [Sysprep 사용 방법: 소개를](https://technet.microsoft.com/library/bb457073.aspx)참조하십시오. 

Windows 기반 컴퓨터에 설치된 모든 역할이나 응용 프로그램이 일반화된 이미지를 지원하는 것은 아닙니다. 따라서 이 절차를 실행하기 전에 Sysprep이 컴퓨터의 역할을 지원하는지 확인하십시오. 자세한 내용은 [Sysprep의 서버 역할 지원](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)을 참조하세요.

### <a name="generalize-a-vhd"></a>VHD 일반화

>[!NOTE]
> 다음 단계에서 `sysprep.exe` 실행한 후 VM을 끕니다. Azure에서 이미지를 만들 때까지 다시 켜지 마십시오.

1. Windows VM에 로그인합니다.
1. 관리자 권한으로 **명령 프롬프트**를 실행합니다. 
1. 디렉터리를 로 `%windir%\system32\sysprep`변경합니다. 그런 다음, `sysprep.exe`을 실행합니다.
1. **시스템 준비 도구** 대화 상자에서 **시스템 OOBE(첫 실행 경험) 입력**을 선택하고 **일반화** 확인란을 선택했는지 확인합니다.

    ![시스템 준비 도구](media/prepare-for-upload-vhd-image/syspre.png)
1. **종료 옵션**에서 **종료**를 선택하고
1. **확인**을 선택합니다.
1. Sysprep이 완료되면 VM을 종료합니다. **다시 시작을** 사용하여 VM을 종료하지 마십시오.

이제 VHD를 업로드할 수 있습니다. 일반화된 디스크에서 VM을 만드는 방법에 대한 자세한 내용은 [일반화된 VHD 업로드를 참조하여 Azure에서 새 VM을 만드는 데 사용합니다.](sa-upload-generalized.md)


>[!NOTE]
> 사용자 지정 *unattend.xml* 파일은 지원되지 않습니다. `additionalUnattendContent` 속성을 지원하지만 Azure 프로비저닝 에이전트가 사용하는 *unattend.xml* 파일에 [microsoft-windows 셸 설치](https://docs.microsoft.com/windows-hardware/customize/desktop/unattend/microsoft-windows-shell-setup) 옵션을 추가하기 위한 제한된 지원만 제공합니다. 예를 들어 [추가Unattend콘텐츠에서](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.compute.models.additionalunattendcontent?view=azure-dotnet) FirstLogonCommand 및 LogonCommands를 추가할 수 있습니다. 자세한 내용은 [추가를 참조하십시오Unattend콘텐츠 첫 번째 로그온 명령 예제](https://github.com/Azure/azure-quickstart-templates/issues/1407).


## <a name="complete-the-recommended-configurations"></a>권장 구성 완료
다음 설정은 VHD 업로드에 영향을 주지 않습니다. 단, 반드시 구성하는 것이 좋습니다.

* Azure [가상 시스템 에이전트를](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)설치합니다. 그런 다음 VM 확장을 사용하도록 설정할 수 있습니다. VM 확장은 VM과 함께 사용할 수 있는 대부분의 중요한 기능을 구현합니다. 예를 들어 암호를 재설정하거나 RDP를 구성하려면 확장이 필요합니다. 자세한 내용은 [Azure 가상 컴퓨터 에이전트 개요를](../extensions/agent-windows.md)참조하십시오.
* Azure에서 VM을 만든 후 성능을 향상시키기 위해 페이지 파일을 *임시 드라이브 볼륨에* 넣는 것이 좋습니다. 다음과 같이 파일 배치를 설정할 수 있습니다.

   ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Memory Management' -Name "PagingFiles" -Value "D:\pagefile.sys" -Type MultiString -Force
   ```
  데이터 디스크가 VM에 연결된 경우 임시 드라이브 볼륨의 문자는 일반적으로 *D입니다.* 이 지정은 설정 및 사용 가능한 드라이브 수에 따라 다를 수 있습니다.
  * 바이러스 백신 소프트웨어에서 제공할 수 있는 스크립트 차단기를 사용하지 않도록 설정하는 것이 좋습니다. 이미지에서 새 VM을 배포할 때 실행되는 Windows 프로비저닝 에이전트 스크립트를 방해하고 차단할 수 있습니다.
  
## <a name="next-steps"></a>다음 단계
* [Resource Manager 배포를 위해 Azure에 Windows VM 이미지 업로드](upload-generalized-managed.md)
* [Azure Windows VM 정품 인증 문제 해결](troubleshoot-activation-problems.md)

