---
title: "P2S RADIUS 연결용 VPN 클라이언트 구성 파일 만들기 및 설치: PowerShell: Azure | Microsoft Docs"
description: "이 문서는 RADIUS 인증을 사용하는 지점 및 사이트 간 연결용 VPN 클라이언트 구성 파일을 만드는 데 도움이 됩니다."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: cherylmc
ms.translationtype: HT
ms.sourcegitcommit: 44e9d992de3126bf989e69e39c343de50d592792
ms.openlocfilehash: c56a9b06a11d25cf046a0faeb29af1d78d9a4a89
ms.contentlocale: ko-kr
ms.lasthandoff: 09/25/2017

---
# <a name="create-and-install-vpn-client-configuration-files-for-p2s-radius-authentication-preview"></a>P2S RADIUS 인증용 VPN 클라이언트 구성 파일 만들기 및 설치(미리 보기)

VPN 클라이언트 구성 파일은 zip 파일에 포함되어 있습니다. 구성 파일은 원시 Windows 또는 Mac IKEv2 VPN 클라이언트가 지점 및 사이트 간 연결을 통해 VNet에 연결하는 데 필요한 설정을 제공합니다. RADIUS 서버는 여러 인증 옵션을 제공하며, 따라서 VPN 클라이언트 구성은 각 옵션에 따라 다릅니다.

### <a name="workflow"></a>워크플로

  1. P2S 연결을 위한 Azure VPN Gateway를 설정합니다.
  2. 인증을 위해 RADIUS 서버를 설정합니다. 
  3. 선택한 인증 옵션에 대한 VPN 클라이언트 구성을 가져오고 이 구성을 사용하여 Windows 장치에서 VPN 클라이언트를 설정합니다. 이렇게 하면 P2S 연결을 통해 Azure Vnet에 연결할 수 있습니다.

>[!IMPORTANT]
>VPN 프로토콜 유형 또는 인증 유형 등 VPN 클라이언트 구성 프로필을 생성한 후 지점 및 사이트 간 구성을 변경한 경우, 새 VPN 클라이언트 구성을 생성하고 사용자 장치에 설치해야 합니다.
>
>

## <a name="adeap"></a>사용자 이름/암호 인증

* **AD 인증:** 널리 사용하는 시나리오는 AD 도메인 인증입니다. 이 시나리오에서는 사용자가 자신의 도메인 자격 증명을 사용하여 Azure Vnet에 연결합니다. RADIUS AD 인증용 VPN 클라이언트 구성 파일을 만들 수 있습니다.

* **AD 없이 인증:** AD 없이 사용자 이름/암호 RADIUS 인증 시나리오를 구성할 수도 있습니다.

모든 연결 사용자가 RADIUS를 통해 인증할 수 있는 사용자 이름/암호 자격 증명을 가지고 있어야 합니다. EAP-MSCHAPv2 사용자 이름/암호 인증 프로토콜용 구성만 만들 수 있습니다. '-AuthenticationMethod'는 'EapMSChapv2'로 지정됩니다.

### <a name="usernamefiles"></a>VPN 클라이언트 구성 파일 생성

다음 명령을 사용하여 VPN 클라이언트 구성을 만듭니다.

```powershell 
New-AzureRmVpnClientConfiguration -ResourceGroupName "TestRG" -VirtualNetworkGatewayName "VNet1GW" -AuthenticationMethod "EapMSChapv2"
```
 
명령을 실행한 후 링크를 반환합니다. 링크를 복사하여 웹 브라우저에 붙여넣고 압축 파일 'VpnClientConfiguration.zip'을 다운로드합니다. 파일의 압축을 풀면 다음 폴더가 보입니다. 
 
* 폴더 'WindowsAmd64' 및 'WindowsX86'. 이 폴더에는 각각 Windows 64비트 및 32비트 설치 관리자가 포함되어 있습니다. 
* 폴더 'GenericDevice'. 이 폴더는 자신의 고유한 VPN 클라이언트 구성을 만드는 데 사용하는 일반 정보를 포함합니다. 이 폴더를 무시합니다.
* 가상 네트워크 게이트웨이를 만들 때 IKEv2를 구성한 경우, 'mobileconfig' 파일을 포함하는 ‘Mac’라는 폴더가 보입니다. 이 파일은 Mac 클라이언트를 구성하는 데 사용됩니다.

이미 만든 클라이언트 구성 파일을 검색할 수 있습니다. 'Get-AzureRmVpnClientConfiguration' cmdlet은 VpnClientConfiguration.zip 파일을 다운로드할 수 있는 URL(링크)을 반환합니다. VPN 프로토콜 유형 또는 인증 유형 등 P2S VPN 구성을 변경하면 구성이 자동으로 업데이트되지 않습니다. 'New-AzureRmVpnClientConfiguration' cmdlet을 실행하여 구성을 다시 만들어야 합니다.

이전에 생성된 클라이언트 구성 파일을 검색하려면 다음 명령을 사용합니다.

```powershell
Get-AzureRmVpnClientConfiguration -ResourceGroupName "TestRG" -VirtualNetworkGatewayName "VNet1GW"
```
 
### <a name="adwincli"></a>Windows VPN 클라이언트 설정

버전이 클라이언트의 아키텍처와 일치하는 한, 각 Windows클라이언트 컴퓨터에서 동일한 VPN 클라이언트 구성 패키지를 사용할 수 있습니다. 지원되는 클라이언트 운영 체제의 목록은 [FAQ](vpn-gateway-vpn-faq.md#P2S)의 지점 및 사이트 간 섹션을 참조하세요.

인증서 인증을 위해 원시 Windows VPN 클라이언트를 구성하려면 다음 단계를 사용합니다.

1. Windows 컴퓨터의 아키텍처에 해당하는 VPN 클라이언트 구성 파일을 선택합니다. 64비트 프로세서 아키텍처의 경우 'VpnClientSetupAmd64' 설치 관리자 패키지를 선택합니다. 32비트 프로세서 아키텍처의 경우 'VpnClientSetupX86' 설치 관리자 패키지를 선택합니다. 
2. 해당 패키지를 두 번 클릭하여 설치합니다. SmartScreen 팝업이 표시되면 **자세한 정보**, **실행**을 차례로 클릭합니다.
3. 클라이언트 컴퓨터에서 **네트워크 설정**으로 이동하고 **VPN**을 클릭합니다. VPN 연결에서 연결되는 가상 네트워크의 이름을 표시합니다. 

### <a name="admaccli"></a>Mac(OSX) VPN 클라이언트 설정

1. **VpnClientSetup mobileconfig** 파일을 선택하고 각 사용자에게 보냅니다. 이메일 또는 다른 방법을 사용하여 이렇게 할 수 있습니다.

2. Mac에서 **mobileconfig** 파일을 찾습니다.

  ![mobilconfig 파일 찾기](./media/point-to-site-vpn-client-configuration-radius/admobileconfigfile.png)
3. 프로필을 두 번 클릭하여 설치하고 **계속**을 클릭합니다. 프로필 이름은 VNet의 이름과 같습니다.

  ![설치](./media/point-to-site-vpn-client-configuration-radius/adinstall.png)
4. **계속**을 클릭하여 프로필의 받는 사람을 신뢰하고 설치를 계속합니다.

  ![계속](./media/point-to-site-vpn-client-configuration-radius/adcontinue.png)
5. 프로필을 설치하는 동안 VPN 인증에 사용한 사용자 이름 및 암호를 지정하는 옵션이 제공됩니다. 이 정보를 입력하는 것은 필수가 아닙니다. 지정한 경우, 이 정보는 연결을 시작할 때 저장되고 자동으로 사용됩니다. **설치**를 클릭하여 계속합니다.

  ![설정](./media/point-to-site-vpn-client-configuration-radius/adsettings.png)
6. 컴퓨터에 프로필을 설치하는 데 필요한 권한에 대한 사용자 이름 및 암호를 입력합니다. **확인**을 클릭합니다.

  ![사용자 이름 및 암호](./media/point-to-site-vpn-client-configuration-radius/adusername.png)
7. 설치된 후 프로필은 **프로필** 대화 상자에서 볼 수 있습니다. 나중에 **시스템 기본 설정**에서 이 대화 상자를 열 수도 있습니다.

  ![시스템 기본 설정]](./media/point-to-site-vpn-client-configuration-radius/adsystempref.png)
8. VPN 연결에 액세스하려면 **시스템 기본 설정**에서 **네트워크**를 엽니다.

  ![네트워크](./media/point-to-site-vpn-client-configuration-radius/adnetwork.png)
9. VPN 연결은 **IkeV2-VPN**로 표시됩니다. **mobileconfig** 파일을 업데이트하여 이름을 변경할 수 있습니다.

  ![connection](./media/point-to-site-vpn-client-configuration-radius/adconnection.png)
10. **인증 설정**을 클릭합니다. 드롭다운 목록에서 **사용자 이름**을 선택하고 자격 증명을 입력합니다. 이전에 자격 증명을 입력한 경우 **사용자 이름**은 드롭다운 목록에 자동으로 선택되며 사용자 이름 및 암호가 미리 채워집니다. **확인**을 클릭하여 설정을 저장합니다. 이제 네트워크 대화 상자로 돌아갑니다.

  ![authenticate](./media/point-to-site-vpn-client-configuration-radius/adauthentication.png)
11. **적용**을 클릭하여 변경 내용을 저장합니다. 연결을 시작하려면 **연결**을 클릭합니다.

## <a name="certeap"></a>인증서 인증
 
EAP-TLS 프로토콜을 사용하는 RADIUS 인증서 인증을 위해 VPN 클라이언트 구성 파일을 만들 수 있습니다. 일반적으로 VPN에 대해 사용자를 인증할 때는 Enterprise 발급 인증서를 사용합니다. 모든 연결 사용자가 사용자의 장치에 인증서를 설치했는지, RADIUS 서버에서 인증서의 유효성을 검사할 수 있는지 확인합니다.
 
* '-AuthenticationMethod'는 'EapTls'입니다.
* 인증서를 인증하는 동안 클라이언트가 인증서의 유효성을 검사하여 RADIUS 서버의 유효성을 검사합니다. -RadiusRootCert는 RADIUS 서버의 유효성을 검사하는 데 사용하는 루트 증서를 포함하는 .cer 파일입니다.  
* Windows 장치가 클라이언트 인증서 여러 개를 포함하는 경우도 있습니다. 인증하는 동안 이 때문에 모든 인증서를 나열하는 팝업 대화 상자가 표시될 수 있습니다. 이때 사용자는 사용할 인증서를 선택해야 합니다. 클라이언트 인증서가 연결해야 하는 루트 인증서를 지정하여 정확한 인증서를 필터링할 수 있습니다. '-ClientRootCert'는 루트 인증서를 포함하는 .cer 파일입니다. 이는 선택적 매개 변수입니다. 연결하려는 장치에 클라이언트 인증서가 한 개만 있는 경우 이 매개 변수를 지정하지 않아도 됩니다.

### <a name="certfiles"></a>VPN 클라이언트 구성 파일 생성

다음 명령을 사용하여 VPN 클라이언트 구성을 만듭니다.
 
```powershell
New-AzureRmVpnClientConfiguration -ResourceGroupName "TestRG" -VirtualNetworkGatewayName "VNet1GW" -AuthenticationMethod "EapTls" -RadiusRootCert <full path name of .cer file containing the RADIUS root> -ClientRootCert <full path name of .cer file containing the client root>
```

Cmdlet 결과가 링크를 반환합니다. 링크를 복사하여 웹 브라우저에 붙여넣고 압축 파일 'VpnClientConfiguration.zip'을 다운로드합니다. 파일의 압축을 풀면 다음 폴더가 보입니다.

* 폴더 'WindowsAmd64' 및 'WindowsX86'. 이 폴더에는 각각 Windows 64비트 및 32비트 설치 관리자가 포함되어 있습니다. 
* 폴더 'GenericDevice'. 이 폴더는 자신의 고유한 VPN 클라이언트 구성을 만드는 데 사용하는 일반 정보를 포함합니다.

이미 만든 클라이언트 구성 파일을 검색할 수 있습니다. 'Get-AzureRmVpnClientConfiguration' cmdlet은 VpnClientConfiguration.zip 파일을 다운로드할 수 있는 URL(링크)을 반환합니다. VPN 프로토콜 유형 또는 인증 유형 등 P2S VPN 구성을 변경하면 구성이 자동으로 업데이트되지 않습니다. 'New-AzureRmVpnClientConfiguration' cmdlet을 실행하여 구성을 다시 만들어야 합니다.

이전에 생성된 클라이언트 구성 파일을 검색하려면 다음 명령을 사용합니다.

```powershell
Get-AzureRmVpnClientConfiguration -ResourceGroupName "TestRG" -VirtualNetworkGatewayName "VNet1GW"
```
 
### <a name="certwincli"></a>Windows VPN 클라이언트 설정

1. 구성 패키지를 선택하고 클라이언트 장치에 설치합니다. 64비트 프로세서 아키텍처의 경우, 'VpnClientSetupAmd64' 설치 관리자 패키지를 선택합니다. 32비트 프로세서 아키텍처의 경우 'VpnClientSetupX86' 설치 관리자 패키지를 선택합니다. SmartScreen 팝업이 표시되면 **자세한 정보**, **실행**을 차례로 클릭합니다. 다른 클라이언트 컴퓨터에 설치하기 위해 패키지를 저장할 수도 있습니다.
2. 클라이언트 컴퓨터에서 **네트워크 설정**으로 이동하고 **VPN**을 클릭합니다. VPN 연결에서 연결되는 가상 네트워크의 이름을 표시합니다.

### <a name="certmaccli"></a>MAC VPN 클라이언트 설정

Azure VNet에 연결하는 모든 Mac 장치에 대해 별도의 프로필을 만들어야 합니다. 이러한 장치를 프로필에 지정하려면 인증을 위한 사용자 인증서가 필요하기 때문입니다. **제네릭** 폴더에는 프로필을 만드는 데 필요한 모든 정보가 들어 있습니다.

  * **VpnSettings.xml**은 서버 주소 및 터널 유형 같은 중요한 설정을 포함합니다.
  * **VpnServerRoot.cer**은 P2S 연결 설정 중에 VPN Gateway 유효성을 검사하는 데 필요한 루트 인증서를 포함합니다.
  * **RadiusServerRoot.cer**은 인증하는 동안 RADIUS 서버 유효성을 검사하는 데 필요한 루트 인증서를 포함합니다.

인증서 인증을 위해 Mac에서 원시 VPN 클라이언트를 구성하려면 다음 단계를 사용합니다.

1. **VpnServerRoot** 및 **RadiusServerRoot** 루트 인증서를 Mac으로 가져옵니다. 파일을 Mac에 복사하고 해당 파일을 두 번 클릭하면 됩니다.  
**추가**를 클릭하여 가져옵니다.

  **VpnServerRoot 추가**

  ![인증서 추가](./media/point-to-site-vpn-client-configuration-radius/addcert.png)

  **RadiusServerRoot 추가**

  ![인증서 추가](./media/point-to-site-vpn-client-configuration-radius/radiusrootcert.png)
2. **네트워크 기본 설정** 아래에서 **네트워크** 대화 상자를 열고 **'+'**를 클릭하여 Azure VNet에 대한 P2S 연결용 새 VPN 클라이언트 연결 프로필을 만듭니다.

  **인터페이스** 값은 'VPN'이고, **VPN 유형** 값은 'IKEv2'입니다. **서비스 이름** 필드에 프로필 이름을 지정한 다음 **만들기**를 클릭하여 VPN 클라이언트 연결 프로필을 만듭니다.

  ![네트워크](./media/point-to-site-vpn-client-configuration-radius/network.png)
3. **Generic** 폴더의 **VpnSettings.xml** 파일에서 **VpnServer** 태그 값을 복사합니다. 이 값을 프로필의 **서버 주소** 및 **원격 ID** 필드에 붙여넣습니다. **로컬 ID** 필드는 비워 둡니다.

  ![서버 정보](./media/point-to-site-vpn-client-configuration-radius/servertag.png)
4. **인증 설정**을 클릭하고 **인증서**를 선택합니다. 

  ![인증 설정](./media/point-to-site-vpn-client-configuration-radius/certoption.png)
5. **선택...**을 클릭합니다. 인증에 사용하려는 인증서를 선택합니다.

  ![인증서](./media/point-to-site-vpn-client-configuration-radius/certificate.png)
6. **ID 선택**은 선택할 수 있는 인증서의 목록을 표시합니다. 적절한 인증서를 선택한 다음 **계속**을 클릭합니다.

  ![ID](./media/point-to-site-vpn-client-configuration-radius/identity.png)
7. **로컬 ID** 필드에서 인증서 이름(5 단계에서 사용한 이름)을 지정합니다. 이 예에서는 "ikev2Client.com"입니다. 그런 다음 **적용** 단추를 클릭하여 변경 내용을 저장합니다.

  ![apply](./media/point-to-site-vpn-client-configuration-radius/applyconnect.png)
8. **네트워크** 대화 상자에서 **적용**을 클릭하여 모든 변경 내용을 저장합니다. 그런 다음 **연결**을 클릭하여 Azure VNet에 대한 P2S 연결을 시작합니다.

## <a name="otherauth"></a>다른 인증 유형 또는 포르토콜

다른 인증 유형(예: OTP)를 사용하고 사용자 이름/암호 또는 인증서를 사용하지 않거나 다른 인증 프로토콜을 사용하려면(EAP-MSCHAPv2 대신에 PEAP-MSCHAPv2 등) 자신의 고유 VPN 클라이언트 구성 프로필을 만들어야 합니다. 프로필을 만들려면 가상 네트워크 게이트웨이 IP 주소, 터널 유형 및 분할 터널 경로 등과 같은 정보가 필요합니다. 다음 단계를 사용하여 이 정보를 얻을 수 있습니다.

1. 'Get-AzureRmVpnClientConfiguration' cmdlet을 사용하여 EapMSChapv2에 대한 VPN 클라이언트 구성을 생성합니다. 지침은 문서의 [이 섹션](#ccradius)을 참조하세요.

2. VpnClientConfiguration.zip 파일의 압축을 풀고 GenenericDevice 폴더를 찾습니다. 64비트 및 32비트 아키텍처에 대한 Windows Installer를 포함하는 폴더를 무시합니다.
 
3. GenenericDevice 폴더는 XML 파일 VpnSettings를 포함합니다. 이 파일은 모든 필요한 정보를 포함합니다.

  * VpnServer - Azure VPN Gateway의 FQDN입니다. 이는 클라이언트가 연결하는 대상 주소입니다.
  * VpnType - 연결하는 데 사용하는 터널 유형입니다.
  * Routes - P2S 터널을 통해 Azure VNet 바인딩된 트래픽만 전송되도록 프로필 내에 구성해야 하는 경로입니다.
  * GenenericDevice 폴더에는 'VpnServerRoot'라는 .cer 파일도 있습니다. 이 파일은 P2S 연결 설정 중에 Azure VPN Gateway 유효성을 검사하는 데 필요한 루트 인증서를 포함합니다. Azure VNet에 연결하는 모든 장치에 인증서를 설치합니다. 
 
## <a name="next-steps"></a>다음 단계

P2S 구성을 완료하려면 [P2S 구성 완료](point-to-site-how-to-radius-ps.md) 문서로 돌아갑니다.
