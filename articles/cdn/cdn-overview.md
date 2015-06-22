<properties 
	pageTitle="Azure CDN 개요" 
	description="Azure CDN(콘텐츠 배달 네트워크) 정의와 Blob 및 정적 콘텐츠를 캐시하여 고대역폭 콘텐츠를 배달하는 데 사용하는 방법을 알아봅니다." 
	services="cdn" 
	documentationCenter=".NET" 
	authors="akucer" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="cdn" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/06/2015" 
	ms.author="akucer"/>

#Azure CDN(콘텐츠 배달 네트워크) 개요

Azure CDN(콘텐츠 배달 네트워크)은 클라우드 서비스에서 사용하는 Azure Blob 및 정적 콘텐츠를 전략적으로 배치된 위치에 캐시하여 사용자에게 콘텐츠를 배달하는 데 최대 대역폭을 제공합니다. 

기존 CDN 고객인 경우 이제 [Microsoft Azure 관리 포털](https://manage.windowsazure.com)을 통해 CDN 끝점을 관리할 수 있습니다. 


>[AZURE.NOTE] Azure CDN은 Azure 저장소 또는 Azure 클라우드 서비스와 별도의 [요금제](http://www.microsoft.com/windowsazure/pricing/)를 사용합니다.
 

CDN은 전 세계 물리적 노드에 콘텐츠를 캐시하여 고대역폭 콘텐츠를 배달하기 위한 글로벌 솔루션을 개발자에게 제공합니다. CDN 노드 위치의 현재 목록은 [Azure CDN(콘텐츠 배달 네트워크) POP 위치](http://msdn.microsoft.com/library/azure/gg680302.aspx)를 참조하세요.

CDN을 사용하여 Azure 데이터를 캐시할 경우의 이점은 다음과 같습니다.

- 콘텐츠 원본에서 멀리 떨어져 있으며 콘텐츠를 로드하기 위해 많은 '인터넷 이동'이 필요한 응용 프로그램을 사용 중인 최종 사용자의 성능 및 사용자 환경 향상
- 제품 런칭 이벤트 시작 시와 같이 순간적인 높은 부하를 더 효율적으로 처리하기 위한 대규모 분산 


>[AZURE.IMPORTANT] CDN 끝점을 만들거나 사용하도록 설정할 때 전 세계에 전파하려면 최대 60분이 걸릴 수 있습니다.
 
CDN에 개체 요청을 처음 수행하는 경우 Blob 서비스나 클라우드 서비스에서 직접 개체가 검색됩니다. CDN 구문을 사용하여 요청을 수행하는 경우 개체에 대한 액세스를 제공하기 위해 요청이 수행된 위치에 가장 가까운 CDN 끝점으로 요청이 리디렉션됩니다. 해당 끝점에 개체가 없을 경우 서비스에서 검색되어 끝점에 캐시되며, 해당 끝점에서 캐시된 개체에 대한 TTL(Time-To-Live) 설정이 유지 관리됩니다.
 
##Azure 저장소의 콘텐츠 캐시

Azure 저장소 계정에서 CDN을 사용하도록 설정하면 공용 컨테이너에 있고 익명 액세스에 사용할 수 있는 모든 Blob이 CDN을 통해 캐시됩니다. 공개적으로 사용할 수 있는 Blob만 Azure CDN을 사용하여 캐시할 수 있습니다. Blob을 익명 액세스에 공개적으로 사용할 수 있게 하려면 해당 컨테이너를 공용으로 표시해야 합니다. 이렇게 하면 해당 컨테이너 내의 모든 Blob을 익명 읽기 권한으로 사용할 수 있습니다. 컨테이너 데이터도 공용으로 만들거나 액세스 권한을 컨테이너 내의 Blob으로만 제한할 수 있습니다. 컨테이너 및 Blob에 대한 액세스 제어 관리와 관련된 자세한 내용은 [컨테이너 및 Blob에 대한 액세스 제한](http://msdn.microsoft.com/library/azure/dd179354.aspx)을 참조하세요.

최상의 성능을 얻으려면 크기가 10GB 미만인 Blob을 배달할 때는 CDN 에지 캐싱을 사용하세요.

저장소 계정에 대해 CDN 액세스를 사용하도록 설정하면 관리 포털에서 CDN 도메인 이름을 다음 형식으로 제공합니다.: http://<identifier>.vo.msecnd.net/. 이 도메인 이름을 사용하여 공용 컨테이너의 Blob에 액세스할 수 있습니다. 예를 들어 myaccount라는 이름의 저장소 계정에 music이라는 이름의 공용 컨테이너가 있을 경우 사용자는 다음의 두 개의 URL 중 하나를 사용하여 그 컨테이너의 Blob에 액세스할 수 있습니다:

- **Azure Blob 서비스 URL**: `http://myAccount.blob.core.windows.net/music/` 
- **Azure CDN URL**: `http://<identifier>.vo.msecnd.net/music/` 

##Azure 웹 사이트의 콘텐츠 캐시

웹 사이트의 CDN이 이미지, 스크립트 및 스타일시트와 같은 웹 콘텐츠를 캐시하도록 할 수 있습니다. [Azure CDN과 Azure 웹 사이트 통합](../cdn-websites-with-cdn.md)을 참조하세요.

웹 사이트에 대해 CDN 액세스를 사용하도록 설정하면 관리 포털에서 CDN 도메인 이름을 http://<identifier>.vo.msecnd.net/ 형식으로 제공합니다. 이 도메인 이름을 사용하여 웹 사이트에서 개체를 검색할 수 있습니다. 예를 들어 cdn이라는 공용 컨테이너와 music.png라는 이미지 파일이 있을 경우 사용자는 다음 두 개의 URL 중 하나를 사용하여 개체에 액세스할 수 있습니다.

- **Azure 웹 사이트 URL**: `http://mySiteName.azurewebsites.net/cdn/music.png` 
- **Azure CDN URL**: `http://<identifier>.vo.msecnd.net/cdn/music.png` 
##Azure 클라우드 서비스의 콘텐츠 캐시

Azure 클라우드 서비스에서 제공하는 개체를 CDN에 캐시할 수 있습니다. 

클라우드 서비스에 대한 캐싱에는 다음과 같은 제약 조건이 있습니다. 


- CDN을 사용하여 정적 콘텐츠만 캐시해야 합니다.

	>[AZURE.WARNING] volatile 콘텐츠나 동적 콘텐츠를 캐시하면 성능이 저하되거나 콘텐츠 문제가 발생할 수 있으며 모두 비용 증가를 초래합니다.
- 프로덕션 배포에 클라우드 서비스를 배포해야 합니다.
- 클라우드 서비스는 HTTP를 사용하여 포트 80에서 개체를 제공해야 합니다.
- 클라우드 서비스는 캐시하거나 배달할 콘텐츠를 클라우드 서비스의 /cdn 폴더에 배치해야 합니다.

클라우드 서비스에 대해 CDN 액세스를 사용하도록 설정하면 관리 포털에서 CDN 도메인 이름을 http://<identifier>.vo.msecnd.net/ 형식으로 제공합니다. 이 도메인 이름을 사용하여 클라우드 서비스에서 개체를 검색할 수 있습니다. 예를 들어 myHostedService라는 클라우드 서비스와 콘텐츠를 배달하는 music.aspx라는 ASP.NET 웹 페이지가 있을 경우 사용자는 다음 두 개의 URL 중 하나를 사용하여 개체에 액세스할 수 있습니다.


- **Azure 클라우드 서비스 URL**: `http://myHostedService.cloudapp.net/cdn/music.aspx` 
- **Azure CDN URL**: `http://<identifier>.vo.msecnd.net/music.aspx` 


###쿼리 문자열을 사용하여 특정 콘텐츠 캐시

쿼리 문자열을 사용하여 클라우드 서비스에서 검색된 개체를 구별할 수 있습니다. 예를 들어 클라우드 서비스에서 달라질 수 있는 차트를 표시하는 경우 쿼리 문자열을 전달하여 필요한 특정 차트를 검색할 수 있습니다. 예를 들면 다음과 같습니다. 

`http://<identifier>.vo.msecnd.net/chart.aspx?item=1`

쿼리 문자열은 문자열 리터럴로 전달됩니다. `?area=2&item=1`과 같은 두 매개 변수를 사용하는 서비스가 있고 이후에 `?item=1&area=2`를 사용하여 서비스를 호출하는 경우 동일한 개체의 두 복사본이 캐시됩니다.
 

##HTTPS를 통해 캐시된 콘텐츠 액세스


Azure에서는 HTTPS 호출을 사용하여 CDN에서 콘텐츠를 검색할 수 있습니다. 이렇게 하면 혼합된 보안 콘텐츠 형식에 대한 경고를 받지 않고 CDN에 캐시된 콘텐츠를 보안 웹 페이지에 통합할 수 있습니다.

HTTPS를 사용하여 CDN 콘텐츠에 액세스하는 경우 다음과 같은 제약 조건이 있습니다.


- CDN에서 제공된 인증서를 사용해야 합니다. 타사 인증서는 지원되지 않습니다.
- CDN 도메인을 사용하여 콘텐츠에 액세스해야 합니다. 현재 CDN이 사용자 지정 인증서를 지원하지 않으므로 CNAME(사용자 지정 도메인 이름)에는 HTTPS 지원을 사용할 수 없습니다.



HTTPS를 사용하도록 설정한 경우에도 HTTP 및 HTTPS 둘 다를 사용하여 CDN에서 콘텐츠를 검색할 수 있습니다.

CDN 콘텐츠에 대해 HTTPS를 사용하도록 설정하는 방법에 대한 자세한 내용은 [Azure에 대해 CDN(콘텐츠 배달 네트워크)을 사용하도록 설정하는 방법](http://msdn.microsoft.com/library/azure/gg680301.aspx)을 참조하세요.


##사용자 지정 도메인을 사용하여 캐시된 콘텐츠 액세스

CDN HTTP 끝점을 사용자 지정 도메인 이름에 매핑하고 해당 이름을 사용하여 CDN에서 개체를 요청할 수 있습니다.

사용자 지정 도메인 매핑에 대한 자세한 내용은 [CDN(콘텐츠 배달 네트워크) 콘텐츠를 사용자 지정 도메인에 매핑하는 방법](http://msdn.microsoft.com/library/azure/gg680307.aspx)을 참조하세요.


<!--HONumber=49--> 