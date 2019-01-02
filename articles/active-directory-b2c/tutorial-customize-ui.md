---
title: 자습서 - Azure Active Directory B2C에서 애플리케이션의 사용자 인터페이스 사용자 지정 | Microsoft Docs
description: Azure Portal를 사용하여 Azure Active Directory B2C에서 애플리케이션의 사용자 인터페이스 사용자 지정하는 방법에 대해 알아봅니다.
services: B2C
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: f8ebb282d3f6abbc37739891c0f7228bef110d82
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/04/2018
ms.locfileid: "52842682"
---
# <a name="tutorial-customize-the-user-interface-of-your-applications-in-azure-active-directory-b2c"></a>자습서: Azure Active Directory B2C에서 애플리케이션의 사용자 인터페이스 사용자 지정

가입, 로그인 및 프로필 편집과 같은 보다 일반적인 사용자 환경을 위해 Azure AD(Azure Active Directory) B2C에서 [사용자 흐름](active-directory-b2c-reference-policies.md)을 사용할 수 있습니다. 이 자습서의 정보는 고유한 HTML 및 CSS 파일을 사용하여 이러한 환경의 [UI(사용자 인터페이스)를 사용자 지정](customize-ui-overview.md)하는 방법을 알아보는 데 도움이 됩니다.

이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
> * UI 사용자 지정 파일 만들기
> * 파일을 사용하는 가입 및 로그인 사용자 흐름 만들기
> * 사용자 지정된 UI 테스트

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>필수 조건

아직 고유한 [Azure AD B2C 테넌트](tutorial-create-tenant.md)를 만들지 않았다면 지금 만듭니다. 이전 자습서에서 만든 경우 기존 테넌트를 사용할 수 있습니다.

## <a name="create-customization-files"></a>사용자 지정 파일 만들기

Azure 저장소 계정 및 컨테이너를 만든 다음, 기본 HTML 및 CSS 파일을 컨테이너에 배치합니다.

### <a name="create-a-storage-account"></a>저장소 계정 만들기

여러 가지 방법으로 파일을 저장할 수 있지만, 이 자습서에서는 [Azure Blob 저장소](../storage/blobs/storage-blobs-introduction.md)에 저장합니다.

1. Azure 구독이 포함된 디렉터리를 사용하고 있는지 확인합니다. 최상위 메뉴에서 **디렉터리 및 구독 필터**를 선택하고 구독이 포함된 디렉터리를 선택합니다. 이 디렉터리는 Azure B2C 테넌트가 포함된 디렉터리와 다릅니다.

    ![구독 디렉터리로 전환](./media/tutorial-customize-ui/switch-directories.png)

2. Azure Portal의 왼쪽 상단 모서리에서 모든 서비스를 선택하고 **저장소 계정**을 검색하여 선택합니다. 
3. **추가**를 선택합니다.
4. **리소스 그룹** 아래에서 **새로 만들기**를 선택하고 새 리소스 그룹의 이름을 입력한 다음, **확인**을 클릭합니다.
5. 저장소 계정의 이름을 입력합니다. 선택하는 이름은 Azure 전체에서 고유해야 하고, 3~24자 사이여야 하고, 숫자 및 소문자만 포함할 수 있습니다.
6. 저장소 계정의 위치를 선택하거나 기본 위치를 적용합니다. 
7. 다른 모든 기본값을 적용하고 **검토 + 만들기**를 선택한 다음, **만들기**를 클릭합니다.
8. 저장소 계정을 만든 후 **리소스로 이동**을 선택합니다.

### <a name="create-a-container"></a>컨테이너 만들기

1. 저장소 계정의 개요 페이지에서 **Blobs**를 선택합니다.
2. **컨테이너**를 선택하고, 컨테이너 이름을 입력하고, **Blob(BLOB 전용 익명 읽기 액세스)** 를 선택한 다음, **확인**을 클릭합니다.

### <a name="enable-cors"></a>CORS를 사용하도록 설정

 브라우저의 Azure AD B2C 코드는 현대적이고 표준적인 방법을 사용하여 사용자 흐름에서 지정하는 URL의 사용자 지정 콘텐츠를 로드합니다. CORS(원본 간 리소스 공유)를 사용하면 웹 페이지의 제한된 리소스를 다른 도메인에서 요청할 수 있습니다.

1. 메뉴에서 **CORS**를 선택합니다.
2. **허용된 원본**에 `https://your-tenant-name.b2clogin.com`을 입력합니다. `your-tenant-name`은 Azure AD B2C 테넌트의 이름으로 바꿉니다. 예: `https://fabrikam.b2clogin.com` 테넌트 이름을 입력할 때는 모두 소문자를 사용해야 합니다.
3. **허용된 메소드**에서 `GET`과 `OPTIONS`를 모두 선택합니다.
4. **허용된 헤더**에 별표(*)를 입력합니다.
5. **노출된 헤더**에 별표(*)를 입력합니다.
6. **최대 기간**에 200을 입력합니다.

    ![CORS를 사용하도록 설정](./media/tutorial-customize-ui/enable-cors.png)

5. **저장**을 클릭합니다.

### <a name="create-the-customization-files"></a>사용자 지정 파일 만들기

등록 환경의 UI를 사용자 지정하려면 간단한 HTML 및 CSS 파일을 만드는 것으로 시작합니다. 원하는 방식으로 HTML을 구성할 수 있지만 식별자가 `api`인 **div** 요소가 있어야 합니다. 예: `<div id="api"></div>` Azure AD B2C는 페이지가 표시될 때 `api` 컨테이너에 요소를 삽입합니다.

1. 로컬 폴더에서 다음 파일을 만들고 `your-storage-account`를 저장소 계정 이름으로 변경하고 `your-container`를 생성된 컨테이너의 이름으로 변경해야 합니다. 예: `https://store1.blob.core.windows.net/b2c/style.css`

    ```html
    <!DOCTYPE html>
    <html>
      <head>
        <title>My B2C Application</title>
        <link rel="stylesheet" href="https://your-storage-account.blob.core.windows.net/your-container/style.css">
      </head>
      <body>  
        <h1>My B2C Application</h1>
        <div id="api"></div>
      </body>
    </html>
    ```

    원하는 방식으로 페이지를 디자인할 수 있지만 **api** div 요소는 사용자가 만든 모든 HTML 사용자 지정 파일에 필요합니다. 

3. 파일을 *custom-ui.html*로 저장합니다.
4. Azure AD B2C가 삽입하는 요소를 포함하여 등록 또는 로그인 페이지의 모든 요소를 센터에 배치하는 다음과 같은 간단한 CSS를 만듭니다.

    ```css
    h1 {
      color: blue;
      text-align: center;
    }
    .intro h2 {
      text-align: center; 
    }
    .entry {
      width: 300px ;
      margin-left: auto ;
      margin-right: auto ;
    }
    .divider h2 {
      text-align: center; 
    }
    .create {
      width: 300px ;
      margin-left: auto ;
      margin-right: auto ;
    }
    ```

5. 파일을 *style.css*로 저장합니다.

### <a name="upload-the-customization-files"></a>사용자 지정 파일 업로드

이 자습서에서는 Azure AD B2C가 액세스할 수 있도록 저장소 계정에서 만든 파일을 저장합니다.

1. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스**를 선택하고 **저장소 계정**을 검색하여 선택합니다.
2. 생성한 저장소 계정을 선택하고 **Blobs**를 선택한 다음, 생성한 컨테이너를 선택합니다.
3. **업로드**를 선택하고 *custom-ui.html* 파일을 선택한 다음, **업로드**를 클릭합니다.

    ![사용자 지정 파일 업로드](./media/tutorial-customize-ui/upload-blob.png)

4. 자습서 뒷부분에서 사용하기 위해 업로드한 파일의 URL을 복사합니다.
5. *style.css* 파일에 대해 3 ~ 4단계를 반복합니다.

## <a name="create-a-sign-up-and-sign-in-user-flow"></a>가입 및 로그인 사용자 흐름 만들기

이 자습서의 단계를 완료하려면 Azure AD B2C에서 테스트 애플리케이션 및 가입 또는 로그인 사용자 흐름을 만들어야 합니다. 이 자습서에 설명된 원칙을 프로필 편집과 같은 다른 사용자 환경에 적용할 수 있습니다.

### <a name="create-an-azure-ad-b2c-application"></a>Azure AD B2C 애플리케이션 만들기

Azure AD B2C와의 통신은 테넌트에서 만드는 애플리케이션을 통해 수행됩니다. 다음 단계에서는 [https://jwt.ms](https://jwt.ms)로 반환되는 권한 부여 토큰을 리디렉션하는 애플리케이션을 만듭니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. Azure AD B2C 테넌트를 포함하는 디렉터리를 사용하려면 위쪽 메뉴에서 **디렉터리 및 구독 필터**를 클릭하고 테넌트가 포함된 디렉터리를 선택합니다.
3. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스**를 선택하고 **Azure AD B2C**를 검색하여 선택합니다.
4. **응용 프로그램**을 선택하고 **추가**를 선택합니다.
5. 애플리케이션 이름(예: *testapp1*)을 입력합니다.
6. **웹앱/웹 API**에서 `Yes`를 선택하고 **회신 URL**에는 `https://jwt.ms`를 입력합니다.
7. **만들기**를 클릭합니다.

### <a name="create-the-user-flow"></a>사용자 흐름 만들기

사용자 지정 파일을 테스트하려면 이전에 만든 애플리케이션을 사용하는 기본 제공 가입 또는 로그인 사용자 흐름을 만듭니다.

1. Azure AD B2C 테넌트에서 **사용자 흐름**을 선택한 다음, **새 사용자 흐름**을 클릭합니다.
2. **추천** 탭에서 **가입 및 로그인**을 클릭합니다.
3. 사용자 흐름의 이름을 입력합니다. 예: *signup_signin*. 사용자 흐름이 생성될 때 *B2C_1* 접두사가 이름에 자동으로 추가됩니다.
4. **ID 공급 기업**에서 **이메일 가입**을 선택합니다.
5. **사용자 특성 및 클레임**에서 **자세히 표시**를 선택합니다.
6. **수집 특성** 열에서 가입하는 동안 고객에게서 수집하려는 특성을 선택합니다. 예를 들어 **국가/지역**, **표시 이름** 및 **우편 번호**를 설정합니다.
7. **클레임 반환** 열에서 성공적인 가입 또는 로그인 환경 이후에 애플리케이션으로 다시 전송된 권한 부여 토큰에서 반환하려는 클레임을 선택합니다. 예를 들어 **표시 이름**, **ID 공급자**, **우편 번호**, **새 사용자** 및 **사용자의 개체 ID**를 선택합니다.
8. **확인**을 클릭합니다.
9. **만들기**를 클릭합니다.
10. **사용자 지정**에서 **페이지 레이아웃**을 선택합니다. **통합 가입 또는 로그인 페이지**를 선택한 다음, **사용자 지정 페이지 콘텐츠 사용**에 대해 **예**를 클릭합니다.
11. **사용자 지정 페이지 URI**에서 이전에 기록한 *custom-ui.html* 파일의 URL을 입력합니다.
12. 페이지 위쪽에서 **저장**을 클릭합니다.

## <a name="test-the-user-flow"></a>사용자 흐름 테스트

1. Azure AD B2C 테넌트에서 **사용자 흐름**을 선택하고 만든 사용자 흐름을 선택합니다. 예: *B2C_1_signup_signin*.
2. 페이지 맨 위에서 **사용자 흐름 실행**을 클릭합니다.
3. **사용자 흐름 실행** 단추를 클릭합니다.

    ![가입 또는 로그인 사용자 흐름 실행](./media/tutorial-customize-ui/run-user-flow.png)

    생성한 CSS 파일에 따라 요소가 중심에 있는 다음 예제와 유사한 페이지가 표시됩니다.

    ![사용자 흐름 결과](./media/tutorial-customize-ui/run-now.png) 

## <a name="next-steps"></a>다음 단계

이 문서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * UI 사용자 지정 파일 만들기
> * 파일을 사용하는 가입 및 로그인 사용자 흐름 만들기
> * 사용자 지정된 UI 테스트

> [!div class="nextstepaction"]
> [Azure Active Directory B2C의 사용자 언어 지정](active-directory-b2c-reference-language-customization.md)