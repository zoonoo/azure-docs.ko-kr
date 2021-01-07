---
title: 포함 파일
description: 포함 파일
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 10/06/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 9caf63fc90be7bae0461ddc24c94594a32199765
ms.sourcegitcommit: 5abc3919a6b99547f8077ce86a168524b2aca350
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/07/2020
ms.locfileid: "91812707"
---
#### <a name="microsoft-windows"></a>Microsoft Windows

##### <a name="openvpn"></a>OpenVPN

1. 공식 웹 사이트에서 OpenVPN 클라이언트를 다운로드하고 설치합니다.
1. 게이트웨이에 대한 VPN 프로필을 다운로드합니다. Azure Portal의 사용자 VPN 구성 탭 또는 PowerShell의 New-AzureRmVpnClientConfiguration에서 이 작업을 수행할 수 있습니다.
1. 프로필의 압축을 풉니다. 메모장에서 OpenVPN 폴더의 vpnconfig.ovpn 구성 파일을 엽니다.
1. base64에서 P2S 클라이언트 인증서 공개 키를 사용하여 P2S 클라이언트 인증서 섹션을 채웁니다. PEM 형식의 인증서에서 .cer 파일을 열고 인증서 헤더 사이에 base64 키를 복사할 수 있습니다. 단계는 [인증서를 내보내 인코딩된 공개 키를 가져오는 방법](../articles/virtual-wan/certificates-point-to-site.md)을 참조하세요.
1. base64에서 P2S 클라이언트 인증서 프라이빗 키를 사용하여 프라이빗 키 섹션을 채웁니다. 단계는 [프라이빗 키를 추출하는 방법](../articles/virtual-wan/howto-openvpn-clients.md#windows)을 참조하세요.
1. 다른 필드는 변경하지 마세요. 클라이언트 입력에 채워진 구성을 사용하여 VPN에 연결합니다.
1. vpnconfig.ovpn 파일을 C:\Program Files\OpenVPN\config 폴더에 복사합니다.
1. 시스템 트레이에서 OpenVPN 아이콘을 마우스 오른쪽 단추로 클릭하고 **연결**을 선택합니다.

##### <a name="ikev2"></a>IKEv2

1. Windows 컴퓨터의 아키텍처에 해당하는 VPN 클라이언트 구성 파일을 선택합니다. 64비트 프로세서 아키텍처의 경우 'VpnClientSetupAmd64' 설치 관리자 패키지를 선택합니다. 32비트 프로세서 아키텍처의 경우 'VpnClientSetupX86' 설치 관리자 패키지를 선택합니다.
1. 해당 패키지를 두 번 클릭하여 설치합니다. SmartScreen 팝업이 표시되면 **추가 정보**를 선택한 다음, **실행**을 선택합니다.
1. 클라이언트 컴퓨터에서 **네트워크 설정**으로 이동하고 **VPN**을 선택합니다. VPN 연결에서 연결되는 가상 네트워크의 이름을 표시합니다.
1. 연결을 시도하기 전에 먼저 클라이언트 컴퓨터에 클라이언트 인증서가 설치되어 있어야 합니다. Azure 기본 인증서 인증 유형을 사용할 때 인증을 위해 클라이언트 인증서가 필요합니다. 인증서 생성에 대한 자세한 내용은 [인증서 생성](../articles/virtual-wan/certificates-point-to-site.md)을 참조하세요. 클라이언트 인증서를 설치하는 방법은 [클라이언트 인증서 설치](../articles/vpn-gateway/point-to-site-how-to-vpn-client-install-azure-cert.md)를 참조하세요.
