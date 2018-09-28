---
title: Azure VPN Gateway에 대해 OpenVPN 클라이언트를 구성하는 방법 | Microsoft Docs
description: Azure VPN Gateway에 대해 OpenVPN 클라이언트를 구성하는 단계
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 09/21/2018
ms.author: cherylmc
ms.openlocfilehash: 141f29ea1c7ebdd8208dda6bc39a60f7873dab7d
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46977845"
---
# <a name="configure-openvpn-clients-for-azure-vpn-gateway-preview"></a>Azure VPN Gateway(미리 보기)에 대해 OpenVPN 클라이언트 구성

이 문서는 OpenVPN 클라이언트를 구성하는 데 도움이 됩니다.

> [!IMPORTANT]
> 이 공개 미리 보기는 Service Level Agreement(서비스 수준 약정)없이 제공되므로 프로덕션 워크로드에 사용하지 말아야 합니다. 특정 기능은 지원되지 않을 수 있거나, 기능이 제한될 수 있거나 모든 Azure 위치에서 사용하지는 못할 수 있습니다. 자세한 내용은 [Microsoft Azure 미리 보기에 대한 보충 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.
>

## <a name="before-you-begin"></a>시작하기 전에

VPN 게이트웨이에 대해 OpenVPN을 구성하는 단계를 완료했는지 확인합니다. 자세한 내용은 [Azure VPN Gateway에 대해 OpenVPN 구성](vpn-gateway-howto-openvpn.md)을 참조하세요.

## <a name="windows"></a>Windows 클라이언트

1. 공식 [OpenVPN 웹 사이트](https://openvpn.net/index.php/open-source/downloads.html)에서 OpenVPN 클라이언트를 다운로드하여 설치합니다.
2. 게이트웨이에 대한 VPN 프로필을 다운로드합니다. Azure Portal의 지점-사이트 간 구성 탭 또는 PowerShell의 ‘New-AzureRmVpnClientConfiguration’에서 이 작업을 수행할 수 있습니다.
3. 프로필의 압축을 풉니다. 그런 다음, 메모장에서 OpenVPN 폴더의 vpnconfig.ovpn 구성 파일을 엽니다.
4. base64에서 P2S 클라이언트 인증서 공개 키를 사용하여 P2S 클라이언트 인증서 섹션을 채웁니다. PEM 형식의 인증서에서 .cer 파일을 열고 인증서 헤더 사이에 base64 키를 복사할 수 있습니다. 인증서를 내보내 인코드된 공개 키를 가져오는 방법은 여기를 참조하세요.
5. base64에서 P2S 클라이언트 인증서 개인 키를 사용하여 개인 키 섹션을 채웁니다. 개인 키를 추출하는 방법에 대한 자세한 내용은 [키 내보내기](vpn-gateway-certificates-point-to-site.md#clientexport)를 참조하세요.
6. 다른 필드는 변경하지 마세요. 클라이언트 입력에 채워진 구성을 사용하여 VPN에 연결합니다.
7. vpnconfig.ovpn 파일을 C:\Program Files\OpenVPN\config 폴더에 복사합니다.
8. 시스템 트레이에서 OpenVPN 아이콘을 마우스 오른쪽 단추로 클릭하고 연결을 클릭합니다.

## <a name="mac"></a>Mac 클라이언트

1. [TunnelBlik](https://tunnelblick.net/downloads.html)와 같은 OpenVPN 클라이언트를 다운로드하여 설치합니다. 
2. 게이트웨이에 대한 VPN 프로필을 다운로드합니다. Azure Portal의 지점-사이트 간 구성 탭 또는 PowerShell의 ‘New-AzureRmVpnClientConfiguration’을 사용하여 이 작업을 수행할 수 있습니다.
3. 프로필의 압축을 풉니다. 메모장에서 OpenVPN 폴더의 vpnconfig.ovpn 구성 파일을 엽니다.
4. base64에서 P2S 클라이언트 인증서 공개 키를 사용하여 P2S 클라이언트 인증서 섹션을 채웁니다. PEM 형식의 인증서에서 .cer 파일을 열고 인증서 헤더 사이에 base64 키를 복사할 수 있습니다. 인증서를 내보내 인코드된 공용 키를 가져오는 방법에 대한 자세한 내용은 [공용 키 내보내기](vpn-gateway-certificates-point-to-site.md#cer)를 참조하세요.
5. base64에서 P2S 클라이언트 인증서 개인 키를 사용하여 개인 키 섹션을 채웁니다. 개인 키를 추출하는 방법에 대한 자세한 내용은 [개인 키 내보내기](https://www.geotrust.eu/en/support/manuals/microsoft/all+windows+servers/export+private+key+or+certificate/)를 참조하세요.
6. 다른 필드는 변경하지 마세요. 클라이언트 입력에 채워진 구성을 사용하여 VPN에 연결합니다.
7. 프로필 파일을 두 번 클릭하여 tunnelblik에서 프로필을 만듭니다.
8. 응용 프로그램 폴더에서 Tunnelblik를 시작합니다.
9. 시스템 트레이에서 Tunneblik 아이콘을 클릭하고 연결을 선택합니다.

## <a name="linux"></a>Linux 클라이언트

1. 새 터미널 세션을 엽니다. ‘Ctrl+Alt+t’를 동시에 누르면 새 세션을 열 수 있습니다.
2. 다음 명령을 입력하여 필요한 구성 요소를 설치합니다.

  ```
  sudo apt-get install openvpn
  sudo apt-get -y install network-manager-openvpn
  sudo service network-manager restart
  ```
3. 게이트웨이에 대한 VPN 프로필을 다운로드합니다. Azure Portal의 지점-사이트 간 구성 탭 또는 PowerShell의 ‘New-AzureRmVpnClientConfiguration’을 사용하여 이 작업을 수행할 수 있습니다.
4. base64에서 P2S 클라이언트 인증서 개인 키를 사용하여 개인 키 섹션을 채웁니다. 개인 키를 추출하는 방법에 대한 자세한 내용은 [개인 키 내보내기](https://www.geotrust.eu/en/support/manuals/microsoft/all+windows+servers/export+private+key+or+certificate/)를 참조하세요.
5. 명령줄을 사용하여 연결하려면 다음을 입력합니다.
  
  ```
  Sudo openvpn –config <name and path of your VPN profile file>
  ```
5. GUI를 사용하여 연결하려면 시스템 설정으로 이동합니다.
6. **+** 를 클릭하여 새 VPN 연결을 추가합니다.
7. **VPN 추가**에서 **파일에서 가져오기...** 를 선택합니다.
8. 프로필 파일을 찾아보고 **열기**를 두 번 클릭하거나 선택합니다.
9. **VPN 추가** 창에서 **추가**를 클릭합니다.
  
  ![파일에서 가져오기](./media/vpn-gateway-howto-openvpn-clients/importfromfile.png)
10. **네트워크 설정** 페이지 또는 시스템 트레이의 네트워크 아이콘 아래에서 VPN을 **설정**하면 연결할 수 있습니다.

## <a name="next-steps"></a>다음 단계

VPN 클라이언트가 다른 vnet(프로덕션)의 리소스에 액세스할 수 있게 하려면 [Vnet 간](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) 문서의 지침에 따라 vnet 간 연결을 설정합니다. 게이트웨이 및 연결에서 BGP를 사용하도록 설정해야 합니다. 그렇지 않으면 트래픽이 흐르지 않습니다.
