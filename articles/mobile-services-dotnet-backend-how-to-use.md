<properties urlDisplayName="Use the Mobile Services .NET Backend" pageTitle="모바일 서비스 .NET 백 엔드 사용 - Azure 모바일 서비스" metaKeywords="" description="Learn the details of the .NET Backend programming model for Azure Mobile Services, including how to work with table data, APIs, authentication, and scheduled jobs" metaCanonical="" services="" documentationCenter="Mobile" title="Use the Mobile Services .NET Backend" authors="mahender" solutions="" manager="dwrede" editor="mollybos" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="11/11/2014" ms.author="mahender" />
# 모바일 서비스 .NET 백 엔드 사용

<div class="dev-center-tutorial-subselector"><a href="/ko-kr/documentation/articles/mobile-services-dotnet-backend-how-to-use/" title=".NET backend" class="current">.NET 백 엔드</a> | <a href="/ko-kr/documentation/articles/mobile-services-how-to-use-server-scripts/"  title="JavaScript backend">JavaScript 백 엔드</a></div>

이 문서에서는 Azure 모바일 서비스에서 .NET 백 엔드를 사용하는 방법에 대한 자세한 정보와 예를 제공합니다. 이 토픽의 내용은 다음과 같습니다.

+ [소개](#intro)
+ [테이블 작업](#table-scripts)

##<a name="intro"></a>소개

모바일 서비스 .NET 백 엔드에서는 [ASP.NET Web API](http://www.asp.net/web-api) 및 원하는 .NET 언어를 사용해서 풍부한 백 엔드 비즈니스 논리를 작성할 수 있습니다. 모바일 서비스는 작은 프로그래밍 모델 영역을 Windows, Android, iOS 등에 대한 플랫폼 간 모바일 서비스 클라이언트 SDK의 서비스에 대한 효율적인 액세스를 지원하는 [NuGet 패키지](http://www.nuget.org/packages?q=%22mobile+services+.net+backend%22)로 제공합니다. 이러한 API는 또한 인증 추가 및 푸시 알림 지원이 가능한 한 간단하게 설정되도록 보장합니다. 하지만 대부분의 경우 프로그래밍 모델은 Web API를 기반으로 하며, 이에 익숙한 개발자는 Web API에 편안함을 느낄 것입니다. 

##<a name="table-scripts"></a>테이블 작업

모바일 서비스 .NET 백 엔드는 데이터베이스 저장소에 대한 CRUD 기반 HTTP API를 나타내는 범용 "테이블" 추상화를 제공합니다. 이러한 추상화는 데이터 저장소 문제를 비즈니스 논리로부터 분리하고 모바일 서비스가 플랫폼 간 모바일 서비스 클라이언트 SDK에서 즉시 이해할 수 있는 일관적인 JSON 기반 유선 형식으로 서로 다른 데이터 저장소를 제공할 수 있게 해줍니다. 

이 프로그래밍 모델의 근본에는 CRUD 데이터 액세스 패턴에 대해 사용자 지정된 정규 Web API [**ApiController**](http://msdn.microsoft.com/library/system.web.http.apicontroller.aspx)인 [**TableController<T>**](http://msdn.microsoft.com/library/dn643359.aspx) 클래스가 있습니다. **TableController**는 SQL([Entity Framework](http://msdn.microsoft.com/data/ef.aspx) 사용), [Azure 테이블 저장소](http://azure.microsoft.com/documentation/services/storage/), [MongoDB](http://www.mongodb.org) 또는 사용자 고유의 사용자 지정 저장소를 포함하여 다양한 데이터 저장소를 사용할 수 있습니다.
