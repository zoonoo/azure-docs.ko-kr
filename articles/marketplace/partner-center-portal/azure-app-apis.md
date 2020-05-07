---
title: Microsoft 상업적 marketplace에서 Azure 앱을 등록 하기 위한 파트너 센터 제출 API
description: Microsoft 파트너 센터의 상용 marketplace에서 Azure 앱에 대 한 파트너 센터 제출 API를 사용 하기 위한 필수 구성 요소에 대해 알아봅니다.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/10/2019
ms.openlocfilehash: 2c37e51ad0c6618e20d9445fab7472b1a3a72ab9
ms.sourcegitcommit: 3beb067d5dc3d8895971b1bc18304e004b8a19b3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82744888"
---
# <a name="partner-center-submission-api-to-onboard-azure-apps-in-partner-center"></a>파트너 센터에서 Azure 앱을 등록 하기 위한 파트너 센터 제출 API

*파트너 센터 제출 API* 를 사용 하 여 프로그래밍 방식으로 쿼리 하 고 Azure 제품에 대 한 제출을 만들며 게시 합니다.  이 API는 계정이 많은 제품을 관리 하 고 이러한 제품에 대 한 제출 프로세스를 자동화 하 고 최적화 하려는 경우에 유용 합니다.

## <a name="api-prerequisites"></a>API 사전 요구 사항

Azure 제품용 파트너 센터 API를 사용 하기 위해 필요한 몇 가지 프로그래밍 자산이 있습니다. 

- Azure Active Directory 응용 프로그램입니다.
- Azure Active Directory (Azure AD) 액세스 토큰입니다.

### <a name="step-1-complete-prerequisites-for-using-the-partner-center-submission-api"></a>1 단계: 파트너 센터 제출 API를 사용 하기 위한 필수 구성 요소 완료

파트너 센터 제출 API를 호출 하는 코드 작성을 시작 하기 전에 다음 필수 구성 요소를 완료 했는지 확인 합니다.

- 사용자 또는 조직에 Azure AD 디렉터리가 있어야 하 고 해당 디렉터리에 대 한 [전역 관리자](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) 권한이 있어야 합니다. 이미 Office 365 또는 Microsoft의 다른 비즈니스 서비스를 사용 하는 경우 Azure AD 디렉터리가 이미 있습니다. 그렇지 않으면 추가 비용 없이 [파트너 센터에서 새 AZURE AD를 만들](https://docs.microsoft.com/windows/uwp/publish/associate-azure-ad-with-partner-center#create-a-brand-new-azure-ad-to-associate-with-your-partner-center-account) 수 있습니다.

- [AZURE AD 응용 프로그램을 파트너 센터 계정에 연결](https://docs.microsoft.com/windows/uwp/monetize/create-and-manage-submissions-using-windows-store-services#associate-an-azure-ad-application-with-your-windows-partner-center-account) 하 고 테 넌 트 id, 클라이언트 id 및 키를 가져와야 합니다. 이러한 값은 Microsoft Store 제출 API에 대 한 호출에서 사용할 Azure AD 액세스 토큰을 가져오는 데 필요 합니다.

#### <a name="how-to-associate-an-azure-ad-application-with-your-partner-center-account"></a>파트너 센터 계정에 Azure AD 응용 프로그램을 연결 하는 방법

Microsoft Store 제출 API를 사용 하려면 Azure AD 응용 프로그램을 파트너 센터 계정에 연결 하 고, 응용 프로그램에 대 한 테 넌 트 ID와 클라이언트 ID를 검색 하 고, 키를 생성 해야 합니다. Azure AD 응용 프로그램은 파트너 센터 제출 API를 호출 하려는 앱 또는 서비스를 나타냅니다. API에 전달 하는 Azure AD 액세스 토큰을 얻으려면 테 넌 트 ID, 클라이언트 ID 및 키가 필요 합니다.

>[!Note]
>이 작업은 한 번만 수행 하면 됩니다. 테 넌 트 ID, 클라이언트 ID 및 키가 있으면 새 Azure AD 액세스 토큰을 만들어야 할 때마다 다시 사용할 수 있습니다.

1. 파트너 센터에서 조직의 [파트너 센터 계정을 조직의 AZURE AD 디렉터리와 연결](https://docs.microsoft.com/windows/uwp/publish/associate-azure-ad-with-partner-center)합니다.
1. 그런 다음 파트너 센터의 **계정 설정** 섹션에 있는 **사용자** 페이지에서 파트너 센터 계정에 대 한 전송에 액세스 하는 데 사용할 앱 또는 서비스를 나타내는 [Azure AD 응용 프로그램을 추가](https://docs.microsoft.com/windows/uwp/publish/add-users-groups-and-azure-ad-applications#add-azure-ad-applications-to-your-partner-center-account) 합니다. 이 응용 프로그램에 **관리자** 역할을 할당 해야 합니다. 응용 프로그램이 아직 Azure AD 디렉터리에 존재 하지 않는 경우 [파트너 센터에서 새 AZURE ad 응용 프로그램을 만들](https://docs.microsoft.com/windows/uwp/publish/add-users-groups-and-azure-ad-applications#create-a-new-azure-ad-application-account-in-your-organizations-directory-and-add-it-to-your-partner-center-account)수 있습니다.
1. **사용자** 페이지로 돌아가서 Azure AD 응용 프로그램의 이름을 클릭 하 여 응용 프로그램 설정으로 이동 하 고 **테 넌 트 ID** 및 **클라이언트 ID** 값을 복사 합니다.
1. **새 키 추가**를 클릭 합니다. 다음 화면에서 **키** 값을 복사 합니다. 이 페이지를 나가면이 정보에 다시 액세스할 수 없습니다. 자세한 내용은 [AZURE AD 응용 프로그램에 대 한 키 관리](https://docs.microsoft.com/windows/uwp/publish/add-users-groups-and-azure-ad-applications#manage-keys)를 참조 하세요.

### <a name="step-2-obtain-an-azure-ad-access-token"></a>2 단계: Azure AD 액세스 토큰 가져오기

파트너 센터 제출 API의 메서드를 호출 하기 전에 먼저 API의 각 메서드에 대 한 **인증** 헤더에 전달 하는 Azure AD 액세스 토큰을 가져와야 합니다. 액세스 토큰을 얻은 후 만료 되기 전에 사용 하는 데 60 분이 있습니다. 토큰이 만료 된 후에는 API에 대 한 후속 호출에서 토큰을 계속 사용할 수 있도록 토큰을 새로 고칠 수 있습니다.

액세스 토큰을 가져오려면 [클라이언트 자격 증명을 사용 하 여 서비스 호출](https://azure.microsoft.com/documentation/articles/active-directory-protocols-oauth-service-to-service/) 에 대 한 지침에 따라를 `HTTP POST` `https://login.microsoftonline.com/<tenant_id>/oauth2/token` 끝점으로 보냅니다. 샘플 요청은 다음과 같습니다.

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

및 client_id 및 *client_secret* 매개 변수의 *tenant_id* 값에 대해 이전 섹션의 파트너 센터에서 검색 한 응용 프로그램의 테 넌 트 id, 클라이언트 id 및 키를 지정 합니다. *client_id* `POST URI` *리소스* 매개 변수의 경우를 지정 `https://api.partner.microsoft.com`해야 합니다.

### <a name="step-3-use-the-microsoft-store-submission-api"></a>3 단계: Microsoft Store 제출 API 사용

Azure AD 액세스 토큰을 만든 후 파트너 센터 제출 API에서 메서드를 호출할 수 있습니다. 제출을 만들거나 업데이트 하려면 일반적으로 파트너 센터 제출 API에서 특정 순서로 여러 메서드를 호출 합니다. 각 시나리오와 각 방법의 구문에 대 한 자세한 내용은 수집 API swagger를 참조 하세요.

https://apidocs.microsoft.com/services/partneringestion/

## <a name="next-steps"></a>다음 단계

* [AZURE VM 기술 자산 만들기](create-azure-container-technical-assets.md) 를 만드는 방법에 대해 알아봅니다.
* [Azure Container 제품](create-azure-container-offer.md) 을 만드는 방법 알아보기
