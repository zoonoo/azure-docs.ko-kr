---
title: Azure AD에서 서비스 앱 등록-FHIR 용 Azure API
description: Azure Active Directory에서 서비스 클라이언트 응용 프로그램을 등록 하는 방법에 대해 알아봅니다.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: matjazl
ms.openlocfilehash: 19d6b0ebfa2570b04c3a9dda3fe69428aa0eed75
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2020
ms.locfileid: "91629326"
---
# <a name="register-a-service-client-application-in-azure-active-directory"></a>Azure Active Directory에 서비스 클라이언트 응용 프로그램 등록

이 문서에서는 Azure Active Directory에 서비스 클라이언트 응용 프로그램을 등록 하는 방법에 대해 알아봅니다. 클라이언트 응용 프로그램 등록은 토큰을 인증 하 고 가져오는 데 사용할 수 있는 응용 프로그램의 Azure Active Directory 표현입니다. 서비스 클라이언트는 응용 프로그램에서 사용자의 대화형 인증 없이 액세스 토큰을 가져오는 데 사용 됩니다. 특정 응용 프로그램 권한을 보유 하 고 액세스 토큰을 가져올 때 응용 프로그램 암호 (암호)를 사용 합니다.

새 서비스 클라이언트를 만들려면 다음 단계를 수행 합니다.

## <a name="app-registrations-in-azure-portal"></a>Azure Portal 앱 등록

1. [Azure Portal](https://portal.azure.com)에서 **Azure Active Directory**로 이동 합니다.

2. **앱 등록**을 선택합니다.

    ![Azure Portal. 새 앱 등록.](media/how-to-aad/portal-aad-new-app-registration.png)

3. **새 등록**을 선택합니다.

4. 서비스 클라이언트에 표시 이름을 지정 합니다. 서비스 클라이언트 응용 프로그램은 일반적으로 회신 URL을 사용 하지 않습니다.

    :::image type="content" source="media/service-client-app/service-client-registration.png" alt-text="Azure Portal. 새 서비스 클라이언트 앱 등록.":::

5. **등록**을 선택합니다.

## <a name="api-permissions"></a>API 사용 권한

응용 프로그램을 등록 했으므로 이제이 응용 프로그램이 사용자를 대신 하 여 요청할 수 있는 API 권한을 선택 해야 합니다.

1. **API 사용 권한**을 선택합니다.
1. **사용 권한 추가**를 선택합니다.

    FHIR 용 Azure API를 사용 하는 경우 **조직에서 사용 하는 api**에서 **azure 의료 api** 를 검색 하 여 azure 의료 api에 대 한 사용 권한을 추가 합니다. 

    다른 리소스 응용 프로그램을 참조 하는 경우 이전에 **내 api**에서 만든 [Fhir Api 리소스 응용 프로그램 등록](register-resource-azure-ad-client-app.md) 을 선택 합니다.

    :::image type="content" source="media/service-client-app/service-client-org-api.png" alt-text="Azure Portal. 새 서비스 클라이언트 앱 등록." lightbox="media/service-client-app/service-client-org-api-expanded.png":::

1. 기밀 응용 프로그램이 사용자를 대신 하 여 요청할 수 있어야 하는 범위 (권한)를 선택 합니다.

    :::image type="content" source="media/service-client-app/service-client-add-permission.png" alt-text="Azure Portal. 새 서비스 클라이언트 앱 등록.":::

1. 응용 프로그램에 동의를 부여 합니다. 필요한 권한이 없는 경우 Azure Active Directory 관리자에 게 확인 합니다.

    :::image type="content" source="media/service-client-app/service-client-grant-permission.png" alt-text="Azure Portal. 새 서비스 클라이언트 앱 등록.":::

## <a name="application-secret"></a>애플리케이션 암호

서비스 클라이언트에는 토큰을 얻기 위한 암호 (암호)가 필요 합니다.

1. **인증서 및 비밀**을 선택합니다.
2. **새 클라이언트 비밀**을 선택합니다.

    ![Azure Portal. 서비스 클라이언트 암호](media/how-to-aad/portal-aad-register-new-app-registration-SERVICE-CLIENT-SECRET.png)

3. 비밀의 설명 및 기간 (1 년, 2 년 또는 안 함)을 제공 합니다.

4. 비밀을 생성 한 후에는 포털에서 한 번만 표시 됩니다. 이를 기록해 두고 안전 하 게에 저장 합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure Active Directory에 서비스 클라이언트 응용 프로그램을 등록 하는 방법에 대해 알아보았습니다. 다음으로 Azure API for FHIR의 추가 설정에 대해 알아볼 수 있습니다.
 
>[!div class="nextstepaction"]
>[추가 설정](azure-api-for-fhir-additional-settings.md)