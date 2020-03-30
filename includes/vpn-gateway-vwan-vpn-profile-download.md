---
title: 포함 파일
description: 포함 파일
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/17/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: c61378510fbfc8bdc13f35ba1063a0d9316d88e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066205"
---
## <a name="1-download-the-file"></a>1. 파일 다운로드

다음 명령을 실행합니다. 프로필 zip 파일을 다운로드하려면 결과 URL을 브라우저에 복사합니다.

```azurepowershell-interactive
$profile = New-AzVpnClientConfiguration -ResourceGroupName AADAuth -Name AADauthGW -AuthenticationMethod "EapTls"
   
$PROFILE.VpnProfileSASUrl
```

## <a name="2-extract-the-zip-file"></a>2. 지퍼 파일 추출

zip 파일의 압축을 풉니다. 파일에는 다음 폴더가 포함되어 있습니다.

* AzureVPN
* 일반
* OpenVPN(게이트웨이에서 OpenVPN 및 Azure AD 인증 설정을 사용하도록 설정한 경우) VPN 게이트웨이의 경우 [테넌트 만들기를](../articles/vpn-gateway/openvpn-azure-ad-tenant.md)참조하십시오. 가상 WAN의 경우 [테넌트 만들기 - VWAN](../articles/virtual-wan/openvpn-azure-ad-tenant.md)을 참조하십시오.

## <a name="3-retrieve-information"></a>3. 정보 검색

**AzureVPN** 폴더에서 ***azurevpnconfig.xml*** 파일로 이동하여 메모장을 사용하여 엽니다. 다음 태그 사이의 텍스트를 기록합니다.

```
<audience>          </audience>
<issuer>            </issuer>
<tennant>           </tennant>
<fqdn>              </fqdn>
<serversecret>      </serversecret>
```

## <a name="profile-details"></a>프로필 세부 정보

연결을 추가할 때 이전 단계에서 수집한 정보를 프로필 세부 정보 페이지에 사용합니다. 필드는 다음 정보에 해당합니다.

   * **잠재고객:** 토큰이 의도한 받는 사람 리소스를 식별합니다.
   * **발급자:** Azure AD 테넌트뿐만 아니라 토큰을 내보낸 STS(보안 토큰 서비스)를 식별합니다.
   * **테넌트:** 토큰을 발행한 디렉터리 테넌트의 변경할 수 없는 고유 식별자가 포함되어 있습니다.
   * **FQDN:** Azure VPN 게이트웨이에서 정규화된 도메인 이름(FQDN)
   * **서버 시크릿:** VPN 게이트웨이 사전 공유 키

## <a name="folder-contents"></a>폴더 콘텐츠

* **일반 폴더에는** 공용 서버 인증서와 VpnSettings.xml 파일이 포함되어 있습니다. VpnSettings.xml 파일에는 일반 클라이언트를 구성하는 데 필요한 정보가 포함되어 있습니다.

* 다운로드 한 zip 파일에는 **WindowsAmd64** 및 **WindowsX86** 폴더도 포함될 수 있습니다. 이러한 폴더에는 Windows 클라이언트용 SSTP 및 IKEv2용 설치 프로그램이 포함되어 있습니다. 클라이언트를 설치하려면 관리자 권한이 필요합니다.