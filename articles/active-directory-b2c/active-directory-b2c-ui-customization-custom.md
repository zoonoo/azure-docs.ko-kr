---
title: Azure Active Directory B2C에서 사용자 지정 정책을 사용하여 애플리케이션의 사용자 인터페이스 사용자 지정 | Microsoft Docs
description: Azure Active Directory B2C에서 사용자 지정 정책을 사용하여 사용자 인터페이스를 사용자 지정하는 방법을 알아봅니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/18/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: c3c97e786e2147f043a63b90b886e01eb5944cb4
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66507683"
---
# <a name="customize-the-user-interface-of-your-application-using-a-custom-policy-in-azure-active-directory-b2c"></a>Azure Active Directory B2C에서 사용자 지정 정책을 사용하여 애플리케이션의 사용자 인터페이스 사용자 지정

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

이 문서를 완료하면 브랜드와 모양이 포함된 등록 및 로그인 사용자 지정 정책을 갖습니다. Azure AD B2C(Azure Active Directory B2C)를 사용하면 사용자에게 제공되는 HTML 및 CSS 콘텐츠를 거의 완벽하게 제어할 수 있습니다. 사용자 지정 정책을 사용하는 경우 Azure Portal의 컨트롤을 사용하는 대신 XML로 UI 사용자 지정을 구성합니다. 

## <a name="prerequisites"></a>필수 조건

[사용자 지정 정책 시작](active-directory-b2c-get-started-custom.md)의 단계를 완료합니다. 로컬 계정을 사용하여 등록 및 로그인하기 위한 사용자 지정 정책이 작동해야 합니다.

## <a name="page-ui-customization"></a>페이지 UI 사용자 지정

페이지 UI 사용자 지정 기능을 사용하면 사용자 지정 정책의 모양과 느낌을 사용자 지정할 수 있습니다. 또한 애플리케이션과 Azure AD B2C 간에 브랜드와 시각적 개체 일관성을 유지할 수 있습니다.

작동 방식은 다음과 같습니다. Azure AD B2C는 고객의 브라우저에서 코드를 실행하고 [CORS(원본 간 리소스 공유)](https://www.w3.org/TR/cors/)라는 최신 방법을 사용합니다. 먼저, 사용자 지정된 HTML 콘텐츠가 있는 사용자 지정 정책에서 URL을 지정합니다. Azure AD B2C는 UI 요소를 URL에서 로드된 HTML 콘텐츠와 병합한 다음 고객에게 해당 페이지를 표시합니다.

## <a name="create-your-html5-content"></a>HTML5 콘텐츠 만들기

제목에 제품의 브랜드 이름이 포함된 HTML 콘텐츠를 만듭니다.

1. 다음 HTML 코드 조각을 복사합니다. *\<body\>* 태그 내에 있는 *\<div id="api"\>\</div\>* 라는 빈 요소와 함께 잘 구성된 HTML5입니다. 이 요소는 Azure AD B2C 콘텐츠가 삽입되는 위치를 나타냅니다.

   ```html
   <!DOCTYPE html>
   <html>
   <head>
       <title>My Product Brand Name</title>
   </head>
   <body>
       <div id="api"></div>
   </body>
   </html>
   ```

2. 복사한 코드 조각을 텍스트 편집기에 붙여넣은 다음 *customize-ui.html*이라는 파일로 저장합니다.

## <a name="create-an-azure-blob-storage-account"></a>Azure Blob Storage 계정 만들기

>[!NOTE]
> 이 가이드에서는 Azure Blob Storage를 사용하여 콘텐츠를 호스팅합니다. 웹 서버에서 콘텐츠를 호스팅하도록 선택할 수 있지만 [웹 서버에서 CORS를 사용하도록 설정](https://enable-cors.org/server.html)(영문)해야 합니다.

Blob Storage에서 이 HTML 콘텐츠를 호스팅하려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **허브** 메뉴에서 **새로 만들기** > **저장소** > **저장소 계정**을 차례로 선택합니다.
3. 저장소 계정의 고유한 **이름**을 입력합니다.
4. **배포 모델**은 **Resource Manager**로 유지하면 됩니다.
5. **계정 종류**를 **Blob Storage**로 변경합니다.
6. **성능**은 **표준**으로 유지하면 됩니다.
7. **복제**는**RA-GRS**로 유지하면 됩니다.
8. **액세스 계층**은 **핫**으로 유지하면 됩니다.
9. **Storage 서비스 암호화**는 **사용 안 함**으로 유지하면 됩니다.
10. 저장소 계정에 대한 **구독**을 선택합니다.
11. **리소스 그룹**을 만들거나 기존 그룹을 선택합니다.
12. 저장소 계정에 대한 **지리적 위치**를 선택합니다.
13. **만들기** 를 클릭하여 저장소 계정을 만들 수 있습니다.  
    배포가 완료되면 **Storage 계정** 블레이드가 자동으로 열립니다.

## <a name="create-a-container"></a>컨테이너 만들기

Blob Storage에 공용 컨테이너를 만들려면 다음을 수행합니다.

1. **개요** 탭을 클릭합니다.
2. **컨테이너**를 클릭합니다.
3. **이름**으로 **$root**를 입력합니다.
4. **액세스 형식**을 **Blob**로 설정합니다.
5. **$root**를 클릭하여 새 컨테이너를 엽니다.
6. **업로드**를 클릭합니다.
7. **파일 선택** 옆에 있는 폴더 아이콘을 클릭합니다.
8. 이전에 페이지 UI 사용자 지정 섹션에서 만든 **customize-ui.html**로 이동합니다.
9. **업로드**를 클릭합니다.
10. 업로드한 customize-ui.html Blob을 선택합니다.
11. **URL** 옆에 있는 **복사**를 클릭합니다.
12. 브라우저에서 복사한 URL을 붙여넣고 해당 사이트로 이동합니다. 사이트에 액세스할 수 없는 경우 컨테이너 액세스 형식이 **Blob**으로 설정되어 있는지 확인합니다.

## <a name="configure-cors"></a>CORS 구성

다음을 수행하여 CORS(원본 간 리소스 공유)에 Blob Storage를 구성합니다.

1. 메뉴에서 **CORS**를 선택합니다.
2. **허용된 원본**에 `https://your-tenant-name.b2clogin.com`을 입력합니다. `your-tenant-name`은 Azure AD B2C 테넌트의 이름으로 바꿉니다. 예: `https://fabrikam.b2clogin.com`. 테넌트 이름을 입력할 때는 모두 소문자를 사용해야 합니다.
3. **허용된 메소드**에서 `GET`과 `OPTIONS`를 모두 선택합니다.
4. **허용된 헤더**에 별표(*)를 입력합니다.
5. **노출된 헤더**에 별표(*)를 입력합니다.
6. **최대 기간**에 200을 입력합니다.
7. **저장**을 클릭합니다.

## <a name="test-cors"></a>CORS 테스트

다음을 수행하여 준비가 되었는지 확인합니다.

1. [www.test-cors.org](https://www.test-cors.org/) 웹 사이트로 이동한 다음, **원격 URL** 상자에 URL을 붙여넣습니다.
2. **요청 보내기**를 클릭합니다.  
    오류가 발생하는 경우 [CORS 설정](#configure-cors)이 올바른지 확인합니다. Ctrl+Shift+P를 눌러 브라우저 캐시를 비우거나 개인 검색 세션을 열어야 할 수도 있습니다.

## <a name="modify-the-extensions-file"></a>확장 파일 수정

UI 사용자 지정을 구성하려면 **ContentDefinition** 및 해당 자식 요소를 기본 파일에서 확장 파일로 복사합니다.

1. 정책의 기본 파일을 엽니다(예: *TrustFrameworkBase.xml*).
2. **ContentDefinitions** 요소의 전체 내용을 검색한 후 복사합니다.
3. 확장 파일을 엽니다(예: 예: *TrustFrameworkExtensions.xml* **BuildingBlocks** 요소를 검색합니다. 요소가 존재하지 않는 경우 추가합니다.
4. 복사한 **ContentDefinitions**의 전체 내용을 **BuildingBlocks** 요소의 자식으로 붙여 넣습니다. 
5. 복사한 XML에서 `Id="api.signuporsignin"`을 포함하는 **ContentDefinition** 요소를 검색합니다.
6. **LoadUri** 값을 저장소에 업로드한 HTML 파일의 URL로 변경합니다. 예: `https://your-storage-account.blob.core.windows.net/your-container/customize-ui.html`.
    
    사용자 지정 정책이 다음과 비슷해야 합니다.

    ```xml
    <BuildingBlocks>
      <ContentDefinitions>
        <ContentDefinition Id="api.signuporsignin">
          <LoadUri>https://your-storage-account.blob.core.windows.net/your-container/customize-ui.html</LoadUri>
          <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
          <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0</DataUri>
          <Metadata>
            <Item Key="DisplayName">Signin and Signup</Item>
          </Metadata>
        </ContentDefinition>
      </ContentDefinitions>
    </BuildingBlocks>
    ```

7. 확장 파일을 저장합니다.

## <a name="upload-your-updated-custom-policy"></a>업데이트된 사용자 지정 정책 업로드

1. Azure AD B2C 테넌트를 포함하는 디렉터리를 사용하려면 위쪽 메뉴에서 **디렉터리 및 구독 필터**를 클릭하고 테넌트가 포함된 디렉터리를 선택합니다.
3. Azure Portal의 왼쪽 위에서 **모든 서비스**를 선택하고 **Azure AD B2C**를 검색하여 선택합니다.
4. **ID 경험 프레임워크**를 선택합니다.
2. **모든 정책**을 클릭합니다.
3. **업로드 정책**을 클릭합니다.
4. 이전에 변경한 확장 파일을 업로드합니다.

## <a name="test-the-custom-policy-by-using-run-now"></a>**지금 실행**을 사용하여 사용자 지정 정책 테스트

1. **Azure AD B2C 블레이드**를 열고 **모든 정책**으로 이동합니다.
2. 업로드한 사용자 지정 정책을 선택하고 **지금 실행** 단추를 클릭합니다.
3. 이메일 주소를 사용하여 등록할 수 있습니다.

## <a name="reference"></a>참조

UI 사용자 지정을 위한 샘플 템플릿은 다음에서 찾을 수 있습니다.

```
git clone https://github.com/azureadquickstarts/b2c-azureblobstorage-client
```

sample_templates/wingtip 폴더에는 다음과 같은 HTML 파일이 있습니다.

| HTML5 템플릿 | 설명 |
|----------------|-------------|
| *phonefactor.html* | 다단계 인증 페이지의 템플릿으로 사용합니다. |
| *resetpassword.html* | 암호 찾기 페이지의 템플릿으로 사용합니다. |
| *selfasserted.html* | 소셜 계정 등록 페이지, 로컬 계정 등록 페이지 또는 로컬 계정 로그인 페이지의 템플릿으로 사용합니다. |
| *unified.html* | 통합 등록 또는 로그인 페이지의 템플릿으로 사용합니다. |
| *updateprofile.html* | 프로필 업데이트 페이지의 템플릿으로 사용합니다. |

등록 또는 로그인 사용자 지정 정책 수정 섹션에서 `api.idpselections`에 대한 콘텐츠 정의를 구성했습니다. 다음 표에서는 Azure AD B2C ID 환경 프레임워크에서 인식되는 콘텐츠 정의 ID 및 해당 설명에 대한 전체 집합을 보여 줍니다.

| 콘텐츠 정의 ID | 설명 | 
|-----------------------|-------------|
| *api.error* | **오류 페이지**입니다. 예외 또는 오류가 발생하면 이 페이지가 표시됩니다. |
| *api.idpselections* | **ID 공급자 선택 페이지**입니다. 이 페이지는 로그인하는 동안 사용자가 선택할 수 있는 ID 공급자의 목록을 포함합니다. 이러한 옵션은 엔터프라이즈 ID 공급자, 소셜 ID 공급자(예: Facebook, Google+) 또는 로컬 계정입니다. |
| *api.idpselections.signup* | **등록을 위한 ID 공급자 선택 페이지**입니다. 이 페이지는 등록하는 동안 사용자가 선택할 수 있는 ID 공급자의 목록을 포함합니다. 이러한 옵션은 엔터프라이즈 ID 공급자, 소셜 ID 공급자(예: Facebook, Google+) 또는 로컬 계정입니다. |
| *api.localaccountpasswordreset* | **암호 찾기 페이지**입니다. 사용자가 암호 재설정을 시작하기 위해 완성해야 하는 양식이 있습니다.  |
| *api.localaccountsignin* | **로컬 계정 로그인 페이지**입니다. 이메일 주소 또는 사용자 이름을 기반으로 하는 로컬 계정으로 로그인하기 위한 양식이 있습니다. 양식에는 텍스트 입력 상자 및 암호 입력란이 포함될 수 있습니다. |
| *api.localaccountsignup* | **로컬 계정 등록 페이지**입니다. 이메일 주소 또는 사용자 이름을 기반으로 하는 로컬 계정을 등록하기 위한 양식이 있습니다. 양식에는 텍스트 입력란, 암호 입력란, 라디오 단추, 단일 선택 드롭다운 상자 및 다중 선택 확인란과 같이 다양한 입력 컨트롤이 포함될 수 있습니다. |
| *api.phonefactor* | **Multi-Factor Authentication 페이지**입니다. 등록 또는 로그인 중에 사용자가 이 페이지에서 텍스트 또는 음성을 사용하여 전화 번호를 확인할 수 있습니다. |
| *api.selfasserted* | **소셜 계정 등록 페이지**입니다. 소셜 ID 공급자(예: Facebook 또는 Google+)에서 기존 계정을 사용하여 등록할 때 사용자가 완성해야 하는 등록 양식이 있습니다. 암호 입력 필드를 제외하고는 위의 소셜 계정 등록 페이지와 비슷합니다. |
| *api.selfasserted.profileupdate* | **프로필 업데이트 페이지**입니다. 사용자가 자신의 프로필을 업데이트하는 데 사용할 수 있는 양식이 있습니다. 암호 입력 필드를 제외하고는 소셜 계정 등록 페이지와 비슷합니다. |
| *api.signuporsignin* | **통합 등록 또는 로그인 페이지**입니다. 엔터프라이즈 ID 공급자, 소셜 ID 공급자(예: Facebook 또는 Google+) 또는 로컬 계정을 사용할 수 있는 사용자의 등록과 로그인을 모두 처리합니다.  |

## <a name="next-steps"></a>다음 단계

사용자 지정할 수 있는 UI 요소에 대한 추가 정보는 [기본 제공 정책의 UI 사용자 지정을 위한 참조 가이드](active-directory-b2c-reference-ui-customization.md)를 참조하세요.
