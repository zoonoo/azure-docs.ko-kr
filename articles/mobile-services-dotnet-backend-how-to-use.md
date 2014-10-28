<properties linkid="mobile-services-dotnet-backend-how-to-use" urlDisplayName="Use the Mobile Services .NET Backend" pageTitle="Use the Mobile Services .NET Backend - Azure Mobile Services" metaKeywords="" description="Learn the details of the .NET Backend programming model for Azure Mobile Services, including how to work with table data, APIs, authentication, and scheduled jobs" metaCanonical="" services="" documentationCenter="Mobile" title="Use the Mobile Services .NET Backend" authors="yavorg" solutions="" manager="" editor="mollybos" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="yavorg"></tags>
# 모바일 서비스 .NET 백 엔드 사용

<div class="dev-center-tutorial-subselector"><a href="/ko--kr/documentation/articles/mobile-services-dotnet-backend-how-to-use/" title=".NET 백 엔드" class="current">.NET 백 엔드</a> | <a href="/ko--kr/documentation/articles/mobile-services-how-to-use-server-scripts/"  title="JavaScript 백 엔드">JavaScript 백 엔드</a></div>

이 문서에서는 Azure 모바일 서비스에서 .NET 백 엔드를 사용하는 방법에 대한 자세한 정보와 예를 제공합니다. 이 토픽의 내용은 다음과 같습니다.

+ [소개][소개]
+ [테이블 작업][테이블 작업]

## <a name="intro"></a> 소개

모바일 서비스 .NET 백 엔드에서는 [ASP.NET Web API][ASP.NET Web API] 및 원하는 .NET 언어를 사용해서 리치 백 엔드 비즈니스 논리를 작성할 수 있습니다. 모바일 서비스는 작은 프로그래밍 모델 영역을 Windows, Android, iOS 등에 대한 플랫폼 간 모바일 서비스 클라이언트 SDK의 서비스에 대한 효율적인 액세스를 지원하는 [NuGet 패키지][NuGet 패키지]로 제공합니다. 이러한 API는 또한 인증 추가 및 푸시 알림 지원이 가능한 한 간단하게 설정되도록 보장합니다. 하지만 대부분의 경우 프로그래밍 모델은 Web API를 기반으로 하며, 이에 익숙한 개발자는 Web API에 편안함을 느낄 것입니다.

## <a name="table-scripts"></a> 테이블 작업

모바일 서비스 .NET 백 엔드는 데이터베이스 저장소에 대한 CRUD 기반 HTTP API를 나타내는 범용 "테이블" 추상화를 제공합니다. 이러한 추상화는 데이터 저장소 문제를 비즈니스 논리로부터 분리하고 모바일 서비스가 플랫폼 간 모바일 서비스 클라이언트 SDK에서 즉시 이해할 수 있는 일관적인 JSON 기반 유선 형식으로 서로 다른 데이터 저장소를 제공할 수 있게 해줍니다.

이 프로그래밍 모델의 근본에는 CRUD 데이터 액세스 패턴에 대해 사용자 지정된 정규 Web API [**ApiController**][**ApiController**]인 [**TableController<t>**][**TableController<t>**] 클래스가 있습니다. **TableController**는 SQL([Entity Framework][Entity Framework] 사용), [Azure 테이블 저장소][Azure 테이블 저장소], [MongoDB][MongoDB] 또는 사용자 고유의 사용자 지정 저장소를 포함하여 다양한 데이터 저장소를 사용할 수 있습니다.


[.NET 백 엔드]: /ko--kr/documentation/articles/mobile-services-dotnet-backend-how-to-use/ ".NET 백 엔드"
[JavaScript 백 엔드]: /ko--kr/documentation/articles/mobile-services-how-to-use-server-scripts/ "JavaScript 백 엔드"
[소개]: #intro
[테이블 작업]: #table-scripts
[ASP.NET Web API]: http://www.asp.net/web-api
[NuGet 패키지]: http://www.nuget.org/packages?q=%22mobile+services+.net+backend%22
[**ApiController**]: http://msdn.microsoft.com/library/system.web.http.apicontroller.aspx
[**TableController<t>**]: http://msdn.microsoft.com/library/dn643359.aspx
[Entity Framework]: http://msdn.microsoft.com/data/ef.aspx
[Azure 테이블 저장소]: http://azure.microsoft.com/documentation/services/storage/
[MongoDB]: http://www.mongodb.org
