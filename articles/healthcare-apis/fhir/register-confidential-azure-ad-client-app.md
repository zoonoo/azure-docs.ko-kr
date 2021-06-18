---
title: Azure AD에서 기밀 클라이언트 앱 등록 - Azure API for FHIR
description: 사용자를 대신하여 인증하고 리소스 애플리케이션에 대한 액세스를 요청하는 기밀 클라이언트 애플리케이션을 Azure Active Directory 등록합니다.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 04/08/2021
ms.author: cavoeg
ms.openlocfilehash: 20d2e86786c3586cd4f45e98d670d4a937a77992
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/17/2021
ms.locfileid: "112299198"
---
# <a name="register-a-confidential-client-application-in-azure-active-directory"></a>Azure Active Directory 기밀 클라이언트 애플리케이션 등록

이 자습서에서는 Azure AD(Azure Active Directory)에서 기밀 클라이언트 애플리케이션을 등록하는 방법을 알아봅니다.  

클라이언트 애플리케이션 등록은 사용자를 대신하여 인증하고 리소스 애플리케이션에 대한 액세스를 요청하는 데 사용할 수 있는 애플리케이션의 Azure AD [표현입니다.](register-resource-azure-ad-client-app.md) 기밀 클라이언트 애플리케이션은 액세스를 요청할 때 비밀을 보관하고 해당 비밀을 표시하도록 신뢰할 수 있는 애플리케이션입니다. 기밀 애플리케이션의 예로는 서버 쪽 애플리케이션이 있습니다. 

새 기밀 클라이언트 애플리케이션을 등록하려면 아래 단계를 참조하세요. 

## <a name="register-a-new-application"></a>새 애플리케이션 등록

1. [Azure Portal](https://portal.azure.com)에서 **Azure Active Directory** 를 선택합니다.

1. **앱 등록** 을 선택합니다. 

    :::image type="content" source="media/how-to-aad/portal-aad-new-app-registration.png" alt-text="Azure Portal. 새 앱 등록.":::

1. **새 등록** 을 선택합니다.

1. 애플리케이션에 사용자용 표시 이름을 지정합니다.

1. **지원되는 계정 유형에** 대해 애플리케이션을 사용하거나 API에 액세스할 수 있는 사람을 선택합니다.

1. (선택 사항) 리디렉션 **URI 를** 제공합니다. 이러한 세부 정보는 나중에 변경할 수 있지만 애플리케이션의 회신 URL을 알고 있는 경우 지금 입력합니다.

    :::image type="content" source="media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT.png" alt-text="새 기밀 클라이언트 앱 등록.":::

1. **등록** 을 선택합니다.

## <a name="api-permissions"></a>API 사용 권한

이제 애플리케이션을 등록했으므로 이 애플리케이션이 사용자를 대신하여 요청해야 하는 API 권한을 선택해야 합니다.

1. **API 사용 권한** 을 선택합니다.

    :::image type="content" source="media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-API-Permissions.png" alt-text="기밀 클라이언트. API 권한.":::

1. **사용 권한 추가** 를 선택합니다.

    Azure API for FHIR 사용하는 경우 조직에서 사용하는 API에서 Azure Healthcare API를 검색하여 **Azure Healthcare API에** 대한 권한을 **추가합니다.** Azure Healthcare API에 대한 검색 결과는 Azure API for FHIR 이미 [배포한](fhir-paas-powershell-quickstart.md)경우에만 반환됩니다.

    다른 리소스 애플리케이션을 참조하는 경우 이전에 **내** API 에서 만든 [FHIR API 리소스 애플리케이션 등록을](register-resource-azure-ad-client-app.md) 선택합니다.


    :::image type="content" source="media/conf-client-app/confidential-client-org-api.png" alt-text="기밀 클라이언트. 내 조직 API" lightbox="media/conf-client-app/confidential-app-org-api-expanded.png":::
    

1. 기밀 클라이언트 애플리케이션이 사용자를 대신하여 요청할 범위(권한)를 선택합니다. **user_impersonation** 을 선택한 다음, **사용 권한 추가** 를 선택합니다.

    :::image type="content" source="media/conf-client-app/confidential-client-add-permission.png" alt-text="기밀 클라이언트. 위임된 권한":::


## <a name="application-secret"></a>애플리케이션 암호

1. **인증서 및 비밀** 을 선택하고 **새 클라이언트 암호** 를 선택합니다. 

    :::image type="content" source="media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-SECRET.png" alt-text="기밀 클라이언트. 애플리케이션 암호.":::

1. 클라이언트 암호에 대한 **설명** 을 입력합니다. **만료** 드롭다운 메뉴를 선택하여 만료 시간 프레임을 선택한 다음, **추가를** 클릭합니다.

   :::image type="content" source="media/how-to-aad/add-a-client-secret.png" alt-text="클라이언트 비밀을 추가합니다.":::

1. 클라이언트 비밀 문자열을 만든 후 **해당 값** 및 **ID** 를 복사하여 선택한 안전한 위치에 저장합니다.

   :::image type="content" source="media/how-to-aad/client-secret-string-password.png" alt-text="클라이언트 비밀 문자열입니다."::: 

> [!NOTE]
>클라이언트 비밀 문자열은 Azure Portal 한 번만 표시됩니다. 인증서 & 비밀 웹 페이지에서 이동한 다음 다시 돌아가면 값 문자열이 마스킹됩니다. 클라이언트 비밀 문자열이 생성된 직후에 복사를 만드는 것이 중요합니다. 클라이언트 비밀의 백업 복사본이 없는 경우 위의 단계를 반복하여 다시 생성해야 합니다.
 
## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure AD에서 기밀 클라이언트 애플리케이션을 등록하는 방법의 단계를 안내했습니다. 또한 Azure Healthcare API에 API 권한을 추가하는 방법의 단계를 안내했습니다. 마지막으로, 애플리케이션 비밀을 만드는 방법을 보여 받았습니다. 또한 Postman을 사용하여 FHIR 서버에 액세스하는 방법을 알아볼 수 있습니다.
 
>[!div class="nextstepaction"]
>[Postman을 사용하여 Azure API for FHIR 액세스](access-fhir-postman-tutorial.md)
