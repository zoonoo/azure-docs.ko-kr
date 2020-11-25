---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 03/19/2020
ms.author: mimart
ms.openlocfilehash: 4cb4cdef227c786b12790903037fdc02649592e3
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2020
ms.locfileid: "95990887"
---
## <a name="use-custom-page-content"></a>사용자 지정 페이지 내용 사용

페이지 UI 사용자 지정 기능을 사용하면 사용자 지정 정책의 모양과 느낌을 사용자 지정할 수 있습니다. 또한 애플리케이션과 Azure AD B2C 간에 브랜드와 시각적 개체 일관성을 유지할 수 있습니다.

### <a name="how-it-works"></a>작동 방식

Azure AD B2C는 [CORS (원본 간 리소스 공유)](https://www.w3.org/TR/cors/)를 사용 하 여 고객의 브라우저에서 코드를 실행 합니다. 런타임에 사용자 흐름이 나 사용자 지정 정책에 지정 하는 URL에서 콘텐츠가 로드 됩니다. 사용자 환경의 각 페이지는 해당 페이지에 대해 지정한 URL에서 해당 콘텐츠를 로드 합니다. URL에서 콘텐츠를 로드 한 후에는 Azure AD B2C에 의해 삽입 된 HTML 조각과 병합 되 고,이 페이지는 고객에 게 표시 됩니다.

![사용자 지정 페이지 콘텐츠 여백](./media/active-directory-b2c-html-templates/html-content-merging.png)

## <a name="custom-html-page-content"></a>사용자 지정 HTML 페이지 콘텐츠

사용자 지정 페이지 콘텐츠를 제공 하기 위해 고유한 브랜딩을 사용 하 여 HTML 페이지를 만듭니다. 이 페이지는 정적 `*.html` 페이지 또는 .net, Node.js 또는 PHP와 같은 동적 페이지가 될 수 있습니다.

사용자 지정 페이지 콘텐츠에는 CSS 및 JavaScript를 비롯 한 HTML 요소가 포함 될 수 있지만 iframe과 같은 안전 하지 않은 요소는 포함할 수 없습니다. 유일 하 게 필요한 요소는 `id` `api` `<div id="api"></div>` HTML 페이지 내에서와 같이로 설정 된 div 요소입니다.

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

### <a name="customize-the-default-azure-ad-b2c-pages"></a>기본 Azure AD B2C 페이지 사용자 지정

사용자 지정 페이지 콘텐츠를 처음부터 만드는 대신 Azure AD B2c의 기본 페이지 콘텐츠를 사용자 지정할 수 있습니다.

다음 표에서는 Azure AD B2C에서 제공 하는 기본 페이지 콘텐츠를 나열 합니다. 파일을 다운로드 하 여 고유한 사용자 지정 페이지를 만들기 위한 시작 지점으로 사용 합니다.

| 기본 페이지 | Description | 콘텐츠 정의 ID<br/>(사용자 지정 정책에만 해당) |
|:-----------------------|:--------|-------------|
| [exception.html](https://login.microsoftonline.com/static/tenant/default/exception.cshtml) | **오류 페이지**. 예외 또는 오류가 발생하면 이 페이지가 표시됩니다. | *api.error* |
| [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) |  **자체 어설션된 페이지**. 이 파일은 소셜 계정 등록 페이지, 로컬 계정 등록 페이지, 로컬 계정 로그인 페이지, 암호 다시 설정 등의 사용자 지정 페이지 콘텐츠로 사용 합니다. 양식에는 텍스트 입력란, 암호 입력란, 라디오 단추, 단일 선택 드롭다운 상자 및 다중 선택 확인란과 같이 다양한 입력 컨트롤이 포함될 수 있습니다. | api. *localaccountsignin*, *localaccountsignup*, *localaccountpasswordreset*, *selfasserted* |
| [multifactor-1.0.0.html](https://login.microsoftonline.com/static/tenant/default/multifactor-1.0.0.cshtml) | **Multi-Factor Authentication 페이지** 입니다. 등록 또는 로그인 중에 사용자가 이 페이지에서 텍스트 또는 음성을 사용하여 전화 번호를 확인할 수 있습니다. | *api.phonefactor* |
| [updateprofile.html](https://login.microsoftonline.com/static/tenant/default/updateProfile.cshtml) | **프로필 업데이트 페이지** 입니다. 이 페이지에는 사용자가 자신의 프로필을 업데이트하기 위해 액세스할 수 있는 양식이 있습니다. 암호 입력 필드를 제외하고는 소셜 계정 등록 페이지와 비슷합니다. | *api.selfasserted.profileupdate* |
| [unified.html](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) | **통합 등록 또는 로그인 페이지** 입니다. 이 페이지는 사용자 등록 및 로그인 프로세스를 처리합니다. 사용자는 엔터프라이즈 ID 공급자, 소셜 ID 공급자(예: Facebook, Google+) 또는 로컬 계정을 사용할 수 있습니다. | *api.signuporsignin* |

## <a name="hosting-the-page-content"></a>페이지 콘텐츠 호스팅

고유한 HTML 및 CSS 파일을 사용 하 여 UI를 사용자 지정 하는 경우 CORS를 지 원하는 공개적으로 사용 가능한 HTTPS 끝점에서 UI 콘텐츠를 호스팅합니다. 예를 들어 [Azure Blob storage](../articles/storage/blobs/storage-blobs-introduction.md), [Azure 앱 Services](../articles/app-service/index.yml), 웹 서버, Cdns, AWS S3 또는 파일 공유 시스템입니다.

## <a name="guidelines-for-using-custom-page-content"></a>사용자 지정 페이지 콘텐츠 사용에 대 한 지침

- HTML 파일에 미디어, CSS 및 JavaScript 파일과 같은 외부 리소스를 포함 하는 경우 절대 URL을 사용 합니다.
- [페이지 레이아웃 버전](../articles/active-directory-b2c/page-layout.md) 1.2.0 이상을 사용 하 여 `data-preload="true"` HTML 태그에 특성을 추가 하 여 CSS 및 JavaScript에 대 한 로드 순서를 제어할 수 있습니다. `data-preload=true`에서는 페이지가 사용자에 게 표시 되기 전에 생성 됩니다. 이 특성을 사용 하면 스타일이 지정 되지 않은 HTML이 사용자에 게 표시 되지 않고 CSS 파일을 미리 로드 하 여 페이지를 "깜박임" 할 수 없습니다. 다음 HTML 코드 조각에서는 태그를 사용 하는 방법을 보여 줍니다 `data-preload` .
  ```HTML
  <link href="https://path-to-your-file/sample.css" rel="stylesheet" type="text/css" data-preload="true"/>
  ```
- 기본 페이지 콘텐츠를 시작 하 고 그 위에 작성 하는 것이 좋습니다.
- [사용자 흐름](../articles/active-directory-b2c/user-flow-javascript-overview.md) 및 사용자 [지정 정책](../articles/active-directory-b2c/javascript-samples.md)에 대 한 사용자 지정 콘텐츠에 JavaScript를 포함할 수 있습니다.
- 지원되는 브라우저 버전은 다음과 같습니다.
  - Internet Explorer 11, 10 및 Microsoft Edge
  - Internet Explorer 9 및 8에 대한 제한된 지원
  - Google Chrome 42.0 이상
  - Mozilla Firefox 38.0 이상
  - IOS 및 macOS 용 Safari, 버전 12 이상
- 보안 제한으로 인해 Azure AD B2C는, `frame` `iframe` 또는 HTML 요소를 지원 하지 않습니다 `form` .

## <a name="custom-page-content-walkthrough"></a>사용자 지정 페이지 콘텐츠 연습

프로세스에 대 한 개요는 다음과 같습니다.

1. 사용자 지정 페이지 콘텐츠를 호스트할 위치를 준비 합니다 (공개적으로 액세스할 수 있는 CORS 사용 HTTPS 끝점).
1. 기본 페이지 콘텐츠 파일을 다운로드 하 고 사용자 지정 합니다 (예:) `unified.html` .
1. 사용자 지정 페이지 콘텐츠를 공개적으로 사용 가능한 HTTPS 끝점에 게시 합니다.
1. 웹앱에 대한 CORS(원본 간 리소스 공유) 설정
1. 사용자 지정 정책 콘텐츠 URI에 대 한 정책을 가리킵니다.

### <a name="1-create-your-html-content"></a>1. HTML 콘텐츠 만들기

제목에 제품의 브랜드 이름을 사용 하 여 사용자 지정 페이지 콘텐츠를 만듭니다.

1. 다음 HTML 코드 조각을 복사합니다. 태그 안에 있는 라는 빈 요소가 있는 올바른 형식의 HTML5입니다 *\<div id="api"\>\</div\>* *\<body\>* . 이 요소는 Azure AD B2C 콘텐츠가 삽입되는 위치를 나타냅니다.

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

1. 복사한 코드 조각을 텍스트 편집기에 붙여넣은 다음 *customize-ui.html* 이라는 파일로 저장합니다.

> [!NOTE]
> Login.microsoftonline.com를 사용 하는 경우 보안 제한으로 인해 HTML 양식 요소가 제거 됩니다. 사용자 지정 HTML 콘텐츠에서 HTML 양식 요소를 사용 하려면 [b2clogin.com를 사용](../articles/active-directory-b2c/b2clogin.md)합니다.

### <a name="2-create-an-azure-blob-storage-account"></a>2. Azure Blob storage 계정 만들기

이 가이드에서는 Azure Blob Storage를 사용하여 콘텐츠를 호스팅합니다. 웹 서버에서 콘텐츠를 호스팅하도록 선택할 수 있지만 [웹 서버에서 CORS를 사용하도록 설정](https://enable-cors.org/server.html)(영문)해야 합니다.

Blob 저장소에서 HTML 콘텐츠를 호스트 하려면 다음 단계를 수행 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **허브** 메뉴에서 **새로 만들기**  >  **저장소**  >  **저장소 계정** 을 선택 합니다.
1. 저장소 계정에 대 한 **구독** 을 선택 합니다.
1. **리소스 그룹** 을 만들거나 기존 그룹을 선택 합니다.
1. 저장소 계정에 대 한 고유한 **이름을** 입력 합니다.
1. 저장소 계정에 대 한 **지리적 위치** 를 선택 합니다.
1. **배포 모델** 은 **Resource Manager** 로 유지하면 됩니다.
1. **성능** 은 **표준** 으로 유지하면 됩니다.
1. **계정 종류** 를 **Blob Storage** 로 변경합니다.
1. **복제** 는 **RA-GRS** 로 유지하면 됩니다.
1. **액세스 계층** 은 **핫** 으로 유지하면 됩니다.
1. **검토 + 만들기** 를 선택 하 여 저장소 계정을 만듭니다.
    배포가 완료 되 면 **저장소 계정** 페이지가 자동으로 열립니다.

#### <a name="21-create-a-container"></a>2.1 컨테이너 만들기

Blob storage에서 공용 컨테이너를 만들려면 다음 단계를 수행 합니다.

1. 왼쪽 메뉴의 **Blob service** 에서 **blob** 을 선택 합니다.
1. **+ 컨테이너** 를 선택 합니다.
1. **이름** 에 *root* 를 입력 합니다. 이름은 선택한 이름 (예: *contoso*)이 될 수 있지만이 예제에서는 *루트* 를 사용 하는 것이 간단 합니다.
1. **공용 액세스 수준** 에서 **Blob**, **확인** 을 차례로 선택 합니다.
1. **루트** 를 선택 하 여 새 컨테이너를 엽니다.

#### <a name="22-upload-your-custom-page-content-files"></a>2.2 사용자 지정 페이지 콘텐츠 파일 업로드

1. **업로드** 를 선택합니다.
1. **파일 선택** 옆에 있는 폴더 아이콘을 선택 합니다.
1. 페이지 UI 사용자 지정 섹션에서 이전에 만든 **customize-ui.html** 로 이동 하 여 선택 합니다.
1. 하위 폴더에 업로드 하려는 경우 **고급** 을 확장 하 고 **폴더에 업로드** 폴더에 폴더 이름을 입력 합니다.
1. **업로드** 를 선택합니다.
1. 업로드 한 **customize-ui.html** blob을 선택 합니다.
1. **Url** 텍스트 상자의 오른쪽에서 **클립보드에 복사** 아이콘을 선택 하 여 url을 클립보드에 복사 합니다.
1. 웹 브라우저에서 복사한 URL로 이동 하 여 업로드 한 blob에 액세스할 수 있는지 확인 합니다. 액세스할 수 없는 경우, 예를 들어 오류가 발생 한 경우 `ResourceNotFound` 컨테이너 액세스 형식이 **blob** 으로 설정 되어 있는지 확인 합니다.

### <a name="3-configure-cors"></a>3. CORS 구성

다음 단계를 수행 하 여 원본 간 리소스 공유에 대 한 Blob 저장소를 구성 합니다.

1. 메뉴에서 **CORS** 를 선택합니다.
1. **허용된 원본** 에 `https://your-tenant-name.b2clogin.com`을 입력합니다. `your-tenant-name`은 Azure AD B2C 테넌트의 이름으로 바꿉니다. 예들 들어 `https://fabrikam.b2clogin.com`입니다. 모든 소문자를 사용 하 여 테 넌 트 이름을 입력 합니다.
1. **허용된 메소드** 에서 `GET`과 `OPTIONS`를 모두 선택합니다.
1. **허용된 헤더** 에 별표(*)를 입력합니다.
1. **노출된 헤더** 에 별표(*)를 입력합니다.
1. **최대 기간** 에 200을 입력합니다.
1. **저장** 을 선택합니다.

#### <a name="31-test-cors"></a>3.1 테스트 CORS

다음 단계를 수행 하 여 준비가 되었는지 확인 합니다.

1. CORS 구성 단계를 반복 합니다. **허용 되는 원본** 에 대해 다음을 입력 합니다.`https://www.test-cors.org`
1. [Www.test-cors.org](https://www.test-cors.org/) 로 이동 합니다. 
1. **원격 url** 상자에 HTML 파일의 url을 붙여넣습니다. 예, `https://your-account.blob.core.windows.net/root/azure-ad-b2c/unified.html`
1. **요청 보내기** 를 선택 합니다.
    결과는 여야 `XHR status: 200` 합니다. 
    오류가 발생하는 경우 CORS 설정이 올바른지 확인합니다. Ctrl+Shift+P를 눌러 브라우저 캐시를 비우거나 개인 검색 세션을 열어야 할 수도 있습니다.