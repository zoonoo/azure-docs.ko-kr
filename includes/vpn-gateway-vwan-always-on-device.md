---
title: 파일 포함
description: 포함 파일
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/12/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: feaf72de1d2c578d2b2d0df9e86ec0fbe0b49445
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "79371760"
---
장치 터널을 성공적으로 설정 하려면 다음 요구 사항을 충족 해야 합니다.

* 장치는 Windows 10 Enterprise 또는 교육용 버전 1809 이상을 실행 하는 도메인에 가입 된 컴퓨터 여야 합니다.
* 터널은 Windows 기본 제공 VPN 솔루션에 대해서만 구성할 수 있으며 컴퓨터 인증서 인증과 함께 IKEv2를 사용 하 여 설정 됩니다.
* 장치 마다 장치 터널을 하나만 구성할 수 있습니다.

1. [지점 및 사이트 간 VPN 클라이언트](../articles/vpn-gateway/point-to-site-how-to-vpn-client-install-azure-cert.md) 문서를 사용 하 여 Windows 10 클라이언트에 클라이언트 인증서를 설치 합니다. 인증서는 로컬 컴퓨터 저장소에 있어야 합니다.
1. VPN 프로필을 만들고 [이러한 지침](https://docs.microsoft.com/windows-server/remote/remote-access/vpn/vpn-device-tunnel-config#vpn-device-tunnel-configuration)을 사용 하 여 로컬 시스템 계정의 컨텍스트에서 장치 터널을 구성 합니다.

### <a name="configuration-example-for-device-tunnel"></a>장치 터널에 대 한 구성 예제

가상 네트워크 게이트웨이를 구성 하 고 Windows 10 클라이언트의 로컬 컴퓨터 저장소에 클라이언트 인증서를 설치한 후 클라이언트 장치 터널을 구성 하려면 다음 예제를 사용 합니다.

1. 다음 텍스트를 복사 하 고 ***devicecert***로 저장 합니다.

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
1. 다음 텍스트를 복사 하 여 **devicecert**와 동일한 폴더에 ***VPNProfile*** 로 저장 합니다. 다음 텍스트를 사용자 환경에 맞게 편집 합니다.

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
1. [Sysinternals](https://docs.microsoft.com/sysinternals/downloads/psexec) 에서 **PsExec** 를 다운로드 하 여 **C:\PSTools**에 파일을 추출 합니다.
1. 관리자 CMD 프롬프트에서 다음을 실행 하 여 PowerShell을 시작 합니다.

   ```
   PsExec.exe Powershell for 32-bit Windows
   PsExec64.exe Powershell for 64-bit Windows
   ```

   ![PowerShell](./media/vpn-gateway-vwan-always-on-device/powershell.png)
1. PowerShell에서 **devicecert** 및 **VPNProfile** 가 있는 폴더로 전환 하 고 다음 명령을 실행 합니다.

   ```powershell
   .\devicecert.ps1 .\VPNProfile.xml MachineCertTest
   ```
   
   ![MachineCertTest](./media/vpn-gateway-vwan-always-on-device/machinecerttest.png)
1. **Rasphone**를 실행 합니다.

   ![rasphone](./media/vpn-gateway-vwan-always-on-device/rasphone.png)
1. **MachineCertTest** 항목을 찾아 **연결**을 클릭 합니다.

   ![연결](./media/vpn-gateway-vwan-always-on-device/connect.png)
1. 연결에 성공 하면 컴퓨터를 다시 부팅 합니다. 터널이 자동으로 연결 됩니다.
