---
title: Azure Cache for Redis에 대한 ASP.NET 출력 캐시 공급자
description: Azure Cache for Redis를 사용하여 ASP.NET 페이지 출력을 캐시하는 방법을 알아봅니다.
services: cache
documentationcenter: na
author: yegu-ms
manager: jhubbard
editor: tysonn
ms.assetid: 78469a66-0829-484f-8660-b2598ec60fbf
ms.service: cache
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: cache
ms.workload: tbd
ms.date: 04/22/2018
ms.author: yegu
ms.openlocfilehash: a93d21b07dc486f743694ee99f60018ed4ef517c
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64943866"
---
# <a name="aspnet-output-cache-provider-for-azure-cache-for-redis"></a>Azure Cache for Redis에 대한 ASP.NET 출력 캐시 공급자

Redis 출력 캐시 공급자는 출력 캐시 데이터에 대한 out-of-process 저장소 메커니즘입니다. 이 데이터는 완전한 HTTP 응답(페이지 출력 캐싱)에 특별히 사용됩니다. 공급자가 ASP.NET 4에 도입된 새로운 출력 캐시 공급자 확장 포인트에 연결됩니다.

Redis 출력 캐시 공급자를 사용하려면 먼저 캐시를 구성한 다음, Redis 출력 개시 공급자 NuGet 패키지를 사용하여 사용자의 ASP.NET 애플리케이션을 구성합니다. 이 항목에서는 Redis 출력 캐시 공급자를 사용할 수 있도록 애플리케이션을 구성하는 지침을 제공합니다. Azure Cache for Redis 인스턴스를 만들고 구성하는 방법에 대한 자세한 내용은 [캐시 만들기](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache)를 참조하세요.

## <a name="store-aspnet-page-output-in-the-cache"></a>ASP.NET 페이지 출력을 캐시에 저장

Azure Cache for Redis 세션 상태 NuGet 패키지를 사용하여 Visual Studio에서 클라이언트 애플리케이션을 구성하려면 **도구** 메뉴에서 **NuGet 패키지 관리자**, **패키지 관리자 콘솔**을 차례로 클릭합니다.

`Package Manager Console` 창에서 다음 명령을 실행합니다.

```
Install-Package Microsoft.Web.RedisOutputCacheProvider
```

Reids Output 캐시 공급자 NuGet 패키지는 StackExchange.Redis.StrongName 패키지에 종속성을 갖습니다. StackExchange.Redis.StrongName 패키지가 프로젝트에 나타나지 않는 경우 설치됩니다. Redis 출력 캐시 공급자 NuGet 패키지에 대한 자세한 내용은 [RedisOutputCacheProvider](https://www.nuget.org/packages/Microsoft.Web.RedisOutputCacheProvider/) NuGet 패키지를 참조하세요.

>[!NOTE]
>강력한 이름의 StackExchange.Redis.StrongName 패키지 외에도 StackExchange.Redis 강력하지 않은 이름의 버전이 있습니다. 프로젝트에는 비-강력한 이름의 StackExchange.Redis 버전을 사용 하는 경우 제거 해야 합니다. 그렇지 않으면 프로젝트에서 이름 충돌을 발생할 수 있습니다. 이 패키지에 대한 자세한 내용은 [.NET 캐시 클라이언트 구성](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients)을 참조하세요.

NuGet 패키지에서는 필수 어셈블리 참조를 다운로드하고 추가하며 web.config 파일에 다음 섹션을 추가합니다. 이 섹션에서는 Redis 출력 캐시 공급자를 사용하기 위해 ASP.NET 애플리케이션에 필수 구성을 포함합니다.

```xml
<caching>
  <outputCache defaultProvider="MyRedisOutputCache">
    <providers>
      <add name="MyRedisOutputCache" type="Microsoft.Web.Redis.RedisOutputCacheProvider"
           host=""
           accessKey=""
           ssl="true" />
    </providers>
  </outputCache>
</caching>
```

Microsoft Azure 포털의 캐시 블레이드에서 값으로 특성을 구성하고, 필요에 따라 다른 값을 구성합니다. 캐시 속성에 액세스하는 방법에 대한 지침은 [Azure Cache for Redis 설정 구성](cache-configure.md#configure-azure-cache-for-redis-settings)을 참조하세요.

| 특성 | Type | Default | 설명 |
| --------- | ---- | ------- | ----------- |
| *host* | 문자열 | "localhost" | Redis 서버 IP 주소 또는 호스트 이름 |
| *port* | 양의 정수 | 6379 (비-SSL)<br/>6380 SSL) | Redis 서버 포트 |
| *accessKey* | 문자열 | "" | 서버 암호 redis는 Redis 인증을 사용 하는 경우. 값은 Redis 서버에 연결할 때 세션 상태 제공자를 사용 하는 모든 암호를 사용 하지 않습니다는 기본적으로 빈 문자열입니다. **Redis 서버에 Azure Redis Cache 같은 공개적으로 액세스할 수 있는 네트워크에 보안을 개선 하기 위해 Redis 권한 부여를 사용 하도록 설정 해야 하 고 안전한 암호를 지정 합니다.** |
| *ssl* | 부울 | **false** | SSL 통해 Redis 서버에 연결할 것인지 지정 합니다. 이 값은 **false** 기본적으로 Redis는 기본적으로 SSL을 지원 하지 않기 때문입니다. **기본적으로 지 원하는 SSL를 true로 설정 해야 Azure Redis Cache를 사용 하는 경우 보안을 향상 합니다.**<br/><br/>비 SSL 포트는 기본적으로 새 캐시에 대해 사용하지 않도록 설정됩니다. 지정할 **true** SSL 포트를 사용 하도록이 설정에 대 한 합니다. 비-SSL 포트 사용 방법에 대한 자세한 내용은 [캐시 구성](cache-configure.md) 토픽의 [액세스 포트](cache-configure.md#access-ports) 섹션을 참조하세요. |
| *databaseIdNumber* | 양의 정수 | 0 | *Web.config 또는 AppSettings를 통해서만이 특성을 지정할 수 있습니다.*<br/><br/>사용할 Redis 데이터베이스를 지정 합니다. |
| *connectionTimeoutInMilliseconds* | 양의 정수 | StackExchange.Redis 제공한 | 설정 하는 데 *ConnectTimeout* StackExchange.Redis.ConnectionMultiplexer를 만들 때. |
| *operationTimeoutInMilliseconds* | 양의 정수 | StackExchange.Redis 제공한 | 설정 하는 데 *SyncTimeout* StackExchange.Redis.ConnectionMultiplexer를 만들 때. |
| *connectionString* (StackExchange.Redis 유효한 연결 문자열) | 문자열 | *n/a* | 두 매개 변수 참조 AppSettings web.config 또는 그렇지 않으면 유효한 StackExchange.Redis 연결 문자열입니다. 이 특성에 대 한 값을 제공할 수 있습니다 *호스트*를 *포트*를 *accessKey*를 *ssl*, 및 기타 StackExchange.Redis 특성입니다. 자세히 살펴보세요 *connectionString*를 참조 하세요 [connectionString 설정](#setting-connectionstring) 에 [정보 특성](#attribute-notes) 섹션. |
| *settingsClassName*<br/>*settingsMethodName* | 문자열<br/>문자열 | *n/a* | *Web.config 또는 AppSettings를 통해서만 이러한 특성을 지정할 수 있습니다.*<br/><br/>이러한 특성을 사용 하 여 연결 문자열을 제공 합니다. *settingsClassName* 지정 된 메서드를 포함 하는 어셈블리 정규화 된 클래스 이름 이어야 합니다 *settingsMethodName*합니다.<br/><br/>지정 된 메서드 *settingsMethodName* public, 정적 및 void 여야 합니다 (매개 변수가 없습니다.)의 반환 형식과 **문자열**합니다. 이 메서드는 실제 연결 문자열을 반환합니다. |
| *loggingClassName*<br/>*loggingMethodName* | 문자열<br/>문자열 | *n/a* | *Web.config 또는 AppSettings를 통해서만 이러한 특성을 지정할 수 있습니다.*<br/><br/>StackExchange.Redis에서 세션 상태/출력 캐시에서 로그와 함께 로그를 제공 하 여 응용 프로그램을 디버깅 하려면 이러한 특성을 사용 합니다. *loggingClassName* 지정 된 메서드를 포함 하는 어셈블리 정규화 된 클래스 이름 이어야 합니다 *loggingMethodName*합니다.<br/><br/>지정 된 메서드 *loggingMethodName* public, 정적 및 void 여야 합니다 (매개 변수가 없습니다.)의 반환 형식과 **System.IO.TextWriter**합니다. |
| *applicationName* | 문자열 | 현재 프로세스의 모듈 이름 또는 "/" | *SessionStateProvider만*<br/>*Web.config 또는 AppSettings를 통해서만이 특성을 지정할 수 있습니다.*<br/><br/>Redis cache에서 사용할 앱 이름 접두사입니다. 고객은 다양 한 용도로 동일한 Redis cache를 사용할 수 있습니다. 세션 키 충돌 하지 않는 복제본에 해당 응용 프로그램 이름을 접두사로 붙일 수 있습니다. |
| *throwOnError* | 부울 | true | *SessionStateProvider만*<br/>*Web.config 또는 AppSettings를 통해서만이 특성을 지정할 수 있습니다.*<br/><br/>오류가 발생 하면 예외가 발생 여부를 나타냅니다.<br/><br/>에 대 한 자세한 *throwOnError*를 참조 하세요 [정보 *throwOnError* ](#notes-on-throwonerror) 에 [정보 특성](#attribute-notes) 섹션. |>*Microsoft.Web.Redis.RedisSessionStateProvider.LastException*. |
| *retryTimeoutInMilliseconds* | 양의 정수 | 5,000 | *SessionStateProvider만*<br/>*Web.config 또는 AppSettings를 통해서만이 특성을 지정할 수 있습니다.*<br/><br/>작업이 실패 하면 다시 시도 하는 기간. 이 값이 보다 작거나 *operationTimeoutInMilliseconds*를 공급자에 다시 시도 하지 것입니다.<br/><br/>에 대 한 자세한 *retryTimeoutInMilliseconds*를 참조 하세요 [정보 *retryTimeoutInMilliseconds* ](#notes-on-retrytimeoutinmilliseconds) 에 [정보 특성](#attribute-notes) 섹션. |
| *redisSerializerType* | 문자열 | *n/a* | Microsoft.Web.Redis를 구현 하는 클래스의 어셈블리 정규화 된 형식 이름을 지정 합니다. ISerializer 올바르고 serialize 및 값을 deserialize 하는 사용자 지정 논리를 포함 합니다. 자세한 내용은 [에 대 한 *redisSerializerType* ](#about-redisserializertype) 에 [정보를 특성](#attribute-notes) 섹션. |
|

## <a name="attribute-notes"></a>특성 정보

### <a name="setting-connectionstring"></a>설정 *connectionString*

변수의 *connectionString* AppSettings에 해당 문자열이 있으면 AppSettings의 실제 연결 문자열을 가져올 키로 사용 됩니다. AppSettings 값 내에서 찾을 수 없으면 *connectionString* web.config에서 실제 연결 문자열을 키로 사용할 **ConnectionString** 섹션에 해당 섹션이 있는 경우. 연결 문자열, 없는 경우 web.config AppSettings에 존재 **ConnectionString** 섹션의 리터럴 값을 *connectionString* 만들 때 연결 문자열로 사용할 StackExchange.Redis.ConnectionMultiplexer 합니다.

다음 예제를 설명 하는 방법 *connectionString* 사용 됩니다.

#### <a name="example-1"></a>예 1

```xml
<connectionStrings>
    <add name="MyRedisConnectionString" connectionString="mycache.redis.cache.windows.net:6380,password=actual access key,ssl=True,abortConnect=False" />
</connectionStrings>
```

`web.config`, 실제 값 대신 매개 변수 값으로 위의 키를 사용 합니다.

```xml
<sessionState mode="Custom" customProvider="MySessionStateStore">
    <providers>
        <add type = "Microsoft.Web.Redis.RedisSessionStateProvide"
             name = "MySessionStateStore"
             connectionString = "MyRedisConnectionString"/>
    </providers>
</sessionState>
```

#### <a name="example-2"></a>예 2

```xml
<appSettings>
    <add key="MyRedisConnectionString" value="mycache.redis.cache.windows.net:6380,password=actual access key,ssl=True,abortConnect=False" />
</appSettings>
```

`web.config`, 실제 값 대신 매개 변수 값으로 위의 키를 사용 합니다.

```xml
<sessionState mode="Custom" customProvider="MySessionStateStore">
    <providers>
        <add type = "Microsoft.Web.Redis.RedisSessionStateProvide"
             name = "MySessionStateStore"
             connectionString = "MyRedisConnectionString"/>
    </providers>
</sessionState>
```

#### <a name="example-3"></a>예 3

```xml
<sessionState mode="Custom" customProvider="MySessionStateStore">
    <providers>
        <add type = "Microsoft.Web.Redis.RedisSessionStateProvide"
             name = "MySessionStateStore"
             connectionString = "mycache.redis.cache.windows.net:6380,password=actual access key,ssl=True,abortConnect=False"/>
    </providers>
</sessionState>
```

### <a name="notes-on-throwonerror"></a>정보 *throwOnError*

현재 세션 작업을 하는 동안 오류가 발생 하는 경우 세션 상태 제공자를 사용 하는 예외가 throw 됩니다. 이 응용 프로그램을 종료 합니다.

이 동작은 또한 원하는 경우 예외에 대해 작동 하는 기능을 제공 하는 동안 기존 ASP.NET 세션 상태 공급자 사용자의 기대치를 지 원하는 방식으로 수정 되었습니다. 오류가 발생 하는 다른 ASP.NET 세션 상태 제공자;와 일치 하는 경우 여전히 예외를 throw 기본 동작 기존 코드는 이전과 동일 하 게 작동 해야 합니다.

설정 하는 경우 *throwOnError* 하 **false**, 오류가 발생 하면 예외가 발생 하는 대신 실패 자동으로 합니다. 오류가 발생 했습니다를 경우 예외를 던 검색, 정적 속성을 확인 하십시오 *Microsoft.Web.Redis.RedisSessionStateProvider.LastException*합니다.

### <a name="notes-on-retrytimeoutinmilliseconds"></a>정보 *retryTimeoutInMilliseconds*

여기서 일부 세션 작업을 다시 시도해 야 실패 시 등 네트워크 결함으로 인해 하면서도 재시도 제한 시간을 제어 하거나 완전히 다시 시도 옵트아웃 하는 경우를 간소화 하는 몇 가지 다시 시도 논리가 제공 합니다.

설정 하는 경우 *retryTimeoutInMilliseconds* 숫자로 2000 예를 들어 다음 세션 작업이 실패 하면 다시 시도 2000 밀리초를 오류로 처리 하기 전에 합니다. 따라서이 재시도 논리를 적용 하려면 세션 상태 공급자가에 제한 시간을 구성입니다. 첫 번째 재시도 네트워크 결함이 발생할 때 충분 한 대부분의 경우에는 20 밀리초 후 발생 합니다. 그 후 시간이 초과 될 때까지 1 초 마다 다시 시도 합니다. 시간 초과 직후는 해당 되지 않습니다 잘릴 제한 시간 (최대) 1 초 있는지 한 번 더 다시 시도 합니다.

(예를 들어 경우 Redis 서버를 실행할 응용 프로그램과 동일한 컴퓨터에서)을 다시 시도 하거나 재시도 논리를 직접 처리 하려는 경우 설정 해야 있습니다 생각 하지 마세요 *retryTimeoutInMilliseconds* 0입니다.

### <a name="about-redisserializertype"></a>About *redisSerializerType*

기본적으로 제공한 이진 형식 값을 Redis에 저장 하는 serialization 이루어집니다 합니다 **BinaryFormatter** 클래스입니다. 사용 하 여 *redisSerializerType* 구현 하는 클래스의 어셈블리 정규화 된 형식 이름을 지정 하려면 **Microsoft.Web.Redis.ISerializer** 있고 사용자 지정 논리를 serialize 및 deserialize 값입니다. 예를 들어, 다음은 JSON.NET을 사용 하는 Json 직렬 변환기 클래스입니다.

```cs
namespace MyCompany.Redis
{
    public class JsonSerializer : ISerializer
    {
        private static JsonSerializerSettings _settings = new JsonSerializerSettings() { TypeNameHandling = TypeNameHandling.All };

        public byte[] Serialize(object data)
        {
            return Encoding.UTF8.GetBytes(JsonConvert.SerializeObject(data, _settings));
        }

        public object Deserialize(byte[] data)
        {
            if (data == null)6t6
            {
                return null;
            }
            return JsonConvert.DeserializeObject(Encoding.UTF8.GetString(data), _settings);
        }
    }
}
```

이름으로 어셈블리에 정의 된이 클래스를 가정 **MyCompanyDll**, 매개 변수를 설정할 수 있습니다 *redisSerializerType* 사용:

```xml
<sessionState mode="Custom" customProvider="MySessionStateStore">
    <providers>
        <add type = "Microsoft.Web.Redis.RedisSessionStateProvider"
             name = "MySessionStateStore"
             redisSerializerType = "MyCompany.Redis.JsonSerializer,MyCompanyDll"
             ... />
    </providers>
</sessionState>
```

## <a name="output-cache-directive"></a>출력 캐시 지시문

출력을 캐시하고자 하는 각 페이지에 OutputCache 지시문을 추가합니다.

```xml
<%@ OutputCache Duration="60" VaryByParam="*" %>
```

이전 예제에서는 캐시된 페이지 데이터가 캐시에 60초 동안 머물게 되며, 각 매개 변수 조합에 따라 페이지의 다른 버전이 캐시됩니다. 출력 캐시 지시문에 대한 자세한 내용은 [@OutputCache](https://go.microsoft.com/fwlink/?linkid=320837)을 참조하세요.

이 단계를 수행하면, 애플리케이션은 Redis 출력 캐시 공급자를 사용하도록 구성됩니다.

## <a name="next-steps"></a>다음 단계

[Azure Cache for Redis에 대한 ASP.NET 세션 상태 제공자](cache-aspnet-session-state-provider.md)를 살펴봅니다.
