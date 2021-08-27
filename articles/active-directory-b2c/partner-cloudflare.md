---
title: Cloudflare 웹 애플리케이션 방화벽을 사용하여 Azure Active Directory B2C를 구성하는 자습서
titleSuffix: Azure AD B2C
description: 악의적인 공격으로부터 애플리케이션을 보호하기 위해 Cloudflare 웹 애플리케이션 방화벽을 사용하여 Azure Active Directory B2C를 구성하는 자습서
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 04/24/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: f2b4f2e54c046ca7abc2172b8df33f14def902fd
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/24/2021
ms.locfileid: "122767899"
---
# <a name="tutorial-configure-cloudflare-with-azure-active-directory-b2c"></a>자습서: Azure Active Directory B2C를 사용하여 Cloudflare 구성

이 샘플 자습서에서는 사용자 지정 도메인을 사용하는 Azure Active Directory(AD) B2C에 대한 [Cloudflare 웹 애플리케이션 방화벽(WAF)](https://www.cloudflare.com/waf/) 솔루션을 사용하도록 설정하는 방법을 알아봅니다. Cloudflare WAF를 사용하면 조직을 SQLi 및 XSS와 같은 취약점을 악용하려는 악의적인 공격으로부터 보호할 수 있습니다.

>[!NOTE]
>이 기능은 공개 미리 보기 상태입니다.

## <a name="prerequisites"></a>필수 조건

시작하려면 다음이 필요합니다.

- Azure 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.

- [Azure AD B2C 테넌트](tutorial-create-tenant.md)를 Azure 구독에 연결

- [Cloudflare](https://dash.cloudflare.com/sign-up) 계정.

## <a name="scenario-description"></a>시나리오 설명

Cloudflare WAF 통합에는 다음 구성 요소가 포함됩니다.

- **Azure AD B2C 테넌트** - 테넌트에 정의된 사용자 지정 정책을 사용하는 사용자의 자격 증명을 확인하는 권한 부여 서버입니다.  ID 공급자라고도 합니다.

- [**Azure Front Door**](../frontdoor/front-door-overview.md) – Azure B2C 테넌트에 대한 사용자 지정 도메인 사용을 설정합니다. Cloudflare WAF의 모든 트래픽은 Azure AD B2C 테넌트에 도착하기 전에 Azure Front Door로 라우팅됩니다.

- **Cloudflare** – 권한 부여 서버로 전송되는 모든 트래픽을 관리하는 웹 애플리케이션 방화벽입니다.

## <a name="integrate-with-azure-ad-b2c"></a>Azure AD B2C와 통합

Azure AD B2C에서 사용자 지정 도메인을 사용하려면 Azure Front Door에서 제공하는 사용자 지정 도메인 기능을 사용해야 합니다. [Azure AD B2C 사용자 지정 도메인 사용](./custom-domain.md?pivots=b2c-user-flow) 방법을 알아봅니다.  

Azure Front Door를 사용하여 Azure AD B2C에 대한 사용자 지정 도메인을 구성한 후에는 계속 진행하기 전에 [사용자 지정 도메인을 테스트](./custom-domain.md?pivots=b2c-custom-policy#test-your-custom-domain)합니다.  

## <a name="onboard-with-cloudflare"></a>Cloudflare로 온보딩

[등록](https://dash.cloudflare.com/sign-up)하고 Cloudflare 계정을 만듭니다. WAF를 사용하도록 설정하려면 최소 [Pro SKU](https://www.cloudflare.com/plans/)가 필요합니다.

### <a name="configure-dns"></a>DNS 구성

1. 도메인에 대해 WAF를 사용하도록 설정하려면 도메인 id.contosobank.co.uk에 대한 예제에 표시된 것과 같이 CNAME 항목에 대한 DNS 콘솔에서 **프록시 설정을 켜야** 합니다.

   ![프록시 설정을 선택하는 방법을 보여 주는 이미지](./media/partner-cloudflare/select-proxy-settings.png)

1. DNS 창에서 사용 가능한 **프록시 상태** 옵션을 전환합니다.

1. **프록시 설정됨** 으로 전환한 후에는 주황색으로 변합니다. 최종 설정은 다음과 비슷합니다.

   ![프록시 설정됨을 선택하는 방법을 보여 주는 이미지](./media/partner-cloudflare/select-proxied.png)

### <a name="configure-waf"></a>WAF 구성

Cloudflare 설정에서 [WAF를 구성](https://www.cloudflare.com/waf/)합니다.

### <a name="configure-firewall-rule"></a>방화벽 규칙 구성

콘솔의 상단 창에서 사용할 수 있는 방화벽 옵션을 사용하여 방화벽 규칙을 추가, 업데이트 또는 제거합니다. 예를 들어 다음 방화벽 설정은 요청이 Azure Front Door로 전송되기 전에 *contosobank.co.uk* 에 대한 모든 수신 요청에 CAPTCHA를 사용합니다. [방화벽 규칙 설정](https://support.cloudflare.com/hc/articles/360016473712-Cloudflare-Firewall-Rules)에 대해 자세히 알아보세요.

![CAPTCHA 적용 방법을 보여 주는 이미지](./media/partner-cloudflare/configure-firewall-rule.png)

### <a name="test-the-settings"></a>설정 테스트

1. 규칙을 저장한 후에는 사용자 지정 도메인에 대한 액세스가 요청될 때마다 CAPTCHA를 완료해야 합니다.

   ![Cloudflare WAF 적용 CAPTCHA를 보여 주는 이미지](./media/partner-cloudflare/enforce-captcha.png)

   > [!NOTE]
   > Cloudflare는 사용자 지정된 블록 페이지를 만드는 다양한 [**다른 옵션**](https://support.cloudflare.com/hc/en-us/articles/200172706-Configuring-Custom-Pages-Error-and-Challenge-)도 제공합니다.  

2. 사용자가 CAPTCHA를 성공적으로 완료한 후 Azure AD B2C 정책으로 이동합니다.

   ![Azure AD B2C 정책 로그인을 보여 주는 이미지](./media/partner-cloudflare/azure-ad-b2c-policy.png)


## <a name="additional-resources"></a>추가 리소스

- [Cloudflare 사용자 지정 페이지 문제 해결](https://support.cloudflare.com/hc/en-us/articles/200172706-Configuring-Custom-Pages-Error-and-Challenge-#5QWV2KVjLnaAQ8L4tjiguw)

- [Azure AD B2C의 사용자 지정 정책 시작하기](./tutorial-create-user-flows.md?pivots=b2c-custom-policy&tabs=applications)

## <a name="next-steps"></a>다음 단계 
 
- [Azure AD B2C에서 사용자 지정 도메인 구성](./custom-domain.md?pivots=b2c-user-flow)
