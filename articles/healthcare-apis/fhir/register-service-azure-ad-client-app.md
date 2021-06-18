---
title: Azure AD에서 서비스 앱 등록 - Azure API for FHIR
description: Azure Active Directory 서비스 클라이언트 애플리케이션을 등록하는 방법을 알아봅니다.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: cavoeg
ms.openlocfilehash: 12df23908d1d5aab18598fcefc574422e233bf19
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/17/2021
ms.locfileid: "112299036"
---
# <a name="register-a-service-client-application-in-azure-active-directory"></a>Azure Active Directory 서비스 클라이언트 애플리케이션 등록

이 문서에서는 Azure Active Directory 서비스 클라이언트 애플리케이션을 등록하는 방법을 알아봅니다. 클라이언트 애플리케이션 등록은 토큰을 인증하고 가져오는 데 사용할 수 있는 애플리케이션의 Azure Active Directory 표현입니다. 서비스 클라이언트는 애플리케이션에서 사용자의 대화형 인증 없이 액세스 토큰을 가져오는 데 사용됩니다. 특정 애플리케이션 권한을 가지며 액세스 토큰을 가져올 때 애플리케이션 비밀(암호)을 사용합니다.

다음 단계에 따라 새 서비스 클라이언트를 만듭니다.

## <a name="app-registrations-in-azure-portal"></a>Azure Portal 앱 등록

1. [Azure Portal](https://portal.azure.com)에서 **Azure Active Directory** 으로 이동합니다.

2. **앱 등록** 을 선택합니다.

    ![Azure Portal 새 앱 등록.](media/how-to-aad/portal-aad-new-app-registration.png)

3. **새 등록** 을 선택합니다.

4. 서비스 클라이언트에 표시 이름을 지정합니다. 서비스 클라이언트 애플리케이션은 일반적으로 회신 URL을 사용하지 않습니다.

    :::image type="content" source="media/service-client-app/service-client-registration.png" alt-text="Azure Portal. 새 서비스 클라이언트 앱 등록.":::

5. **등록** 을 선택합니다.

## <a name="api-permissions"></a>API 사용 권한

이제 애플리케이션을 등록했으므로 이 애플리케이션이 사용자를 대신하여 요청할 수 있어야 하는 API 권한을 선택해야 합니다.

1. **API 사용 권한** 을 선택합니다.
1. **사용 권한 추가** 를 선택합니다.

    Azure API for FHIR 사용하는 경우 **조직에서 사용하는** API에서 Azure Healthcare API를 검색하여 **Azure Healthcare API에** 대한 권한을 추가합니다. 

    다른 리소스 애플리케이션을 참조하는 경우 이전에 **내** API 에서 만든 [FHIR API 리소스 애플리케이션 등록을](register-resource-azure-ad-client-app.md) 선택합니다.

    :::image type="content" source="media/service-client-app/service-client-org-api.png" alt-text="기밀 클라이언트. 내 조직 API" lightbox="media/service-client-app/service-client-org-api-expanded.png":::

1. 기밀 애플리케이션이 사용자를 대신하여 요청할 수 있는 범위(권한)를 선택합니다.

    :::image type="content" source="media/service-client-app/service-client-add-permission.png" alt-text="서비스 클라이언트. 위임된 권한":::

1. 애플리케이션에 동의를 부여합니다. 필요한 권한이 없으면 Azure Active Directory 관리자에게 문의하세요.

    :::image type="content" source="media/service-client-app/service-client-grant-permission.png" alt-text="서비스 클라이언트. 동의 부여":::

## <a name="application-secret"></a>애플리케이션 암호

토큰을 얻으려면 서비스 클라이언트에 비밀(암호)이 필요합니다.

1. **인증서 및 비밀** 을 선택합니다.
2. **새 클라이언트 비밀** 을 선택합니다.

    ![Azure Portal 서비스 클라이언트 암호](media/how-to-aad/portal-aad-register-new-app-registration-SERVICE-CLIENT-SECRET.png)

3. 비밀에 대한 설명 및 기간(1년, 2년 또는 절대)을 제공합니다.

4. 비밀이 생성되면 포털에 한 번만 표시됩니다. 기록해 두고 안전하게 에 저장합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure Active Directory 서비스 클라이언트 애플리케이션을 등록하는 방법을 배웠습니다. 다음으로 Postman을 사용하여 FHIR 서버에 대한 액세스를 테스트합니다.
 
>[!div class="nextstepaction"]
>[Postman을 사용하여 Azure API for FHIR 액세스](access-fhir-postman-tutorial.md)
