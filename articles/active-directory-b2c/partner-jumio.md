---
title: Jumio를 사용 하 여 Azure Active Directory B2C를 구성 하는 자습서
titleSuffix: Azure AD B2C
description: 이 자습서에서는 자동화 된 ID 확인을 위해 Jumio를 사용 하 여 Azure Active Directory B2C를 구성 하 고 고객 데이터를 보호 합니다.
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/20/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: f3a8881b9fe44727caf07b3cc0d5ee19f0444e98
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2020
ms.locfileid: "94953664"
---
# <a name="tutorial-for-configuring-jumio-with-azure-active-directory-b2c"></a>Azure Active Directory B2C로 Jumio 구성에 대 한 자습서

이 샘플 자습서에서는 [Jumio](https://www.jumio.com/)와 Azure Active Directory B2C (Azure AD B2C)를 통합 하는 방법에 대 한 지침을 제공 합니다. Jumio는 고객 데이터를 보호 하는 데 도움이 되는 실시간 자동 ID 확인을 가능 하 게 하는 ID 확인 서비스입니다.

## <a name="prerequisites"></a>사전 요구 사항

시작 하려면 다음이 필요 합니다.

- Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.

- Azure 구독에 연결 된 [Azure AD B2C 테 넌 트](./tutorial-create-tenant.md) 입니다.

## <a name="scenario-description"></a>시나리오 설명

Jumio 통합에는 다음 구성 요소가 포함 됩니다.

- Azure AD B2C: 사용자의 자격 증명을 확인 해야 하는 권한 부여 서버입니다. Id 공급자 라고도 합니다.

- Jumio: 사용자가 제공한 ID 세부 정보를 가져와서 확인 하는 서비스입니다.

- 중간 Rest API: Azure AD B2C와 Jumio 서비스 간의 통합을 구현 하는 API입니다.

- Azure Blob storage: Azure AD B2C 정책에 사용자 지정 UI 파일을 제공 하는 서비스입니다.

다음 아키텍처 다이어그램에서는 구현을 보여 줍니다.

![Jumio와 Azure AD B2C 통합의 아키텍처 다이어그램](./media/partner-jumio/jumio-architecture-diagram.png)

|단계 | Description |
|:-----| :-----------|
| 1. | 사용자가 로그인 하거나 등록 하 여 계정을 만들기 위해 페이지에 도착 합니다. Azure AD B2C 사용자 특성을 수집 합니다.
| 2. | Azure AD B2C 중간 계층 API를 호출 하 고 사용자 특성을 전달 합니다.
| 3. | 중간 계층 API는 사용자 특성을 수집 하 고 Jumio API에서 사용할 수 있는 형식으로 변환 합니다. 그런 다음 특성을 Jumio으로 보냅니다.
| 4. | Jumio는 정보를 사용 하 고 처리 한 후 중간 계층 API에 결과를 반환 합니다.
| 5. | 중간 계층 API는 정보를 처리 하 고 Azure AD B2C에 관련 정보를 다시 보냅니다.
| 6. | Azure AD B2C는 중간 계층 API에서 정보를 다시 받습니다. 오류 응답이 표시 되 면 사용자에 게 오류 메시지가 표시 됩니다. 성공 응답이 표시 되 면 사용자가 인증 되 고 디렉터리에 기록 됩니다.

## <a name="sign-up-with-jumio"></a>Jumio를 사용 하 여 등록

Jumio 계정을 만들려면 [Jumio](https://www.jumio.com/contact/)에 문의 하세요.

## <a name="configure-azure-ad-b2c-with-jumio"></a>Jumio를 사용 하 여 Azure AD B2C 구성

Jumio 계정을 만든 후에는 계정을 사용 하 여 Azure AD B2C를 구성 합니다. 다음 섹션에서는이 프로세스를 순서 대로 설명 합니다.

### <a name="deploy-the-api"></a>API 배포

제공 된 [API 코드](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Jumio/API/Jumio.Api) 를 Azure 서비스에 배포 합니다. 다음 [지침](/visualstudio/deployment/quickstart-deploy-to-azure?view=vs-2019)에 따라 Visual Studio에서 코드를 게시할 수 있습니다.

>[!NOTE]
>필요한 설정을 사용 하 여 Azure AD를 구성 하려면 배포 된 서비스의 URL이 필요 합니다.

### <a name="deploy-the-client-certificate"></a>클라이언트 인증서 배포

1. 클라이언트 인증서는 Jumio API 호출을 보호 하는 데 도움이 됩니다. 다음 PowerShell 샘플 코드를 사용 하 여 자체 서명 된 인증서를 만듭니다.

   ``` PowerShell
   $cert = New-SelfSignedCertificate -Type Custom -Subject "CN=Demo-SigningCertificate" -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.3") -KeyUsage DigitalSignature -KeyAlgorithm RSA -KeyLength 2048 -NotAfter (Get-Date).AddYears(2) -CertStoreLocation "Cert:\CurrentUser\My"
   $cert.Thumbprint
   $pwdText = "Your password"
   $pwd = ConvertTo-SecureString -String $pwdText -Force -AsPlainText
   Export-PfxCertificate -Cert $Cert -FilePath "{your-local-path}\Demo-SigningCertificate.pfx" -Password $pwd.

   ```

   그런 다음 인증서를에 지정 된 위치로 내보냅니다 ``{your-local-path}`` .

3. [이 문서의](../app-service/configure-ssl-certificate.md#upload-a-private-certificate)지침에 따라 Azure App Service 인증서를 가져옵니다.

### <a name="create-a-signingencryption-key"></a>서명/암호화 키 만들기

문자 및 숫자만 포함 된 64 자 보다 긴 임의의 문자열을 만듭니다.

예: ``C9CB44D98642A7062A0D39B94B6CDC1E54276F2E7CFFBF44288CEE73C08A8A65``

다음 PowerShell 스크립트를 사용 하 여 문자열을 만듭니다.

```PowerShell
-join ((0x30..0x39) + ( 0x41..0x5A) + ( 0x61..0x7A) + ( 65..90 ) | Get-Random -Count 64  | % {[char]$_})

```

### <a name="configure-the-api"></a>API 구성

[Azure App Service에서 응용 프로그램 설정을 구성할](../app-service/configure-common.md#configure-app-settings)수 있습니다. 이 방법을 사용 하면 리포지토리에 체크 인하지 않고 설정을 안전 하 게 구성할 수 있습니다. Rest API에 대 한 다음 설정을 제공 해야 합니다.

| 애플리케이션 설정 | 원본 | 메모 |
| :-------- | :------------| :-----------|
|JumioSettings: AuthUsername | Jumio 계정 구성 |     |
|JumioSettings: AuthPassword | Jumio 계정 구성 |     |
|AppSettings: SigningCertThumbprint|생성 된 자체 서명 된 인증서의 지문|  |
|AppSettings: IdTokenSigningKey| PowerShell을 사용 하 여 만든 서명 키 | |
| AppSettings: IdTokenEncryptionKey |PowerShell을 사용 하 여 만든 암호화 키
| AppSettings: IdTokenIssuer | JWT 토큰에 사용할 발급자 (GUID 값이 선호 됨) |
| AppSettings: IdTokenAudience  | JWT 토큰에 사용할 대상 그룹 (GUID 값이 선호 됨) |
|AppSettings:가는 안 되는 Directurl | Azure AD B2C 정책의 기준 URL | https://{테 넌 트 이름}. b2clogin/{응용 프로그램 id}|
| WEBSITE_LOAD_CERTIFICATES| 생성 된 자체 서명 된 인증서의 지문 |

### <a name="deploy-the-ui"></a>UI 배포

1. [저장소 계정에 blob 저장소 컨테이너](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)를 설정 합니다.

2. Ui [폴더](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Jumio/UI) 의 ui 파일을 blob 컨테이너에 저장 합니다.

#### <a name="update-ui-files"></a>UI 파일 업데이트

1. UI 파일에서 [ocean_blue](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Jumio/UI/ocean_blue)폴더로 이동 합니다.

2. 각 HTML 파일을 엽니다.

3. 를 찾고을 `{your-ui-blob-container-url}` blob 컨테이너의 URL로 바꿉니다.

4. 을 (를) 검색 하 여 `{your-intermediate-api-url}` 중간 API app service의 URL로 바꿉니다.

>[!NOTE]
> 모범 사례로, 특성 컬렉션 페이지에서 동의 알림을 추가 하는 것이 좋습니다. Id 확인을 위해 타사 서비스에 정보를 보내도록 사용자에 게 알립니다.

### <a name="configure-the-azure-ad-b2c-policy"></a>Azure AD B2C 정책 구성

1. 정책 폴더의 [Azure AD B2C 정책](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Jumio/Policies) 으로 이동 합니다.

2. [이 문서](./custom-policy-get-started.md?tabs=applications#custom-policy-starter-pack) 에 따라 [localaccounts 시작 팩](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/LocalAccounts)을 다운로드 합니다.

3. Azure AD B2C 테 넌 트에 대 한 정책을 구성 합니다.

>[!NOTE]
>제공 된 정책을 업데이트 하 여 특정 테 넌 트와 관련 된 정책을 업데이트 합니다.

## <a name="test-the-user-flow"></a>사용자 흐름 테스트

1. Azure AD B2C 테 넌 트를 엽니다. **정책** 에서 **Identity Experience Framework** 를 선택합니다.

2. 이전에 만든 **Signupsignin** 을 선택 합니다.

3. **사용자 흐름 실행** 을 선택 하 고 다음을 수행 합니다.

   a. **응용 프로그램** 의 경우 등록 된 앱 (JWT)을 선택 합니다.

   b. **회신 url** 에 대해 **리디렉션 url** 을 선택 합니다.

   c. **사용자 흐름 실행** 을 선택합니다.

4. 등록 흐름을 진행 하 고 계정을 만듭니다.

5. Jumio 서비스는 사용자 특성을 만든 후 흐름 중에 호출 됩니다. 흐름이 불완전 한 경우 사용자가 디렉터리에 저장 되지 않았는지 확인 합니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 문서를 참조 하세요.

- [Azure AD B2C의 사용자 지정 정책](./custom-policy-overview.md)

- [Azure AD B2C에서 사용자 지정 정책 시작](./custom-policy-get-started.md?tabs=applications)