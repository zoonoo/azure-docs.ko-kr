---
title: 'VPN 게이트웨이: VPN 클라이언트 문제 해결 - Azure AD 인증'
description: VPN 게이트웨이 P2S Azure AD 인증 클라이언트 문제 해결
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: cherylmc
ms.openlocfilehash: 8871e92f0911c4d3cbcc1772bef1daeb5c70b5d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74151963"
---
# <a name="troubleshoot-an-azure-ad-authentication-vpn-client"></a>Azure AD 인증 VPN 클라이언트 문제 해결

이 문서에서는 POINT-To-Site VPN 및 Azure Active Directory 인증을 사용하여 VPN 클라이언트가 가상 네트워크에 연결하는 문제를 해결하는 데 도움이 됩니다.

## <a name="view-status-log"></a><a name="status"></a>상태 로그 보기

오류 메시지에 대한 상태 로그를 봅니다.

![로그](./media/troubleshoot-ad-vpn-client/1.png)

1. 클라이언트 창의 오른쪽 하단모서리에 있는 화살표 아이콘을 클릭하여 **상태 로그를 표시합니다.**
2. 로그에서 문제를 나타낼 수 있는 오류가 있는지 확인합니다.
3. 오류 메시지가 빨간색으로 표시됩니다.

## <a name="clear-sign-in-information"></a><a name="clear"></a>로그인 정보 지우기

로그인 정보를 지웁히 합니다.

![로그인](./media/troubleshoot-ad-vpn-client/2.png)

1. 선택 ... 문제 해결하려는 프로필 옆에 있습니다. **저장계정 정리를 > 선택**.
2. **저장**을 선택합니다.
3. 연결을 시도합니다.
4. 연결이 계속 실패하면 다음 섹션으로 계속 진행합니다.

## <a name="run-diagnostics"></a><a name="diagnostics"></a>진단 실행

VPN 클라이언트에서 진단을 실행합니다.

![진단](./media/troubleshoot-ad-vpn-client/3.png)

1. 를 **클릭합니다.** 로 진단 프로그램을 실행하려는 프로필 옆에 있습니다. **진단 -> 실행 진단을 선택합니다.**
2. 클라이언트는 일련의 테스트를 실행하고 테스트 결과를 표시합니다.

   * 인터넷 액세스 - 클라이언트에 인터넷 연결이 있는지 확인
   * 클라이언트 자격 증명 - Azure Active Directory 인증 끝점에 연결할 수 있는지 확인합니다.
   * 서버 해결 가능 – DNS 서버에 연결하여 구성된 VPN 서버의 IP 주소를 확인합니다.
   * 서버에 연결할 수 있습니다 - VPN 서버가 응답하고 있는지 확인합니다.
3. 테스트가 실패하면 네트워크 관리자에게 문의하여 문제를 해결하십시오.
4. 다음 섹션에서는 필요한 경우 로그를 수집하는 방법을 보여 주며, 필요한 경우 로그를 수집하는 방법을 보여 주시면 됩니다.

## <a name="collect-client-log-files"></a><a name="logfiles"></a>클라이언트 로그 파일 수집

VPN 클라이언트 로그 파일을 수집합니다. 로그 파일은 사용자가 선택한 방법을 통해 지원/관리자에게 전송할 수 있습니다. 예를 들어 전자 메일을 보겠습니다.

1. "..." 로 진단 프로그램을 실행하려는 프로필 옆에 있습니다. **진단 -> 로그 디렉터리 표시를 선택합니다.**

   ![로그 표시](./media/troubleshoot-ad-vpn-client/4.png)
2. Windows 탐색기는 로그 파일이 포함된 폴더로 열립니다.

   ![파일 보기](./media/troubleshoot-ad-vpn-client/5.png)

## <a name="next-steps"></a>다음 단계

자세한 내용은 [Azure AD 인증을 사용하는 P2S Open VPN 연결에 대한 Azure Active Directory 테넌트 만들기를](openvpn-azure-ad-tenant.md)참조하십시오.