---
title: Always On VPN 사용자 터널 구성
titleSuffix: Azure VPN Gateway
description: 이 문서에서는 VPN gateway에 대 한 Always On VPN 사용자 터널을 구성 하는 방법을 설명 합니다.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/02/2019
ms.author: cherylmc
ms.openlocfilehash: a22ba5d8b33dd41fcc76c65fcddaf60c1c0ed5e3
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/22/2020
ms.locfileid: "76514751"
---
# <a name="configure-an-always-on-vpn-user-tunnel"></a>Always On VPN 사용자 터널 구성

Windows 10 VPN 클라이언트 Always On의 새로운 기능은 VPN 연결을 유지 관리할 수 있는 기능입니다. Always On 사용 하면 활성 VPN 프로필이 자동으로 연결 될 수 있으며 사용자 로그인, 네트워크 상태 변경, 장치 화면 활성 등의 트리거에 따라 연결 된 상태를 유지할 수 있습니다.

Windows 10 Always On에서 Azure virtual network 게이트웨이를 사용 하 여 Azure에 대 한 영구 사용자 터널 및 장치 터널을 설정할 수 있습니다. 이 문서에서는 Always On VPN 사용자 터널을 구성 하는 방법을 설명 합니다.

Always On VPN 연결에는 다음 두 가지 유형의 터널 중 하나가 포함 됩니다.

* **장치 터널**: 사용자가 장치에 로그인 하기 전에 지정 된 VPN 서버에 연결 합니다. 사전 로그인 연결 시나리오 및 장치 관리는 장치 터널을 사용 합니다.

* **사용자 터널**: 사용자가 장치에 로그인 한 후에만 연결 합니다. 사용자 터널을 사용 하 여 VPN 서버를 통해 조직 리소스에 액세스할 수 있습니다.

장치 터널 및 사용자 터널은 VPN 프로필에 독립적으로 작동 합니다. 동시에 연결할 수 있으며, 다른 인증 방법 및 다른 VPN 구성 설정을 적절 하 게 사용할 수 있습니다.

다음 섹션에서는 VPN gateway와 사용자 터널을 구성 합니다.

## <a name="step-1-configure-a-vpn-gateway"></a>1 단계: VPN gateway 구성

이 [지점 및 사이트](vpn-gateway-howto-point-to-site-resource-manager-portal.md) 간 문서의 지침에 따라 IKEv2 및 인증서 기반 인증을 사용 하도록 VPN gateway를 구성 합니다.

## <a name="step-2-configure-a-user-tunnel"></a>2 단계: 사용자 터널 구성

1. 이 [지점 및 사이트 간 VPN 클라이언트](point-to-site-how-to-vpn-client-install-azure-cert.md) 문서에 표시 된 대로 Windows 10 클라이언트에 클라이언트 인증서를 설치 합니다. 인증서가 현재 사용자 저장소에 있어야 합니다.

1. [Windows 10 클라이언트 ALWAYS ON vpn 연결 구성](https://docs.microsoft.com/windows-server/remote/remote-access/vpn/always-on-vpn/deploy/vpn-deploy-client-vpn-connections)의 지침에 따라 PowerShell, Configuration Manager 또는 Intune을 통해 Always On vpn 클라이언트를 구성 합니다.

### <a name="example-configuration-for-the-user-tunnel"></a>사용자 터널에 대 한 구성 예

가상 네트워크 게이트웨이를 구성 하 고 Windows 10 클라이언트의 로컬 컴퓨터 저장소에 클라이언트 인증서를 설치한 후 다음 예제를 사용 하 여 클라이언트 장치 터널을 구성 합니다.

1. 다음 텍스트를 복사 하 고 usercert로 저장 *합니다*.

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
1. 다음 텍스트를 복사 하 고 *usercert*와 동일한 폴더에 *VPNProfile* 로 저장 합니다. 사용자 환경에 맞게 다음 텍스트를 편집 합니다.

   * `<Servers>azuregateway-1234-56-78dc.cloudapp.net</Servers>  <= Can be found in the VpnSettings.xml in the downloaded profile zip file`
   * `<Address>192.168.3.5</Address>  <= IP of resource in the vnet or the vnet address space`
   * `<Address>192.168.3.4</Address>  <= IP of resource in the vnet or the vnet address space`
   * `<PrefixSize>32</PrefixSize>     <= Subnet mask`

   ```
    <VPNProfile>  
      <NativeProfile>  
    <Servers>azuregateway-b115055e-0882-49bc-a9b9-7de45cba12c0-8e6946892333.vpn.azure.com</Servers>  
    <NativeProtocolType>IKEv2</NativeProtocolType>  
    <Authentication>  
    <UserMethod>Eap</UserMethod>
    <Eap>
    <Configuration>
    <EapHostConfig xmlns="http://www.microsoft.com/provisioning/EapHostConfig"><EapMethod><Type xmlns="http://www.microsoft.com/provisioning/EapCommon">13</Type><VendorId xmlns="http://www.microsoft.com/provisioning/EapCommon">0</VendorId><VendorType xmlns="http://www.microsoft.com/provisioning/EapCommon">0</VendorType><AuthorId xmlns="http://www.microsoft.com/provisioning/EapCommon">0</AuthorId></EapMethod><Config xmlns="http://www.microsoft.com/provisioning/EapHostConfig"><Eap xmlns="http://www.microsoft.com/provisioning/BaseEapConnectionPropertiesV1"><Type>13</Type><EapType xmlns="http://www.microsoft.com/provisioning/EapTlsConnectionPropertiesV1"><CredentialsSource><CertificateStore><SimpleCertSelection>true</SimpleCertSelection></CertificateStore></CredentialsSource><ServerValidation><DisableUserPromptForServerValidation>false</DisableUserPromptForServerValidation><ServerNames></ServerNames></ServerValidation><DifferentUsername>false</DifferentUsername><PerformServerValidation xmlns="http://www.microsoft.com/provisioning/EapTlsConnectionPropertiesV2">false</PerformServerValidation><AcceptServerName xmlns="http://www.microsoft.com/provisioning/EapTlsConnectionPropertiesV2">false</AcceptServerName></EapType></Eap></Config></EapHostConfig>
    </Configuration>
    </Eap>
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
    <!-- traffic filters for the routes specified above so that only this traffic can go over the device tunnel --> 
      <TrafficFilter>  
    <RemoteAddressRanges>192.168.3.4, 192.168.3.5</RemoteAddressRanges>  
      </TrafficFilter>
    <!-- need to specify always on = true --> 
    <AlwaysOn>true</AlwaysOn>
    <RememberCredentials>true</RememberCredentials>
    <!--new node to register client IP address in DNS to enable manage out -->
    <RegisterDNS>true</RegisterDNS>
    </VPNProfile>
   ```
1. PowerShell을 관리자 권한으로 실행합니다.

1. PowerShell에서 *usercert* 및 *VPNProfile* 가 있는 폴더로 전환 하 고 다음 명령을 실행 합니다.

   ```powershell
   C:\> .\usercert.ps1 .\VPNProfile.xml UserTest
   ```
   
   ![MachineCertTest](./media/vpn-gateway-howto-always-on-user-tunnel/p2s2.jpg)
1. **VPN 설정**에서 **usertest** 항목을 찾은 다음 **연결**을 선택 합니다.

1. 연결에 성공 하면 Always On 사용자 터널이 성공적으로 구성 된 것입니다.

## <a name="clean-up-your-resources"></a>리소스 정리

프로필을 제거 하려면 다음을 수행 합니다.

1. 다음 명령 실행:

   ```powershell
   C:\> Remove-VpnConnection UserTest  
   ```

1. 연결을 끊고 **자동으로 연결** 확인란의 선택을 취소 합니다.

![정리](./media/vpn-gateway-howto-always-on-user-tunnel/p2s4..jpg)

## <a name="next-steps"></a>다음 단계

발생할 수 있는 연결 문제를 해결 하려면 [Azure 지점 및 사이트 간 연결 문제](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)를 참조 하세요.
