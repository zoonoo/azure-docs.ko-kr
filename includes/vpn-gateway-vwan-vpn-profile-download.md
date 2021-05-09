---
title: 포함 파일
description: 포함 파일
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/08/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: a5ae81cde28efbf57965beda2f82915854579e43
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106073209"
---
## <a name="extract-the-zip-file"></a>ZIP 파일 추출

zip 파일의 압축을 풉니다. 이 파일에는 다음 폴더가 포함됩니다.

* AzureVPN
* 일반
* OpenVPN(게이트웨이에서 **Azure 인증서** 또는 **RADIUS 인증** 설정으로 OpenVPN을 사용하도록 설정한 경우). 구성에 해당하는 적절한 문서를 선택하여 테넌트를 만듭니다.

  * [VPN Gateway - 테넌트를 만듭니다](../articles/vpn-gateway/openvpn-azure-ad-tenant.md).
  * [Virtual WAN - 테넌트를 만듭니다](../articles/virtual-wan/openvpn-azure-ad-tenant.md).

## <a name="retrieve-information"></a>정보 검색

**AzureVPN** 폴더에서 **_azurevpnconfig.xml_** 파일로 이동하여 메모장에서 엽니다. 다음 태그 사이에 있는 텍스트를 기록해 둡니다.

```
<audience>          </audience>
<issuer>            </issuer>
<tennant>           </tennant>
<fqdn>              </fqdn>
<serversecret>      </serversecret>
```

## <a name="profile-details"></a>프로필 세부 정보

연결을 추가할 때 이전 단계에서 수집한 정보를 사용하여 프로필 세부 사항 페이지에 사용합니다. 필드는 다음 정보에 해당합니다.

* **대상:** 토큰의 의도된 수신자 리소스를 식별합니다.
* **발급자:** Azure AD 테넌트뿐만 아니라 토큰을 내보낸 STS(보안 토큰 서비스)를 식별합니다.
* **테넌트:** 토큰을 발급한 디렉터리 테넌트에 대한 변경 불가능한 고유 식별자를 포함합니다.
* **FQDN:** Azure VPN 게이트웨이의 FQDN(정규화된 도메인 이름).
* **ServerSecret:** VPN 게이트웨이에 미리 공유한 키.

## <a name="folder-contents"></a>폴더 내용

* **일반 폴더** 에는 공용 서버 인증서와 VpnSettings.xml 파일이 포함되어 있습니다. VpnSettings.xml 파일에는 일반 클라이언트를 구성하는 데 필요한 정보가 포함되어 있습니다.

* 다운로드한 zip 파일에는 **WindowsAmd64** 및 **WindowsX86** 폴더가 포함될 수도 있습니다. 이 폴더에는 SSTP용 설치 프로그램과 Windows 클라이언트용 IKEv2가 포함됩니다. 설치하려는 클라이언트에 대한 관리자 권한이 있어야 합니다.
