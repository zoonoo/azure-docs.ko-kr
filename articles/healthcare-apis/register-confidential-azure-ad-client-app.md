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
ms.openlocfilehash: 756645d2df22f1222c3004a44e5a46c7a3bc1a2f
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/06/2020
ms.locfileid: "87852551"
---
# <a name="register-a-confidential-client-application-in-azure-active-directory"></a>Azure Active Directory에서 기밀 클라이언트 응용 프로그램 등록

이 자습서에서는 Azure Active Directory에서 기밀 클라이언트 응용 프로그램을 등록 하는 방법에 대해 알아봅니다. 

클라이언트 응용 프로그램 등록은 사용자를 대신 하 여 인증 하 고 [리소스 응용 프로그램](register-resource-azure-ad-client-app.md)에 대 한 액세스를 요청 하는 데 사용할 수 있는 응용 프로그램의 Azure Active Directory 표현입니다. 기밀 클라이언트 응용 프로그램은 암호를 저장 하 고 액세스 토큰을 요청할 때 해당 비밀을 제공할 수 있는 신뢰할 수 있는 응용 프로그램입니다. 기밀 응용 프로그램의 예로는 서버 쪽 응용 프로그램이 있습니다.

포털에서 새 기밀 응용 프로그램을 등록 하려면 다음 단계를 수행 합니다.

## <a name="app-registrations-in-azure-portal"></a>Azure Portal 앱 등록

1. [Azure Portal](https://portal.azure.com)의 왼쪽 탐색 창에서 **Azure Active Directory**를 클릭합니다.

2. **Azure Active Directory** 블레이드에서 **앱 등록**를 클릭 합니다.

    ![Azure Portal. 새 앱 등록.](media/how-to-aad/portal-aad-new-app-registration.png)

3. **새 등록**을 클릭 합니다.

## <a name="register-a-new-application"></a>새 애플리케이션 등록

1. 응용 프로그램에 표시 이름을 지정 합니다.

2. 회신 URL을 제공 합니다. 이러한 세부 정보는 나중에 변경할 수 있지만 응용 프로그램의 회신 URL을 알고 있는 경우 지금 입력 하세요.

    ![새 비밀 클라이언트 앱 등록.](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT.png)

## <a name="api-permissions"></a>API 사용 권한

다음 API 추가 권한 추가:

1. **API 권한**을 엽니다.

    ![기밀 클라이언트. API 사용 권한](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-API-Permissions.png)

2. **권한 추가를 클릭 합니다** .

3. 적절 한 리소스 API를 선택 합니다.

    Azure API for FHIR (관리 서비스)의 경우 **내 조직에서 사용** 하는 api를 클릭 하 고 "Azure 의료 api"를 검색 합니다. Azure 용 오픈 소스 FHIR 서버에 대해 [FHIR API 리소스 응용 프로그램 등록](register-resource-azure-ad-client-app.md)을 선택 합니다.

    ![기밀 클라이언트. 내 Api](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-API-MyApis.png)

4. 기밀 응용 프로그램이 사용자를 대신 하 여 요청할 수 있어야 하는 범위 (권한)를 선택 합니다.

    ![기밀 클라이언트. 위임된 권한](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-API-DelegatedPermissions.png)

## <a name="application-secret"></a>애플리케이션 암호

1. 응용 프로그램 암호 (클라이언트 암호)를 만듭니다.

    ![기밀 클라이언트. 응용 프로그램 암호](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-SECRET.png)

2. 비밀의 설명 및 기간을 제공 합니다.

3. 일단 생성 되 면 포털에서 한 번만 표시 됩니다. 이를 기록해 두고 안전 하 게 저장 합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure Active Directory에 기밀 클라이언트 응용 프로그램을 등록 하는 방법에 대해 알아보았습니다. 이제 [FHIR 용 AZURE API](fhir-paas-powershell-quickstart.md)를 배포할 준비가 되었습니다.

FHIR 용 Azure API를 배포한 후 사용 가능한 추가 설정을 검토할 수 있습니다.
 
>[!div class="nextstepaction"]
>[Azure API for FHIR 배포](fhir-paas-powershell-quickstart.md)