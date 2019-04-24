---
title: 응용 프로그램의 게시자 도메인 구성 | Azure
description: 사용자는 해당 정보가 전송 되는지 알 수 있도록 응용 프로그램의 게시자 도메인을 구성 하는 방법에 알아봅니다.
services: active-directory
documentationcenter: dev-center-name
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/05/2019
ms.author: celested
ms.reviewer: lenalepa, sureshja, zachowd
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: efbf448770bfcf797d6bf01cd3c28dc98023adff
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60299789"
---
# <a name="how-to-configure-an-applications-publisher-domain-preview"></a>방법: 응용 프로그램의 게시자 도메인 (미리 보기)를 구성 합니다.

응용 프로그램의 게시자 도메인에 사용자에 게 표시 되는 [응용 프로그램의 동의 확인 프롬프트](application-consent-experience.md) 해당 정보가 전송 되는 사용자에 게 알립니다. 다중 테 넌 트 응용 프로그램 2019 년 5 월 21 일 후 등록 된 게시자 도메인 없는로 표시 **확인 되지 않은**합니다. 다중 테 넌 트 응용 프로그램은 단일 조직 디렉터리; 외부 계정을 지 원하는 응용 프로그램 예를 들어, 모든 Azure AD 계정이 지원 또는 Azure AD 계정 및 개인 Microsoft 계정 모두 지원 합니다.

## <a name="new-applications"></a>새 응용 프로그램

새 앱을 등록 하면 앱의 게시자 도메인 기본값으로 설정할 수 있습니다. 앱 등록 되어 있는 테 넌 트에서 앱 등록 및 확인 된 도메인 테 넌 트에 테 넌 트 인지 여부를 특히 되는 값입니다.

테 넌 트 확인 된 도메인의 경우 앱 게시자 도메인의 테 넌 트의 확인된 된 기본 도메인 기본값은입니다. 테 넌 트가 없습니다 확인 된 도메인 (있는 경우 응용 프로그램 테 넌 트에 등록 되지 않은 경우), 앱 게시자 도메인을 설정할 경우 null입니다.

다음 표에서 게시자 도메인 값의 기본 동작을 보여 줍니다.  

| 테 넌 트 도메인이 | 기본값은 게시자 도메인 |
|-------------------------|----------------------------|
| Null | Null |
| *.onmicrosoft.com | *.onmicrosoft.com |
| - *.onmicrosoft.com<br/>- domain1.com<br/>-domain2.com (기본) | domain2.com |

다중 테 넌 트 응용 프로그램의 게시자 도메인을 설정 하지 않으면 또는 끝나는 도메인으로 설정 된 경우. onmicrosoft.com, 응용 프로그램의 동의 확인 프롬프트가 표시 됩니다 **확인 되지 않은** 게시자 도메인 대신 합니다.

## <a name="grandfathered-applications"></a>Grandfathered 응용 프로그램

앱 2019 년 5 월 21 일 전에 등록 된 응용 프로그램의 동의 확인 프롬프트가 표시 되지 것입니다 **확인 되지 않은** 게시자 도메인을 설정 하지 않은 경우. 값 설정 하는 게시자 도메인 사용자가 앱의 동의 확인 프롬프트에서이 정보를 볼 수 있도록 하는 것이 좋습니다.

## <a name="configure-publisher-domain-using-the-azure-portal"></a>Azure portal을 사용 하 여 게시자 도메인 구성

앱의 게시자 도메인을 설정 하려면 다음이 단계를 수행 합니다.

1. [Azure Portal](https://portal.azure.com)에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.

1. 계정에 둘 이상의 Azure AD 테 넌 트에 있는 경우:
   1. 프로필 페이지의 오른쪽 위 모서리에 있는 메뉴에서 선택 차례로 **디렉터리 전환**합니다.
   1. Azure AD 테 넌 트 응용 프로그램을 작성 하려는 사용자의 세션을 변경 합니다.

1. 이동할 [Azure Active Directory > 앱 등록](https://go.microsoft.com/fwlink/?linkid=2083908) 를 찾고 구성 하려는 앱을 선택 합니다.

   앱의 앱을 선택한 후 볼 **개요** 페이지입니다.

1. 앱에서 **개요** 페이지에서 선택 합니다 **브랜드** 섹션입니다.

1. 찾을 합니다 **게시자 도메인** 필드를 다음 옵션 중 하나를 선택 합니다.

   - 선택 **도메인을 구성** 도메인을 아직 구성 하지 않은 경우.
   - 선택 **업데이트 도메인** 도메인을 이미 구성 된 경우.

앱은 테 넌 트에 등록 되 면 두 개의 탭에서 선택에 표시 됩니다. **확인 된 도메인을 선택** 하 고 **새 도메인을 확인**합니다.

테 넌 트에서 앱 등록 되지 않은 경우 응용 프로그램에 대 한 새 도메인을 확인 하는 옵션에만 표시 됩니다.

### <a name="to-verify-a-new-domain-for-your-app"></a>앱에 대 한 새 도메인을 확인 하려면

1. 라는 파일을 만들고 `microsoft-identity-association.json` 다음 JSON 코드 조각을 붙여넣습니다.

   ```json
   {
      "associatedApplications": [
        {
           "applicationId": "{YOUR-APP-ID-HERE}"
        }
      ]
    }
   ```

1. 자리 표시자 *{YOUR-앱-ID-여기서}* 앱에 해당 하는 응용 프로그램 (클라이언트) id입니다.

1. 호스트의 파일에 있는: `https://{YOUR-DOMAIN-HERE}.com/.well-known/microsoft-identity-association.json`합니다. 자리 표시자 *{YOUR DOMAIN 여기}* 확인 된 도메인 일치 하도록 합니다.

1. 클릭 합니다 **확인 도메인 저장** 단추입니다.

### <a name="to-select-a-verified-domain"></a>확인된 된 도메인을 선택 하려면

- 테 넌 트에 도메인을 확인 하는 경우에서 도메인 중 하나를 선택 합니다 **도메인인 선택** 드롭다운 합니다.

## <a name="implications-on-the-app-consent-prompt"></a>앱에 미치는 영향 동의 확인 프롬프트

게시자 도메인 구성에 앱 동의 확인 프롬프트에서 사용자에 게 표시에 영향을 줍니다. 동의 확인 프롬프트의 구성 요소를 완벽 하 게 이해 하려면 참조 [응용 프로그램 동의 환경을 이해](application-consent-experience.md)합니다.

다음 표에서 2019 년 5 월 21 일 이전에 만든 응용 프로그램에 대 한 동작을 설명 합니다.

![2019 년 5 월 21 일 이전에 만든 앱에 대 한 동의 확인 프롬프트](./media/howto-configure-publisher-domain/old-app-behavior-table.png)

2019 년 5 월 21 일 이후에 만들어진 새 응용 프로그램에 대 한 동작 게시자 도메인 및 응용 프로그램 유형에 따라 달라 집니다. 다음 표에 구성의 다른 조합을 사용 하 여 확인 하려는 해야 변경 내용이 있습니다.

![2019 년 5 월 21 일 이후에 만든 앱에 대 한 동의 확인 프롬프트](./media/howto-configure-publisher-domain/new-app-behavior-table.png)

## <a name="implications-on-redirect-uris"></a>리디렉션 Uri에 미치는 영향

모든 회사 또는 학교 계정 또는 개인 Microsoft 계정을 사용 하 여 사용자를 로그인 하는 응용 프로그램 ([다중 테 넌 트](single-and-multi-tenant-apps.md)) 리디렉션 지정 하는 경우 몇 가지 제한 사항이 적용 된 Uri입니다.

### <a name="single-root-domain-restriction"></a>단일 루트 도메인 제한

다중 테 넌 트 앱에 대 한 게시자 도메인 값이 null 인 앱에 설정 된 경우 리디렉션 Uri에 대 한 단일 루트 도메인을 공유 하도록 제한 됩니다. 때문에 값 조합을 허용 되지 않습니다는 예를 들어, 루트 도메인, contoso.com, fabrikam.com 일치 하지 않습니다.

```
"https://contoso.com",
"https://fabrikam.com",
```

### <a name="subdomain-restrictions"></a>하위 도메인 제한

하위 도메인 수 있지만 명시적으로 루트 도메인을 등록 해야 합니다. 예를 들어, 다음 Uri는 단일 루트 도메인을 공유 하는 동안 조합은 허용 되지 않습니다.

```
"https://app1.contoso.com",
"https://app2.contoso.com",
```

그러나 개발자는 명시적으로 루트 도메인에 추가 하는 경우 조합을 허용 됩니다.

```
"https://contoso.com",
"https://app1.contoso.com",
"https://app2.contoso.com",
```

### <a name="exceptions"></a>예외

다음과 같은 경우 단일 루트 도메인 제한이 적용 되지 않습니다.

- 단일 테 넌 트 앱 또는 단일 디렉터리에서 계정을 대상으로 하는 앱
- 리디렉션 Uri와 같이 localhost 사용
- 사용자 지정 체계 (HTTP 또는 HTTPS)을 사용 하 여 리디렉션 Uri

## <a name="configure-publisher-domain-programmatically"></a>게시자 도메인을 프로그래밍 방식으로 구성

현재 게시자 도메인을 프로그래밍 방식으로 구성 하려면 REST API 또는 PowerShell 지원 되지 않습니다 수 있습니다.
