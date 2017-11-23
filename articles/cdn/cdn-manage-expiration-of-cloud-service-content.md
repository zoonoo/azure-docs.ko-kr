---
title: "Azure CDN(Content Delivery Network)에서 웹 콘텐츠 만료 관리 | Microsoft Docs"
description: "Azure CDN에서 Azure Web Apps/Cloud Services, ASP.NET 또는 IIS 콘텐츠의 만료를 관리하는 방법을 알아봅니다."
services: cdn
documentationcenter: .NET
author: zhangmanling
manager: erikre
editor: 
ms.assetid: bef53fcc-bb13-4002-9324-9edee9da8288
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 11/10/2017
ms.author: mazha
ms.openlocfilehash: d58a245923242b3963b188ca869e8290d999c0a2
ms.sourcegitcommit: 659cc0ace5d3b996e7e8608cfa4991dcac3ea129
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/13/2017
---
# <a name="manage-expiration-of-web-content-in-azure-content-delivery-network"></a>Azure CDN(Content Delivery Network)에서 웹 콘텐츠 만료 관리
 Azure CDN
> [!div class="op_single_selector"]
> * [Azure Web Apps/Cloud Services, ASP.NET 또는 IIS](cdn-manage-expiration-of-cloud-service-content.md)
> * [Azure Blob 저장소](cdn-manage-expiration-of-blob-content.md)
> 

TTL(time-to-live)이 경과할 때까지 원본 웹 서버에서 공개적으로 액세스할 수 있는 파일은 Azure CDN(Content Delivery Network)에 캐시될 수 있습니다. TTL은 원본 서버의 HTTP 응답에 있는 [`Cache-Control` 헤더](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9)에 기반하여 결정됩니다. 이 문서에서는 Microsoft Azure App Service, Azure Cloud Services, ASP.NET 응용 프로그램 및 IIS(Internet Information Services) 사이트에 대한 웹앱 기능의 `Cache-Control` 헤더를 설정하는 방법을 설명하며, 이 헤더들은 모두 비슷하게 구성됩니다.

> [!TIP]
> 파일에 TTL을 설정하지 않을 수도 있습니다. 이 경우에 Azure CDN은 기본 TTL인 7일을 자동으로 적용합니다.
> 
> 파일과 다른 리소스에 대한 액세스 속도를 높이기 위해 Azure CDN이 작동하는 방법에 대한 자세한 내용은 [Azure CDN(Content Delivery Network) 개요](cdn-overview.md)를 참조하세요.
> 

## <a name="setting-cache-control-headers-in-configuration-files"></a>구성 파일에 Cache-Control 헤더 설정
이미지, 스타일 시트 등 정적 콘텐츠에 대해서는 웹 응용 프로그램의 **applicationHost.config** 또는 **web.config** 파일을 수정함으로써 업데이트 빈도를 제어할 수 있습니다. 구성 파일의 **system.webServer\staticContent\clientCache** 요소에는 콘텐츠에 대한 `Cache-Control` 헤더가 설정됩니다. **web.config**의 구성 설정은 하위 폴더 수준에서 재정의된 경우를 제외하고는 폴더 및 그 하위 폴더의 모든 항목에 영향을 줍니다. 예를 들어, 루트 폴더의 기본 TTL 설정을 3일 동안 모든 정적 콘텐츠를 캐시하고, 다른 콘텐츠가 있는 하위 폴더는 6시간 동안만 콘텐츠를 캐시하도록 설정할 수 있습니다. **applicationHost.config** 파일 설정이 사이트의 모든 응용 프로그램에 영향을 미치지만 응용 프로그램의 기존 **web.config** 파일의 설정에 의해 재정의됩니다.

다음 XML은 최대 기간을 3일로 지정하는 **clientCache** 설정 방법을 보여 줍니다.  

```xml
<configuration>
    <system.webServer>
        <staticContent>
            <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="3.00:00:00" />
        </staticContent>
    </system.webServer>
</configuration>
```

**UseMaxAge**를 지정하면 **CacheControlMaxAge** 특성에 지정된 값에 따라 `Cache-Control: max-age=<nnn>` 헤더를 응답에 추가합니다. **cacheControlMaxAge** 특성에 대한 시간 범위의 형식은 `<days>.<hours>:<min>:<sec>`입니다. **clientCache** 노드에 대한 자세한 내용은 [클라이언트 캐시<clientCache>](http://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache)를 참조하세요.  

## <a name="setting-cache-control-headers-in-code"></a>코드에서 Cache-Control 헤더 설정
ASP.NET 응용 프로그램의 경우 **HttpResponse.Cache** 속성을 설정하면 CDN 캐싱 동작을 프로그래밍 방식으로 제어할 수 있습니다. **HttpResponse.Cache** 속성에 대한 자세한 내용은 [HttpResponse.Cache 속성](http://msdn.microsoft.com/library/system.web.httpresponse.cache.aspx) 및 [HttpCachePolicy 클래스](http://msdn.microsoft.com/library/system.web.httpcachepolicy.aspx)를 참조하세요.  

ASP.NET에서 프로그래밍 방식으로 콘텐츠를 캐시하려면 다음 단계를 따릅니다.
   1. `HttpCacheability`를 *Public*으로 설정하여 콘텐츠를 캐시 가능하게 표시합니다. 
   2. 다음 메서드 중 하나를 호출하여 캐시 유효성 검사기를 설정합니다.
      - `SetLastModified`를 호출하여 LastModified 타임스탬프를 설정합니다.
      - `SetETag`를 호출하여 `ETag` 값을 설정합니다.
   3. 또는 `SetExpires`를 호출하여 캐시 만료 시간을 지정합니다. 그렇지 않은 경우 이 문서의 앞 부분에서 설명한 기본 캐시 추론이 적용됩니다.

예를 들어 1시간 동안의 콘텐츠를 캐시하려면 다음 C# 코드를 추가합니다.  

```csharp
// Set the caching parameters.
Response.Cache.SetExpires(DateTime.Now.AddHours(1));
Response.Cache.SetCacheability(HttpCacheability.Public);
Response.Cache.SetLastModified(DateTime.Now);
```

## <a name="next-steps"></a>다음 단계
* [**clientCache** 요소에 대한 자세한 내용](http://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache)
* [**HttpResponse.Cache** 속성에 대한 자세한 내용](http://msdn.microsoft.com/library/system.web.httpresponse.cache.aspx) 
* [**HttpCachePolicy 클래스**에 대한 자세한 내용](http://msdn.microsoft.com/library/system.web.httpcachepolicy.aspx)  

