---
title: 포함 파일
description: 포함 파일
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 05/13/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 1d14ee849c89e6c3807636d0a728157abd9de97a
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83650674"
---
## <a name="1-download-the-file"></a>1. 파일 다운로드

다음 명령을 실행합니다. 프로필 zip 파일을 다운로드하기 위해 결과 URL을 브라우저에 복사합니다.

```azurepowershell-interactive
$profile = New-AzVpnClientConfiguration -ResourceGroupName AADAuth -Name AADauthGW -AuthenticationMethod "EapTls"
   
$PROFILE.VpnProfileSASUrl
```

## <a name="2-extract-the-zip-file"></a>2. ZIP 파일 추출

zip 파일의 압축을 풉니다. 이 파일에는 다음 폴더가 포함됩니다.

* AzureVPN
* 일반
* OpenVPN(게이트웨이에서 OpenVPN 및 Azure AD 인증 설정을 사용하도록 설정한 경우) VPN Gateway의 경우 [테넌트 만들기](../articles/vpn-gateway/openvpn-azure-ad-tenant.md)를 참조하세요. 가상 WAN의 경우 [테넌트 만들기 - VWAN](../articles/virtual-wan/openvpn-azure-ad-tenant.md)을 참조하세요.

## <a name="3-retrieve-information"></a>3. 정보 검색

**AzureVPN** 폴더에서 ***azurevpnconfig.xml*** 파일로 이동하여 메모장에서 엽니다. 다음 태그 사이에 있는 텍스트를 기록해 둡니다.

```
<audience>          </audience>
<issuer>            </issuer>
<tennant>           </tennant>
<fqdn>              </fqdn>
<serversecret>      </serversecret>
```

## <a name="profile-details"></a>프로필 세부 정보

연결을 추가할 때 이전 단계에서 수집한 정보를 사용하여 프로필 세부 사항 페이지에 사용합니다. 필드는 다음 정보에 해당합니다.

   * **사용자:** 토큰의 의도된 수신자 리소스를 식별합니다.
   * **발급자:** Azure AD 테넌트뿐만 아니라 토큰을 내보낸 STS(보안 토큰 서비스)를 식별합니다.
   * **테넌트:** 토큰을 발급한 디렉터리 테넌트에 대한 변경 불가능한 고유 식별자를 포함합니다.
   * **FQDN:** Azure VPN Gateway의 FQDN(정규화된 도메인 이름)
   * **ServerSecret:** VPN 게이트웨이에 미리 공유한 키

## <a name="folder-contents"></a>폴더 내용

* **일반 폴더**에는 공용 서버 인증서와 VpnSettings.xml 파일이 포함되어 있습니다. VpnSettings.xml 파일에는 일반 클라이언트를 구성하는 데 필요한 정보가 포함되어 있습니다.

* 다운로드한 zip 파일에는 **WindowsAmd64** 및 **WindowsX86** 폴더가 포함될 수도 있습니다. 이 폴더에는 SSTP용 설치 프로그램과 Windows 클라이언트용 IKEv2가 포함됩니다. 설치하려는 클라이언트에 대한 관리자 권한이 있어야 합니다.
