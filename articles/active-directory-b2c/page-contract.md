---
title: 페이지 계약-Azure Active Directory B2C를 선택 합니다.
description: Azure Active Directory B2C에서 페이지 계약을 선택하는 방법을 알아봅니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/04/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: c3ee05096b0bfd071ea569105973097ce9727b07
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67604536"
---
# <a name="select-a-page-contract-in-azure-active-directory-b2c-using-custom-policies"></a>사용자 지정 정책을 사용하여 Azure Active Directory B2C에서 페이지 계약 선택

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

사용자 흐름 또는 사용자 지정 정책을 사용 하는 Azure Active Directory (Azure AD) B2C 정책에서 JavaScript 클라이언트 쪽 코드를 사용할 수 있습니다. JavaScript 응용 프로그램을 사용 하려면 요소를 추가 해야 하 [사용자 지정 정책](active-directory-b2c-overview-custom.md), 페이지 계약을 선택 하 고 사용 하 여 [b2clogin.com](b2clogin.md) 요청에서.

페이지 계약은 Azure AD B2C에서 제공하는 요소와 사용자가 제공하는 콘텐츠를 합한 것입니다.

이 문서에서는 사용자 지정 정책에서 구성 하 여 Azure AD B2C에서 페이지 계약을 선택 하는 방법을 설명 합니다.

> [!NOTE]
> 사용자 흐름에 대 한 JavaScript를 사용 하도록 설정 하려는 경우 참조 [JavaScript 및 페이지에는 Azure Active Directory B2C에서 버전 계약](user-flow-javascript-overview.md)합니다.

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
| `urn:com:microsoft:aad:b2c:elements:claimsconsent:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:claimsconsent:1.0.0` |
| `urn:com:microsoft:aad:b2c:elements:globalexception:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.0.0` |
| `urn:com:microsoft:aad:b2c:elements:globalexception:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.1.0` |
| `urn:com:microsoft:aad:b2c:elements:idpselection:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.0.0` |
| `urn:com:microsoft:aad:b2c:elements:multifactor:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.0.0` |
| `urn:com:microsoft:aad:b2c:elements:multifactor:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.1.0` |
| `urn:com:microsoft:aad:b2c:elements:selfasserted:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.0.0` |
| `urn:com:microsoft:aad:b2c:elements:selfasserted:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.1.0` |
| `urn:com:microsoft:aad:b2c:elements:unifiedssd:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:unifiedssd:1.0.0` |
| `urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.0.0` |
| `urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.1.0` |

## <a name="version-change-log"></a>버전 변경 로그

페이지 계약 패키지 수정 및 개선 사항이 해당 페이지 요소에 포함 하도록 정기적으로 업데이트 됩니다. 다음 변경 로그에는 각 버전에 도입 된 변경 내용을 지정 합니다.

### <a name="110"></a>1.1.0

- 예외 페이지 (globalexception)
  - 내게 필요한 옵션 수정
  - 정책에서 연락처에 없는 경우 기본 메시지 제거
  - CSS 기본 제거
- MFA (다단계) 페이지
  - '코드 확인' 단추 제거 됨
  - 현재 수행 되는 코드에 대 한 입력된 필드에 최대 6 개의 문자 입력
  - 페이지는 모든 단추를 클릭할 필요가 없이 6 자리 코드를 입력 하면 입력 한 코드를 확인 하려고 자동으로
  - 코드 입력된 필드는 자동으로 선택 취소 한 다음 잘못 된 경우
  - 잘못 된 코드를 사용 하 여 3 회 시도 후 B2C 신뢰 당사자에 다시 오류를 전송
  - 내게 필요한 옵션 수정
  - CSS 기본 제거
- 자체 어설션된 페이지 (selfasserted)
  - 제거 취소 경고
  - 오류 요소에 대 한 CSS 클래스
  - 표시/숨기기 오류 논리가 향상
  - CSS 기본 제거
- 통합 된 SSP (unifiedssp)
  - 추가 유지 (KMSI) 컨트롤

### <a name="100"></a>1.0.0

- 최초 릴리스

## <a name="next-steps"></a>다음 단계

[Azure Active Directory B2C의 사용자 지정 정책을 사용하여 애플리케이션의 사용자 인터페이스 사용자 지정](active-directory-b2c-ui-customization-custom.md)에서 애플리케이션의 사용자 인터페이스를 사용자 지정하는 방법에 대한 자세한 정보를 확인합니다.
