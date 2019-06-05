---
title: Azure에 업로드할 Windows VHD 준비 | Microsoft Docs
description: Azure에 업로드하기 전에 Windows VHD 또는 VHDX를 준비하는 방법
services: virtual-machines-windows
documentationcenter: ''
author: glimoli
manager: jeconnoc
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
ms.openlocfilehash: 5ae0e7855db6bec9f48d2b9511f0d0626d883111
ms.sourcegitcommit: 179918af242d52664d3274370c6fdaec6c783eb6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/13/2019
ms.locfileid: "65561348"
---
# <a name="prepare-a-windows-vhd-or-vhdx-to-upload-to-azure"></a>Azure에 업로드할 Windows VHD 또는 VHDX 준비

Windows VM (가상 머신) 온-프레미스에서 Microsoft azure에 업로드 하기 전에 가상 하드 디스크 (VHD 또는 VHDX)를 준비 해야 합니다. Azure에서는 1 세대와 2 세대 Vm VHD 파일 형식 및가 고정 크기 디스크. VHD에 허용되는 최대 크기는 1,023GB입니다. 1세대 VM을 VHDX 파일 시스템에서 VHD로, 동적 확장 디스크에서 고정 크기로 변환할 수 있습니다. 하지만 VM의 세대는 변경할 수 없습니다. 자세한 내용은 참조 하세요. [1 또는 2 세대를 만들어야에서 하이퍼-V VM](https://technet.microsoft.com/windows-server-docs/compute/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v) 하 고 [Azure에서 2 세대 Vm](generation-2.md)합니다.

Azure VM을 위한 지원 정책에 대한 자세한 내용은 [Microsoft Azure VM을 위한 Microsoft 서버 소프트웨어 지원](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)을 참조하세요.

> [!Note]
> 이 문서의 지침은 Windows Server 2008 R2 이상 Windows Server 운영 체제의 64비트 버전에 적용됩니다. Azure에서 실행 중인 32비트 버전 운영 체제에 대한 정보는 [Azure 가상 머신에서 32비트 운영 체제 지원](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines)을 참조하세요.

## <a name="convert-the-virtual-disk-to-vhd-and-fixed-size-disk"></a>가상 디스크를 VHD 및 고정된 크기 디스크로 변환 
가상 디스크를 Azure에 필요한 형식으로 변환해야 할 경우 이 섹션의 방법 중 하나를 사용합니다. 가상 디스크 변환 프로세스를 실행하기 전에 VM을 백업하고 Windows VHD가 로컬 서버에서 제대로 작동하는지 확인합니다. Azure로 변환하거나 업로드하기 전에 VM 자체 내에서 오류를 해결해 보세요.

디스크를 변환한 후 변환된 디스크를 사용하는 VM을 만듭니다. 시작하고 VM에 로그인하여 업로드를 위한 VM 준비를 완료합니다.

### <a name="convert-disk-using-hyper-v-manager"></a>Hyper-V 관리자를 사용하여 디스크 변환
1. Hyper-V 관리자를 열고 왼쪽에서 로컬 컴퓨터를 선택합니다. 컴퓨터 목록 위의 메뉴에서 **작업** > **디스크 편집**을 클릭합니다.
2. **가상 하드 디스크 찾기** 화면에서 가상 디스크를 찾아 선택합니다.
3. **작업 선택** 화면에서 **변환** 및 **다음**을 선택합니다.
4. VHDX에서 변환해야 하는 경우 **VHD**를 선택하고 **다음**을 클릭합니다.
5. 동적 확장 디스크에서 변환해야 하는 경우 **고정 크기**를 선택하고 **다음**을 클릭합니다.
6. 새 VHD 파일을 저장할 경로를 찾아 선택합니다.
7. **Finish**를 클릭합니다.

>[!NOTE]
>이 문서의 명령은 상승된 PowerShell 세션에서 실행되어야 합니다.

### <a name="convert-disk-by-using-powershell"></a>PowerShell을 사용한 디스크 변환
Windows PowerShell의 [Convert-VHD](https://technet.microsoft.com/library/hh848454.aspx) 명령을 사용하여 가상 디스크를 변환할 수 있습니다. PowerShell을 시작할 때 **관리자 권한으로 실행**을 선택합니다. 

다음 예제 명령은 VHDX에서 VHD로, 동적 확장 디스크에서 고정된 크기로 변환합니다.

```Powershell
Convert-VHD –Path c:\test\MY-VM.vhdx –DestinationPath c:\test\MY-NEW-VM.vhd -VHDType Fixed
```
이 명령에서 “-Path” 값을 변환하려는 가상 하드 디스크에 대한 경로로 바꾸고 “-DestinationPath” 값을 변환된 디스크에 대한 새 경로 및 이름으로 바꿉니다.

### <a name="convert-from-vmware-vmdk-disk-format"></a>VMware VMDK 디스크 형식에서 변환
[VMDK 파일 형식](https://en.wikipedia.org/wiki/VMDK)의 Windows VM 이미지가 있는 경우 [Microsoft VM Converter](https://www.microsoft.com/download/details.aspx?id=42497)를 사용하여 VHD로 변환합니다. 자세한 내용은 블로그 문서 [VMware VMDK를 Hyper-V VHD로 변환하는 방법](https://blogs.msdn.com/b/timomta/archive/2015/06/11/how-to-convert-a-vmware-vmdk-to-hyper-v-vhd.aspx)을 참조하세요.

## <a name="set-windows-configurations-for-azure"></a>Azure에 대한 Windows 구성 설정

Azure에 업로드하려는 VM에서 다음 단계의 모든 명령을 [관리자 권한 명령 프롬프트 창](https://technet.microsoft.com/library/cc947813.aspx)에서 실행합니다.

1. 라우팅 테이블에서 정적 영구 경로를 제거합니다.
   
   * 경로 테이블을 보려면 명령 프롬프트에서 `route print`를 실행합니다.
   * **지속성 경로** 섹션을 확인합니다. 지속성 경로가 있는 경우 **경로 삭제** 명령을 사용하여 경로를 제거합니다.
2. WinHTTP 프록시를 제거합니다.
   
    ```PowerShell
    netsh winhttp reset proxy
    ```

    VM이 특정 프록시를 사용해야 하는 경우 Azure IP 주소([168.63.129.16](https://blogs.msdn.microsoft.com/mast/2015/05/18/what-is-the-ip-address-168-63-129-16/
))에 프록시 예외를 추가해야 VM이 Azure에 연결됩니다.
    ```
    $proxyAddress="<your proxy server>"
    $proxyBypassList="<your list of bypasses>;168.63.129.16"

    netsh winhttp set proxy $proxyAddress $proxyBypassList
    ```

3. 디스크 SAN 정책을 [Onlineall](https://technet.microsoft.com/library/gg252636.aspx)로 설정합니다.
   
    ```PowerShell
    diskpart 
    ```
    명령 프롬프트 열기 창에서 다음 명령을 입력합니다.

     ```DISKPART
    san policy=onlineall
    exit   
    ```

4. Windows에 대해 UTC(협정 세계시) 시간을 설정하고 Windows Time(w32time) 서비스의 시작 형식을 **자동**으로 설정합니다.
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\TimeZoneInformation' -name "RealTimeIsUniversal" -Value 1 -Type DWord -force

    Set-Service -Name w32time -StartupType Automatic
    ```
5. 전원 프로필을 **고성능**으로 설정합니다.

    ```PowerShell
    powercfg /setactive SCHEME_MIN
    ```
6. **TEMP** 및 **TMP** 환경 변수가 기본값으로 설정되어 있는지 확인합니다.

    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Environment' -name "TEMP" -Value "%SystemRoot%\TEMP" -Type ExpandString -force

    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Environment' -name "TMP" -Value "%SystemRoot%\TEMP" -Type ExpandString -force
    ```

## <a name="check-the-windows-services"></a>Windows 서비스 확인
각 Windows 서비스가 **Windows 기본값**으로 설정되어 있는지 확인합니다. 이들은 VM이 연결되었는지 확인하려면 반드시 설정해야 하는 최소 서비스 수입니다. 시작 설정을 재설정하려면 다음 명령을 실행합니다.
   
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
원격 데스크톱 연결에 대해 다음 설정이 올바르게 구성되어 있는지 확인합니다.

>[!Note] 
>이러한 단계에서 **Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services -name &lt;object name&gt; -value &lt;value&gt;** 를 실행하면 오류 메시지가 나타날 수 있습니다. 오류 메시지는 무시해도 됩니다. 이는 도메인이 그룹 정책 개체를 통해 해당 구성을 푸시하지 않는다는 점만을 의미합니다.
>
>

1. RDP(원격 데스크톱 프로토콜)이 활성화되어 있습니다.
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server' -name "fDenyTSConnections" -Value 0 -Type DWord -force

    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -name "fDenyTSConnections" -Value 0 -Type DWord -force
    ```
   
2. RDP 포트가 올바르게 설정되어 있습니다(기본 포트 3389).
   
    ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "PortNumber" -Value 3389 -Type DWord -force
    ```
    VM을 배포할 때 기본 규칙은 포트 3389에 대해 생성됩니다. 포트 번호를 변경하려는 경우 Azure에서 VM을 배포한 후에 수행합니다.

3. 수신기가 모든 네트워크 인터페이스에서 수신 대기합니다.
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "LanAdapter" -Value 0 -Type DWord -force
   ```
4. RDP 연결에 대한 네트워크 수준 인증 모드를 구성합니다.
   
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
8. RDP 수신기에 연결된 자체 서명 인증서가 있는 경우 다음과 같이 제거합니다.
    
    ```PowerShell
    Remove-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name "SSLCertificateSHA1Hash" -force
    ```
    VM을 배포할 때 시작 부분에 연결할 수 있는지 확인하기 위한 것입니다. 필요한 경우 Azure에서 VM을 배포한 후 나중에도 이 항목을 검토할 수 있습니다.

9. VM이 도메인의 일부가 될 경우 다음 설정을 모두 확인하여 이전 설정이 되돌려지지 않도록 확인합니다. 확인이 필요한 정책은 다음과 같습니다.
    
    | 목표                                     | 정책                                                                                                                                                       | 값                                                                                    |
    |------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------|
    | RDP 사용                           | Computer Configuration\Policies\Windows Settings\Administrative Templates\Components\Remote Desktop Services\Remote Desktop Session Host\Connections         | 사용자가 원격 데스크톱을 사용하여 원격으로 연결하도록 허용                                  |
    | NLA 그룹 정책                         | Settings\Administrative Templates\Components\Remote Desktop Services\Remote Desktop Session Host\Security                                                    | 네트워크 수준 인증을 사용하여 원격 연결에 대한 사용자 인증 필요 |
    | 연결 유지 설정                      | Computer Configuration\Policies\Windows Settings\Administrative Templates\Windows Components\Remote Desktop Services\Remote Desktop Session Host\Connections | 연결 유지 연결 간격 구성                                                 |
    | 다시 연결 설정                       | Computer Configuration\Policies\Windows Settings\Administrative Templates\Windows Components\Remote Desktop Services\Remote Desktop Session Host\Connections | 자동 다시 연결                                                                   |
    | 연결 수 제한 설정 | Computer Configuration\Policies\Windows Settings\Administrative Templates\Windows Components\Remote Desktop Services\Remote Desktop Session Host\Connections | 연결 수 제한                                                              |

## <a name="configure-windows-firewall-rules"></a>Windows 방화벽 규칙 구성
1. 세 개 프로필(도메인, 표준 및 공용)에서 Windows 방화벽을 켭니다.

   ```PowerShell
    Set-NetFirewallProfile -Profile Domain,Public,Private -Enabled True
   ```

2. PowerShell에서 다음 명령을 실행하여 세 개의 방화벽 프로필(도메인, 프라이빗 및 공용)을 통한 WinRM을 허용하고 PowerShell 원격 서비스를 사용하도록 설정합니다.
   
   ```PowerShell
    Enable-PSRemoting -force

    Set-NetFirewallRule -DisplayName "Windows Remote Management (HTTP-In)" -Enabled True
   ```
3. 다음 방화벽 규칙을 사용하도록 설정하여 RDP 트래픽을 허용합니다.

   ```PowerShell
    Set-NetFirewallRule -DisplayGroup "Remote Desktop" -Enabled True
   ```   
4. VM이 Virtual Network 내의 ping 명령에 응답할 수 있도록 파일 및 프린터 공유 규칙을 사용합니다.

   ```PowerShell
   Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -Enabled True
   ``` 
5. VM이 도메인의 일부가 될 경우 다음 설정을 확인하여 이전 설정이 되돌려지지 않도록 확인합니다. 확인이 필요한 AD 정책은 다음과 같습니다.

    | 목표                                 | 정책                                                                                                                                                  | 값                                   |
    |--------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------|
    | Windows 방화벽 프로필을 사용하도록 설정 | Computer Configuration\Policies\Windows Settings\Administrative Templates\Network\Network Connection\Windows Firewall\Domain Profile\Windows Firewall   | 모든 네트워크 연결 보호         |
    | RDP를 사용하도록 설정                           | Computer Configuration\Policies\Windows Settings\Administrative Templates\Network\Network Connection\Windows Firewall\Domain Profile\Windows Firewall   | 인바운드 원격 데스크톱 예외 허용 |
    |                                      | Computer Configuration\Policies\Windows Settings\Administrative Templates\Network\Network Connection\Windows Firewall\Standard Profile\Windows Firewall | 인바운드 원격 데스크톱 예외 허용 |
    | ICMP-V4를 사용하도록 설정                       | Computer Configuration\Policies\Windows Settings\Administrative Templates\Network\Network Connection\Windows Firewall\Domain Profile\Windows Firewall   | ICMP 예외 허용                   |
    |                                      | Computer Configuration\Policies\Windows Settings\Administrative Templates\Network\Network Connection\Windows Firewall\Standard Profile\Windows Firewall | ICMP 예외 허용                   |

## <a name="verify-vm-is-healthy-secure-and-accessible-with-rdp"></a>VM이 정상 상태이고 안전하며 RDP로 액세스할 수 있는지 확인 
1. 디스크가 정상이고 일관되도록 하려면 다음 VM이 다시 시작할 때 디스크 검사 작업을 실행합니다.

    ```PowerShell
    Chkdsk /f
    ```
    보고서에 정리되어 있고 정상인 디스크가 표시되는지 확인합니다.

2. BCD(부팅 구성 데이터) 설정을 지정합니다. 

    > [!Note]
    > 승격된 PowerShell 창에서 이러한 명령을 실행해야 합니다.
   
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
3. 덤프 로그는 Windows 충돌 문제를 해결하는 데 도움이 될 수 있습니다. 덤프 로그 수집을 사용하도록 설정합니다.

    ```powershell
    # Setup the Guest OS to collect a kernel dump on an OS crash event
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -name CrashDumpEnabled -Type DWord -force -Value 2
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -name DumpFile -Type ExpandString -force -Value "%SystemRoot%\MEMORY.DMP"
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -name NMICrashDump -Type DWord -force -Value 1

    #Setup the Guest OS to collect user mode dumps on a service crash event
    $key = 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps'
    if ((Test-Path -Path $key) -eq $false) {(New-Item -Path 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting' -Name LocalDumps)}
    New-ItemProperty -Path $key -name DumpFolder -Type ExpandString -force -Value "c:\CrashDumps"
    New-ItemProperty -Path $key -name CrashCount -Type DWord -force -Value 10
    New-ItemProperty -Path $key -name DumpType -Type DWord -force -Value 2
    Set-Service -Name WerSvc -StartupType Manual
    ```
4. Windows Management Instrumentations 리포지토리가 일관되는지 확인합니다. 이를 수행하려면 다음 명령을 실행합니다.

    ```PowerShell
    winmgmt /verifyrepository
    ```
    리포지토리가 손상된 경우 [WMI: Repository Corruption, or Not](https://blogs.technet.microsoft.com/askperf/2014/08/08/wmi-repository-corruption-or-not)(WMI: 리포지토리 손상 여부)을 참조하세요.

5. 타사 애플리케이션이 포트 3389를 사용하지 않는지 확인합니다. 이 포트는 Azure의 RDP 서비스에 사용됩니다. **netstat-anob**를 실행하여 VM에서 사용되는 포트를 확인할 수 있습니다.

    ```PowerShell
    netstat -anob
    ```

6. 업로드하려는 Windows VHD가 도메인 컨트롤러인 경우 이러한 단계를 수행합니다.

    1. [이러한 추가 단계](https://support.microsoft.com/kb/2904015)에 따라 디스크를 준비합니다.

    1. 특정 시점에 DSRM에서 VM을 시작해야 하는 경우 DSRM 암호를 알고 있어야 합니다. 이 링크를 참조하여 [DSRM 암호](https://technet.microsoft.com/library/cc754363(v=ws.11).aspx)를 설정할 수 있습니다.

7. 기본 제공 관리자 계정 및 암호를 알고 있어야 합니다. 현재 로컬 관리자 암호를 다시 설정하고, 이 계정을 사용하여 RDP 연결을 통해 Windows에 로그인할 수 있는지 확인합니다. 이 액세스 권한은 "원격 데스크톱 서비스를 통한 로그온 허용" 그룹 정책 개체에 의해 제어됩니다. 이 개체는 다음 위치에 있는 로컬 그룹 정책 편집기에서 볼 수 있습니다.

    Computer Configuration\Windows Settings\Security Settings\Local Policies\User Rights Assignment

8. 다음 AD 정책을 확인하여 RDP를 통해 또는 네트워크에서 RDP 액세스를 차단하지는 않는지 확인합니다.

    - Computer Configuration\Windows Settings\Security Settings\Local Policies\User Rights Assignment\Deny access to this computer from the network

    - Computer Configuration\Windows Settings\Security Settings\Local Policies\User Rights Assignment\Deny log on through Remote Desktop Services


9. 다음 AD 정책을 확인하여 다음의 필수 액세스 계정을 제거하지 않는지 검토해야 합니다.

   - 컴퓨터 구성\Windows Settings\Security Settings\Local Policies\User Rights Assignment\네트워크에서 이 컴퓨팅에 액세스

     다음 그룹이 이 정책에 나열되어야 합니다.

   - 관리자
   - 백업 운영자
   - 모든 사람
   - 사용자

10. VM을 다시 시작하여 Windows가 여전히 정상 상태인지와 RDP 연결을 사용하여 연결할 수 있는지 확인합니다. 이 시점에서 사용자 로컬 Hyper-V에서 VM을 만들고, VM이 완벽히 시작되고 있는지 확인한 다음, RDP가 연결 가능한 상태인지 여부를 테스트할 수 있습니다.

11. TCP 패킷 또는 추가 방화벽을 분석하는 소프트웨어와 같은 추가 전송 드라이버 인터페이스 필터를 모두 제거합니다. 필요한 경우 Azure에서 VM을 배포한 후 나중에도 이 항목을 검토할 수 있습니다.

12. 실제 구성 요소 또는 다른 가상화 기술과 관련된 다른 타사 소프트웨어 및 드라이버를 제거합니다.

### <a name="install-windows-updates"></a>Windows 업데이트 설치
이상적인 구성은 **최신 컴퓨터의 패치 수준을 갖추는 것**입니다. 가능하지 않은 경우 다음 업데이트가 설치되어 있는지 확인합니다.

| 구성 요소               | 이진         | Windows 7 SP1, Windows Server 2008 R2 SP1 | Windows 8, Windows Server 2012               | Windows 8.1, Windows Server 2012 R2 | Windows 10 버전 1607 Windows Server 2016 버전 1607 | Windows 10 버전 1703    | Windows 10 1709, Windows Server 2016 버전 1709 | Windows 10 1803 Windows Server 2016 버전 1803 |
|-------------------------|----------------|-------------------------------------------|---------------------------------------------|------------------------------------|---------------------------------------------------------|----------------------------|-------------------------------------------------|-------------------------------------------------|
| Storage                 | disk.sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.17638 / 6.2.9200.21757 - KB3137061 | 6.3.9600.18203 - KB3137061         | -                                                       | -                          | -                                               | -                                               |
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
| 코어                    | ntoskrnl.exe   | 6.1.7601.23807 - KB4022719                | 6.2.9200.22170 - KB4022718                  | 6.3.9600.18696 - KB4022726         | 10.0.14393.1358 - KB4022715                             | 10.0.15063.483             | -                                               | -                                               |
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
       
### Sysprep를 사용하는 경우 <a id="step23"></a>    

Sysprep는 모든 개인 데이터를 제거하고 여러 구성 요소를 다시 설정함으로써 시스템의 설치를 다시 설정하고 “첫 실행 경험”을 제공하는 Windows 설치를 실행할 수 있는 프로세스입니다. 일반적으로 특정 구성이 있는 다른 여러 VM을 배포할 수 있는 템플릿을 만들려는 경우 이를 수행합니다. 이를 **일반화된 이미지**라고 합니다.

대신에 한 디스크에서 하나의 VM만 만들려는 경우 Sysprep를 사용할 필요가 없습니다. 이 경우 **특수화된 이미지**에서 VM을 만들면 됩니다.

특수화된 디스크에서 VM을 만드는 방법에 대한 자세한 내용은 다음을 참조하세요.

- [특수화된 디스크에서 VM 만들기](create-vm-specialized.md)
- [특수화된 VHD 디스크에서 VM 만들기](https://docs.microsoft.com/azure/virtual-machines/windows/create-vm-specialized-portal?branch=master)

일반화된 이미지를 만들려는 경우 Sysprep를 실행해야 합니다. Sysprep에 대한 자세한 내용은 [Sysprep 사용 방법: 소개](https://technet.microsoft.com/library/bb457073.aspx)를 참조하세요. 

Windows 기반 컴퓨터에 설치된 모든 역할 또는 애플리케이션이 이 일반화를 지원하는 것은 아닙니다. 따라서 이 절차를 실행하기 전, 다음 문서를 참조하여 해당 컴퓨터의 역할이 Sysprep에서 지원되는지 확인합니다. 자세한 내용은 [서버 역할에 대한 Sysprep 지원](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)을 참조하세요.

### <a name="steps-to-generalize-a-vhd"></a>VHD를 일반화하는 단계

>[!NOTE]
> 다음 단계에 지정된 대로 sysprep.exe를 실행한 후 VM을 끕니다. Azure에서 이미지를 만든 후에 다시 켜야 합니다.

1. Windows VM에 로그인합니다.
2. 관리자 권한으로 **명령 프롬프트**를 실행합니다. 
3. 디렉터리를 **%windir%\system32\sysprep**로 변경한 후 **sysprep.exe**를 실행합니다.
3. **시스템 준비 도구** 대화 상자에서 **시스템 OOBE(첫 실행 경험) 입력**을 선택하고 **일반화** 확인란을 선택했는지 확인합니다.

    ![시스템 준비 도구](media/prepare-for-upload-vhd-image/syspre.png)
4. **종료 옵션**에서 **종료**를 선택합니다.
5. **확인**을 클릭합니다.
6. Sysprep가 완료되면 VM을 종료합니다. VM을 종료할 때 **다시 시작**을 사용해서는 안 됩니다.
7. 이제 VHD를 업로드할 수 있습니다. 일반화된 디스크에서 VM을 만드는 방법에 대한 자세한 내용은 [일반화된 VHD를 업로드하고 Azure에서 새 VM 만들기](sa-upload-generalized.md)를 참조하세요.


>[!NOTE]
> 사용자 지정 unattend.xml은 지원되지 않습니다. additionalUnattendContent 속성을 지원하지만, Azure 프로비저닝 에이전트가 사용하는 unattend.xml에 [microsoft-windows-shell-setup](https://docs.microsoft.com/windows-hardware/customize/desktop/unattend/microsoft-windows-shell-setup) 옵션을 추가하기 위한 제한된 지원만 제공됩니다. 예를 들어  [additionalUnattendContent](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.compute.models.additionalunattendcontent?view=azure-dotnet)를 사용하여 FirstLogonCommands 및 LogonCommands를 추가할 수 있습니다. [additionalUnattendContent FirstLogonCommands 예제](https://github.com/Azure/azure-quickstart-templates/issues/1407)를 참조하세요.


## <a name="complete-recommended-configurations"></a>권장된 구성 완료
다음 설정은 VHD 업로드에 영향을 주지 않습니다. 단, 반드시 구성하는 것이 좋습니다.

* [Azure VM 에이전트](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)를 설치합니다. 그런 다음 VM 확장을 사용하도록 설정할 수 있습니다. VM 확장은 암호 재설정, RDP 구성 등 VM에서 사용하려는 대부분의 중요 기능을 구현합니다. 자세한 내용은 [Azure Virtual Machine 에이전트 개요](../extensions/agent-windows.md)합니다.
* Azure에서 VM을 만든 후에 성능 향상을 위해 “임시 드라이브” 볼륨에 페이지 파일을 배치하는 것이 좋습니다. 이 작업은 다음과 같이 준비할 수 있습니다.

   ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Memory Management' -name "PagingFiles" -Value "D:\pagefile.sys" -Type MultiString -force
   ```
  VM에 연결된 데이터 디스크가 있는 경우 Temporal 드라이브 볼륨의 드라이브 문자는 일반적으로 “D”입니다. 이 지정은 사용 가능한 드라이브 수 및 사용자가 지정한 설정에 따라 달라질 수 있습니다.

## <a name="next-steps"></a>다음 단계
* [Resource Manager 배포를 위해 Azure에 Windows VM 이미지 업로드](upload-generalized-managed.md)
* [Windows Azure 가상 머신 정품 인증 문제 해결](troubleshoot-activation-problems.md)

