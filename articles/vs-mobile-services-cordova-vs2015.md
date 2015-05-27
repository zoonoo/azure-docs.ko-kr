<properties 
	pageTitle="" 
	description="Cordova 프로젝트에서 모바일 서비스 사용" 
	services="mobile-services" 
	documentationCenter="" 
	authors="patshea123" 
	manager="douge" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="NA" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="05/06/2015" 
	ms.author="patshea123"/>

# Visual Studio 2015 Preview의 Cordova 프로젝트에서 모바일 서비스 사용

Visual Studio 2015 Preview의 Cordova 프로젝트에서 Azure 모바일 서비스를 사용하려면 다음과 같은 해결 방법을 적용해야 합니다.

1. Visual Studio 2015 Preview Cordova 프로젝트에서 Config.xml을 열고, **플러그 인** 탭 아래에서 **Azure 모바일 서비스** 플러그 인을 사용하도록 설정합니다.<br/>
![][1]

2. index.html에서는 **MobileServices.Web-1.2.2.min.js**를 참조하는 줄을 삭제합니다.<br/>

<PRE style="prettyprint">
&lt;!DOCTYPE html&gt;
&lt;html&gt;
&lt;head&gt;
    &lt;meta charset="utf-8" /&gt;
    &lt;title&gt;MyCordovaApp&gt;/title&gt;

    &lt;!--MyCordovaApp references --&gt;
    &lt;link href="css/index.css" rel="stylesheet" /&gt;
&lt;/head&gt;
&lt;body&gt;
    &lt;p&gt;Hello world!&lt/p&gt;

    &lt;!--Cordova reference, this is added to your app when it's build. --&gt;
    &lt;script src="cordova.js"&gt;&lt;/script&gt;
    &lt;script src="scripts/platformOverrides.js"&gt;&lt;/script&gt;

    &lt;script src="scripts/index.js"&gt;&lt;/script&gt;

    &lt;!-- yourservicename references --&gt;
    <span style="background-color:yellow">&lt;script src="//ajax.aspnetcdn.com/ajax/mobileservices/MobileServices.Web-1.2.2.min.js"&gt;&lt/script&gt;</span>
    &lt;script src="/services/mobileservices/settings/yourservicename.js"&gt;&lt;/script&gt;
&lt;/body&gt;
&lt;/html&gt;
</PRE>

3. services -> mobileServices -> settings 폴더 아래에서 {yourservicename}.js를 열고 기존 코드 조각을 다음과 같이 바꿉니다.

		// http://go.microsoft.com/fwlink/?LinkID=290993&clcid=0x409
		var {yourservicename}Client;
		document.addEventListener("deviceready", function () {    
            mhadetestserviceClient = new WindowsAzure.MobileServiceClient(
                    "https://{yourservicename}.azure-mobile.net/",
                    "<your application key>");
		});

[1]: ./media/vs-mobile-services-cordova-vs2015/AzureMobileServicesPlugIn.png
<!--HONumber=54-->