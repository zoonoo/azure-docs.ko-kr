---
title: 포털을 통해 Azure Application Gateway에서 상호 인증 구성
description: 포털을 통해 상호 인증을 포함하도록 Application Gateway를 구성하는 방법 알아보기
services: application-gateway
author: mscatyao
ms.service: application-gateway
ms.topic: how-to
ms.date: 04/02/2021
ms.author: caya
ms.openlocfilehash: b2118a257f029a63445b09dfa618d6e3ceacddda
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/02/2021
ms.locfileid: "106230942"
---
# <a name="configure-mutual-authentication-with-application-gateway-through-portal-preview"></a>포털을 통해 Application Gateway에서 상호 인증 구성(미리 보기)

이 문서에서는 Azure Portal을 사용하여 Application Gateway에서 상호 인증을 구성하는 방법을 설명합니다. 상호 인증은 Application Gateway가 Application Gateway에 업로드한 클라이언트 인증서를 사용하여 요청을 보내는 클라이언트를 인증함을 의미합니다. 

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="before-you-begin"></a>시작하기 전에

Application Gateway를 사용하여 상호 인증을 구성하려면 게이트웨이에 업로드할 클라이언트 인증서가 필요합니다. 클라이언트 인증서는 클라이언트가 Application Gateway에 제공할 인증서의 유효성을 검사하는 데 사용됩니다. 테스트용으로 자체 서명된 인증서를 만들 수 있습니다. 그러나 이 방법은 관리가 어렵고 완전히 안전하지 않으므로 프로덕션 워크로드에는 권장되지 않습니다. 

특히 업로드할 수 있는 클라이언트 인증서의 종류에 대한 자세한 내용은 [Application Gateway를 사용한 상호 인증 개요](./mutual-authentication-overview.md#certificates-supported-for-mutual-authentication)를 참조하세요.

## <a name="create-a-new-application-gateway"></a>새 Application Gateway 만들기

먼저 일반적으로 포털에서 만드는 것처럼 새 Application Gateway를 만듭니다. 상호 인증을 사용하도록 설정하는 데 필요한 추가 단계가 없습니다. 포털에서 Application Gateway를 만드는 방법에 관한 자세한 내용은 [포털 빠른 시작 자습서](./quick-create-portal.md)를 확인하세요.

## <a name="configure-mutual-authentication"></a>상호 인증 구성 

상호 인증을 사용하여 기존 Application Gateway를 구성하려면 먼저 포털에서 **SSL 설정(미리 보기)** 탭으로 이동하고 새 SSL 프로필을 만들어야 합니다. SSL 프로필을 만들면 **클라이언트 인증** 및 **SSL 정책** 탭이 표시됩니다. **클라이언트 인증** 탭에서는 클라이언트 인증서를 업로드합니다. **SSL 정책** 탭에서는 수신기 관련 SSL 정책을 구성합니다. 자세한 내용은 [수신기 관련 SSL 정책 구성](./application-gateway-configure-listener-specific-ssl-policy.md)을 확인하세요.

> [!IMPORTANT]
> 전체 클라이언트 CA 인증서 체인을 한 파일에 업로드하고 파일당 하나의 체인만 업로드하는지 확인하세요.

1. 포털에서 **Application Gateway** 를 검색하고, **애플리케이션 게이트웨이** 를 선택하고, 기존 Application Gateway를 클릭합니다.

2. 왼쪽 메뉴에서 **SSL 설정(미리 보기)** 을 선택합니다.

3. 위쪽에서 **SSL 프로필** 옆에 있는 더하기 기호를 클릭하여 새 SSL 프로필을 만듭니다.

4. **SSL 프로필 이름** 아래에 이름을 입력합니다. 이 예제에서는 SSL 프로필 *applicationGatewaySSLProfile* 을 호출합니다. 

5. **클라이언트 인증** 탭에서 계속합니다. **새 인증서 업로드** 단추를 사용하여 클라이언트와 Application Gateway 간 상호 인증에 사용할 PEM 인증서를 업로드합니다. 

    여기에서 업로드할 신뢰할 수 있는 클라이언트 CA 인증서 체인을 추출하는 방법에 관한 자세한 내용은 [신뢰할 수 있는 클라이언트 CA 인증서 체인을 추출하는 방법](./mutual-authentication-certificate-management.md)을 참조하세요.

   > [!NOTE]
   > 이것이 첫 번째 SSL 프로필이 아니고 다른 클라이언트 인증서를 Application Gateway에 업로드한 경우 드롭다운 메뉴를 통해 게이트웨이에서 기존 인증서를 다시 사용하도록 선택할 수 있습니다. 

6. Application Gateway에서 클라이언트 인증서의 즉시 발급자 고유 이름을 확인하려는 경우에만 **클라이언트 인증서 발급자의 DN 확인** 상자를 선택합니다. 

7. 수신기 관련 정책을 추가하는 것이 좋습니다. [수신기 관련 SSL 정책 설정](./application-gateway-configure-listener-specific-ssl-policy.md)에서 지침을 참조하세요.

8. **추가** 를 선택하여 저장합니다.
    > [!div class="mx-imgBorder"]
    > ![SSL 프로필에 클라이언트 인증 추가](./media/mutual-authentication-portal/mutual-authentication-portal.png)

## <a name="associate-the-ssl-profile-with-a-listener"></a>수신기와 SSL 프로필 연결

상호 인증이 구성된 SSL 프로필을 만들었으므로 이제 상호 인증 설정을 완료하려면 SSL 프로필을 수신기에 연결해야 합니다. 

1. 기존 Application Gateway로 이동합니다. 위 단계를 완료한 경우에는 여기에서 작업을 수행하지 않아도 됩니다. 

2. 왼쪽 메뉴에서 **수신기** 를 선택합니다. 

3. HTTPS 수신기를 아직 설정하지 않은 경우 **수신기 추가** 를 클릭합니다. HTTPS 수신기가 이미 있는 경우 목록에서 클릭합니다. 

4. 요구 사항에 맞게 **수신기 이름**, **프런트 엔드 IP**, **포트**, **프로토콜**, 기타 **HTTPS 설정** 을 입력합니다.

5. 수신기와 연결할 SSL 프로필을 선택할 수 있도록 **SSL 프로필 사용** 확인란을 선택합니다. 

6. 드롭다운 목록에서 방금 만든 SSL 프로필을 선택합니다. 이 예제에서는 이전 단계에서 만든 SSL 프로필 *applicationGatewaySSLProfile* 을 선택합니다. 

7. 요구 사항에 맞게 나머지 수신기를 계속 구성합니다. 

8. **추가** 를 클릭하여 연결된 SSL 프로필을 사용하여 새 수신기를 저장합니다. 

    > [!div class="mx-imgBorder"]
    > ![새 수신기에 SSL 프로필 연결](./media/mutual-authentication-portal/mutual-authentication-listener-portal.png)

## <a name="renew-expired-client-ca-certificates"></a>만료된 클라이언트 CA 인증서 갱신

클라이언트 CA 인증서가 만료된 경우 다음 단계를 통해 게이트웨이에서 인증서를 업데이트할 수 있습니다. 

1. Application Gateway로 이동하고 왼쪽 메뉴에서 **SSL 설정(미리 보기)** 탭으로 이동합니다. 
 
1. 만료된 클라이언트 인증서가 있는 기존 SSL 프로필을 선택합니다. 
 
1. **클라이언트 인증** 탭에서 **새 인증서 업로드** 를 선택하고 새 클라이언트 인증서를 업로드합니다. 
 
1. 만료된 인증서 옆에 있는 휴지통 아이콘을 선택합니다. 그러면 SSL 프로필에서 해당 인증서의 연결이 제거됩니다. 

1. 동일한 만료된 클라이언트 인증서를 사용하고 있던 다른 SSL 프로필을 사용하여 위 2~4단계를 반복합니다. 다른 SSL 프로필의 드롭다운 메뉴에서 3단계에서 업로드한 새 인증서를 선택할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Azure CLI를 사용하여 애플리케이션 게이트웨이로 웹 트래픽 관리](./tutorial-manage-web-traffic-cli.md)