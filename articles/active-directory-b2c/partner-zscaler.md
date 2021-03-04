---
title: 자습서-Zscaler를 사용 하 여 Azure Active Directory B2C 구성
titleSuffix: Azure AD B2C
description: Zscaler를 사용 하 여 Azure AD B2C 인증을 통합 하는 방법에 대해 알아봅니다.
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/09/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: dbeb3cd4fccf80f434e6c7ac08c658632f64b135
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102096856"
---
# <a name="tutorial-configure-zscaler-private-access-with-azure-active-directory-b2c"></a>자습서: Azure Active Directory B2C을 사용 하 여 Zscaler Private 액세스 구성

이 자습서에서는 [ZPA (Zscaler Private Access](https://www.zscaler.com/products/zscaler-private-access))와 Azure AD B2C (Azure Active Directory B2C) 인증을 통합 하는 방법에 대해 알아봅니다. ZPA는 VPN (가상 사설망)의 비용, 부담이 나 보안 위험 없이 개인 응용 프로그램 및 자산에 대 한 보안 액세스를 정책에 따라 제공 합니다. Zscaler 보안 하이브리드 액세스 기능을 사용 하면 소비자 지향 응용 프로그램이 Azure AD B2C와 결합 될 때 공격에 노출 될 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

시작 하기 전에 다음이 필요 합니다.

- Azure 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.  
- Azure 구독에 연결 된 [Azure AD B2C 테 넌 트](./tutorial-create-tenant.md) 입니다.  
- [ZPA 구독](https://azuremarketplace.microsoft.com/marketplace/apps/aad.zscalerprivateaccess?tab=Overview).

## <a name="scenario-description"></a>시나리오 설명

ZPA 통합에는 다음 구성 요소가 포함 됩니다.

- **Azure AD B2C**: 사용자의 자격 증명을 확인 하는 IdP (id 공급자)입니다. 또한 새 사용자를 등록 해야 합니다.  
- **Zpa**: [0 신뢰 액세스](https://www.microsoft.com/security/blog/2018/12/17/zero-trust-part-1-identity-and-access-management/#:~:text=Azure%20Active%20Directory%20%28Azure%20AD%29%20provides%20the%20strong%2C,to%20express%20their%20access%20requirements%20in%20simple%20terms.)를 적용 하 여 웹 응용 프로그램 보안을 담당 하는 서비스입니다.  
- **웹 응용 프로그램**: 사용자가 액세스 하려고 하는 서비스를 호스팅합니다.

다음 다이어그램에서는 ZPA를 Azure AD B2C와 통합 하는 방법을 보여 줍니다.

![ZPA를 Azure AD B2C와 통합 하는 방법을 보여 주는 Zscaler 아키텍처의 다이어그램입니다.](media/partner-zscaler/zscaler-architecture-diagram.png)

시퀀스는 다음 표에 설명 되어 있습니다.

|단계 | 설명 |
| :-----:| :-----------|
| 1 | 사용자가 ZPA 사용자 포털 또는 ZPA 브라우저 액세스 응용 프로그램에 도착 합니다.
| 2 | 사용자가 웹 응용 프로그램에 액세스할 수 있도록 허용할지 여부를 결정 하려면 ZPA에 사용자 컨텍스트 정보가 필요 합니다. 사용자를 인증 하기 위해 ZPA는 Azure AD B2C 로그인 페이지에 대 한 SAML 리디렉션을 수행 합니다.  
| 3 | 사용자가 Azure AB B2C 로그인 페이지에 도착 합니다. 새 사용자가 계정을 만들기 위해 등록 하 고 기존 사용자가 기존 자격 증명을 사용 하 여 로그인 합니다. Azure AD B2C은 사용자 id의 유효성을 검사 합니다.
| 4 | 인증에 성공 하면 Azure AD B2C는 SAML 어설션을 사용 하 여 사용자를 ZPA로 다시 리디렉션합니다. ZPA는 SAML 어설션을 확인 하 고 사용자 컨텍스트를 설정 합니다.
| 5 | ZPA는 사용자에 대 한 액세스 정책을 평가 합니다. 사용자가 웹 응용 프로그램에 액세스할 수 있는 경우에는 연결을 통과할 수 있습니다.

## <a name="onboard-to-zpa"></a>ZPA에 등록

이 자습서에서는 이미 작동 하는 ZPA 설정이 있다고 가정 합니다. ZPA를 시작 하는 경우 [zpa에 대 한 단계별 구성 가이드](https://help.zscaler.com/zpa/step-step-configuration-guide-zpa)를 참조 하세요.

## <a name="integrate-zpa-with-azure-ad-b2c"></a>Azure AD B2C와 ZPA 통합

### <a name="step-1-configure-azure-ad-b2c-as-an-idp-on-zpa"></a>1 단계: ZPA에서 IdP로 Azure AD B2C 구성

[ZPA에서](https://help.zscaler.com/zpa/configuring-idp-single-sign)Azure AD B2C를 IdP 구성 하려면 다음을 수행 합니다.

1. [Zpa 관리 포털](https://admin.private.zscaler.com)에 로그인 합니다.

1. **관리**  >  **IdP 구성** 으로 이동 합니다.

1. **IdP 구성 추가** 를 선택 합니다.

   **IdP 구성 추가** 창이 열립니다.

   !["IdP 구성 추가" 창에서 "IdP 정보" 탭의 스크린샷](media/partner-zscaler/add-idp-configuration.png)

1. **IdP 정보** 탭을 선택 하 고 다음을 수행 합니다.

   a. **이름** 상자에 **Azure AD B2C** 을 입력 합니다.  
   b. **Single sign-on** 에서 **사용자** 를 선택 합니다.  
   다. **도메인** 드롭다운 목록에서이 IdP 연결 하려는 인증 도메인을 선택 합니다.

1. **다음** 을 선택합니다.

1. **SP 메타 데이터** 탭을 선택 하 고 다음을 수행 합니다.

   a. **서비스 공급자 URL** 에서 나중에 사용할 값을 복사 하거나 적어 둡니다.  
   b. **서비스 공급자 엔터티 ID** 에서 나중에 사용할 값을 복사 하거나 적어 둡니다.

   !["IdP 구성 추가" 창에서 "SP 메타 데이터" 탭의 스크린샷](media/partner-zscaler/sp-metadata.png)

1. **일시 중지** 를 선택 합니다.

Azure AD B2C 구성한 후에는 나머지 IdP 구성이 다시 시작 됩니다.

### <a name="step-2-configure-custom-policies-in-azure-ad-b2c"></a>2 단계: Azure AD B2C에서 사용자 지정 정책 구성

>[!Note]
>이 단계는 사용자 지정 정책을 아직 구성 하지 않은 경우에만 필요 합니다. 사용자 지정 정책이 이미 하나 이상 있는 경우이 단계를 건너뛸 수 있습니다.

Azure AD B2C 테 넌 트에서 사용자 지정 정책을 구성 하려면 [Azure Active Directory B2C에서 사용자 지정 정책 시작](./custom-policy-get-started.md)을 참조 하세요.

### <a name="step-3-register-zpa-as-a-saml-application-in-azure-ad-b2c"></a>3 단계: Azure AD B2C에서 SAML 응용 프로그램으로 ZPA 등록

Azure AD B2C에서 SAML 응용 프로그램을 구성 하려면 [Azure AD B2C에 saml 응용 프로그램 등록](./saml-service-provider.md)을 참조 하세요. 

["정책 업로드"](./saml-service-provider.md#upload-your-policy)단계에서 Azure AD B2C에 사용 되는 IdP SAML 메타 데이터 URL을 복사 하거나 적어 둡니다. 나중에 필요합니다.

["Azure AD B2C에서 응용 프로그램 구성"](./saml-service-provider.md#configure-your-application-in-azure-ad-b2c)단계를 수행 하 여 지침을 따르세요. 4.2 단계에서 다음과 같이 응용 프로그램 매니페스트 속성을 업데이트 합니다.

- **IdentifierUris**: 이전에 "1.6. b 단계"에서 복사한 서비스 공급자 엔터티 ID를 사용 합니다.  
- **SamlMetadataUrl**: ZPA가 SAML 메타 데이터 URL을 호스팅하지 않으므로이 속성을 건너뜁니다.  
- **ReplyUrlsWithType**: 이전에 "1.6. a 단계"에서 복사한 서비스 공급자 URL을 사용 합니다.  
- **Logouturl**: zpa가 로그 아웃 URL을 지원 하지 않으므로이 속성을 건너뜁니다.

나머지 단계는이 자습서와 관련이 없습니다.

### <a name="step-4-extract-the-idp-saml-metadata-from-azure-ad-b2c"></a>4 단계: Azure AD B2C에서 IdP SAML 메타 데이터 추출

다음으로, 다음 형식으로 SAML 메타 데이터 URL을 가져와야 합니다.

```https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/<policy-name>/Samlp/metadata```

는 `<tenant-name>` Azure AD B2C 테 넌 트의 이름이 고 `<policy-name>` 은 이전 단계에서 만든 사용자 지정 SAML 정책의 이름입니다.

예를 들어 URL은 `https://safemarch.b2clogin.com/safemarch.onmicrosoft.com/B2C_1A_signup_signin_saml//Samlp/metadata`이 될 수 있습니다.

웹 브라우저를 열고 SAML 메타 데이터 URL로 이동 합니다. 페이지의 아무 곳 이나 마우스 오른쪽 단추로 클릭 하 고 다른 **이름으로 저장** 을 선택한 후 다음 단계에서 사용할 수 있도록 파일을 컴퓨터에 저장 합니다.

### <a name="step-5-complete-the-idp-configuration-on-zpa"></a>5 단계: ZPA에서 IdP 구성 완료

이전에 "1 단계: configuration Azure AD B2C as a IdP on ZPA"에서 부분적으로 구성한 [ZPA Admin 포털의 IdP 구성을](https://help.zscaler.com/zpa/configuring-idp-single-sign) 완료 합니다.

1. [Zpa 관리 포털](https://admin.private.zscaler.com)에서 **관리**  >  **IdP 구성** 으로 이동 합니다.

1. "1 단계"에서 구성한 IdP를 선택한 다음 **다시 시작** 을 선택 합니다.

1. **IdP 구성 추가** 창에서 **IdP 만들기** 탭을 선택 하 고 다음을 수행 합니다.

   a. **IdP 메타 데이터 파일** 아래에서 "4 단계: Azure AD B2C에서 IdP SAML 메타 데이터 추출"에서 이전에 저장 한 메타 데이터 파일을 업로드 합니다.  
   b. IdP 구성에 대 한 **상태** 를 **사용할 수** 있는지 확인 합니다.  
   c. **저장** 을 선택합니다.

   !["IdP 구성 추가" 창에서 "IdP 만들기" 탭의 스크린샷](media/partner-zscaler/create-idp.png)

## <a name="test-the-solution"></a>솔루션 테스트

ZPA 사용자 포털 또는 브라우저 액세스 응용 프로그램으로 이동 하 여 등록 또는 로그인 프로세스를 테스트 합니다. 테스트로 인해 SAML 인증이 성공적으로 수행 되어야 합니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 문서를 검토 하세요.

- [Azure AD B2C에서 사용자 지정 정책 시작](./custom-policy-get-started.md)
- [Azure AD B2C에 SAML 애플리케이션 등록](./saml-service-provider.md)
- [ZPA에 대 한 단계별 구성 가이드](https://help.zscaler.com/zpa/step-step-configuration-guide-zpa)
- [Single Sign-On에 대 한 IdP 구성](https://help.zscaler.com/zpa/configuring-idp-single-sign)