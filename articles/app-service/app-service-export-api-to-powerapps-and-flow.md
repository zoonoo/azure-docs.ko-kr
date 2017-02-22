---
title: "Azure 호스팅 API를 PowerApps 및 Microsoft Flow로 내보내기 | Microsoft Docs"
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
ms.date: 02/06/2017
ms.author: mahender
translationtype: Human Translation
ms.sourcegitcommit: 060fbb885f254c0de02fe422460e8e8d659ac848
ms.openlocfilehash: 503f17a629527e8c9a3bfe6cde1da31dcf1f18ef


---
# <a name="exporting-an-azure-hosted-api-to-powerapps-and-microsoft-flow"></a>Azure 호스팅 API를 PowerApps 및 Microsoft Flow로 내보내기

## <a name="creating-custom-apis-for-powerapps-and-microsoft-flow"></a>PowerApps 및 Microsoft Flow를 위한 사용자 지정 API

[Microsoft 비즈니스 응용 프로그램 플랫폼](https://businessplatform.microsoft.com/)은 고급 사용자가 더 많은 일을 할 수 있도록 도와주는 다양한 제품을 포함하고 있습니다. [PowerApps](https://powerapps.com)는 플랫폼 간에 데이터와 작업을 연결하는 사용자 지정 비즈니스 앱을 빌드하고 사용할 수 있는 서비스입니다. [Microsoft Flow](https://flow.microsoft.com)는 자주 사용하는 앱과 서비스 간의 워크플로 및 비즈니스 프로세스를 간단하게 자동화할 수 있습니다. PowerApps와 Microsoft Flow에는 Office 365, Dynamics 365, Salesforce 등 다양한 데이터 원본 커넥터가 기본적으로 제공됩니다. 하지만 사용자는 조직에서 개발하는 데이터 원본 및 API도 활용할 수 있어야 합니다.

마찬가지로 조직 내에서 자신의 API를 보다 광범위하게 노출하려는 개발자는 PowerApps 및 Microsoft Flow 사용자에게 자신의 API를 제공하려 할 것입니다. 이 토픽에서는 Azure App Service 또는 Azure Functions로 작성된 API를 PowerApps 및 Microsoft Flow에 노출하는 방법을 보여줍니다. [Azure App Service](https://azure.microsoft.com/services/app-service/)는 개발자가 빠르고 쉽게 엔터프라이즈급 웹, 모바일 및 API 응용 프로그램을 개발할 수 있게 해주는 PaaS(Platform-as-a-Service) 제품입니다. [Azure Functions](https://azure.microsoft.com/services/functions/)는 시스템의 다른 부분에 반응하여 수요에 따라 규모를 조정하는 코드를 신속하게 작성할 수 있는 이벤트 기반 무서버 계산 솔루션입니다.

이러한 서비스에 대해 자세히 알아보려면 다음을 참조하세요.
- [PowerApps 학습 도우미](https://powerapps.microsoft.com/guided-learning/learning-introducing-powerapps/) 
- [Microsoft Flow 학습 도우미](https://flow.microsoft.com/guided-learning/learning-introducing-flow/)
- [App Service란?](https://docs.microsoft.com/azure/app-service/app-service-value-prop-what-is)
- [Azure Functions란?](https://docs.microsoft.com/azure/azure-functions/functions-overview)

## <a name="sharing-an-api-definition"></a>API 정의 공유

API는 종종 [개방형 API 문서](https://www.openapis.org/)("Swagger" 문서라고도 함)를 사용하는 것으로 설명됩니다. 여기에는 어떤 작업이 가능하며 데이터가 어떻게 구조화되는지에 대한 모든 정보가 포함됩니다. PowerApps와 Microsoft Flow는 모든 개방형 API 2.0 문서를 위한 사용자 지정 API를 만들 수 있습니다. 사용자 지정 API가 만들어지면 기본 제공 커넥터 중 하 나와 동일한 방식으로 사용할 수 있으며 응용 프로그램에 신속하게 통합할 수 있습니다.

Azure App Service와 Azure Functions는 개방형 API 문서를 만들고, 호스트하고, 관리할 수 있는 [기본 지원](https://docs.microsoft.com/azure/app-service-api/app-service-api-metadata)을 제공합니다. 웹, 모바일, API 또는 함수 앱을 위한 사용자 지정 커넥터를 만들려면 다음 두 단계가 필요합니다.

1. [App Service 또는 Azure Functions에서 API 정의 검색](#export)
2. [PowerApps로 API 정의 가져오기](#import)

특정 사용자가 두 작업을 모두 수행할 권한을 갖고 있지 않아 조직 내 서로 다른 두 사람이 이러한 두 단계를 각각 따로 수행해야 할 수도 있습니다. 이 경우 App Service 또는 Azure Functions 응용 프로그램에 대한 참여자 액세스 권한을 가진 개발자가 API 정의(단일 JSON 파일)을 가져오거나 링크를 연결해야 합니다. 그런 다음 PowerApps 또는 Microsoft Flow 소유자에 게 해당 정의를 제공해야 합니다. 해당 소유자는 메타데이터를 사용하여 사용자 지정 API를 만들 수 있습니다.

> [!NOTE]
> API 정의 복사본이 사용되므로 PowerApps 및 Microsoft Flow는 응용 프로그램 업데이트 또는 주요 변경 내용을 즉시 파악할 수 없습니다. 새 버전의 API가 출시되면 새 버전에 대해 이러한 단계를 반복해야 합니다. 

<a name="export"></a>
## <a name="retrieving-the-api-definition-from-app-service-or-azure-functions"></a>App Service 또는 Azure Functions에서 API 정의 검색

이 섹션에서는 나중에 PowerApps에서 사용할 수 있도록 App Service API에 대한 API 정의를 내보낼 것입니다.

1. [Azure Portal](https://portal.azure.com)을 열고 App Service 또는 Azure Functions 응용 프로그램으로 이동합니다.

    Azure App Service를 사용하는 경우 설정 목록에서 **API 정의**를 선택합니다. 
    
    Azure Functions를 사용하는 경우 **함수 앱 설정**을 선택한 다음 **API 메타데이터 구성**을 선택합니다.

2. API 정의가 제공되는 경우 **PowerApps + Microsoft Flow로 내보내기** 단추가 보일 것입니다. 이 단추를 클릭하면 내보내기 프로세스가 시작됩니다.

3. **API 정의 다운로드** 또는 **링크 받기** 중 하나를 선택할 수 있습니다. 어떤 것을 선택하든 다음 섹션의 PowerApps에 결과가 제공됩니다. 다음 옵션 중 하나를 선택하고 지침을 따릅니다.
 
4. API 정의에 보안 정의가 포함된 경우 2단계에서 보안 정의가 호출됩니다. 가져오는 동안 PowerApps 및 Microsoft Flow가 보안 정의를 감지하고 보안 정보를 요청합니다. 사용자가 API에 액세스할 수 있도록 서비스에서는 이 정보를 사용하여 사용자를 로그인합니다. API에서 인증을 요구하는 경우 개방형 API 문서에서 _보안 정의_로 캡처되었는지 확인합니다.

    다음 섹션에서 사용할 각 정의와 관련된 자격 증명을 수집합니다. PowerApps에서 기본적으로 지원하고 각 자격 증명에서 요구하는 ID 공급자 목록은 [PowerApps에 사용자 지정 API 등록] 및 [Microsoft Flow에 사용자 지정 API 등록]을 참조하세요.
 
> [!NOTE]
> Azure Active Directory 인증을 사용하는 경우 API 및 회신 URL _https://msmanaged-na.consent.azure-apim.net/redirect_에 대한 액세스를 위임한 새 AAD 앱 등록이 필요합니다. Azure Resource Manager의 API를 교체하는 방법에 대한 자세한 내용은 [이 예제](
https://powerapps.microsoft.com/tutorials/customapi-azure-resource-manager-tutorial/)를 참조하세요.
>
> 다른 직원이 PowerApps로 API 정의를 가져오는 경우 API 정의 파일 외에도 **새 등록의** 클라이언트 ID 및 클라이언트 비밀과 API의 리소스 URL을 제공합니다. 이러한 비밀은 안전하게 관리해야 합니다. **API 보안 자격 증명 자체를 공유하지 마세요.**

<a name="import"></a>
## <a name="importing-the-api-definition-into-powerapps-and-microsoft-flow"></a>PowerApps 및 Microsoft Flow로 API 정의 가져오기

이 섹션에서는 앞에서 얻은 API 정의를 사용하여 PowerApps 및 Microsoft Flow에 사용자 지정 API를 만들 것입니다. 사용자 지정 API는 두 서비스 간에 공유되므로 정의를 한 번만 가져오면 됩니다. 사용자 지정 API에 대한 자세한 내용은 [PowerApps에 사용자 지정 API 등록] 및 [Microsoft Flow에 사용자 지정 API 등록]을 참조하세요.

**PowerApps로 가져오려면:**

1. [Powerapps 웹 포털](https://web.powerapps.com)을 열어서 로그인한 다음 **연결**을 선택합니다. **새 연결**을 클릭합니다.

2. **사용자 지정**을 선택한 다음 **새 사용자 지정 API**를 클릭합니다.

3. API 이름을 입력한 다음 Swagger 정의를 업로드하거나 메타데이터 URL에 붙여 넣습니다. **다음**을 클릭합니다.

4. 인증 세부 정보를 입력하라는 메시지가 나오면 이전 섹션에서 얻은 값을 입력합니다. 그렇지 않으면 다음 단계를 진행합니다.

5. **만들기**를 클릭합니다.

**Microsoft Flow로 가져오려면:**

1. [Microsoft Flow 웹 포털](https://flow.microsoft.com/)을 열고 로그인합니다. 

2. 페이지 오른쪽 위에서 기어 모양의 **설정** 단추를 클릭하고 **사용자 지정 API**를 선택합니다. **사용자 지정 API 만들기**를 클릭합니다.

3. Swagger 정의를 업로드하고 **계속**을 클릭합니다.

4. 인증 세부 정보를 입력하라는 메시지가 나오면 이전 섹션에서 얻은 값을 입력합니다. 그렇지 않으면 다음 단계를 진행합니다.

5. 화면 위쪽에 있는 확인란을 클릭합니다.



[PowerApps에 사용자 지정 API 등록]: https://powerapps.microsoft.com/tutorials/register-custom-api/
[Microsoft Flow에 사용자 지정 API 등록]: https://flow.microsoft.com/documentation/register-custom-api/



<!--HONumber=Feb17_HO2-->


