---
title: 사용자 지정 정책을 사용하여 앱의 사용자 인터페이스 지역화
description: Azure Active Directory B2C에서 지역화 정책을 사용하여 사용자 인터페이스를 사용자 지정하는 방법을 알아봅니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/16/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: cdd7f4957b22eb4c87d60e3d57b0cec675659963
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92131805"
---
# <a name="localize-the-user-interface-of-your-application-using-a-custom-policy-in-azure-active-directory-b2c"></a>Azure Active Directory B2C에서 지역화 정책을 사용하여 애플리케이션의 사용자 인터페이스 사용자 지정

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure AD B2C(Azure Active Directory B2C)의 언어 사용자 지정을 사용하면 고객 요구에 적합한 다른 언어로 맞출 수 있습니다. Microsoft는 [36개 언어](https://docs.microsoft.com/azure/active-directory-b2c/user-flow-language-customization#supported-languages)에 대한 번역을 제공하지만 사용자가 언어에 대한 고유한 번역을 제공할 수 있습니다. 단일 언어로만 환경이 제공되더라도 페이지에 있는 텍스트를 사용자 지정할 수 있습니다. 

이 문서에서는 사용자 경험에 대한 정책에서 여러 로캘 또는 언어를 지원하는 방법을 보여 줍니다. 지역화에는 세 단계가 필요합니다. 지원되는 언어의 명시적 목록을 설정하고, 언어별 문자열 및 컬렉션을 제공하고, 페이지의 [콘텐츠 정의](contentdefinitions.md)를 편집합니다. 

## <a name="set-up-the-list-of-supported-languages"></a>지원되는 언어 목록 설정

정책의 확장 파일을 엽니다. 예를 들어 <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em>입니다.

1. [BuildingBlocks](buildingblocks.md) 요소를 검색합니다. 요소가 존재하지 않는 경우 추가합니다.
1. 지원되는 언어인 영어(기본값) 및 스페인어를 사용하여 `Localization` 요소를 추가합니다.  


```xml
<Localization Enabled="true">
  <SupportedLanguages DefaultLanguage="en" MergeBehavior="ReplaceAll">
    <SupportedLanguage>en</SupportedLanguage>
    <SupportedLanguage>es</SupportedLanguage>
  </SupportedLanguages>
</Localization>
```

## <a name="provide-language-specific-labels"></a>언어별 레이블 제공

`Localization` 요소의 [LocalizedResources](localization.md#localizedresources)에는 지역화된 문자열 목록이 포함되어 있습니다. 지역화된 리소스 요소에는 지역화된 리소스를 고유하게 식별하는 데 사용되는 식별자가 있습니다. 이 식별자는 나중에 [콘텐츠 정의](contentdefinitions.md) 요소에서 사용됩니다.

콘텐츠 정의에 대해 지역화된 리소스 요소 및 지원하려는 모든 언어를 구성합니다. 영어 및 스페인어의 통합 등록 또는 로그인 페이지를 사용자 지정하려면 `</SupportedLanguages>` 요소를 닫은 후 다음 `LocalizedResources` 요소를 추가합니다.

> [!NOTE]
> 다음 샘플에서는 각 줄의 시작에 파운드 `#` 기호를 추가하여 화면에서 지역화된 레이블을 편리하게 찾도록 했습니다.

```xml
<!--Local account sign-up or sign-in page English-->
<Localization Enabled="true">
  ...
  <LocalizedResources Id="api.signuporsignin.en">
    <LocalizedStrings>
      <LocalizedString ElementType="UxElement" StringId="logonIdentifier_email">#Email Address</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="requiredField_email">#Please enter your email</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="logonIdentifier_username">#Username</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="password">#Password</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="createaccount_link">#Sign up now</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="requiredField_username">#Please enter your user name</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="createaccount_intro">#Don't have an account?</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="forgotpassword_link">#Forgot your password?</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="divider_title">#OR</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="cancel_message">#The user has forgotten their password</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="button_signin">#Sign in</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="social_intro">#Sign in with your social account</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="requiredField_password">#Please enter your password</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="invalid_password">#The password you entered is not in the expected format.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="local_intro_username">#Sign in with your user name</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="local_intro_email">#Sign in with your existing account</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="invalid_email">#Please enter a valid email address</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="unknown_error">#We are having trouble signing you in. Please try again later.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="email_pattern">^[a-zA-Z0-9.!#$%&amp;'^_`{}~-]+@[a-zA-Z0-9-]+(?:\.[a-zA-Z0-9-]+)*$</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidPassword">#Your password is incorrect.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalDoesNotExist">#We can't seem to find your account.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfOldPasswordUsed">#Looks like you used an old password.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="DefaultMessage">#Invalid username or password.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfUserAccountDisabled">#Your account has been locked. Contact your support person to unlock it, then try again.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfUserAccountLocked">#Your account is temporarily locked to prevent unauthorized use. Try again later.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="AADRequestsThrottled">#There are too many requests at this moment. Please wait for some time and try again.</LocalizedString>
    </LocalizedStrings>
  </LocalizedResources>
  <!--Local account sign-up or sign-in page Spanish-->
  <LocalizedResources Id="api.signuporsignin.es">
    <LocalizedStrings>
      <LocalizedString ElementType="UxElement" StringId="logonIdentifier_email">#Correo electrónico</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="requiredField_email">#Este campo es obligatorio</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="logonIdentifier_username">#Nombre de usuario</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="password">#Contraseña</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="createaccount_link">#Registrarse ahora</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="requiredField_username">#Escriba su nombre de usuario</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="createaccount_intro">#¿No tiene una cuenta?</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="forgotpassword_link">#¿Olvidó su contraseña?</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="divider_title">#O</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="cancel_message">#El usuario ha olvidado su contraseña</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="button_signin">#Iniciar sesión</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="social_intro">#Iniciar sesión con su cuenta de redes sociales</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="requiredField_password">#Escriba su contraseña</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="invalid_password">#La contraseña que ha escrito no está en el formato esperado.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="local_intro_username">#Iniciar sesión con su nombre de usuario</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="local_intro_email">#Iniciar sesión con su cuenta existente</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="invalid_email">#Escriba una dirección de correo electrónico válida</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="unknown_error">#Tenemos problemas para iniciar su sesión. Vuelva a intentarlo más tarde.  </LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="email_pattern">^[a-zA-Z0-9.!#$%&amp;'^_`{}~-]+@[a-zA-Z0-9-]+(?:\.[a-zA-Z0-9-]+)*$</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidPassword">#Su contraseña es incorrecta.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalDoesNotExist">#Parece que no podemos encontrar su cuenta.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfOldPasswordUsed">#Parece que ha usado una contraseña antigua.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="DefaultMessage">#El nombre de usuario o la contraseña no son válidos.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfUserAccountDisabled">#Se bloqueó su cuenta. Póngase en contacto con la persona responsable de soporte técnico para desbloquearla y vuelva a intentarlo.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfUserAccountLocked">#Su cuenta se bloqueó temporalmente para impedir un uso no autorizado. Vuelva a intentarlo más tarde.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="AADRequestsThrottled">#Hay demasiadas solicitudes en este momento. Espere un momento y vuelva a intentarlo.</LocalizedString>
    </LocalizedStrings>
  </LocalizedResources>
  <!--Local account sign-up page English-->
  <LocalizedResources Id="api.localaccountsignup.en">
    <LocalizedStrings>
      <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">#Email Address</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">#Email address that can be used to contact you.</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">#Please enter a valid email address.</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="DisplayName">#New Password</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="UserHelpText">#Enter new password</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="PatternHelpText">#8-16 characters, containing 3 out of 4 of the following: Lowercase characters, uppercase characters, digits (0-9), and one or more of the following symbols: @ # $ % ^ &amp; * - _ + = [ ] { } | \ : ' , ? / ` ~ " ( ) ; .</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="DisplayName">#Confirm New Password</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="UserHelpText">#Confirm new password</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="PatternHelpText">#8-16 characters, containing 3 out of 4 of the following: Lowercase characters, uppercase characters, digits (0-9), and one or more of the following symbols: @ # $ % ^ &amp; * - _ + = [ ] { } | \ : ' , ? / ` ~ " ( ) ; .</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="displayName" StringId="DisplayName">#Display Name</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="displayName" StringId="UserHelpText">#Your display name.</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="surname" StringId="DisplayName">#Surname</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="surname" StringId="UserHelpText">#Your surname (also known as family name or last name).  </LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="givenName" StringId="DisplayName">#Given Name</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="givenName" StringId="UserHelpText">#Your given name (also known as first name).</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="button_continue">#Create</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="error_fieldIncorrect">#One or more fields are filled out incorrectly. Please check your entries and try again.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="error_passwordEntryMismatch">#The password entry fields do not match. Please enter the same password in both fields and try again.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="error_requiredFieldMissing">#A required field is missing. Please fill out all required fields and try again.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="helplink_text">#What is this?</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="initial_intro">#Please provide the following details.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="preloader_alt">#Please wait</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="required_field">#This information is required.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_edit">#Change e-mail</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_resend">#Send new code</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_send">#Send verification code</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_verify">#Verify code</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_code_expired">#That code is expired. Please request a new code.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_no_retry">#You've made too many incorrect attempts. Please try again later.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_retry">#That code is incorrect. Please try again.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_server">#We are having trouble verifying your email address. Please enter a valid email address and try again.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_throttled">#There have been too many requests to verify this email address. Please wait a while, then try again.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_info_msg">#Verification code has been sent to your inbox. Please copy it to the input box below.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_input">#Verification code</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_intro_msg">#Verification is necessary. Please click Send button.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_success_msg">#E-mail address verified. You can now continue.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="ServiceThrottled">#There are too many requests at this moment. Please wait for some time and try again.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimNotVerified">#Claim not verified: {0}</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">#A user with the specified ID already exists. Please choose a different one.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfIncorrectPattern">#Incorrect pattern for: {0}</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidInput">#{0} has invalid input.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfMissingRequiredElement">#Missing required element: {0}</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfValidationError">#Error in validation by: {0}</LocalizedString>
    </LocalizedStrings>
  </LocalizedResources>
  <!--Local account sign-up page Spanish-->
  <LocalizedResources Id="api.localaccountsignup.es">
    <LocalizedStrings>
      <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">#Dirección de correo electrónico</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">#Dirección de correo electrónico que puede usarse para ponerse en contacto con usted.</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">#Introduzca una dirección de correo electrónico válida.  </LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="DisplayName">#Nueva contraseña</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="UserHelpText">#Escriba la contraseña nueva</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="PatternHelpText">#De 8 a 16 caracteres, que contengan 3 de los 4 tipos siguientes: caracteres en minúsculas, caracteres en mayúsculas, dígitos (0-9) y uno o más de los siguientes símbolos: @ # $ % ^ &amp; * - _ + = [ ] { } | \\ : ' , ? / ` ~ \" ( ) ; .</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="DisplayName">#Confirmar nueva contraseña</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="UserHelpText">#Confirmar nueva contraseña</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="PatternHelpText">#8 a 16 caracteres, que contengan 3 de los 4 tipos siguientes: caracteres en minúsculas, caracteres en mayúsculas, dígitos (0-9) y uno o más de los siguientes símbolos: @ # $ % ^ &amp; * - _ + = [ ] { } | \\ : ' , ? / ` ~ \" ( ) ; .</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="displayName" StringId="DisplayName">#Nombre para mostrar</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="displayName" StringId="UserHelpText">#Su nombre para mostrar.</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="surname" StringId="DisplayName">#Apellido</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="surname" StringId="UserHelpText">#Su apellido.</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="givenName" StringId="DisplayName">#Nombre</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="givenName" StringId="UserHelpText">#Su nombre (también conocido como nombre de pila).</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="button_continue">#Crear</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="error_fieldIncorrect">#Hay uno o varios campos rellenados de forma incorrecta. Compruebe las entradas y vuelva a intentarlo.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="error_passwordEntryMismatch">#Los campos de entrada de contraseña no coinciden. Escriba la misma contraseña en ambos campos y vuelva a intentarlo.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="error_requiredFieldMissing">#Falta un campo obligatorio. Rellene todos los campos necesarios y vuelva a intentarlo.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="helplink_text">#¿Qué es esto?</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="initial_intro">#Proporcione los siguientes detalles.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="preloader_alt">#Espere</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="required_field">#Esta información es obligatoria.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_edit">#Cambiar correo electrónico</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_resend">#Enviar nuevo código</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_send">#Enviar código de comprobación</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_verify">#Comprobar código</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_code_expired">#El código ha expirado. Solicite otro nuevo.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_no_retry">#Ha realizado demasiados intentos incorrectos. Vuelva a intentarlo más tarde.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_retry">#Ese código es incorrecto. Inténtelo de nuevo.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_server">#Tenemos problemas para comprobar la dirección de correo electrónico. Escriba una dirección de correo electrónico válida y vuelva a intentarlo.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_throttled">#Ha habido demasiadas solicitudes para comprobar esta dirección de correo electrónico. Espere un poco y vuelva a intentarlo.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_info_msg">#Se ha enviado el código de verificación a su Bandeja de entrada. Cópielo en el siguiente cuadro de entrada.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_input">#Código de verificación</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_intro_msg">#La comprobación es obligatoria. Haga clic en el botón Enviar.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_success_msg">#Dirección de correo electrónico comprobada. Puede continuar.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="ServiceThrottled">#Hay demasiadas solicitudes en este momento. Espere un momento y vuelva a intentarlo.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimNotVerified">#Reclamación no comprobada: {0}</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">#Ya existe un usuario con el id. especificado. Elija otro diferente.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfIncorrectPattern">#Patrón incorrecto para: {0}</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidInput">#{0} tiene una entrada no válida.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfMissingRequiredElement">#Falta un elemento obligatorio: {0}</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfValidationError">#Error en la validación de: {0}</LocalizedString>
    </LocalizedStrings>
  </LocalizedResources>
</Localization>
```

## <a name="edit-the-content-definition-with-the-localization"></a>지역화를 사용하여 콘텐츠 정의 편집

복사한 ContentDefinitions의 전체 내용을 BuildingBlocks 요소의 자식으로 붙여넣습니다.

다음 예제에서는 영어(en) 및 스페인어(es) 사용자 지정 문자열이 등록 또는 로그인 페이지 및 로컬 계정 회원가입 페이지에 추가됩니다. 각 **LocalizedResourcesReference**의 **LocalizedResourcesReferenceId**는 해당 로캘과 동일하지만 임의 문자열을 식별자로 사용할 수 있습니다. 각 언어 및 페이지 조합에 대해 이전에 만든 해당 **LocalizedResources**를 가리킵니다.

```xml
<ContentDefinitions>
  <ContentDefinition Id="api.signuporsignin">
    <LocalizedResourcesReferences MergeBehavior="Prepend">
        <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.signuporsignin.en" />
        <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.signuporsignin.es" />
    </LocalizedResourcesReferences>
  </ContentDefinition>

  <ContentDefinition Id="api.localaccountsignup">
    <LocalizedResourcesReferences MergeBehavior="Prepend">
        <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.localaccountsignup.en" />
        <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.localaccountsignup.es" />
    </LocalizedResourcesReferences>
  </ContentDefinition>
</ContentDefinitions>
```

##  <a name="upload-and-test-your-updated-custom-policy"></a>업데이트된 사용자 지정 정책 업로드 및 테스트

### <a name="upload-the-custom-policy"></a>사용자 지정 정책 업로드

1. 확장 파일을 저장합니다.
1. Azure AD B2C 테넌트를 포함하는 디렉터리를 사용하려면 위쪽 메뉴에서 **디렉터리 + 구독** 필터를 선택하고, 테넌트가 포함된 디렉터리를 선택합니다.
1. **Azure AD B2C**를 검색하고 선택합니다.
1. **정책**에서 **Identity Experience Framework**를 선택합니다.
1. **사용자 지정 정책 업로드**를 선택합니다.
1. 이전에 변경한 확장 파일을 업로드합니다.

### <a name="test-the-custom-policy-by-using-run-now"></a>**지금 실행**을 사용하여 사용자 지정 정책 테스트

1. 업로드했던 정책을 선택한 다음 **지금 실행**을 선택합니다.
1. 지역화된 등록 또는 로그인 페이지가 표시됩니다.
1. 등록 링크를 클릭하면 지역화된 등록 페이지를 볼 수 있습니다.
1. 브라우저의 기본 언어를 스페인어로 전환합니다. 또는 쿼리 문자열 매개 변수 `ui_locales`를 권한 부여 요청에 추가할 수 있습니다. 다음은 그 예입니다. 

```http
https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/B2C_1A_signup_signin/oauth2/v2.0/authorize&client_id=0239a9cc-309c-4d41-12f1-31299feb2e82&nonce=defaultNonce&redirect_uri=https%3A%2F%2Fjwt.ms&scope=openid&response_type=id_token&prompt=login&ui_locales=es
```

## <a name="next-steps"></a>다음 단계

- IEF 참조에서 [지역화](localization.md) 요소에 대한 자세한 내용을 알아보세요.
- Azure AD B2C에서 사용할 수 있는 [지역화 문자열 ID](localization-string-ids.md) 목록을 참조하세요.

