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
ms.openlocfilehash: f3eb2d9469ab3a3d2c1d09e4adc3ee2cb1f86e6e
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/09/2021
ms.locfileid: "99979075"
---
## <a name="extract-the-zip-file"></a>Zip 파일의 압축을 풉니다.

zip 파일의 압축을 풉니다. 이 파일에는 다음 폴더가 포함됩니다.

* AzureVPN
* 일반
* OpenVPN (게이트웨이에서 **Azure 인증서** 또는 **RADIUS 인증** 설정으로 openvpn을 사용 하도록 설정한 경우) 구성에 해당 하는 적절 한 문서를 선택 하 여 테 넌 트를 만듭니다.

  * [VPN Gateway-테 넌 트를 만듭니다](../articles/vpn-gateway/openvpn-azure-ad-tenant.md).
  * [가상 WAN-테 넌 트를 만듭니다](../articles/virtual-wan/openvpn-azure-ad-tenant.md).

## <a name="retrieve-information"></a>정보 검색

**Azurevpn** 폴더에서 **_azurevpnconfig.xml_** 파일로 이동 하 여 메모장에서 엽니다. 다음 태그 사이에 있는 텍스트를 기록해 둡니다.

```
<audience>          </audience>
<issuer>            </issuer>
<tennant>           </tennant>
<fqdn>              </fqdn>
<serversecret>      </serversecret>
```

## <a name="profile-details"></a>프로필 세부 정보

연결을 추가할 때 이전 단계에서 수집한 정보를 사용하여 프로필 세부 사항 페이지에 사용합니다. 필드는 다음 정보에 해당합니다.

* **대상:** 토큰이 사용 되는 받는 사람 리소스를 식별 합니다.
* **발급자:** Azure AD 테 넌 트 뿐만 아니라 토큰을 내보낸 STS (보안 토큰 서비스)를 식별 합니다.
* **테 넌 트:** 토큰을 발급 한 디렉터리 테 넌 트의 변경할 수 없는 고유 식별자를 포함 합니다.
* **FQDN:** Azure VPN gateway의 FQDN (정규화 된 도메인 이름)입니다.
* **Serversecret:** VPN gateway 미리 공유한 키입니다.

## <a name="folder-contents"></a>폴더 내용

* **일반 폴더** 에는 공용 서버 인증서와 VpnSettings.xml 파일이 포함되어 있습니다. VpnSettings.xml 파일에는 일반 클라이언트를 구성하는 데 필요한 정보가 포함되어 있습니다.

* 다운로드한 zip 파일에는 **WindowsAmd64** 및 **WindowsX86** 폴더가 포함될 수도 있습니다. 이 폴더에는 SSTP용 설치 프로그램과 Windows 클라이언트용 IKEv2가 포함됩니다. 설치하려는 클라이언트에 대한 관리자 권한이 있어야 합니다.
