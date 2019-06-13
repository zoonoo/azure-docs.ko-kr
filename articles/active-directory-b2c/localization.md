---
title: Localization - Azure Active Directory B2C | Microsoft Docs
description: Azure Active Directory B2C에서 사용자 지정 정책의 Localization 요소를 지정하는 방법을 설명합니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: a02983c5019870e8b17db48184b2f238a82f8a40
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66510590"
---
# <a name="localization"></a>지역화

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

**Localization** 요소를 사용하면 사용자 경험용 정책에서 여러 로캘이나 언어를 지원할 수 있습니다. 정책의 지역화 지원을 사용하여 다음을 수행할 수 있습니다.

- 정책에서 지원되는 언어의 명시적 목록을 설정하고 기본 언어를 선택합니다.
- 언어별 문자열 및 컬렉션을 제공합니다.

```XML
<Localization Enabled="true">
  <SupportedLanguages DefaultLanguage="en" MergeBehavior="ReplaceAll">
    <SupportedLanguage>en</SupportedLanguage>
    <SupportedLanguage>es</SupportedLanguage>
  </SupportedLanguages>
  <LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedResources Id="api.localaccountsignup.es">
  ...
```

**Localization** 요소에는 다음 특성이 포함됩니다.

| 특성 | 필수 | 설명 |
| --------- | -------- | ----------- |
| 사용 | 아닙니다. | 가능한 값은 `true` 또는 `false`입니다. |

**Localization** 요소에는 다음 XML 요소가 포함됩니다.

| 요소 | 발생 수 | 설명 |
| ------- | ----------- | ----------- |
| SupportedLanguages | 1:n | 지원되는 언어 목록입니다. | 
| LocalizedResources | 0:n | 지역화된 리소스 목록입니다. |

## <a name="supportedlanguages"></a>SupportedLanguages

**SupportedLanguages** 요소에는 다음 특성이 포함됩니다.

| 특성 | 필수 | 설명 |
| --------- | -------- | ----------- |
| DefaultLanguage | 예 | 지역화된 리소스의 기본값으로 사용할 언어입니다. |
| MergeBehavior | 아닙니다. | 동일한 식별자를 가진, 부모 정책에 있는 모든 ClaimType과 함께 병합되는 값의 열거형 값입니다. 기본 정책에 지정된 클레임을 덮어쓰는 경우 이 특성을 사용합니다. 가능한 값은 `Append`, `Prepend` 또는 `ReplaceAll`입니다. `Append` 값은 부모 정책에 지정된 컬렉션의 끝에 데이터 컬렉션을 추가하도록 지정합니다. `Prepend` 값은 부모 정책에 지정된 컬렉션 앞에 데이터 컬렉션을 추가하도록 지정합니다. `ReplaceAll` 값은 부모 정책에 정의된 데이터 컬렉션을 무시하고 현재 정책에 정의된 데이터를 대신 사용하도록 지정합니다. |

### <a name="supportedlanguages"></a>SupportedLanguages

**SupportedLanguages** 요소에는 다음 요소가 포함됩니다.

| 요소 | 발생 수 | 설명 |
| ------- | ----------- | ----------- |
| SupportedLanguage | 1:n | RFC 5646 - 언어 식별 태그를 기준으로 언어 태그를 준수하는 콘텐츠를 표시합니다. | 

## <a name="localizedresources"></a>LocalizedResources

**LocalizedResources** 요소에는 다음 특성이 포함됩니다.

| 특성 | 필수 | 설명 |
| --------- | -------- | ----------- |
| Id | 예 | 지역화된 리소스를 고유하게 식별하는 데 사용되는 식별자입니다. |

**LocalizedResources** 요소에는 다음 요소가 포함됩니다.

| 요소 | 발생 수 | 설명 |
| ------- | ----------- | ----------- |
| LocalizedCollections | 0:n | 다양한 문화권에서 전체 컬렉션을 정의합니다. 컬렉션은 문화권마다 여러 항목과 다른 문자열을 포함할 수 있습니다. 컬렉션의 예로는 클레임 유형에 표시되는 열거형이 있습니다. 예를 들어 국가/지역 목록이 드롭다운 목록을 통해 사용자에게 표시됩니다. |
| LocalizedStrings | 0:n | 다양한 문화권에서 컬렉션에 표시되는 문자열을 제외한 모든 문자열을 정의합니다. |

### <a name="localizedcollections"></a>LocalizedCollections

**LocalizedCollections** 요소에는 다음 요소가 포함됩니다.

| 요소 | 발생 수 | 설명 |
| ------- | ----------- | ----------- |
| LocalizedCollection | 1:n | 지원되는 언어 목록입니다. |

#### <a name="localizedcollection"></a>LocalizedCollection

**LocalizedCollection** 요소에는 다음 특성이 포함됩니다.

| 특성 | 필수 | 설명 |
| --------- | -------- | ----------- |
| ElementType | 예 | 정책 파일의 ClaimType 요소 또는 사용자 인터페이스 요소를 참조합니다. |
| ElementId | 예 | **ElementType**이 ClaimType으로 설정된 경우 사용하는 ClaimsSchema 섹션에 이미 정의된 클레임 유형에 대한 참조를 포함하는 문자열입니다. |
| TargetCollection | 예 | 대상 컬렉션입니다. |

**LocalizedCollection** 요소에는 다음 요소가 포함됩니다.

| 요소 | 발생 수 | 설명 |
| ------- | ----------- | ----------- |
| 항목 | 0:n | 드롭다운의 값과 같이 사용자 인터페이스에서 사용자가 클레임에 대해 선택할 수 있는 옵션을 정의합니다. |

**Item** 요소에는 다음 특성이 포함됩니다.

| 특성 | 필수 | 설명 |
| --------- | -------- | ----------- |
| 텍스트 | 예 | 사용자 인터페이스에서 이 옵션에 대해 사용자에게 표시되어야 하는 사용자에게 친숙한 표시 문자열입니다. |
| 값 | 예 | 이 옵션 선택과 연결된 문자열 클레임 값입니다. |

다음 예제는 **LocalizedCollections** 요소의 사용을 보여 줍니다. 각각 영어와 스페인어로 된 두 개의 **LocalizedCollection** 요소를 포함합니다. 둘 다 영어 및 스페인어 항목 목록을 사용하여 `Gender` 클레임의 **Restriction** 컬렉션을 설정합니다.

```XML
<LocalizedResources Id="api.selfasserted.en">
 <LocalizedCollections>
   <LocalizedCollection ElementType="ClaimType" ElementId="Gender" TargetCollection="Restriction">
      <Item Text="Female" Value="F" />
      <Item Text="Male" Value="M" />
    </LocalizedCollection>
</LocalizedCollections>

<LocalizedResources Id="api.selfasserted.es">
 <LocalizedCollections>
   <LocalizedCollection ElementType="ClaimType" ElementId="Gender" TargetCollection="Restriction">
      <Item Text="Femenino" Value="F" />
      <Item Text="Masculino" Value="M" />
    </LocalizedCollection>
</LocalizedCollections>

```

### <a name="localizedstrings"></a>LocalizedStrings

**LocalizedStrings** 요소에는 다음 요소가 포함됩니다.

| 요소 | 발생 수 | 설명 |
| ------- | ----------- | ----------- |
| LocalizedString | 1:n | 지역화된 문자열입니다. |

**LocalizedString** 요소에는 다음 특성이 포함됩니다.

| 특성 | 필수 | 설명 |
| --------- | -------- | ----------- |
| ElementType | 예 | 정책의 클레임 유형 요소 또는 사용자 인터페이스 요소에 대한 참조입니다. 가능한 값은 `ClaimType`, `UxElement`, `ErrorMessage`, `Predicate` 등입니다. `ClaimType` 값은 StringId에 지정된 대로 클레임 특성 중 하나를 지역화하는 데 사용됩니다. `UxElement` 값은 StringId에 지정된 대로 사용자 인터페이스 요소 중 하나를 지역화하는 데 사용됩니다. `ErrorMessage` 값은 StringId에 지정된 대로 시스템 오류 메시지 중 하나를 지역화하는 데 사용됩니다. `Predicate` 값은 StringId에 지정된 대로 [Predicate](predicates.md) 오류 메시지 중 하나를 지역화하는 데 사용됩니다. `InputValidation` 값은 StringId에 지정된 대로 [PredicateValidation](predicates.md) 그룹 오류 메시지 중 하나를 지역화하는 데 사용됩니다. |
| ElementId | 예 | **ElementType**이 `ClaimType`, `Predicate` 또는 `InputValidation`으로 설정된 경우 이 요소는 ClaimsSchema 섹션에 이미 정의된 클레임 유형에 대한 참조를 포함합니다. | 
| StringId | 예 | **ElementType**이 `ClaimType`으로 설정된 경우 이 요소는 클레임 유형의 특성에 대한 참조를 포함합니다. 가능한 값은 `DisplayName`, `AdminHelpText` 또는 `PatternHelpText`입니다. `DisplayName` 값은 클레임 표시 이름을 설정하는 데 사용됩니다. `AdminHelpText` 값은 클레임 사용자의 도움말 텍스트 이름을 설정하는 데 사용됩니다. `PatternHelpText` 값은 클레임 패턴 도움말 텍스트를 설정하는 데 사용됩니다. **ElementType**이 `UxElement`로 설정된 경우 이 요소는 사용자 인터페이스 요소의 특성에 대한 참조를 포함합니다. **ElementType**이 `ErrorMessage`로 설정된 경우 이 요소는 오류 메시지의 식별자를 지정합니다. `UxElement` 식별자의 전체 목록은 [지역화 문자열 ID](localization-string-ids.md)를 참조하세요.|


다음 예제는 지역화된 등록 페이지를 보여 줍니다. 처음 세 개의 **LocalizedString** 값은 클레임 특성을 설정합니다. 세 번째 값은 계속 단추의 값을 변경합니다. 마지막 값은 오류 메시지를 변경합니다.

```XML
<LocalizedResources Id="api.selfasserted.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">Email</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">Please enter your email</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">Please enter a valid email address</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="button_continue">Create new account</LocalizedString>
   <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">The account you are trying to create already exists, please sign-in.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

다음 예제는 ID가 `IsLengthBetween8And64`인 **Predicate**의 지역화된 **UserHelpText**를 보여 줍니다. 그리고 ID가 `StrongPassword`인 **PredicateValidation**에서 ID가 `CharacterClasses`인 **PredicateGroup**의 지역화된 **UserHelpText**를 보여 줍니다.

```XML
<PredicateValidation Id="StrongPassword">
  <PredicateGroups>
    ...
    <PredicateGroup Id="CharacterClasses">
    ...
    </PredicateGroup>
  </PredicateGroups>
</PredicateValidation>

...

<Predicate Id="IsLengthBetween8And64" Method="IsLengthRange">
  ...
</Predicate>
...


<LocalizedString ElementType="InputValidation" ElementId="StrongPassword" StringId="CharacterClasses">The password must have at least 3 of the following:</LocalizedString>

<LocalizedString ElementType="Predicate" ElementId="IsLengthBetween8And64" StringId="HelpText">The password must be between 8 and 64 characters.</LocalizedString>              
```

## <a name="set-up-localization"></a>지역화 설정

이 문서에서는 사용자 경험에 대한 정책에서 여러 로캘 또는 언어를 지원하는 방법을 보여 줍니다. 지역화에는 세 단계가 필요합니다. 지원되는 언어의 명시적 목록을 설정하고, 언어별 문자열 및 컬렉션을 제공하고, 페이지의 ContentDefinition을 편집합니다.

### <a name="set-up-the-explicit-list-of-supported-languages"></a>지원되는 언어의 명시적 목록 설정

**BuildingBlocks** 요소 아래에 지원되는 언어 목록이 포함된 **지역화** 요소를 추가합니다. 다음 예제는 영어(기본값) 및 스페인어 둘 다에 대한 지역화 지원을 정의하는 방법을 보여 줍니다.

```XML
<Localization Enabled="true">
  <SupportedLanguages DefaultLanguage="en" MergeBehavior="ReplaceAll">
    <SupportedLanguage>en</SupportedLanguage>
    <SupportedLanguage>es</SupportedLanguage>
  </SupportedLanguages>
</Localization>
```

### <a name="provide-language-specific-strings-and-collections"></a>언어별 문자열 및 컬렉션 제공 

**SupportedLanguages** 요소를 닫은 후 **Localization** 요소 내부에 **LocalizedResources** 요소를 추가합니다. 각 페이지(콘텐츠 정의) 및 지원하려는 모든 언어에 대해 **LocalizedResources** 요소를 추가합니다. 영어, 스페인어 및 프랑스어의 통합 등록 또는 로그인 페이지, 등록 및 MFA(다단계 인증) 페이지를 사용자 지정하려면 다음 **LocalizedResources** 요소를 추가합니다.  
- 통합 등록 또는 로그인 페이지, 영어 `<LocalizedResources Id="api.signuporsignin.en">`
- 통합 등록 또는 로그인 페이지, 스페인어 `<LocalizedResources Id="api.signuporsignin.es">`
- 통합 등록 또는 로그인 페이지, 프랑스어 `<LocalizedResources Id="api.signuporsignin.fr">` 
- 등록, 영어 `<LocalizedResources Id="api.localaccountsignup.en">`
- 등록, 스페인어 `<LocalizedResources Id="api.localaccountsignup.es">`
- 등록, 프랑스어 `<LocalizedResources Id="api.localaccountsignup.fr">`
- MFA, 영어 `<LocalizedResources Id="api.phonefactor.en">`
- MFA, 스페인어 `<LocalizedResources Id="api.phonefactor.es">`
- MFA, 프랑스어 `<LocalizedResources Id="api.phonefactor.fr">`

각 **LocalizedResources** 요소에는 다중 **LocalizedStrings** 요소가 있는 필수 **LocalizedStrings** 요소와 다중 **LocalizedCollection** 요소가 있는 **LocalizedCollections** 요소가 모두 포함됩니다.  다음 예제는 등록 페이지 영어 지역화를 추가합니다. 

참고: 이 예제는 `Gender` 및 `City` 클레임 유형에 대한 참조를 작성합니다. 이 예제를 사용하려면 해당 클레임을 정의해야 합니다. 자세한 내용은 [ClaimsSchema](claimsschema.md)를 참조하세요.

```XML
<LocalizedResources Id="api.localaccountsignup.en">

 <LocalizedCollections>
   <LocalizedCollection ElementType="ClaimType" ElementId="Gender" TargetCollection="Restriction">
      <Item Text="Female" Value="F" />
      <Item Text="Male" Value="M" />
    </LocalizedCollection>
   <LocalizedCollection ElementType="ClaimType" ElementId="City" TargetCollection="Restriction">
      <Item Text="New York" Value="NY" />
      <Item Text="Paris" Value="Paris" />
      <Item Text="London" Value="London" />
    </LocalizedCollection>
  </LocalizedCollections>

  <LocalizedStrings>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">Email</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">Please enter your email</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">Please enter a valid email address</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="button_continue">Create new account</LocalizedString>
   <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">The account you are trying to create already exists, please sign-in.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

스페인어 등록 페이지 지역화입니다.

```XML
<LocalizedResources Id="api.localaccountsignup.es">

 <LocalizedCollections>
   <LocalizedCollection ElementType="ClaimType" ElementId="Gender" TargetCollection="Restriction">
      <Item Text="Femenino" Value="F" />
      <Item Text="Masculino" Value="M" />
    </LocalizedCollection>
   <LocalizedCollection ElementType="ClaimType" ElementId="City" TargetCollection="Restriction">
      <Item Text="Nueva York" Value="NY" />
      <Item Text="París" Value="Paris" />
      <Item Text="Londres" Value="London" />
    </LocalizedCollection>
  </LocalizedCollections>

  <LocalizedStrings>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">Dirección de correo electrónico</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">Dirección de correo electrónico que puede usarse para ponerse en contacto con usted.</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">Introduzca una dirección de correo electrónico.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="button_continue">Crear</LocalizedString>
   <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">Ya existe un usuario con el id. especificado. Elija otro diferente.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

### <a name="edit-the-contentdefinition-for-the-page"></a>페이지에 대한 ContentDefinition 편집 

지역화하려는 각 페이지에 대해 **ContentDefinition**에서 찾을 언어 코드를 지정합니다.

다음 예제에서는 영어(en) 및 스페인어(es) 사용자 지정 문자열이 등록 페이지에 추가됩니다. 각 **LocalizedResourcesReference**의 **LocalizedResourcesReferenceId**는 해당 로캘과 동일하지만 임의 문자열을 식별자로 사용할 수 있습니다. 각 언어 및 페이지 조합에 대해 이전에 만든 해당 **LocalizedResources**를 가리킵니다.

```XML
<ContentDefinition Id="api.localaccountsignup">
...
  <LocalizedResourcesReferences MergeBehavior="Prepend">
    <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.localaccountsignup.en" />
    <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.localaccountsignup.es" />
  </LocalizedResourcesReferences>
</ContentDefinition>
```

다음 예제는 최종 XML을 보여 줍니다.

```XML
<BuildingBlocks>
  <ContentDefinitions>
    <ContentDefinition Id="api.localaccountsignup">
      <!-- Other content definitions elements... -->
      <LocalizedResourcesReferences MergeBehavior="Prepend">
         <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.localaccountsignup.en" />
         <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.localaccountsignup.es" />
      </LocalizedResourcesReferences>
    </ContentDefinition>
    <!-- More content definitions... -->
  </ContentDefinitions>

  <Localization Enabled="true">

    <SupportedLanguages DefaultLanguage="en" MergeBehavior="ReplaceAll">
      <SupportedLanguage>en</SupportedLanguage>
      <SupportedLanguage>es</SupportedLanguage>
      <!-- More supported language... -->
    </SupportedLanguages>

    <LocalizedResources Id="api.localaccountsignup.en">
      <LocalizedCollections>
        <LocalizedCollection ElementType="ClaimType" ElementId="Gender" TargetCollection="Restriction">
          <Item Text="Female" Value="F" />
          <Item Text="Male" Value="M" />
          <!-- More items... -->
        </LocalizedCollection>
        <LocalizedCollection ElementType="ClaimType" ElementId="City" TargetCollection="Restriction">
          <Item Text="New York" Value="NY" />
          <Item Text="Paris" Value="Paris" />
          <Item Text="London" Value="London" />
        </LocalizedCollection>
        <!-- More localized collections... -->
      </LocalizedCollections>
      <LocalizedStrings>
        <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">Email</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">Please enter your email</LocalizedString>
        <LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">Please enter a valid email address</LocalizedString>
        <LocalizedString ElementType="UxElement" StringId="button_continue">Create new account</LocalizedString>
       <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">The account you are trying to create already exists, please sign-in.</LocalizedString>
        <!-- More localized strings... -->
      </LocalizedStrings>
    </LocalizedResources>

    <LocalizedResources Id="api.localaccountsignup.es">
      <LocalizedCollections>
       <LocalizedCollection ElementType="ClaimType" ElementId="Gender" TargetCollection="Restriction">
          <Item Text="Femenino" Value="F" />
          <Item Text="Masculino" Value="M" />
        </LocalizedCollection>
        <LocalizedCollection ElementType="ClaimType" ElementId="City" TargetCollection="Restriction">
          <Item Text="Nueva York" Value="NY" />
          <Item Text="París" Value="Paris" />
          <Item Text="Londres" Value="London" />
        </LocalizedCollection>
      </LocalizedCollections>
      <LocalizedStrings>
        <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">Dirección de correo electrónico</LocalizedString>
        <LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">Dirección de correo electrónico que puede usarse para ponerse en contacto con usted.</LocalizedString>
        <LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">Introduzca una dirección de correo electrónico.</LocalizedString>
        <LocalizedString ElementType="UxElement" StringId="button_continue">Crear</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">Ya existe un usuario con el id. especificado. Elija otro diferente.</LocalizedString>
      </LocalizedStrings>
    </LocalizedResources>
    <!-- More localized resources... -->
  </Localization>
</BuildingBlocks>
```




