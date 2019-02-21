---
title: Azure Active Directory B2C에서 페이지 계약 선택 | Microsoft Docs
description: Azure Active Directory B2C에서 페이지 계약을 선택하는 방법을 알아봅니다.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: fa48d3825b5b942497b1eabd19a1eeb7e0141058
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/11/2019
ms.locfileid: "56003145"
---
# <a name="select-a-page-contract-in-azure-active-directory-b2c-using-custom-policies"></a>사용자 지정 정책을 사용하여 Azure Active Directory B2C에서 페이지 계약 선택

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

사용자 흐름 또는 사용자 지정 정책을 사용하든 관계 없이 Azure AD(Azure Active Directory) B2C 정책에서 JavaScript 클라이언트 쪽 코드를 사용하도록 설정할 수 있습니다. 이 문서에서는 [사용자 지정 정책](active-directory-b2c-overview-custom.md)에서 구성하여 Azure AD B2C에서 페이지 계약을 선택하는 방법을 설명합니다. 페이지 계약은 Azure AD B2C에서 제공하는 요소와 사용자가 제공하는 콘텐츠를 합한 것입니다. [Javascript](javascript-samples.md)를 사용하려면 사용자 지정 정책의 모든 콘텐츠 정의에 대해 페이지 계약 버전을 정의해야 합니다.

> [!NOTE]
> 사용자 흐름에 대해 JavaScript를 사용하도록 설정하려는 경우 [사용자 흐름에서 JavaScript 및 페이지 계약 버전 사용 정보](user-flow-javascript-overview.md)를 참조하세요.

## <a name="replace-datauri-values"></a>DataUri 값 바꾸기

사용자 지정 정책에서 사용자 경험에 사용되는 HTML 템플릿을 정의하는 [ContentDefinitions](contentdefinitions.md)이 있을 수 있습니다. **ContentDefinition**에는 Azure AD B2C에서 제공하는 페이지 요소를 참조하는 **DataUri**가 포함되어 있습니다. **LoadUri**는 사용자가 제공하는 HTML 및 CSS 콘텐츠의 상대 경로입니다.

```XML
<ContentDefinition Id="api.idpselections">
  <LoadUri>~/tenant/default/idpSelector.cshtml</LoadUri>
  <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
  <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.0.0</DataUri>
  <Metadata>
    <Item Key="DisplayName">Idp selection page</Item>
    <Item Key="language.intro">Sign in</Item>
  </Metadata>
</ContentDefinition>
```

페이지 계약을 선택하려면 정책의 [ContentDefinitions](contentdefinitions.md)에서 **DataUri** 값을 변경합니다. 이전 **DataUri** 값을 새 값을 전환하면 변경할 수 없는 패키지를 선택하는 것입니다. 이 패키지를 사용할 때의 이점은 아시다시피 변경되지 않으므로 페이지에 예기치 않은 동작이 발생하지 않는다는 것입니다. 

페이지 계약을 설정하려면 다음 표를 사용하여 **DataUri** 값을 찾습니다. 

| 이전 DataUri 값 | 새 DataUri 값 |
| ----------------- | ----------------- |
| urn:com:microsoft:aad:b2c:elements:idpselection:1.0.0 | urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.0.0 |
| urn:com:microsoft:aad:b2c:elements:unifiedssd:1.0.0 | urn:com:microsoft:aad:b2c:elements:contract:unifiedssd:1.0.0 | 
| urn:com:microsoft:aad:b2c:elements:claimsconsent:1.0.0 | urn:com:microsoft:aad:b2c:elements:contract:claimsconsent:1.0.0 |
| urn:com:microsoft:aad:b2c:elements:multifactor:1.0.0 | urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.0.0 |
| urn:com:microsoft:aad:b2c:elements:multifactor:1.1.0 | urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.1.0 |
| urn:com:microsoft:aad:b2c:elements:selfasserted:1.0.0 | urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.0.0 |
| urn:com:microsoft:aad:b2c:elements:selfasserted:1.1.0 | urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.1.0 | 
| urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0 | urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.0.0 |
| urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0 | urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.1.0 |
| urn:com:microsoft:aad:b2c:elements:globalexception:1.0.0 | urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.0.0 |
| urn:com:microsoft:aad:b2c:elements:globalexception:1.1.0 | urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.1.0 |

## <a name="next-steps"></a>다음 단계

[Azure Active Directory B2C의 사용자 지정 정책을 사용하여 애플리케이션의 사용자 인터페이스 사용자 지정](active-directory-b2c-ui-customization-custom.md)에서 애플리케이션의 사용자 인터페이스를 사용자 지정하는 방법에 대한 자세한 정보를 확인합니다.



