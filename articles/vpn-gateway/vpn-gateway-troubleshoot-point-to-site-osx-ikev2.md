---
title: 'Azure VPN Gateway: 지점 및 사이트 간 연결 문제 해결: Mac OS X 클라이언트'
description: 기본 VPN 클라이언트 및 IKEv2를 사용 하 여 Mac OS X에서 지점 및 사이트 간 연결 문제를 해결 하는 방법에 대해 알아봅니다.
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: troubleshooting
ms.date: 03/27/2018
ms.author: alzam
ms.openlocfilehash: be7b6349a7bf51a4fb99e3604a04f9d95403b74f
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91359327"
---
# <a name="troubleshoot-point-to-site-vpn-connections-from-mac-os-x-vpn-clients"></a>Mac OS X VPN 클라이언트에서 지점 및 사이트 간 VPN 연결 문제 해결

이 문서는 기본 VPN 클라이언트와 IKEv2를 사용하여 Mac OS X에서 지점 및 사이트 간 연결 문제를 해결하는 데 도움이 됩니다. IKEv2용 Mac의 VPN 클라이언트는 매우 기본적이며, 많은 사용자 지정을 허용하지 않습니다. 확인해야 하는 네 가지 설정만 있습니다.

* 서버 주소
* 원격 ID
* 로컬 ID
* 인증 설정
* OS 버전 10.11 이상


## <a name="troubleshoot-certificate-based-authentication"></a><a name="VPNClient"></a> 인증서 기반 인증 문제 해결
1. VPN 클라이언트 설정을 확인합니다. Command+Shift를 눌러 **네트워크 설정**으로 이동한 다음, “VPN”을 입력하여 VPN 클라이언트 설정을 확인합니다. 목록에서 조사해야 하는 VPN 항목을 클릭합니다.

   ![IKEv2 인증서 기반 인증](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2cert1.jpg)
2. **서버 주소**가 완전한 FQDN이고 cloudapp.net을 포함하는지 확인합니다.
3. **원격 ID**는 서버 주소(게이트웨이 FQDN)와 동일해야 합니다.
4. **로컬 ID**는 클라이언트 인증서의 **주체**와 동일해야 합니다.
5. **인증 설정**을 클릭하여 인증 설정 페이지를 엽니다.

   ![인증서가 선택 된 인증 설정 대화 상자를 보여 주는 스크린샷](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2auth2.jpg)
6. 드롭다운에서 **인증서**가 선택되었는지 확인합니다.
7. **선택** 단추를 클릭하고 올바른 인증서가 선택되었는지 확인합니다. **확인**을 클릭하여 변경 내용을 저장합니다.

## <a name="troubleshoot-username-and-password-authentication"></a><a name="ikev2"></a>사용자 이름 및 암호 인증 문제 해결

1. VPN 클라이언트 설정을 확인합니다. Command+Shift를 눌러 **네트워크 설정**으로 이동한 다음, “VPN”을 입력하여 VPN 클라이언트 설정을 확인합니다. 목록에서 조사해야 하는 VPN 항목을 클릭합니다.

   ![IKEv2 사용자 이름 암호](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2user3.jpg)
2. **서버 주소**가 완전한 FQDN이고 cloudapp.net을 포함하는지 확인합니다.
3. **원격 ID**는 서버 주소(게이트웨이 FQDN)와 동일해야 합니다.
4. **로컬 ID**는 공백일 수 있습니다.
5. **인증 설정** 단추를 클릭하고 드롭다운에서 “사용자 이름”이 선택되었는지 확인합니다.

   ![스크린샷 선택한 사용자 이름으로 인증 설정 대화 상자를 표시 합니다.](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2auth4.png)
6. 올바른 자격 증명 정보가 입력되었는지 확인합니다.

## <a name="additional-steps"></a><a name="additional"></a>추가 단계

이전 단계를 시도했으며 모든 항목이 제대로 구성된 경우 [Wireshark](https://www.wireshark.org/#download)를 다운로드하고 패킷 캡처를 수행합니다.

1. *isakmp*를 필터링하고 **IKE_SA** 패킷을 확인합니다. **페이로드: 보안 연결** 아래에서 SA 제안 세부 정보를 확인할 수 있습니다. 
2. 클라이언트와 서버에 공통 집합이 있는지 확인합니다.

   ![패킷](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/packet5.jpg) 
  
3. 네트워크 추적에 서버 응답이 없으면 Azure Portal 웹 사이트의 Azure 게이트웨이 구성 페이지에서 IKEv2 프로토콜을 사용 하도록 설정 했는지 확인 합니다.

## <a name="next-steps"></a>다음 단계
자세한 내용은 [Microsoft 지원](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)을 참조하세요.
