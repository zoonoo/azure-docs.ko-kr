---
title: Azure Active Directory reporting API에 액세스하기 위한 필수 구성 요소 | Microsoft Docs
description: Azure AD Reporting API에 액세스하기 위한 필수 구성 요소에 대해 알아보기
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: ada19f69-665c-452a-8452-701029bf4252
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8ab30dfeccc4ae1c7b09a8f48846ffb9e71cfc23
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2019
ms.locfileid: "58436747"
---
# <a name="prerequisites-to-access-the-azure-active-directory-reporting-api"></a>Azure Active Directory reporting API에 액세스하기 위한 필수 구성 요소

[Azure AD(Azure Active Directory) Reporting API](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-reports-and-events-preview)는 일련의 REST 기반 API를 통해 데이터에 프로그래밍 방식으로 액세스합니다. 다양한 프로그래밍 언어 및 도구에서 이러한 API를 호출할 수 있습니다.

Reporting API는 [OAuth](https://msdn.microsoft.com/library/azure/dn645545.aspx) 를 사용하여 Web API에 대한 액세스 권한을 부여합니다.

reporting API에 액세스하도록 준비하려면 다음을 수행해야 합니다.

1. [역할 할당](#assign-roles)
2. [애플리케이션 등록](#register-an-application)
3. [권한 부여](#grant-permissions)
4. [구성 설정 수집](#gather-configuration-settings)

## <a name="assign-roles"></a>역할 할당

API를 통해 보고 데이터에 액세스하려면 다음 역할 중 하나를 할당받아야 합니다.

- 보안 판독기

- 보안 관리자

- 전역 관리자


## <a name="register-an-application"></a>애플리케이션 등록

스크립트를 사용하여 보고 API에 액세스하는 경우에도 애플리케이션을 등록해야 합니다. 이렇게 하면 인증 호출에 필요한 **애플리케이션 ID**가 제공되고, 이를 통해 코드에서 토큰을 받을 수 있습니다.

Azure AD 보고 API에 액세스하도록 디렉터리를 구성하려면 Azure AD 테넌트에서 **글로벌 관리자** 디렉터리 역할의 멤버이기도 한 Azure 관리자 계정을 사용하여 [Azure Portal](https://portal.azure.com)에 로그인해야 합니다.

> [!IMPORTANT]
> 이 같은 관리자 권한이 있는 자격 증명 하에서 실행 중인 애플리케이션은 매우 강력할 수 있으므로, 애플리케이션의 ID 및 비밀 자격 증명을 안전하게 보관해야 합니다.
> 

**Azure AD 애플리케이션을 등록하려면:**

1. [Azure Portal](https://portal.azure.com)의 왼쪽 탐색 패널에서 **Azure Active Directory**를 선택합니다.
   
    ![애플리케이션 등록](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. **Azure Active Directory** 페이지에서 **앱 등록**을 선택합니다.

    ![애플리케이션 등록](./media/howto-configure-prerequisites-for-reporting-api/02.png) 

3. **앱 등록** 페이지에서 **새 애플리케이션 등록**을 선택합니다.

    ![애플리케이션 등록](./media/howto-configure-prerequisites-for-reporting-api/03.png)

4. **만들기** 페이지에서 다음 단계를 수행합니다.

    ![애플리케이션 등록](./media/howto-configure-prerequisites-for-reporting-api/04.png)

    a. **이름** 텍스트 상자에서 `Reporting API application`을 입력합니다.

    b. **애플리케이션 형식**으로 **웹앱/API**를 선택합니다.

    다. **로그온 URL** 텍스트 상자에 `https://localhost`을(를) 입력합니다.

    d. **만들기**를 선택합니다. 


## <a name="grant-permissions"></a>권한 부여 

액세스하려는 API에 따라 앱에 다음 권한을 부여해야 합니다.  

| API | 사용 권한 |
| --- | --- |
| Windows Azure Active Directory | 디렉터리 데이터 읽기 |
| Microsoft Graph | 모든 감사 로그 데이터 읽기 |


![애플리케이션 등록](./media/howto-configure-prerequisites-for-reporting-api/36.png)

다음 섹션에는 두 API에 대한 단계가 나열됩니다. API 중 하나에 액세스하지 않으려는 경우 관련 단계를 건너뛰면 됩니다.

**API를 사용하도록 애플리케이션에 권한을 부여하려면:**

1. **앱 등록** 페이지에서 애플리케이션을 선택하고 **설정**을 선택합니다. 

    ![애플리케이션 등록](./media/howto-configure-prerequisites-for-reporting-api/05.png)

2. **설정** 페이지에서 **필요한 권한**을 선택합니다. 

    ![애플리케이션 등록](./media/howto-configure-prerequisites-for-reporting-api/06.png)

3. **필요한 권한** 페이지의 **API** 목록에서 **Windows Azure Active Directory**를 클릭합니다. 

    ![애플리케이션 등록](./media/howto-configure-prerequisites-for-reporting-api/07.png)

4. **액세스 사용** 페이지에서 **디렉터리 데이터 읽기**를 선택하고 **로그인 및 사용자 프로필 읽기**를 선택 취소합니다. 

    ![애플리케이션 등록](./media/howto-configure-prerequisites-for-reporting-api/08.png)

5. 위쪽의 도구 모음에서 **저장**을 클릭합니다.

    ![애플리케이션 등록](./media/howto-configure-prerequisites-for-reporting-api/15.png)

6. **필요한 권한** 페이지의 위쪽 도구 모음에서 **추가**를 클릭합니다.

    ![애플리케이션 등록](./media/howto-configure-prerequisites-for-reporting-api/32.png)

7. **API 액세스 추가** 페이지에서 **API 선택**을 클릭합니다.

    ![애플리케이션 등록](./media/howto-configure-prerequisites-for-reporting-api/31.png)

8. **API 선택** 페이지에서 **Microsoft Graph**를 클릭한 다음, **선택**을 클릭합니다.

    ![애플리케이션 등록](./media/howto-configure-prerequisites-for-reporting-api/33.png)

9. **액세스 사용** 페이지에서 **Read all audit log data**(모든 감사 로그 데이터 읽기)를 선택한 다음, **선택**을 클릭합니다.  

    ![애플리케이션 등록](./media/howto-configure-prerequisites-for-reporting-api/34.png)

10. **API 액세스 추가** 페이지에서 **완료**를 클릭합니다.  

11. **필요한 권한** 페이지의 위쪽 도구 모음에서 **권한 부여**를 클릭한 다음, **예**를 클릭합니다.

    ![애플리케이션 등록](./media/howto-configure-prerequisites-for-reporting-api/17.png)


## <a name="gather-configuration-settings"></a>구성 설정 수집 

이 섹션에서는 디렉터리에서 다음 설정을 가져오는 방법을 보여 줍니다.

- 도메인 이름
- 클라이언트 ID
- 클라이언트 암호

Reporting API에 대한 호출을 구성하는 경우 이 값이 필요합니다. 

### <a name="get-your-domain-name"></a>도메인 이름 가져오기

**도메인 이름을 가져오려면**

1. [Azure Portal](https://portal.azure.com)의 왼쪽 탐색 창에서 **Active Directory**를 선택합니다.
   
    ![애플리케이션 등록](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. **Azure Active Directory** 페이지에서 **사용자 지정 도메인 이름**을 선택합니다.

    ![애플리케이션 등록](./media/howto-configure-prerequisites-for-reporting-api/09.png) 

3. 도메인 목록에서 도메인 이름을 복사합니다.


### <a name="get-your-applications-client-id"></a>애플리케이션의 클라이언트 ID 가져오기

**애플리케이션의 클라이언트 ID를 가져오려면**

1. [Azure Portal](https://portal.azure.com)의 왼쪽 탐색 패널에서 **Azure Active Directory**를 클릭합니다.
   
    ![애플리케이션 등록](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. **앱 등록** 페이지에서 애플리케이션을 선택합니다.

3. 애플리케이션 페이지에서 **애플리케이션 ID**로 이동하고 **복사하려면 클릭**을 선택합니다.

    ![애플리케이션 등록](./media/howto-configure-prerequisites-for-reporting-api/11.png) 


### <a name="get-your-applications-client-secret"></a>애플리케이션의 클라이언트 비밀 가져오기
애플리케이션의 클라이언트 암호를 가져오려면 새 키를 만들고 새 키를 저장할 때 해당 값을 저장해야 합니다. 나중에 이 값을 검색하는 것이 불가능하기 때문입니다.

**애플리케이션의 클라이언트 비밀을 가져오려면**

1. [Azure Portal](https://portal.azure.com)의 왼쪽 탐색 패널에서 **Azure Active Directory**를 클릭합니다.
   
    ![애플리케이션 등록](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2.  **앱 등록** 페이지에서 애플리케이션을 선택합니다.

3. 애플리케이션 페이지의 위쪽의 도구 모음에서 **설정**을 선택합니다. 

    ![애플리케이션 등록](./media/howto-configure-prerequisites-for-reporting-api/05.png)

4. **설정** 페이지의 **API 액세스** 섹션에서 **키**를 클릭합니다. 

    ![애플리케이션 등록](./media/howto-configure-prerequisites-for-reporting-api/12.png)

5. **키** 페이지에서 다음 단계를 수행합니다.

    ![애플리케이션 등록](./media/howto-configure-prerequisites-for-reporting-api/14.png)

    a. **설명** 텍스트 상자에서 `Reporting API`를 입력합니다.

    b. **만료**로 **In 2 years**(2년)를 선택합니다.

    다. **저장**을 클릭합니다.

    d. 키 값을 복사합니다.

## <a name="troubleshoot-errors-in-the-reporting-api"></a>보고 API의 오류 문제 해결

이 섹션에는 MS Graph API를 사용하여 활동 보고서에 액세스할 때 발생할 수 있는 일반적인 오류 메시지와, 이러한 오류를 해결할 수 있는 단계가 나와 있습니다.

### <a name="500-http-internal-server-error-while-accessing-microsoft-graph-v2-endpoint"></a>Microsoft Graph V2 엔드포인트에 액세스하는 동안 500 HTTP 내부 서버 오류 발생

현재 Microsoft Graph v2 엔드포인트가 지원되지 않습니다. Microsoft Graph v1 엔드포인트를 사용하여 활동 로그에 액세스해야 합니다.

### <a name="error-failed-to-get-user-roles-from-ad-graph"></a>오류: AD Graph에서 사용자 역할을 가져오지 못함

Graph 탐색기를 사용하여 로그인에 액세스할 때 이 오류 메시지가 표시될 수 있습니다. 다음 이미지와 같이 Graph 탐색기 UI에서 두 로그인 단추를 모두 사용하여 계정에 로그인했는지 확인합니다. 

![Graph 탐색기](./media/troubleshoot-graph-api/graph-explorer.png)

### <a name="error-failed-to-do-premium-license-check-from-ad-graph"></a>오류: AD Graph에서 Premium 라이선스를 확인하지 못함 

Graph 탐색기를 사용하여 로그인에 액세스하는 동안 이 오류 메시지가 표시되면 왼쪽 탐색에 있는 계정 아래에서 **권한 수정**을 선택하고 **Tasks.ReadWrite** 및 **Directory.Read.All**을 선택합니다. 

![권한 수정 UI](./media/troubleshoot-graph-api/modify-permissions.png)


### <a name="error-neither-tenant-is-b2c-or-tenant-doesnt-have-premium-license"></a>오류: 테넌트가 B2C가 아니거나 테넌트에 Premium 라이선스가 없음

로그인 보고서에 액세스하려면 Azure Active Directory P1(Premium 1) 라이선스가 필요합니다. 로그인에 액세스하는 동안 이 오류 메시지가 표시되면 Azure AD P1 라이선스를 사용하여 테넌트에게 사용이 허가되었는지 확인합니다.

### <a name="error-user-is-not-in-the-allowed-roles"></a>오류: 사용자가 허용된 역할이 아님 

API를 사용하여 감사 로그 또는 로그인에 액세스하는 동안 이 오류 메시지가 표시되면 Azure Active Directory 테넌트의 **보안 읽기 권한자** 또는 **보고서 구독자** 역할이 계정에 포함되어 있는지 확인합니다. 

### <a name="error-application-missing-aad-read-directory-data-permission"></a>오류: 애플리케이션에 AAD '디렉터리 데이터 읽기' 권한이 없음 

[Azure Active Directory reporting API에 액세스하기 위한 필수 구성 요소](howto-configure-prerequisites-for-reporting-api.md)의 단계에 따라 애플리케이션이 적합한 권한 집합으로 실행되고 있는지 확인합니다. 

### <a name="error-application-missing-msgraph-api-read-all-audit-log-data-permission"></a>오류: 애플리케이션에 MSGraph API '모든 감사 로그 데이터 읽기' 권한이 없음

[Azure Active Directory reporting API에 액세스하기 위한 필수 구성 요소](howto-configure-prerequisites-for-reporting-api.md)의 단계에 따라 애플리케이션이 적합한 권한 집합으로 실행되고 있는지 확인합니다. 

## <a name="next-steps"></a>다음 단계

* [인증서와 함께 Azure Active Directory reporting API를 사용하여 데이터 가져오기](tutorial-access-api-with-certificates.md).
* [감사 API 참조](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) 
* [로그인 활동 보고서 API 참조](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)
