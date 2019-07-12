---
title: Azure에 업로드할 Windows VHD 준비 | Microsoft Docs
description: Azure에 업로드할 Windows VHD 또는 VHDX를 준비 하는 방법 알아보기
services: virtual-machines-windows
documentationcenter: ''
author: glimoli
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 7802489d-33ec-4302-82a4-91463d03887a
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 05/11/2019
ms.author: genli
ms.openlocfilehash: f40b3e0d2a49f6522149a977572d4f3c12e34255
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67720057"
---
# <a name="prepare-a-windows-vhd-or-vhdx-to-upload-to-azure"></a>Azure에 업로드할 Windows VHD 또는 VHDX 준비

Windows 가상 컴퓨터 (VM)를 온-프레미스에서 Azure에 업로드 하기 전에 가상 하드 디스크 (VHD 또는 VHDX)를 준비 해야 합니다. Azure는 1 세대 및 2 세대 Vm VHD 파일 형식에 있고 고정 크기 디스크를 모두 지원 합니다. VHD에 허용되는 최대 크기는 1,023GB입니다. 

1 세대에서 VM을 VHDX 파일 시스템을 VHD로 변환할 수 있습니다. 또한 고정 크기 디스크를 동적 확장 디스크를 변환할 수 있습니다. 하지만 VM의 세대는 변경할 수 없습니다. 자세한 내용은 참조 하세요. [수립 해야 하는 1 또는 2 세대 Hyper-v에서 VM?](https://technet.microsoft.com/windows-server-docs/compute/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v) 및 [2 개의 Vm (미리 보기)를 생성 하기 위해 Azure 지원](generation-2.md).

Azure Vm에 대 한 지원 정책에 대 한 정보를 참조 하세요 [Azure Vm에 대 한 Microsoft 서버 소프트웨어 지원](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)합니다.

> [!NOTE]
> 이 문서의 지침은 Windows Server 2008 R2 및 이상 Windows Server 운영 체제의 64 비트 버전에 적용 됩니다. Azure에서 32 비트 운영 체제를 실행 하는 방법에 대 한 내용은 [지원 Azure Vm에서 32 비트 운영 체제용](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines)합니다.

## <a name="convert-the-virtual-disk-to-a-fixed-size-and-to-vhd"></a>고정된 크기 VHD로 가상 디스크 변환 
가상 디스크를 Azure에 필요한 형식으로 변환해야 할 경우 이 섹션의 방법 중 하나를 사용합니다. 가상 디스크를 변환 하기 전에 VM을 백업 합니다. Windows VHD는 로컬 서버에서 제대로 작동 하는지 확인 합니다. 다음 변환 하거나 Azure에 업로드 하기 전에 VM 자체 내에서 오류를 해결 합니다.

디스크를 변환한 후 디스크를 사용 하는 VM을 만듭니다. 시작 하 고 업로드 하기 위한 준비를 완료 하려면 VM에 로그인 합니다.

### <a name="use-hyper-v-manager-to-convert-the-disk"></a>Hyper-v 관리자를 사용 하 여 디스크 변환 
1. Hyper-V 관리자를 열고 왼쪽에서 로컬 컴퓨터를 선택합니다. 컴퓨터 목록 위의 메뉴에서 선택 **동작** > **디스크 편집**합니다.
2. 에 **가상 하드 디스크 찾기** 페이지에서 가상 디스크를 선택 합니다.
3. 에 **작업 선택** 페이지에서 **변환** > **다음**합니다.
4. VHDX에서 변환 해야 할 경우 선택 **VHD** > **다음**합니다.
5. 동적 확장 디스크에서 변환 해야 하는 경우 선택할 **고정 크기** > **다음**합니다.
6. 새 VHD 파일을 저장할 경로를 찾아 선택합니다.
7. **마침**을 선택합니다.

> [!NOTE]
> 이 문서의 명령을 실행 하려면 관리자 권한 PowerShell 세션을 사용 합니다.

### <a name="use-powershell-to-convert-the-disk"></a>PowerShell을 사용 하 여 디스크 변환 하려면 
Windows PowerShell의 [Convert-VHD](https://technet.microsoft.com/library/hh848454.aspx) 명령을 사용하여 가상 디스크를 변환할 수 있습니다. PowerShell을 시작할 때 **관리자 권한으로 실행**을 선택합니다. 

다음 예제 명령은 VHDX에서 vhd 디스크를 변환합니다. 또한 고정 크기 디스크 명령을 동적 확장 디스크에서이 디스크를 변환합니다.

```Powershell
Convert-VHD –Path c:\test\MY-VM.vhdx –DestinationPath c:\test\MY-NEW-VM.vhd -VHDType Fixed
```

이 명령에 대 한 값을 바꿉니다 `-Path` 변환 하려는 가상 하드 디스크에 대 한 경로 사용 하 여 합니다. 에 대 한 값을 바꿉니다 `-DestinationPath` 새 경로와 변환된 된 디스크의 이름입니다.

### <a name="convert-from-vmware-vmdk-disk-format"></a>VMware VMDK 디스크 형식에서 변환
Windows VM 이미지가 있는 경우는 [VMDK 파일 형식](https://en.wikipedia.org/wiki/VMDK)를 사용 합니다 [Microsoft Virtual Machine Converter](https://www.microsoft.com/download/details.aspx?id=42497) VHD 형식으로 변환할 수입니다. 자세한 내용은 [VMware VMDK를 Hyper-v VHD로 변환 하는 방법을](https://blogs.msdn.com/b/timomta/archive/2015/06/11/how-to-convert-a-vmware-vmdk-to-hyper-v-vhd.aspx)합니다.

## <a name="set-windows-configurations-for-azure"></a>Azure에 대한 Windows 구성 설정

Azure에 업로드 하려는 VM에서에서 다음 명령을 실행 하는 [관리자 권한 명령 프롬프트 창을](https://technet.microsoft.com/library/cc947813.aspx):

1. 라우팅 테이블에서 정적 영구 경로를 제거합니다.
   
   * 경로 테이블을 보려면 명령 프롬프트에서 `route print`를 실행합니다.
   * 확인 된 `Persistence Routes` 섹션입니다. 영구 경로 사용 하 여는 `route delete` 명령을 제거 합니다.
2. WinHTTP 프록시를 제거합니다.
   
    ```PowerShell
    netsh winhttp reset proxy
    ```

    VM을 특정 프록시를 사용 하는 경우에 Azure IP 주소에 프록시 예외를 추가 하십시오 ([168.63.129.16](https://blogs.msdn.microsoft.com/mast/2015/05/18/what-is-the-ip-address-168-63-129-16/
)) VM은 Azure에 연결할 수 있도록 합니다.
    ```
    $proxyAddress="<your proxy server>"
    $proxyBypassList="<your list of bypasses>;168.63.129.16"

    netsh winhttp set proxy $proxyAddress $proxyBypassList
    ```

3. 디스크 SAN 정책을 설정 [ `Onlineall` ](https://technet.microsoft.com/library/gg252636.aspx):
   
    ```PowerShell
    diskpart 
    ```
    명령 프롬프트 열기 창에서 다음 명령을 입력 합니다.

     ```DISKPART
    san policy=onlineall
    exit   
    ```

4. Windows에 대 한 utc (협정 세계시) 시간을 설정 합니다. Windows 시간 서비스의 시작 유형을 설정 (`w32time`)를 `Automatic`:
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\TimeZoneInformation' -name "RealTimeIsUniversal" -Value 1 -Type DWord -force

    Set-Service -Name w32time -StartupType Automatic
    ```
5. 고성능을 전원 프로필을 설정 합니다.

    ```PowerShell
    powercfg /setactive SCHEME_MIN
    ```
6. 환경 변수 했는지 `TEMP` 및 `TMP` 기본값으로 설정 됩니다.

    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Environment' -name "TEMP" -Value "%SystemRoot%\TEMP" -Type ExpandString -force

    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Environment' -name "TMP" -Value "%SystemRoot%\TEMP" -Type ExpandString -force
    ```

## <a name="check-the-windows-services"></a>Windows 서비스 확인
다음 Windows 서비스의 각 Windows 기본 값으로 설정 되어 있는지 확인 합니다. 이러한 서비스는 최소한의 VM 연결을 위해 설정 해야 합니다. 시작 설정을 재설정하려면 다음 명령을 실행합니다.
   
```PowerShell
Set-Service -Name bfe -StartupType Automatic
Set-Service -Name dhcp -StartupType Automatic
Set-Service -Name dnscache -StartupType Automatic
Set-Service -Name IKEEXT -StartupType Automatic
Set-Service -Name iphlpsvc -StartupType Automatic
Set-Service -Name netlogon -StartupType Manual
Set-Service -Name netman -StartupType Manual
Set-Service -Name nsi -StartupType Automatic
Set-Service -Name termService -StartupType Manual
Set-Service -Name MpsSvc -StartupType Automatic
Set-Service -Name RemoteRegistry -StartupType Automatic
```

## <a name="update-remote-desktop-registry-settings"></a>원격 데스크톱 레지스트리 설정 업데이트
원격 액세스에 대 한 다음 설정을 올바르게 구성 되었는지 확인 합니다.

>[!NOTE] 
>실행할 때 오류 메시지가 나타날 `Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services -name &lt;object name&gt; -value &lt;value&gt;`합니다. 이 메시지를 안전 하 게 무시할 수 있습니다. 도메인 그룹 정책 개체를 통해 해당 구성을 푸시 하지는 것만 의미 합니다.

1. RDP(원격 데스크톱 프로토콜)이 활성화되어 있습니다.
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server' -name "fDenyTSConnections" -Value 0 -Type DWord -force

    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -name "fDenyTSConnections" -Value 0 -Type DWord -force
    ```
   
2. RDP 포트가 올바르게 설정 됩니다. 기본 포트 3389는:
   
    ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "PortNumber" -Value 3389 -Type DWord -force
    ```
    VM을 배포할 때 기본 규칙은 포트 3389에 대해 생성됩니다. 포트 번호를 변경 하려는 경우 수행 하는 Azure에서 VM을 배포한 후 합니다.

3. 수신기가 모든 네트워크 인터페이스에서 수신 대기합니다.
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "LanAdapter" -Value 0 -Type DWord -force
   ```
4. RDP 연결에 (NLA) 네트워크 수준 인증 모드를 구성 합니다.
   
    ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name "UserAuthentication" -Value 1 -Type DWord -force

    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name "SecurityLayer" -Value 1 -Type DWord -force

    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name "fAllowSecProtocolNegotiation" -Value 1 -Type DWord -force
     ```

5. 연결 유지 값을 설정합니다.
    
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -name "KeepAliveEnable" -Value 1  -Type DWord -force
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -name "KeepAliveInterval" -Value 1  -Type DWord -force
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "KeepAliveTimeout" -Value 1 -Type DWord -force
    ```
6. 다시 연결합니다.
    
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -name "fDisableAutoReconnect" -Value 0 -Type DWord -force
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "fInheritReconnectSame" -Value 1 -Type DWord -force
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "fReconnectSame" -Value 0 -Type DWord -force
    ```
7. 동시 연결 수를 제한합니다.
    
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "MaxInstanceCount" -Value 4294967295 -Type DWord -force
    ```
8. RDP 수신기에 연결 된 모든 자체 서명 된 인증서를 제거 합니다.
    
    ```PowerShell
    Remove-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name "SSLCertificateSHA1Hash" -force
    ```
    이 코드는 VM을 배포 하는 경우 시작 부분에 연결할 수 있는지 확인 합니다. 나중에 검토 해야 할 경우 Azure에서 VM을 배포한 후이 수행할 수 있습니다.

9. VM이 도메인의 일부가 될, 경우에 이전 설정이 없는 되돌릴 되도록 다음 정책을 확인 합니다. 
    
    | 목표                                     | 정책                                                                                                                                                       | 값                                                                                    |
    |------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------|
    | RDP 사용                           | Computer Configuration\Policies\Windows Settings\Administrative Templates\Components\Remote Desktop Services\Remote Desktop Session Host\Connections         | 사용자가 원격 데스크톱을 사용하여 원격으로 연결하도록 허용                                  |
    | NLA 그룹 정책                         | Settings\Administrative Templates\Components\Remote Desktop Services\Remote Desktop Session Host\Security                                                    | NLA를 사용 하 여 원격 액세스에 대 한 사용자 인증 필요 |
    | 연결 유지 설정                      | Computer Configuration\Policies\Windows Settings\Administrative Templates\Windows Components\Remote Desktop Services\Remote Desktop Session Host\Connections | 연결 유지 연결 간격 구성                                                 |
    | 다시 연결 설정                       | Computer Configuration\Policies\Windows Settings\Administrative Templates\Windows Components\Remote Desktop Services\Remote Desktop Session Host\Connections | 자동으로 다시 연결                                                                   |
    | 제한 된 개수의 연결 설정 | Computer Configuration\Policies\Windows Settings\Administrative Templates\Windows Components\Remote Desktop Services\Remote Desktop Session Host\Connections | 연결 수 제한                                                              |

## <a name="configure-windows-firewall-rules"></a>Windows 방화벽 규칙 구성
1. 세 개의 프로필 (도메인, 표준 및 공용)에서 Windows 방화벽을 설정 합니다.

   ```PowerShell
    Set-NetFirewallProfile -Profile Domain,Public,Private -Enabled True
   ```

2. 세 개의 방화벽 프로필 (도메인, 사설 및 공용)를 통한 WinRM을 허용 하는 PowerShell에서 다음 명령을 실행 하 고 PowerShell 원격 서비스를 사용 하도록 설정 합니다.
   
   ```PowerShell
    Enable-PSRemoting -force

    Set-NetFirewallRule -DisplayName "Windows Remote Management (HTTP-In)" -Enabled True
   ```
3. 다음 방화벽 규칙을 사용하도록 설정하여 RDP 트래픽을 허용합니다.

   ```PowerShell
    Set-NetFirewallRule -DisplayGroup "Remote Desktop" -Enabled True
   ```   
4. 파일 및 프린터 공유를 VM 가상 네트워크 내의 ping 명령에 응답할 수 있도록 하는 것에 대 한 규칙을 사용 합니다.

   ```PowerShell
   Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -Enabled True
   ``` 
5. VM이 도메인의 일부가 될, 경우에 이전 설정이 되돌릴 되지 있는지 확인 하려면 다음 Azure AD 정책을 확인 합니다. 

    | 목표                                 | 정책                                                                                                                                                  | 값                                   |
    |--------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------|
    | Windows 방화벽 프로필을 사용하도록 설정 | Computer Configuration\Policies\Windows Settings\Administrative Templates\Network\Network Connection\Windows Firewall\Domain Profile\Windows Firewall   | 모든 네트워크 연결 보호         |
    | RDP를 사용하도록 설정                           | Computer Configuration\Policies\Windows Settings\Administrative Templates\Network\Network Connection\Windows Firewall\Domain Profile\Windows Firewall   | 인바운드 원격 데스크톱 예외 허용 |
    |                                      | Computer Configuration\Policies\Windows Settings\Administrative Templates\Network\Network Connection\Windows Firewall\Standard Profile\Windows Firewall | 인바운드 원격 데스크톱 예외 허용 |
    | ICMP-V4를 사용하도록 설정                       | Computer Configuration\Policies\Windows Settings\Administrative Templates\Network\Network Connection\Windows Firewall\Domain Profile\Windows Firewall   | ICMP 예외 허용                   |
    |                                      | Computer Configuration\Policies\Windows Settings\Administrative Templates\Network\Network Connection\Windows Firewall\Standard Profile\Windows Firewall | ICMP 예외 허용                   |

## <a name="verify-the-vm"></a>VM 확인 

정상, 보안, VM이 있는지 확인 하 고 RDP에 액세스할 수 있습니다. 

1. 디스크가 정상이 고 일관 되는지 확인 합니다는 다음 VM 다시 시작할 때 디스크를 확인 합니다.

    ```PowerShell
    Chkdsk /f
    ```
    보고서에 정리 되어 있고 정상인 디스크가 표시 되는지 확인 합니다.

2. BCD(부팅 구성 데이터) 설정을 지정합니다. 

    > [!NOTE]
    > 관리자 권한 PowerShell 창을 사용 하 여 이러한 명령을 실행 합니다.
   
   ```powershell
    cmd

    bcdedit /set {bootmgr} integrityservices enable
    bcdedit /set {default} device partition=C:
    bcdedit /set {default} integrityservices enable
    bcdedit /set {default} recoveryenabled Off
    bcdedit /set {default} osdevice partition=C:
    bcdedit /set {default} bootstatuspolicy IgnoreAllFailures

    #Enable Serial Console Feature
    bcdedit /set {bootmgr} displaybootmenu yes
    bcdedit /set {bootmgr} timeout 5
    bcdedit /set {bootmgr} bootems yes
    bcdedit /ems {current} ON
    bcdedit /emssettings EMSPORT:1 EMSBAUDRATE:115200

    exit
   ```
3. 덤프 로그는 Windows 충돌 문제 해결에 유용할 수 있습니다. 덤프 로그 수집을 사용 하도록 설정 합니다.

    ```powershell
    # Set up the guest OS to collect a kernel dump on an OS crash event
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -name CrashDumpEnabled -Type DWord -force -Value 2
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -name DumpFile -Type ExpandString -force -Value "%SystemRoot%\MEMORY.DMP"
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -name NMICrashDump -Type DWord -force -Value 1

    # Set up the guest OS to collect user mode dumps on a service crash event
    $key = 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps'
    if ((Test-Path -Path $key) -eq $false) {(New-Item -Path 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting' -Name LocalDumps)}
    New-ItemProperty -Path $key -name DumpFolder -Type ExpandString -force -Value "c:\CrashDumps"
    New-ItemProperty -Path $key -name CrashCount -Type DWord -force -Value 10
    New-ItemProperty -Path $key -name DumpType -Type DWord -force -Value 2
    Set-Service -Name WerSvc -StartupType Manual
    ```
4. Windows Management Instrumentation (WMI) 리포지토리가 일관 되는지 확인 합니다.

    ```PowerShell
    winmgmt /verifyrepository
    ```
    리포지토리가 손상된 경우 [WMI: 리포지토리 손상 여부](https://blogs.technet.microsoft.com/askperf/2014/08/08/wmi-repository-corruption-or-not)합니다.

5. 다른 응용 프로그램 포트 3389를 사용 하 고 있는지 확인 합니다. 이 포트는 Azure의 RDP 서비스에 사용됩니다. VM에서 사용 되는 포트를 확인 하려면 실행 `netstat -anob`:

    ```PowerShell
    netstat -anob
    ```

6. Windows VHD를 업로드 하는 도메인 컨트롤러:

   * [이러한 추가 단계](https://support.microsoft.com/kb/2904015)에 따라 디스크를 준비합니다.

   * 특정 시점에 DSRM에서 VM을 시작 해야 하는 경우 디렉터리 서비스 복원 모드 (DSRM) 암호를 알고 있는지 확인 합니다. 자세한 내용은 [Set DSRM password](https://technet.microsoft.com/library/cc754363(v=ws.11).aspx)합니다.

7. 기본 제공 관리자 계정 및 암호를 알고 있는지 확인 합니다. 현재 로컬 관리자 암호를 재설정 하 고이 계정을 사용 하 여 RDP 연결을 통해 Windows에 로그인 수 있습니다. 이 액세스 권한은 "원격 데스크톱 서비스를 통한에서 로그온을 허용" 그룹 정책 개체에 의해 제어 됩니다. 로컬 그룹 정책 편집기에서 다음이 개체를 보려면:

    Computer Configuration\Windows Settings\Security Settings\Local Policies\User Rights Assignment

8. RDP를 통해 또는 네트워크에서 RDP 액세스를 차단 하지 않았는지 확인 하려면 다음 Azure AD 정책을 확인 합니다.

    - Computer Configuration\Windows Settings\Security Settings\Local Policies\User Rights Assignment\Deny access to this computer from the network

    - Computer Configuration\Windows Settings\Security Settings\Local Policies\User Rights Assignment\Deny log on through Remote Desktop Services


9. 필요한 액세스 계정 중 하나를 제거 하지 않습니다 하 고 있는지 확인 하려면 다음 Azure AD 정책을 확인 합니다.

   - 컴퓨터 구성 설정 \ 보안 설정 \ 로컬 정책 \ 사용자 권한 Assignment\Access 네트워크에서이 컴퓨터

   정책에는 다음 그룹을 나열 해야 합니다.

   - Administrators

   - 백업 운영자

   - 모든 사람

   - 사용자

10. Windows가 여전히 정상 상태 인지 하 고 RDP 연결을 통해 연결할 수 있도록 VM을 다시 시작 합니다. 이 시점에서 VM을 완전히 시작 되도록 하려면 로컬 Hyper-v에서 VM을 만들려면 좋습니다. 그런 다음 RDP 통해 VM을 연결할 수 있는지 테스트 합니다.

11. 모든 추가 전송 드라이버 인터페이스 (TDI) 필터를 제거 합니다. 예를 들어 TCP를 분석 하는 소프트웨어를 제거 패킷 또는 추가 방화벽입니다. 나중에 검토 해야 할 경우 Azure에서 VM을 배포한 후이 수행할 수 있습니다.

12. 다른 타사 소프트웨어 또는 실제 관련된 구성 요소 또는 다른 가상화 기술과 되는 드라이버를 제거 합니다.

### <a name="install-windows-updates"></a>Windows 업데이트를 설치 합니다.
컴퓨터에서 업데이트를 유지 해야 하는 이상적으로 *패치 수준*합니다. 이것이 불가능 한 경우 다음 업데이트가 설치 되어 있는지 확인 합니다.

| 구성 요소               | 이항         | Windows 7 SP1, Windows Server 2008 R2 SP1 | Windows 8, Windows Server 2012               | Windows 8.1, Windows Server 2012 R2 | Windows 10 v1607, Windows Server 2016 v1607 | Windows 10 v1703    | Windows 10 v1709, Windows Server 2016 v1709 | Windows 10 v1803, Windows Server 2016 v1803 |
|-------------------------|----------------|-------------------------------------------|---------------------------------------------|------------------------------------|---------------------------------------------------------|----------------------------|-------------------------------------------------|-------------------------------------------------|
| 저장 공간                 | disk.sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.17638 / 6.2.9200.21757 - KB3137061 | 6.3.9600.18203 - KB3137061         | -                                                       | -                          | -                                               | -                                               |
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
| Core                    | ntoskrnl.exe   | 6.1.7601.23807 - KB4022719                | 6.2.9200.22170 - KB4022718                  | 6.3.9600.18696 - KB4022726         | 10.0.14393.1358 - KB4022715                             | 10.0.15063.483             | -                                               | -                                               |
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
       
### Sysprep을 사용할 시기 결정 <a id="step23"></a>    

시스템 준비 도구 (Sysprep) 하는 프로세스는 Windows 설치를 다시 실행할 수 있습니다. Sysprep는 모든 개인 데이터를 제거 하 고 여러 구성 요소를 다시 설정 하 여 "기본" 환경을 제공 합니다. 

일반적으로 특정 구성을 가진 여러 Vm을 배포할 수 있는 템플릿을 만들려면 Sysprep를 실행 합니다. 템플릿을 호출 되는 *일반화 된 이미지*합니다.

한 디스크에서 하나의 VM을 만들려는 경우 Sysprep를 사용할 필요가 없습니다. 대신에서 VM을 만들 수는 *특수화 된 이미지*합니다. 특수화 된 디스크에서 VM을 만드는 방법에 대 한 정보를 참조 하세요.

- [특수화된 디스크에서 VM 만들기](create-vm-specialized.md)
- [특수화된 VHD 디스크에서 VM 만들기](https://docs.microsoft.com/azure/virtual-machines/windows/create-vm-specialized-portal?branch=master)

일반화 된 이미지를 만들려는 경우 Sysprep를 실행 해야 합니다. 자세한 내용은 참조 하세요. [Sysprep를 사용 하는 방법. 소개](https://technet.microsoft.com/library/bb457073.aspx)합니다. 

모든 역할 또는 응용 프로그램을 Windows 기반 컴퓨터에 설치 되어 있는 일반화 된 이미지를 지원 합니다. 따라서이 절차를 실행 하기 전에 Sysprep을 컴퓨터의 역할을 지원 하는지 확인 합니다. 자세한 내용은 [Sysprep의 서버 역할 지원](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)을 참조하세요.

### <a name="generalize-a-vhd"></a>VHD를 일반화

>[!NOTE]
> 실행 한 후 `sysprep.exe` VM에서 다음 단계를 해제 합니다. 없는 껐다가 다시 켭니다 Azure에서 이미지를 만들 때까지 합니다.

1. Windows VM에 로그인합니다.
1. 관리자 권한으로 **명령 프롬프트**를 실행합니다. 
1. 디렉터리를 `%windir%\system32\sysprep`입니다. 그런 다음, `sysprep.exe`을 실행합니다.
1. **시스템 준비 도구** 대화 상자에서 **시스템 OOBE(첫 실행 경험) 입력**을 선택하고 **일반화** 확인란을 선택했는지 확인합니다.

    ![시스템 준비 도구](media/prepare-for-upload-vhd-image/syspre.png)
1. **종료 옵션**에서 **종료**를 선택합니다.
1.           **확인**을 선택합니다.
1. Sysprep가 완료 되 면 VM을 종료 합니다. 사용 하지 마세요 **다시 시작** VM을 종료 합니다.

이제 VHD를 업로드할 수 있습니다. 일반화 된 디스크에서 VM을 만드는 방법에 대 한 자세한 내용은 참조 하세요. [일반화 된 VHD를 업로드 하 고 Azure에서 새 VM을 만드는 데 사용할](sa-upload-generalized.md)합니다.


>[!NOTE]
> 사용자 지정 *unattend.xml* 파일이 지원 되지 않습니다. 지원지 않습니다 하지만 합니다 `additionalUnattendContent` 추가 대 한 제한 된 지원만 제공 하는 속성을 [microsoft-windows-shell-설치 프로그램](https://docs.microsoft.com/windows-hardware/customize/desktop/unattend/microsoft-windows-shell-setup) 옵션에 *unattend.xml* 파일을 Azure 프로 비전 에이전트 사용합니다. 예를 들어 사용할 수 있습니다 [additionalUnattendContent](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.compute.models.additionalunattendcontent?view=azure-dotnet) FirstLogonCommands 및 LogonCommands를 추가 합니다. 자세한 내용은 [additionalUnattendContent FirstLogonCommands 예제](https://github.com/Azure/azure-quickstart-templates/issues/1407)합니다.


## <a name="complete-the-recommended-configurations"></a>권장된 구성을 완료 합니다.
다음 설정은 VHD 업로드 영향을 주지 않습니다. 단, 반드시 구성하는 것이 좋습니다.

* 설치 합니다 [Azure Virtual Machine 에이전트](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)합니다. 그런 다음 VM 확장을 사용하도록 설정할 수 있습니다. VM 확장은 대부분의 Vm에서 사용 하려는 중요 한 기능을 구현 합니다. 해야 확장, 예를 들어, RDP 구성 또는 암호를 재설정 하려고 합니다. 자세한 내용은 [Azure Virtual Machine 에이전트 개요](../extensions/agent-windows.md)합니다.
* Azure에서 VM을 만든 후 페이지 파일을 추가 하는 것이 좋습니다 합니다 *temporal 드라이브 볼륨* 성능 향상을 위해. 다음과 같이 파일 배치를 설정할 수 있습니다.

   ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Memory Management' -name "PagingFiles" -Value "D:\pagefile.sys" -Type MultiString -force
   ```
  데이터 디스크는 VM에 연결 된 경우 temporal 드라이브 볼륨의 문자는 일반적으로 *D*합니다. 이 지정 사용자 설정 및 사용 가능한 드라이브 수에 따라 다를 수 있습니다.

## <a name="next-steps"></a>다음 단계
* [Resource Manager 배포를 위해 Azure에 Windows VM 이미지 업로드](upload-generalized-managed.md)
* [Azure Windows VM 정품 인증 문제 해결](troubleshoot-activation-problems.md)

