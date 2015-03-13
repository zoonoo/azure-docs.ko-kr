<properties 
	pageTitle="Azure CDN에 Azure 웹사이트 통합" 
	description="통합 Azure CDN 끝점에서 콘텐츠를 제공하는 웹 사이트의 배포 방법에 대해 설명하는 자습서입니다." 
	services="cdn, web-sites" 
	documentationCenter=".net" 
	authors="cephalin" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="cdn" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="10/02/2014" 
	ms.author="cephalin"/>

<a name="intro"></a>
# Azure CDN에 Azure 웹사이트 통합 #

Azure 웹사이트를 [Azure CDN](http://azure.microsoft.com/services/cdn/)에 통합하여 서버 노드에서 가까이에 있는 고객에게 전역적으로 웹 사이트 콘텐츠를 제공함으로써 Azure 웹사이트에 기본적으로 포함되어 있는 전역 확장 기능이 강화될 수 있습니다(모든 현재 노드 위치의 업데이트된 목록은 [여기](http://msdn.microsoft.com/library/azure/gg680302.aspx)에서 찾을 수 있음). 이러한 통합은 Azure 웹사이트의 성능을 크게 향상시키고 전 세계 웹사이트 사용자 환경을 획기적으로 개선합니다. 

Azure CDN과 Azure 웹사이트를 통합하면 다음과 같은 이점을 얻을 수 있습니다.

- 콘텐츠 배포(이미지, 스크립트 및 스타일시트)를 Azure 웹사이트의 [연속 배포](http://azure.microsoft.com/documentation/articles/web-sites-publish-source-control/) 프로세스로 통합
- jQuery 또는 부트스트랩 버전과 같은 Azure 웹사이트의 NuGet 패키지를 쉽게 업그레이드 
- 동일한 Visual Studio 인터페이스에서 웹 응용 프로그램 및 CDN 제공 콘텐츠 관리
- ASP.NET 묶음 및 축소를 Azure CDN과 통합

## 알아볼 내용 ##

이 자습서에서는 다음 방법에 대해 알아봅니다.

-	[Azure CDN 끝점을 Azure 웹사이트와 통합하여 Azure CDN에서 웹 페이지의 정적 콘텐츠 제공](#deploy)
-	[Azure 웹사이트의 정적 콘텐츠에 대한 캐시 설정 구성](#caching)
-	[Azure CDN을 통해 컨트롤러 작업의 콘텐츠 제공](#controller)
-	[Visual Studio의 스크립트 디버깅 환경은 유지하면서 Azure CDN을 통해 묶이고 축소된 콘텐츠 제공](#bundling)
-	[Azure CDN이 오프라인인 경우 스크립트 및 CSS 대체 구성](#fallback) 

## 빌드할 내용 ##

Visual Studio에서 기본 ASP.NET MVC 템플릿을 사용하여 Azure 웹사이트를 배포하고, 코드를 추가하여 통합 Azure CDN에서 이미지, 컨트롤러 작업 결과, 기본 JavaScript 및 CSS 파일과 같은 콘텐츠를 제공하고, 코드를 작성하여 CDN이 오프라인인 경우에 제공할 번들의 대체 메커니즘을 구성합니다.

## 필요한 사항 ##

이 자습서를 사용하려면 다음 필수 조건이 필요합니다.

-	활성 [Microsoft Azure 계정](http://azure.microsoft.com/account/)
-	[Azure SDK를 설치한 Visual Studio 2013](http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409)

<div class="wa-note">
  <span class="wa-icon-bulb"></span>
  <h5><a name="note"></a>이 자습서를 완료하려면 Azure 계정이 있어야 합니다.</h5>
  <ul>
    <li><a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F">Azure 계정을 무료로 개설</a>할 수 있음 - 유료 Azure 서비스를 사용해볼 수 있는 크레딧을 받게 되며 크레딧을 모두 사용한 후에도 계정을 유지하고 무료 Azure 서비스(예: 웹 서비스)를 사용할 수 있습니다.</li>
    <li><a href="http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F">MSDN 구독자 혜택을 활성화</a>할 수 있음 - MSDN 구독은 유료 Azure 서비스에 사용할 수 있는 크레딧을 매달 제공합니다.</li>
  <ul>
</div>

<a name="deploy"></a>
## 통합된 CDN 끝점에 Azure 웹사이트 배포 ##

이 섹션에서는 Visual Studio 2013에서 기본 ASP.NET MVC 응용 프로그램 템플릿을 Azure 웹사이트에 배포한 후 새로운 CDN 끝점과 통합합니다. 아래의 지침을 따르세요.

1. Visual Studio 2013의 메뉴 모음에서 **파일 > 새로 만들기 > 프로젝트 > 웹 >ASP.NET 웹 응용 프로그램**으로 이동하여 새 ASP.NET 웹 응용 프로그램을 만듭니다. 해당 서비스의 이름을 지정하고 **확인**을 클릭합니다.

	![](media/cdn-websites-with-cdn/1-new-project.png)

3. **MVC**를 선택하고 **구독 관리**를 클릭합니다.

	![](media/cdn-websites-with-cdn/2-webapp-template.png)

4. **로그인**을 클릭합니다.

	![](media/cdn-websites-with-cdn/3-manage-subscription.png)

6. 로그인 페이지에서 Azure 계정을 활성화하는 데 사용한 Microsoft 계정으로 로그인합니다.
7. 로그인했으면 **닫기**을 클릭합니다. 계속하려면 **확인**을 클릭합니다.

	![](media/cdn-websites-with-cdn/4-signed-in.png)

8. Azure 웹사이트를 만들지 않았다면 Visual Studio에서 쉽게 만들 수 있습니다. **Microsoft Azure 웹사이트 구성** 대화 상자에서 사이트 이름이 고유한지 확인합니다. 그런 후 **확인**을 클릭합니다.

	![](media/cdn-websites-with-cdn/5-create-website.png)

9. ASP.NET 응용 프로그램이 만들어지면 **Publish `<app name>` to this site now**를 클릭하여 Web Publish Activity 창에서 Azure에 해당 응용 프로그램을 게시합니다. **게시**를 클릭하여 프로세스를 완료합니다.

	![](media/cdn-websites-with-cdn/6-publish-website.png)

	게시가 완료되면 브라우저에 게시된 Azure 웹사이트가 표시됩니다. 

1. CDN 끝점을 만들려면 [Azure 관리 포털](http://manage.windowsazure.com/)에 로그인합니다. 
2. **새로 만들기** > **앱 서비스** > **CDN** > **빠른 생성**을 클릭합니다. **http://*<sitename>*.azurewebsites.net/**을 선택하고 **만들기**를 클릭합니다.

	![](media/cdn-websites-with-cdn/7-create-cdn.png)

	>[WACOM.NOTE] CDN 끝점이 생성되면 Azure 포털에 해당 URL 및 통합된 원본 도메인이 표시됩니다. 그러나 새 CDN 끝점의 구성이 모든 CDN 노드 위치에 완전히 전파되는 데는 시간이 조금 걸릴 수 있습니다. 

3. Azure 포털로 돌아가 **CDN** 탭에서 방금 만든 CDN 끝점의 이름을 클릭합니다.

	![](media/cdn-websites-with-cdn/8-select-cdn.png)

3. **쿼리 문자열 사용**을 클릭하여 CDN 캐시에서 쿼리 문자열을 사용하도록 설정합니다. 쿼리 문자열을 사용하도록 설정하면 다른 쿼리 문자열로 액세스하는 동일 링크가 별도의 항목으로 캐시됩니다.

	![](media/cdn-websites-with-cdn/9-enable-query-string.png)

	>[WACOM.NOTE] 이 자습서 섹션에서는 쿼리 문자열을 사용하지 않아도 되지만, 여기서 변경한 사항이 모든 CDN 노드에 전파되는 데 시간이 걸리기 때문에 그리고 쿼리 문자열을 사용하지 않는 콘텐츠가 CDN 캐시를 막는 것을 방지하기 위해 편의상 가능한 한 빨리 쿼리 문자열을 사용하도록 설정할 수 있습니다(CDN 콘텐츠 업데이트에 대해서는 뒷부분에서 설명).

2. 이제 CDN 끝점 주소를 클릭합니다. 끝점이 준비되면 웹사이트가 표시됩니다. **HTTP 404** 오류가 발생하는 경우 CDN 끝점이 아직 준비되지 않은 것입니다. CDN 구성이 모든 에지 노드로 전파될 때까지 최대 1시간 정도 기다려야 합니다. 

	![](media/cdn-websites-with-cdn/11-access-success.png)

1. 다음으로 ASP.NET 프로젝트의 **~/Content/bootstrap.css** 파일에 액세스합니다. 브라우저 창에서 **http://*<cdn이름>*.vo.msecnd.net/Content/bootstrap.css**로 이동합니다. 이 자습서 설정에서 이 URL은 다음과 같습니다.

		http://az673227.vo.msecnd.net/Content/bootstrap.css

	이는 CDN 끝점의 다음 원본 URL과 일치합니다.

		http://cdnwebapp.azurewebsites.net/Content/bootstrap.css

	**http://*<cdn이름>*.vo.msecnd.net/Content/bootstrap.css**로 이동한 경우 Azure 웹사이트에서 들어오는 bootstrap.css를 다운로드하라는 메시지가 표시됩니다. 

	![](media/cdn-websites-with-cdn/12-file-access.png)

마찬가지로 CDN 끝점에서 **http://*<서비스이름>*.cloudapp.net/**의 공개적으로 액세스 가능한 URL에 바로 액세스할 수 있습니다. 예를 들면 다음과 같습니다.

-	/Script 경로의 .js 파일
-	/Content 경로의 모든 콘텐츠 파일
-	모든 controller/action 
-	CDN 끝점에서 쿼리 문자열을 사용하도록 설정한 경우 쿼리 문자열이 포함된 모든 URL
-	전체 Azure 웹사이트(모든 콘텐츠가 공용인 경우)

Azure CDN을 통해 전체 Azure 웹사이트를 제공하는 것이 일반적으로는 괜찮지만 항상 좋은 생각이 아닐 수도 있습니다. 다음 사항을 주의해야 합니다.

-	Azure CDN이 개인 콘텐츠를 제공할 수 없으므로 이 접근 방식에서는 전체 사이트가 공용이어야 합니다.
-	예정된 유지 관리 또는 사용자 오류든, 어떤 이유로 CDN 끝점이 오프라인 상태가 된 경우 고객이 원본 URL인 **http://*<사이트이름>*.azurewebsites.net/**으로 리디렉션되지 않은 한 전체 웹사이트가 오프라인 상태가 됩니다. 
-	사용자 지정 캐시 컨트롤 설정([Azure 웹사이트의 정적 파일에 대한 캐싱 옵션 구성](#caching) 참조)이 있는 경우에도 CDN 끝점이 높은 수준의 동적 콘텐츠의 성능을 개선하지는 않습니다. 위와 같이 CDN 끝점에서 홈페이지를 로드하려는 경우 상당히 단순한 페이지인 기본 홈페이지를 처음 로드하는 데도 5초 이상이 걸립니다. 이 페이지에 매분 업데이트되어야 하는 동적 콘텐츠가 포함되어 있다면 클라이언트 환경이 어떨지 상상해 보세요. CDN 끝점에서 동적 콘텐츠를 제공하려면 캐시 만료가 짧아야 하며 이는 CDN 끝점에서 빈번한 캐시 누락이 발생함을 의미합니다. 그러면 Azure 웹사이트의 성능이 저하되며 CDN의 목적이 무산됩니다.

대안은 Azure 웹사이트의 사례별로 Azure CDN에서 제공할 콘텐츠를 판단하는 것입니다. 이를 위해 CDN 끝점에서 개별 콘텐츠 파일에 액세스하는 방법을 이미 알아보았습니다. [Azure CDN을 통해 컨트롤러 작업의 콘텐츠 제공](#controller)에서는 CDN 끝점을 통해 특정 컨트롤러 작업을 제공하는 방법을 설명하겠습니다.

<a name="caching"></a>
## Azure 웹사이트의 정적 파일에 대한 캐싱 옵션 구성 ##

Azure 웹사이트의 Azure CDN 통합으로 정적 콘텐츠를 CDN 끝점에서 캐시하는 방법을 지정할 수 있습니다. 이렇게 하려면 ASP.NET 프로젝트(예: **cdnwebapp**)에서 *Web.config*를 열고 `<system.webServer>`에 `<staticContent>` 요소를 추가합니다. 아래 XML은 캐시가 3일 이내에 만료되도록 구성합니다.  
<pre class="prettyprint">
<system.webServer>
  <mark><staticContent>
    <clientCache cacheControlMode=&quot;UseMaxAge&quot; cacheControlMaxAge=&quot;3.00:00:00&quot;/>
  </staticContent></mark>
  ...
</system.webServer>
</pre>

이렇게 구성하면 Azure 웹사이트의 모든 정적 파일이 CDN 캐시에서 동일한 규칙을 준수합니다. 캐싱 설정을 더 세밀하게 제어하려면 *Web.config* 파일을 폴더에 추가하고 이 파일에 해당 설정을 추가합니다. 예를 들어 *Web.config* 파일을 *\Content* 폴더에 추가하고 다음 XML로 내용을 바꿉니다.

	<?xml version="1.0"?>
	<configuration>
	  <system.webServer>
	    <staticContent>
	      <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="15.00:00:00"/>
	    </staticContent>
	  </system.webServer>
	</configuration>

이 설정을 통해 *\Content* 폴더의 모든 정적 파일은 15일 동안 캐시됩니다.

`<clientCache>` 요소를 구성하는 방법에 대한 자세한 내용은 [클라이언트 캐시 <clientCache>](http://www.iis.net/configreference/system.webserver/staticcontent/clientcache)(영문)를 참조하세요.

[Azure CDN을 통해 컨트롤러 작업의 콘텐츠 제공](#controller)에서는 CDN 캐시의 컨트롤러 작업 결과에 대해 캐시 설정을 구성하는 방법에 관해서도 설명합니다.

<a name="controller"></a>
## Azure CDN을 통해 컨트롤러 작업의 콘텐츠 제공 ##

Azure 웹사이트를 Azure CDN과 통합한 경우 Azure CDN을 통해 컨트롤러 작업의 콘텐츠를 비교적 쉽게 제공할 수 있습니다. 다시 말해서 CDN을 통해 전체 Azure 웹사이트를 제공하도록 결정한 경우 CDN을 통해 모든 컨트롤러 작업에 접근 가능하므로 이 작업을 수행할 필요가 없습니다. 하지만 [통합된 CDN 끝점에 Azure 웹사이트 배포](#deploy)에서 이미 지적한 이유 때문에 이와 반대로 Azure CDN에서 제공할 컨트롤러 작업을 대신 선택하기로 결정할 수 있습니다. [Maarten Balliauw](https://twitter.com/maartenballiauw)는 [Microsoft Azure CDN으로 웹의 대기 시간 단축](http://channel9.msdn.com/events/TechDays/Techdays-2014-the-Netherlands/Reducing-latency-on-the-web-with-the-Windows-Azure-CDN)(영문)에서 MemeGenerator 컨트롤러로 이러한 작업을 수행하는 방법을 보여 줍니다. 여기서 그 방법을 간단히 재현해보겠습니다.

Azure 웹사이트에서 다음과 같은 젊은 척 노리스의 이미지([Alan Light](http://www.flickr.com/photos/alan-light/218493788/) 제공 사진)를 기반으로 하여 밈을 생성하려고 한다고 가정해 보세요.

![](media/cdn-cloud-service-with-cdn/cdn-5-memegenerator.PNG)

고객이 최상급 이미지를 지정한 후 작업에 게시하면 밈이 생성되는 간단한 `Index` 작업이 있습니다. 유명한 척 노리스이기 때문에 이 페이지가 전 세계적으로 널리 알려질 것으로 예상합니다. 이는 Azure CDN으로 동적인 요소가 가미된 콘텐츠를 제공하는 좋은 예입니다. 

위의 단계에 따라 이 컨트롤러 작업을 설정하려면 다음을 수행합니다.

1. *\Controllers* 폴더에서 *MemeGeneratorController.cs*라는 새로운 .cs 파일을 만들고 내용을 다음 코드로 바꿉니다. 또한 강조 표시된 부분은 사용 중인 파일 경로 및 CDN 이름으로 바꿉니다.
	<pre class="prettyprint">
	using System;
	using System.Collections.Generic;
	using System.Diagnostics;
	using System.Drawing;
	using System.IO;
	using System.Net;
	using System.Web.Hosting;
	using System.Web.Mvc;
	using System.Web.UI;
	
	namespace cdnwebapp.Controllers
	{
	    public class MemeGeneratorController : Controller
	    {
	        static readonly Dictionary<string, Tuple<string ,string>> Memes = new Dictionary<string, Tuple<string, string>>();

	        public ActionResult Index()
	        {
	            return View();
	        }
	
	        [HttpPost, ActionName(&quot;Index&quot;)]
        	public ActionResult Index_Post(string top, string bottom)
	        {
	            var identifier = Guid.NewGuid().ToString();
	            if (!Memes.ContainsKey(identifier))
	            {
	                Memes.Add(identifier, new Tuple<string, string>(top, bottom));
	            }
	
	            return Content(&quot;<a href=\&quot;&quot; + Url.Action(&quot;Show&quot;, new {id = identifier}) + &quot;\&quot;>here&#39;s your meme</a>&quot;);
	        }


	        [OutputCache(VaryByParam = &quot;*&quot;, Duration = 1, Location = OutputCacheLocation.Downstream)]
	        public ActionResult Show(string id)
	        {
	            Tuple<string, string> data = null;
	            if (!Memes.TryGetValue(id, out data))
	            {
	                return new HttpStatusCodeResult(HttpStatusCode.NotFound);
	            }
	
	            if (Debugger.IsAttached) // Preserve the debug experience
	            {
	                return Redirect(string.Format(&quot;/MemeGenerator/Generate?top={0}&bottom={1}&quot;, data.Item1, data.Item2));
	            }
	            else // Get content from Azure CDN
	            {
	                return Redirect(string.Format(&quot;http://<mark><yourCDNName></mark>.vo.msecnd.net/MemeGenerator/Generate?top={0}&bottom={1}&quot;, data.Item1, data.Item2));
	            }
	        }

	        [OutputCache(VaryByParam = "*", Duration = 3600, Location = OutputCacheLocation.Downstream)]
	        public ActionResult Generate(string top, string bottom)
	        {
	            string imageFilePath = HostingEnvironment.MapPath(&quot;<mark>~/Content/chuck.bmp</mark>&quot;);
	            Bitmap bitmap = (Bitmap)Image.FromFile(imageFilePath);
	
	            using (Graphics graphics = Graphics.FromImage(bitmap))
	            {
	                SizeF size = new SizeF();
	                using (Font arialFont = FindBestFitFont(bitmap, graphics, top.ToUpperInvariant(), new Font("Arial Narrow", 100), out size))
	                {
	                    graphics.DrawString(top.ToUpperInvariant(), arialFont, Brushes.White, new PointF(((bitmap.Width - size.Width) / 2), 10f));
	                }
	                using (Font arialFont = FindBestFitFont(bitmap, graphics, bottom.ToUpperInvariant(), new Font("Arial Narrow", 100), out size))
	                {
	                    graphics.DrawString(bottom.ToUpperInvariant(), arialFont, Brushes.White, new PointF(((bitmap.Width - size.Width) / 2), bitmap.Height - 10f - arialFont.Height));
	                }
	            }
	
	            MemoryStream ms = new MemoryStream();
	            bitmap.Save(ms, System.Drawing.Imaging.ImageFormat.Png);
	            return File(ms.ToArray(), &quot;image/png&quot;);
	        }
	
	        private Font FindBestFitFont(Image i, Graphics g, String text, Font font, out SizeF size)
	        {
	            // Compute actual size, shrink if needed
	            while (true)
	            {
	                size = g.MeasureString(text, font);
	
	                // It fits, back out
	                if (size.Height < i.Height &&
	                     size.Width < i.Width) { return font; }
	
	                // Try a smaller font (90% of old size)
	                Font oldFont = font;
	                font = new Font(font.Name, (float)(font.Size * .9), font.Style);
	                oldFont.Dispose();
	            }
	        }
	    }
	}
	</pre>

2. 기본 `Index()` 작업에서 마우스 오른쪽 단추로 클릭하고 **뷰 추가**를 선택합니다.

	![](media/cdn-cloud-service-with-cdn/cdn-6-addview.PNG)

3.  아래의 설정을 적용하고 **추가**를 클릭합니다.

	![](media/cdn-cloud-service-with-cdn/cdn-7-configureview.PNG)

4. 새로운 *Views\MemeGenerator\Index.cshtml* 파일을 열고 내용을 다음과 같이 최상급을 제출하는 간단한 HTML로 바꿉니다.

		<h2>Meme Generator</h2>
		
		<form action="" method="post">
		    <input type="text" name="top" placeholder="Enter top text here" />
		    <br />
		    <input type="text" name="bottom" placeholder="Enter bottom text here" />
		    <br />
		    <input class="btn" type="submit" value="Generate meme" />
		</form>

5. Azure 웹사이트를 다시 게시하고 브라우저에서 **http://*<서비스이름>*.cloudapp.net/MemeGenerator/Index**로 이동합니다. 

양식 값을 `/MemeGenerator/Index`로 제출하면 `Index_Post` 작업 메서드는 해당 입력 식별자를 포함하는 `Show` 작업 메서드에 대한 링크를 반환합니다. 링크를 클릭하면 다음 코드로 이동합니다.  
<pre class="prettyprint">
[OutputCache(VaryByParam = &quot;*&quot;, Duration = 1, Location = OutputCacheLocation.Downstream)]
public ActionResult Show(string id)
{
    Tuple<string, string> data = null;
    if (!Memes.TryGetValue(id, out data))
    {
        return new HttpStatusCodeResult(HttpStatusCode.NotFound);
    }

    if (Debugger.IsAttached) // Preserve the debug experience
    {
        return Redirect(string.Format(&quot;/MemeGenerator/Generate?top={0}&bottom={1}&quot;, data.Item1, data.Item2));
    }
    else // Get content from Azure CDN
    {
        return Redirect(string.Format(&quot;http://<mark><yourCDNName></mark>.vo.msecnd.net/MemeGenerator/Generate?top={0}&bottom={1}&quot;, data.Item1, data.Item2));
    }
}
</pre>

로컬 디버거가 연결되어 있다면 로컬로 리디렉션되면서 정규 디버그 환경이 구현됩니다. Azure 웹사이트에서 실행 중인 경우에는 다음으로 리디렉션됩니다.

	http://<yourCDNName>.vo.msecnd.net/MemeGenerator/Generate?top=<formInput>&bottom=<formInput>

이는 CDN 끝점의 다음 원본 URL과 일치합니다.

	http://<yourSiteName>.azurewebsites.net/cdn/MemeGenerator/Generate?top=<formInput>&bottom=<formInput>

먼저 URL 다시 쓰기 규칙을 적용한 후라면 CDN 끝점에 캐시되는 실제 파일은 다음과 같습니다.

	http://<yourSiteName>.azurewebsites.net/MemeGenerator/Generate?top=<formInput>&bottom=<formInput>

그런 다음 `Generate` 메서드에서 `OutputCacheAttribute` 특성을 사용하여 작업 결과를 캐시할 방법을 지정할 수 있으며, 이렇게 지정한 설정은 Azure CDN에서 유지됩니다. 다음 코드는 캐시 만료를 1시간(3,600초)으로 지정합니다.

    [OutputCache(VaryByParam = "*", Duration = 3600, Location = OutputCacheLocation.Downstream)]

마찬가지로 Azure 웹사이트에서 모든 컨트롤러 작업의 콘텐츠를 Azure CDN을 통해 원하는 캐싱 옵션으로 제공할 수 있습니다.

다음 섹션에서는 Azure CDN을 통해 묶이고 축소된 스크립트 및 CSS를 제공하는 방법을 설명하겠습니다. 

<a name="bundling"></a>
## ASP.NET 묶음 및 축소를 Azure CDN과 통합 ##

스크립트 및 CSS 스타일시트는 드물게 변경되며 Azure CDN 캐시의 주요 후보입니다. Azure CDN을 통해 전체 웹사이트를 제공하는 것은 묶음 및 축소를 Azure CDN에 통합하는 가장 쉬운 방법입니다. 그렇지만 [Azure CDN 끝점을 Azure 웹사이트와 통합하여 Azure CDN에서 웹 페이지의 정적 콘텐츠 제공](#deploy)에 설명된 이유 때문에 사용자가 이 접근 방법을 선택하지 않을 수 있으므로 다음과 같이 바람직한 ASP.NET 묶음 및 축소 개발자 환경을 유지하면서 해당 작업 방법을 살펴보겠습니다.

-	탁월한 디버그 모드 환경
-	간소화된 배포
-	스크립트/CSS 버전 업그레이드를 위한 클라이언트 즉시 업데이트
-	CDN 끝점의 오류에 대비한 대체 메커니즘
-	코드 수정의 최소화

[Azure CDN 끝점을 Azure 웹사이트와 통합하여 Azure CDN에서 웹 페이지의 정적 콘텐츠 제공](#deploy)에서 만든 ASP.NET 프로젝트에서 *App_Start\BundleConfig.cs*를 열고 `bundles.Add()` 메서드 호출을 살펴봅니다.

    public static void RegisterBundles(BundleCollection bundles)
    {
        bundles.Add(new ScriptBundle("~/bundles/jquery").Include(
                    "~/Scripts/jquery-{version}.js"));
		...
    }

첫 번째 `bundles.Add()` 문은 가상 디렉터리 `~/bundles/jquery`에 스크립트 번들을 추가합니다. 그런 다음 *Views\Shared\_Layout.cshtml* 파일을 열고 스크립트 번들 태그가 어떻게 렌더링되는지 살펴보세요. 다음과 같은 Razor 코드 줄이 있습니다.

    @Scripts.Render("~/bundles/jquery")

이 Razor 코드가 Azure 웹사이트에서 실행되는 경우 다음과 유사한 스크립트 번들의 `<script>` 태그를 렌더링합니다. 

    <script src="/bundles/jquery?v=FVs3ACwOLIVInrAl5sdzR2jrCDmVOWFbZMY6g6Q0ulE1"></script>

그러나 `F5`를 눌러 코드가 Visual Studio에서 실행되면 다음과 같이 번들의 각 스크립트 파일을 개별적으로 렌더링합니다(위의 경우에는 하나의 스크립트 파일만 번들에 있음).

    <script src="/Scripts/jquery-1.10.2.js"></script>

그러면 프로덕션 환경에서 클라이언트의 동시 연결은 줄이고(묶음) 파일 다운로드 성능은 향상시키는(축소) 동시에 개발 환경에서 JavaScript 코드를 디버깅할 수 있습니다. 이 방법은 Azure CDN 통합을 유지하는 훌륭한 기능입니다. 또한 렌더링된 번들에는 이미 자동으로 생성된 버전 문자열이 포함되어 있기 때문에 NuGet을 통해 jQuery 버전을 업데이트할 때마다 가능한 한 빠르게 클라이언트 쪽에서 업데이트할 수 있도록 해당 기능을 복제할 수 있습니다.

ASP.NET 묶음 및 축소를 CDN 끝점과 통합하려면 다음 단계를 따르세요.

1. *App_Start\BundleConfig.cs* 파일로 돌아가 `bundles.Add()` 메서드를 수정하여 CDN 주소를 지정한 다른 [Bundle 생성자](http://msdn.microsoft.com/library/jj646464.aspx)를 사용합니다. 이를 수행하려면 `RegisterBundles` 메서드 정의를 다음 코드로 바꿉니다.  
	<pre class="prettyprint">
	public static void RegisterBundles(BundleCollection bundles)
	{
	    <mark>bundles.UseCdn = true;
	    var version = System.Reflection.Assembly.GetAssembly(typeof(Controllers.HomeController))
	        .GetName().Version.ToString();
	    var cdnUrl = &quot;http://<yourCDNName>.vo.msecnd.net/{0}?v=&quot; + version;</mark>
	
	    bundles.Add(new ScriptBundle(&quot;~/bundles/jquery&quot;<mark>, string.Format(cdnUrl, &quot;bundles/jquery&quot;)</mark>).Include(
	                &quot;~/Scripts/jquery-{version}.js&quot;));
	
	    bundles.Add(new ScriptBundle(&quot;~/bundles/jqueryval&quot;<mark>, string.Format(cdnUrl, &quot;bundles/jqueryval&quot;)</mark>).Include(
	                &quot;~/Scripts/jquery.validate*&quot;));
	
	    // Use the development version of Modernizr to develop with and learn from. Then, when you&#39;re
	    // ready for production, use the build tool at http://modernizr.com to pick only the tests you need.
	    bundles.Add(new ScriptBundle(&quot;~/bundles/modernizr&quot;<mark>, string.Format(cdnUrl, &quot;bundles/modernizer&quot;)</mark>).Include(
	                &quot;~/Scripts/modernizr-*&quot;));
	
	    bundles.Add(new ScriptBundle(&quot;~/bundles/bootstrap&quot;<mark>, string.Format(cdnUrl, &quot;bundles/bootstrap&quot;)</mark>).Include(
	                &quot;~/Scripts/bootstrap.js&quot;,
	                &quot;~/Scripts/respond.js&quot;));
	
	    bundles.Add(new StyleBundle(&quot;~/Content/css&quot;<mark>, string.Format(cdnUrl, &quot;Content/css&quot;)</mark>).Include(
	                &quot;~/Content/bootstrap.css&quot;,
	                &quot;~/Content/site.css&quot;));
	}
	</pre>

	`<CDN이름>`은 사용 중인 Azure CDN 이름으로 바꾸세요.

	쉽게 말하면 `bundles.UseCdn = true`를 설정하고 있으며 신중하게 만든 CDN URL을 각 번들에 추가했습니다. 예를 들어 코드의 첫 번째 생성자는 다음과 같습니다.

		new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "bundles/jquery"))

	이 코드는 다음과 같습니다. 

		new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "http://<yourCDNName>.vo.msecnd.net/bundles/jquery?v=<W.X.Y.Z>"))

	이 생성자에 따라 ASP.NET 묶음 및 축소가 로컬에서 디버깅될 때 개별 스크립트 파일을 렌더링하지만 지정된 CDN 주소를 사용하여 해당 스크립트에 액세스합니다. 그러나 신중하게 만든 이 CDN URL의 두 가지 중요한 특징에서 다음을 주의해야 합니다.
	
	-	이 CDN URL의 원본은 `http://<사이트이름>.azurewebsites.net/bundles/jquery?v=<W.X.Y.Z>`로, 실제로는 웹 응용 프로그램에 포함된 스크립트 번들의 가상 디렉터리입니다.
	-	CDN 생성자를 사용하고 있으므로 번들의 CDN 스크립트 태그가 더 이상 렌더링된 URL에 자동으로 생성된 버전 문자열을 포함하지 않습니다. 스크립트 번들이 Azure CDN에서 캐시 누락을 강제하도록 수정될 때마다 고유한 버전 문자열을 수동으로 생성해야 합니다. 동시에 고유한 버전 문자열은 전 배포 수명 동안 일정하게 유지되어 번들이 배포된 이후 Azure CDN에서 캐시 적중을 극대화해야 합니다.
	-	쿼리 문자열 v=<W.X.Y.Z>는 ASP.NET 프로젝트의 *Properties\AssemblyInfo.cs* 파일에서 가져옵니다. Azure에 게시할 때마다 어셈블리 버전 증분 등의 기능을 포함하는 배포 워크플로를 설정할 수 있습니다. 또는 간단히 와일드카드 문자(*)를 사용하여 빌드할 때마다 버전 문자열을 자동으로 증분하도록 프로젝트의 *Properties\AssemblyInfo.cs* 파일을 수정할 수 있습니다. 예를 들면 다음과 같습니다.
	
			[assembly: AssemblyVersion("1.0.0.*")]
	
		배포 수명 동안의 고유한 문자열 생성을 간소화하는 다른 전략도 효과가 있을 수 있습니다.

3. ASP.NET 응용 프로그램을 다시 게시하고 홈페이지에 액세스합니다.
 
4. 페이지의 HTML 코드를 확인합니다. 변경 내용을 Azure 웹사이트에 다시 게시할 때마다 고유한 버전 문자열과 함께 렌더링된 CDN URL이 표시됩니다. 예를 들면 다음과 같습니다.  
	<pre class="prettyprint">
	...

    <link href=&quot;http://az673227.vo.msecnd.net/Content/css?v=1.0.0.25449&quot; rel=&quot;stylesheet&quot;/>

    <script src=&quot;http://az673227.vo.msecnd.net/bundles/modernizer?v=1.0.0.25449&quot;>&lt;/script>

	...

    <script src=&quot;http://az673227.vo.msecnd.net/bundles/jquery?v=1.0.0.25449&quot;></script>

    <script src=&quot;http://az673227.vo.msecnd.net/bundles/bootstrap?v=1.0.0.25449&quot;></script>

	...</pre>

5. Visual Studio에서 `F5` 키를 눌러 ASP.NET 응용 프로그램을 디버그합니다. 

6. 페이지의 HTML 코드를 확인합니다. 개별적으로 렌더링된 각 스크립트 파일을 살펴볼 수 있으므로 Visual Studio의 일관된 디버그 환경을 구현할 수 있습니다.  
	<pre class="prettyprint">
	...
	
	    <link href=&quot;/Content/bootstrap.css&quot; rel=&quot;stylesheet&quot;/>
	<link href=&quot;/Content/site.css&quot; rel=&quot;stylesheet&quot;/>
	
	    <script src=&quot;/Scripts/modernizr-2.6.2.js&quot;></script>
	
	...
	
	    <script src=&quot;/Scripts/jquery-1.10.2.js&quot;></script>
	
	    <script src=&quot;/Scripts/bootstrap.js&quot;></script>
	<script src=&quot;/Scripts/respond.js&quot;></script>
	
	...    
	</pre>

<a name="fallback"></a>
## CDN URL의 대체 메커니즘 ##

어떤 이유로 Azure CDN 끝점에 문제가 발생한 경우 JavaScript 또는 부트스트랩을 로드하는 대체 옵션으로 원본 웹 서버에 액세스할 수 있을 정도로 지능적인 웹 페이지를 원합니다. CDN을 사용할 수 없어서 웹사이트의 이미지가 손실되는 심각한 상황이 올 수도 있지만 좀 더 심각한 경우는 스크립트 및 스타일시트에서 제공하는 중요한 페이지 기능을 사용하지 못하는 게 되는 것입니다.

[Bundle](http://msdn.microsoft.com/library/system.web.optimization.bundle.aspx) 클래스에는 CDN 오류에 대비해 대체 메커니즘을 구성할 수 있도록 [CdnFallbackExpression](http://msdn.microsoft.com/library/system.web.optimization.bundle.cdnfallbackexpression.aspx)이라는 속성이 포함되어 있습니다. 이 속성을 사용하려면 다음 단계를 따르세요.

1. ASP.NET 프로젝트에서 각 [Bundle 생성자](http://msdn.microsoft.com/library/jj646464.aspx)의 CDN URL을 추가한 *App_Start\BundleConfig.cs* 파일을 열고 다음과 같이 강조 표시된 내용을 변경하여 기본 번들에 대체 메커니즘을 추가합니다.  
	<pre class="prettyprint">
	public static void RegisterBundles(BundleCollection bundles)
	{
	    var version = System.Reflection.Assembly.GetAssembly(typeof(BundleConfig))
	        .GetName().Version.ToString();
	    var cdnUrl = &quot;http://cdnurl.vo.msecnd.net/.../{0}?&quot; + version;
	    bundles.UseCdn = true;
	
	    bundles.Add(new ScriptBundle(&quot;~/bundles/jquery&quot;, string.Format(cdnUrl, &quot;bundles/jquery&quot;)) 
					<mark>{ CdnFallbackExpression = &quot;window.jquery&quot; }</mark>
	                .Include(&quot;~/Scripts/jquery-{version}.js&quot;));
	
	    bundles.Add(new ScriptBundle(&quot;~/bundles/jqueryval&quot;, string.Format(cdnUrl, &quot;bundles/jqueryval&quot;)) 
					<mark>{ CdnFallbackExpression = &quot;$.validator&quot; }</mark>
	            	.Include(&quot;~/Scripts/jquery.validate*&quot;));
	
	    // Use the development version of Modernizr to develop with and learn from. Then, when you&#39;re
	    // ready for production, use the build tool at http://modernizr.com to pick only the tests you need.
	    bundles.Add(new ScriptBundle(&quot;~/bundles/modernizr&quot;, string.Format(cdnUrl, &quot;bundles/modernizer&quot;)) 
					<mark>{ CdnFallbackExpression = &quot;window.Modernizr&quot; }</mark>
					.Include(&quot;~/Scripts/modernizr-*&quot;));
	
	    bundles.Add(new ScriptBundle(&quot;~/bundles/bootstrap&quot;, string.Format(cdnUrl, &quot;bundles/bootstrap&quot;)) 	
					<mark>{ CdnFallbackExpression = &quot;$.fn.modal&quot; }</mark>
	        		.Include(
		              		&quot;~/Scripts/bootstrap.js&quot;,
		              		&quot;~/Scripts/respond.js&quot;));
	
	    bundles.Add(new StyleBundle(&quot;~/Content/css&quot;, string.Format(cdnUrl, &quot;Content/css&quot;)).Include(
	                &quot;~/Content/bootstrap.css&quot;,
	                &quot;~/Content/site.css&quot;));
	}</pre>

	`CdnFallbackExpression`이 Null이 아니면 스크립트가 HTML에 삽입되어 번들이 제대로 로드되는지 테스트하고 제대로 로드되지 않은 경우 원본 웹 서버에서 직접 번들에 액세스합니다. 이 속성은 각각의 CDN 번들이 제대로 로드되는지 테스트하는 JavaScript 식으로 설정되어야 합니다. 각 번들을 테스트하는 데 필요한 식은 콘텐츠에 따라 다릅니다. 위 기본 번들의 경우는 다음과 같습니다.
	
	-	`window.jquery`는 jquery-{version}.js에 정의되어 있습니다.
	-	`$.validator`는 jquery.validate.js에 정의되어 있습니다.
	-	`window.Modernizr`는 modernizer-{version}.js에 정의되어 있습니다.
	-	`$.fn.modal`은 bootstrap.js에 정의되어 있습니다.
	
	여기서는 `~/Cointent/css` 번들에 대해 CdnFallbackExpression을 설정하지 않았습니다. 그 이유는 현재 필요한 `<link>` 태그 대신 대체 CSS의 `<script>` 태그를 삽입한 [System.Web.Optimization에 버그](https://aspnetoptimization.codeplex.com/workitem/104)(영문)가 있기 때문입니다.
	
	그러나 [Ember 컨설팅 그룹](https://github.com/EmberConsultingGroup)(영문)에서 제공한 우수한 [스타일 번들 대체](https://github.com/EmberConsultingGroup/StyleBundleFallback)(영문)를 사용할 수 있습니다. 

2. CSS에 대해 이 해결 방법을 사용하려면 *StyleBundleExtensions.cs*라고 하는 ASP.NET 프로젝트의 *App_Start* 폴더에서 새로운 .cs 파일을 만들어 해당 내용을 [GitHub의 코드](https://github.com/EmberConsultingGroup/StyleBundleFallback/blob/master/Website/App_Start/StyleBundleExtensions.cs)(영문)로 바꿉니다. 

4. *App_Start\StyleFundleExtensions.cs*에서 네임스페이스의 이름을 ASP.NET 응용 프로그램의 네임스페이스(예: **cdnwebapp**)로 바꿉니다. 

3. `App_Start\BundleConfig.cs` 파일로 돌아가 다음과 같이 강조 표시된 코드가 있는 마지막 `bundles.Add` 문을 수정합니다.  
	<pre class="prettyprint">
	bundles.Add(new StyleBundle("~/Content/css", string.Format(cdnUrl, "Content/css"))
	    <mark>.IncludeFallback("~/Content/css", "sr-only", "width", "1px")</mark>
	    .Include(
	          "~/Content/bootstrap.css",
	          "~/Content/site.css"));
	</pre>

	이 새로운 확장 메서드는 동일한 개념을 사용하여 CSS 번들에 정의된 일치하는 클래스 이름, 규칙 이름 및 규칙 값에 대한 DOM을 확인하고 일치를 찾지 못한 경우 원본 웹 서버로 대체하는 스크립트를 HTML에 삽입합니다.

4. Azure 웹사이트에 다시 게시하고 홈 페이지에 액세스합니다. 
5. 페이지의 HTML 코드를 확인합니다. 다음과 비슷한 삽입 스크립트가 표시되어야 합니다.    
	<pre class="prettyprint">...
	
		<link href=&quot;http://az673227.vo.msecnd.net/Content/css?v=1.0.0.25474&quot; rel=&quot;stylesheet&quot;/>
	<mark><script>(function() {
	                var loadFallback,
	                    len = document.styleSheets.length;
	                for (var i = 0; i < len; i++) {
	                    var sheet = document.styleSheets[i];
	                    if (sheet.href.indexOf(&#39;http://az673227.vo.msecnd.net/Content/css?v=1.0.0.25474&#39;) !== -1) {
	                        var meta = document.createElement(&#39;meta&#39;);
	                        meta.className = &#39;sr-only&#39;;
	                        document.head.appendChild(meta);
	                        var value = window.getComputedStyle(meta).getPropertyValue(&#39;width&#39;);
	                        document.head.removeChild(meta);
	                        if (value !== &#39;1px&#39;) {
	                            document.write(&#39;<link href=&quot;/Content/css&quot; rel=&quot;stylesheet&quot; type=&quot;text/css&quot; />&#39;);
	                        }
	                    }
	                }
	                return true;
	            }())||document.write(&#39;<script src=&quot;/Content/css&quot;><\/script>&#39;);</script></mark>
	
	    <script src=&quot;http://az673227.vo.msecnd.net/bundles/modernizer?v=1.0.0.25474&quot;></script>
	<mark><script>(window.Modernizr)||document.write(&#39;<script src=&quot;/bundles/modernizr&quot;><\/script>&#39;);</script></mark>
	
	...	
	
	    <script src=&quot;http://az673227.vo.msecnd.net/bundles/jquery?v=1.0.0.25474&quot;></script>
	<mark><script>(window.jquery)||document.write(&#39;<script src=&quot;/bundles/jquery&quot;><\/script>&#39;);</script></mark>
	
	    <script src=&quot;http://az673227.vo.msecnd.net/bundles/bootstrap?v=1.0.0.25474&quot;></script>
	<mark><script>($.fn.modal)||document.write(&#39;<script src=&quot;/bundles/bootstrap&quot;><\/script>&#39;);</script></mark>
	
	...
	</pre>

	CSS 번들의 삽입 스크립트에서 여전히 다음과 같은 줄에 `CdnFallbackExpression` 속성의 잘못된 나머지 부분이 포함되어 있습니다.

        }())||document.write('<script src="/Content/css"><\/script>');</script>

	그러나 || 식의 첫 부분이 항상 true(바로 위의 줄에서)를 반환하므로 document.write() 함수가 실행되지 않습니다.

6. 대체(fallback) 스크립트가 작동 중인지를 테스트하려면 CDN 끝점의 대시보드로 돌아간 후 **끝점 사용 안 함**을 클릭합니다.

	![](media/cdn-websites-with-cdn/13-test-fallback.png)

7. Azure 웹사이트에 대한 브라우저 창을 새로 고칩니다. 이제 모든 스크립트와 스타일시트가 제대로 로드된 것을 확인할 수 있습니다.

# 추가 정보 #
- [Azure CDN(콘텐츠 배달 네트워크) 개요](http://msdn.microsoft.com/library/azure/ff919703.aspx)
- [웹 응용 프로그램에서 Azure CDN의 콘텐츠 제공](http://azure.microsoft.com/Documentation/Articles/cdn-serve-content-from-cdn-in-your-web-application/)
- [Azure CDN과 클라우드 서비스 통합](http://azure.microsoft.com/documentation/articles/cdn-cloud-service-with-cdn/)
- [ASP.NET 묶음 및 축소](http://www.asp.net/mvc/tutorials/mvc-4/bundling-and-minification)
- [Azure에 CDN 사용](http://azure.microsoft.com/documentation/articles/cdn-how-to-use/)

<!--HONumber=46--> 
