---
title: Azure Cache for Redis에 대한 ASP.NET 출력 캐시 공급자
description: Redis에 대한 Azure 캐시를 사용하여 ASP.NET 페이지 출력을 캐시하는 방법에 대해 알아봅니다. Redis 출력 캐시 공급자는 출력 캐시 데이터에 대한 out-of-process 스토리지 메커니즘입니다.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 04/22/2018
ms.openlocfilehash: f1d8189068278b46e3ec3ea66875d79bb91e5e16
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/10/2020
ms.locfileid: "81010208"
---
# <a name="aspnet-output-cache-provider-for-azure-cache-for-redis"></a>Azure Cache for Redis에 대한 ASP.NET 출력 캐시 공급자

Redis 출력 캐시 공급자는 출력 캐시 데이터에 대한 out-of-process 스토리지 메커니즘입니다. 이 데이터는 완전한 HTTP 응답(페이지 출력 캐싱)에 특별히 사용됩니다. 공급자가 ASP.NET 4에 도입된 새로운 출력 캐시 공급자 확장 포인트에 연결됩니다.

Redis 출력 캐시 공급자를 사용하려면 먼저 캐시를 구성한 다음, Redis 출력 개시 공급자 NuGet 패키지를 사용하여 사용자의 ASP.NET 애플리케이션을 구성합니다. 이 항목에서는 Redis 출력 캐시 공급자를 사용할 수 있도록 애플리케이션을 구성하는 지침을 제공합니다. Azure Cache for Redis 인스턴스를 만들고 구성하는 방법에 대한 자세한 내용은 [캐시 만들기](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache)를 참조하세요.

## <a name="store-aspnet-page-output-in-the-cache"></a>ASP.NET 페이지 출력을 캐시에 저장

Azure Cache for Redis 세션 상태 NuGet 패키지를 사용하여 Visual Studio에서 클라이언트 애플리케이션을 구성하려면 **도구** 메뉴에서 **NuGet 패키지 관리자**, **패키지 관리자 콘솔**을 차례로 클릭합니다.

`Package Manager Console` 창에서 다음 명령을 실행합니다.

```
Install-Package Microsoft.Web.RedisOutputCacheProvider
```

Reids Output 캐시 공급자 NuGet 패키지는 StackExchange.Redis.StrongName 패키지에 종속성을 갖습니다. StackExchange.Redis.StrongName 패키지가 프로젝트에 나타나지 않는 경우 설치됩니다. Redis 출력 캐시 공급자 NuGet 패키지에 대한 자세한 내용은 [RedisOutputCacheProvider](https://www.nuget.org/packages/Microsoft.Web.RedisOutputCacheProvider/) NuGet 패키지를 참조하세요.

>[!NOTE]
>강력한 이름의 StackExchange.Redis.StrongName 패키지 외에도 StackExchange.Redis 강력하지 않은 이름의 버전이 있습니다. 프로젝트에서 강력한 이름이 아닌 StackExchange.Redis 버전을 사용하는 경우 제거해야 합니다. 그렇지 않으면 프로젝트에서 이름 충돌이 발생합니다. 이 패키지에 대한 자세한 내용은 [.NET 캐시 클라이언트 구성](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients)을 참조하세요.

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

| attribute | 형식 | 기본값 | 설명 |
| --------- | ---- | ------- | ----------- |
| *호스트* | 문자열 | "현지호스트" | Redis 서버 IP 주소 또는 호스트 이름 |
| *포트* | 양의 정수 | 6379(TLS/SSL제외)<br/>6380(TLS/SSL) | 서버 포트 재배포 |
| *Accesskey* | 문자열 | "" | Redis 권한 부여가 활성화되면 서버 암호를 다시 사용합니다. 이 값은 기본적으로 빈 문자열이며, 이는 세션 상태 공급자가 Redis 서버에 연결할 때 암호를 사용하지 않는다는 것을 의미합니다. **Redis 서버가 Azure Redis Cache와 같이 공개적으로 액세스할 수 있는 네트워크에 있는 경우 Redis 권한 부여를 활성화하여 보안을 개선하고 보안 암호를 제공해야 합니다.** |
| *Ssl* | boolean | **false** | TLS를 통해 Redis 서버에 연결할지 여부입니다. Redis가 즉시 TLS를 지원하지 않기 때문에 이 값은 기본적으로 **false입니다.** **즉시 사용 중인 SSL을 지원하는 Azure Redis 캐시를 사용하는 경우 보안을 개선하기 위해 이 것을 true로 설정해야 합니다.**<br/><br/>새 캐시에 대해 TLS가 아닌 포트는 기본적으로 비활성화됩니다. TLS 포트를 사용 하려면이 설정에 대 한 **true를** 지정 합니다. 비 TLS 포트를 사용하도록 설정하는 것에 대한 자세한 내용은 [캐시 구성](cache-configure.md) 항목의 [액세스 포트](cache-configure.md#access-ports) 섹션을 참조하십시오. |
| *데이터베이스ID번호* | 양의 정수 | 0 | *이 특성은 web.config 또는 AppSettings를 통해서만 지정할 수 있습니다.*<br/><br/>사용할 Redis 데이터베이스를 지정합니다. |
| *연결타임아웃인밀리초* | 양의 정수 | 스택익스체인지에서 제공.재배포 | 스택 익스체인지.Redis.연결다중색기를 만들 때 *연결 시간 설정을* 설정하는 데 사용됩니다. |
| *작업타임아웃인밀리초* | 양의 정수 | 스택익스체인지에서 제공.재배포 | 스택 익스체인지.Redis.ConnectionMultiplexer를 만들 때 *동기화 시간 설정을* 설정하는 데 사용됩니다. |
| *연결 문자열(유효한* 스택익스체인지.Redis 연결 문자열) | 문자열 | *해당 /a* | AppSettings 또는 web.config에 대한 매개 변수 참조 또는 유효한 StackExchange.Redis 연결 문자열입니다. 이 특성은 *호스트,* *포트,* *accessKey,* *ssl*및 기타 StackExchange.Redis 특성에 대한 값을 제공할 수 있습니다. *연결문자열에*대한 자세한 내용은 [특성 노트의](#attribute-notes) [연결 문자열 설정](#setting-connectionstring) 섹션을 참조하십시오. |
| *설정클래스이름*<br/>*설정메소메네임* | 문자열<br/>문자열 | *해당 /a* | *이러한 특성은 web.config 또는 AppSettings를 통해서만 지정할 수 있습니다.*<br/><br/>이러한 특성을 사용하여 연결 문자열을 제공합니다. *settingsClassName* *설정MethodName에*의해 지정된 메서드를 포함 하는 어셈블리 정규화된 클래스 이름 이어야 합니다.<br/><br/>*settingsMethodName에* 의해 지정 된 메서드는 **문자열의**반환 형식과 함께 공개, 정적 및 void (매개 변수를 사용 하지 않음)여야 합니다. 이 메서드는 실제 연결 문자열을 반환합니다. |
| *로깅클래스이름*<br/>*로깅메네임* | 문자열<br/>문자열 | *해당 /a* | *이러한 특성은 web.config 또는 AppSettings를 통해서만 지정할 수 있습니다.*<br/><br/>이러한 특성을 사용하여 StackExchange.Redis의 로그와 함께 세션 상태/출력 캐시의 로그를 제공하여 응용 프로그램을 디버깅합니다. *loggingClassName* *로깅MethodName에*의해 지정된 메서드를 포함 하는 어셈블리 자격을 갖춘 클래스 이름 이어야 합니다.<br/><br/>*loggingMethodName에* 의해 지정된 메서드는 **System.IO.TextWriter의**반환 형식과 함께 공개, 정적 및 void(매개 변수를 사용하지 않음)여야 합니다. |
| *Applicationname* | 문자열 | 현재 프로세스의 모듈 이름 또는 "/" | *세션 상태 공급자 전용*<br/>*이 특성은 web.config 또는 AppSettings를 통해서만 지정할 수 있습니다.*<br/><br/>Redis 캐시에서 사용할 앱 이름 접두사입니다. 고객은 다른 목적으로 동일한 Redis 캐시를 사용할 수 있습니다. 세션 키가 충돌하지 않도록 하려면 응용 프로그램 이름으로 접두사에 붙일 수 있습니다. |
| *throwOnError* | boolean | true | *세션 상태 공급자 전용*<br/>*이 특성은 web.config 또는 AppSettings를 통해서만 지정할 수 있습니다.*<br/><br/>오류가 발생할 때 예외를 throw할지 여부입니다.<br/><br/>*throwOnError에*대한 자세한 내용은 [속성 노트](#attribute-notes) 섹션의 [ *throwOnError에* 대한 메모를](#notes-on-throwonerror) 참조하십시오. |>*마이크로소프트.Web.Redis.RedisSessionState.LastException*. |
| *다시 시도시간 아웃인밀리초* | 양의 정수 | 5,000 | *세션 상태 공급자 전용*<br/>*이 특성은 web.config 또는 AppSettings를 통해서만 지정할 수 있습니다.*<br/><br/>작업이 실패할 때 다시 시도하는 시간입니다. 이 값이 *작업보다 적으면TimeoutInMilliseconds*, 공급자는 다시 시도하지 않습니다.<br/><br/>재시도에 대한 자세한 내용은 [재시도에](#attribute-notes) [대한 참고 를 *참조하십시오TimeoutInMilliseconds* ](#notes-on-retrytimeoutinmilliseconds) 속성 노트 섹션에서 다시 시도시간 인밀리초. *retryTimeoutInMilliseconds* |
| *재배포 재위화자유형* | 문자열 | *해당 /a* | Microsoft.Web.Redis를 구현하는 클래스의 어셈블리 정규화된 형식 이름을 지정합니다. ISerializer 및 그 값을 직렬화 하 고 직렬화 하는 사용자 지정 논리를 포함 합니다. 자세한 내용은 [특성 노트의](#attribute-notes) [재배포 문자 *유형* 정보](#about-redisserializertype) 섹션을 참조하십시오. |

## <a name="attribute-notes"></a>특성 노트

### <a name="setting-connectionstring"></a>*연결 문자열* 설정

*연결String의* 값은 AppSettings에서 실제 연결 문자열을 가져오는 키로 사용 됩니다., 이러한 문자열AppSettings에 있는 경우. AppSettings 내에서 찾을 수 없는 *경우, 연결의 값String* web.config **ConnectionString** 섹션에서 실제 연결 문자열을 가져오는 키로 사용 됩니다., 해당 섹션이 있는 경우. 연결 문자열이 AppSettings 또는 web.config **연결 String** 섹션에 없는 경우, StackExchange.Redis.ConnectionMultiplexer를 만들 때 *연결* 문자열의 리터럴 값으로 사용 됩니다.

다음 예제에서는 *연결String이* 사용되는 방법을 보여 줍니다.

#### <a name="example-1"></a>예 1

```xml
<connectionStrings>
    <add name="MyRedisConnectionString" connectionString="mycache.redis.cache.windows.net:6380,password=actual access key,ssl=True,abortConnect=False" />
</connectionStrings>
```

에서 `web.config`위 키를 실제 값 대신 매개 변수 값으로 사용합니다.

```xml
<sessionState mode="Custom" customProvider="MySessionStateStore">
    <providers>
        <add type = "Microsoft.Web.Redis.RedisSessionStateProvide"
             name = "MySessionStateStore"
             connectionString = "MyRedisConnectionString"/>
    </providers>
</sessionState>
```

#### <a name="example-2"></a>예제 2

```xml
<appSettings>
    <add key="MyRedisConnectionString" value="mycache.redis.cache.windows.net:6380,password=actual access key,ssl=True,abortConnect=False" />
</appSettings>
```

에서 `web.config`위 키를 실제 값 대신 매개 변수 값으로 사용합니다.

```xml
<sessionState mode="Custom" customProvider="MySessionStateStore">
    <providers>
        <add type = "Microsoft.Web.Redis.RedisSessionStateProvide"
             name = "MySessionStateStore"
             connectionString = "MyRedisConnectionString"/>
    </providers>
</sessionState>
```

#### <a name="example-3"></a>예제 3

```xml
<sessionState mode="Custom" customProvider="MySessionStateStore">
    <providers>
        <add type = "Microsoft.Web.Redis.RedisSessionStateProvide"
             name = "MySessionStateStore"
             connectionString = "mycache.redis.cache.windows.net:6380,password=actual access key,ssl=True,abortConnect=False"/>
    </providers>
</sessionState>
```

### <a name="notes-on-throwonerror"></a>*throwOnError에* 대한 참고 사항

현재 세션 작업 중에 오류가 발생하면 세션 상태 공급자가 예외를 throw합니다. 그러면 응용 프로그램이 종료됩니다.

이 동작은 기존 ASP.NET 세션 상태 공급자 사용자의 기대를 지원하는 동시에 원하는 경우 예외에 대해 조치를 취하지 않는 방식으로 수정되었습니다. 기본 동작은 오류가 발생할 때 다른 ASP.NET 세션 상태 공급자와 일치하는 예외를 계속 throw합니다. 기존 코드는 이전과 동일하게 작동해야 합니다.

*throwOnError를* **false로**설정하면 오류가 발생할 때 예외를 throw하는 대신 자동으로 실패합니다. 오류가 있는지 확인하고 예외가 무엇인지 확인하려면 정적 속성 *Microsoft.Web.Redis.RedisSessionStateState.LastException*을 확인합니다.

### <a name="notes-on-retrytimeoutinmilliseconds"></a>재시도에 대한 참고 *사항타임아웃인밀리초*

이렇게 하면 네트워크 결함과 같은 이유로 일부 세션 작업이 실패시 다시 시도해야 하는 경우를 단순화하는 동시에 재시도 시간 시간을 제어하거나 다시 시도를 완전히 옵트아웃할 수 있습니다.

*retryTimeoutInMilliseconds를* 숫자(예: 2000)로 설정한 다음 세션 작업이 실패하면 오류로 취급하기 전에 2000밀리초 동안 다시 시도합니다. 따라서 세션 상태 공급자가 이 재시도 논리를 적용하도록 하려면 시간 시간을 구성하기만 하면 됩니다. 첫 번째 재시도는 20밀리초 후에 발생하며, 대부분의 경우 네트워크 결함이 발생할 때 충분합니다. 그 후, 그것은 시간 시간 때까지 매 초마다 다시 시도 합니다. 시간 시간이 지나면 한 번 더 다시 시도하여 시간 시간이 최대 1초로 차단되지 않도록 합니다.

다시 try가 필요하지 않다고 생각되는 경우(예: 응용 프로그램과 동일한 컴퓨터에서 Redis 서버를 실행하는 경우) 재시도 논리를 직접 처리하려면 *retryTimeOutInMilliseconds를* 0으로 설정합니다.

### <a name="about-redisserializertype"></a>*재배포 문자 입력 문자*

기본적으로 Redis의 값을 저장하는 직렬화는 **BinaryFormatter** 클래스에서 제공하는 바이너리 형식으로 수행됩니다. *redisSerializerType을* 사용하여 **Microsoft.Web.Redis.I Serializer를** 구현하고 값을 직렬화하고 역직해제하는 사용자 지정 논리가 있는 클래스의 어셈블리 정규화된 형식 이름을 지정합니다. 예를 들어 JSON.NET 사용하는 Json serializer 클래스는 다음과 같습니다.

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
            if (data == null)
            {
                return null;
            }
            return JsonConvert.DeserializeObject(Encoding.UTF8.GetString(data), _settings);
        }
    }
}
```

이 클래스가 **MyCompanyDll이라는**이름의 어셈블리에 정의되어 있다고 가정하면 매개 변수 *redisSerializerType을* 설정하여 사용할 수 있습니다.

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

이전 예제에서는 캐시된 페이지 데이터가 캐시에 60초 동안 머물게 되며, 각 매개 변수 조합에 따라 페이지의 다른 버전이 캐시됩니다. OutputCache 지시문에 대한 자세한 [@OutputCache](https://go.microsoft.com/fwlink/?linkid=320837)내용은 을 참조하십시오.

이 단계를 수행하면, 애플리케이션은 Redis 출력 캐시 공급자를 사용하도록 구성됩니다.

## <a name="third-party-output-cache-providers"></a>타사 출력 캐시 공급자

* [N캐시 (주)](https://www.alachisoft.com/blogs/how-to-use-a-distributed-cache-for-asp-net-output-cache/)
* [아파치 점화](https://apacheignite-net.readme.io/docs/aspnet-output-caching)


## <a name="next-steps"></a>다음 단계

[Azure Cache for Redis에 대한 ASP.NET 세션 상태 제공자](cache-aspnet-session-state-provider.md)를 살펴봅니다.
