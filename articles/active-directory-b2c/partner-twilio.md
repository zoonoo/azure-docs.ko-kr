---
title: Azure Active Directory B2C를 사용 하 여 Twilio Verify 앱 확인
titleSuffix: Azure AD B2C
description: Azure AD B2C에서 샘플 온라인 지불 앱을 Twilio Verify API와 통합 하는 방법에 대해 알아봅니다. 동적 링크 및 강력한 고객 인증을 통해 PSD2 (결제 서비스 지시어 2) 트랜잭션 요구 사항을 준수 합니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 06/08/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 953653a758577ed3d48ca2d81403b4cb363ea294
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91259071"
---
# <a name="integrating-twilio-verify-app-with-azure-active-directory-b2c"></a>Azure Active Directory B2C Twilio Verify 앱 통합

이 연습에서는 Azure Active Directory B2C (Azure AD B2C)의 샘플 온라인 지불 앱을 Twilio Verify API와 통합 하는 방법에 대해 알아봅니다. Twilio Verify 앱 Azure AD B2C을 사용 하면 고객이 동적 링크 및 강력한 고객 인증을 통해 PSD2 (결제 서비스 지시어 2) 트랜잭션 요구 사항을 준수할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

시작 하려면 다음이 필요 합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* Azure 구독에 연결 된 [Azure AD B2C 테 넌 트](tutorial-create-tenant.md) 입니다.
* Twilio의 [평가판 계정](https://www.twilio.com/try-twilio)

## <a name="scenario-description"></a>시나리오 설명

Twilio 솔루션을 구성 하는 구성 요소는 다음과 같습니다.

- 로그인 또는 등록 하는 기능을 제공 하는 .NET [PSD2 demo 웹 앱](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Twilio-VerifyAPI/source-code/PSD2%20Demo%20App)이며 더미 높은 위험 트랜잭션을 수행 합니다.
- Azure AD B2C 결합 된 [로그인 및 등록 정책](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Twilio-VerifyAPI/policy)입니다.
- 을 사용 하 여 Twilio Verify API와 통합 된 Azure AD B2C 정책 `id_token_hint` 입니다.
- `.well-known`의 유효성 검사를 허용 하기 위해 openidconnect 끝점을 호스트 하는 .Net 웹 앱 `id_token_hint` 입니다.


    ![twilio flow](media/partner-twilio/twilio-flow.png)

| 단계 | 설명 |
|------|------|
| 1     | 사용자가 PSD2 Demo 앱에 로그인 또는 등록을 시작 합니다. 사용자는 Azure AD B2C 결합 된 로그인 및 등록 정책을 통해 인증 됩니다. 응용 프로그램에 토큰이 반환 됩니다. 등록 시 사용자의 전화 번호는 SMS/Phone을 사용 하 여 확인 되 고 Azure AD B2C 계정에 기록 됩니다.     |
| 2     | 사용자가 $50.00의 전송과 같은 높은 위험 수준 트랜잭션을 시작 합니다. 사용자의 현재 액세스 토큰은 PolicyId를 평가 하 여 사용자가 Step-Up 사용자 지정 정책을 통해 이미 인증 되었는지 여부를 확인 합니다.     |
| 3     | 응용 프로그램은 트랜잭션 값과 수취인, $50.00 및 John Doe를 기록 하 고 서명 된 토큰을 생성 합니다. 이 토큰을 라고 `id_token_hint` 하며 클레임을 포함 `amount:$500, payee:john doe` 합니다. 는 `id_token_hint` Twilio와 통합 된 Azure AD B2C 사용자 지정 정책에 대 한 요청과 함께 전송 됩니다.     |
| 4     | Azure AD B2C는 응용 프로그램 openid connect Connect 끝점을 확인 하 여 id_token_hint의 서명을 확인 합니다 `/.well-known` . 확인 후, 특히 및와 같은 토큰에서 클레임을 추출 합니다 `amount` `payee` . 사용자에 게 SMS 메시지를 통해 휴대폰 번호를 확인 하는 페이지가 표시 됩니다.     |
| 5     | 사용자가 SMS 메시지를 통해 전화 번호를 확인 하도록 요청 하 고 Azure AD B2C Twilio의 Verify API 끝점에 대 한 REST API 요청을 만듭니다. 또한 `amount` `payee` PSD2 프로세스의 일부로 트랜잭션을 전송 하 여 OTP (일회용 암호)를 생성 합니다. Twilio는 사용자의 등록 된 전화 번호로 SMS 메시지를 보냅니다.     |
| 6     |  사용자가 SMS 메시지에서 받은 OTP를 입력 하 고 Azure AD B2C으로 전송 합니다. Azure AD B2C는 OTP가 올바른지 확인 하기 위해이 OTP를 사용 하 여 API 요청을 Twilio의 verify API로 만듭니다. 마지막으로 응용 프로그램에 토큰이 발급 되 고, 사용자가 인증을 단계별로 실행 하 고 있음을 나타내는 새 PolicyId가 사용 됩니다.    |
|      |      |

## <a name="onboard-with-twilio"></a>Twilio으로 등록

1. Twilio에서 [평가판 계정을](https://www.twilio.com/try-twilio) 얻습니다.

2. [이 문서](https://support.twilio.com/hc/articles/223135247-How-to-Search-for-and-Buy-a-Twilio-Phone-Number-from-Console) 에 설명 된 대로 Twilio에서 전화 번호를 구매 합니다.

3. Twilio 콘솔에서 [API 확인](https://www.twilio.com/console/verify/services) 으로 이동 하 고 [지침](https://www.twilio.com/docs/verify/verifying-transactions-psd2) 에 따라 서비스를 만들고 PSD2 옵션을 사용 하도록 설정 합니다.  

## <a name="configure-the-psd2-demo-app"></a>PSD2 Demo 앱 구성

1. B2C-WebAPI 솔루션을 열고 다음 값을 web.config의 테 넌 트 별 값으로 바꿉니다.

    ```xml
   <add key="ida:Tenant" value="yourtenant.onmicrosoft.com" />
   <add key="ida:TenantId" value="d6f33888-0000-4c1f-9b50-1590f171fc70" />
   <add key="ida:ClientId" value="6bd98cc8-0000-446a-a05e-b5716ef2651b" />
   <add key="ida:ClientSecret" value="secret" />
   <add key="ida:AadInstance" value="https://yourtenant.b2clogin.com/tfp/{0}/{1}" />
   <add key="ida:RedirectUri" value="https://your hosted psd2 demo app url/" />
   ```

2. 또한 [웹 앱](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Twilio-VerifyAPI/source-code/PSD2%20Demo%20App) 은 ID 토큰 힌트 생성기 및 메타 데이터 끝점을 호스팅합니다.
   - 이 [샘플 설명](https://github.com/azure-ad-b2c/samples/tree/master/policies/invite#creating-a-signing-certificate)에 설명 된 대로 서명 인증서를 만듭니다.
   - web.config에서 인증서를 기준으로 다음 줄을 업데이트 합니다.
   
   ```xml
   <add key="ida:SigningCertThumbprint" value="4F39D6014818082CBB763E5BA5F230E545212E89" />
   <add key="ida:SigningCertAlgorithm" value="RS256" />
   ```

3. 선택한 호스팅 공급자에 데모 응용 프로그램을 업로드 합니다. 인증서를 업로드 하는 지침을 포함 하 여 [이 샘플 설명](https://github.com/azure-ad-b2c/samples/tree/master/policies/invite#hosting-the-application-in-azure-app-service)에 Azure App Service에 대 한 지침이 제공 됩니다.

4. 응용 프로그램이 호스팅되는 URL에 해당 하는 회신 URL을 추가 하 여 Azure AD B2C 응용 프로그램 등록을 업데이트 합니다.

5. [정책 파일](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Twilio-VerifyAPI/policy) 을 열고의 모든 인스턴스를  `contoso` 테 넌 트 이름으로 바꿉니다.

6. Twilio REST API 기술 프로필 **사용자 지정-SMS-등록**을 찾습니다.  `ServiceURL`   Twilio AccountSID 및 From 번호를 구매한 전화 번호로 업데이트 합니다.

7. Twilio REST API 기술 프로필, TwilioRestAPI- **1 단계**   및 **TwilioRestAPI-2 단계**를 찾고  `ServiceURL` Twilio AccountSID를 사용 하 여를 업데이트 합니다   .

## <a name="integrate-with-azure-ad-b2c"></a>Azure AD B2C와 통합

Azure AD B2C에 정책 파일을 추가 합니다.

1. Azure AD B2C 테넌트의 전역 관리자로 [Azure Portal](https://portal.azure.com/)에 로그인합니다.

2. Azure AD B2C 테넌트를 포함하는 디렉터리를 사용하려면 위쪽 메뉴에서 **디렉터리 + 구독** 필터를 선택하고, 테넌트가 포함된 디렉터리를 선택합니다.

3. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스**를 선택하고 **Azure AD B2C**를 검색하여 선택합니다.

4. **Azure AD B2C**  >  **id 경험 프레임 워크**  >  **정책 키**로 이동 합니다.

5. 이름이 **B2cRestTwilioClientId**인 새 키를 추가 합니다.  **수동**을 선택 하 고 Twilio AccountSID의 값을 제공 합니다.

6. 이름이 **B2cRestTwilioClientSecret**인 새 키를 추가 합니다.  **수동**을 선택 하 고 TWILIO AUTH 토큰의 값을 제공 합니다.

7. 모든 정책 파일을 테 넌 트에 업로드 합니다.

8. 등록 SMS 텍스트에 대 한 GenerateOTPMessageEnrol 클레임 변환의 문자열을 사용자 지정 합니다.

## <a name="test-the-solution"></a>솔루션 테스트

* 응용 프로그램으로 이동 하 여 로그인, 등록 및 비용 보내기 작업을 테스트 합니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 문서를 참조 하세요.

- [Twilio 통합 코드 샘플](https://github.com/azure-ad-b2c/samples/tree/master/policies/twilio-mfa-psd2) 은 GitHub를 참조 하세요.  

- [AAD B2C의 사용자 지정 정책](custom-policy-overview.md)

- [AAD B2C에서 사용자 지정 정책 시작](custom-policy-get-started.md?tabs=applications)
