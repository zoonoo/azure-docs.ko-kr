---
title: Jumio를 사용하여 Azure Active Directory B2C를 구성하는 방법에 대한 자습서
titleSuffix: Azure AD B2C
description: 이 자습서에서는 자동화된 ID 확인을 위해 Jumio를 사용하여 Azure Active Directory B2C를 구성하고 고객 데이터를 보호합니다.
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/20/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 66ec0d4b09dc983eb898d63d45b3dd7cab291c4c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96928667"
---
# <a name="tutorial-for-configuring-jumio-with-azure-active-directory-b2c"></a>Azure Active Directory B2C를 사용하여 Jumio를 구성하기 위한 자습서

이 샘플 자습서에서는 Azure Active Directory B2C(Azure AD B2C)를 [Jumio](https://www.jumio.com/)와 통합하는 방법에 대한 참고 자료를 제공합니다. Jumio는 ID 확인 서비스이며, 이 서비스를 사용하면 실시간 자동화 ID를 확인하여 고객 데이터를 보호할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

시작하려면 다음이 필요합니다.

- Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.

- Azure 구독에 연결된 [Azure AD B2C 테넌트](./tutorial-create-tenant.md)입니다.

## <a name="scenario-description"></a>시나리오 설명

Jumio 통합에는 다음 구성 요소가 포함됩니다.

- Azure AD B2C: 사용자의 자격 증명 확인을 담당하는 권한 부여 서버입니다. ID 공급자라고도 합니다.

- Jumio: 사용자가 제공한 ID 세부 정보를 가져와서 확인하는 서비스입니다.

- 중간 Rest API: Azure AD B2C와 Jumio 서비스 간의 통합을 구현하는 API입니다.

- Azure Blob Storage: Azure AD B2C 정책에 사용자 지정 UI 파일을 제공하는 서비스입니다.

다음 아키텍처 다이어그램은 구현된 상황을 보여줍니다.

![Jumio과의 Azure AD B2C 통합 아키텍처 다이어그램.](./media/partner-jumio/jumio-architecture-diagram.png)

|단계 | 설명 |
|:-----| :-----------|
| 1. | 사용자가 로그인하거나 등록하여 계정을 만들기 위해 페이지에 도착합니다. Azure AD B2C가 사용자 특성을 수집합니다.
| 2. | Azure AD B2C는 중간 API를 호출하고 사용자 특성을 전달합니다.
| 3. | 중간 계층 API는 사용자 특성을 수집하고 Jumio API에서 사용할 수 있는 형식으로 변환합니다. 그런 다음 특성을 Jumio으로 보냅니다.
| 4. | Jumio는 정보를 사용하고 처리한 후 중간 계층 API에 결과를 반환합니다.
| 5. | 중간 계층 API는 정보를 처리하고 Azure AD B2C에 관련 정보를 다시 보냅니다.
| 6. | Azure AD B2C는 중간 계층 API에서 정보를 다시 받습니다. 오류 응답이 표시되면 사용자에게 오류 메시지가 표시됩니다. 성공 응답이 표시되면 사용자가 인증되고 디렉터리에 기록됩니다.

## <a name="sign-up-with-jumio"></a>Jumio에 가입

Jumio 계정을 만들려면 [Jumio](https://www.jumio.com/contact/)에 문의하세요.

## <a name="configure-azure-ad-b2c-with-jumio"></a>Jumio로 Azure AD B2C 구성

Jumio 계정을 만든 후에는 계정을 사용하여 Azure AD B2C를 구성합니다. 다음 섹션은 이 프로세스를 순서대로 설명합니다.

### <a name="deploy-the-api"></a>API 배포

제공된 [API 코드](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Jumio/API/Jumio.Api)를 Azure 서비스에 배포합니다. [다음 지침](/visualstudio/deployment/quickstart-deploy-to-azure)에 따라 Visual Studio에서 코드를 게시할 수 있습니다.

>[!NOTE]
>필요한 설정을 사용하여 Azure AD를 구성하려면 배포된 서비스의 URL이 필요합니다.

### <a name="deploy-the-client-certificate"></a>클라이언트 인증서 배포

1. 클라이언트 인증서는 Jumio API 호출을 보호하는 데 도움이 됩니다. 다음 PowerShell 샘플 코드를 사용하여 자체 서명된 인증서를 만듭니다.

   ``` PowerShell
   $cert = New-SelfSignedCertificate -Type Custom -Subject "CN=Demo-SigningCertificate" -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.3") -KeyUsage DigitalSignature -KeyAlgorithm RSA -KeyLength 2048 -NotAfter (Get-Date).AddYears(2) -CertStoreLocation "Cert:\CurrentUser\My"
   $cert.Thumbprint
   $pwdText = "Your password"
   $pwd = ConvertTo-SecureString -String $pwdText -Force -AsPlainText
   Export-PfxCertificate -Cert $Cert -FilePath "{your-local-path}\Demo-SigningCertificate.pfx" -Password $pwd.

   ```

   그런 다음 인증서를 ``{your-local-path}``에 지정된 위치로 내보냅니다.

3. [이 문서](../app-service/configure-ssl-certificate.md#upload-a-private-certificate)의 지침에 따라 Azure App Service 인증서를 가져옵니다.

### <a name="create-a-signingencryption-key"></a>서명/암호화 키 만들기

문자 및 숫자만 포함된 64자 보다 긴 임의의 문자열을 만듭니다.

예: ``C9CB44D98642A7062A0D39B94B6CDC1E54276F2E7CFFBF44288CEE73C08A8A65``

다음 샘플 PowerShell 스크립트를 사용하여 문자열을 만듭니다.

```PowerShell
-join ((0x30..0x39) + ( 0x41..0x5A) + ( 0x61..0x7A) + ( 65..90 ) | Get-Random -Count 64  | % {[char]$_})

```

### <a name="configure-the-api"></a>API를 구성합니다.

[Azure App Service에서 애플리케이션 설정을 구성](../app-service/configure-common.md#configure-app-settings)할 수 있습니다. 이 방법을 사용하면 설정을 리포지토리로 체크인하지 않고도 안전하게 구성할 수 있습니다. Rest API에 대한 다음 설정을 제공해야 합니다.

| 애플리케이션 설정 | 원본 | 참고 |
| :-------- | :------------| :-----------|
|JumioSettings:AuthUsername | Jumio 계정 구성 |     |
|JumioSettings: AuthPassword | Jumio 계정 구성 |     |
|AppSettings: SigningCertThumbprint|생성된 자체 서명된 인증서의 지문|  |
|AppSettings: IdTokenSigningKey| PowerShell을 사용하여 만든 서명 키 | |
| AppSettings:IdTokenEncryptionKey |PowerShell을 사용하여 만든 암호화 키
| AppSettings:IdTokenIssuer | JWT 토큰에 사용할 발급자(GUID 값이 선호됨) |
| AppSettings:IdTokenAudience  | JWT 토큰에 사용할 사용자 (GUID 값이 선호됨) |
|AppSettings:BaseRedirectUrl | Azure AD B2C 정책의 기준 URL | https://{your-tenant-name}.b2clogin.com/{your-application-id}|
| WEBSITE_LOAD_CERTIFICATES| 생성된 자체 서명된 인증서의 지문 |

### <a name="deploy-the-ui"></a>UI 배포

1. [스토리지 계정에 Blob Storage 컨테이너](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)를 설정합니다.

2. [Ui 폴더](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Jumio/UI) 의 UI 파일을 blob 컨테이너에 저장합니다.

#### <a name="update-ui-files"></a>UI 파일 업데이트

1. UI 파일에서 [ocean_blue](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Jumio/UI/ocean_blue)폴더로 이동합니다.

2. 각 HTML 파일을 엽니다.

3. 검색하여 `{your-ui-blob-container-url}`을 BLOB 컨테이너의 URL로 바꿉니다.

4. 검색하여 `{your-intermediate-api-url}`을 중간 API 앱 서비스의 URL로 바꿉니다.

>[!NOTE]
> 모범 사례로, 고객 특성 컬렉션 페이지에서 동의 알림을 추가하는 것이 좋습니다. 신원 확인을 위해 타사 서비스에 정보가 보내짐을 사용자에게 알립니다.

### <a name="configure-the-azure-ad-b2c-policy"></a>Azure AD B2C 정책 구성

1. 정책 폴더의 [Azure AD B2C 정책](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Jumio/Policies)으로 이동합니다.

2. [이 문서](./custom-policy-get-started.md?tabs=applications#custom-policy-starter-pack)에 따라 [LocalAccounts 시작 팩](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/LocalAccounts)을 다운로드합니다.

3. Azure AD B2C 테넌트에 대한 정책을 구성합니다.

>[!NOTE]
>특정 테넌트와 관련하여 제공된 정책을 업데이트합니다.

## <a name="test-the-user-flow"></a>사용자 흐름 테스트

1. Azure AD B2C 테넌트를 엽니다. **정책** 에서 **Identity Experience Framework** 를 선택합니다.

2. 이전에 만든 **SignUpSignIn** 을 선택합니다.

3. **사용자 흐름 실행** 을 선택하고 다음을 수행합니다.

   a. **애플리케이션** 의 경우, 등록된 앱을 선택합니다(JWT는 샘플).

   b. **회신 URL** 의 경우, **리디렉션 URL** 을 선택합니다.

   다. **사용자 흐름 실행** 을 선택합니다.

4. 등록 흐름을 진행하고 계정을 만듭니다.

5. 사용자 특성을 만든 뒤에 흐름 동안 Jumio 서비스가 호출됩니다. 흐름이 불완전한 경우 사용자가 디렉터리에 저장되지 않았는지 확인합니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 문서를 참조하세요:

- [Azure AD B2C의 사용자 지정 정책](./custom-policy-overview.md)

- [Azure AD B2C의 사용자 지정 정책 시작하기](./custom-policy-get-started.md?tabs=applications)
