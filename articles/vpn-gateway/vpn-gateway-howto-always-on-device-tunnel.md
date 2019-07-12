---
title: VPN Gateway에 대 한 Always On VPN 터널을 구성
description: VPN Gateway에 대 한 Always On VPN 터널을 구성 하는 단계
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptional
ms.date: 07/09/2019
ms.author: cherylmc
ms.openlocfilehash: 81822297dcf9370fc8ce7f7ce0285689c31606ce
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67695775"
---
# <a name="configure-an-always-on-vpn-device-tunnel"></a>Always On VPN 디바이스 터널 구성

Windows 10 가상 개인 네트워크 (VPN) 클라이언트의 새로운 기능 중 하나에 VPN 연결을 유지 하는 기능입니다. Always On은 자동으로 연결 하 여 트리거를 기반으로 연결 되어 현재 VPN 프로필을 사용 하도록 설정 하는 Windows 10 기능-즉, 사용자 로그인, 네트워크 상태 변경 또는 장치 화면을 활성화 합니다.

Azure 가상 네트워크 게이트웨이에 azure 터널 장치 뿐만 아니라 영구 사용자 터널을 설정 하려면 Windows 10 Always On을 사용 하 여 사용할 수 있습니다. 이 문서에서는 Always ON VPN 장치 터널을 구성 하는 데 도움이 됩니다.

Always On VPN 연결에는 터널의 두 가지 유형이 포함 됩니다.

* **장치 터널** 사용자가 장치에 로그인 하기 전에 지정 된 VPN 서버에 연결 합니다. 사전 로그인 연결 시나리오 및 장치 관리 목적으로 장치 터널을 사용 합니다.

* **사용자 터널** 장치에서 사용자가 로그인 한 후에 연결 합니다. 사용자 터널을 사용 하면 VPN 서버를 통해 조직 리소스에 액세스할 수 있습니다.

터널 장치 및 사용자 터널 모두 독립적으로 작동할 해당 VPN 프로필을 사용 하 여 합니다. 이와 동시에 연결 될 수 하며 다양 한 인증 방법 및 다른 VPN 구성 설정을 적절 하 게 사용할 수 있습니다.

## <a name="1-configure-the-gateway"></a>1. 게이트웨이 구성

IKEv2 및이 사용 하 여 인증서 기반 인증을 사용 하도록 VPN gateway 구성 [지점 및 사이트 문서](vpn-gateway-howto-point-to-site-resource-manager-portal.md)합니다.

## <a name="2-configure-the-user-tunnel"></a>2. 사용자 터널 구성

1. 이 표시 된 것과 같이 Windows 10 클라이언트에 클라이언트 인증서를 설치할 [지점-사이트 간 VPN 클라이언트 문서](point-to-site-how-to-vpn-client-install-azure-cert.md)합니다. 현재 사용자 저장소에 있어야 인증서
2. PowerShell, SCCM 또는 Intune을 사용 하 여 Always On VPN 클라이언트 구성 [이러한 지침](https://docs.microsoft.com/windows-server/remote/remote-access/vpn/always-on-vpn/deploy/vpn-deploy-client-vpn-connections)합니다.

## <a name="3-configure-the-device-tunnel"></a>3. 장치 터널 구성

장치 터널을 설정 하려면 다음 요구 사항은 충족 되어야 합니다.

* 장치가는 도메인에 가입된 된 컴퓨터 Windows 10 Enterprise 또는 교육용 버전 1709 이상을 실행 해야 합니다.
* 터널만 기본 제공 Windows VPN 솔루션에 대 한 구성 가능 하며 IKEv2를 사용 하 여 컴퓨터 인증서 인증을 사용 하 여 설정 됩니다. 
* 장치 당 하나의 장치만 터널을 구성할 수 있습니다.

1. 이 표시 된 것과 같이 Windows 10 클라이언트에 클라이언트 인증서를 설치할 [지점-사이트 간 VPN 클라이언트 문서](point-to-site-how-to-vpn-client-install-azure-cert.md)합니다. 인증서를 로컬 컴퓨터 저장소에 있어야 합니다.
1. 사용 하 여 [이러한 지침](https://docs.microsoft.com/windows-server/remote/remote-access/vpn/vpn-device-tunnel-config#vpn-device-tunnel-configuration) VPN 프로필 만들기 및 로컬 시스템 계정의 컨텍스트에서 터널 장치를 구성 합니다.

### <a name="configuration-example-for-device-tunnel"></a>장치 터널에 대 한 구성 예제

가상 네트워크 게이트웨이 구성 하 고 Windows 10 클라이언트에서 로컬 컴퓨터 저장소에 클라이언트 인증서를 설치, 후 클라이언트 장치 터널을 구성 하려면 다음 예제를 사용 합니다.

1. 다음 텍스트를 복사 및 저장 ***devicecert.ps1***합니다.

   ```
   Param(
   [string]$xmlFilePath,
   [string]$ProfileName
   )

   $a = Test-Path $xmlFilePath
   echo $a

   $ProfileXML = Get-Content $xmlFilePath

   echo $XML

   $ProfileNameEscaped = $ProfileName -replace ' ', '%20'

   $Version = 201606090004

   $ProfileXML = $ProfileXML -replace '<', '&lt;'
   $ProfileXML = $ProfileXML -replace '>', '&gt;'
   $ProfileXML = $ProfileXML -replace '"', '&quot;'

   $nodeCSPURI = './Vendor/MSFT/VPNv2'
   $namespaceName = "root\cimv2\mdm\dmmap"
   $className = "MDM_VPNv2_01"

   $session = New-CimSession

   try
   {
   $newInstance = New-Object Microsoft.Management.Infrastructure.CimInstance $className, $namespaceName
   $property = [Microsoft.Management.Infrastructure.CimProperty]::Create("ParentID", "$nodeCSPURI", 'String', 'Key')
   $newInstance.CimInstanceProperties.Add($property)
   $property = [Microsoft.Management.Infrastructure.CimProperty]::Create("InstanceID", "$ProfileNameEscaped", 'String', 'Key')
   $newInstance.CimInstanceProperties.Add($property)
   $property = [Microsoft.Management.Infrastructure.CimProperty]::Create("ProfileXML", "$ProfileXML", 'String', 'Property')
   $newInstance.CimInstanceProperties.Add($property)

   $session.CreateInstance($namespaceName, $newInstance)
   $Message = "Created $ProfileName profile."
   Write-Host "$Message"
   }
   catch [Exception]
   {
   $Message = "Unable to create $ProfileName profile: $_"
   Write-Host "$Message"
   exit
   }
   $Message = "Complete."
   Write-Host "$Message"
   ```
1. 다음 텍스트를 복사 및 저장 ***VPNProfile.xml*** 와 같은 폴더에 **devicecert.ps1**합니다. 환경에 맞게 다음 텍스트를 편집 합니다.

   * `<Servers>azuregateway-1234-56-78dc.cloudapp.net</Servers>`
   * `<Address>192.168.3.5</Address>`
   * `<Address>192.168.3.4</Address>`

   ```
   <VPNProfile>  
     <NativeProfile>  
   <Servers>azuregateway-1234-56-78dc.cloudapp.net</Servers>  
   <NativeProtocolType>IKEv2</NativeProtocolType>  
   <Authentication>  
     <MachineMethod>Certificate</MachineMethod>  
   </Authentication>  
   <RoutingPolicyType>SplitTunnel</RoutingPolicyType>  
    <!-- disable the addition of a class based route for the assigned IP address on the VPN interface -->
   <DisableClassBasedDefaultRoute>true</DisableClassBasedDefaultRoute>  
     </NativeProfile> 
     <!-- use host routes(/32) to prevent routing conflicts -->  
     <Route>  
   <Address>192.168.3.5</Address>  
   <PrefixSize>32</PrefixSize>  
     </Route>  
     <Route>  
   <Address>192.168.3.4</Address>  
   <PrefixSize>32</PrefixSize>  
     </Route>  
   <!-- need to specify always on = true --> 
     <AlwaysOn>true</AlwaysOn> 
   <!-- new node to specify that this is a device tunnel -->  
    <DeviceTunnel>true</DeviceTunnel>
   <!--new node to register client IP address in DNS to enable manage out -->
   <RegisterDNS>true</RegisterDNS>
   </VPNProfile>
   ```
1. 다운로드 **PsExec** 에서 [Sysinternals](https://docs.microsoft.com/sysinternals/downloads/psexec) 파일을 추출 하 고 **C:\PSTools**합니다.
1. 관리자 CMD 프롬프트에서 실행 하 여 PowerShell을 시작 합니다.

   ```
   C:\PsTools\PsExec.exe Powershell for 32-bit Windows
   C:\PsTools\PsExec64.exe Powershell for 64-bit Windows
   ```

   ![Powershell](./media/vpn-gateway-howto-always-on-device-tunnel/powershell.png)
1. PowerShell에서 폴더로 전환 하 고 여기서 **devicecert.ps1** 하 고 **VPNProfile.xml** 하는 위치 및 다음 명령을 실행:

   ```powershell
   C:\> .\devicecert.ps1 .\VPNProfile.xml MachineCertTest
   ```
   
   ![MachineCertTest](./media/vpn-gateway-howto-always-on-device-tunnel/machinecerttest.png)
1. 실행할 **rasphone**합니다.

   ![rasphone](./media/vpn-gateway-howto-always-on-device-tunnel/rasphone.png)
1. 검색할 합니다 **MachineCertTest** 항목을 클릭 **Connect**합니다.

   ![연결](./media/vpn-gateway-howto-always-on-device-tunnel/connect.png)
1. 연결에 성공 하는 경우 컴퓨터를 다시 부팅 합니다. 터널은 자동으로 연결 됩니다.

## <a name="cleanup"></a>정리

프로필을 제거 하려면 다음 명령을 실행 합니다.

![정리](./media/vpn-gateway-howto-always-on-device-tunnel/cleanup.png)

## <a name="next-steps"></a>다음 단계

문제를 해결 하려면 참조 [Azure 지점-사이트 간 연결 문제](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)
