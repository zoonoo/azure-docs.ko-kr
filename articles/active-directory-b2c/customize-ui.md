---
title: 사용자 인터페이스 사용자 지정
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C를 사용 하는 응용 프로그램에 대 한 사용자 인터페이스를 사용자 지정 하는 방법을 알아봅니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/28/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: ecece3a00a788b67f6c831804bf5b00372fef93d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99055863"
---
# <a name="customize-the-user-interface-in-azure-active-directory-b2c"></a>Azure Active Directory B2C에서 사용자 인터페이스 사용자 지정

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

고객에 게 표시 되 Azure Active Directory B2C는 사용자 인터페이스 (Azure AD B2C)의 브랜딩 및 사용자 지정은 응용 프로그램에서 원활한 사용자 환경을 제공 하는 데 도움이 됩니다. 이러한 환경에는 등록, 로그인, 프로필 편집 및 암호 재설정이 포함 됩니다. 이 문서에서는 페이지 템플릿 및 회사 브랜딩을 사용 하 여 Azure AD B2C 페이지를 사용자 지정 합니다. 

> [!TIP]
> 페이지 템플릿, 배너 로고, 배경 이미지 또는 배경색 이외의 사용자 흐름 페이지의 다른 측면을 사용자 지정 하려면 [HTML 템플릿을 사용 하 여 UI를 사용자 지정](customize-ui-with-html.md)하는 방법을 참조 하세요.

## <a name="prerequisites"></a>필수 구성 요소

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="overview"></a>개요

사용자 경험 페이지를 전문적으로 제공 하기 위해 선택할 수 있는 몇 가지 기본 제공 템플릿을 제공 Azure AD B2C 합니다. 이러한 페이지 템플릿은 [회사 브랜딩](#company-branding) 기능을 사용 하 여 사용자 지정을 위한 시작 지점으로도 사용할 수 있습니다.

### <a name="ocean-blue"></a>바다색

등록 로그인 페이지에서 렌더링 된 대양 Blue 템플릿의 예:

![대양 Blue 템플릿 스크린샷](media/customize-ui/template-ocean-blue.png)

### <a name="slate-gray"></a>슬레이트 회색

로그인 페이지에서 렌더링 되는 슬레이트 회색 템플릿의 예:

![슬레이트 회색 템플릿 스크린샷](media/customize-ui/template-slate-gray.png)

### <a name="classic"></a>클래식

등록 로그인 페이지에서 렌더링 되는 클래식 템플릿의 예는 다음과 같습니다.

![클래식 템플릿 스크린샷](media/customize-ui/template-classic.png)

### <a name="company-branding"></a>회사 브랜딩

Azure Active Directory [회사 브랜딩을](../active-directory/fundamentals/customize-branding.md)사용 하 여 배너 로고, 배경 이미지 및 배경색으로 Azure AD B2C 페이지를 사용자 지정할 수 있습니다. 회사 브랜딩에는 등록, 로그인, 프로필 편집 및 암호 재설정이 포함 됩니다. 

다음 예제에서는 대양 Blue 템플릿을 사용 하 여 사용자 지정 로고, 배경 이미지를 사용 하 여 *등록 및 로그인* 페이지를 보여 줍니다.

![Azure AD B2C에서 제공 하는 브랜드 등록/로그인 페이지](media/customize-ui/template-ocean-blue-branded.png)


## <a name="select-a-page-template"></a>페이지 템플릿 선택

::: zone pivot="b2c-user-flow"

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택한 다음, Azure AD B2C 테넌트가 포함된 디렉터리를 선택합니다.
1. Azure Portal에서 **Azure AD B2C** 를 검색하고 선택합니다.
1. **사용자 흐름** 을 선택합니다.
1. 사용자 지정 하려는 사용자 흐름을 선택 합니다.
1. 왼쪽 메뉴의 **사용자 지정** 에서 **페이지 레이아웃** 을 선택한 다음 **템플릿을** 선택 합니다.
    ![Azure Portal의 사용자 흐름 페이지에서 템플릿 선택 드롭다운](./media/customize-ui/select-page-template.png)

템플릿을 선택 하면 선택한 템플릿이 사용자 흐름의 모든 페이지에 적용 됩니다. 각 페이지의 URI는 **사용자 지정 페이지 uri** 필드에 표시 됩니다.

::: zone-end

::: zone pivot="b2c-custom-policy"

페이지 템플릿을 선택 하려면 `LoadUri` [콘텐츠 정의](contentdefinitions.md)의 요소를 설정 합니다. 다음 예제에서는 콘텐츠 정의 식별자와 해당을 보여 줍니다 `LoadUri` . 

바다색:

```xml
<ContentDefinitions>
  <ContentDefinition Id="api.error">
    <LoadUri>~/tenant/templates/AzureBlue/exception.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections">
    <LoadUri>~/tenant/templates/AzureBlue/idpSelector.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections.signup">
    <LoadUri>~/tenant/templates/AzureBlue/idpSelector.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.signuporsignin">
    <LoadUri>~/tenant/templates/AzureBlue/unified.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted">
    <LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted.profileupdate">
    <LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountsignup">
    <LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountpasswordreset">
    <LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.phonefactor">
    <LoadUri>~/tenant/templates/AzureBlue/multifactor-1.0.0.cshtml</LoadUri>
  </ContentDefinition>
</ContentDefinitions>
```

슬레이트 회색:

```xml
<ContentDefinitions>
  <ContentDefinition Id="api.error">
    <LoadUri>~/tenant/templates/MSA/exception.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections">
    <LoadUri>~/tenant/templates/MSA/idpSelector.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections.signup">
    <LoadUri>~/tenant/templates/MSA/idpSelector.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.signuporsignin">
    <LoadUri>~/tenant/templates/MSA/unified.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted">
    <LoadUri>~/tenant/templates/MSA/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted.profileupdate">
    <LoadUri>~/tenant/templates/MSA/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountsignup">
    <LoadUri>~/tenant/templates/MSA/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountpasswordreset">
    <LoadUri>~/tenant/templates/MSA/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.phonefactor">
    <LoadUri>~/tenant/templates/MSA/multifactor-1.0.0.cshtml</LoadUri>
  </ContentDefinition>
</ContentDefinitions>
```

기존 

```xml
<ContentDefinitions>
  <ContentDefinition Id="api.error">
    <LoadUri>~/tenant/templates/default/exception.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections">
    <LoadUri>~/tenant/templates/default/idpSelector.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections.signup">
    <LoadUri>~/tenant/templates/default/idpSelector.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.signuporsignin">
    <LoadUri>~/tenant/templates/AzureBlue/unified.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted">
    <LoadUri>~/tenant/templates/default/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted.profileupdate">
    <LoadUri>~/tenant/templates/default/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountsignup">
    <LoadUri>~/tenant/templates/default/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountpasswordreset">
    <LoadUri>~/tenant/templates/default/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.phonefactor">
    <LoadUri>~/tenant/templates/default/multifactor-1.0.0.cshtml</LoadUri>
  </ContentDefinition>
</ContentDefinitions>
```
::: zone-end


## <a name="configure-company-branding"></a>회사 브랜딩 구성

사용자 흐름 페이지를 사용자 지정 하려면 먼저 Azure Active Directory에서 회사 브랜딩을 구성한 다음 Azure AD B2C 사용자 흐름에서 사용 하도록 설정 합니다.

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



## <a name="enable-company-branding-in-user-flow-pages"></a>사용자 흐름 페이지에서 회사 브랜딩 사용

::: zone pivot="b2c-user-flow"

회사 브랜딩을 구성 했으면 사용자 흐름에서 사용 하도록 설정 합니다.

1. Azure Portal 왼쪽 메뉴에서 **Azure AD B2C** 를 선택 합니다.
1. **정책** 에서 **사용자 흐름(정책)** 을 선택합니다.
1. 회사 브랜딩을 사용 하도록 설정할 사용자 흐름을 선택 합니다. 회사 브랜딩은 표준 *로그인* 및 표준 *프로필 편집* 사용자 흐름 유형에 대해 **지원 되지 않습니다** .
1. **사용자 지정** 에서 **페이지 레이아웃** 을 선택한 다음 브랜드를 지정 하려는 페이지를 선택 합니다. 예를 들어 **통합 등록 또는 로그인 페이지** 를 선택 합니다.
1. **페이지 레이아웃 버전 (미리 보기)** 의 경우 버전 **1.2.0** 이상을 선택 합니다.
1. **저장** 을 선택합니다.

사용자 흐름의 모든 페이지를 브랜드 하려면 사용자 흐름에서 각 페이지 레이아웃에 대 한 페이지 레이아웃 버전을 설정 합니다.

![Azure Portal에서 Azure AD B2C 페이지 레이아웃 선택](media/customize-ui/portal-02-page-layout-select.png)

::: zone-end

::: zone pivot="b2c-custom-policy"

회사 브랜딩을 구성 했으면 사용자 지정 정책에서 사용 하도록 설정 합니다. [](contentdefinitions.md#migrating-to-page-layout) `contract` 사용자 지정 정책의 *모든* 콘텐츠 정의에 대해 페이지 버전으로 페이지 레이아웃 버전을 구성 합니다. 값 형식에는 `contract` _urn: com: microsoft: aad: b2c: elements:**contract**:p age-name: version_ 이라는 단어가 포함 되어야 합니다. 이전 **Datauri** 값을 사용 하는 사용자 지정 정책에서 페이지 레이아웃을 지정 합니다. 자세한 내용은 페이지 버전으로 [페이지 레이아웃으로 마이그레이션하](contentdefinitions.md#migrating-to-page-layout) 는 방법을 알아봅니다.

다음 예제에서는 해당 페이지 계약 및 *대양 Blue* 페이지 템플릿이 포함 된 콘텐츠 정의를 보여 줍니다. 

```xml
<ContentDefinitions>
  <ContentDefinition Id="api.error">
    <LoadUri>~/tenant/templates/AzureBlue/exception.cshtml</LoadUri>
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections">
    <LoadUri>~/tenant/templates/AzureBlue/idpSelector.cshtml</LoadUri>
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections.signup">
    <LoadUri>~/tenant/templates/AzureBlue/idpSelector.cshtml</LoadUri>
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.signuporsignin">
    <LoadUri>~/tenant/templates/AzureBlue/unified.cshtml</LoadUri>
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted">
    <LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted.profileupdate">
     <LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountsignup">
     <LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountpasswordreset">
     <LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.phonefactor">
    <LoadUri>~/tenant/templates/AzureBlue/multifactor-1.0.0.cshtml</LoadUri>
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.2.0</DataUri>
  </ContentDefinition>
</ContentDefinitions>
```

::: zone-end

## <a name="next-steps"></a>다음 단계

응용 프로그램의 사용자 인터페이스를 사용자 지정 하는 방법에 대 한 자세한 내용은 [Azure Active Directory B2C에서 응용 프로그램의 사용자 인터페이스 사용자 지정](customize-ui-with-html.md)을 확인 하세요.
