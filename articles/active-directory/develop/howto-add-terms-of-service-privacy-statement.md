---
title: 앱에 대 한 서비스 및 개인정보취급방침 약관 | Azure
description: Azure AD를 사용하도록 등록된 앱의 서비스 약관 및 개인정보처리방침을 구성하는 방법을 알아봅니다.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/22/2019
ms.author: ryanwi
ms.reviwer: lenalepa, sureshja
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 399f72c7819378ddc1566c2bdde94801b2c71687
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66235315"
---
# <a name="how-to-configure-terms-of-service-and-privacy-statement-for-an-app"></a>방법: 앱에 대 한 서비스 및 개인정보취급방침 조건 구성

Azure AD(Azure Active Directory)와 Microsoft 계정을 통합하는 앱을 빌드하고 관리하는 개발자는 앱의 서비스 약관 및 개인정보처리방침에 대한 링크를 포함해야 합니다. 서비스 약관 및 개인정보처리방침은 사용자 동의 환경을 통해 사용자에게 표시됩니다. 서비스 약관 및 개인정보처리방침은 사용자가 앱을 믿고 사용할 수 있게 도와줍니다. 서비스 약관 및 개인정보처리방침은 여러 디렉터리에서 사용되거나 모든 Microsoft 계정에 제공되는 사용자용 다중 테넌트 앱에서 특히 중요합니다.

개발자는 앱의 서비스 약관 및 개인정보처리방침 문서를 만들고 이러한 문서에 대한 URL을 제공할 책임이 있습니다. 다중 테넌트 앱에서 이러한 링크를 제공하지 않는 경우 앱의 사용자 동의 환경에 사용자의 앱 사용 동의를 말리는 경고가 표시됩니다.

> [!NOTE]
> * 단일 테넌트 앱에는 경고가 표시되지 않습니다.
> * 두 링크 중 하나 또는 둘 다 없으면 앱에서 경고를 표시합니다.

## <a name="user-consent-experience"></a>사용자 동의 경험

다음 예제에서는 서비스 약관 및 개인정보처리방침을 구성하고 링크를 구성하지 않을 때의 사용자 동의 환경을 보여줍니다.

![개인정보처리방침 및 서비스 약관이 제공된 스크린샷과 제공되지 않은 스크린샷](./media/howto-add-terms-of-service-privacy-statement/user-consent-exp-privacy-statement-terms-service.png)

## <a name="formatting-links-to-the-terms-of-service-and-privacy-statement-documents"></a>서비스 약관 및 개인정보처리방침 문서의 링크 서식 지정

앱의 서비스 약관 및 개인정보처리방침 문서에 링크를 추가하기 전에 URL이 지침을 준수하는지 확인해야 합니다.

| 지침     | 설명                           |
|---------------|---------------------------------------|
| 형식        | 유효한 URL                             |
| 유효한 스키마 | HTTP 및 HTTPS<br/>HTTPS 권장 |
| 최대 길이    | 2048자                       |

예: `https://myapp.com/terms-of-service` 및 `https://myapp.com/privacy-statement`

## <a name="adding-links-to-the-terms-of-service-and-privacy-statement"></a>서비스 약관 및 개인정보처리방침에 링크 추가

서비스 약관 및 개인정보처리방침이 준비되면 다음 방법 중 하나를 사용하여 앱에 이러한 문서의 링크를 추가할 수 있습니다.

* [Azure Portal을 통해](#azure-portal)
* [앱 개체 JSON을 사용하여](#app-object-json)
* [MSGraph 베타 REST API를 사용하여](#msgraph-beta-rest-api)

### <a name="azure-portal"></a>Azure portal을 사용 하 여
Azure portal에서 다음이 단계를 수행 합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. **앱 등록** 섹션으로 이동하여 앱을 선택합니다.
3. 엽니다는 **브랜드** 창입니다.
4. **서비스 약관 URL** 및 **개인정보처리방침 URL** 필드를 채웁니다.
5. 변경 내용을 저장합니다.

    ![서비스 약관 및 개인정보처리방침 URL이 있는 앱 속성 섹션](./media/howto-add-terms-of-service-privacy-statement/azure-portal-terms-service-privacy-statement-urls.png)

### <a name="app-object-json"></a>앱 개체 JSON을 사용하여

앱 개체 JSON을 직접 수정하는 것을 선호하는 경우 Azure Portal 또는 애플리케이션 등록 포털에서 매니페스트 편집기를 사용하여 앱의 서비스 약관 및 개인정보처리방침 링크를 포함할 수 있습니다.

```json
    "informationalUrls": { 
        "termsOfService": "<your_terms_of_service_url>", 
        "privacy": "<your_privacy_statement_url>" 
    }
```

### <a name="msgraph-beta-rest-api"></a>MSGraph 베타 REST API를 사용하여

프로그래밍 방식으로 모든 앱을 업데이트하려면 MSGraph 베타 REST API를 사용하여 서비스 약관 및 개인정보처리방침 문서 링크를 포함하도록 모든 앱을 업데이트하면 됩니다.

```
PATCH https://graph.microsoft.com/beta/applications/{application id}
{ 
    "appId": "{your application id}", 
    "info": { 
        "termsOfServiceUrl": "<your_terms_of_service_url>", 
        "supportUrl": null, 
        "privacyStatementUrl": "<your_privacy_statement_url>", 
        "marketingUrl": null, 
        "logoUrl": null 
    }
}
```

> [!NOTE]
> * `supportUrl`, `marketingUrl` 및 `logoUrl` 필드에 할당된 기존 값을 덮어쓰지 않도록 주의해야 합니다.
> * MSGraph 베타 REST API는 Azure AD 계정으로 로그인하는 경우에만 작동합니다. 개인 Microsoft 계정은 지원되지 않습니다.
