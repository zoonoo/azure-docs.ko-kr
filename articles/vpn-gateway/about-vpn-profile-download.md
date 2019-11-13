---
title: '지점 및 사이트 간 VPN 클라이언트 프로필 정보: Azure VPN Gateway | Microsoft Docs'
description: 이렇게 하면 클라이언트 프로필 파일을 사용할 수 있습니다.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 11/04/2019
ms.author: cherylmc
ms.openlocfilehash: 340ff6d9b112f4ff04950499b2708d8882fa6120
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73960623"
---
# <a name="about-p2s-vpn-client-profiles"></a>P2S VPN 클라이언트 프로필 정보

다운로드 한 프로필 파일에는 VPN 연결을 구성 하는 데 필요한 정보가 포함 되어 있습니다. 이 문서는 VPN 클라이언트 프로필에 필요한 정보를 얻고 이해 하는 데 도움이 됩니다.

## <a name="1-download-the-file"></a>1. 파일을 다운로드 합니다.

다음 명령을 실행합니다. 프로필 zip 파일을 다운로드 하기 위해 결과 URL을 브라우저에 복사 합니다.

```azurepowershell-interactive
$profile = New-AzVpnClientConfiguration -ResourceGroupName AADAuth -Name AADauthGW -AuthenticationMethod "EapTls"
   
$PROFILE.VpnProfileSASUrl
```

## <a name="2-extract-the-zip-file"></a>2. zip 파일의 압축을 풉니다.

zip 파일의 압축을 풉니다. 파일에는 다음 폴더가 포함 되어 있습니다.

* AzureVPN
* 일반
* OpenVPN (게이트웨이에서 OpenVPN 및 Azure AD 인증 설정을 사용 하도록 설정한 경우) [테 넌 트 만들기를](openvpn-azure-ad-tenant.md)참조 하세요.)

## <a name="3-retrieve-information"></a>3. 정보 검색

**Azurevpn** 폴더에서 ***azurevpnconfig*** 파일로 이동 하 여 메모장에서 엽니다. 다음 태그 사이에 있는 텍스트를 적어둡니다.

```
<audience>          </audience>
<issuer>            </issuer>
<tennant>           </tennant>
<fqdn>              </fqdn>
<serversecret>      </serversecret>
```

## <a name="profile-details"></a>프로필 세부 정보

연결을 추가 하는 경우 이전 단계에서 프로필 세부 정보 페이지에 대해 수집한 정보를 사용 합니다. 필드는 다음 정보에 해당 합니다.

   * **대상:** 토큰이 사용 되는 받는 사람 리소스를 식별 합니다.
   * **발급자:** Azure AD 테 넌 트 뿐만 아니라 토큰을 내보낸 STS (보안 토큰 서비스)를 식별 합니다.
   * **테 넌 트:** 토큰을 발급 한 디렉터리 테 넌 트의 변경할 수 없는 고유 식별자를 포함 합니다.
   * **FQDN:** Azure VPN gateway의 FQDN (정규화 된 도메인 이름)
   * **Serversecret:** VPN gateway 미리 공유한 키

## <a name="folder-contents"></a>폴더 내용

* **Openvpn 폴더** 에는 키와 인증서를 포함 하도록 수정 해야 하는 *ovpn* 프로필이 포함 되어 있습니다. 자세한 내용은 [Azure VPN Gateway에 대 한 OpenVPN 클라이언트 구성](vpn-gateway-howto-openvpn-clients.md#windows)을 참조 하세요.

* **일반 폴더** 에는 공용 서버 인증서와 vpnsettings.xml 파일이 포함 되어 있습니다. Vpnsettings.xml 파일에는 일반 클라이언트를 구성 하는 데 필요한 정보가 포함 되어 있습니다.

* 다운로드 한 zip 파일에도 **WindowsAmd64** 및 **WindowsX86** 폴더가 포함 될 수 있습니다. 이러한 폴더에는 SSTP 용 설치 관리자와 Windows 클라이언트용 IKEv2가 포함 됩니다. 클라이언트를 설치 하려면 관리자 권한이 있어야 합니다.

## <a name="next-steps"></a>다음 단계

지점 및 사이트 간 정보에 대 한 자세한 내용은 [지점 및 사이트](point-to-site-about.md)간 정보를 참조 하세요.
