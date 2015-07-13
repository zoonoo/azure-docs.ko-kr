<properties 
	pageTitle="웹 응용 프로그램에서 Azure CDN의 콘텐츠 제공" 
	description="CDN에서 콘텐츠를 사용하여 웹 응용 프로그램의 성능을 향상시키는 방법에 대해 설명하는 자습서입니다." 
	services="cdn" 
	documentationCenter=".net" 
	authors="cephalin" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="cdn" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="05/27/2015" 
	ms.author="cephalin"/>

# 웹 응용 프로그램에서 Azure CDN의 콘텐츠 제공 #

이 자습서에서는 Azure CDN을 활용하여 웹 응용 프로그램의 도달 범위 및 성능을 개선하는 방법을 보여 줍니다. Azure CDN은 다음과 같은 경우에 웹 응용 프로그램의 성능을 개선하는 데 도움을 줄 수 있습니다.

- 페이지에 정적 콘텐츠 또는 정적인 요소가 일부 포함된 콘텐츠에 대한 여러 링크가 있는 경우
- 전 세계의 클라이언트에서 응용 프로그램에 액세스하는 경우
- 웹 서버에서 트래픽을 오프로드하려는 경우
- 웹 서버에 대한 클라이언트의 동시 연결 수를 줄이려는 경우([묶음 및 축소](http://www.asp.net/mvc/tutorials/mvc-4/bundling-and-minification)[영문]에서 이 내용을 다룸) 
- 페이지의 인지된 로드/새로 고침 시간을 증가시키려는 경우

## 알아볼 내용 ##

이 자습서에서는 다음을 수행하는 방법을 알아봅니다.

-	[Azure CDN 끝점에서 정적 콘텐츠 제공](#deploy)
-	[ASP.NET 응용 프로그램에서 CDN 끝점으로 콘텐츠 업로드 자동화](#upload)
-	[원하는 콘텐츠 업데이트를 반영하도록 CDN 캐시 구성](#update)
-	[쿼리 문자열을 사용하여 최신 콘텐츠 즉시 제공](#query)

## 필요한 사항 ##

이 자습서를 사용하려면 다음 필수 조건이 필요합니다.

-	활성 [Microsoft Azure 계정](/account/). 무료 평가판 계정을 등록할 수 있습니다.
-	Blob 관리 GUI용 [Azure SDK](http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409)를 설치한 Visual Studio 2013
-	[Azure PowerShell](http://go.microsoft.com/?linkid=9811175&clcid=0x409)([ASP.NET 응용 프로그램에서 CDN 끝점으로 콘텐츠 업로드 자동화](#upload)에서 사용)

> [AZURE.NOTE]이 자습서를 완료하려면 Azure 계정이 있어야 합니다. + [Azure 계정을 무료로 개설](/pricing/free-trial/?WT.mc_id=A261C142F)할 수 있음 - 유료 Azure 서비스를 사용해볼 수 있는 크레딧을 받게 되며 크레딧을 모두 사용한 후에도 계정을 유지하고 무료 Azure 서비스(예: 웹 사이트)를 사용할 수 있습니다. + [MSDN 구독자 혜택을 활성화](/pricing/member-offers/msdn-benefits-details/)할 수 있음 - MSDN 구독은 유료 Azure 서비스에 사용할 수 있는 크레딧을 매달 제공합니다.

<a name="static"></a>
## Azure CDN 끝점에서 정적 콘텐츠 제공 ##

이 자습서 섹션에서는 CDN을 만드는 방법 및 CDN을 사용하여 정적 콘텐츠를 제공하는 방법을 알아봅니다. 관련된 주요 단계는 다음과 같습니다.

1. 저장소 계정 만들기
2. 저장소 계정과 연결된 CDN 만들기
3. 저장소 계정에 Blob 컨테이너 만들기
4. Blob 컨테이너에 콘텐츠 업로드
5. CDN URL을 사용하여 업로드한 콘텐츠에 연결

그럼 시작하겠습니다. 아래 단계에 따라 Azure CDN을 사용하세요.

1. CDN 끝점을 만들려면 [Azure 관리 포털](http://manage.windowsazure.com/)에 로그인합니다. 
1. **새로 만들기 > 데이터 서비스 > 저장소 > 빠른 생성**을 클릭하여 저장소 계정을 만듭니다. URL 및 위치를 지정하고 **저장소 계정 만들기**를 클릭합니다. 

	![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-1.PNG)

	>[AZURE.NOTE]여기서는 지역으로 동아시아를 사용합니다. 나중에 CDN을 테스트할 북아메리카로부터 충분히 멀리 떨어져 있기 때문입니다.

2. 새 저장소 계정의 상태가 **온라인**이 되면 생성한 저장소 계정과 연결된 새 CDN 끝점을 만듭니다. **새로 만들기 > 앱 서비스 > CDN > 빠른 생성**을 클릭합니다. 생성한 저장소 계정을 선택하고 **만들기**를 클릭합니다.

	![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-2.PNG)

	>[AZURE.NOTE]CDN이 생성되면 Azure 포털에 해당 URL 및 연결된 원본 도메인이 표시됩니다. 그러나 CDN 끝점의 구성이 모든 노드 위치에 완전히 전파되는 데는 시간이 조금 걸릴 수 있습니다.

3. CDN 끝점에 ping을 실행하여 온라인 상태인지 확인하기 위해 CDN 끝점을 테스트합니다. CDN 끝점이 모든 노드에 전파되지 않은 경우 아래와 유사한 메시지가 표시됩니다.

	![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-3-fail.PNG)

	한 시간 정도 기다린 후 다시 테스트합니다. CDN 끝점의 노드에 대한 전파가 완료되면 끝점에서 ping에 응답해야 합니다.

	![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-3-succeed.PNG)

4. 이때 CDN 끝점이 결정한 가장 가까운 CDN 노드가 어디에 있는지 확인할 수 있습니다. 이 자습서의 경우 데스크톱 컴퓨터에서 응답하는 IP 주소는 **93.184.215.201**입니다. [www.ip-address.org](http://www.ip-address.org)(영문)와 같은 사이트에 연결하면 서버가 워싱턴 D.C.에 있다는 것을 알 수 있습니다.

	![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-4.PNG)

	모든 CDN 노드 위치 목록은 [Azure CDN(콘텐츠 배달 네트워크) 노드 위치](http://msdn.microsoft.com/library/azure/gg680302.aspx)를 참조하세요.

3. Azure 포털로 돌아가 **CDN** 탭에서 방금 만든 CDN 끝점의 이름을 클릭합니다.

	![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-2-enablequerya.PNG)

3. **쿼리 문자열 사용**을 클릭하여 Azure CDN 캐시에서 쿼리 문자열을 사용하도록 설정합니다. 쿼리 문자열을 사용하도록 설정하면 다른 쿼리 문자열로 액세스하는 동일 링크가 별도의 항목으로 캐시됩니다.

	![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-2-enablequeryb.PNG)

	>[AZURE.NOTE]이 자습서에서는 쿼리 문자열 사용이 필요하지 않지만, 여기서 변경한 사항이 나머지 노드에 전파되는 데 시간이 걸리기 때문에 그리고 쿼리 문자열을 사용하지 않는 콘텐츠가 CDN 캐시를 막는 것을 방지하기 위해 편의상 가능한 한 빨리 쿼리 문자열을 사용하도록 설정할 수 있습니다(CDN 콘텐츠 업데이트에 대해서는 뒷부분에서 설명). 쿼리 문자열을 활용하는 방법은 [쿼리 문자열을 통해 최신 콘텐츠 즉시 제공](#query)에서 찾아볼 수 있습니다.

6. Visual Studio 2013의 서버 탐색기에서 **Microsoft Azure에 연결** 단추를 클릭합니다.

	![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-5.PNG)

7.  표시되는 메시지에 따라 Azure 계정에 로그인합니다.
8.  로그인하면 **Microsoft Azure > 저장소 > 저장소 계정**을 확장합니다. **Blob**을 마우스 오른쪽 단추로 클릭하고 **Blob 컨테이너 만들기**를 선택합니다.

	![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-6.PNG)

8.	Blob 컨테이너 이름을 지정하고 **확인**을 클릭합니다.

	![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-7.PNG)

9.	생성한 Blob 컨테이너를 관리하려면 서버 탐색기에서 해당 컨테이너를 두 번 클릭합니다. 가운데 창에 관리 인터페이스가 표시됩니다.

	![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-8.PNG)

10.	웹 페이지에서 사용한 이미지, 스크립트 또는 스타일시트를 Blob 컨테이너에 업로드하려면 **Blob 업로드** 버튼을 클릭합니다. 업로드 진행률이 **Azure 활동 로그**에 표시되며 업로드가 완료되면 Blob이 컨테이너 보기에 표시됩니다.

	![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-9.PNG)

11.	Blob을 업로드했으므로 Blob에 액세스할 수 있도록 공용으로 설정해야 합니다. 서버 탐색기에서 컨테이너를 마우스 오른쪽 단추로 클릭하고 **속성**을 선택합니다. **Blob**에 **공용 읽기 권한** 속성을 설정합니다.

	![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-10.PNG)

12.	브라우저에서 Blob 중 하나의 URL로 이동하여 Blob의 공용 액세스를 테스트합니다. 예를 들어 `http://cephalinstorage.blob.core.windows.net/cdn/cephas_lin.png`를 사용하여 업로드한 목록의 첫 번째 이미지를 테스트할 수 있습니다.

	Visual Studio의 Blob 관리 인터페이스에서는 지정된 HTTPS 주소를 사용하지 않습니다. HTTP를 사용하여 콘텐츠를 공개적으로 액세스할 수 있는지 테스트하며 이는 Azure CDN의 요구 사항입니다.

13.	적절하게 렌더링된 Blob이 브라우저에 표시되는 경우 Azure CDN의 URL을 `http://<yourStorageAccountName>.blob.core.windows.net`(으)로 변경합니다. 여기서는 CDN 끝점에서 첫 번째 이미지를 테스트하기 위해 `http://az623979.vo.msecnd.net/cdn/cephas_lin.png`을(를) 사용합니다.

	>[AZURE.NOTE]Azure 관리 포털의 CDN 탭에서 CDN 끝점의 URL을 확인할 수 있습니다.

	직접 Blob 액세스와 CDN 액세스의 성능을 비교하는 경우 Azure CDN을 사용하여 성능 향상을 확인할 수 있습니다. 다음은 Internet Explorer 11에서 이미지에 대한 Blob URL 액세스를 F12 개발자 도구로 캡처한 스크린샷입니다.

	![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-11-blob.PNG)

	동일한 이미지에 대한 CDN URL 액세스 스크린샷입니다.

	![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-11-cdn.PNG)

 	**Request** 시간을 나타내는 숫자를 살펴보세요. 이 숫자는 첫 번째 바이트에 대한 시간 또는 요청을 보내고 서버에서 첫 응답을 받은 데 걸린 시간입니다. 여기서는 동아시아 지역에서 호스트되는 Blob에 액세스할 때 서버에 도달하기 위해 태평양 전체를 횡단해야 하므로 266ms가 걸렸습니다. 그러나 Azure CDN에 액세스하는 경우 16ms밖에 걸리지 않으며 이는 거의 **20배의 성능 향상**을 의미합니다!
	
15.	이제 CDN 끝점 만들기는 웹 페이지에서 새 링크를 사용하는 정도의 문제입니다. 예를 들어 다음과 같은 이미지 태그를 추가할 수 있습니다.

		<img alt="Mugshot" src="http://az623979.vo.msecnd.net/cdn/cephas_lin.png" />

이 섹션에서는 CDN 끝점을 만드는 방법, 끝점에 콘텐츠를 업로드하는 방법 및 웹 페이지에서 CDN 콘텐츠에 연결하는 방법을 알아보았습니다.

<a name="upload"></a>
## ASP.NET 응용 프로그램에서 CDN 끝점으로 콘텐츠 업로드 자동화 ##

ASP.NET 웹 응용 프로그램의 모든 정적 콘텐츠를 CDN 끝점으로 쉽게 업로드하려는 경우 또는 지속적인 전송(예제는 [Azure 클라우드 서비스의 지속적인 전송](../cloud-services/cloud-services-dotnet-continuous-delivery.md) 참조)을 사용하여 웹 응용 프로그램을 배포하는 경우 Azure PowerShell을 사용하여 웹 응용 프로그램을 배포할 때마다 Azure Blob에 대한 최신 콘텐츠 파일의 동기화를 자동화할 수 있습니다. 예를 들어 [ASP.NET 응용 프로그램에서 Azure Blob으로 콘텐츠 파일 업로드](http://gallery.technet.microsoft.com/scriptcenter/Upload-Content-Files-from-41c2142a)(영문)의 스크립트를 실행하여 ASP.NET 응용 프로그램의 모든 콘텐츠 파일을 업로드할 수 있습니다. 이 스크립트를 사용하려면 다음을 수행하세요.

4. **시작** 메뉴에서 **Microsoft Azure PowerShell**을 실행합니다.
5. Azure PowerShell 창에서 `Get-AzurePublishSettingsFile`을(를) 실행하여 Azure 계정의 게시 설정 파일을 다운로드합니다.
6. 게시 설정 파일 다운로드가 완료되면 다음을 실행합니다. 

		Import-AzurePublishSettingsFile "<yourDownloadedFilePath>"

	>[AZURE.NOTE]게시 설정 파일을 가져오면 해당 파일이 모든 Azure PowerShell 세션에 사용되는 기본 Azure 계정이 됩니다. 즉, 위 단계를 한 번만 수행해야 합니다.
	
1. [다운로드 페이지](http://gallery.technet.microsoft.com/scriptcenter/Upload-Content-Files-from-41c2142a)에서 스크립트를 다운로드합니다. 다운로드한 스크립트를 ASP.NET 응용 프로그램의 프로젝트 폴더에 저장합니다.
2. 다운로드한 스크립트를 마우스 오른쪽 단추로 클릭하고 **속성**을 클릭합니다.
3. **차단 해제**를 클릭합니다.
4. PowerShell 창을 열고 다음을 실행합니다.

		cd <ProjectFolder>
		.\UploadContentToAzureBlobs.ps1 -StorageAccount "<yourStorageAccountName>" -StorageContainer "<yourContainerName>"

이 스크립트는 *\Content* 및 *\Scripts* 폴더의 모든 파일을 지정한 저장소 계정 및 컨테이너로 업로드합니다. 그 장점은 다음과 같습니다.

-	Visual Studio 프로젝트의 파일 구조를 자동으로 복제합니다.
-	필요한 경우 Blob 컨테이너를 자동으로 만듭니다.
-	별도의 각 Blob 컨테이너에서 여러 웹 응용 프로그램의 동일한 Azure 저장소 계정 및 CDN 끝점을 다시 사용합니다.
-	새 콘텐츠가 있는 Azure CDN을 쉽게 업데이트합니다. 콘텐츠 업데이트에 대한 자세한 내용은 [원하는 콘텐츠 업데이트를 반영하도록 CDN 캐시 구성](#update)을 참조하세요.

`-StorageContainer` 매개 변수의 경우 웹 응용 프로그램 이름 또는 Visual Studio 프로젝트 이름을 사용하는 것이 좋습니다. 앞에서는 컨테이너 이름으로 일반 "cdn"을 사용했지만, 웹 응용 프로그램 이름을 사용하면 쉽게 식별할 수 있는 동일한 컨테이너로 관련 콘텐츠를 구성할 수 있습니다.

콘텐츠 업로드가 완료되면 `http://<yourCDNName>.vo.msecnd.net/<containerName>`을(를) 사용하여 .cshtml 파일과 같이 HTML 코드의 *\Content* 및 *\Scripts* 폴더에 있는 모든 항목과 연결할 수 있습니다. 다음은 Razor 뷰에서 사용할 수 있는 예제 중 하나입니다.

	<img alt="Mugshot" src="http://az623979.vo.msecnd.net/MyMvcApp/Content/cephas_lin.png" />

PowerShell 스크립트를 연속 배달 구성으로 통합하는 예제는 [Azure 클라우드 서비스의 지속적인 전송](../cloud-services/cloud-services-dotnet-continuous-delivery.md)을 참조하세요.

<a name="update"></a>
## 원하는 콘텐츠 업데이트를 반영하도록 CDN 캐시 구성 ##

이제 Blob 컨테이너의 웹앱에서 정적 파일을 업로드한 후 프로젝트의 파일 중 하나를 변경하여 Blob 컨테이너로 다시 업로드하는 상황을 가정해 보세요. 변경 내용이 CDN 끝점으로 자동 업데이트된다고 생각할 수 있지만, 실제로는 콘텐츠의 CDN URL에 액세스할 때 반영된 업데이트가 표시되지 않아서 당황하게 됩니다.

실은, CDN이 실제로 Blob 저장소에서 자동 업데이트를 수행하지만 콘텐츠에 기본 7일 캐싱 규칙이 적용되어 이런 상황이 발생합니다. 즉, CDN 노드가 Blob 저장소에서 콘텐츠를 끌어온 후에도 동일한 콘텐츠가 캐시에서 만료될 때까지 새로 고쳐지지 않습니다.

그러나 캐시 만료를 사용자 지정할 수 있습니다. 대부분의 브라우저와 마찬가지로 Azure CDN은 콘텐츠의 Cache-Control 헤더에서 지정한 만료 시간을 따릅니다. Azure 포털의 Blob 컨테이너로 이동하여 Blob 속성을 편집하여 사용자 지정 Cache-Control 헤더 값을 지정할 수 있습니다. 아래 스크린샷은 1시간(3,600초)으로 설정한 캐시 만료를 보여 줍니다.

![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-updates-1.PNG)

PowerShell 스크립트에서 모든 Blob의 Cache-Control 헤더를 설정하여 캐시 만료를 설정할 수도 있습니다. [ASP.NET 응용 프로그램에서 CDN 끝점으로 콘텐츠 업로드 자동화](#upload) 스크립트의 경우 다음 코드 조각을 볼 수 있습니다.

    Set-AzureStorageBlobContent `
        -Container $StorageContainer `
        -Context $context `
        -File $file.FullName `
        -Blob $blobFileName `
        -Properties @{ContentType=$contentType} `
        -Force

이 코드를 다음과 같이 수정합니다.

    Set-AzureStorageBlobContent `
        -Container $StorageContainer `
        -Context $context `
        -File $file.FullName `
        -Blob $blobFileName `
        -Properties @{ContentType=$contentType; CacheControl="public, max-age=3600"} `
        -Force

새로운 Cache-Control 헤더를 사용해도 여전히 Azure CDN에서 새 콘텐츠를 끌어오기 전에 7일 동안 캐시된 콘텐츠가 만료되기를 기다려야 합니다. 즉, JavaScript 또는 CSS 업데이트와 같이 콘텐츠 업데이트를 즉시 사용하려는 경우에는 사용자 지정 캐싱 값이 도움되지 않습니다. 그러나 쿼리 문자열을 통해 파일의 이름을 바꾸거나 버전을 지정하여 이 문제를 해결할 수 있습니다. 자세한 내용은 [쿼리 문자열을 사용하여 최신 콘텐츠 즉시 제공](#query)을 참조하세요.

물론 캐싱할 경우와 장소가 있습니다. 예를 들어 3시간마다 경기 소식이 업데이트되는 월드컵 경기와 같이 업데이트가 빈번하지는 않지만 글로벌 트래픽이 많은 콘텐츠를 관리해야 하는데 자체 웹 서버에서 트래픽을 오프로드하려는 경우를 가정해 보세요. 이 경우 Azure CDN 캐싱을 사용하는 것이 훌륭한 대안일 수 있습니다.

<a name="query"></a>
## 쿼리 문자열을 사용하여 최신 콘텐츠 즉시 제공 ##

Azure CDN에서 쿼리 문자열을 사용하도록 설정하여 특정 쿼리 문자열이 포함된 URL의 콘텐츠가 별도로 캐시되도록 할 수 있습니다. 이는 캐시된 CDN 콘텐츠가 만료될 때까지 기다리지 않고 클라이언트 브라우저로 특정 콘텐츠 업데이트를 즉시 푸시하려는 경우 매우 유용한 기능입니다. URL에 버전 번호가 포함된 웹 페이지를 게시한다고 가정합니다. <pre class="prettyprint"> &lt;link href=&quot;http://az623979.vo.msecnd.net/MyMvcApp/Content/bootstrap.css<mark>?v=3.0.0</mark>&quot; rel=&quot;stylesheet&quot;/&gt; </pre>

CSS 업데이트를 게시할 경우 다음과 같이 CSS URL에 다른 버전 번호를 사용합니다. <pre class="prettyprint"> &lt;link href=&quot;http://az623979.vo.msecnd.net/MyMvcApp/Content/bootstrap.css<mark>?v=3.1.1</mark>&quot; rel=&quot;stylesheet&quot;/&gt; </pre>

쿼리 문자열을 사용하도록 설정한 CDN 끝점에 대해 두 URL은 서로 고유하므로 새로운 *bootstrap.css*를 검색하도록 웹 서버에 새로운 요청을 제출하는 것입니다. 그러나 쿼리 문자열을 사용하지 않도록 설정한 CDN 끝점에 대해서는 URL이 동일하므로 단순히 캐시된 *bootstrap.css*가 제공됩니다.

즉, 버전 번호를 자동으로 업데이트하는 것이 중요합니다. Visual Studio에서는 이것이 쉽습니다. 위의 링크를 사용할 .cshtml 파일에서는 어셈블리 번호를 기반으로 버전 번호를 지정할 수 있습니다. <pre class="prettyprint"> @{ <mark>var cdnVersion = System.Reflection.Assembly.GetAssembly( typeof(MyMvcApp.Controllers.HomeController)) .GetName().Version.ToString();</mark> }

...

&lt;link href=&quot;http://az623979.vo.msecnd.net/MyMvcApp/Content/bootstrap.css<mark>?v=@cdnVersion</mark>&quot; rel=&quot;stylesheet&quot;/&gt; </pre>

모든 게시 주기의 일부로 어셈블리 번호를 변경하면 마찬가지로 웹앱을 게시할 때마다 고유한 버전 번호를 가져올 수 있으며 다음 게시 주기 때까지 동일한 번호가 유지됩니다. 또는 Visual Studio 프로젝트의 * Properties\AssemblyInfo.cs*를 열고 `AssemblyVersion`에 `*`을(를) 사용하여 웹앱을 빌드할 때마다 Visual Studio가 어셈블리 버전 번호를 자동으로 증분하도록 설정할 수 있습니다. 예:

	[assembly: AssemblyVersion("1.0.0.*")]

## ASP.NET에서 번들로 묶인 스크립트 및 스타일시트의 경우는 어떻나요? ##

[Azure 앱 서비스 웹앱](http://go.microsoft.com/fwlink/?LinkId=529714) 및 [Azure 클라우드 서비스](/services/cloud-services/)를 사용하면 [ASP.NET 묶음 및 축소](http://www.asp.net/mvc/tutorials/mvc-4/bundling-and-minification)에 Azure CDN을 완벽하게 통합할 수 있습니다.

Azure 앱 서비스 또는 Azure 클라우드 서비스를 Azure CDN에 통합하면 다음과 같은 이점이 있습니다.

- 콘텐츠 배포(이미지, 스크립트 및 스타일시트)를 Azure 웹앱 [연속 배포](../web-sites-publish-source-control.md) 프로세스의 일부로 통합
- CDN 제공 NuGet 패키지(예: jQuery 또는 부트스트랩 버전)를 쉽게 업그레이드 
- 동일한 Visual Studio 인터페이스에서 웹 응용 프로그램 및 CDN 제공 콘텐츠 관리

관련 자습서는 다음을 참조하십시오. - [Azure 앱 서비스에서Azure CDN 사용](../cdn-websites-with-cdn.md) - [Azure CDN와 클라우드 서비스 통합](cdn-cloud-service-with-cdn.md)

Azure 앱 서비스 웹앱 또는 Azure 클라우드 서비스와 통합하지 않고도 다음 사항을 주의하면 스크립트 번들에 Azure CDN을 사용할 수 있습니다.

- Blob 저장소에 번들 스크립트를 수동으로 업로드해야 합니다. 프로그래밍 솔루션은 [stackoverflow](http://stackoverflow.com/a/13736433)(영문)에 제안되어 있습니다.
- .cshtml 파일에서 렌더링된 스크립트/CSS 태그를 변환하여 Azure CDN을 사용합니다. 예:

		@Html.Raw(Styles.Render("~/Content/css").ToString().Insert(0, "http://<yourCDNName>.vo.msecnd.net"))

## 추가 정보 ##
- [Azure CDN(콘텐츠 배달 네트워크) 개요](cdn-overview.md)
- [Azure 앱 서비스에서 Azure CDN 사용](../cdn-websites-with-cdn.md)
- [Azure CDN과 클라우드 서비스 통합](cdn-cloud-service-with-cdn.md)
- [CDN(콘텐츠 배달 네트워크) 콘텐츠를 사용자 지정 도메인에 매핑하는 방법](http://msdn.microsoft.com/library/azure/gg680307.aspx)
- [Azure에 CDN 사용](cdn-how-to-use-cdn.md)
 

<!---HONumber=62-->