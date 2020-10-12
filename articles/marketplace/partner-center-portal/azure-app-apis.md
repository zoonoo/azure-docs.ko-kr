---
title: Microsoft 상업용 Marketplace에서 Azure 앱을 온보딩하기 위한 파트너 센터 제출 API
description: Microsoft 파트너 센터의 상업용 Marketplace에서 Azure 앱에 대한 파트너 센터 제출 API를 사용하기 위한 필수 구성 요소에 대해 알아봅니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: mingshen
author: mingshen-ms
ms.openlocfilehash: fec52caa3cb7565a86a6c50d300fe448b6518457
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87320071"
---
# <a name="partner-center-submission-api-to-onboard-azure-apps-in-partner-center"></a>파트너 센터에서 Azure 앱을 온보딩하기 위한 파트너 센터 제출 API

*파트너 센터 제출 API*를 사용하여 프로그래밍 방식으로 쿼리하고, Azure 제품에 대한 제출을 만들고, 게시할 수 있습니다.  이 API는 계정이 많은 제품을 관리하고, 이러한 제품에 대한 제출 프로세스를 자동화하고 최적화하려는 경우에 유용합니다.

## <a name="api-prerequisites"></a>API 사전 요구 사항

Azure 제품에 파트너 센터 API를 사용하려면 몇 가지 프로그래밍 자산이 필요합니다. 

- Azure Active Directory 애플리케이션
- Azure AD(Azure Active Directory) 액세스 토큰

### <a name="step-1-complete-prerequisites-for-using-the-partner-center-submission-api"></a>1단계: 파트너 센터 제출 API를 사용하기 위한 필수 조건 완료

파트너 센터 제출 API를 호출하는 코드를 작성하기 전에 다음 필수 조건을 완료했는지 확인합니다.

- 사용자(또는 조직)는 Azure AD 디렉터리가 있어야 하고 디렉터리에 대한 [전역 관리자](../../active-directory/users-groups-roles/directory-assign-admin-roles.md) 권한이 있어야 합니다. Microsoft에서 이미 Microsoft 365 또는 다른 비즈니스 서비스를 사용 하는 경우 Azure AD 디렉터리가 이미 있습니다. 그렇지 않은 경우 추가 비용 없이 [파트너 센터에서 새 Azure AD를 만들면 됩니다](https://docs.microsoft.com/windows/uwp/publish/associate-azure-ad-with-partner-center#create-a-brand-new-azure-ad-to-associate-with-your-partner-center-account).

- [Azure AD 애플리케이션을 파트너 센터 계정에 연결](https://docs.microsoft.com/windows/uwp/monetize/create-and-manage-submissions-using-windows-store-services#associate-an-azure-ad-application-with-your-windows-partner-center-account)하고 테넌트 ID, 클라이언트 ID 및 키를 가져와야 합니다. Microsoft Store 제출 API를 호출하는 데 사용할 Azure AD 액세스 토큰을 얻으려면 이러한 값이 필요합니다.

#### <a name="how-to-associate-an-azure-ad-application-with-your-partner-center-account"></a>파트너 센터 계정에 Azure AD 애플리케이션을 연결하는 방법

Microsoft Store 제출 API를 사용하려면 Azure AD 애플리케이션을 파트너 센터 계정에 연결하고, 애플리케이션에 대한 테넌트 ID와 클라이언트 ID를 검색하고, 키를 생성해야 합니다. Azure AD 애플리케이션은 파트너 센터 제출 API를 호출하려는 앱 또는 서비스를 나타냅니다. API에 전달하는 Azure AD 액세스 토큰을 얻으려면 테넌트 ID, 클라이언트 ID 및 키가 필요합니다.

>[!Note]
>이 작업은 한 번만 수행하면 됩니다. 테넌트 ID, 클라이언트 ID 및 키가 있으면 새 Azure AD 액세스 토큰을 만들어야 할 때마다 다시 사용할 수 있습니다.

1. 파트너 센터에서 [조직의 파트너 센터 계정을 조직의 Azure AD 디렉터리에 연결합니다](https://docs.microsoft.com/windows/uwp/publish/associate-azure-ad-with-partner-center).
1. 그런 다음 파트너 센터의 **계정 설정** 섹션에 있는 **사용자** 페이지에서 파트너 센터 계정에 대한 전송에 액세스하는 데 사용할 앱 또는 서비스를 나타내는 [Azure AD 애플리케이션을 추가합니다](https://docs.microsoft.com/windows/uwp/publish/add-users-groups-and-azure-ad-applications#add-azure-ad-applications-to-your-partner-center-account). 이 애플리케이션을 **관리자** 역할에 할당해야 합니다. 애플리케이션이 아직 Azure AD 디렉터리에 존재하지 않는 경우 [파트너 센터에서 새 Azure AD 애플리케이션 만들 수 있습니다](https://docs.microsoft.com/windows/uwp/publish/add-users-groups-and-azure-ad-applications#create-a-new-azure-ad-application-account-in-your-organizations-directory-and-add-it-to-your-partner-center-account).
1. **사용자** 페이지로 돌아가 Azure AD 애플리케이션의 이름을 클릭하여 애플리케이션 설정으로 이동한 다음 **테넌트 ID**와 **클라이언트 ID** 값을 복사합니다.
1. **새 키 추가**를 클릭합니다. 다음 화면에서 **키** 값을 복사합니다. 이 페이지를 나가면 이 정보에 다시 액세스할 수 없습니다. 자세한 내용은 [Azure AD 애플리케이션 키 관리](https://docs.microsoft.com/windows/uwp/publish/add-users-groups-and-azure-ad-applications#manage-keys)를 참조하세요.

### <a name="step-2-obtain-an-azure-ad-access-token"></a>2단계: Azure AD 액세스 토큰 가져오기

파트너 센터 제출 API의 메서드를 호출하기 전에 먼저 API의 각 메서드에 대한 **권한 부여** 헤더에 전달하는 Azure AD 액세스 토큰을 가져와야 합니다. 액세스 토큰을 얻은 후 만료되기 전에 60분 동안 사용할 수 있습니다. 토큰이 만료된 후 나중에 API를 호출할 때 토큰을 계속 사용할 수 있도록 토큰을 새로 고칠 수 있습니다.

액세스 토큰을 얻으려면 [클라이언트 자격 증명을 사용한 서비스 간 호출](https://azure.microsoft.com/documentation/articles/active-directory-protocols-oauth-service-to-service/)의 지침에 따라 `HTTP POST`를 `https://login.microsoftonline.com/<tenant_id>/oauth2/token` 엔드포인트로 보냅니다. 다음은 샘플 요청입니다.

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

`POST URI`의 *tenant_id* 값 및 *client_id*와 *client_secret* 매개 변수에 이전 섹션의 파트너 센터에서 검색한 애플리케이션의 테넌트 ID, 클라이언트 ID 및 키를 지정합니다. *리소스* 매개 변수에는 `https://api.partner.microsoft.com`을 지정해야 합니다.

### <a name="step-3-use-the-microsoft-store-submission-api"></a>3단계: Microsoft Store 제출 API 사용

Azure AD 액세스 토큰이 있으면 파트너 센터 제출 API에서 메서드를 호출할 수 있습니다. 제출을 만들거나 업데이트하려면 일반적으로 파트너 센터 제출 API에서 특정 순서로 여러 메서드를 호출합니다. 각 시나리오와 각 메서드의 구문에 대한 자세한 내용은 수집 API Swagger를 참조하세요.

https://apidocs.microsoft.com/services/partneringestion/

## <a name="next-steps"></a>다음 단계

* [Azure VM 기술 자산](create-azure-container-technical-assets.md)을 만드는 방법에 대해 알아봅니다.
* [Azure Container 제품](create-azure-container-offer.md)을 만드는 방법에 대해 알아봅니다.
