---
title: V2를 v3 마이그레이션 설정으로 Media Services
description: 이 문서는 Azure Media Services v 2에서 v3로 마이그레이션하기 위한 환경을 설정 하는 데 도움이 됩니다.
services: media-services
author: IngridAtMicrosoft
manager: femila
editor: ''
tags: ''
keywords: azure media services, 마이그레이션, 스트림, 브로드캐스트, 라이브, SDK
ms.service: media-services
ms.devlang: multiple
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 1/14/2021
ms.author: inhenkel
ms.openlocfilehash: 1f3be34c6488b977a0b03237025ba0f0ab1af7ec
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102214459"
---
# <a name="step-3---set-up-to-migrate-to-the-v3-rest-api-or-client-sdk"></a>3 단계-V3 REST API 또는 클라이언트 SDK로 마이그레이션하도록 설정

![마이그레이션 가이드 로고](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![마이그레이션 2 단계](./media/migration-guide/steps-3.svg)

다음은 Media Services V3 API를 사용 하도록 환경을 설정 하기 위해 수행 해야 하는 단계를 설명 합니다.

## <a name="sdk-model"></a>SDK 모델

V2 API에는 두 가지 다른 클라이언트 Sdk가 있습니다. 하나는 관리 API에 대 한 것으로,이를 통해 프로그래밍 방식으로 계정을 만들고 리소스를 관리할 수 있습니다.

이전에는 개발자가 AMS 계정에 대 한 특정 V2 REST API 끝점과 함께 Azure 서비스 주체 클라이언트 ID 및 클라이언트 암호를 사용 하 여 작업 합니다.

V3 API는 ARM (Azure 리소스 관리)을 기반으로 합니다. Azure AD (Azure Active Directory) 서비스 사용자 Id와 키를 사용 하 여 API에 연결 합니다. 개발자는 API에 연결할 서비스 주체 또는 관리 되는 id를 만들어야 합니다. V3 API에서 API는 표준 ARM 끝점을 사용 하며, 다른 모든 Azure 서비스에 대해 비슷하거나 일관 된 모델을 사용 합니다.

이전에 2015-10-01 버전의 ARM management API를 사용 하 여 V2 계정을 관리 하는 고객은 V3 API 액세스에 대해 지원 되는 ARM 관리 API의 2020-05-01 버전을 사용 해야 합니다.

## <a name="create-a-new-media-services-account-for-testing"></a>테스트를 위한 새 media services 계정 만들기

Azure Portal 사용 하 여 [환경을 설정](how-to-set-azure-subscription.md?tabs=portal) 하는 빠른 시작 단계를 따르세요. API 액세스 및 서비스 주체 인증을 선택 하 여이 테스트 계정에 사용할 새 Azure AD 응용 프로그램 ID 및 암호를 생성 합니다.

[Media services 계정을 만듭니다](create-account-howto.md?tabs=portal).
[MEDIA SERVICES API에 액세스할 수 있는 자격 증명을 가져옵니다](access-api-howto.md?tabs=portal).

## <a name="download-client-sdk-of-your-choice-and-set-up-your-environment"></a>선택한 클라이언트 SDK를 다운로드 하 고 환경 설정

- Sdk는 [.net](/dotnet/api/overview/azure/mediaservices/management?preserve-view=true&view=azure-dotnet), .net Core, [Node.js](/javascript/api/overview/azure/mediaservices/management), [Python](/python/api/overview/azure/mediaservices/management?preserve-view=true&view=azure-python), [Java](/java/api/overview/azure/mediaservices/management), [Go](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/mediaservices/mgmt/2018-07-01/media)및 [Ruby](https://github.com/Azure/azure-sdk-for-ruby/blob/master/README.md)에 사용할 수 있습니다.
- [Azure CLI](/cli/azure/ams)   간단한 스크립팅 지원에 대 한 통합.

> [!NOTE]
> 더 이상 V3에서 커뮤니티 PHP SDK를 Azure Media Services 사용할 수 없습니다. V2에서 PHP를 사용 하는 경우 코드에서 직접 REST API로 마이그레이션해야 합니다.

## <a name="open-api-specifications"></a>Open API 사양

- V3은 Azure Resource Manager 기반으로 구축 된 관리 및 운영 기능을 모두 제공 하는 통합 API 화면을 기반으로 합니다. Azure Resource Manager 템플릿을 사용 하 여 변환, 스트리밍 끝점, 라이브 이벤트 등을 만들고 배포할 수 있습니다.

- [Openapi 사양](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2020-05-01) (이전의 Swagger) 문서에서는 모든 서비스 구성 요소에 대 한 스키마를 설명 합니다.

- 모든 클라이언트 Sdk는 GitHub에 게시 된 Open API 사양에서 파생 되 고 생성 됩니다. 이 문서를 게시할 당시에는 최신 개방형 API 사양이 GitHub에서 공개적으로 유지 관리 됩니다. 2020-05-01 버전은 [안정적인 최신](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2020-05-01)버전입니다.

## <a name="rest"></a>[REST (영문)](#tab/rest)

Media Services v3 REST API 호출에 대해 [Postman](./media-rest-apis-with-postman.md) 을 사용 합니다.
[REST API 참조 페이지](/rest/api/media/)를 읽습니다.

Postman collection에서 2020-05-01 버전 문자열을 사용 해야 합니다.

## <a name="net"></a>[.NET](#tab/net)

환경을 설정 하려면이 문서에서 [.net을 사용 하 여 Media Services V3 API에 연결](configure-connect-dotnet-howto.md) 문서를 참조 하세요.

PackageManager를 사용 하 여 최신 SDK를 설치 하려는 경우 다음 명령을 사용 합니다.

```Install-Package Microsoft.Azure.Management.Media```

또는 .NET CLI를 사용 하 여 최신 SDK를 설치 하려면 다음 명령을 사용 합니다.

```dotnet add package Microsoft.Azure.Management.Media```

또한 다양 한 시나리오에 대 한 전체 .NET 샘플을 [Azure-samples/media services-v3-dotnet](https://github.com/Azure-Samples/media-services-v3-dotnet) 에서 사용할 수 있습니다. 이 리포지토리의 프로젝트는 v3 버전을 사용 하 여 다양 한 Azure Media Services 시나리오를 구현 하는 방법을 보여 줍니다.

### <a name="get-started-adjusting-your-code"></a>코드 조정 시작

코드 베이스에서 사용 인스턴스를 검색 `CloudMediaContext` 하 여 V3 API로 업그레이드 프로세스를 시작 합니다.

다음 코드는 이전에 v2 .NET SDK를 사용 하 여 v2 API에 액세스 한 방법을 보여 줍니다. 개발자는를 만들고 `CloudMediaContext` 개체를 사용 하 여 인스턴스를 만드는 것부터 시작 `AzureAdTokenCredentials` 합니다.

```dotnet

class Program
    {
        // Read values from the App.config file.
        private static readonly string _AADTenantDomain =
            ConfigurationManager.AppSettings["AMSAADTenantDomain"];
        private static readonly string _RESTAPIEndpoint =
            ConfigurationManager.AppSettings["AMSRESTAPIEndpoint"];
        private static readonly string _AMSClientId =
            ConfigurationManager.AppSettings["AMSClientId"];
        private static readonly string _AMSClientSecret =
            ConfigurationManager.AppSettings["AMSClientSecret"];

        private static CloudMediaContext _context = null;

        static void Main(string[] args)
        {
        try
        {
            AzureAdTokenCredentials tokenCredentials = 
                new AzureAdTokenCredentials(_AADTenantDomain,
                    new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                    AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

```

## <a name="java"></a>[Java](#tab/java)

환경을 설정 하려면 문서에서 [Java를 사용 하 여 Media Services V3 API에 연결](configure-connect-java-howto.md) 문서를 참조 하세요.

## <a name="python"></a>[Python](#tab/python)

문서를 읽고 [Azure Media Services V3 API에 연결-Python](configure-connect-python-howto.md) 을 설정 하 여 환경을 설정 합니다.

## <a name="nodejs"></a>[Node.JS](#tab/nodejs)

[Azure Media Services V3 API에 연결](configure-connect-nodejs-howto.md) 문서를 읽고 환경을 설정 Node.js.

## <a name="ruby"></a>[Ruby](#tab/ruby)

- GitHub에서 [Ruby](https://github.com/Azure/azure-sdk-for-ruby/blob/master/README.md) SDK를 다운로드 합니다.

## <a name="go"></a>[Go](#tab/go)

[Go](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/mediaservices/mgmt/2018-07-01/media) SDK를 다운로드 합니다.

---

## <a name="next-steps"></a>다음 단계

[!INCLUDE [migration guide next steps](./includes/migration-guide-next-steps.md)]