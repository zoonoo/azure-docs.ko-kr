---
title: "Azure Active Directory B2C: 사용자 지정 정책을 사용하여 UI 사용자 지정 | Microsoft Docs"
description: "Azure AD B2C에서 사용자 지정 정책을 사용하는 경우 UI(사용자 인터페이스) 사용자 지정에 대한 항목"
services: active-directory-b2c
documentationcenter: 
author: saeeda
manager: krassk
editor: gsacavdm
ms.assetid: 658c597e-3787-465e-b377-26aebc94e46d
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/04/2017
ms.author: saeeda
ms.translationtype: Human Translation
ms.sourcegitcommit: 125f05f5dce5a0e4127348de5b280f06c3491d84
ms.openlocfilehash: 9de6a57671cf65c4aa5d8695d21e0932175b1183
ms.contentlocale: ko-kr
ms.lasthandoff: 05/22/2017

---
# <a name="azure-active-directory-b2c-ui-customization-in-a-custom-policy"></a>Azure Active Directory B2C: 사용자 지정 정책에서 UI 사용자 지정

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

이 문서를 완료하면 브랜드와 모양을 가진 등록 및 로그인 정책을 사용자 지정하게 됩니다.  Azure AD B2C에서는 최종 사용자에게 표시되는 HTML 및 CSS의 거의 모든 권한을 허용합니다.  사용자 지정 정책을 사용하는 경우 Azure Portal에 있는 컨트롤을 사용하는 대신 UI 사용자 지정을 XML에서 구성합니다.

## <a name="prerequisites"></a>필수 조건

계속하기 전에 [사용자 지정 정책을 사용하여 시작](active-directory-b2c-get-started-custom.md)을 완료해야 합니다.  로컬 계정을 사용하여 등록 및 로그인하기 위한 사용자 지정 정책이 작동해야 합니다.

## <a name="the-page-ui-customization-feature"></a>페이지 UI 사용자 지정 기능

페이지 UI 사용자 지정 기능을 사용하여 사용자 지정 정책의 모양과 느낌을 사용자 지정할 수 있습니다.  응용 프로그램과 Azure AD B2C 간에 브랜드와 시각적 일관성을 유지합니다.

작동 방식은 다음과 같습니다. Azure AD B2C는 소비자의 브라우저에서 코드를 실행하고 [크로스-원본 자원 공유 (CORS)](http://www.w3.org/TR/cors/)라는 최신 방법을 사용합니다.  먼저, 사용자 지정된 HTML 콘텐츠가 있는 사용자 지정 정책에서 URL을 지정합니다.  Azure AD B2C는 UI 요소를 URL에서 로드된 HTML 콘텐츠에 병합하고 소비자에게 페이지를 표시합니다.

## <a name="creating-your-html5-content"></a>HTML5 콘텐츠 만들기

제목에서 제품의 브랜드가 있는 HTML 콘텐츠를 만들어보겠습니다.

1. 이 html 코드 조각에서 **복사**를 클릭합니다.  `<body>`에 위치한 `<div id="api"></div>`라는 빈 요소를 사용하여 제대로 구성된 HTML5입니다. 이 요소는 Azure AD B2C 콘텐츠가 삽입되는 위치를 표시합니다.

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

1. 텍스트 편집기에 붙여넣고 파일을 `customize-ui.html`으로 저장합니다.

## <a name="create-a-blob-storage-account"></a>Blob Storage 계정 만들기

>[!NOTE]
> 이 가이드에서 Azure Blob Storage를 사용하여 콘텐츠를 호스트합니다.  웹 서버에 콘텐츠를 호스팅할 수도 있지만 [웹 서버에서 CORS(원본 간 리소스 공유)를 사용하도록 설정](https://enable-cors.org/server.html)해야 합니다.

이제 Azure Blob Storage에서 이 HTML을 호스트해보겠습니다.

1. [Azure 포털](https://portal.azure.com)에 로그인합니다.
1. 허브 메뉴에서 **새로 만들기** -> **저장소** -> **저장소 계정**을 차례로 선택합니다.
1. 저장소 계정의 고유한 **이름**을 입력합니다.
1. **배포 모델**은 **Resource Manager**를 사용하면 됩니다.
1. **계정 종류**를 **Blob Storage**로 변경합니다.
1. **성능**은 **표준**을 사용하면 됩니다.
1. **복제**는 **RA-GRS**를 사용하면 됩니다.
1. **액세스 계층**은 **핫**을 사용하면 됩니다.
1. **저장소 서비스 암호화**는 **비활성화** 상태로 두면 됩니다.
1. 저장소 계정의 **구독**을 선택합니다.
1. **리소스 그룹**을 만들거나 기존 그룹을 선택합니다.
1. 저장소 계정의 **지리적 위치**를 선택합니다.
1. **만들기** 를 클릭하여 저장소 계정을 만들 수 있습니다.
1. 배포가 완료되기를 기다리면 저장소 계정 블레이드가 자동으로 열립니다.

## <a name="create-a-container"></a>컨테이너 만들기

Azure Blob Storage에 공용 컨테이너를 만들어 보겠습니다.

1. **개요**라는 왼쪽 탭으로 전환합니다.
1. **+ 컨테이너**를 클릭합니다.
1. **이름**에 `$root`을 입력합니다.
1. **액세스 형식**을 **Blob**로 설정합니다.
1. '$root'를 클릭하여 새 컨테이너를 엽니다.
1. **업로드**를 클릭합니다.
1. `Select a file` 옆에 있는 폴더 아이콘을 클릭합니다.
1. [이전 섹션](#the-page-ui-customization-feature)에서 만든 `customize-ui.html`로 이동합니다.
1. **업로드**를 클릭합니다.
1. `customize-ui.html`라는 업로드한 Blob을 선택합니다.
1. **URL** 옆의 복사 단추를 클릭합니다.
1. 브라우저를 열고 이 URL로 이동합니다.  액세스할 수 없는 경우 컨테이너 액세스 형식을 Blob으로 설정했는지 확인합니다.

## <a name="configure-cors"></a>CORS 구성

이제 CORS(원본 간 리소스 공유)에 Azure Blob Storage를 구성합니다.

>[!NOTE]
>샘플 HTML 및 CSS 콘텐츠를 사용하여 UI 사용자 지정 기능을 사용하시겠습니까?  Azure Blob Storage 계정에 샘플 콘텐츠를 업로드하고 구성하는 [간단한 도우미 도구](active-directory-b2c-reference-ui-customization-helper-tool.md)를 제공했습니다.  이 도구를 사용하는 경우 [등록 또는 로그인 사용자 지정 정책 수정](#modify-your-sign-up-or-sign-in-custom-policy)로 건너뜁니다.

1. 설정의 저장소 블레이드에서 **CORS**를 엽니다.
1. **+ 추가**를 클릭합니다.
1. `Allowed origins`을 `*`로 설정합니다.
1. `Allowed verbs`라는 드롭다운 목록에서 `GET` 및 `OPTIONS`을 둘 다 선택합니다.
1. `Allowed headers`을 `*`로 설정합니다.
1. `Exposed headers`을 `*`로 설정합니다.
1. `Maximum age (seconds)`을 `200`로 설정합니다.
1. **추가**를 클릭합니다.

## <a name="testing-cors"></a>CORS 테스트

준비되었는지 확인해 보겠습니다.

1. http://test-cors.org/로 이동하고 URL을 `Remote URL` 필드에 붙여넣습니다.
1. **요청 전송**을 클릭합니다.
1. 오류가 발생하는 경우 [CORS 설정](#configure-cors)이 정확한지 확인합니다.  브라우저 캐시의 선택을 취소하거나 개인 브라우저 세션 `CTRL-SHIFT-P`을 사용해야 합니다.

## <a name="modify-your-sign-up-or-sign-in-custom-policy"></a>등록 또는 로그인 사용자 지정 정책 수정

1. 최상위 수준 `<BuildingBlocks>` 태그에서 `<TrustFrameworkPolicy>` 태그를 찾아야 합니다.  `<BuildingBlocks>` 태그 내에서 이 예제를 복사하여 `ContentDefinitions` 태그를 추가합니다.  `{your_storage_account}`을 저장소 계정 이름으로 바꿉니다.

  ```xml
  <BuildingBlocks>
    <ContentDefinitions>
      <ContentDefinition Id="api.idpselections">
        <LoadUri>https://{your_storage_account}.blob.core.windows.net/customize-ui.html</LoadUri>
      </ContentDefinition>
    </ContentDefinitions>
  </BuildingBlocks>
  ```

## <a name="upload-your-updated-custom-policy"></a>업데이트된 사용자 지정 정책 업로드

1. [Azure Portal](https://portal.azure.com)에서 [Azure AD B2C 테넌트의 컨텍스트로 전환](active-directory-b2c-navigate-to-b2c-context.md)하고 Azure AD B2C 블레이드를 엽니다.
1. **모든 정책**을 클릭합니다.
1. **업로드 정책**을 클릭합니다.
1. 추가한 `ContentDefinitions` 태그를 포함한 `SignUpOrSignin.xml`를 업로드합니다.

## <a name="test-the-custom-policy-using-run-now"></a>"지금 실행"을 사용하여 사용자 지정 정책 테스트

1. **Azure AD B2C 블레이드**를 열고 **모든 정책**으로 이동합니다.
1. 업로드한 사용자 지정 정책을 선택하고 **지금 실행** 단추를 클릭합니다.
1. 전자 메일 주소를 사용하여 등록할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 [기본 제공 정책의 UI 사용자 지정을 위한 참조 가이드](active-directory-b2c-reference-ui-customization.md)는 사용자 지정할 수 있는 UI 요소에 대한 추가 정보를 포함합니다.  기본 제공 정책과 사용자 지정 정책 간에는 UI 사용자 지정에 대한 차이점이 없습니다.

