---
title: v3 API로 개발
titleSuffix: Azure Media Services
description: Media Services v3로 개발할 때 엔터티 및 API에 적용되는 규칙에 대해 알아봅니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 10/21/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: eacdfe8211c97e75b6609f5e11b681f84ae55846
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79472087"
---
# <a name="develop-with-media-services-v3-apis"></a>미디어 서비스 v3 API로 개발

개발자인 경우 Media Services [REST API](https://docs.microsoft.com/rest/api/media/) 또는 REST API와 상호 작용할 수 있도록 하는 클라이언트 라이브러리를 사용하여 사용자 지정 미디어 워크플로를 손쉽게 만들고, 관리하고 유지할 수 있습니다. [미디어 서비스 v3](https://aka.ms/ams-v3-rest-sdk) API는 OpenAPI 사양(이전의 스웨거)을 기반으로 합니다.

이 문서에서는 Media Services v3로 개발할 때 엔터티 및 API에 적용되는 규칙에 대해 설명합니다.

## <a name="accessing-the-azure-media-services-api"></a>Azure 미디어 서비스 API 액세스

Media Services 리소스 및 Media Services API에 액세스할 수 있는 권한을 부여하려면 먼저 인증을 거쳐야 합니다. 미디어 서비스는 [Azure Active Directory(Azure AD) 기반](../../active-directory/fundamentals/active-directory-whatis.md) 인증을 지원합니다. 두 가지 일반적인 인증 옵션은 다음과 같습니다.
 
* **서비스 주체 인증**: 서비스를 인증하는 데 사용됩니다(예: 웹 앱, 함수 앱, 논리 앱, API 및 마이크로 서비스). 이 인증 방법을 일반적으로 사용하는 애플리케이션은 디먼 서비스, 중간 계층 서비스 또는 예약된 작업을 실행하는 앱입니다. 예를 들어 웹 앱의 경우 항상 서비스 주체와 함께 미디어 서비스에 연결하는 중간 계층이 있어야 합니다.
* **사용자 인증**: 앱을 사용하여 미디어 서비스 리소스와 상호 작용하는 사용자를 인증하는 데 사용됩니다. 대화형 앱은 먼저 사용자에게 사용자의 자격 증명을 묻는 메시지를 표시해야 합니다. 예제는 권한 있는 사용자가 인코딩 작업 또는 라이브 스트리밍을 모니터링하기 위해 사용한 관리 콘솔 앱입니다.

미디어 서비스 API는 REST API 요청을 하는 사용자 또는 앱이 미디어 서비스 계정 리소스에 액세스하고 **기여자** 또는 **소유자** 역할을 사용하도록 요구합니다. **API는 Reader** 역할로 액세스할 수 있지만 **받기** 또는 **목록** 작업만 사용할 수 있습니다.자세한 내용은 [미디어 서비스 계정에 대한 역할 기반 액세스 제어를](rbac-overview.md)참조하십시오.

서비스 주체를 만드는 대신 Azure 리소스에 대해 관리되는 ID를 사용하여 Azure 리소스 관리자를 통해 미디어 서비스 API에 액세스하는 것이 좋습니다. Azure 리소스에 대한 관리ID에 대해 자세히 알아보려면 [Azure 리소스에 대한 관리되는 ID를](../../active-directory/managed-identities-azure-resources/overview.md)참조하십시오.

### <a name="azure-ad-service-principal"></a>Azure AD 서비스 주체

Azure AD 앱 및 서비스 주체를 만드는 경우 앱은 자체 테넌트에 있어야 합니다. 앱을 만든 후 앱 **기여자** 또는 **소유자** 역할 액세스 권한을 미디어 서비스 계정에 제공합니다.

Azure AD 앱을 만들 수 있는 권한이 있는지 확실하지 않은 경우 [필수 사용 권한](../../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)을 참조하십시오.

다음 그림에서 숫자는 요청의 흐름을 시간 순으로 나타냅니다.

![웹 API에서 AAD를 이어 중간 계층 앱 인증](./media/use-aad-auth-to-access-ams-api/media-services-principal-service-aad-app1.png)

1. 중간 계층 앱은 다음 매개 변수가 있는 Azure AD 액세스 토큰을 요청합니다.  

   * Azure AD 테넌트 엔드포인트.
   * Media Services 리소스 URI.
   * REST Media Services의 리소스 URI.
   * Azure AD 앱 값: 클라이언트 ID 및 클라이언트 보안입니다.

   필요한 모든 값을 얻으려면 [Azure CLI를 사용하여 Azure 미디어 서비스 API 액세스를](access-api-cli-how-to.md)참조하십시오.

2. Azure AD 액세스 토큰이 중간 계층으로 전송됩니다.
4. 중간 계층은 Azure AD 토큰과 함께 Azure Media REST API로 요청을 보냅니다.
5. 중간 계층은 Media Services에서 데이터를 다시 가져옵니다.

### <a name="samples"></a>샘플

Azure AD 서비스 주체와 연결하는 방법을 보여 주는 다음 샘플을 참조하십시오.

* [REST와 연결](media-rest-apis-with-postman.md)  
* [Java로 연결](configure-connect-java-howto.md)
* [.NET으로 연결](configure-connect-dotnet-howto.md)
* [Node.js로 연결](configure-connect-nodejs-howto.md)
* [Python으로 연결](configure-connect-python-howto.md)

## <a name="naming-conventions"></a>명명 규칙

Azure Media Services v3 리소스 이름(예: 자산, 작업, 변환)은 Azure Resource Manager 명명 제약 조건에 따라 달라집니다. Azure Resource Manager에 따라 리소스 이름은 항상 고유합니다. 따라서 리소스 이름에 대해 고유 식별자 문자열(예: GUID)을 사용할 수 있습니다.

미디어 서비스 리소스 이름에는 '<', '>', '%', '&', ':','&#92;', '?', '/',',',',',',',',',',',', 단일 견적 문자 또는 모든 제어 문자를 포함할 수 없습니다. 다른 문자를 모두 허용합니다. 리소스 이름의 최대 길이는 260자입니다.

Azure 리소스 관리자 이름에 대한 자세한 내용은 [명명 요구 사항](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/resource-api-reference.md#arguments-for-crud-on-resource) 및 명명 [규칙을](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging)참조하십시오.

### <a name="names-of-filesblobs-within-an-asset"></a>자산 내의 파일/Blob 이름

자산 내의 파일/Blob 이름은 [Blob 이름 요구 사항과](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata) [NTFS 이름 요구 사항을](https://docs.microsoft.com/windows/win32/fileio/naming-a-file)모두 따라야 합니다. 이러한 요구 사항에 대 한 이유는 파일을 처리할 수 있는 로컬 NTFS 디스크에 Blob 저장소에서 복사할 수 있습니다.

## <a name="long-running-operations"></a>장기 실행 작업

Azure Media `x-ms-long-running-operation` 서비스 [스웨거 파일에](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/streamingservice.json) 표시된 작업은 장기 실행 작업입니다. 

비동기 Azure 작업을 추적하는 방법에 대한 자세한 내용은 [비동기 작업을](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations#monitor-status-of-operation)참조하십시오.

미디어 서비스에는 다음과 같은 장기 실행 작업이 있습니다.

* [라이브 이벤트 만들기](https://docs.microsoft.com/rest/api/media/liveevents/create)
* [라이브 이벤트 업데이트](https://docs.microsoft.com/rest/api/media/liveevents/update)
* [라이브 이벤트 삭제](https://docs.microsoft.com/rest/api/media/liveevents/delete)
* [라이브 이벤트 시작](https://docs.microsoft.com/rest/api/media/liveevents/start)
* [라이브 이벤트 중지](https://docs.microsoft.com/rest/api/media/liveevents/stop)

  매개 `removeOutputsOnStop` 변수를 사용하여 이벤트를 중지할 때 연결된 모든 라이브 출력을 삭제합니다.  
* [라이브 이벤트 재설정](https://docs.microsoft.com/rest/api/media/liveevents/reset)
* [라이브 출력 만들기](https://docs.microsoft.com/rest/api/media/liveevents/create)
* [라이브출력 삭제](https://docs.microsoft.com/rest/api/media/liveevents/delete)
* [StreamingEndpoint 만들기](https://docs.microsoft.com/rest/api/media/streamingendpoints/create)
* [스트리밍 엔드포인트 업데이트](https://docs.microsoft.com/rest/api/media/streamingendpoints/update)
* [스트리밍 엔드포인트 삭제](https://docs.microsoft.com/rest/api/media/streamingendpoints/delete)
* [StreamingEndpoint 시작](https://docs.microsoft.com/rest/api/media/streamingendpoints/start)
* [스트리밍 중지엔드포인트](https://docs.microsoft.com/rest/api/media/streamingendpoints/stop)
* [스케일 스트리밍엔드포인트](https://docs.microsoft.com/rest/api/media/streamingendpoints/scale)

긴 작업을 성공적으로 제출하면 '202 수락됨'이 수신되며 반환된 작업 ID를 사용하여 작업 완료를 폴링해야 합니다.

[비동기 Azure 작업 추적](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations) 문서에서는 응답에서 반환된 값을 통해 비동기 Azure 작업의 상태를 추적하는 방법에 대해 자세히 설명합니다.

지정된 라이브 이벤트 또는 관련 라이브 출력에 대해 하나의 장기 실행 작업만 지원됩니다. 시작되면 동일한 LiveEvent 또는 연결된 라이브 출력에서 후속 장기 실행 작업을 시작하기 전에 장기 실행 작업을 완료해야 합니다. 여러 라이브 출력이 있는 라이브 이벤트의 경우 다른 라이브 출력에서 장기 실행 작업을 트리거하기 전에 한 라이브 출력에서 장기 실행 작업이 완료될 때까지 기다려야 합니다. 

## <a name="sdks"></a>SDK

> [!NOTE]
> Azure 미디어 서비스 v3 SDK는 스레드 안전성으로 보장되지 않습니다. 다중 스레드 앱을 개발할 때는 클라이언트를 보호하거나 스레드당 새 AzureMediaServicesClient 개체를 사용하기 위해 고유한 스레드 동기화 논리를 추가해야 합니다. 코드에서 클라이언트에 제공하는 선택적 개체(예: .NET의 HttpClient 인스턴스) 때문에 다중 스레드 문제가 도입될 수 있다는 점에도 주의해야 합니다.

|SDK)|참고|
|---|---|
|[.NET SDK](https://aka.ms/ams-v3-dotnet-sdk)|[.NET 참조](https://aka.ms/ams-v3-dotnet-ref)|
|[Java SDK](https://aka.ms/ams-v3-java-sdk)|[Java 참조](https://aka.ms/ams-v3-java-ref)|
|[Python SDK](https://aka.ms/ams-v3-python-sdk)|[Python 예제](https://aka.ms/ams-v3-python-ref)|
|[Node.js SDK](https://aka.ms/ams-v3-nodejs-sdk) |[Node.js 예제](/javascript/api/overview/azure/mediaservices/management)| 
|[Go SDK](https://aka.ms/ams-v3-go-sdk) |[참조로 이동](https://aka.ms/ams-v3-go-ref)|
|[Ruby SDK](https://aka.ms/ams-v3-ruby-sdk)||

### <a name="see-also"></a>참조

- [미디어 서비스 이벤트를 포함하는 EventGrid .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.EventGrid/)
- [Media Services 이벤트의 정의](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/eventgrid/data-plane/Microsoft.Media/stable/2018-01-01/MediaServices.json)

## <a name="azure-media-services-explorer"></a>Azure Media Services 탐색기

[Azure Media Services 탐색기](https://github.com/Azure/Azure-Media-Services-Explorer)(AMSE)는 Media Services에 대해 알아보고자 하는 Windows 고객이 사용할 수 있는 도구입니다. AMSE는 Media Services에서 VOD 및 실시간 콘텐츠의 업로드, 다운로드, 인코딩, 스트리밍을 수행하는 Winforms/C# 애플리케이션입니다. AMSE 도구는 코드를 작성하지 않고 Media Services를 테스트하려는 고객을 위한 도구입니다. AMSE 코드는 Media Services를 사용하여 개발하려는 고객을 위한 리소스로 제공됩니다.

AMSE는 오픈 소스 프로젝트이며, 커뮤니티에서 지원을 제공합니다(문제는 https://github.com/Azure/Azure-Media-Services-Explorer/issues)에 보고할 수 있음). 이 프로젝트는 [Microsoft 오픈 소스 준수 사항](https://opensource.microsoft.com/codeofconduct/)을 채택했습니다. 자세한 내용은 행동 [강령 FAQ를](https://opensource.microsoft.com/codeofconduct/faq/) opencode@microsoft.com 참조하거나 다른 질문이나 의견에 문의하십시오.

## <a name="filtering-ordering-paging-of-media-services-entities"></a>Media Services 엔터티 필터링, 순서 지정, 페이징

[Azure 미디어 서비스 엔터티의 필터링, 순서 정렬, 페이징을](entities-overview.md)참조하십시오.

## <a name="ask-questions-give-feedback-get-updates"></a>질문, 피드백 제공, 업데이트 받기

[Azure Media Services 커뮤니티](media-services-community.md) 문서를 체크 아웃하여 다양한 방법으로 질문을 하고, 피드백을 제공하고, Media Services에 대한 업데이트를 가져올 수 있습니다.

## <a name="see-also"></a>참조

[Azure CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)

## <a name="next-steps"></a>다음 단계

* [Java를 사용하여 미디어 서비스에 연결](configure-connect-java-howto.md)
* [.NET을 사용하여 미디어 서비스에 연결](configure-connect-dotnet-howto.md)
* [Node.js로 미디어 서비스에 연결](configure-connect-nodejs-howto.md)
* [파이썬으로 미디어 서비스에 연결](configure-connect-python-howto.md)
