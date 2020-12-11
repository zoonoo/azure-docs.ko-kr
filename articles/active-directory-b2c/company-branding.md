---
title: 조직의 로그인 페이지에 브랜딩 추가
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C 페이지에 조직의 브랜딩을 추가 하는 방법에 대해 알아봅니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/10/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: bee81876b066b9fc1ea69c418ee4d0839db27b3c
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/11/2020
ms.locfileid: "97111496"
---
# <a name="add-branding-to-your-organizations-azure-active-directory-b2c-pages"></a>조직의 Azure Active Directory B2C 페이지에 브랜딩 추가

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Azure Active Directory [회사 브랜딩을](../active-directory/fundamentals/customize-branding.md)사용 하 여 배너 로고, 배경 이미지 및 배경색으로 Azure AD B2C 페이지를 사용자 지정할 수 있습니다. 회사 브랜딩에는 등록, 로그인, 프로필 편집 및 암호 재설정이 포함 됩니다. 

> [!TIP]
> 배너 로고, 배경 이미지 또는 배경색을 벗어나는 사용자 흐름 페이지의 다른 측면을 사용자 지정 하려면 [HTML 템플릿을 사용 하 여 UI를 사용자 지정](customize-ui-with-html.md)하는 방법을 참조 하세요.

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]


사용자 흐름 페이지를 사용자 지정 하려면 먼저 Azure Active Directory에서 회사 브랜딩을 구성한 다음 Azure AD B2C 사용자 흐름의 페이지 레이아웃에서 사용 하도록 설정 합니다.

## <a name="configure-company-branding"></a>회사 브랜딩 구성

**회사 브랜딩** 내에서 배너 로고, 배경 이미지 및 배경색을 설정 하 여 시작 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택한 다음, Azure AD B2C 테넌트가 포함된 디렉터리를 선택합니다.
1. Azure Portal에서 **Azure AD B2C** 를 검색하고 선택합니다.
1. **관리** 에서 **회사 브랜딩** 을 선택 합니다.
1. [조직의 Azure Active Directory 로그인 페이지에 브랜딩 추가](../active-directory/fundamentals/customize-branding.md)의 단계를 따릅니다.

Azure AD B2C에서 회사 브랜딩을 구성할 때 다음 사항을 염두에 두어야 합니다.

* Azure AD B2C의 회사 브랜딩은 현재 **배경 이미지**, **배너 로고** 및 **배경 색** 사용자 지정으로 제한 됩니다. 회사 브랜딩 창의 다른 속성 (예: **고급 설정**)은 *지원 되지 않습니다*.
* 배경 이미지를 로드 하기 전에 사용자 흐름 페이지에서 배경색을 표시 합니다. 더 부드러운 로드 환경을 위해 배경 이미지의 색과 거의 일치 하는 배경색을 선택 하는 것이 좋습니다.
* 등록 사용자 흐름을 시작할 때 사용자에 게 전송 된 확인 전자 메일에 배너 로고가 표시 됩니다.

::: zone pivot="b2c-user-flow"

## <a name="enable-branding-in-user-flow-pages"></a>사용자 흐름 페이지에서 브랜딩 사용

회사 브랜딩을 구성 했으면 사용자 흐름에서 사용 하도록 설정 합니다.

1. Azure Portal 왼쪽 메뉴에서 **Azure AD B2C** 를 선택 합니다.
1. **정책** 에서 **사용자 흐름(정책)** 을 선택합니다.
1. 회사 브랜딩을 사용 하도록 설정할 사용자 흐름을 선택 합니다. 회사 브랜딩은 표준 *로그인* 및 표준 *프로필 편집* 사용자 흐름 유형에 대해 **지원 되지 않습니다** .
1. **사용자 지정** 에서 **페이지 레이아웃** 을 선택한 다음 브랜드를 지정할 레이아웃을 선택 합니다. 예를 들어 **통합 등록 또는 로그인 페이지** 를 선택 합니다.
1. **페이지 레이아웃 버전 (미리 보기)** 의 경우 버전 **1.2.0** 이상을 선택 합니다.
1. **저장** 을 선택합니다.

사용자 흐름의 모든 페이지를 브랜드 하려면 사용자 흐름에서 각 페이지 레이아웃에 대 한 페이지 레이아웃 버전을 설정 합니다.

![Azure Portal에서 Azure AD B2C 페이지 레이아웃 선택](media/company-branding/portal-02-page-layout-select.png)

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="select-a-page-layout"></a>페이지 레이아웃 선택

회사 브랜딩을 사용 하도록 설정 하려면 [](contentdefinitions.md#migrating-to-page-layout) `contract` 사용자 지정 정책의 *모든* 콘텐츠 정의에 대해 페이지 버전으로 페이지 레이아웃 버전을 정의 해야 합니다. 값 형식에는 `contract` _urn: com: microsoft: aad: b2c: elements:**contract**:p age-name: version_ 이라는 단어가 포함 되어야 합니다. 이전 **Datauri** 값을 사용 하는 사용자 지정 정책에서 페이지 레이아웃을 지정 합니다.

페이지 버전으로 [페이지 레이아웃으로 마이그레이션하](contentdefinitions.md#migrating-to-page-layout) 는 방법에 대해 알아봅니다.

다음 예제에서는 페이지 계약이 포함 된 콘텐츠 정의 식별자와 해당 하는 **Datauri** 를 보여 줍니다. 

```xml
<ContentDefinitions>
  <ContentDefinition Id="api.error">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections.signup">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.signuporsignin">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted.profileupdate">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountsignup">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountpasswordreset">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.phonefactor">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.2.0</DataUri>
  </ContentDefinition>
</ContentDefinitions>
```

::: zone-end

다음 예제에서는 대양 Blue 템플릿을 사용 하는 *등록 및 로그인* 사용자 흐름 페이지의 사용자 지정 배너 로고 및 배경 이미지를 보여 줍니다.

![Azure AD B2C에서 제공 하는 브랜드 등록/로그인 페이지](media/company-branding/template-ocean-blue-branded.png)

## <a name="use-company-branding-assets-in-custom-html"></a>사용자 지정 HTML에서 회사 브랜딩 자산 사용

[사용자 지정 HTML](customize-ui-with-html.md)에서 회사 브랜딩 자산을 사용 하려면 태그 외부에 다음 태그를 추가 합니다 `<div id="api">` .

```HTML
<img data-tenant-branding-background="true" />
<img data-tenant-branding-logo="true" alt="Company Logo" />
```

이미지 소스는 배경 이미지 및 배너 로고의 소스로 바뀝니다.

## <a name="next-steps"></a>다음 단계

응용 프로그램의 사용자 인터페이스를 사용자 지정 하는 방법에 대 한 자세한 내용은 [Azure Active Directory B2C에서 응용 프로그램의 사용자 인터페이스 사용자 지정](customize-ui-with-html.md)을 확인 하세요.