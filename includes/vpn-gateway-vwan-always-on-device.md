---
title: 포함 파일
description: 포함 파일
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/12/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: feaf72de1d2c578d2b2d0df9e86ec0fbe0b49445
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371760"
---
장치 터널을 성공적으로 설정하려면 다음 요구 사항을 충족해야 합니다.

* 장치는 Windows 10 엔터프라이즈 또는 교육 버전 1809 이상을 실행하는 도메인 에 가입된 컴퓨터여야 합니다.
* 터널은 Windows 기본 제공 VPN 솔루션에 대해서만 구성할 수 있으며 컴퓨터 인증서 인증을 사용하여 IKEv2를 사용하여 설정됩니다.
* 장치당 하나의 장치 터널만 구성할 수 있습니다.

1. 지점 간 VPN 클라이언트 아티클을 사용하여 Windows 10 클라이언트에 클라이언트 [인증서를 설치합니다.](../articles/vpn-gateway/point-to-site-how-to-vpn-client-install-azure-cert.md) 인증서는 로컬 컴퓨터 저장소에 있어야 합니다.
1. VPN 프로필을 만들고 다음 지침을 사용하여 LOCAL SYSTEM 계정의 컨텍스트에서 장치 터널을 [구성합니다.](https://docs.microsoft.com/windows-server/remote/remote-access/vpn/vpn-device-tunnel-config#vpn-device-tunnel-configuration)

### <a name="configuration-example-for-device-tunnel"></a>장치 터널의 구성 예

가상 네트워크 게이트웨이를 구성하고 Windows 10 클라이언트의 로컬 컴퓨터 저장소에 클라이언트 인증서를 설치한 후 다음 예제를 사용하여 클라이언트 장치 터널을 구성합니다.

1. 다음 텍스트를 복사하여 ***devicecert.ps1로***저장합니다.

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
1. 다음 텍스트를 복사하여 **devicecert.ps1과**동일한 폴더에 ***VPNProfile.xml로*** 저장합니다. 환경에 맞게 다음 텍스트를 편집합니다.

   * `<Servers>azuregateway-1234-56-78dc.cloudapp.net</Servers> <= Can be found in the VpnSettings.xml in the downloaded profile zip file`
   * `<Address>192.168.3.5</Address> <= IP of resource in the vnet or the vnet address space`
   * `<Address>192.168.3.4</Address> <= IP of resource in the vnet or the vnet address space`

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
1. [Sysinternals에서](https://docs.microsoft.com/sysinternals/downloads/psexec) **PsExec을** 다운로드하고 **C :\PSTools로**파일을 추출합니다.
1. 관리자 CMD 프롬프트에서 다음을 실행하여 PowerShell을 시작합니다.

   ```
   PsExec.exe Powershell for 32-bit Windows
   PsExec64.exe Powershell for 64-bit Windows
   ```

   ![PowerShell](./media/vpn-gateway-vwan-always-on-device/powershell.png)
1. PowerShell에서 **devicecert.ps1** 및 **VPNProfile.xml이** 있는 폴더로 전환하고 다음 명령을 실행합니다.

   ```powershell
   .\devicecert.ps1 .\VPNProfile.xml MachineCertTest
   ```
   
   ![기계 테스트](./media/vpn-gateway-vwan-always-on-device/machinecerttest.png)
1. **rasphone을 실행합니다.**

   ![라스폰](./media/vpn-gateway-vwan-always-on-device/rasphone.png)
1. **MachineCertTest** 항목을 찾아 **연결**을 클릭합니다.

   ![연결](./media/vpn-gateway-vwan-always-on-device/connect.png)
1. 연결이 성공하면 컴퓨터를 다시 부팅합니다. 터널이 자동으로 연결됩니다.
