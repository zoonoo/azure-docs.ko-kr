---
title: Azure AD에서 기밀 클라이언트 앱 등록-FHIR 용 Azure API
description: 사용자를 대신 하 여 인증 하 고 리소스 응용 프로그램에 대 한 액세스를 요청 하는 Azure Active Directory에 기밀 클라이언트 응용 프로그램을 등록 합니다.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 04/08/2021
ms.author: matjazl
ms.openlocfilehash: c10b27d375e2bfb8c64130eceb416a633241cf68
ms.sourcegitcommit: c6a2d9a44a5a2c13abddab932d16c295a7207d6a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107284439"
---
# <a name="register-a-confidential-client-application-in-azure-active-directory"></a>Azure Active Directory에서 기밀 클라이언트 응용 프로그램 등록

이 자습서에서는 Azure AD (Azure Active Directory)에서 기밀 클라이언트 응용 프로그램을 등록 하는 방법에 대해 알아봅니다.  

클라이언트 응용 프로그램 등록은 사용자를 대신 하 여 인증 하 고 [리소스 응용 프로그램](register-resource-azure-ad-client-app.md)에 대 한 액세스를 요청 하는 데 사용할 수 있는 응용 프로그램의 Azure AD 표현입니다. 기밀 클라이언트 응용 프로그램은 암호를 저장 하 고 액세스 토큰을 요청할 때 해당 비밀을 제공할 수 있는 신뢰할 수 있는 응용 프로그램입니다. 기밀 응용 프로그램의 예로는 서버 쪽 응용 프로그램이 있습니다. 

새 기밀 클라이언트 응용 프로그램을 등록 하려면 다음 단계를 참조 하세요. 

## <a name="register-a-new-application"></a>새 애플리케이션 등록

1. [Azure Portal](https://portal.azure.com)에서 **Azure Active Directory** 를 선택합니다.

1. **앱 등록** 을 선택합니다. 

    :::image type="content" source="media/how-to-aad/portal-aad-new-app-registration.png" alt-text="Azure Portal. 새 앱 등록.":::

1. **새 등록** 을 선택합니다.

1. 응용 프로그램에 사용자 측 표시 이름을 지정 합니다.

1. **지원 되는 계정 유형** 으로 응용 프로그램을 사용 하거나 API에 액세스할 수 있는 사용자를 선택 합니다.

1. 필드 **리디렉션 URI** 를 제공 합니다. 이러한 세부 정보는 나중에 변경할 수 있지만 응용 프로그램의 회신 URL을 알고 있는 경우 지금 입력 하세요.

    :::image type="content" source="media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT.png" alt-text="새 비밀 클라이언트 앱 등록.":::

1. **등록** 을 선택합니다.

## <a name="api-permissions"></a>API 사용 권한

응용 프로그램을 등록 했으므로 이제이 응용 프로그램이 사용자를 대신해 서 요청 해야 하는 API 권한을 선택 해야 합니다.

1. **API 사용 권한** 을 선택합니다.

    :::image type="content" source="media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-API-Permissions.png" alt-text="기밀 클라이언트. API 권한.":::

1. **사용 권한 추가** 를 선택합니다.

    FHIR 용 Azure API를 사용 하는 경우 **조직에서 사용 하는 api** 에서 **azure 의료 api** 를 검색 하 여 azure 의료 api에 대 한 사용 권한을 추가 합니다. Azure 의료 API에 대 한 검색 결과는 [AZURE API FOR FHIR](fhir-paas-powershell-quickstart.md)을 이미 배포한 경우에만 반환 됩니다.

    다른 리소스 응용 프로그램을 참조 하는 경우 이전에 **내 api** 에서 만든 [Fhir Api 리소스 응용 프로그램 등록](register-resource-azure-ad-client-app.md) 을 선택 합니다.


    :::image type="content" source="media/conf-client-app/confidential-client-org-api.png" alt-text="기밀 클라이언트. 내 조직 Api" lightbox="media/conf-client-app/confidential-app-org-api-expanded.png":::
    

1. 기밀 클라이언트 응용 프로그램에서 사용자를 대신 하 여 요청 하는 범위 (권한)를 선택 합니다. **user_impersonation** 을 선택한 다음, **사용 권한 추가** 를 선택합니다.

    :::image type="content" source="media/conf-client-app/confidential-client-add-permission.png" alt-text="기밀 클라이언트. 위임 된 권한":::


## <a name="application-secret"></a>애플리케이션 암호

1. **인증서 & 암호** 를 선택 하 고 **새 클라이언트 암호** 를 선택 합니다. 

    :::image type="content" source="media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-SECRET.png" alt-text="기밀 클라이언트. 응용 프로그램 암호입니다.":::

1. 클라이언트 암호에 대한 **설명** 을 입력합니다. 만료 시간 프레임을 선택 하려면 **만료** 드롭다운 메뉴를 선택 하 고 **추가** 를 클릭 합니다.

   :::image type="content" source="media/how-to-aad/add-a-client-secret.png" alt-text="클라이언트 암호를 추가 합니다.":::

1. 클라이언트 암호 문자열을 만든 후에는 해당 **값** 과 **ID** 를 복사 하 여 원하는 보안 위치에 저장 합니다.

   :::image type="content" source="media/how-to-aad/client-secret-string-password.png" alt-text="클라이언트 암호 문자열입니다."::: 

> [!NOTE]
>클라이언트 암호 문자열은 Azure Portal에서 한 번만 표시 됩니다. 인증서 & 비밀 웹 페이지에서 다른 곳으로 이동 하 여 다시 돌아오면 값 문자열이 마스킹 됩니다. 생성 된 직후에 클라이언트 암호 문자열을 복사 하는 것이 중요 합니다. 클라이언트 암호의 백업 복사본이 없는 경우에는 위의 단계를 반복 하 여 다시 생성 해야 합니다.
 
## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure AD에서 기밀 클라이언트 응용 프로그램을 등록 하는 방법의 단계를 안내 했습니다. 또한 Azure 의료 API에 API 권한을 추가 하는 방법의 단계를 안내 했습니다. 마지막으로, 응용 프로그램 암호를 만드는 방법을 살펴보았습니다. 또한 Postman을 사용 하 여 FHIR 서버에 액세스 하는 방법을 알아볼 수 있습니다.
 
>[!div class="nextstepaction"]
>[Postman을 사용하여 Azure API for FHIR 액세스](access-fhir-postman-tutorial.md)
