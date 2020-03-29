---
title: 앱의 게시자 도메인 구성 | Azure
titleSuffix: Microsoft identity platform
description: 사용자에게 정보가 전송되는 위치를 알 수 있도록 응용 프로그램의 게시자 도메인을 구성하는 방법을 알아봅니다.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/05/2019
ms.author: ryanwi
ms.reviewer: lenalepa, sureshja, zachowd
ms.custom: aaddev
ms.openlocfilehash: 68040c8ee22454c300296493b6c840eabbca98aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76697135"
---
# <a name="how-to-configure-an-applications-publisher-domain"></a>방법: 응용 프로그램의 게시자 도메인 구성

응용 프로그램의 게시자 도메인은 사용자에게 자신의 정보가 전송되는 위치를 알려주는 [응용 프로그램의 동의 프롬프트에](application-consent-experience.md) 사용자에게 표시됩니다. 게시자 도메인이 없는 2019년 5월 21일 이후에 등록된 다중 테넌트 응용 프로그램은 **확인되지 않은**것으로 표시됩니다. 다중 테넌트 응용 프로그램은 단일 조직 디렉터리 외부의 계정을 지원하는 응용 프로그램입니다. 예를 들어 모든 Azure AD 계정을 지원하거나 모든 Azure AD 계정 및 개인 Microsoft 계정을 지원합니다.

## <a name="new-applications"></a>새로운 응용 프로그램

새 앱을 등록하면 앱의 게시자 도메인이 기본값으로 설정될 수 있습니다. 값은 앱이 등록된 위치, 특히 앱이 테넌트에 등록되어 있는지 여부와 테넌트가 도메인을 확인했는지 여부에 따라 달라집니다.

테넌트에서 확인된 도메인이 있는 경우 앱의 게시자 도메인은 테넌트의 기본 인증 도메인으로 기본설정됩니다. 테넌트가 확인된 도메인이 없는 경우(응용 프로그램이 테넌트에 등록되지 않은 경우) 앱의 게시자 도메인이 null로 설정됩니다.

다음 표에는 게시자 도메인 값의 기본 동작이 요약됩니다.  

| 테넌트 확인 도메인 | 게시자 도메인의 기본값 |
|-------------------------|----------------------------|
| null | null |
| *.onmicrosoft.com | *.onmicrosoft.com |
| - *.onmicrosoft.com<br/>- domain1.com<br/>- domain2.com (기본) | domain2.com |

다중 테넌트 응용 프로그램의 게시자 도메인이 설정되지 않았거나 .onmicrosoft.com 끝나는 도메인으로 설정된 경우 앱의 동의 프롬프트가 게시자 도메인 대신 **확인되지 않은** 것으로 표시됩니다.

## <a name="grandfathered-applications"></a>할아버지 응용 프로그램

2019년 5월 21일 이전에 앱을 등록한 경우 게시자 도메인을 설정하지 않은 경우 응용 프로그램의 동의 프롬프트가 **확인되지 않음을** 표시하지 않습니다. 사용자가 앱의 동의 프롬프트에서 이 정보를 볼 수 있도록 게시자 도메인 값을 설정하는 것이 좋습니다.

## <a name="configure-publisher-domain-using-the-azure-portal"></a>Azure 포털을 사용하여 게시자 도메인 구성

앱의 게시자 도메인을 설정하려면 다음 단계를 따르세요.

1. 회사 또는 학교 계정 또는 개인 Microsoft 계정을 사용하여 [Azure 포털에](https://portal.azure.com) 로그인합니다.

1. 계정이 여러 Azure AD 테넌트에 있는 경우:
   1. 페이지의 오른쪽 위 모서리에 있는 메뉴에서 프로필을 선택한 다음, **디렉터리를 전환**합니다.
   1. 애플리케이션을 만들려는 Azure AD 테넌트로 세션을 변경합니다.

1. Azure [Active Directory > 앱 등록으로](https://go.microsoft.com/fwlink/?linkid=2083908) 이동하여 구성할 앱을 찾고 선택합니다.

   앱을 선택하면 앱의 **개요** 페이지가 표시됩니다.

1. 앱의 **개요** 페이지에서 **브랜딩** 섹션을 선택합니다.

1. **게시자 도메인** 필드를 찾아 다음 옵션 중 하나를 선택합니다.

   - 아직 도메인을 구성하지 않은 경우 도메인 **구성을** 선택합니다.
   - **도메인이** 이미 구성된 경우 도메인 업데이트를 선택합니다.

앱이 테넌트에 등록된 경우 **확인된 도메인을 선택하고** **새 도메인을 확인합니다.**

앱이 테넌트에 등록되지 않은 경우 응용 프로그램에 대한 새 도메인을 확인하는 옵션만 표시됩니다.

### <a name="to-verify-a-new-domain-for-your-app"></a>앱의 새 도메인을 확인하려면

1. 명명된 `microsoft-identity-association.json` 파일을 만들고 다음 JSON 코드 조각을 붙여넣습니다.

   ```json
   {
      "associatedApplications": [
        {
           "applicationId": "{YOUR-APP-ID-HERE}"
        }
      ]
    }
   ```

1. 자리 표시자 *{YOUR-APP-ID-HERE}를* 앱에 해당하는 응용 프로그램(클라이언트) ID로 바꿉니다.

1. 다음에서 파일을 `https://{YOUR-DOMAIN-HERE}.com/.well-known/microsoft-identity-association.json`호스트합니다. 확인된 도메인과 일치하도록 자리 표시자 *{YOUR-DOMAIN-HERE}를* 바꿉습니다.

1. 도메인 **확인 및 저장** 버튼을 클릭합니다.

### <a name="to-select-a-verified-domain"></a>확인된 도메인을 선택하려면

- 테넌트가 도메인을 확인한 경우 확인된 도메인 드롭다운 **선택에서** 도메인 중 하나를 선택합니다.

>[!Note]
> 반환해야 하는 예상되는 '콘텐츠 유형' `application/json`헤더는 . 아래에서 언급한 대로 다음과 같은 다른 것을 사용하는 경우 오류가 발생할 수 있습니다.`application/json; charset=utf-8` 
> 
>``` "Verification of publisher domain failed. Error getting JSON file from https:///.well-known/microsoft-identity-association. The server returned an unexpected content type header value. " ```
>

## <a name="implications-on-the-app-consent-prompt"></a>앱 동의 프롬프트에 대한 의미

게시자 도메인을 구성하면 앱 동의 프롬프트에 표시되는 내용에 영향을 미칩니다. 동의 프롬프트의 구성 요소를 완전히 이해하려면 [응용 프로그램 동의 환경 이해를](application-consent-experience.md)참조하십시오.

다음 표에서는 2019년 5월 21일 이전에 만든 응용 프로그램의 동작에 대해 설명합니다.

![2019년 5월 21일 이전에 생성된 앱에 대한 동의 프롬프트](./media/howto-configure-publisher-domain/old-app-behavior-table.png)

2019년 5월 21일 이후에 생성된 새 응용 프로그램의 동작은 게시자 도메인 및 응용 프로그램 유형에 따라 달라집니다. 다음 표에서는 구성의 다양한 조합으로 예상되는 변경 사항에 대해 설명합니다.

![2019년 5월 21일 이후에 생성된 앱에 대한 동의 프롬프트](./media/howto-configure-publisher-domain/new-app-behavior-table.png)

## <a name="implications-on-redirect-uris"></a>리디렉션 URI에 미치는 영향

사용자 또는 학교 계정 또는 개인 Microsoft[계정(다중 테넌트)으로](single-and-multi-tenant-apps.md)사용자를 로그인하는 응용 프로그램은 리디렉션 URI를 지정할 때 몇 가지 제한 사항이 적용됩니다.

### <a name="single-root-domain-restriction"></a>단일 루트 도메인 제한

다중 테넌트 앱의 게시자 도메인 값이 null로 설정되면 앱은 리디렉션 URI에 대한 단일 루트 도메인을 공유하도록 제한됩니다. 예를 들어 contoso.com 루트 도메인이 fabrikam.com 일치하지 않기 때문에 다음 값 조합이 허용되지 않습니다.

```
"https://contoso.com",
"https://fabrikam.com",
```

### <a name="subdomain-restrictions"></a>하위 도메인 제한

하위 도메인은 허용되지만 루트 도메인을 명시적으로 등록해야 합니다. 예를 들어 다음 URI는 단일 루트 도메인을 공유하지만 조합은 허용되지 않습니다.

```
"https://app1.contoso.com",
"https://app2.contoso.com",
```

그러나 개발자가 루트 도메인을 명시적으로 추가하면 조합이 허용됩니다.

```
"https://contoso.com",
"https://app1.contoso.com",
"https://app2.contoso.com",
```

### <a name="exceptions"></a>예외

다음 경우는 단일 루트 도메인 제한이 적용되지 않습니다.

- 단일 테넌트 앱 또는 단일 디렉터리에서 계정을 대상으로 하는 앱
- 리디렉션 URI로 로컬 호스트 사용
- 사용자 지정 스키마를 통해 URI 리디렉션(HTTP 또는 HTTPS가 아닌 경우)

## <a name="configure-publisher-domain-programmatically"></a>게시자 도메인을 프로그래밍 방식으로 구성

현재 게시자 도메인을 프로그래밍 방식으로 구성하는 REST API 또는 PowerShell 지원은 없습니다.
