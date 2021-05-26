---
title: 자습서 - 확인 가능한 자격 증명을 발급하도록 Azure Active Directory 구성(미리 보기)
description: 이 자습서에서는 테넌트에서 확인 가능한 자격 증명을 배포하는 데 필요한 환경을 구축합니다.
documentationCenter: ''
author: barclayn
manager: daveba
ms.service: identity
ms.topic: tutorial
ms.subservice: verifiable-credentials
ms.date: 05/18/2021
ms.author: barclayn
ms.reviewer: ''
ms.openlocfilehash: 3f5808f3074fa2e45bc638e3e3d13cad431997ac
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110092795"
---
# <a name="tutorial---configure-your-azure-active-directory-to-issue-verifiable-credentials-preview"></a>자습서 - 확인 가능한 자격 증명을 발급하도록 Azure Active Directory 구성(미리 보기)

이 자습서에서는 [시작](get-started-verifiable-credentials.md) 문서의 일부로 수행된 작업을 기반으로 자체 DID([탈중앙화 식별자](https://www.microsoft.com/security/business/identity-access-management/decentralized-identity-blockchain?rtc=1#:~:text=Decentralized%20identity%20is%20a%20trust,protect%20privacy%20and%20secure%20transactions.))를 사용하여 Azure AD(Azure Active Directory)를 설정합니다. 샘플 앱 및 발급자를 사용하여 확인 가능한 자격 증명을 발급하기 위해 탈중앙화 식별자를 사용하지만 이 자습서에서는 샘플 Azure B2C 테넌트를 인증에 계속 사용합니다.  다음 자습서에서는 Azure AD와 작동하도록 구성된 앱을 가져오기 위한 추가 단계를 수행합니다.

> [!IMPORTANT]
> Azure Active Directory 확인 가능한 자격 증명은 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

이 문서의 내용

> [!div class="checklist"]
> * 확인 가능한 자격 증명을 위해 Azure AD를 온보딩하는 데 필요한 서비스를 만듭니다. 
> * DID를 만듭니다.
> * 규칙 및 디스플레이 파일을 사용자 지정합니다.
> * Azure AD에서 확인 가능한 자격 증명을 구성합니다.


## <a name="prerequisites"></a>필수 구성 요소

이 자습서를 성공적으로 완료하려면 먼저 다음을 수행해야 합니다.

- [시작](get-started-verifiable-credentials.md)을 완료합니다.
- 활성 구독이 포함된 Azure 계정이 있어야 합니다. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- P2 [라이선스](https://azure.microsoft.com/pricing/details/active-directory/)가 있는 Azure AD. [무료 개발자 계정을 만드는 방법](how-to-create-a-free-developer-account.md)(해당 계정이 없는 경우)을 참조하세요.
- 키 및 비밀을 만들 권한이 있는 [Azure Key Vault](../../key-vault/general/overview.md) 인스턴스.

## <a name="azure-active-directory"></a>Azure Active Directory

시작하기 전에 Azure AD 테넌트가 필요합니다. 테넌트가 확인 가능한 자격 증명에 사용될 수 있도록 설정되면 DID(탈중앙화 ID)가 할당되고 확인 가능한 자격 증명을 발급하기 위해 발급자 서비스에서 사용이 가능하도록 설정됩니다. 사용자가 발급하는 확인 가능한 자격 증명은 테넌트 및 DID에서 발급됩니다. DID는 확인 가능한 자격 증명을 확인할 때도 사용됩니다.
테스트 Azure 구독을 만든 경우 테넌트를 사용자 계정으로 채울 필요가 없지만 이후 자습서를 완료하려면 사용자 테스트 계정이 하나 이상 있어야 합니다.

## <a name="create-a-key-vault"></a>주요 자격 증명 모음 만들기

확인 가능한 자격 증명으로 작업하면, 테넌트가 확인 가능한 자격 증명에 디지털 서명하는 데 사용하는 암호화 키를 완전히 제어하고 관리할 수 있습니다. 자격 증명을 발급하고 확인하려면 Azure AD에 자체 Azure Key Vault 인스턴스에 대한 액세스 권한을 제공해야 합니다.

1. Azure Portal 메뉴 또는 **홈** 페이지에서 **리소스 만들기** 를 선택합니다.
2. 검색 상자에 **Key Vault** 를 입력합니다.
3. 결과 목록에서 **Key Vault** 를 선택합니다.
4. Key Vault 섹션에서 **만들기** 를 선택합니다.
5. **Key Vault 만들기** 섹션에서 다음 정보를 제공합니다.
    - **구독**: 구독을 선택합니다.
    - **리소스 그룹** 에서 **새로 만들기** 를 선택하고 리소스 그룹 이름(예: **vc-resource-group**)을 입력합니다. 여러 문서에서 동일한 리소스 그룹 이름을 사용합니다.
    - **Name**: 고유 이름은 필수입니다. **woodgroup-vc-kv** 를 사용하므로 이 값을 고유한 이름으로 바꿉니다.
    - **위치** 풀 다운 메뉴에서 위치를 선택합니다.
    - 다른 옵션은 기본값으로 그대로 둡니다.
6. 위의 정보를 제공한 후 **액세스 정책** 을 선택합니다.

    ![키 자격 증명 모음 만들기 페이지](media/enable-your-tenant-verifiable-credentials/create-key-vault.png)

7. 액세스 정책 화면에서 **액세스 정책 추가** 를 선택합니다.

    >[!NOTE]
    > 기본적으로 키 자격 증명 모음을 생성하는 계정이 액세스 권한이 있는 유일한 계정입니다. 확인 가능한 자격 증명 서비스는 키 자격 증명 모음에 대한 액세스 권한이 필요합니다. 키 자격 증명 모음에는 관리자가 **키를 생성** 하고, 옵트아웃하는 경우 **키를 삭제** 하고, 확인 가능한 자격 증명에 대한 도메인 바인딩을 생성하기 위해 **서명** 하도록 허용하는 액세스 정책이 있어야 합니다. 테스트하는 동안 동일한 계정을 사용하는 경우에는 자격 증명 모음 생성자에게 부여된 기본 권한 외에도 계정 **서명** 을 허용하도록 기본 정책을 수정해야 합니다.

8. 사용자 관리자의 경우 키 권한 섹션에 **만들기**, **삭제** 및 **서명** 이 가능하도록 설정되어 있는지 확인합니다. 기본적으로 만들기 및 삭제는 이미 가능하도록 설정되어 있으며, 업데이트해야 하는 유일한 키 권한은 서명이어야 합니다. 

    ![키 자격 증명 모음 권한](media/enable-your-tenant-verifiable-credentials/keyvault-access.png)

9. **검토 + 만들기** 를 선택합니다.
10. **만들기** 를 선택합니다.
11. 자격 증명 모음으로 이동하여 자격 증명 모음 이름과 URI를 기록해 둡니다.

아래에 나열된 두 개의 속성을 기록합니다.

- **자격 증명 모음 이름**: 예에서 값 이름은 **woodgrove-vc-kv** 입니다. 이 이름은 다른 단계에서 사용됩니다.
- **자격 증명 모음 URI**: 이 예에서 값은 https://woodgrove-vc-kv.vault.azure.net/ 입니다. REST API를 통해 사용자 자격 증명 모음을 사용하는 애플리케이션은 URI를 사용해야 합니다.

>[!NOTE]
> 키 자격 증명 모음을 트랜잭션할 때마다 Azure 구독 비용이 추가로 발생합니다. 자세한 내용은 [Key Vault 가격 책정 페이지](https://azure.microsoft.com/pricing/details/key-vault/)를 검토하세요.

>[!IMPORTANT]
> Azure Active Directory 확인 가능한 자격 증명 미리 보기 중에는 자격 증명 모음에서 만든 키와 비밀을 생성된 후에 수정해서는 안 됩니다. 키와 비밀을 삭제, 비활성화 또는 업데이트하면 발급된 자격 증명이 무효화됩니다. 미리 보기 중에는 키 또는 비밀을 수정하지 마세요.

## <a name="create-a-modified-rules-and-display-file"></a>수정된 규칙 및 디스플레이 파일 만들기

이 섹션에서는 [샘플 발급자 앱](https://github.com/Azure-Samples/active-directory-verifiable-credentials/)의 규칙 및 디스플레이 파일을 사용하고 이를 약간 수정하여 테넌트의 첫 번째 확인 가능한 자격 증명을 만듭니다.

1. 규칙 및 디스플레이 json 파일을 모두 임시 폴더에 복사하고 이름을 **MyFirstVC-display.json** 및 **MyFirstVC-rules.json** 으로 각각 바꿉니다. 두 파일은 **issuer\issuer_config** 에서 모두 찾을 수 있습니다.

   ![샘플 앱 디렉터리에 있는 디스플레이 및 규칙 파일](media/enable-your-tenant-verifiable-credentials/sample-app-rules-display.png)

   ![임시 폴더에 있는 디스플레이 및 규칙 파일](media/enable-your-tenant-verifiable-credentials/display-rules-files-temp.png)

2. 코드 편집기에서 MyFirstVC-rules.json 파일을 엽니다. 

    ```json
         {
          "attestations": {
            "idTokens": [
              {
                "mapping": {
                  "firstName": { "claim": "given_name" },
                  "lastName": { "claim": "family_name" }
                },
                "configuration": "https://didplayground.b2clogin.com/didplayground.onmicrosoft.com/B2C_1_sisu/v2.0/.well-known/openid-configuration",
                "client_id": "8d5b446e-22b2-4e01-bb2e-9070f6b20c90",
                "redirect_uri": "vcclient://openid/"
              }
            ]
          },
          "validityInterval": 2592000,
          "vc": {
            "type": ["VerifiedCredentialExpert"]
          }
        }
      
    ```

   이제 type 필드를 "MyFirstVC"로 변경하겠습니다. 

   ```json
    "type": ["MyFirstVC"]
  
   ```

   이 변경 사항을 저장합니다.

   >[!NOTE]
   > 자습서의 이 시점에서는 **"configuration"** 또는 **"client_id"** 를 변경하지 않습니다. [시작](get-started-verifiable-credentials.md)에서 사용한 Microsoft B2C 테넌트를 계속 사용합니다. Azure AD는 다음 자습서에서 사용합니다.

3. 코드 편집기에서 MyFirstVC-display.json 파일을 엽니다.

   ```json
       {
          "default": {
           "locale": "en-US",
           "card": {
             "title": "Verified Credential Expert",
             "issuedBy": "Microsoft",
             "backgroundColor": "#000000",
             "textColor": "#ffffff",
             "logo": {
               "uri": "https://didcustomerplayground.blob.core.windows.net/public/VerifiedCredentialExpert_icon.png",
               "description": "Verified Credential Expert Logo"
             },
             "description": "Use your verified credential to prove to anyone that you know all about verifiable credentials."
           },
           "consent": {
             "title": "Do you want to get your Verified Credential?",
             "instructions": "Sign in with your account to get your card."
           },
           "claims": {
             "vc.credentialSubject.firstName": {
               "type": "String",
               "label": "First name"
             },
             "vc.credentialSubject.lastName": {
               "type": "String",
               "label": "Last name"
             }
           }
         }
      }
   ```

   확인 가능한 자격 증명이 샘플 코드 버전과 다르게 보이도록 몇 가지를 수정해 보겠습니다. 

    ```json
         "card": {
            "title": "My First VC",
            "issuedBy": "Your Issuer Name",
            "backgroundColor": "#ffffff",
            "textColor": "#000000",
          }
    ```
 
   >[!NOTE]
   > 자격 증명을 읽고 액세스할 수 있게 하려면 [대비 비율](https://www.w3.org/WAI/WCAG21/Techniques/general/G18)이 최소 4.5:1 이상인 텍스트와 배경색을 선택하는 것이 좋습니다.  

   이 변경 사항을 저장합니다.

## <a name="create-a-storage-account"></a>스토리지 계정 만들기

확인 가능한 자격 증명을 처음 만들기 전에, 구성 및 규칙 파일을 보관할 수 있는 Blob Storage 컨테이너를 만들어야 합니다.

1. 아래 표시된 옵션을 사용하여 스토리지 계정을 만듭니다. 자세한 단계는 [스토리지 계정 만들기](../../storage/common/storage-account-create.md?tabs=azure-portal) 문서에서 검토하세요.

   - **구독:** 이 자습서에 사용 중인 구독을 선택합니다.
   - **리소스 그룹:** 이전 자습서에서 사용한 것과 동일한 리소스 그룹(**vc-resource-group**)을 선택합니다.
   - **이름**: 고유한 이름
   - **위치:** (미국) 미국 동부
   - **성능:** 표준
   - **계정 종류:** Storage V2.
   - **복제:** 로컬 중복.
 
   ![새 스토리지 계정 만들기](media/enable-your-tenant-verifiable-credentials/create-storage-account.png)

2. 스토리지 계정을 만든 후 컨테이너를 만들어야 합니다. **Blob Storage** 에서 **컨테이너** 를 선택하고 아래 제공된 값을 사용하여 컨테이너를 만듭니다.

    - **이름:** vc-container
    - **공용 액세스 수준:** 프라이빗(익명 액세스 없음)

   ![컨테이너 만들기](media/enable-your-tenant-verifiable-credentials/new-container.png)

3. 이제 새 컨테이너를 선택하고 이전에 만든 새 규칙 및 디스플레이 파일인 **MyFirstVC-display.json** 및 **MyFirstVC-rules.json** 을 모두 업로드합니다.

   ![규칙 파일 업로드](media/enable-your-tenant-verifiable-credentials/blob-storage-upload-rules-display-files.png)

## <a name="assign-blob-role"></a>Blob 역할 할당

자격 증명을 만들기 전에 먼저 로그인한 사용자에게 올바른 역할 할당을 제공해야 사용자가 Storage Blob의 파일에 액세스할 수 있습니다.

1. **Storage** > **컨테이너** 로 이동합니다.
2. 왼쪽 메뉴에서 **액세스 제어(IAM)** 를 선택합니다.
3. **역할 할당** 을 선택합니다.
4. **추가** 를 선택합니다.
5. **역할** 섹션에서 **Storage Blob 데이터 Reader** 를 선택합니다.
6. **다음에 대한 액세스 할당** 에서 **사용자, 그룹 또는 서비스 보안 주체** 를 선택합니다.
7. **선택** 에서: 이 단계를 수행하기 위해 사용 중인 계정을 선택합니다.
8. **저장** 을 선택하여 역할 할당을 완료합니다.


   ![역할 할당 추가](media/enable-your-tenant-verifiable-credentials/role-assignment.png)

  >[!IMPORTANT]
  >기본적으로 컨테이너 생성자는 **소유자** 역할을 할당받습니다. **소유자** 역할만으로는 충분하지 않습니다. 사용 중인 계정에 **Storage Blob 데이터 Reader** 역할이 필요합니다. 자세한 내용은 [Azure Portal을 사용하여 Blob 및 큐 데이터에 액세스하기 위한 Azure 역할 할당](../../storage/common/storage-auth-aad-rbac-portal.md)을 참조하세요.

## <a name="set-up-verifiable-credentials-preview"></a>확인 가능한 자격 증명 설정(미리 보기)

이제 확인 가능한 자격 증명을 위해 테넌트를 설정하는 마지막 단계를 수행해야 합니다.

1. Azure Portal에서 **확인 가능한 자격 증명** 을 검색합니다.
2. **확인 가능한 자격 증명(미리 보기)** 을 선택합니다.
3. **시작** 을 선택합니다.
4. 조직을 설정하고 조직 이름, 도메인 및 키 자격 증명 모음을 제공해야 합니다. 각각의 값을 살펴보겠습니다.

      - **조직 이름**: 이 이름은 확인 가능한 자격 증명 서비스 내에서 귀사의 비즈니스를 참조하는 방법입니다. 이 값은 고객에게 표시되지 않습니다.
      - **도메인:** 입력한 도메인은 DID 문서의 서비스 엔드포인트에 추가됩니다. [Microsoft Authenticator](../user-help/user-help-auth-app-download-install.md) 및 기타 월렛은 이 정보를 사용하여 DID가 [도메인에 연결되어 있는지](how-to-dnsbind.md) 확인합니다. 월렛이 DID를 확인할 수 있으면 확인된 기호가 표시됩니다. 월렛이 DID를 확인할 수 없으면, 유효성을 검사할 수 없는 조직에서 자격 증명이 발급되었음을 사용자에게 알립니다. 도메인은 DID를 사용자가 귀사의 비즈니스에 대해 알 수 있는 유형의 무언가에 바인딩하는 것입니다.
      - **키 자격 증명 모음:** 이전에 생성한 키 자격 증명 모음의 이름을 제공합니다.
 
   >[!IMPORTANT]
   > 도메인은 리디렉션될 수 없습니다. 리디렉션되면 DID와 도메인을 연결할 수 없습니다. https://www.domain.com 형식을 사용해야 합니다.
    
5. **자격 증명 저장 및 생성** 을 선택합니다.

    ![조직 ID 설정](media/enable-your-tenant-verifiable-credentials/save-create.png)

축하합니다. 이제 테넌트에서 확인 가능한 자격 증명 미리 보기를 사용할 수 있습니다.

## <a name="create-your-vc-in-the-portal"></a>포털에서 VC 만들기

이전 단계는 **새 자격 증명 만들기** 페이지에서 끝났습니다. 

   ![확인 가능한 자격 증명 시작](media/enable-your-tenant-verifiable-credentials/create-credential-after-enable-did.png)

1. 자격 증명 이름에 **MyFirstVC** 라는 이름을 추가합니다. 이 이름은 포털에서 확인 가능한 자격 증명을 식별하는 데 사용되며 확인 가능한 자격 증명 계약의 일부로 포함됩니다.
2. 디스플레이 파일 섹션에서 **디스플레이 파일 구성** 을 선택합니다.
3. **스토리지 계정** 섹션에서 **woodgrovevcstorage** 를 선택합니다.
4. 사용 가능한 컨테이너 목록에서 **vc-container** 를 선택합니다.
5. 앞에서 만든 **MyFirstVC-display.json** 파일을 선택합니다.
6. **규칙 파일** 섹션의 **새 자격 증명 만들기** 에서 **규칙 파일 구성** 을 선택합니다.
7. **스토리지 계정** 섹션에서 **woodgrovecstorage** 를 선택합니다.
8. **vc-container** 를 선택합니다.
9. **MyFirstVC-rules.json** 을 선택합니다.
10. **새 자격 증명 만들기** 화면에서 **만들기** 를 선택합니다.

   ![새 자격 증명 만들기](media/enable-your-tenant-verifiable-credentials/create-my-first-vc.png)

### <a name="credential-url"></a>자격 증명 URL

새 자격 증명이 생성되었으면 자격 증명 URL을 복사합니다.

   ![자격 증명 발급 URL](media/enable-your-tenant-verifiable-credentials/issue-credential-url.png)

>[!NOTE]
>자격 증명 URL은 규칙 및 디스플레이 파일의 조합입니다. Authenticator가 사용자에게 확인 가능한 자격 증명 발급 요구 사항을 표시하기 전에 평가하는 URL입니다.  

## <a name="update-the-sample-app"></a>샘플 앱 업데이트

이제 샘플 앱의 발급자 코드를 수정하여 확인 가능한 자격 증명 URL로 업데이트합니다. 이렇게 하면 자체 테넌트를 사용하여 확인 가능한 자격 증명을 발급할 수 있습니다.

1. 샘플 앱의 파일이 있는 폴더로 이동합니다.
2. 발급자 폴더를 연 다음, Visual Studio Code에서 app.js를 엽니다.
3. 상수 'credential'을 새 자격 증명 URL로 업데이트하고 credentialType 상수를 'MyFirstVC'로 설정하고 파일을 저장합니다.

    ![관련 영역이 강조 표시된 Visual Studio 코드 이미지](media/enable-your-tenant-verifiable-credentials/sample-app-vscode.png)

4. 명령 프롬프트를 열고 발급자 폴더를 엽니다.
5. 업데이트된 노드 앱을 실행합니다.

    ```terminal
    node app.js
    ```

6. 다른 명령 프롬프트를 사용하여 ngrok를 실행해 URL을 8081에 설정합니다. [ngrok npm 패키지](https://www.npmjs.com/package/ngrok/)를 사용하여 ngrok를 전역으로 설치할 수 있습니다.

    ```terminal
    ngrok http 8081
    ```
    
    >[!IMPORTANT]
    > 이 앱이나 웹 사이트가 위험할 수 있다는 경고도 표시될 수 있습니다. 아직 DID를 도메인에 연결하지 않았기 때문에 현재 이 메시지가 표시될 수 있습니다. 이 내용을 구성하려면 [DNS 바인딩](how-to-dnsbind.md) 지침을 참조하세요.

    
7. ngrok에서 생성된 HTTPS URL을 엽니다.

    ![NGROK 전달 엔드포인트](media/enable-your-tenant-verifiable-credentials/ngrok-url-screen.png)

8. **자격 증명 가져오기** 를 선택합니다.
9. Authenticator가 QR 코드를 스캔합니다.
10. **이 앱 또는 웹 사이트는 위험할 수 있습니다** 경고 메시지에서 **고급** 을 선택합니다.

  ![초기 경고](media/enable-your-tenant-verifiable-credentials/site-warning.png)

11. 위험한 웹 사이트에서 경고에서 **계속 진행(안전하지 않음)** 을 선택합니다.

  ![발급자에 대한 두 번째 경고](media/enable-your-tenant-verifiable-credentials/site-warning-proceed.png)


12. **자격 증명 추가** 화면에서 다음 몇 가지를 확인합니다. 
    1. 화면 맨 위에 빨간색 **확인 안 됨** 메시지가 보입니다.
    1. 디스플레이 파일의 변경 사항을 기반으로 자격 증명이 사용자 지정되었습니다.
    1. **귀하 계정에 로그인** 옵션이 **didplayground.b2clogin.com** 을 가리킵니다.
    
   ![경고가 있는 자격 증명 추가 화면](media/enable-your-tenant-verifiable-credentials/add-credential-not-verified.png)

13. **귀하 계정에 로그인** 을 선택하고 [시작 자습서](get-started-verifiable-credentials.md)에서 제공한 자격 증명 정보를 사용하여 인증합니다.
14. 인증에 성공하면 **추가** 단추가 더 이상 회색으로 표시되지 않습니다. **추가** 를 선택합니다.

  ![인증 후 자격 증명 추가 화면](media/enable-your-tenant-verifiable-credentials/add-credential-not-verified-authenticated.png)

이제 인증에 B2C 테넌트를 계속 사용하면서 테넌트를 사용하여 확인 가능한 자격 증명을 발급하여 vc를 생성했습니다.

  ![Azure AD에서 발급되고 Azure B2C 인스턴스에서 인증된 VC](media/enable-your-tenant-verifiable-credentials/my-vc-b2c.png)


## <a name="test-verifying-the-vc-using-the-sample-app"></a>샘플 앱을 사용하여 VC 확인 테스트

자체 테넌트에 의해 확인 가능한 자격 증명이 발급되었으면 샘플 앱을 사용하여 확인해보겠습니다.

>[!IMPORTANT]
> 테스트할 때는 [시작](get-started-verifiable-credentials.md) 문서에서 사용한 것과 동일한 이메일 및 암호를 사용합니다. 테넌트에 의해 vc가 발급되는 동안 입력은 Microsoft B2C 테넌트에서 발급된 ID 토큰에서 제공됩니다. 자습서 2에서는 토큰 발급을 테넌트로 전환합니다.

1. Azure Portal의 확인 가능한 자격 증명 블레이드에서 **설정** 을 엽니다. DID(탈중앙화 식별자)를 복사합니다.

   ![DID 복사](media/enable-your-tenant-verifiable-credentials/issuer-identifier.png)

2. 이제 샘플 앱 파일의 검증 도구 폴더를 엽니다. 검증 도구 샘플 코드에서 app.js 파일을 업데이트하고 다음과 같이 변경해야 합니다.

    - **credential**: 자격 증명 URL로 변경합니다.
    - **credentialType**: 'MyFirstVC'
    - **issuerDid**: 이 값은 Azure Portal > 확인 가능한 자격 증명(미리 보기) > 설정 > DID(탈중앙화 식별자)에서 복사합니다.
    
   ![상수 issuerDid를 발급자 식별자와 일치하도록 업데이트](media/enable-your-tenant-verifiable-credentials/editing-verifier.png)

3. 발급자 ngrok 서비스 실행을 중지합니다.

    ```cmd
    control-c
    ```

4. 이제 ngrok를 검증 도구 포트 8082를 사용하여 실행합니다.

    ```cmd
    ngrok http 8082
    ```

5. 다른 터미널 창에서 검증 도구 앱으로 이동하여 발급자 앱을 실행한 방법과 유사하게 실행합니다.

    ```cmd
    cd ..
    cd verifier
    node app.js
    ```

6. 브라우저에서 ngrok URL을 열고 모바일 디바이스에서 Authenticator를 사용하여 QR 코드를 스캔합니다.
7. 모바일 디바이스의 **새 권한 요청** 화면에서 **허용** 을 선택합니다.

    >[!NOTE]
    > 이 VC에 서명하는 DID는 아직 Microsoft B2C입니다. 검증 도구 DID는 여전히 Microsoft 샘플 앱 테넌트에서 가져온 것입니다. Microsoft의 DID는 당사 소유 도메인에 연결되었기 때문에 발급 과정에서 발생한 것과 같은 경고는 표시되지 않습니다. 이 사항은 다음 섹션에서 업데이트될 예정입니다.
    
   ![새 권한 요청](media/enable-your-tenant-verifiable-credentials/new-permission-request.png)

8. 이제 자격 증명이 확인되었습니다.

## <a name="next-steps"></a>다음 단계

자체 발급자로부터 VC를 발행하는 샘플 코드가 준비되었으면 계속해서 다음 섹션에서는 자체 ID 공급자를 사용하여 확인 가능한 자격 증명을 얻으려는 사용자를 인증하고 DID를 사용하여 프레젠테이션 요청에 서명해보겠습니다.

> [!div class="nextstepaction"]
> [자습서 - 테넌트를 사용하여 확인 가능한 자격 증명 발급 및 확인](issue-verify-verifiable-credentials-your-tenant.md)


