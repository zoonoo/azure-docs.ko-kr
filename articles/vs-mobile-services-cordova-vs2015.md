<properties title="Using Mobile Services with Cordova Projects" pageTitle="" metaKeywords="Azure, Cordova, Mobile Services" description="" services="mobile-services" documentationCenter="" authors="ghogen" />

<tags ms.service="mobile-services" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/11/2014" ms.author="ghogen" />

### Visual Studio 2015 Preview의 Cordova 프로젝트에서 모바일 서비스 사용

Visual Studio 2015 Preview의 Cordova 프로젝트에서 Azure 모바일 서비스를 사용하려면 다음과 같은 해결 방법을 적용해야 합니다.

1. Visual Studio 2015 Preview Cordova 프로젝트에서 Config.xml을 열고, **플러그 인** 탭 아래에서 **Windows Azure 모바일 서비스** 플러그 인을 사용하도록 설정합니다.<br/>
![][1]

2. index.html에서는 **MobileServices.Web-1.2.2.min.js**를 참조하는 줄을 삭제합니다.<br/>

<PRE style="prettyprint">
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8" />
    <title>MyCordovaApp>/title>

    <!--MyCordovaApp references -->
    <link href="css/index.css" rel="stylesheet" />
</head>
<body>
    <p>Hello world!&lt/p>

    <!--Cordova reference, this is added to your app when it's build. -->
    <script src="cordova.js"></script>
    <script src="scripts/platformOverrides.js"></script>

    <script src="scripts/index.js"></script>

    <!-- yourservicename references -->
    <span style="background-color:yellow"><script src="//ajax.aspnetcdn.com/ajax/mobileservices/MobileServices.Web-1.2.2.min.js">&lt/script></span>
    <script src="/services/mobileservices/settings/yourservicename.js"></script>
</body>
</html>
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
