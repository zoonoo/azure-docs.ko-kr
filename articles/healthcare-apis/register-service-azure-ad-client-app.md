---
title: Azure AD에서 서비스 앱 등록-FHIR 용 Azure API
description: 토큰을 인증 하 고 가져오는 데 사용할 수 있는 Azure Active Directory에 서비스 클라이언트 응용 프로그램을 등록 하는 방법에 대해 알아봅니다.
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: 83514ea13ca51e6980a1b4b60d4555974db5a240
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84871083"
---
# <a name="register-a-service-client-application-in-azure-active-directory"></a>Azure Active Directory에 서비스 클라이언트 응용 프로그램 등록

이 문서에서는 Azure Active Directory에 서비스 클라이언트 응용 프로그램을 등록 하는 방법에 대해 알아봅니다. 클라이언트 응용 프로그램 등록은 토큰을 인증 하 고 가져오는 데 사용할 수 있는 응용 프로그램의 Azure Active Directory 표현입니다. 서비스 클라이언트는 응용 프로그램에서 사용자의 대화형 인증 없이 액세스 토큰을 가져오는 데 사용 됩니다. 특정 응용 프로그램 권한을 보유 하 고 액세스 토큰을 가져올 때 응용 프로그램 암호 (암호)를 사용 합니다.

새 서비스 클라이언트를 만들려면 다음 단계를 수행 합니다.

## <a name="app-registrations-in-azure-portal"></a>Azure Portal 앱 등록

1. [Azure Portal](https://portal.azure.com)의 왼쪽 탐색 창에서 **Azure Active Directory**를 클릭합니다.

2. **Azure Active Directory** 블레이드에서 **앱 등록**를 클릭 합니다.

    ![Azure Portal. 새 앱 등록.](media/how-to-aad/portal-aad-new-app-registration.png)

3. **새 등록**을 클릭합니다.

## <a name="service-client-application-details"></a>서비스 클라이언트 응용 프로그램 세부 정보

* 서비스 클라이언트에는 표시 이름이 필요 하며 회신 URL을 제공할 수도 있지만 일반적으로 사용 되지 않습니다.

    ![Azure Portal. 새 서비스 클라이언트 앱 등록.](media/how-to-aad/portal-aad-register-new-app-registration-SERVICE-CLIENT-NAME.png)

## <a name="api-permissions"></a>API 사용 권한

서비스 클라이언트 응용 프로그램 역할을 부여 해야 합니다. 

1. **Api 권한을** 열고 [Fhir Api 리소스 응용 프로그램 등록](register-resource-azure-ad-client-app.md)을 선택 합니다. FHIR 용 Azure API를 사용 하는 경우 **조직에서 사용 하는 api**에서 Azure 의료 api를 검색 하 여 Azure 의료 api에 대 한 사용 권한을 추가 합니다.

    ![Azure Portal. 서비스 클라이언트 API 권한](media/how-to-aad/portal-aad-register-new-app-registration-SERVICE-CLIENT-API-PERMISSIONS.png)

2. 리소스 응용 프로그램에 정의 된 응용 프로그램 역할을 선택 합니다.

    ![Azure Portal. 서비스 클라이언트 응용 프로그램 사용 권한](media/how-to-aad/portal-aad-register-new-app-registration-SERVICE-CLIENT-APPLICATION-PERMISSIONS.png)

3. 응용 프로그램에 동의를 부여 합니다. 필요한 권한이 없는 경우 Azure Active Directory 관리자에 게 확인 합니다.

    ![Azure Portal. 서비스 클라이언트 관리자 동의](media/how-to-aad/portal-aad-register-new-app-registration-SERVICE-CLIENT-ADMIN-CONSENT.png)

## <a name="application-secret"></a>애플리케이션 암호

서비스 클라이언트에는 토큰을 가져올 때 사용 되는 암호 (암호)가 필요 합니다.

1. **인증서 &amp; 비밀** 클릭

2. **새 클라이언트 암호**를 클릭합니다.

    ![Azure Portal. 서비스 클라이언트 암호](media/how-to-aad/portal-aad-register-new-app-registration-SERVICE-CLIENT-SECRET.png)

3. 비밀의 기간을 제공 합니다.

4. 생성 된 후에는 포털에서 한 번만 표시 됩니다. 이를 기록해 두고 안전 하 게에 저장 합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure Active Directory에 서비스 클라이언트 응용 프로그램을 등록 하는 방법에 대해 알아보았습니다. 다음으로 Azure에서 FHIR API를 배포 합니다.
 
>[!div class="nextstepaction"]
>[오픈 소스 서버 배포](fhir-oss-powershell-quickstart.md)