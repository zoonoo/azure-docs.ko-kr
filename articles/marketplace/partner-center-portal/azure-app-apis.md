---
title: 상용 마켓플레이스에서 Azure 앱용 온보딩 API
description: Microsoft 파트너 센터의 상용 마켓플레이스에서 Azure 앱에 대한 API 필수 구성 조건입니다.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/10/2019
ms.openlocfilehash: cc4d56058ce3985ec3a1d9124ef4ec73ff6be1a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80279762"
---
# <a name="api-for-onboarding-azure-apps-in-partner-center"></a>파트너 센터에서 Azure 앱 온보딩을 위한 API

파트너 *센터 제출 API를* 사용하여 프로그래밍 방식으로 쿼리하고, 제출을 만들고, Azure 오퍼를 게시합니다.  이 API는 계정에서 많은 오퍼를 관리하고 이러한 오퍼에 대한 제출 프로세스를 자동화하고 최적화하려는 경우에 유용합니다.

## <a name="api-prerequisites"></a>API 전제 조건

Azure 제품에 대한 파트너 센터 API를 사용하는 데 필요한 몇 가지 프로그래밍 방식 자산이 있습니다. 

- Azure Active 디렉터리 응용 프로그램입니다.
- Azure Active Directory(Azure AD) 액세스 토큰입니다.

### <a name="step-1-complete-prerequisites-for-using-the-partner-center-submission-api"></a>1단계: 파트너 센터 제출 API 사용에 대한 완벽한 전제 조건

파트너 센터 제출 API를 호출하는 코드를 작성하기 전에 다음 필수 구성 조건을 완료했는지 확인합니다.

- 사용자(또는 조직에)에는 Azure AD 디렉터리가 있어야 하며 디렉터리에 대한 [전역 관리자](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) 권한이 있어야 합니다. 이미 Microsoft의 Office 365 또는 기타 비즈니스 서비스를 사용하는 경우 Azure AD 디렉터리가 이미 있습니다. 그렇지 않으면 파트너 센터에서 추가 비용 없이 [새 Azure AD를 만들](https://docs.microsoft.com/windows/uwp/publish/associate-azure-ad-with-partner-center#create-a-brand-new-azure-ad-to-associate-with-your-partner-center-account) 수 있습니다.

- Azure [AD 응용 프로그램을 파트너 센터 계정에 연결하고](https://docs.microsoft.com/windows/uwp/monetize/create-and-manage-submissions-using-windows-store-services#associate-an-azure-ad-application-with-your-windows-partner-center-account) 테넌트 ID, 클라이언트 ID 및 키를 가져와야 합니다. Microsoft Store 제출 API에 대한 호출에서 사용할 Azure AD 액세스 토큰을 얻으려면 이러한 값이 필요합니다.

#### <a name="how-to-associate-an-azure-ad-application-with-your-partner-center-account"></a>Azure AD 응용 프로그램을 파트너 센터 계정과 연결하는 방법

Microsoft Store 제출 API를 사용하려면 Azure AD 응용 프로그램을 파트너 센터 계정과 연결하고 응용 프로그램에 대한 테넌트 ID 및 클라이언트 ID를 검색하고 키를 생성해야 합니다. Azure AD 응용 프로그램은 파트너 센터 제출 API를 호출하려는 앱 또는 서비스를 나타냅니다. API에 전달하는 Azure AD 액세스 토큰을 얻으려면 테넌트 ID, 클라이언트 ID 및 키가 필요합니다.

>[!Note]
>이 작업은 한 번만 수행하면 됩니다. 테넌트 ID, 클라이언트 ID 및 키가 있으면 새 Azure AD 액세스 토큰을 만들어야 할 때마다 다시 사용할 수 있습니다.

1. 파트너 센터에서 [조직의 파트너 센터 계정을 조직의 Azure AD 디렉터리와 연결합니다.](https://docs.microsoft.com/windows/uwp/publish/associate-azure-ad-with-partner-center)
1. 그런 다음 파트너 센터의 **계정 설정** 섹션의 **사용자** 페이지에서 파트너 센터 계정의 제출에 액세스하는 데 사용할 앱 또는 서비스를 나타내는 [Azure AD 응용 프로그램을 추가합니다.](https://docs.microsoft.com/windows/uwp/publish/add-users-groups-and-azure-ad-applications#add-azure-ad-applications-to-your-partner-center-account) 이 응용 프로그램을 **관리자** 역할을 할당해야 합니다. 응용 프로그램이 Azure AD 디렉터리에 아직 없는 경우 [파트너 센터에서 새 Azure AD 응용 프로그램을 만들](https://docs.microsoft.com/windows/uwp/publish/add-users-groups-and-azure-ad-applications#create-a-new-azure-ad-application-account-in-your-organizations-directory-and-add-it-to-your-partner-center-account)수 있습니다.
1. **사용자** 로 돌아가고 Azure AD 응용 프로그램의 이름을 클릭하여 응용 프로그램 설정으로 이동하고 **테넌트 ID** 및 **클라이언트 ID** 값을 복사합니다.
1. **새 키 추가를 클릭합니다.** 다음 화면에서 **키** 값을 복사합니다. 이 페이지를 떠난 후에는 이 정보에 다시 액세스할 수 없습니다. 자세한 내용은 [Azure AD 응용 프로그램의 키 관리를](https://docs.microsoft.com/windows/uwp/publish/add-users-groups-and-azure-ad-applications#manage-keys)참조하십시오.

### <a name="step-2-obtain-an-azure-ad-access-token"></a>2단계: Azure AD 액세스 토큰 구하기

파트너 센터 제출 API의 메서드를 호출하기 전에 API에서 각 메서드의 **권한 부여** 헤더에 전달하는 Azure AD 액세스 토큰을 먼저 가져와야 합니다. 액세스 토큰을 얻은 후 만료되기 전에 60분 동안 사용할 수 있습니다. 토큰이 만료된 후 토큰을 새로 고쳐 API에 대한 이후 호출에서 계속 사용할 수 있습니다.

액세스 토큰을 얻으려면 [클라이언트 자격 증명을 사용하여 서비스 호출에](https://azure.microsoft.com/documentation/articles/active-directory-protocols-oauth-service-to-service/) `HTTP POST` 대한 `https://login.microsoftonline.com/<tenant_id>/oauth2/token` 서비스의 지침을 따라 엔드포인트로 전송합니다. 다음은 샘플 요청입니다.

JSONCopy
```Json
POST https://login.microsoftonline.com/<tenant_id>/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded; charset=utf-8

grant_type=client_credentials
&client_id=<your_client_id>
&client_secret=<your_client_secret>
&resource= https://api.partner.microsoft.com
```

client_id *및* *client_secret* 매개 변수의 tenant_id 값에 대해 이전 섹션의 파트너 센터에서 검색한 응용 프로그램의 테넌트 ID, 클라이언트 ID 및 키를 지정합니다. *tenant_id* `POST URI` *리소스* 매개 변수의 경우 `https://api.partner.microsoft.com`을 지정해야 합니다.

### <a name="step-3-use-the-microsoft-store-submission-api"></a>3단계: Microsoft 스토어 제출 API 사용

Azure AD 액세스 토큰이 있으면 파트너 센터 제출 API에서 메서드를 호출할 수 있습니다. 제출을 만들거나 업데이트하려면 일반적으로 파트너 센터 제출 API에서 여러 메서드를 특정 순서로 호출합니다. 각 시나리오 및 각 메서드의 구문에 대한 자세한 내용은 인기 변환 API 스웨거를 참조하십시오.

https://apidocs.microsoft.com/services/partneringestion/
