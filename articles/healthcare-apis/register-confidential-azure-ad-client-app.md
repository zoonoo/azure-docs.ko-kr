---
title: Azure AD에서 기밀 클라이언트 앱 등록-FHIR 용 Azure API
description: 사용자를 대신 하 여 인증 하 고 리소스 응용 프로그램에 대 한 액세스를 요청 하는 Azure Active Directory에 기밀 클라이언트 응용 프로그램을 등록 합니다.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: matjazl
ms.openlocfilehash: e74271119b581b2bb291b1a9ddd74ad0781855e6
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2020
ms.locfileid: "91629147"
---
# <a name="register-a-confidential-client-application-in-azure-active-directory"></a>Azure Active Directory에서 기밀 클라이언트 응용 프로그램 등록

이 자습서에서는 Azure Active Directory에서 기밀 클라이언트 응용 프로그램을 등록 하는 방법에 대해 알아봅니다. 

클라이언트 응용 프로그램 등록은 사용자를 대신 하 여 인증 하 고 [리소스 응용 프로그램](register-resource-azure-ad-client-app.md)에 대 한 액세스를 요청 하는 데 사용할 수 있는 응용 프로그램의 Azure Active Directory 표현입니다. 기밀 클라이언트 응용 프로그램은 암호를 저장 하 고 액세스 토큰을 요청할 때 해당 비밀을 제공할 수 있는 신뢰할 수 있는 응용 프로그램입니다. 기밀 응용 프로그램의 예로는 서버 쪽 응용 프로그램이 있습니다.

포털에서 새 기밀 응용 프로그램을 등록 하려면 다음 단계를 수행 합니다.

## <a name="register-a-new-application"></a>새 애플리케이션 등록

1. [Azure Portal](https://portal.azure.com)에서 **Azure Active Directory**로 이동 합니다.

1. **앱 등록**을 선택합니다.

    ![Azure Portal. 새 앱 등록.](media/how-to-aad/portal-aad-new-app-registration.png)

1. **새 등록**을 선택합니다.

1. 응용 프로그램에 표시 이름을 지정 합니다.

1. 회신 URL을 제공 합니다. 이러한 세부 정보는 나중에 변경할 수 있지만 응용 프로그램의 회신 URL을 알고 있는 경우 지금 입력 하세요.

    ![새 비밀 클라이언트 앱 등록.](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT.png)
1. **등록**을 선택합니다.

## <a name="api-permissions"></a>API 사용 권한

응용 프로그램을 등록 했으므로 이제이 응용 프로그램이 사용자를 대신 하 여 요청할 수 있는 API 권한을 선택 해야 합니다.

1. **API 사용 권한**을 선택합니다.

    ![기밀 클라이언트. API 사용 권한](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-API-Permissions.png)

1. **사용 권한 추가**를 선택합니다.

    FHIR 용 Azure API를 사용 하는 경우 **조직에서 사용 하는 api**에서 **azure 의료 api** 를 검색 하 여 azure 의료 api에 대 한 사용 권한을 추가 합니다. 

    다른 리소스 응용 프로그램을 참조 하는 경우 이전에 **내 api**에서 만든 [Fhir Api 리소스 응용 프로그램 등록](register-resource-azure-ad-client-app.md) 을 선택 합니다.


    :::image type="content" source="media/conf-client-app/confidential-client-org-api.png" alt-text="기밀 클라이언트. 내 조직 Api" lightbox="media/conf-client-app/confidential-app-org-api-expanded.png":::
    

3. 기밀 응용 프로그램이 사용자를 대신 하 여 요청할 수 있어야 하는 범위 (권한)를 선택 합니다.

    :::image type="content" source="media/conf-client-app/confidential-client-add-permission.png" alt-text="기밀 클라이언트. 내 조직 Api":::

## <a name="application-secret"></a>애플리케이션 암호

1. **인증서 & 암호**를 선택 합니다.
1. **새 클라이언트 비밀**을 선택합니다. 

    ![기밀 클라이언트. 응용 프로그램 암호](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-SECRET.png)

2. 비밀의 설명 및 기간 (1 년, 2 년 또는 안 함)을 제공 합니다.

3. 일단 생성 되 면 포털에서 한 번만 표시 됩니다. 이를 기록해 두고 안전 하 게 저장 합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure Active Directory에 기밀 클라이언트 응용 프로그램을 등록 하는 방법에 대해 알아보았습니다. 이제 [FHIR 용 AZURE API](fhir-paas-powershell-quickstart.md)를 배포할 준비가 되었습니다.

FHIR 용 Azure API를 배포한 후 사용 가능한 추가 설정을 검토할 수 있습니다.
 
>[!div class="nextstepaction"]
>[Azure API for FHIR 배포](fhir-paas-powershell-quickstart.md)
