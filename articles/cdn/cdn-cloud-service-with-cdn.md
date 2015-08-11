<properties 
	pageTitle="Azure CDN과 클라우드 서비스 통합" 
	description="통합 Azure CDN 끝점에서 콘텐츠를 제공하는 클라우드 서비스의 배포 방법에 대해 설명하는 자습서입니다." 
	services="cdn, cloud-services" 
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
	ms.date="05/27/2015" 
	ms.author="cephalin"/>

#<a name="intro"></a> Azure CDN과 클라우드 서비스 통합 #

<!-- Keeping this article pinned to the old portal because CDN is not yet lit up in the new portal -->

클라우드 서비스는 Azure CDN과 통합되어 클라우드 서비스의 `~/CDN` 경로에 있는 모든 콘텐츠를 제공할 수 있습니다. 이 접근 방식을 통해 다음과 같은 장점을 얻을 수 있습니다.

- 클라우드 서비스 프로젝트 디렉터리의 이미지, 스크립트 및 스타일시트를 쉽게 배포 및 업데이트
- jQuery 또는 부트스트랩 버전과 같은 클라우드 서비스의 NuGet 패키지를 쉽게 업그레이드 
- 동일한 Visual Studio 인터페이스에서 웹 응용 프로그램 및 CDN 제공 콘텐츠 모두 관리
- 웹 응용 프로그램 및 CDN 제공 콘텐츠에 대한 통합 배포 워크플로
- ASP.NET 묶음 및 축소를 Azure CDN과 통합

## 알아볼 내용 ##

이 자습서에서는 다음 방법을 알아봅니다.

-	[Azure CDN 끝점을 클라우드 서비스와 통합하여 Azure CDN에서 웹 페이지의 정적 콘텐츠 제공](#deploy)
-	[클라우드 서비스의 정적 콘텐츠에 대한 캐시 설정 구성](#caching)
-	[Azure CDN을 통해 컨트롤러 작업의 콘텐츠 제공](#controller)
-	[Visual Studio의 스크립트 디버깅 환경은 유지하면서 Azure CDN을 통해 묶이고 축소된 콘텐츠 제공](#bundling)
-	[Azure CDN이 오프라인인 경우 스크립트 및 CSS 대체 구성](#fallback) 

## 빌드할 내용 ##

기본 ASP.NET MVC 템플릿을 사용하여 클라우드 서비스 웹 역할을 배포하고, 코드를 추가하여 통합 Azure CDN에서 이미지, 컨트롤러 작업 결과, 기본 JavaScript 및 CSS 파일과 같은 콘텐츠를 제공하고, 코드를 작성하여 CDN이 오프라인인 경우에 제공할 번들의 대체 메커니즘을 구성합니다.

## 필요한 사항 ##

이 자습서를 사용하려면 다음 필수 조건이 필요합니다.

-	활성 [Microsoft Azure 계정](/account/)
-	[Azure SDK](http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409)를 설치한 Visual Studio 2013

> [AZURE.NOTE] 이 자습서를 완료하려면 Azure 계정이 있어야 합니다.
> + [Azure 계정을 무료로 개설](/pricing/free-trial/)할 수 있음 - 유료 Azure 서비스를 사용해볼 수 있는 크레딧을 받게 되며 크레딧을 모두 사용한 후에도 계정을 유지하고 무료 Azure 서비스(예: [앱 서비스](http://go.microsoft.com/fwlink/?LinkId=529714) 웹앱)를 사용할 수 있습니다.
> + [MSDN 구독자 혜택을 활성화](/pricing/member-offers/msdn-benefits-details/)할 수 있음 - MSDN 구독은 유료 Azure 서비스에 사용할 수 있는 크레딧을 매달 제공합니다.

<a name="deploy"></a>
## 통합 CDN 끝점으로 클라우드 서비스 배포 ##

이 섹션에서는 Visual Studio 2013에서 기본 ASP.NET MVC 응용 프로그램 템플릿을 클라우드 서비스 웹 역할에 배포한 후 새로운 CDN 끝점과 통합합니다. 아래의 지침을 따르세요.

1. Visual Studio 2013의 메뉴 모음에서 **파일 > 새로 만들기 > 프로젝트 > 클라우드 > Azure 클라우드 서비스**로 이동하여 새 Azure 클라우드 서비스를 만듭니다. 해당 서비스의 이름을 지정하고 **확인**을 클릭합니다.

	![](media/cdn-cloud-service-with-cdn/cdn-cs-1-new-project.PNG)

2. **ASP.NET 웹 역할**을 선택하고 **>** 단추를 클릭합니다. 확인을 클릭합니다.

	![](media/cdn-cloud-service-with-cdn/cdn-cs-2-select-role.PNG)

3. **MVC**를 선택하고 **확인**을 클릭합니다.

	![](media/cdn-cloud-service-with-cdn/cdn-cs-3-mvc-template.PNG)

4. 이제 이 웹 역할을 Azure 클라우드 서비스에 게시합니다. 클라우드 서비스 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시**를 선택합니다.

	![](media/cdn-cloud-service-with-cdn/cdn-cs-4-publish-a.png)

5. Microsoft Azure에 아직 로그인하지 않은 경우 **로그인** 단추를 클릭합니다.

	![](media/cdn-cloud-service-with-cdn/cdn-cs-5-publish-signin.png)

6. 로그인 페이지에서 Azure 계정을 활성화하는 데 사용한 Microsoft 계정으로 로그인합니다.
7. 로그인했으면 **다음**을 클릭합니다.

	![](media/cdn-cloud-service-with-cdn/cdn-cs-6-publish-signedin.png)

8. Visual Studio에서는 클라우드 서비스 또는 저장소 계정이 생성되지 않았다고 가정하고 두 계정의 생성을 지원합니다. **클라우드 서비스 및 계정 만들기** 대화 상자에서 원하는 서비스 이름을 입력합니다. 그런 다음에 **만들기**를 클릭합니다.

	![](media/cdn-cloud-service-with-cdn/cdn-cs-7-publish-createserviceandstorage.png)

9. 게시 설정 페이지에서 구성을 확인하고 **게시**를 클릭합니다.

	![](media/cdn-cloud-service-with-cdn/cdn-cs-8-publish-finalize.png)

	>[AZURE.NOTE]클라우드 서비스를 게시하는 프로세스는 시간이 조금 걸립니다. 모든 웹 역할에 대해 웹 배포 사용 옵션을 사용하면 웹 역할에 대한 신속한(하지만 임시적) 업데이트를 제공함으로써 클라우드 서비스를 훨씬 빠르게 디버깅할 수 있습니다. 이 옵션에 대한 자세한 내용은 [Azure Tools를 사용하여 클라우드 서비스 게시](http://msdn.microsoft.com/library/ff683672.aspx)를 참조하세요.

	**Microsoft Azure 활동 로그**에서 게시 상태가 **완료**로 표시된 경우 이 클라우드 서비스와 통합된 CDN 끝점을 만들 수 있습니다.

1. CDN 끝점을 만들려면 [Azure 관리 포털](http://manage.windowsazure.com/)에 로그인합니다.
2. **새로 만들기** > **앱 서비스** > **CDN** > **빠른 생성**을 클릭합니다. **http://*&lt;servicename>*.cloudapp.net/cdn/**을 선택하고 **만들기**를 클릭합니다.

	![](media/cdn-cloud-service-with-cdn/cdn-cs-10-createcdn.png)

	>[AZURE.NOTE]CDN 끝점이 생성되면 Azure 포털에 해당 URL 및 통합된 원본 도메인이 표시됩니다. 그러나 새 CDN 끝점의 구성이 모든 CDN 노드 위치에 완전히 전파되는 데는 시간이 조금 걸릴 수 있습니다.

	CDN 끝점은 클라우드 서비스의 **cdn/** 경로와 연결됩니다. **WebRole1** 프로젝트에 **cdn** 폴더를 만들거나, URL 다시 쓰기를 사용하여 이 경로의 모든 들어오는 링크를 줄일 수 있습니다. 이 자습서에서는 두 번째 방법을 사용합니다.

3. Azure 포털로 돌아가 **CDN** 탭에서 방금 만든 CDN 끝점의 이름을 클릭합니다.

	![](media/cdn-cloud-service-with-cdn/cdn-cs-11-disablequerya.png)

3. **쿼리 문자열 사용**을 클릭하여 CDN 캐시에서 쿼리 문자열을 사용하도록 설정합니다. 쿼리 문자열을 사용하도록 설정하면 다른 쿼리 문자열로 액세스하는 동일 링크가 별도의 항목으로 캐시됩니다.

	![](media/cdn-cloud-service-with-cdn/cdn-cs-12-disablequeryb.png)

	>[AZURE.NOTE]이 자습서 섹션에서는 쿼리 문자열을 사용하지 않아도 되지만, 여기서 변경한 사항이 모든 CDN 노드에 전파되는 데 시간이 걸리기 때문에 그리고 쿼리 문자열을 사용하지 않는 콘텐츠가 CDN 캐시를 막는 것을 방지하기 위해 편의상 가능한 한 빨리 쿼리 문자열을 사용하도록 설정할 수 있습니다(CDN 콘텐츠 업데이트에 대해서는 뒷부분에서 설명).

3. CDN 끝점에 ping을 실행하여 CDN 노드에 전파되었는지 확인합니다. ping 응답에 성공하기까지 최대 1시간이 걸릴 수도 있습니다.

	![](media/cdn-cloud-service-with-cdn/cdn-cs-13-testcdn.png)

2. Visual Studio 2013으로 돌아가 **WebRole1** 프로젝트에서 **Web.config**를 열고 다음 코드를 `<system.webServer>` 태그에 추가합니다.
	<pre class="prettyprint">
&lt;system.webServer>
  <mark>&lt;rewrite>
    &lt;rules>
      &lt;rule name="RewriteIncomingCdnRequest" stopProcessing="true">
        &lt;match url="^cdn/(.*)$"/>
        &lt;action type="Rewrite" url="{R:1}"/>
      &lt;/rule>
    &lt;/rules>
  &lt;/rewrite></mark>
  ...
&lt;/system.webServer>
</pre>

4. 클라우드 서비스를 다시 게시합니다. 클라우드 서비스 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시**를 선택합니다.

	![](media/cdn-cloud-service-with-cdn/cdn-cs-4-publish-a.png)

1. 게시 상태가 **완료**인 경우 브라우저 창을 열고 **http://*&lt;cdnName>*.vo.msecnd.net/Content/bootstrap.css**로 이동합니다. 이 자습서 설정에서 이 URL은 다음과 같습니다.

		http://az632148.vo.msecnd.net/Content/bootstrap.css

	이는 CDN 끝점의 다음 원본 URL과 일치합니다.

		http://cephalinservice.cloudapp.net/cdn/Content/bootstrap.css

	웹앱에서 URL 다시 쓰기 후 CDN 캐시에 캐시되는 실제 파일은 다음과 같습니다.

		http://cephalinservice.cloudapp.net/Content/bootstrap.css

	**http://*&lt;cdnName>*.vo.msecnd.net/Content/bootstrap.css**로 이동한 경우 게시한 웹앱에서 들어오는 bootstrap.css를 다운로드하라는 메시지가 표시됩니다.

	![](media/cdn-cloud-service-with-cdn/cdn-1-browser-access.PNG)

마찬가지로 CDN 끝점에서 **http://*&lt;serviceName>*.cloudapp.net/**의 공개적으로 액세스 가능한 URL에 바로 액세스할 수 있습니다. 예:

-	/Script 경로의 .js 파일
-	/Content 경로의 모든 콘텐츠 파일
-	모든 controller/action 
-	CDN 끝점에서 쿼리 문자열을 사용하도록 설정한 경우 쿼리 문자열이 포함된 모든 URL

실제로 위의 구성으로 **http://*&lt;cdnName>*.vo.msecnd.net/**에서 전체 클라우드 서비스를 호스트할 수 있습니다. **http://az632148.vo.msecnd.net/**으로 이동한 경우, Home/Index에서 작업 결과를 가져옵니다.

![](media/cdn-cloud-service-with-cdn/cdn-2-home-page.PNG)

그러나 Azure CDN을 통해 전체 클라우드 서비스를 제공하는 것이 일반적으로 좋은 생각이거나 항상 좋은 생각은 아닐 수도 있습니다. 다음 사항을 주의해야 합니다.

-	Azure CDN이 개인 콘텐츠를 제공할 수 없으므로 이 접근 방식에서는 전체 사이트가 공용이어야 합니다.
-	예정된 유지 관리 또는 사용자 오류든, 어떤 이유로 CDN 끝점이 오프라인 상태가 된 경우 고객이 원본 URL인 **http://*&lt;serviceName>*.cloudapp.net/**으로 리디렉션되지 않은 한 전체 클라우드 서비스가 오프라인 상태가 됩니다. 
-	사용자 지정 캐시 컨트롤 설정([클라우드 서비스의 정적 파일에 대한 캐싱 옵션 구성](#caching) 참조)이 있는 경우에도 CDN 끝점이 높은 수준의 동적 콘텐츠의 성능을 개선하지는 않습니다. 위와 같이 CDN 끝점에서 홈페이지를 로드하려는 경우 상당히 단순한 페이지인 기본 홈페이지를 처음 로드하는 데도 5초 이상이 걸립니다. 이 페이지에 매분 업데이트되어야 하는 동적 콘텐츠가 포함되어 있다면 클라이언트 환경이 어떨지 상상해 보세요. CDN 끝점에서 동적 콘텐츠를 제공하려면 캐시 만료가 짧아야 하며 이는 CDN 끝점에서 빈번한 캐시 누락이 발생함을 의미합니다. 그러면 클라우드 서비스의 성능이 저하되며 CDN의 목적이 무산됩니다.

대안은 클라우드 서비스의 사례별로 Azure CDN에서 제공할 콘텐츠를 판단하는 것입니다. 이를 위해 CDN 끝점에서 개별 콘텐츠 파일에 액세스하는 방법을 이미 알아보았습니다. [Azure CDN을 통해 컨트롤러 작업의 콘텐츠 제공](#controller)에서는 CDN 끝점을 통해 특정 컨트롤러 작업을 제공하는 방법을 설명하겠습니다.

더욱 제한적인 URL 다시 쓰기 규칙을 지정하면 CDN 끝점을 통해 액세스 가능한 콘텐츠를 제한할 수 있습니다. 예를 들어 URL 다시 쓰기를 *\Scripts* 폴더로 제한하려면 위의 다시 쓰기 규칙을 다음과 같이 변경하세요.  
<pre class="prettyprint">
&lt;rule name=&quot;RewriteIncomingCdnRequest&quot; stopProcessing=&quot;true&quot;&gt;
  &lt;match url=&quot;^cdn/<mark>Scripts/</mark>(.*)$&quot;/&gt;
  &lt;action type=&quot;Rewrite&quot; url=&quot;<mark>Scripts/</mark>{R:1}&quot;/&gt;
&lt;/rule&gt;
</pre>

<a name="caching"></a>
## 클라우드 서비스의 정적 파일에 대한 캐싱 옵션 구성 ##

클라우드 서비스의 Azure CDN 통합으로 정적 콘텐츠를 CDN 끝점에서 캐시하는 방법을 지정할 수 있습니다. 이를 수행하려면 웹 역할 프로젝트(예: WebRole1)에서 *Web.config*를 열고 `<staticContent>` 요소를 `<system.webServer>`에 추가합니다. 아래 XML은 캐시가 3일 이내에 만료되도록 구성합니다.  
<pre class="prettyprint">
&lt;system.webServer&gt;
  <mark>&lt;staticContent&gt;
    &lt;clientCache cacheControlMode=&quot;UseMaxAge&quot; cacheControlMaxAge=&quot;3.00:00:00&quot;/&gt;
  &lt;/staticContent&gt;</mark>
  ...
&lt;/system.webServer&gt;
</pre>

이렇게 구성하면 클라우드 서비스의 모든 정적 파일이 CDN 캐시에서 동일한 규칙을 준수합니다. 캐싱 설정을 더 세밀하게 제어하려면 *Web.config* 파일을 폴더에 추가하고 이 파일에 해당 설정을 추가합니다. 예를 들어 *Web.config* 파일을 *\Content* 폴더에 추가하고 다음 XML로 내용을 바꿉니다.

	<?xml version="1.0"?>
	<configuration>
	  <system.webServer>
	    <staticContent>
	      <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="15.00:00:00"/>
	    </staticContent>
	  </system.webServer>
	</configuration>

이 설정을 통해 *\Content* 폴더의 모든 정적 파일은 15일 동안 캐시됩니다.

`<clientCache>` 요소를 구성하는 방법에 대한 자세한 내용은 [클라이언트 캐시 &lt;clientCache>](http://www.iis.net/configreference/system.webserver/staticcontent/clientcache)(영문)를 참조하세요.

[Azure CDN을 통해 컨트롤러 작업의 콘텐츠 제공](#controller)에서는 CDN 캐시의 컨트롤러 작업 결과에 대해 캐시 설정을 구성하는 방법에 관해서도 설명합니다.

<a name="controller"></a>
## Azure CDN을 통해 컨트롤러 작업의 콘텐츠 제공 ##

클라우드 서비스 웹 역할을 Azure CDN과 통합한 경우 Azure CDN을 통해 컨트롤러 작업의 콘텐츠를 비교적 쉽게 제공할 수 있습니다. [Maarten Balliauw](https://twitter.com/maartenballiauw)는 Azure CDN을 통해 직접 클라우드 서비스를 제공(위에서 설명함)하지 않고 [Azure CDN으로 웹 대기 시간 단축(영문)](http://channel9.msdn.com/events/TechDays/Techdays-2014-the-Netherlands/Reducing-latency-on-the-web-with-the-Windows-Azure-CDN)에서 재미있는 MemeGenerator 컨트롤러로 이 작업을 수행하는 방법을 보여 줍니다. 여기서 그 방법을 간단히 재현해보겠습니다.

클라우드 서비스에서 다음과 같은 젊은 척 노리스의 이미지([Alan Light](http://www.flickr.com/photos/alan-light/218493788/) 제공 사진)를 기반으로 하여 밈을 생성하려고 한다고 가정해 보세요.

![](media/cdn-cloud-service-with-cdn/cdn-5-memegenerator.PNG)

고객이 최상급 이미지를 지정한 후 작업에 게시하면 밈이 생성되는 간단한 `Index` 작업이 있습니다. 유명한 척 노리스이기 때문에 이 페이지가 전 세계적으로 널리 알려질 것으로 예상합니다. 이는 Azure CDN으로 동적인 요소가 가미된 콘텐츠를 제공하는 좋은 예입니다.

위의 단계에 따라 이 컨트롤러 작업을 설정하려면 다음을 수행합니다.

1. *\Controllers* 폴더에서 *MemeGeneratorController.cs*라는 새로운 .cs 파일을 만들고 내용을 다음 코드로 바꿉니다. 또한 강조 표시된 부분은 사용 중인 CDN 이름으로 바꿉니다.  
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

namespace WebRole1.Controllers
{
    public class MemeGeneratorController : Controller
    {
        static readonly Dictionary&lt;string, Tuple&lt;string ,string>> Memes = new Dictionary&lt;string, Tuple&lt;string, string>>();

        public ActionResult Index()
        {
            return View();
        }

        [HttpPost, ActionName("Index")]
    	public ActionResult Index_Post(string top, string bottom)
        {
            var identifier = Guid.NewGuid().ToString();
            if (!Memes.ContainsKey(identifier))
            {
                Memes.Add(identifier, new Tuple&lt;string, string>(top, bottom));
            }

            return Content("&lt;a href="" + Url.Action("Show", new {id = identifier}) + "">here's your meme&lt;/a>");
        }

        [OutputCache(VaryByParam = "*", Duration = 1, Location = OutputCacheLocation.Downstream)]
        public ActionResult Show(string id)
        {
            Tuple&lt;string, string> data = null;
            if (!Memes.TryGetValue(id, out data))
            {
                return new HttpStatusCodeResult(HttpStatusCode.NotFound);
            }

            if (Debugger.IsAttached) // Preserve the debug experience
            {
                return Redirect(string.Format("/MemeGenerator/Generate?top={0}&amp;bottom={1}", data.Item1, data.Item2));
            }
            else // Azure CDN에서 콘텐츠 가져오기
            {
                return Redirect(string.Format("http://<mark>&lt;yourCdnName></mark>.vo.msecnd.net/MemeGenerator/Generate?top={0}&amp;bottom={1}", data.Item1, data.Item2));
            }
        }

        [OutputCache(VaryByParam = "*", Duration = 3600, Location = OutputCacheLocation.Downstream)]
        public ActionResult Generate(string top, string bottom)
        {
            string imageFilePath = HostingEnvironment.MapPath("<mark>~/Content/chuck.bmp</mark>");
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
            return File(ms.ToArray(), "image/png");
        }

        private Font FindBestFitFont(Image i, Graphics g, String text, Font font, out SizeF size)
        {
            // 실제 크기 계산, 필요한 경우 축소
            while (true)
            {
                size = g.MeasureString(text, font);

                // 적합, 철회
                if (size.Height &lt; i.Height &amp;&amp;
                     size.Width &lt; i.Width) { return font; }

                // 작은 글꼴 시도(이전 크기의 90%)
                Font oldFont = font;
                font = new Font(font.Name, (float)(font.Size * .9), font.Style);
                oldFont.Dispose();
            }
        }
    }
}
</pre>

2. 기본 `Index()` 작업을 마우스 오른쪽 단추로 클릭하고 **뷰 추가**를 선택합니다.

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

5. 클라우드 서비스를 다시 게시하고 브라우저에서 **http://*&lt;serviceName>*.cloudapp.net/MemeGenerator/Index**로 이동합니다.

양식 값을 `/MemeGenerator/Index`로 제출할 경우 `Index_Post` 동작 메서드가 각각의 입력 식별자와 함께 `Show` 동작 메서드에 대한 링크를 반환합니다. 링크를 클릭하면 다음 코드가 표시됩니다.  
<pre class="prettyprint">
[OutputCache(VaryByParam = &quot;*&quot;, Duration = 1, Location = OutputCacheLocation.Downstream)]
public ActionResult Show(string id)
{
    Tuple&lt;string, string&gt; data = null;
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
        return Redirect(string.Format(&quot;http://<mark>&lt;cdnName&gt;</mark>.vo.msecnd.net/MemeGenerator/Generate?top={0}&amp;bottom={1}&quot;, data.Item1, data.Item2));
    }
}
</pre>

로컬 디버거가 연결되어 있다면 로컬로 리디렉션되면서 정규 디버그 환경이 구현됩니다. 클라우드 서비스에서 실행 중인 경우에는 다음으로 리디렉션됩니다.

	http://<yourCDNName>.vo.msecnd.net/MemeGenerator/Generate?top=<formInput>&bottom=<formInput>

이는 CDN 끝점의 다음 원본 URL과 일치합니다.

	http://<youCloudServiceName>.cloudapp.net/cdn/MemeGenerator/Generate?top=<formInput>&bottom=<formInput>

먼저 URL 다시 쓰기 규칙을 적용한 후라면 CDN 끝점에 캐시되는 실제 파일은 다음과 같습니다.

	http://<youCloudServiceName>.cloudapp.net/MemeGenerator/Generate?top=<formInput>&bottom=<formInput>

그런 다음 `Generate` 메서드의 `OutputCacheAttribute` 특성을 사용하여 작업 결과를 캐시할 방법을 지정할 수 있으며, 이렇게 지정한 설정은 Azure CDN에서 적용됩니다. 다음 코드는 캐시 만료를 1시간(3,600초)으로 지정합니다.

    [OutputCache(VaryByParam = "*", Duration = 3600, Location = OutputCacheLocation.Downstream)]

마찬가지로 클라우드 서비스에서 모든 컨트롤러 작업의 콘텐츠를 Azure CDN을 통해 원하는 캐싱 옵션으로 제공할 수 있습니다.

다음 섹션에서는 Azure CDN을 통해 묶이고 축소된 스크립트 및 CSS를 제공하는 방법을 설명하겠습니다.

<a name="bundling"></a>
## ASP.NET 묶음 및 축소를 Azure CDN과 통합 ##

스크립트 및 CSS 스타일시트는 드물게 변경되며 Azure CDN 캐시의 주요 후보입니다. Azure CDN을 통해 전체 웹 역할을 제공하는 것은 묶음 및 축소를 Azure CDN에 통합하는 가장 쉬운 방법입니다. 그러나 이러한 방법을 원하지 않을 수도 있으므로 다음과 같이 ASP.NET 묶음 및 축소의 바람직한 개발자 환경을 유지하면서 동시에 통합을 수행하는 방법을 설명하겠습니다.

-	탁월한 디버그 모드 환경
-	간소화된 배포
-	스크립트/CSS 버전 업그레이드를 위한 클라이언트 즉시 업데이트
-	CDN 끝점의 오류에 대비한 대체 메커니즘
-	코드 수정의 최소화

[통합 CDN 끝점으로 클라우드 서비스 배포](#deploy)에서 만든 **WebRole1** 프로젝트에서 *App_Start\BundleConfig.cs* 파일을 열고 `bundles.Add()` 메서드 호출을 살펴보세요.

    public static void RegisterBundles(BundleCollection bundles)
    {
        bundles.Add(new ScriptBundle("~/bundles/jquery").Include(
                    "~/Scripts/jquery-{version}.js"));
		...
    }

첫 번째 `bundles.Add()` 문은 가상 디렉터리 `~/bundles/jquery`에 스크립트 번들을 추가합니다. 그런 다음 *Views\Shared_Layout.cshtml* 파일을 열고 스크립트 번들 태그가 어떻게 렌더링되는지 살펴보세요. 다음과 같은 Razor 코드 줄이 있습니다.

    @Scripts.Render("~/bundles/jquery")

이 Razor 코드가 Azure 웹 역할에서 실행되는 경우 다음과 유사한 스크립트 번들의 `<script>` 태그를 렌더링합니다.

    <script src="/bundles/jquery?v=FVs3ACwOLIVInrAl5sdzR2jrCDmVOWFbZMY6g6Q0ulE1"></script>

그러나 `F5` 키를 눌러 Visual Studio에서 코드가 실행되면 다음과 같이 번들의 각 스크립트 파일을 개별적으로 렌더링합니다(위의 경우에는 하나의 스크립트 파일만 번들에 있음).

    <script src="/Scripts/jquery-1.10.2.js"></script>

그러면 프로덕션 환경에서 클라이언트의 동시 연결은 줄이고(묶음) 파일 다운로드 성능은 향상시키는(축소) 동시에 개발 환경에서 JavaScript 코드를 디버깅할 수 있습니다. 이 방법은 Azure CDN 통합을 유지하는 훌륭한 기능입니다. 또한 렌더링된 번들에는 이미 자동으로 생성된 버전 문자열이 포함되어 있기 때문에 NuGet을 통해 jQuery 버전을 업데이트할 때마다 가능한 한 빠르게 클라이언트 쪽에서 업데이트할 수 있도록 해당 기능을 복제할 수 있습니다.

ASP.NET 묶음 및 축소를 CDN 끝점과 통합하려면 다음 단계를 따르세요.

1. *App_Start\BundleConfig.cs* 파일로 돌아가서 CDN 주소를 지정한 다른 [Bundle 생성자](http://msdn.microsoft.com/library/jj646464.aspx)를 사용하도록 `bundles.Add()` 메서드를 수정합니다. 이를 수행하려면 `RegisterBundles` 메서드 정의를 다음 코드로 바꿉니다.  
	<pre class="prettyprint">
public static void RegisterBundles(BundleCollection bundles)
{
    <mark>bundles.UseCdn = true;
    var version = System.Reflection.Assembly.GetAssembly(typeof(Controllers.HomeController))
        .GetName().Version.ToString();
    var cdnUrl = "http://&lt;yourCDNName>.vo.msecnd.net/{0}?v=" + version;</mark>

    bundles.Add(new ScriptBundle("~/bundles/jquery"<mark>, string.Format(cdnUrl, "bundles/jquery")</mark>).Include(
                "~/Scripts/jquery-{version}.js"));

    bundles.Add(new ScriptBundle("~/bundles/jqueryval"<mark>, string.Format(cdnUrl, "bundles/jqueryval")</mark>).Include(
                "~/Scripts/jquery.validate*"));

    // Modernizr의 개발 버전을 사용하여 개발하고 학습합니다. 그런 다음 프로덕션 준비가 되면,
    // http://modernizr.com에서 빌드 도구를 사용하여 필요한 테스트만 선택합니다.
    bundles.Add(new ScriptBundle("~/bundles/modernizr"<mark>, string.Format(cdnUrl, "bundles/modernizer")</mark>).Include(
                "~/Scripts/modernizr-*"));

    bundles.Add(new ScriptBundle("~/bundles/bootstrap"<mark>, string.Format(cdnUrl, "bundles/bootstrap")</mark>).Include(
                "~/Scripts/bootstrap.js",
                "~/Scripts/respond.js"));

    bundles.Add(new StyleBundle("~/Content/css"<mark>, string.Format(cdnUrl, "Content/css")</mark>).Include(
                "~/Content/bootstrap.css",
                "~/Content/site.css"));
	}
	</pre>
	`<yourCDNName>`을 사용 중인 Azure CDN 이름으로 바꾸세요.

	쉽게 말하면 `bundles.UseCdn = true`를 설정하고 있으며 신중하게 만든 CDN URL을 각 번들에 추가했습니다. 예를 들어 코드의 첫 번째 생성자는 다음과 같습니다.

		new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "bundles/jquery"))

	이 코드는 다음과 같습니다.

		new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "http://<yourCDNName>.vo.msecnd.net/bundles/jquery?v=<W.X.Y.Z>"))

	이 생성자에 따라 ASP.NET 묶음 및 축소가 로컬에서 디버깅될 때 개별 스크립트 파일을 렌더링하지만 지정된 CDN 주소를 사용하여 해당 스크립트에 액세스합니다. 그러나 신중하게 만든 이 CDN URL의 두 가지 중요한 특징에서 다음을 주의해야 합니다.
	
	-	이 CDN URL의 원본은 `http://<yourCloudService>.cloudapp.net/bundles/jquery?v=<W.X.Y.Z>`이며, 실제로는 클라우드 서비스에서 스크립트 번들의 가상 디렉터리입니다.
	-	CDN 생성자를 사용하고 있으므로 번들의 CDN 스크립트 태그가 더 이상 렌더링된 URL에 자동으로 생성된 버전 문자열을 포함하지 않습니다. 스크립트 번들이 Azure CDN에서 캐시 누락을 강제하도록 수정될 때마다 고유한 버전 문자열을 수동으로 생성해야 합니다. 동시에 고유한 버전 문자열은 전 배포 수명 동안 일정하게 유지되어 번들이 배포된 이후 Azure CDN에서 캐시 적중을 극대화해야 합니다.
	-	쿼리 문자열 v=<W.X.Y.Z>는 웹 역할 프로젝트의 *Properties\AssemblyInfo.cs* 파일에서 가져옵니다. Azure에 게시할 때마다 어셈블리 버전 증분 등의 기능을 포함하는 배포 워크플로를 설정할 수 있습니다. 또는 간단히 와일드카드 문자(*)를 사용하여 빌드할 때마다 버전 문자열을 자동으로 증분하도록 프로젝트의 *Properties\AssemblyInfo.cs* 파일을 수정할 수 있습니다. 예:
	
			[assembly: AssemblyVersion("1.0.0.*")]
	
		배포 수명 동안의 고유한 문자열 생성을 간소화하는 다른 전략도 효과가 있을 수 있습니다.

3. 클라우드 서비스를 다시 게시하고 홈페이지에 액세스합니다.
 
4. 페이지의 HTML 코드를 확인합니다. 변경 내용을 클라우드 서비스에 다시 게시할 때마다 고유한 버전 문자열과 함께 렌더링된 CDN URL이 표시됩니다. 예:
	<pre class="prettyprint">
...

&lt;link href="http://az632148.vo.msecnd.net/Content/css?v=1.0.0.25449" rel="stylesheet"/>

&lt;script src="http://az632148.vo.msecnd.net/bundles/modernizer?v=1.0.0.25449">&lt;/script>

...

&lt;script src="http://az632148.vo.msecnd.net/bundles/jquery?v=1.0.0.25449">&lt;/script>

&lt;script src="http://az632148.vo.msecnd.net/bundles/bootstrap?v=1.0.0.25449">&lt;/script>

...</pre>

5. Visual Studio로 돌아가 `F5` 키를 눌러 Visual Studio에서 클라우드 서비스를 디버깅합니다.

6. 페이지의 HTML 코드를 확인합니다. 개별적으로 렌더링된 각 스크립트 파일을 살펴볼 수 있으므로 Visual Studio의 일관된 디버그 환경을 구현할 수 있습니다.
	<pre class="prettyprint">
...

    &lt;link href="/Content/bootstrap.css" rel="stylesheet"/>
&lt;link href="/Content/site.css" rel="stylesheet"/>

    &lt;script src="/Scripts/modernizr-2.6.2.js">&lt;/script>

...

    &lt;script src="/Scripts/jquery-1.10.2.js">&lt;/script>

    &lt;script src="/Scripts/bootstrap.js">&lt;/script>
&lt;script src="/Scripts/respond.js">&lt;/script>

...    
</pre>

<a name="fallback"></a>
## CDN URL의 대체 메커니즘 ##

어떤 이유로 Azure CDN 끝점에 문제가 발생한 경우 JavaScript 또는 부트스트랩을 로드하는 대체 옵션으로 원본 웹 서버에 액세스할 수 있을 정도로 지능적인 웹 페이지를 원합니다. CDN을 사용할 수 없어서 웹 사이트의 이미지가 손실되는 심각한 상황이 올 수도 있지만 좀 더 심각한 경우는 스크립트 및 스타일시트에서 제공하는 중요한 페이지 기능을 사용하지 못하는 게 되는 것입니다.

[Bundle](http://msdn.microsoft.com/library/system.web.optimization.bundle.aspx) 클래스에는 CDN 오류에 대비해 대체 메커니즘을 구성할 수 있도록 [CdnFallbackExpression](http://msdn.microsoft.com/library/system.web.optimization.bundle.cdnfallbackexpression.aspx)이라는 속성이 포함되어 있습니다. 이 속성을 사용하려면 다음 단계를 따르세요.

1. 웹 역할 프로젝트에서 각 [Bundle 생성자](http://msdn.microsoft.com/library/jj646464.aspx)의 CDN URL을 추가한 *App_Start\BundleConfig.cs* 파일을 열고 다음과 같이 강조 표시된 내용을 변경하여 기본 번들에 대체 메커니즘을 추가합니다.  
	<pre class="prettyprint">
public static void RegisterBundles(BundleCollection bundles)
{
    var version = System.Reflection.Assembly.GetAssembly(typeof(BundleConfig))
        .GetName().Version.ToString();
    var cdnUrl = "http://cdnurl.vo.msecnd.net/.../{0}?" + version;
    bundles.UseCdn = true;

    bundles.Add(new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "bundles/jquery")) 
				<mark>{ CdnFallbackExpression = "window.jquery" }</mark>
                .Include("~/Scripts/jquery-{version}.js"));

    bundles.Add(new ScriptBundle("~/bundles/jqueryval", string.Format(cdnUrl, "bundles/jqueryval")) 
				<mark>{ CdnFallbackExpression = "$.validator" }</mark>
            	.Include("~/Scripts/jquery.validate*"));

    // Modernizr의 개발 버전을 사용하여 개발하고 학습합니다. 그런 다음 프로덕션 준비가 되면,
    // http://modernizr.com에서 빌드 도구를 사용하여 필요한 테스트만 선택합니다.
    bundles.Add(new ScriptBundle("~/bundles/modernizr", string.Format(cdnUrl, "bundles/modernizer")) 
				<mark>{ CdnFallbackExpression = "window.Modernizr" }</mark>
				.Include("~/Scripts/modernizr-*"));

    bundles.Add(new ScriptBundle("~/bundles/bootstrap", string.Format(cdnUrl, "bundles/bootstrap")) 	
				<mark>{ CdnFallbackExpression = "$.fn.modal" }</mark>
        		.Include(
	              		"~/Scripts/bootstrap.js",
	              		"~/Scripts/respond.js"));

    bundles.Add(new StyleBundle("~/Content/css", string.Format(cdnUrl, "Content/css")).Include(
                "~/Content/bootstrap.css",
                "~/Content/site.css"));
	}</pre>
	`CdnFallbackExpression`이 null이 아니면 스크립트가 HTML에 삽입되어 번들이 제대로 로드되는지 테스트하고 제대로 로드되지 않는 경우 원본 웹 서버에서 직접 번들에 액세스합니다. 이 속성은 각각의 CDN 번들이 제대로 로드되는지 테스트하는 JavaScript 식으로 설정되어야 합니다. 각 번들을 테스트하는 데 필요한 식은 콘텐츠에 따라 다릅니다. 위 기본 번들의 경우는 다음과 같습니다.
	
	-	`window.jquery`는 jquery-{version}.js에 정의되어 있습니다.
	-	`$.validator`는 jquery.validate.js에 정의되어 있습니다.
	-	`window.Modernizr`는 modernizer-{version}.js에 정의되어 있습니다.
	-	`$.fn.modal`은 bootstrap.js에 정의되어 있습니다.
	
	여기서는 `~/Cointent/css` 번들에 대해 CdnFallbackExpression을 설정하지 않았습니다. 그 이유는 현재 필요한 `<link>` 태그 대신 대체 CSS의 `<script>` 태그를 삽입하는 [System.Web.Optimization에 버그](https://aspnetoptimization.codeplex.com/workitem/104)(영문)가 있기 때문입니다.
	
	그러나 [Ember 컨설팅 그룹](https://github.com/EmberConsultingGroup/StyleBundleFallback)(영문)에서 제공한 우수한 [스타일 번들 대체](https://github.com/EmberConsultingGroup)(영문)를 사용할 수 있습니다.

2. 이 CSS 해결 방법을 사용하려면 *StyleBundleExtensions.cs*라고 하는 웹 역할 프로젝트의 *App_Start* 폴더에서 새로운 .cs 파일을 만들어 해당 내용을 [GitHub의 코드(영문)](https://github.com/EmberConsultingGroup/StyleBundleFallback/blob/master/Website/App_Start/StyleBundleExtensions.cs)로 바꿉니다.

4. *App_Start\StyleFundleExtensions.cs* 파일에서 웹 역할 이름(예: **WebRole1**)에 대한 네임스페이스의 이름을 바꿉니다.

3. `App_Start\BundleConfig.cs` 파일로 돌아가 마지막 `bundles.Add` 문을 다음과 같은 강조 표시된 코드로 수정합니다.
	<pre class="prettyprint">
bundles.Add(new StyleBundle("~/Content/css", string.Format(cdnUrl, "Content/css"))
    <mark>.IncludeFallback("~/Content/css", "sr-only", "width", "1px")</mark>
    .Include(
          "~/Content/bootstrap.css",
          "~/Content/site.css"));
	</pre>
	이 새로운 확장 메서드는 동일한 개념을 사용하여 CSS 번들에 정의된 일치하는 클래스 이름, 규칙 이름 및 규칙 값에 대한 DOM을 확인하고 일치 항목을 찾지 못할 경우 원본 웹 서버로 대체하는 스크립트를 HTML에 삽입합니다.

4. 클라우드 서비스를 다시 게시하고 홈페이지에 액세스합니다.
5. 페이지의 HTML 코드를 확인합니다. 다음과 비슷한 삽입 스크립트가 표시됩니다.    
	<pre class="prettyprint">...

	&lt;link href="http://az632148.vo.msecnd.net/Content/css?v=1.0.0.25474" rel="stylesheet"/>
<mark>&lt;script>(function() {
                var loadFallback,
                    len = document.styleSheets.length;
                for (var i = 0; i &lt; len; i++) {
                    var sheet = document.styleSheets[i];
                    if (sheet.href.indexOf('http://az632148.vo.msecnd.net/Content/css?v=1.0.0.25474') !== -1) {
                        var meta = document.createElement('meta');
                        meta.className = 'sr-only';
                        document.head.appendChild(meta);
                        var value = window.getComputedStyle(meta).getPropertyValue('width');
                        document.head.removeChild(meta);
                        if (value !== '1px') {
                            document.write('&lt;link href="/Content/css" rel="stylesheet" type="text/css" />');
                        }
                    }
                }
                return true;
            }())||document.write('&lt;script src="/Content/css">&lt;/script>');&lt;/script></mark>

    &lt;script src="http://az632148.vo.msecnd.net/bundles/modernizer?v=1.0.0.25474">&lt;/script>
<mark>&lt;script>(window.Modernizr)||document.write('&lt;script src="/bundles/modernizr">&lt;/script>');&lt;/script></mark>

...	

    &lt;script src="http://az632148.vo.msecnd.net/bundles/jquery?v=1.0.0.25474">&lt;/script>
<mark>&lt;script>(window.jquery)||document.write('&lt;script src="/bundles/jquery">&lt;/script>');&lt;/script></mark>

    &lt;script src="http://az632148.vo.msecnd.net/bundles/bootstrap?v=1.0.0.25474">&lt;/script>
<mark>&lt;script>($.fn.modal)||document.write('&lt;script src="/bundles/bootstrap">&lt;/script>');&lt;/script></mark>

...
	</pre>
	CSS 번들의 삽입 스크립트에서 다음 줄에 여전히 `CdnFallbackExpression` 속성의 나머지 잘못된 부분이 포함되어 있습니다.

        }())||document.write('<script src="/Content/css"></script>');</script>

	그러나 || 식의 첫 부분이 항상 true를 반환하므로(바로 위의 줄에서) document.write() 함수가 실행되지 않습니다.

## 추가 정보 ##
- [Azure CDN(콘텐츠 배달 네트워크) 개요](cdn-overview.md)
- [웹 응용 프로그램에서 Azure CDN의 콘텐츠 제공](cdn-serve-content-from-cdn-in-your-web-application.md)
- [Azure 앱 서비스에서 Azure CDN 사용](../cdn-websites-with-cdn.md)
- [ASP.NET 묶음 및 축소](http://www.asp.net/mvc/tutorials/mvc-4/bundling-and-minification)
- [Azure에 CDN 사용](cdn-how-to-use-cdn.md)
 

<!-----HONumber=July15_HO5-->