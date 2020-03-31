---
title: 포함 파일
description: 포함 파일
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 08/14/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: f322803d3484b4ec2d5449e19d67d75b35d6d92f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "75752483"
---
[!INCLUDE [P2S FAQ All](vpn-gateway-faq-p2s-all-include.md)]

### <a name="what-should-i-do-if-im-getting-a-certificate-mismatch-when-connecting-using-certificate-authentication"></a>인증서 인증을 사용하여 연결할 때 인증서 불일치가 발생하면 어떻게 해야 합니까?

**"인증서의 유효성을 검사하여 서버의 ID 확인"을** 선택 취소하거나 수동으로 프로필을 만들 때 **인증서와 함께 서버 FQDN을 추가합니다.** 명령 프롬프트에서 **rasphone을** 실행하고 드롭다운 목록에서 프로필을 선택하여 이 작업을 수행할 수 있습니다.

서버 ID 유효성 검사를 우회하는 것은 일반적으로 권장되지 않지만 Azure 인증서 인증을 사용하면 VPN 터널링 프로토콜(IKEv2/SSTP) 및 EAP 프로토콜의 서버 유효성 검사에 동일한 인증서가 사용됩니다. 서버 인증서와 FQDN은 이미 VPN 터널링 프로토콜에 의해 검증되었기 때문에 EAP에서 동일한 유효성을 다시 검사하는 것은 중복됩니다.

![지점 간](./media/vpn-gateway-faq-p2s-all-include/servercert.png "서버 인증서")

### <a name="can-i-use-my-own-internal-pki-root-ca-to-generate-certificates-for-point-to-site-connectivity"></a>내 내부 PKI 루트 CA를 사용하여 사이트 간 연결에 대한 인증서를 생성할 수 있습니까?

예. 이전에는 자체 서명한 루트 인증서만 사용할 수 있었습니다. 여전히 20개의 루트 인증서를 업로드할 수 있습니다.

### <a name="can-i-use-certificates-from-azure-key-vault"></a>Azure 키 자격 증명 모음의 인증서를 사용할 수 있습니까?

아니요.

### <a name="what-tools-can-i-use-to-create-certificates"></a>인증서를 만들려면 어떤 도구를 사용합니까?

You can use your Enterprise PKI 솔루션(사용자 인터넷 PKI), Azure PowerShell, MakeCert 및 OpenSSL을 사용할 수 있습니다.

### <a name="are-there-instructions-for-certificate-settings-and-parameters"></a><a name="certsettings"></a>인증서 설정 및 매개 변수에 대한 지침이 있습니까?

* **Internal PKI/Enterprise PKI 솔루션:**[인증서 생성](../articles/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#generatecert)단계를 봅니다.

* **Azure PowerShell:** 단계는 [Azure PowerShell](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md) 문서를 참조하세요.

* **Makecert:** 단계는 [Makecert](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-makecert.md) 문서를 참조하세요.

* **Openssl:** 

    * 인증서를 내보낼 때는 루트 인증서를 Base64로 변환해야 합니다.

    * 클라이언트 인증서:

      * 프라이빗 키를 만들 때에는 길이를 4096으로 지정합니다.
      * 인증서를 만들 때에는 *-extensions* 매개 변수에 대해 *usr_cert*를 지정합니다.
