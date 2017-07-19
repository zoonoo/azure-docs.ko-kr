---
title: "Azure에서 호스트되는 API를 PowerApps 및 Microsoft Flow로 내보내기 | Microsoft Docs"
description: "App Service에 호스트되는 API를 PowerApps 및 Microsoft Flow로 내보내는 방법에 대한 개요"
services: app-service
documentationcenter: 
author: mattchenderson
manager: erikre
editor: 
ms.assetid: 
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 06/20/2017
ms.author: mahender
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: 0d166a2e5b60c3b9f911f9fc3ad49ad7f252abb4
ms.contentlocale: ko-kr
ms.lasthandoff: 07/01/2017


---
# <a name="exporting-an-azure-hosted-api-to-powerapps-and-microsoft-flow"></a>Azure에서 호스트되는 API를 PowerApps 및 Microsoft Flow로 내보내기

## <a name="creating-custom-connectors-for-powerapps-and-microsoft-flow"></a>PowerApps 및 Microsoft Flow를 위한 사용자 지정 커넥터

[PowerApps](https://powerapps.com)는 플랫폼 간에 데이터와 작업을 연결하는 사용자 지정 비즈니스 앱을 빌드하고 사용할 수 있는 서비스입니다. [Microsoft Flow](https://flow.microsoft.com)는 자주 사용하는 앱과 서비스 간의 워크플로 및 비즈니스 프로세스를 간단하게 자동화할 수 있습니다. PowerApps와 Microsoft Flow에는 Office 365, Dynamics 365, Salesforce 등 다양한 데이터 원본 커넥터가 기본적으로 제공됩니다. 하지만 사용자는 조직에서 개발하는 데이터 원본 및 API도 활용할 수 있어야 합니다.

마찬가지로 조직 내에서 자신의 API를 보다 광범위하게 노출하려는 개발자는 PowerApps 및 Microsoft Flow 사용자에게 자신의 API를 제공하려 할 것입니다. 이 토픽에서는 Azure App Service 또는 Azure Functions로 작성된 API를 PowerApps 및 Microsoft Flow에 노출하는 방법을 보여줍니다. [Azure App Service](https://azure.microsoft.com/services/app-service/)는 개발자가 빠르고 쉽게 엔터프라이즈급 웹, 모바일 및 API 응용 프로그램을 개발할 수 있게 해주는 PaaS(Platform-as-a-Service) 제품입니다. [Azure Functions](https://azure.microsoft.com/services/functions/)는 시스템의 다른 부분에 반응하여 수요에 따라 규모를 조정하는 코드를 신속하게 작성할 수 있는 이벤트 기반 무서버 계산 솔루션입니다.

이러한 서비스에 대해 자세히 알아보려면 다음을 참조하세요.
- [PowerApps 학습 도우미](https://powerapps.microsoft.com/guided-learning/learning-introducing-powerapps/) 
- [Microsoft Flow 학습 도우미](https://flow.microsoft.com/guided-learning/learning-introducing-flow/)
- [App Service란?](https://docs.microsoft.com/azure/app-service/app-service-value-prop-what-is)
- [Azure Functions란?](https://docs.microsoft.com/azure/azure-functions/functions-overview)

## <a name="sharing-an-api-definition"></a>API 정의 공유

API는 보통 [OpenAPI 문서](https://www.openapis.org/)(“Swagger” 문서라고도 함)를 사용하는 것으로 설명됩니다. 여기에는 어떤 작업이 가능하며 데이터가 어떻게 구조화되는지에 대한 모든 정보가 포함됩니다. PowerApps 및 Microsoft Flow는 모든 OpenAPI 2.0 문서를 위한 사용자 지정 커넥터를 만들 수 있습니다. 사용자 지정 커넥터가 만들어지면 기본 제공 커넥터 중 하나와 동일한 방식으로 사용할 수 있으며 응용 프로그램에 신속하게 통합할 수 있습니다.

Azure App Service 및 Azure Functions는 OpenAPI 문서를 만들고, 호스트하고, 관리할 수 있는 [기본 지원](https://docs.microsoft.com/azure/app-service-api/app-service-api-metadata)을 제공합니다. 웹, 모바일, API 또는 함수 앱용 사용자 지정 커넥터를 만들려면 PowerApps 및 Flow에 정의 복사본을 제공해야 합니다.

> [!NOTE]
> API 정의 복사본이 사용되므로 PowerApps 및 Microsoft Flow는 응용 프로그램 업데이트 또는 주요 변경 내용을 즉시 파악할 수 없습니다. 새 버전의 API가 출시되면 새 버전에 대해 이러한 단계를 반복해야 합니다. 

PowerApps 및 Microsoft Flow에 앱에 대한 호스트된 API 정의를 제공하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com)을 열고 App Service 또는 Azure Functions 응용 프로그램으로 이동합니다.

    Azure App Service를 사용하는 경우 설정 목록에서 **API 정의**를 선택합니다. 
    
    Azure Functions를 사용하는 경우 함수 앱을 선택한 다음 **플랫폼 기능**과 **API 정의**를 차례로 선택합니다. 대신 **API 정의(미리 보기)** 탭을 열도록 선택할 수도 있습니다.

2. API 정의가 제공되는 경우 **PowerApps + Microsoft Flow로 내보내기** 단추가 보일 것입니다. 이 단추를 클릭하면 내보내기 프로세스가 시작됩니다.

3. **내보내기 모드**를 선택합니다. 내보내기 모드가 커넥터를 만들기 위해 수행해야 할 단계를 결정합니다. App Service는 PowerApps 및 Microsoft Flow에 API 정의를 제공하기 위한 두 가지 옵션을 제공합니다.

    **기본**을 사용하면 Azure Portal 내에서 사용자 지정 커넥터를 만들 수 있습니다. 이 모드를 사용하려면 현재 로그인한 사용자에게 대상 환경에서 커넥터를 만들 수 있는 권한이 필요합니다. 이 모드는 해당 요구 사항을 충족할 수 있는 경우 권장되는 방법입니다. 이 모드를 사용하는 경우 아래 [기본 내보내기](#express) 지침을 따릅니다.

    **수동**을 사용하면 PowerApps 또는 Microsoft Flow 포털을 사용하여 가져올 수 있는 API 정의 복사본을 내보낼 수 있습니다. 이 모드는 Azure 사용자와 커넥터를 만들 수 있는 권한이 있는 사용자가 다른 사람이거나 다른 테넌트에서 커넥터를 만들어야 하는 경우 권장되는 방법입니다. 이 모드를 사용하는 경우 아래 [수동 내보내기 및 가져오기](#manual) 지침을 따릅니다.

<a name="express"></a>
## <a name="express-export"></a>기본 내보내기

이 섹션에서는 Azure Portal 내에서 새 사용자 지정 커넥터를 만듭니다. 내보내려는 테넌트에 로그인한 상태여야 하고, 대상 환경에서 사용자 지정 커넥터를 만들 수 있는 권한이 있어야 합니다.

1. 커넥터를 만들 환경을 선택합니다. 그런 다음 사용자 지정 커넥터의 이름을 제공합니다.

2. API 정의에 보안 정의가 포함된 경우 2단계에서 보안 정의가 호출됩니다. 필요한 경우 사용자에게 API에 대한 액세스 권한을 부여하는 데 필요한 보안 구성 세부 정보를 제공합니다. 자세한 내용은 아래 [인증](#auth)을 참조하세요. 

3. **확인**을 클릭하여 사용자 지정 커넥터를 만듭니다.


<a name="manual"></a>
## <a name="manual-export-and-import"></a>수동 내보내기 및 가져오기

웹, 모바일, API 또는 함수 앱을 위한 사용자 지정 커넥터를 만들려면 다음 두 단계가 필요합니다.

1. [App Service 또는 Azure Functions에서 API 정의 검색](#export)
2. [PowerApps 및 Microsoft Flow로 API 정의 가져오기](#import)

특정 사용자가 두 작업을 모두 수행할 권한을 갖고 있지 않아 조직 내 서로 다른 두 사람이 이러한 두 단계를 각각 따로 수행해야 할 수도 있습니다. 이 경우 App Service 또는 Azure Functions 응용 프로그램에 대한 참여자 액세스 권한을 가진 개발자가 API 정의(단일 JSON 파일)을 가져오거나 링크를 연결해야 합니다. 그런 다음 PowerApps 또는 Microsoft Flow 소유자에 게 해당 정의를 제공해야 합니다. 해당 소유자는 메타데이터를 사용하여 사용자 지정 커넥터를 만들 수 있습니다.

<a name="export"></a>
### <a name="retrieving-the-api-definition-from-app-service-or-azure-functions"></a>App Service 또는 Azure Functions에서 API 정의 검색

이 섹션에서는 나중에 PowerApps 또는 Microsoft Flow 포털에서 사용할 수 있도록 App Service API에 대한 API 정의를 내보냅니다.

1. **API 정의 다운로드** 또는 **링크 받기** 중 하나를 선택할 수 있습니다. 어떤 것을 선택하든 다음 섹션에 결과가 제공됩니다. 다음 옵션 중 하나를 선택하고 지침을 따릅니다.
 
2. API 정의에 보안 정의가 포함된 경우 2단계에서 보안 정의가 호출됩니다. 가져오는 동안 PowerApps 및 Microsoft Flow가 보안 정의를 감지하고 보안 정보를 요청합니다. 다음 섹션에서 사용할 각 정의와 관련된 자격 증명을 수집합니다. 자세한 내용은 아래 [인증](#auth)을 참조하세요. 

<a name="import"></a>
### <a name="importing-the-api-definition-into-powerapps-and-microsoft-flow"></a>PowerApps 및 Microsoft Flow로 API 정의 가져오기

이 섹션에서는 앞에서 얻은 API 정의를 사용하여 PowerApps 및 Microsoft Flow에 사용자 지정 커넥터를 만들 것입니다. 사용자 지정 커넥터는 두 서비스 간에 공유되므로 정의를 한 번만 가져오면 됩니다. 사용자 지정 커넥터에 대한 자세한 내용은 [PowerApps에서 사용자 지정 커넥터 등록 및 사용] 및 [Microsoft Flow에서 사용자 지정 커넥터 등록 및 사용]을 참조하세요.

1. [Powerapps 웹 포털](https://web.powerapps.com) 또는 [Microsoft Flow 웹 포털](https://flow.microsoft.com/)을 열고 로그인합니다. 

2. 페이지 오른쪽 위에서 **설정** 단추(기어 모양 아이콘)를 클릭하고 **사용자 지정 커넥터**를 선택합니다. 

3. **사용자 지정 커넥터 만들기**를 클릭합니다.

4. **일반** 탭에서 API 이름을 입력한 다음 OpenAPI 정의를 업로드하거나 메타데이터 URL에 붙여 넣습니다. **계속**을 클릭합니다.

4. **보안** 탭에서 인증 세부 정보를 입력하라는 메시지가 나오면 이전 섹션에서 얻은 값을 입력합니다. 그렇지 않으면 다음 단계를 진행합니다.

5. **정의** 탭에서 OpenAPI 파일에 정의된 모든 작업이 자동으로 채워집니다. 필요한 모든 작업이 정의되면 다음 단계로 이동할 수 있습니다. 그렇지 않은 경우 여기에서 작업을 추가하고 수정할 수 있습니다.

6. **커넥터 만들기**를 클릭합니다. API 호출을 테스트하려는 경우 다음 단계로 이동합니다.

7. **테스트** 탭에서 연결을 만들고, 테스트할 작업을 선택하고, 해당 작업에 필요한 모든 데이터를 입력합니다.

8. **작업 테스트**를 클릭합니다.


<a name="auth"></a>
## <a name="authentication"></a>인증

PowerApps 및 Microsoft Flow는 기본적으로 사용자 지정 커넥터 사용자를 로그인하는 데 사용할 수 있는 ID 공급자 컬렉션을 지원합니다. API에서 인증을 요구하는 경우 OpenAPI 문서에서 _보안 정의_로 캡처되었는지 확인합니다. 내보내는 동안 PowerApps 및 Microsoft Flow에서 로그인 작업을 수행할 수 있게 해주는 구성 값을 제공해야 합니다.

이 섹션에서는 기본 흐름에서 지원되는 인증 형식인 API 키, Azure Active Directory 및 제네릭 OAuth 2.0을 설명합니다. 각각에 필요한 공급자 및 자격 증명의 전체 목록은 [PowerApps에서 사용자 지정 커넥터 등록 및 사용] 및 [Microsoft Flow에서 사용자 지정 커넥터 등록 및 사용]을 참조하세요.

### <a name="api-key"></a>API 키
이 보안 체계가 사용되는 경우 커넥터의 사용자가 연결을 만들 때 키를 제공하라는 메시지가 표시됩니다. 사용자가 필요한 키를 알 수 있도록 API 키 이름을 제공할 수 있습니다. Azure Functions의 경우 이 키는 일반적으로 호스트 키 중 하나가 되며 함수 앱 내의 여러 함수를 포괄합니다.

### <a name="azure-active-directory"></a>Azure Active Directory
AAD 로그인이 필요한 사용자 지정 커넥터를 구성할 때는 두 가지 AAD 응용 프로그램 등록이 필요합니다. 하나는 백 엔드 API를 모델링하기 위한 것이고, 다른 하나는 PowerApps 및 Flow의 커넥터를 모델링하기 위한 것입니다.

API는 첫 번째 등록과 작동하도록 구성되어야 하고, [App Service 인증/권한 부여](https://docs.microsoft.com/azure/app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication) 기능을 사용한 경우 이러한 구성이 이미 수행된 상태가 됩니다.

[AAD 응용 프로그램 추가](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications#adding-an-application)에 설명된 단계를 사용하여 커넥터에 대한 두 번째 등록을 수동으로 만들어야 합니다. 등록에는 API 및 `https://msmanaged-na.consent.azure-apim.net/redirect`의 회신 URL에 대한 위임된 액세스가 필요합니다. Azure Resource Manager의 API를 대체하는 방법에 대한 자세한 내용은 [이 예제](
https://powerapps.microsoft.com/tutorials/customapi-azure-resource-manager-tutorial/)를 참조하세요.

다음과 같은 구성 값이 필요합니다.
- **클라이언트 ID** - 커넥터 AAD 등록의 클라이언트 ID
- **클라이언트 암호** - 커넥터 AAD 등록의 클라이언트 암호
- **로그인 URL** - AAD의 기준 URL. 공용 Azure에서는 일반적으로 `https://login.windows.net`이 됩니다.
- **테넌트 ID** - 로그인에 사용할 테넌트의 ID. “common” 또는 커넥터를 만들 테넌트의 ID여야 합니다.
- **리소스 URL** - API 백 엔드 AAD 등록의 리소스 URL

> [!IMPORTANT]
> 다른 사용자가 수동 흐름의 일부로 PowerApps 및 Microsoft Flow로 API 정의를 가져오는 경우 API의 리소스 URL뿐만 아니라 **커넥터 등록**의 클라이언트 ID 및 클라이언트 암호를 해당 사용자에게 제공해야 합니다. 이러한 비밀은 안전하게 관리해야 합니다. **API 보안 자격 증명 자체를 공유하지 마세요.**

### <a name="generic-oauth-20"></a>제네릭 OAuth 2.0
제네릭 OAuth 2.0 지원을 통해 모든 OAuth 2.0 공급자와 통합할 수 있습니다. 따라서 기본적으로 지원되지 않는 모든 사용자 지정 공급자를 가져올 수 있습니다.

다음과 같은 구성 값이 필요합니다.
- **클라이언트 ID** - OAuth 2.0 클라이언트 ID
- **클라이언트 암호** - OAuth 2.0 클라이언트 암호
- **권한 부여 URL** - OAuth 2.0 권한 부여 URL
- **토큰 URL** - OAuth 2.0 토큰 URL
- **새로 고침 URL** - OAuth 2.0 새로 고침 URL



[PowerApps에서 사용자 지정 커넥터 등록 및 사용]: https://powerapps.microsoft.com/tutorials/register-custom-api/
[Microsoft Flow에서 사용자 지정 커넥터 등록 및 사용]: https://flow.microsoft.com/documentation/register-custom-api/

