---
title: 지점 및 사이트 간 VPN 클라이언트 문제 해결 - Azure AD 인증
titleSuffix: Azure VPN Gateway
description: Azure AD 인증을 사용하는 VPN Gateway 지점 및 사이트 간 클라이언트 문제를 해결하는 방법에 대해 알아봅니다.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: troubleshooting
ms.date: 04/29/2021
ms.author: cherylmc
ms.openlocfilehash: d307524fffd9b129044cd0f4e4b20d833db5ba83
ms.sourcegitcommit: fc9fd6e72297de6e87c9cf0d58edd632a8fb2552
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/30/2021
ms.locfileid: "108292201"
---
# <a name="troubleshoot-an-azure-ad-authentication-vpn-client"></a>Azure AD 인증 VPN 클라이언트 문제 해결

이 문서는 지점 및 사이트 간 VPN과 Azure Active Directory 인증을 사용하여 가상 네트워크에 연결하기 위해 VPN 클라이언트 문제를 해결하는 데 도움이 됩니다.

## <a name="view-status-log"></a><a name="status"></a>상태 로그 보기

오류 메시지에 대한 상태 로그를 봅니다.

![로그](./media/troubleshoot-ad-vpn-client/1.png)

1. 클라이언트 창의 오른쪽 아래 구석에 있는 화살표 아이콘을 클릭하여 **상태 로그** 를 표시합니다.
2. 문제를 나타낼 수 있는 오류에 대한 로그를 확인합니다.
3. 오류 메시지는 적색으로 표시됩니다.

## <a name="clear-sign-in-information"></a><a name="clear"></a>로그인 정보 지우기

로그인 정보 지우기

![로그인](./media/troubleshoot-ad-vpn-client/2.png)

1. 문제를 해결하려는 프로필 옆의 ...를 선택합니다. **구성 -> 저장된 계정 선택 취소** 를 선택합니다.
2. **저장** 을 선택합니다.
3. 연결을 시도합니다.
4. 그래도 연결에 실패하는 경우 다음 항목을 계속 진행합니다.

## <a name="run-diagnostics"></a><a name="diagnostics"></a>진단 실행

VPN 클라이언트에서 진단을 실행합니다.

![진단](./media/troubleshoot-ad-vpn-client/3.png)

1. **Schemas** 속성에서 진단을 실행하려는 프로필 옆의 ...를 클릭합니다. **진단 -> 진단 실행** 을 선택합니다.
2. 클라이언트는 일련의 테스트를 실행하고 테스트 결과를 표시합니다.

   * 인터넷 액세스 – 클라이언트가 인터넷에 연결되어 있는지 확인합니다.
   * 클라이언트 자격 증명 – Azure Active Directory 인증 엔드포인트에 연결할 수 있는지 확인합니다.
   * 서버 확인 가능 - DNS 서버에 연결하여 구성된 VPN 서버의 IP 주소를 확인합니다.
   * 서버에 연결할 수 있음 – VPN 서버가 응답하는지 여부를 확인합니다.
3. 테스트가 실패하는 경우 네트워크 관리자에게 문의하여 문제를 해결하십시오.
4. 다음 섹션에서는 필요한 경우 로그를 수집하는 방법을 보여 줍니다.

## <a name="collect-client-log-files"></a><a name="logfiles"></a>클라이언트 로그 파일 수집

VPN 클라이언트 로그 파일 수집 로그 파일은 사용자가 선택한 방법으로 고객 지원팀/관리자에게 보낼 수 있습니다. 예를 들어, 전자 메일이 여기에 해당합니다.

1. 진단을 실행하려는 프로필 옆의 “...”를 클릭합니다. **진단 -> 로그 디렉터리 표시** 를 선택합니다.

   ![로그 표시](./media/troubleshoot-ad-vpn-client/4.png)
2. Windows Explorer에서 로그 파일이 포함된 폴더가 표시됩니다.

   ![파일 보기](./media/troubleshoot-ad-vpn-client/5.png)

## <a name="next-steps"></a>다음 단계

자세한 내용은 [Azure AD 인증을 사용하는 P2S Open VPN 연결에 대한 Azure Active Directory 테넌트 만들기](openvpn-azure-ad-tenant.md)를 참조하세요.