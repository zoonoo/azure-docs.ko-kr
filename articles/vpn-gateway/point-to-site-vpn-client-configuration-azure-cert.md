---
title: "Azure 인증서 인증용 P2S VPN 클라이언트 구성 파일 만들기 및 설치: PowerShell: Azure | Microsoft Docs"
description: "이 문서에서는 인증서 인증을 사용하는 지점 및 사이트 간 연결을 위한 VPN 클라이언트 구성 파일을 만들고 설치하는 방법을 설명합니다."
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
ms.date: 09/27/2017
ms.author: cherylmc
ms.openlocfilehash: a8129678b5ee2b0b1f2a59049fc6632b6cbf3383
ms.sourcegitcommit: 9c3150e91cc3075141dc2955a01f47040d76048a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/26/2017
---
# <a name="create-and-install-vpn-client-configuration-files-for-native-azure-certificate-authentication-p2s-configurations"></a>Azure 기본 인증서 인증 P2S 구성용 VPN 클라이언트 구성 파일 만들기 및 설치

VPN 클라이언트 구성 파일은 zip 파일에 포함되어 있습니다. 구성 파일은 Windows 또는 Mac IKEv2 VPN 기본 클라이언트에서 Azure 기본 인증서 인증을 사용하는 지점 및 사이트 간 연결을 통해 VNet에 연결하는 데 필요한 설정을 제공합니다.

>[!NOTE]
>P2S의 IKEv2는 현재 미리 보기로 제공되고 있습니다.
>

### <a name="workflow"></a>P2S 워크플로

  1. P2S 연결을 위한 Azure VPN 게이트웨이를 설정합니다.
  2. 루트 인증서 및 클라이언트 인증서를 생성합니다. 루트 인증서 공개 키를 Azure에 업로드하고, 클라이언트 장치에 클라이언트 인증서를 설치합니다. 인증서는 연결하는 사용자를 인증하는 데 사용됩니다.
  3. 선택한 인증 옵션에 대한 VPN 클라이언트 구성을 가져 오고 Windows 및 Mac 장치에서 VPN 클라이언트를 설정합니다. 이렇게 하면 지점 및 사이트 간 연결을 통해 Azure VNet에 연결할 수 있습니다.

>[!NOTE]
>클라이언트 구성 파일은 VNet에 대한 VPN 구성에 적용됩니다. VPN 프로토콜 유형 또는 인증 유형과 같은 VPN 클라이언트 구성 파일을 생성한 후에 지점 및 사이트 간 VPN 구성을 변경하면 사용자 장치에 대한 새 VPN 클라이언트 구성 파일을 생성해야 합니다.
>
>

## <a name="generate"></a>VPN 클라이언트 구성 파일 생성

시작하기 전에 연결하는 모든 사용자에 대해 유효한 인증서가 해당 사용자의 장치에 설치되어 있는지 확인합니다. 클라이언트 인증서 설치에 대한 자세한 내용은 [클라이언트 인증서 설치](point-to-site-how-to-vpn-client-install-azure-cert.md)를 참조하세요.

PowerShell을 사용하거나 Azure Portal을 사용하여 클라이언트 구성 파일을 생성할 수 있습니다. 두 메서드 모두 동일한 zip 파일을 반환합니다. 파일의 압축을 풀어 다음 폴더를 확인합니다.

  * **WindowsAmd64** 및 **WindowsX86** - 각각 Windows 64비트 및 32비트 설치 관리자 패키지가 포함되어 있습니다. **WindowsAmd64** 설치 관리자 패키지는 Amd 외에도 지원되는 모든 64비트 Windows 클라이언트에서 사용됩니다.
  * **Generic** - 사용자 고유의 VPN 클라이언트 구성을 만드는 데 사용되는 일반 정보가 포함되어 있습니다. 이 폴더는 무시합니다. Generic 폴더는 게이트웨이에 IKEv2 또는 SSTP + IKEv2를 구성한 경우에 제공됩니다. SSTP만 구성되면 Generic 폴더가 없습니다.

### <a name="zipportal"></a>Azure Portal을 사용하여 파일 생성

1. Azure Portal에서 연결하려는 가상 네트워크의 가상 네트워크 게이트웨이로 이동합니다.
2. 가상 네트워크 게이트웨이 페이지에서 **지점 및 사이트 간 구성**을 클릭합니다.
3. 지점 및 사이트 간 구성 페이지의 맨 위에서 **VPN 클라이언트 다운로드**를 클릭합니다. 클라이언트 구성 패키지를 생성하는 데 몇 분이 소요됩니다.
4. 클라이언트 구성 zip 파일을 사용할 수 있으면 브라우저에 표시됩니다. 게이트웨이와 동일한 이름이 지정됩니다. 파일의 압축을 풀어 폴더를 확인합니다.

### <a name="zipps"></a>PowerShell을 사용하여 파일 생성

1. VPN 클라이언트 구성 파일을 생성하는 경우 '-AuthenticationMethod' 값은 'EapTls'입니다. 다음 명령을 사용하여 VPN 클라이언트 구성 파일을 생성합니다.

  ```powershell
  $profile=New-AzureRmVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapTls"

  $profile.VPNProfileSASUrl
  ```
2. 브라우저에 URL을 복사하여 zip 파일을 다운로드한 다음 파일의 압축을 풀어 폴더를 봅니다.

## <a name="installwin"></a>Windows VPN 클라이언트 구성 패키지 설치

클라이언트의 아키텍처와 일치하는 버전이면 각 Windows 클라이언트 컴퓨터에서 동일한 VPN 클라이언트 구성 패키지를 사용할 수 있습니다. 지원되는 클라이언트 운영 체제의 목록은 [VPN Gateway FAQ](vpn-gateway-vpn-faq.md#P2S)의 지점 및 사이트 간 연결 섹션을 참조하세요.

다음 단계에 따라 인증서 인증을 위한 Windows VPN 기본 클라이언트를 구성합니다.

1. Windows 컴퓨터의 아키텍처에 해당하는 VPN 클라이언트 구성 파일을 선택합니다. 64비트 프로세서 아키텍처의 경우 'VpnClientSetupAmd64' 설치 관리자 패키지를 선택합니다. 32비트 프로세서 아키텍처의 경우 'VpnClientSetupX86' 설치 관리자 패키지를 선택합니다. 
2. 해당 패키지를 두 번 클릭하여 설치합니다. SmartScreen 팝업이 표시되면 **자세한 정보**, **실행**을 차례로 클릭합니다.
3. 클라이언트 컴퓨터에서 **네트워크 설정**으로 이동하고 **VPN**을 클릭합니다. VPN 연결에서 연결되는 가상 네트워크의 이름을 표시합니다. 

## <a name="installmac"></a>Mac에서 VPN 클라이언트 구성(OSX)

Azure는 원시 Azure 인증서 인증을 위해 mobileconfig 파일을 제공하지 않습니다. Azure에 연결할 모든 Mac에서 원시 IKEv2 VPN 클라이언트를 수동으로 구성해야 합니다. **Generic** 폴더에는 이것을 구성하는 데 필요한 모든 정보가 들어 있습니다. 다운로드에 Generic 폴더가 보이지 않으면 IKEv2가 터널 유형으로 선택되지 않았을 가능성이 큽니다. IKEv2가 선택되면 zip 파일을 다시 생성하여 Generic 폴더를 검색합니다. Generic 폴더에 포함되는 파일은 다음과 같습니다.

* **VpnSettings.xml** - 서버 주소 및 터널 종류와 같은 중요한 설정이 포함되어 있습니다. 
* **VpnServerRoot.cer** - P2S 연결 설정에서 Azure VPN Gateway의 유효성을 검사하는 데 필요한 루트 인증서가 포함되어 있습니다.

다음 단계에 따라 인증서 인증을 위한 Mac VPN 기본 클라이언트를 구성합니다. Azure에 연결할 모든 Mac에서 다음 단계를 완료해야 합니다.

1. **VpnServerRoot** 루트 인증서를 Mac으로 가져옵니다. 이렇게 하려면 해당 파일을 Mac으로 복사하고 두 번 클릭하면 됩니다.  
**추가**를 클릭하여 가져옵니다.

  ![인증서 추가](./media/point-to-site-vpn-client-configuration-azure-cert/addcert.png)
2. **네트워크 기본 설정** 아래에서 **네트워크** 대화 상자를 열고 **'+'**를 클릭하여 Azure VNet에 대한 P2S 연결용 새 VPN 클라이언트 연결 프로필을 만듭니다.

  **인터페이스** 값은 'VPN'이고, **VPN 유형** 값은 'IKEv2'입니다. **서비스 이름** 필드에 프로필 이름을 지정한 다음 **만들기**를 클릭하여 VPN 클라이언트 연결 프로필을 만듭니다.

  ![네트워크](./media/point-to-site-vpn-client-configuration-azure-cert/network.png)
3. **Generic** 폴더의 **VpnSettings.xml** 파일에서 **VpnServer** 태그 값을 복사합니다. 이 값을 프로필의 **서버 주소** 및 **원격 ID** 필드에 붙여넣습니다.

  ![서버 정보](./media/point-to-site-vpn-client-configuration-azure-cert/server.png)
4. **인증 설정**을 클릭하고 **인증서**를 선택합니다. 

  ![인증 설정](./media/point-to-site-vpn-client-configuration-azure-cert/authsettings.png)
5. **선택...**을 클릭합니다. 인증에 사용하려는 클라이언트 인증서를 선택합니다. 컴퓨터에 클라이언트 인증서가 이미 설치되어 있어야 합니다(위의 **P2S 워크플로** 섹션 2단계 참조).

  ![인증서](./media/point-to-site-vpn-client-configuration-azure-cert/certificate.png)
6. **ID 선택**은 선택할 수 있는 인증서의 목록을 표시합니다. 적절한 인증서를 선택한 다음 **계속**을 클릭합니다.

  ![ID](./media/point-to-site-vpn-client-configuration-azure-cert/identity.png)
7. **로컬 ID** 필드에서 인증서 이름(6단계에서 사용한 이름)을 지정합니다. 이 예에서는 "ikev2Client.com"입니다. 그런 다음 **적용** 단추를 클릭하여 변경 내용을 저장합니다.

  ![apply](./media/point-to-site-vpn-client-configuration-azure-cert/applyconnect.png)
8. **네트워크** 대화 상자에서 **적용**을 클릭하여 모든 변경 내용을 저장합니다. 그런 다음 **연결**을 클릭하여 Azure VNet에 대한 P2S 연결을 시작합니다.

## <a name="next-steps"></a>다음 단계

P2S 구성을 완료하려면 [P2S 구성 완료](vpn-gateway-howto-point-to-site-rm-ps.md) 문서로 돌아갑니다.
