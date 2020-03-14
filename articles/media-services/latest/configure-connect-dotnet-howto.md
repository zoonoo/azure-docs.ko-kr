---
title: Azure Media Services v3 API-.NET에 연결
description: 이 문서에서는 .NET을 사용 하 여 Media Services v3 API에 연결 하는 방법을 보여 줍니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/18/2019
ms.author: juliako
ms.openlocfilehash: b8f4de1a5b9d8216ae2442631f5f9135c3c72d0b
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79269810"
---
# <a name="connect-to-media-services-v3-api---net"></a>Media Services v3 API-.NET에 연결

이 문서에서는 서비스 사용자 로그인 메서드를 사용 하 여 Azure Media Services v3 .NET SDK에 연결 하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>사전 요구 사항

- [Media Services 계정 만들기](create-account-cli-how-to.md) 리소스 그룹 이름 및 Media Services 계정 이름을 명심 해야 합니다.
- .NET 개발에 사용할 도구를 설치 합니다. 이 문서의 단계에서는 [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/)을 사용 하는 방법을 보여 줍니다. Visual Studio Code를 사용할 수 있습니다. [작업 C# ](https://code.visualstudio.com/docs/languages/csharp)을 참조 하세요. 또는 다른 코드 편집기를 사용할 수 있습니다.

> [!IMPORTANT]
> [명명 규칙](media-services-apis-overview.md#naming-conventions)을 검토 합니다.

## <a name="create-a-console-application"></a>콘솔 애플리케이션 만들기

1. Visual Studio를 시작합니다. 
1. **파일** 메뉴에서 **새로 만들기** > **프로젝트**를 클릭 합니다. 
1. **.Net Core** 콘솔 응용 프로그램을 만듭니다.

이 항목의 샘플 앱은 `netcoreapp2.0`를 대상으로 합니다. 이 코드에서는 C# 7.1부터 사용할 수 있는 ' async main '을 사용 합니다. 자세한 내용은이 [블로그](https://blogs.msdn.microsoft.com/benwilli/2017/12/08/async-main-is-available-but-hidden/) 를 참조 하세요.

## <a name="add-required-nuget-packages"></a>필요한 NuGet 패키지 추가

1. Visual Studio에서 **도구** > **nuget 패키지 관리자** > **nuget 관리자 콘솔**을 선택 합니다.
2. **패키지 관리자 콘솔** 창에서 `Install-Package` 명령을 사용 하 여 다음 NuGet 패키지를 추가 합니다. `Install-Package Microsoft.Azure.Management.Media`)을 입력합니다.

|패키지|Description|
|---|---|
|`Microsoft.Azure.Management.Media`|Azure Media Services SDK. <br/>최신 Azure Media Services 패키지를 사용 하 고 있는지 확인 하려면 [Microsoft.](https://www.nuget.org/packages/Microsoft.Azure.Management.Media)|
|`Microsoft.Rest.ClientRuntime.Azure.Authentication`|NET 용 Azure SDK 용 ADAL 인증 라이브러리|
|`Microsoft.Extensions.Configuration.EnvironmentVariables`|환경 변수 및 로컬 JSON 파일에서 구성 값을 읽습니다.|
|`Microsoft.Extensions.Configuration.Json`|환경 변수 및 로컬 JSON 파일에서 구성 값을 읽습니다.
|`WindowsAzure.Storage`|저장소 SDK|

## <a name="create-and-configure-the-app-settings-file"></a>앱 설정 파일 만들기 및 구성

### <a name="create-appsettingsjson"></a>Appsettings를 만듭니다.

1. **일반** > **텍스트 파일로**이동 합니다.
1. 이름을 "appsettings. json"으로 합니다.
1. Json 파일의 "출력 디렉터리로 복사" 속성을 "최신 버전으로 복사"로 설정 하 여 응용 프로그램이 게시 될 때 액세스할 수 있도록 합니다.

### <a name="set-values-in-appsettingsjson"></a>Appsettings에서 값을 설정 합니다.

[액세스 api](access-api-cli-how-to.md)에 설명 된 대로 `az ams account sp create` 명령을 실행 합니다. 명령은 "appsettings"에 복사 해야 하는 json을 반환 합니다.
 
## <a name="add-configuration-file"></a>구성 파일 추가

편의상 "appsettings"에서 값을 읽는 구성 파일을 추가 합니다.

1. 프로젝트에 새 .cs 클래스를 추가 합니다. 이름을 `ConfigWrapper`로 지정합니다. 
1. 이 파일에 다음 코드를 붙여 넣습니다 (이 예제에서는 네임 스페이스가 `ConsoleApp1`된 것으로 가정).

```csharp
using System;

using Microsoft.Extensions.Configuration;

namespace ConsoleApp1
{
    public class ConfigWrapper
    {
        private readonly IConfiguration _config;

        public ConfigWrapper(IConfiguration config)
        {
            _config = config;
        }

        public string SubscriptionId
        {
            get { return _config["SubscriptionId"]; }
        }

        public string ResourceGroup
        {
            get { return _config["ResourceGroup"]; }
        }

        public string AccountName
        {
            get { return _config["AccountName"]; }
        }

        public string AadTenantId
        {
            get { return _config["AadTenantId"]; }
        }

        public string AadClientId
        {
            get { return _config["AadClientId"]; }
        }

        public string AadSecret
        {
            get { return _config["AadSecret"]; }
        }

        public Uri ArmAadAudience
        {
            get { return new Uri(_config["ArmAadAudience"]); }
        }

        public Uri AadEndpoint
        {
            get { return new Uri(_config["AadEndpoint"]); }
        }

        public Uri ArmEndpoint
        {
            get { return new Uri(_config["ArmEndpoint"]); }
        }

        public string Region
        {
            get { return _config["Region"]; }
        }
    }
}
```

## <a name="connect-to-the-net-client"></a>.NET 클라이언트에 연결

.NET으로 Media Services API를 사용하려면 **AzureMediaServicesClient** 개체를 만들어야 합니다. 개체를 만들려면 Azure AD를 사용하여 클라이언트가 Azure에 연결하는 데 필요한 자격 증명을 제공해야 합니다. 아래 코드에서 GetCredentialsAsync 함수는 로컬 구성 파일에 제공 된 자격 증명을 기반으로 ServiceClientCredentials 개체를 만듭니다.

1. `Program.cs`를 엽니다.
1. 다음 코드를 붙여 넣습니다.

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Threading.Tasks;

using Microsoft.Azure.Management.Media;
using Microsoft.Azure.Management.Media.Models;
using Microsoft.Extensions.Configuration;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using Microsoft.Rest;
using Microsoft.Rest.Azure.Authentication;

namespace ConsoleApp1
{
    class Program
    {
        public static async Task Main(string[] args)
        {
            
            ConfigWrapper config = new ConfigWrapper(new ConfigurationBuilder()
                .SetBasePath(Directory.GetCurrentDirectory())
                .AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
                .AddEnvironmentVariables()
                .Build());

            try
            {
                IAzureMediaServicesClient client = await CreateMediaServicesClientAsync(config);
                Console.WriteLine("connected");
            }
            catch (Exception exception)
            {
                if (exception.Source.Contains("ActiveDirectory"))
                {
                    Console.Error.WriteLine("TIP: Make sure that you have filled out the appsettings.json file before running this sample.");
                }

                Console.Error.WriteLine($"{exception.Message}");

                ApiErrorException apiException = exception.GetBaseException() as ApiErrorException;
                if (apiException != null)
                {
                    Console.Error.WriteLine(
                        $"ERROR: API call failed with error code '{apiException.Body.Error.Code}' and message '{apiException.Body.Error.Message}'.");
                }
            }

            Console.WriteLine("Press Enter to continue.");
            Console.ReadLine();
        }
 
        private static async Task<ServiceClientCredentials> GetCredentialsAsync(ConfigWrapper config)
        {
            // Use ApplicationTokenProvider.LoginSilentWithCertificateAsync or UserTokenProvider.LoginSilentAsync to get a token using service principal with certificate
            //// ClientAssertionCertificate
            //// ApplicationTokenProvider.LoginSilentWithCertificateAsync

            // Use ApplicationTokenProvider.LoginSilentAsync to get a token using a service principal with symmetric key
            ClientCredential clientCredential = new ClientCredential(config.AadClientId, config.AadSecret);
            return await ApplicationTokenProvider.LoginSilentAsync(config.AadTenantId, clientCredential, ActiveDirectoryServiceSettings.Azure);
        }

        private static async Task<IAzureMediaServicesClient> CreateMediaServicesClientAsync(ConfigWrapper config)
        {
            var credentials = await GetCredentialsAsync(config);

            return new AzureMediaServicesClient(config.ArmEndpoint, credentials)
            {
                SubscriptionId = config.SubscriptionId,
            };
        }

    }
}
```

## <a name="next-steps"></a>다음 단계

- [자습서: 비디오 업로드, 인코딩 및 스트리밍-.NET](stream-files-tutorial-with-api.md) 
- [자습서: Media Services v3로 라이브 스트리밍-.NET](stream-live-tutorial-with-api.md)
- [자습서: Media Services v3-.NET을 사용 하 여 비디오 분석](analyze-videos-tutorial-with-api.md)
- [로컬 파일에서 작업 입력 만들기 - .NET](job-input-from-local-file-how-to.md)
- [HTTPS URL에서 작업 입력 만들기 - .NET](job-input-from-http-how-to.md)
- [사용자 지정 변환으로 인코딩 - .NET](customize-encoder-presets-how-to.md)
- [AES-128 동적 암호화 및 키 전달 서비스 사용 - .NET](protect-with-aes128.md)
- [DRM 동적 암호화 및 라이선스 배달 서비스 사용 - .NET](protect-with-drm.md)
- [기존 정책에서 서명 키 가져오기 - .NET](get-content-key-policy-dotnet-howto.md)
- [Media Services로 필터 만들기 - .NET](filters-dynamic-manifest-dotnet-howto.md)
- [Media Services v3 및 Azure Functions v2의 고급 비디오 주문형 예제](https://aka.ms/ams3functions)

## <a name="see-also"></a>참고 항목

* [.NET 참조](https://docs.microsoft.com/dotnet/api/overview/azure/mediaservices/management?view=azure-dotnet)
* 더 많은 코드 예제는 [.NET SDK 샘플](https://github.com/Azure-Samples/media-services-v3-dotnet) 리포지토리를 참조 하세요.
