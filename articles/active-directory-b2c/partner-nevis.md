---
title: 네비스를 사용 하 여 Azure Active Directory B2C를 구성 하는 자습서
titleSuffix: Azure AD B2C
description: 암호 없는 인증을 위해 네비스와 Azure AD B2C 인증을 통합 하는 방법을 알아봅니다.
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/23/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 19aa847ce7ce1a6ba727a4733aefcdfad845e8c6
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/17/2020
ms.locfileid: "97629394"
---
# <a name="tutorial-to-configure-nevis-with-azure-active-directory-b2c-for-passwordless-authentication"></a>암호 없는 인증을 위해 Azure Active Directory B2C를 구성 하는 자습서

이 샘플 자습서에서는 암호 없는 인증을 사용 하기 위해  [네비스](https://www.nevis.net/solution/authentication-cloud) 와 Azure AD B2C를 확장 하는 방법을 알아봅니다. 네비스는 강력한 고객 인증을 제공 하 고 PSD2 (결제 서비스 지시어 2) 트랜잭션 요구 사항을 준수 하기 위해 네비스 Access 앱과의 모바일 우선, 완전 한 브랜드 최종 사용자 환경을 제공 합니다.

## <a name="prerequisites"></a>필수 구성 요소

시작 하려면 다음이 필요 합니다.

- 네비스 [평가판 계정](https://www.nevis-security.com/aadb2c/)

- Azure AD 구독 계정이 없는 경우 [무료 계정을](https://azure.microsoft.com/free/)받으세요.

- Azure 구독에 연결 된 [Azure AD B2C 테 넌 트](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant) 입니다.

- 등록 정책 흐름에 네비스를 통합 하려는 경우 [사용자 지정 정책을](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started)사용 하도록 Azure AD B2C 환경을 구성 합니다.

## <a name="scenario-description"></a>시나리오 설명

이 시나리오에서는 암호 없는 인증을 위해 백 엔드 응용 프로그램에 완전히 브랜드 액세스 앱을 추가 합니다. 솔루션을 구성 하는 구성 요소는 다음과 같습니다.

- 백 엔드에 대 한 결합 된 로그인 및 등록 정책을 포함 하는 Azure AD B2C 테 넌 트
- 네비스 인스턴스 및 REST API Azure AD B2C 향상
- 사용자의 브랜드 액세스 앱

다이어그램은 구현을 보여 줍니다.

![Azure AD B2C 및 네비스를 사용 하는 상위 수준의 암호 로그인 흐름](./media/partner-nevis/nevis-architecture-diagram.png)

|단계 | 설명 |
|:-----| :-----------|
| 1. | 사용자가 로그인 하 고 Azure AD B2C 로그인 정책을 통해 응용 프로그램에 로그인 하거나 로그인을 시도 합니다.
| 2. | 등록 하는 동안 네비스 Access 앱은 QR 코드를 사용 하 여 사용자 장치에 등록 됩니다. 사용자 장치에 개인 키가 생성 되 고 사용자 요청에 서명 하는 데 사용 됩니다.
| 3. |  Azure AD B2C RESTful 기술 프로필을 사용 하 여 네비스 솔루션으로 로그인을 시작 합니다.
| 4. | 로그인 요청은 푸시 메시지, QR 코드 또는 딥 링크로 access 앱에 전송 됩니다.
| 5. | 사용자가 해당 생체 인식을 사용 하 여 로그인 시도를 승인 합니다. 그러면 메시지는 저장 된 공개 키를 사용 하 여 로그인을 확인 하는 네비스로 반환 됩니다.
| 6. | Azure AD B2C는 1 개의 마지막 요청을 네비스로 보내 로그인이 성공적으로 완료 되었음을 확인 합니다.
| 7. |Azure AD B2C 사용자의 성공/실패 메시지에 따라 응용 프로그램에 대 한 액세스 권한이 부여 되거나 거부 됩니다.

## <a name="integrate-your-azure-ad-b2c-tenant"></a>Azure AD B2C 테 넌 트 통합

### <a name="onboard-to-nevis"></a>네비스에 등록 

[네비스 계정에 등록](https://www.nevis-security.com/aadb2c/)합니다.
두 개의 전자 메일을 받게 됩니다.

1. 관리 계정 알림

2. 모바일 앱 초대.

### <a name="add-your-azure-ad-b2c-tenant-to-your-nevis-account"></a>Azure AD B2C 테 넌 트를 네비스 계정에 추가 합니다.

1. 네비스 management 계정 평가판 전자 메일에서 관리 키를 클립보드에 복사 합니다.

2. https://console.nevis.cloud/브라우저에서를 엽니다.

3. 키를 사용 하 여 관리 콘솔에 로그인 합니다.

4. **인스턴스 추가** 선택

5. 새로 만든 인스턴스를 선택 하 여 엽니다.

6. 측면 탐색 모음에서 **사용자 지정 통합** 을 선택 합니다.

7. **사용자 지정 통합 추가** 를 선택 합니다.

8. 통합 이름에 Azure AD B2C 테 넌 트 이름을 입력 합니다.

9. URL/도메인에 대해 다음을 입력 합니다. `https://yourtenant.onmicrosoft.com`

10. **다음** 을 선택합니다.

>[!NOTE]
>나중에 네비스 액세스 토큰이 필요 합니다.

11. **완료** 를 선택합니다.

### <a name="install-the-nevis-access-app-on-your-phone"></a>휴대폰에 네비스 Access 앱 설치

1. 네비스 모바일 앱 평가판 전자 메일에서 **테스트 비행 앱** 초대를 엽니다.

2. 앱을 설치 합니다.

3. 제공 된 지침에 따라 네비스 Access 앱을 설치 합니다.

### <a name="integrate-azure-ad-b2c-with-nevis"></a>네비스와 Azure AD B2C 통합

1. [Azure Portal](https://portal.azure.com/)을 엽니다.

2. Azure AD B2C 테 넌 트로 전환 합니다. Azure AD B2C 테 넌 트가 일반적으로 별도의 테 넌 트에 있기 때문에 올바른 테 넌 트를 선택 했는지 확인 합니다.

3. 메뉴에서 **IEF (Id 경험 프레임 워크)** 를 선택 합니다.

4. **정책 키** 선택

5. **추가** 를 선택 하 고 다음 설정을 사용 하 여 새 키를 만듭니다.

      a. 옵션에서 **수동** 을 선택 합니다.

      b. 이름을 **AuthCloudAccessToken** 로 설정 합니다.

      c. 이전에 저장 된 **네비스 액세스 토큰** 을 비밀 필드에 붙여넣습니다.

      d. 키 사용에 대해 **암호화** 를 선택 합니다.

      e. **만들기** 를 선택합니다.

### <a name="configure-and-upload-the-nevishtml-to-azure-blob-storage"></a>nevis.html을 구성 하 고 Azure blob storage에 업로드 합니다.

1. Id 환경 (IDE)에서 [**정책**](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Nevis/policy) 폴더로 이동 합니다.

2. [**nevis.html**](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/Nevis/policy/nevis.html) 파일을 엽니다.

3. **Authentication_cloud_url** 를 네비스 Admin CONSOLE의 url로 바꿉니다 `https://<instance_id>.mauth.nevis.cloud` .

4. 변경 내용을 파일에 **저장** 합니다.

5. [지침](https://docs.microsoft.com/azure/active-directory-b2c/customize-ui-with-html#2-create-an-azure-blob-storage-account) 에 따라 **nevis.html** 파일을 Azure blob 저장소에 업로드 합니다.

6. [지침](https://docs.microsoft.com/azure/active-directory-b2c/customize-ui-with-html#3-configure-cors) 에 따라이 파일에 대 한 CORS (크로스-원본 자원 공유)를 사용 하도록 설정 합니다.

7. 업로드가 완료 되 고 CORS가 사용 하도록 설정 되 면 목록에서 **nevis.html** 파일을 선택 합니다.

8. **개요** 탭에서 **URL** 옆에 있는 **링크 복사** 아이콘을 선택 합니다.

9. 새 브라우저 탭에서 링크를 열어 회색 상자가 표시 되는지 확인 합니다.

>[!NOTE]
>Blob 링크는 나중에 필요 합니다.

### <a name="customize-your-trustframeworkbasexml"></a>TrustFrameworkBase.xml 사용자 지정

1. IDE에서 [**정책**](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Nevis/policy) 폴더로 이동 합니다.

2. [**TrustFrameworkBase.xml**](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/Nevis/policy/TrustFrameworkBase.xml) 파일을 엽니다.

3. 해당 **테 넌 트** 를 **TenantId** 의 Azure 테 넌 트 계정 이름으로 바꿉니다.

4. 해당 **테 넌 트** 를 **publicpolicyuri** 의 Azure 테 넌 트 계정 이름으로 바꿉니다.

5. 모든 **authentication_cloud_url** 인스턴스를 네비스 Admin CONSOLE의 url로 바꿉니다.

6. 변경 내용을 파일에 **저장** 합니다.

### <a name="customize-your-trustframeworkextensionsxml"></a>TrustFrameworkExtensions.xml 사용자 지정

1. IDE에서 [**정책**](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Nevis/policy) 폴더로 이동 합니다.

2. [**TrustFrameworkExtensions.xml**](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/Nevis/policy/TrustFrameworkExtensions.xml) 파일을 엽니다.

3. 해당 **테 넌 트** 를 **TenantId** 의 Azure 테 넌 트 계정 이름으로 바꿉니다.

4. 해당 **테 넌 트** 를 **publicpolicyuri** 의 Azure 테 넌 트 계정 이름으로 바꿉니다.

5. **Basepolicy** 의 **TenantId** 에서 해당 _테 넌 트_ 를 Azure 테 넌 트 계정 이름으로 바꿉니다.

6. **BuildingBlocks** 아래에서 **loaduri** 를 blob storage 계정에 있는 _nevis.html_ 의 blob 링크 URL로 바꿉니다.

7. 파일을 **저장** 합니다.

### <a name="customize-your-signuporsigninxml"></a>SignUpOrSignin.xml 사용자 지정

1. IDE에서 [**정책**](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Nevis/policy) 폴더로 이동 합니다.

2. [**SignUpOrSignin.xml**](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/Nevis/policy/SignUpOrSignin.xml) 파일을 엽니다.

3. 해당 **테 넌 트** 를 **TenantId** 의 Azure 테 넌 트 계정 이름으로 바꿉니다.

4. 해당 **테 넌 트** 를 **publicpolicyuri** 의 Azure 테 넌 트 계정 이름으로 바꿉니다.

5. 또한 **TenantId** 의 **basepolicy** 에서 해당 **테 넌 트** 를 Azure 테 넌 트 계정 이름으로 바꿉니다.

6. 파일을 **저장** 합니다.

### <a name="upload-your-custom-policies-to-azure-ad-b2c"></a>Azure AD B2C에 사용자 지정 정책 업로드

1. [Azure AD B2C 테 넌 트](https://portal.azure.com/#blade/Microsoft_AAD_B2CAdmin/TenantManagementMenuBlade/overview) 홈을 엽니다.

2. **ID 경험 프레임워크** 를 선택합니다.

3. **사용자 지정 정책 업로드** 를 선택합니다.

4. 수정한 **TrustFrameworkBase.xml** 파일을 선택 합니다.

5. **이미 있는 경우 사용자 지정 정책 덮어쓰기** 확인란을 선택 합니다.
6. **업로드** 를 선택합니다.

7. **TrustFrameworkExtensions.xml** 에 대해 5 단계와 6 단계를 반복 합니다.

8. **SignUpOrSignin.xml** 에 대해 5 단계와 6 단계를 반복 합니다.

## <a name="test-the-user-flow"></a>사용자 흐름 테스트

### <a name="test-account-creation-and-nevis-access-app-setup"></a>계정 만들기 및 네비스 액세스 앱 설정 테스트

1. [Azure AD B2C 테 넌 트](https://portal.azure.com/#blade/Microsoft_AAD_B2CAdmin/TenantManagementMenuBlade/overview) 홈을 엽니다.

2. **ID 경험 프레임워크** 를 선택합니다.

3. 사용자 지정 정책으로 스크롤하고 **B2C_1A_signup_signin** 를 선택 합니다.

4. **지금 실행** 을 선택합니다.

5. 팝업 창에서 **지금 등록** 을 선택 합니다.

6. 전자 메일 주소를 추가 합니다.

7. **확인 코드 보내기** 를 선택 합니다.

8. 전자 메일에서 확인 코드를 복사 합니다.

9. **확인** 을 선택합니다.

10. 양식에 새 암호 및 표시 이름을 입력 합니다.

11. **만들기** 를 선택합니다.

12. QR 코드 검색 페이지로 이동 합니다.

13. 휴대폰에서 **네비스 Access 앱** 을 엽니다.

14. **얼굴 ID** 를 선택 합니다.

15. **인증자 등록에 성공 했다는** 메시지가 표시 되 면 **계속** 을 선택 합니다.

16. 휴대폰에서 얼굴을 다시 인증 합니다.

17. 디코딩된 토큰 세부 정보를 표시 하는 [jwt.ms](https://jwt.ms) 방문 페이지로 이동 합니다.

### <a name="test-the-pure-passwordless-sign-in"></a>순수한 암호 없는 로그인 테스트

1. **Id 경험 프레임 워크** 에서 **B2C_1A_signup_signin** 를 선택 합니다.

2. **지금 실행** 을 선택합니다.

3. 팝업 창에서 **Passwordless Authentication** 을 선택 합니다.

4. 전자 메일 주소를 입력합니다.

5. **계속** 을 선택합니다.

6. 휴대폰의 알림에서 **네비스 Access 앱 알림** 을 선택 합니다.

7. 얼굴을 사용 하 여 인증 합니다.

8. 토큰을 표시 하는 [jwt.ms](https://jwt.ms) 방문 페이지로 자동으로 이동 됩니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 문서를 검토 하세요.

- [Azure AD B2C의 사용자 지정 정책](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-overview)

- [Azure AD B2C에서 사용자 지정 정책 시작](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications)
