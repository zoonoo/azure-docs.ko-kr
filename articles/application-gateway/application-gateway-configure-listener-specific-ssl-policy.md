---
title: 포털을 통해 Azure Application Gateway에서 수신기별 SSL 정책 구성
description: 포털을 통해 Application Gateway에서 수신기별 SSL 정책을 구성하는 방법을 알아봅니다.
services: application-gateway
author: mscatyao
ms.service: application-gateway
ms.topic: how-to
ms.date: 03/30/2021
ms.author: caya
ms.openlocfilehash: b0bda1921c63b4e353365c3b391e17f620740396
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/02/2021
ms.locfileid: "106231003"
---
# <a name="configure-listener-specific-ssl-policies-on-application-gateway-through-portal-preview"></a>포털을 통해 Application Gateway에서 수신기별 SSL 정책 구성(미리 보기)

이 문서에서는 Azure Portal을 사용하여 Application Gateway에서 수신기별 SSL 정책을 구성하는 방법을 설명합니다. 수신기별 SSL 정책을 사용하면 특정 수신기가 서로 다른 SSL 정책을 사용하도록 구성할 수 있습니다. 수신기별 SSL 정책으로 덮어쓰이지 않는 한 모든 수신기가 사용하는 기본 SSL 정책을 계속 설정해 놓을 수 있습니다. 

> [!NOTE]
> 수신기별 정책은 SSL 프로필의 일부이므로 Standard_v2 및 WAF_v2 SKU만이 수신기별 정책을 지원하며 SSL 프로필은 v2 게이트웨이에서만 지원됩니다. 



Azure 구독이 아직 없는 경우, 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="create-a-new-application-gateway"></a>새 Application Gateway 만들기

먼저 일반적으로 포털을 통해 하듯이 새 Application Gateway를 만듭니다. 만들 때 수신기별 SSL 정책을 구성하기 위한 추가 단계는 없습니다. 포털에서 Application Gateway를 만드는 방법에 대한 자세한 내용은 [포털 빠른 시작 가이드](./quick-create-portal.md)를 확인하세요.

## <a name="set-up-a-listener-specific-ssl-policy"></a>수신기별 SSL 정책 설정

수신기별 SSL 정책을 설정하려면 먼저 포털의 **SSL 설정(미리 보기)** 탭으로 이동하여 새 SSL 프로필을 만들어야 합니다. SSL 프로필을 만들 때 **클라이언트 인증** 및 **SSL 정책** 이라는 두 개의 탭이 표시됩니다. **SSL 정책** 탭은 수신기별 SSL 정책을 구성하기 위한 것입니다. **클라이언트 인증** 탭은 상호 인증을 위해 클라이언트 인증서를 업로드하는 곳입니다. 자세한 내용은 [상호 인증 구성](./mutual-authentication-portal.md)을 참조하세요.

> [!NOTE]
> TLS 1.2는 나중에 위임될 것이므로 TLS 1.2를 사용하는 것이 좋습니다. 

1. 포털에서 **Application Gateway** 를 검색하고 **애플리케이션 게이트웨이** 를 선택한 다음 기존 Application Gateway를 클릭합니다.

2. 왼쪽 메뉴에서 **SSL 설정(미리 보기)** 을 선택합니다.

3. 상단의 **SSL 프로필** 옆에 있는 더하기 기호를 클릭하여 새 SSL 프로필을 만듭니다.

4. **SSL 프로필 이름** 아래에 이름을 입력합니다. 이 예에서는 SSL 프로필을 *applicationGatewaySSLProfile* 이라고 합니다. 

5. **SSL 정책** 탭으로 이동하여 **수신기별 SSL 정책 사용** 확인란을 선택합니다. 

6. 요구 사항에 따라 수신기별 SSL 정책을 설정합니다. 미리 정의된 SSL 정책 중에서 선택하고 고유한 SSL 정책을 사용자 지정할 수 있습니다. SSL 정책에 대한 자세한 내용은 [SSL 정책 개요](./application-gateway-ssl-policy-overview.md)를 참조하세요. TLS 1.2를 사용하는 것이 좋습니다.

7. 저장하려면 **추가** 를 선택합니다.

    > [!NOTE]
    > 수신기에 연결하기 위해 SSL 프로필에 대해 클라이언트 인증을 구성할 필요가 없습니다. 클라이언트 인증만 구성하거나 수신기 특정 SSL 정책만 구성하거나 SSL 프로필에 둘 다 구성할 수 있습니다.  

    ![SSL 프로필에 수신기별 SSL 정책 추가](./media/application-gateway-configure-listener-specific-ssl-policy/listener-specific-ssl-policy-ssl-profile.png)
    
## <a name="associate-the-ssl-profile-with-a-listener"></a>수신기에 SSL 프로필 연결

이제 수신기 관련 SSL 정책을 사용하여 SSL 프로필을 만들었으므로 수신기에 SSL 프로필을 연결하여 수신기별 정책을 사용하도록 해야 합니다. 

1. 기존 Application Gateway로 이동합니다. 위의 단계를 완료한 경우에는 여기에서 더 이상 작업을 수행할 필요가 없습니다. 

2. 왼쪽 메뉴에서 **수신기** 를 선택합니다. 

3. HTTPS 수신기를 아직 설정하지 않은 경우 **수신기 추가** 를 클릭합니다. HTTPS 수신기가 이미 있는 경우 목록에서 클릭합니다. 

4. 요구 사항에 맞게 **수신기 이름**, **프런트 엔드 IP**, **포트**, **프로토콜** 및 기타 **HTTPS 설정** 을 입력합니다.

5. 수신기와 연결할 SSL 프로필을 선택할 수 있도록 **SSL 프로필 사용** 확인란을 선택합니다. 

6. 드롭다운 목록에서 만든 SSL 프로필을 선택합니다. 이 예에서는 이전 단계에서 만든 SSL 프로필인 *applicationGatewaySSLProfile* 을 선택합니다. 

7. 요구 사항에 맞게 나머지 수신기를 계속 구성합니다. 

8. **추가** 를 클릭하여 SSL 프로필이 연결된 새 수신기를 저장합니다. 

    ![새 수신기에 SSL 프로필 연결](./media/mutual-authentication-portal/mutual-authentication-listener-portal.png)        

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure CLI를 사용하여 애플리케이션 게이트웨이로 웹 트래픽 관리](./tutorial-manage-web-traffic-cli.md)