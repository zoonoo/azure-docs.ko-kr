---
title: "Azure에 업로드할 Windows VHD 준비 | Microsoft Docs"
description: "Azure에 업로드하기 전에 Windows VHD 또는 VHDX를 준비하는 방법"
services: virtual-machines-windows
documentationcenter: 
author: genlin
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 7802489d-33ec-4302-82a4-91463d03887a
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 1/11/2017
ms.author: glimoli;genli
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: 104063500a33dfe55c56467517a5002c562772df
ms.lasthandoff: 03/31/2017


---
# <a name="prepare-a-windows-vhd-or-vhdx-to-upload-to-azure"></a>Azure에 업로드할 Windows VHD 또는 VHDX 준비
온-프레미스에서 Azure로 Windows VM을 업로드하려면 가상 하드 디스크(VHD 또는 VHDX)를 준비해야 합니다. Azure에서는 VHD 파일 형식의 고정된 크기의 디스크를 가진 1세대 가상 컴퓨터만 지원합니다. VHD에 허용되는 최대 크기는 1,023GB입니다. VHDX에서 VHD 파일 형식으로, 동적 확장에서 고정된 크기의 디스크로 1세대 가상 컴퓨터를 변환할 수 있습니다. 하지만 가상 컴퓨터의 세대를 변경할 수 없습니다. 자세한 내용은 [Hyper-V에 1 또는 2세대 가상 컴퓨터를 만들어야 합니까?](https://technet.microsoft.com/en-us/windows-server-docs/compute/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)를 참조하세요.

## <a name="convert-the-virtual-disk-to-vhd-and-fixed-size-disk"></a>가상 디스크를 VHD 및 고정된 크기 디스크로 변환 
가상 디스크를 Azure에 필요한 형식으로 변환해야 할 경우 이 섹션의 방법 중 하나를 사용합니다. 가상 디스크 변환 프로세스를 실행하기 전에 VM을 백업하고 Windows VHD가 로컬 서버에서 제대로 작동하는지 확인합니다. Azure로 변환하거나 업로드하기 전에 VM 자체 내에서 오류를 해결해 보세요.

디스크를 변환한 후 변환된 디스크를 사용하는 VM을 만듭니다. 시작하고 VM에 로그인하여 업로드를 위한 VM 준비를 완료합니다.

### <a name="convert-disk-using-hyper-v-manager"></a>Hyper-V 관리자를 사용하여 디스크 변환
1. Hyper-V 관리자를 열고 왼쪽에서 로컬 컴퓨터를 선택합니다. 위쪽 메뉴에서 **작업** > **디스크 편집**을 클릭합니다.
2. **가상 하드 디스크 찾기** 화면에서 가상 디스크를 찾아 선택합니다.
3. **작업 선택** 화면에서 **변환** 및 **다음**을 선택합니다.
4. VHDX에서 변환해야 하는 경우 **VHD**를 선택하고 **다음**을 클릭합니다.
5. 동적 확장 디스크에서 변환해야 하는 경우 **고정 크기**를 선택하고 **다음**을 클릭합니다.
6. 새 VHD 파일을 저장할 경로를 찾아 선택합니다.
7. **마침** 을 클릭하여 닫습니다.

### <a name="convert-disk-using-powershell"></a>PowerShell을 사용하여 디스크 변환
Windows PowerShell의 [Convert-VHD](http://technet.microsoft.com/library/hh848454.aspx) cmdlet을 사용하여 가상 디스크를 변환할 수 있습니다. PowerShell을 시작할 때 **관리자 권한으로 실행**을 선택합니다. 다음 예제에서는 VHDX에서 VHD로, 동적 확장 디스크에서 고정된 크기로 변환하는 방법을 보여 줍니다.

```powershell
Convert-VHD –Path c:\test\MY-VM.vhdx –DestinationPath c:\test\MY-NEW-VM.vhd -VHDType Fixed
```
Path 값을 변환하려는 가상 하드 디스크에 대한 경로로 대체하고 DestinationPath 값을 변환된 디스크에 대한 새 경로 및 이름으로 대체합니다.

### <a name="convert-from-vmware-vmdk-disk-format"></a>VMware VMDK 디스크 형식에서 변환
[VMDK 파일 형식](https://en.wikipedia.org/wiki/VMDK)의 Windows VM 이미지가 있는 경우 [Microsoft Virtual Machine Converter](https://www.microsoft.com/download/details.aspx?id=42497)를 사용하여 VHD로 변환합니다. 자세한 내용은 블로그 [VMware VMDK를 Hyper-V VHD로 변환하는 방법](http://blogs.msdn.com/b/timomta/archive/2015/06/11/how-to-convert-a-vmware-vmdk-to-hyper-v-vhd.aspx)을 참조하세요.

## <a name="set-windows-configurations-for-azure"></a>Azure에 대한 Windows 구성 설정

가상 컴퓨터에서 Azure로 업로드하려는 경우 명령 프롬프트 창에서 [관리자 권한](https://technet.microsoft.com/library/cc947813.aspx)으로 다음 모든 명령을 실행합니다.

1. 라우팅 테이블에서 정적 영구 경로를 제거합니다.
   
   * 경로 테이블을 보려면 명령 프롬프트 창에서 `route print`를 실행합니다.
   * **지속성 경로** 섹션을 확인합니다. 지속성 경로가 있는 경우 [경로 삭제](https://technet.microsoft.com/library/cc739598.apx) 를 사용하여 경로를 제거합니다.
2. WinHTTP 프록시를 제거합니다.
   
    ```CMD
    netsh winhttp reset proxy
    ```
3. 디스크 SAN 정책을 [Onlineall](https://technet.microsoft.com/library/gg252636.aspx)로 설정합니다. 
   
    ```CMD
    diskpart 
    san policy=onlineall
    exit
    ```
    

4. Windows에 대해 UTC(협정 세계시) 시간을 설정하고 Windows Time(w32time) 서비스의 시작 형식을 **자동**으로 설정합니다.
   
    ```CMD
    REG ADD HKLM\SYSTEM\CurrentControlSet\Control\TimeZoneInformation /v RealTimeIsUniversal /t REG_DWORD /d 1
    ```
    ```CMD
    sc config w32time start= auto
    ```

## <a name="set-services-startup-to-windows-default-values"></a>서비스 시작을 Windows 기본 값으로 설정
각 Windows 서비스가 **Windows 기본값**으로 설정되어 있는지 확인합니다. 시작 설정을 재설정하려면 다음 명령을 실행합니다.
   
```CMD
sc config bfe start= auto
   
sc config dcomlaunch start= auto
   
sc config dhcp start= auto
   
sc config dnscache start= auto
   
sc config IKEEXT start= auto
   
sc config iphlpsvc start= auto
   
sc config PolicyAgent start= demand
   
sc config LSM start= auto
   
sc config netlogon start= demand
   
sc config netman start= demand
   
sc config NcaSvc start= demand
   
sc config netprofm start= demand
   
sc config NlaSvc start= auto
   
sc config nsi start= auto
   
sc config RpcSs start= auto
   
sc config RpcEptMapper start= auto
   
sc config termService start= demand
   
sc config MpsSvc start= auto
   
sc config WinHttpAutoProxySvc start= demand
   
sc config LanmanWorkstation start= auto
   
sc config RemoteRegistry start= auto
```

## <a name="update-remote-desktop-registry-settings"></a>원격 데스크톱 레지스트리 설정 업데이트
1. RDP(원격 데스크톱 프로토콜) 수신기에 연결된 자체 서명 인증서가 있는 경우 다음과 같이 제거합니다.
   
    ```CMD
    REG DELETE "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp\SSLCertificateSHA1Hash”
    ```
   
    RDP 수신기에 대한 인증서 구성 방법에 대한 자세한 내용은 [Windows Server의 수신기 인증서 구성 ](https://blogs.technet.microsoft.com/askperf/2014/05/28/listener-certificate-configurations-in-windows-server-2012-2012-r2/)
2. RDP 서비스에 대한 [KeepAlive](https://technet.microsoft.com/library/cc957549.aspx) 값을 구성합니다.
   
    ```CMD
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v KeepAliveEnable /t REG_DWORD  /d 1 /f
    ```
    ```CMD
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v KeepAliveInterval /t REG_DWORD  /d 1 /f
    ```
    ```CMD
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v KeepAliveTimeout /t REG_DWORD /d 1 /f
    ```
3. RDP 서비스에 대한 인증 모드를 구성합니다.
   
    ```CMD
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v UserAuthentication /t REG_DWORD  /d 1 /f
   ```
    ```CMD
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SecurityLayer /t REG_DWORD  /d 1 /f
   ```
    ```CMD
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v fAllowSecProtocolNegotiation /t REG_DWORD  /d 1 /f
    ```
4. 레지스트리에 다음 하위 키를 추가하여 RDP 서비스를 사용하도록 설정합니다.
   
    ```CMD
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD  /d 0 /f
    ```

## <a name="configure-windows-firewall-rules"></a>Windows 방화벽 규칙 구성
1. PowerShell에서 다음 명령을 실행하여 세 개의 방화벽 프로필(도메인, 개인 및 공용)을 통한 WinRM을 허용하고 PowerShell 원격 서비스를 사용하도록 설정합니다.
   
   ```powershell
   Enable-PSRemoting -force
   ```
2. 명령 프롬프트 창에서 다음 명령을 실행하여 다음 게스트 운영 체제 방화벽 규칙이 준비되어 있는지 확인합니다.
   
   * 인바운드
   
   ```CMD
   netsh advfirewall firewall set rule dir=in name="File and Printer Sharing (Echo Request - ICMPv4-In)" new enable=yes
   
   netsh advfirewall firewall set rule dir=in name="Network Discovery (LLMNR-UDP-In)" new enable=yes
   
   netsh advfirewall firewall set rule dir=in name="Network Discovery (NB-Datagram-In)" new enable=yes
   
   netsh advfirewall firewall set rule dir=in name="Network Discovery (NB-Name-In)" new enable=yes
   
   netsh advfirewall firewall set rule dir=in name="Network Discovery (Pub-WSD-In)" new enable=yes
   
   netsh advfirewall firewall set rule dir=in name="Network Discovery (SSDP-In)" new enable=yes
   
   netsh advfirewall firewall set rule dir=in name="Network Discovery (UPnP-In)" new enable=yes
   
   netsh advfirewall firewall set rule dir=in name="Network Discovery (WSD EventsSecure-In)" new enable=yes
   
   netsh advfirewall firewall set rule dir=in name="Windows Remote Management (HTTP-In)" new enable=yes
   
   netsh advfirewall firewall set rule dir=in name="Windows Remote Management (HTTP-In)" new enable=yes
   ```
   
   * 인바운드 및 아웃바운드
   
   ```CMD
   netsh advfirewall firewall set rule group="Remote Desktop" new enable=yes
   
   netsh advfirewall firewall set rule group="Core Networking" new enable=yes
   ```
   
   * 아웃바운드
   
   ```CMD
   netsh advfirewall firewall set rule dir=out name="Network Discovery (LLMNR-UDP-Out)" new enable=yes
   
   netsh advfirewall firewall set rule dir=out name="Network Discovery (NB-Datagram-Out)" new enable=yes
   
   netsh advfirewall firewall set rule dir=out name="Network Discovery (NB-Name-Out)" new enable=yes
   
   netsh advfirewall firewall set rule dir=out name="Network Discovery (Pub-WSD-Out)" new enable=yes
   
   netsh advfirewall firewall set rule dir=out name="Network Discovery (SSDP-Out)" new enable=yes
   
   netsh advfirewall firewall set rule dir=out name="Network Discovery (UPnPHost-Out)" new enable=yes
   
   netsh advfirewall firewall set rule dir=out name="Network Discovery (UPnP-Out)" new enable=yes
   
   netsh advfirewall firewall set rule dir=out name="Network Discovery (WSD Events-Out)" new enable=yes
   
   netsh advfirewall firewall set rule dir=out name="Network Discovery (WSD EventsSecure-Out)" new enable=yes
   
   netsh advfirewall firewall set rule dir=out name="Network Discovery (WSD-Out)" new enable=yes
   ```

## <a name="verify-vm-is-healthy-secure-and-accessible-with-rdp"></a>VM이 정상 상태이고 안전하며 RDP로 액세스할 수 있는지 확인 
1. 명령 프롬프트 창에서 `winmgmt /verifyrepository`를 실행하여 WMI(Windows Management Instrumentation) 리포지토리가 일관되는지 확인합니다. 리포지토리가 손상된 경우 블로그 게시물 [WMI: 저장소 손상 여부?](https://blogs.technet.microsoft.com/askperf/2014/08/08/wmi-repository-corruption-or-not)를 참조하세요.
2. BCD(부팅 구성 데이터) 설정 지정:
   
   ```CMD
   bcdedit /set {bootmgr} integrityservices enable
   
   bcdedit /set {default} device partition=C:
   
   bcdedit /set {default} integrityservices enable
   
   bcdedit /set {default} recoveryenabled Off
   
   bcdedit /set {default} osdevice partition=C:
   
   bcdedit /set {default} bootstatuspolicy IgnoreAllFailures
   ```
3. TCP 패킷을 분석하는 소프트웨어와 같은 추가 전송 드라이버 인터페이스 필터를 모두 제거합니다.
4. 디스크가 정상 상태이고 일관되는지 확인하려면 명령 프롬프트 창에서 `CHKDSK /f` 명령을 실행합니다. "Y"를 입력하여 검사를 예약하고 VM을 다시 시작합니다.
5. 실제 구성 요소 또는 다른 가상화 기술과 관련된 다른 타사 소프트웨어 및 드라이버를 제거합니다.
6. 타사 응용 프로그램이 포트 3389를 사용하지 않는지 확인합니다. 이 포트는 Azure의 RDP 서비스에 사용됩니다. 명령 프롬프트 창에서 `netstat -anob`를 실행하여 응용 프로그램에서 사용되는 포트를 볼 수 있습니다.
7. 업로드하려는 Windows VHD가 도메인 컨트롤러인 경우 [이러한 추가 단계](https://support.microsoft.com/kb/2904015) 를 수행하여 디스크를 준비합니다.
8. VM을 다시 부팅하여 Windows가 여전히 정상 상태인지와 RDP 연결을 사용하여 연결할 수 있는지 확인합니다.
9. 현재 로컬 관리자 암호를 복원하고, 이 계정을 사용하여 RDP 연결을 통해 Windows에 로그인할 수 있는지 확인합니다. 이 액세스 권한은 "원격 데스크톱 서비스를 통한 로그온 허용" 그룹 정책 개체에 의해 제어됩니다. "컴퓨터 구성\Windows 설정\보안 설정\로컬 정책\사용자 권한 할당" 아래의 로컬 그룹 정책 편집기에서 이 개체를 볼 수 있습니다.

## <a name="install-windows-updates"></a>Windows 업데이트 설치
Windows용 최신 업데이트를 설치합니다. 가능하지 않은 경우 다음 업데이트가 설치되어 있는지 확인합니다.
   
   * [KB3137061](https://support.microsoft.com/kb/3137061) Microsoft Azure VM은 데이터 중단에서 복구되지 않으며 데이터 손상 문제가 발생함
   * [KB3115224](https://support.microsoft.com/kb/3115224) Windows Server 2012 R2 또는 Windows Server 2012 호스트에서 실행되는 VM에 대한 안정성 개선
   * [KB3140410](https://support.microsoft.com/kb/3140410) MS16-031: 권한 상승 문제를 해결하기 위한 Microsoft Windows용 보안 업데이트: 2016년 3월 8일
   * [KB3063075](https://support.microsoft.com/kb/3063075) Microsoft Azure에서 Windows Server 2012 R2 가상 컴퓨터를 실행할 때 많은 ID 129 이벤트가 기록됨
   * [KB3137061](https://support.microsoft.com/kb/3137061) Microsoft Azure VM은 데이터 중단에서 복구되지 않으며 데이터 손상 문제가 발생함
   * [KB3114025](https://support.microsoft.com/kb/3114025) Windows 8.1 또는 Server 2012 R2에서 Azure 파일 저장소에 액세스할 때 성능이 저하됨
   * [KB3033930](https://support.microsoft.com/kb/3033930) Windows에서 Azure 서비스에 대한 프로세스당 RIO 버퍼의 64K 제한이 핫픽스로 인해 증가됨
   * [KB3004545](https://support.microsoft.com/kb/3004545) Windows의 VPN 연결을 통해 Azure 호스팅 서비스에서 호스트되는 가상 컴퓨터에 액세스할 수 없음
   * [KB3082343](https://support.microsoft.com/kb/3082343) Azure 사이트 간 VPN 터널이 Windows Server 2012 R2 RRAS를 사용할 때 크로스-프레미스 VPN 연결이 끊어짐
   * [KB3140410](https://support.microsoft.com/kb/3140410) MS16-031: 권한 상승 문제를 해결하기 위한 Microsoft Windows용 보안 업데이트: 2016년 3월 8일
   * [KB3146723](https://support.microsoft.com/kb/3146723) MS16-048: CSRSS용 보안 업데이트에 대한 설명: 2016년 4월 12일
   * [KB2904100](https://support.microsoft.com/kb/2904100) Windows에서 디스크 입/출력을 수행하는 동안 시스템이 중지됨
     
## Sysprep 실행 <a id="step23"></a>    
여러 VM에 배포할 이미지를 만들려는 경우 Azure에 VHD를 업로드하기 전에 [Sysprep을 실행하여 이미지를 일반화](generalize-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)해야 합니다. 특수화된 VHD를 사용하여 Sysprep을 실행하지 않아도 됩니다. 자세한 내용은 다음 문서를 참조하세요.
   
   * [Sysprep을 사용하여 Windows 가상 컴퓨터 일반화](generalize-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
   * [서버 역할에 대한 Sysprep 지원](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)

## <a name="complete-recommended-configurations"></a>권장된 구성 완료
다음 설정은 VHD 업로드에 영향을 주지 않습니다. 그러나 반드시 구성하는 것이 좋습니다.

* [Azure Virtual Machines 에이전트](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)를 설치합니다. 에이전트를 설치한 후에 VM 확장을 사용하도록 설정할 수 있습니다. VM 확장은 VM에서 사용하려는 대부분의 중요 기능을 구현합니다. 여기에는 암호 재설정, RDP 구성 등을 비롯한 많은 기능이 포함됩니다.
* 덤프 로그는 Windows 충돌 문제를 해결하는 데 도움이 될 수 있습니다. 덤프 로그 수집을 사용하도록 설정합니다.
  
    ```CMD
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 2 /f`
  
    REG ADD "HKLM\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps" /v DumpFolder /t REG_EXPAND_SZ /d "c:\CrashDumps" /f
  
    REG ADD "HKLM\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps" /v DumpCount /t REG_DWORD /d 10 /f
  
    REG ADD "HKLM\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps" /v DumpType /t REG_DWORD /d 2 /f
  
    sc config wer start= auto
    ```
* Azure에서 VM을 만든 후에 D: 드라이브에 시스템 정의 크기 페이지 파일을 구성합니다.
  
    ```CMD
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Session Manager\Memory Management" /t REG_MULTI_SZ /v PagingFiles /d "D:\pagefile.sys 0 0" /f
    ```

## <a name="next-steps"></a>다음 단계
* [Resource Manager 배포를 위해 Azure에 Windows VM 이미지 업로드](upload-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)


