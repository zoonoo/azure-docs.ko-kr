<properties pageTitle="미디어 서비스 사용 방법" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="How to Use Media Services" authors="" solutions="" manager="dwrede" editor="" />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="10/30/2014" ms.author="juliako" />


#미디어 서비스 사용 방법

이 가이드에서는 Azure 미디어 서비스를 사용하여 프로그래밍을 시작하는 방법을 보여 줍니다. 가이드는 일련의 문서로 구성되어 있으며 미디어 서비스 기술 개요, 미디어 서비스를 위해 Azure 계정을 구성하는 단계 및 일반적인 프로그래밍 작업을 수행하는 방법을 보여 주는 항목이 포함되어 있습니다. 제공된 시나리오에는 자산 업로드, 자산 암호화, 자산 인코드 및 자산 제공이 포함됩니다. 샘플은 C#으로 작성되었으며 Media Services SDK for .NET을 사용합니다. Azure 미디어 서비스에 대한 자세한 내용은 [다음 단계][] 항목을 참조하세요.

OData 기반 REST API를 사용하여 미디어 서비스를 프로그래밍할 수도 있습니다. .NET 언어나 기타 프로그래밍 언어에서 미디어 서비스에 대한 REST API 호출을 수행하여 응용 프로그램을 빌드할 수 있습니다. 미디어 서비스 REST API를 사용한 프로그래밍에 대한 전체 설명서 시리즈는 [Azure 미디어 서비스 REST API를 사용하여 응용 프로그램 빌드](http://go.microsoft.com/fwlink/?linkid=252967)를 참조하세요. 

미디어 서비스 REST API 또는 미디어 Media Services SDK를 사용하여 프로그래밍을 시작하려면 먼저 [미디어 서비스에 대한 Azure 계정 설정][] 섹션에 설명된 대로 미디어 서비스에 Azure 계정을 사용하도록 설정합니다.

최신 Media Services SDK 설명서는 [여기](http://msdn.microsoft.com/ko-kr/library/hh973613.aspx)에 있습니다. 

##<a name="what-are"></a><span class="short header">미디어 서비스 정의</span>
Azure 미디어 서비스는 Microsoft Media Platform 및 타사 미디어 구성 요소의 장점을 통합하는 확장 가능한 미디어 플랫폼을 Azure에 구성합니다. 미디어 서비스는 업계 파트너가 구성 요소 기술을 확장하거나 대체할 수 있는 미디어 파이프라인을 클라우드에 제공합니다. ISV 및 미디어 공급자는 미디어 서비스를 사용하여 종단 간 미디어 솔루션을 빌드할 수 있습니다. 이 개요에서는 미디어 서비스에 대한 일반적인 아키텍처 및 개발 시나리오에 대해 설명합니다.

다음 다이어그램은 기본 미디어 서비스 아키텍처를 보여 줍니다.

![Media Services Architecture](./media/media-services-dotnet-how-to-use/wams-01.png)

###미디어 서비스 기능 지원
최신 미디어 서비스 릴리스는 클라우드에서 미디어 응용 프로그램을 개발하기 위해 다음 기능 집합을 제공합니다.

- **수집**. 수집 작업은 자산을 업로드하고 Azure 저장소에 저장되기 전에 암호화하여 자산을 시스템으로 가져옵니다. 미디어 서비스는 파트너 구성 요소와 통합되어 빠른 UDP(User Datagram Protocol) 업로드 솔루션을 제공합니다.
- **인코드**. 인코딩 작업에는 미디어 자산 인코딩, 변형 및 변환이 포함됩니다. Azure 미디어 인코더를 사용하여 클라우드에서 인코딩 작업을 실행할 수 있습니다. 인코딩 옵션은 다음과 같습니다.
   - Azure 미디어 인코더를 사용하고 업계 최고의 IIS 부드러운 스트리밍, MP4, Apple HTTP 라이브 스트리밍으로 변환 등의 다양한 표준 코덱 및 형식으로 작업
   - 입력과 출력을 총체적으로 제어하여 전체 라이브러리 또는 개별 파일 변환
   - 지원되는 다양한 파일 형식, 형식 및 코덱 집합([미디어 서비스에 대해 지원되는 파일 형식][]).
   - 지원되는 형식 변환. 미디어 서비스를 통해 ISO MP4(.mp4)를 부드러운 스트리밍 파일 형식(PIFF 1.3)(.ismv; .isma)으로 변환할 수 있습니다. 부드러운 스트리밍 파일 형식(PIFF)을 Apple HTTP 라이브 스트리밍(.msu8, .ts)으로 변환할 수도 있습니다.
- **보호**. 콘텐츠 보호는 보안 전송, 저장소 및 전달을 위해 라이브 스트리밍 또는 주문형 콘텐츠를 암호화하는 작업입니다. 미디어 서비스는 콘텐츠 보호를 위해 DRM 기술을 알 수 없는 솔루션을 제공합니다.  현재 지원되는 DRM 기술은 Microsoft PlayReady 및 MPEG Common Encryption입니다. 추가 DRM 기술도 지원될 예정입니다. 
- **스트림**. 콘텐츠 스트리밍은 라이브 또는 주문형으로 콘텐츠를 클라이언트에 보내는 작업입니다. 또는 클라우드에서 특정 미디어 파일을 다운로드할 수 있습니다. 미디어 서비스는 콘텐츠 스트리밍을 위해 형식을 알 수 없는 솔루션을 제공합니다.  미디어 서비스는 스트리밍과 관련해서 부드러운 스트리밍, Apple HTTP 라이브 스트리밍 및 MP4 형식을 지원합니다. 추가 형식도 지원될 예정입니다. 수백만 명의 사용자로 확장하는 옵션을 지원하는 타사 CDN을 사용하여 스트리밍 콘텐츠를 매끄럽게 제공할 수도 있습니다.   

###미디어 서비스 개발 시나리오
미디어 서비스는 다음 표의 설명과 같이 여러 가지 일반적인 미디어 개발 시나리오를 지원합니다. 
<table border="2" cellspacing="0" cellpadding="5" style="border: 2px solid #000000;">
  <thead>
    <tr>
       <th>시나리오</th>
       <th>설명</th>
    </tr>
  </thead>
  <tbody>
    <tr>
        <td>종단 간 워크플로 작성</td>
        <td>클라우드에서 포괄적인 미디어 워크플로 작성. 미디어 업로드에서 콘텐츠 배포에 이르기까지 미디어 서비스는 함께 결합되어 특정 응용 프로그램 워크플로를 처리할 수 있는 다양한 구성 요소를 제공합니다. 현재 기능에는 업로드, 저장소, 인코딩, 형식 변환, 콘텐츠 보호, 주문형 스트리밍 전달 등이 포함됩니다.</td>
    </tr>
    <tr>
        <td>하이브리드 워크플로 작성</td>
        <td>미디어 서비스와 기존 도구 및 프로세스를 통합할 수 있습니다. 예를 들어 현장에서 콘텐츠를 인코딩한 후 여러 형식으로 트랜스코딩하고 Azure CDN 또는 타사 CDN을 통해 전달하기 위해 미디어 서비스에 업로드합니다. 외부 응용 프로그램 및 서비스와 통합하기 위해 표준 REST API를 통해 미디어 서비스를 개별적으로 호출할 수 있습니다.</td>
    </tr>
    <tr>
        <td>미디어 플레이어에 대한 클라우드 지원 제공</td>
        <td>여러 장치(iOS, Android 및 Windows 장치 포함)와 플랫폼에서 미디어를 만들고 관리 및 제공할 수 있습니다.</td>
    </tr>
  </tbody>
</table>

<h3><a name="media-client"></a>미디어 서비스 클라이언트 개발</h3>
SDK 및 플레이어 프레임워크를 통해 미디어 클라이언트 응용 프로그램을 빌드하여 미디어 서비스 솔루션의 도달 범위를 확장합니다. 이러한 클라이언트는 다양한 장치와 플랫폼에서 매력적인 사용자 환경을 제공하는 미디어 서비스 응용 프로그램을 빌드하려는 개발자를 위한 것입니다. 대상 장치에 따라 Microsoft 및 타사 파트너가 제공하는 SDK 및 플레이어 프레임워크 옵션을 사용할 수 있습니다.  

사용 가능한 클라이언트 SDK 및 플레이어 프레임워크 목록은 다음과 같습니다.  이러한 SDK 및 플레이어 프레임워크와 기타 계획된 SDK 및 플레이어 프레임워크, 지원할 수 있는 기능에 대한 자세한 내용은 미디어 서비스 포럼의 [미디어 서비스 클라이언트 개발][](영문)을 참조하세요. 

####Mac 및 PC 클라이언트 지원  
PC 및 Mac의 경우 Microsoft Silverlight 또는 Adobe Open Source Media Framework를 사용하여 스트리밍 환경을 대상으로 지정할 수 있습니다.

-	[Silverlight용 부드러운 스트리밍 클라이언트](http://www.microsoft.com/ko-kr/download/details.aspx?id=29940)
-	[Microsoft Media Platform: Silverlight용 플레이어 프레임워크](http://smf.codeplex.com/documentation)
-	[OSMF용 부드러운 스트리밍 플러그 인 2.0](http://go.microsoft.com/fwlink/?LinkId=275022). 이 플러그 인을 사용하는 방법에 대한 자세한 내용은 [Adobe Open Source Media Framework용 부드러운 스트리밍 플러그 인을 사용하는 방법](../media-services-use-osmf-smooth-streaming-client-plugin/)(영문)을 참조하세요.

####Windows 8 응용 프로그램
Windows 8의 경우 HTML, Javascript, XAML, C#, C+ 등의 지원되는 개발 언어와 구문을 사용하여 Windows 스토어 응용 프로그램을 빌드할 수 있습니다.

-	[Smooth Streaming Client SDK for Windows 8](http://go.microsoft.com/fwlink/?LinkID=246146). 이 SDK를 사용하여 Windows 스토어 응용 프로그램을 만드는 방법에 대한 자세한 내용은 [부드러운 스트리밍 Windows 스토어 응용 프로그램을 빌드하는 방법](http://go.microsoft.com/fwlink/?LinkId=271647)(영문)을 참조하세요. HTML5로 부드러운 스트리밍 플레이어를 만드는 방법에 대한 자세한 내용은 [연습: 첫 번째 HTML5 부드러운 스트리밍 플레이어 빌드](http://msdn.microsoft.com/ko-kr/library/jj573656(v=vs.90).aspx)(영문)를 참조하세요.

-	[Microsoft Media Platform: Windows 8 Windows 스토어 응용 프로그램용 플레이어 프레임워크](http://playerframework.codeplex.com/wikipage?title=Player%20Framework%20for%20Windows%208%20Metro%20Style%20Apps&referringTitle=Home)

####Xbox
Xbox는 부드러운 스트리밍 콘텐츠를 사용할 수 있는 Xbox LIVE 응용 프로그램을 지원합니다. Xbox LIVE ADK(응용 프로그램 개발 키트)에는 다음 항목이 포함되어 있습니다.

-	Xbox LIVE ADK용 부드러운 스트리밍 클라이언트
-	Microsoft Media Platform: Xbox LIVE ADK용 플레이어 프레임워크

####포함된 장치 또는 전용 장치
연결된 TV, 셋톱 박스, Blu-Ray 플레이어, OTT TV 박스, 모바일 장치 등 사용자 지정 응용 프로그램 개발 프레임워크 및 사용자 지정 미디어 파이프라인이 있는 장치. Microsoft는 사용이 허가될 수 있는 다음 포팅 키트를 제공하며 파트너가 플랫폼에 대한 부드러운 스트리밍 재생을 포팅할 수 있게 합니다.

-	[부드러운 스트리밍 클라이언트 포팅 키트](http://www.microsoft.com/ko-kr/mediaplatform/sspk.aspx)
-	[Microsoft PlayReady 장치 포팅 키트](http://www.microsoft.com/PlayReady/Licensing/device_technology.mspx)

####Windows Phone
Microsoft는 Windows Phone용 프리미엄 비디오 응용 프로그램을 빌드하는 데 사용할 수 있는 SDK를 제공합니다. 

-	[Silverlight용 부드러운 스트리밍 클라이언트](http://www.microsoft.com/ko-kr/download/details.aspx?id=29940)
-	[Microsoft Media Platform: Silverlight용 플레이어 프레임워크](http://smf.codeplex.com/documentation)

####iOS 장치
iPhone, iPod 및 iPad를 비롯한 iOS 장치의 경우 Microsoft는 프리미엄 비디오 콘텐츠를 제공하기 위해 이러한 플랫폼용 응용 프로그램을 빌드하는 데 사용할 수 있는 SDK, 즉 Smooth Streaming SDK for iOS Devices with PlayReady를 제공합니다.  이 SDK는 라이선스 실시권자만 사용할 수 있습니다. 자세한 내용은 [Microsoft에 메일로 문의](mailto:askdrm@microsoft.com)하세요. iOS 개발에 대한 자세한 내용은 [iOS 개발자 센터](https://developer.apple.com/devcenter/ios/index.action)를 참조하세요.

####Android 장치
여러 Microsoft 파트너는 Android 장치에서 부드러운 스트리밍을 재생하는 기능을 추가하는 Android 플랫폼용 SDK를 제공합니다. 해당 파트너에 대한 자세한 내용은 [Microsoft에 메일로 문의](mailto:sspkinfo@microsoft.com?subject=Partner%20SDKs%20for%20Android%20Devices)하세요.

##다음 단계
이제 미디어 서비스의 개요를 마쳤으므로 [미디어 서비스를 위해 컴퓨터 설정](../media-services-set-up-computer/) 항목으로 이동하세요.


  [미디어 서비스 정의]: #what-are
  [미디어 서비스 클라이언트 개발]: #media-client
  [미디어 서비스용 Azure 계정 설정]: #setup-account
  [미디어 서비스 개발을 위한 설정]: #setup-dev
  [방법: 프로그래밍 방식으로 미디어 서비스에 연결]: #connect
  [방법: 암호화된 자산 만들기 및 저장소에 업로드]: #create-asset
  [방법: 미디어 프로세서 인스턴스 가져오기]: #get-mediaproc
  [방법: 작업 진행 상태 확인]: #check-job-progress
  [방법: 자산 인코드]: #encode-asset
  [방법: PlayReady Protection을 사용하여 자산 보호]: #playready
  [방법: 저장소의 자산 관리]: #manage-asset
  [방법: 다운로드를 통해 자산 제공]: #download-asset
  [방법: 스트리밍 콘텐츠 제공]: #stream-asset
  [방법: Apple HLS 스트리밍 콘텐츠 제공]: #stream-HLS
  [방법: Azure CDN 사용]: #enable-cdn
  [다음 단계]: #next-steps

  [Azure Media Services REST API를 사용하여 응용 프로그램 빌드]: http://go.microsoft.com/fwlink/?linkid=252967
  [개방형 데이터 프로토콜]: http://odata.org/
  [WCF Data Services 5.0 for OData v3]: http://www.microsoft.com/download/en/details.aspx?id=29306
  [Azure 마켓플레이스]: https://datamarket.azure.com/
  [미디어 서비스 클라이언트 개발]: http://social.msdn.microsoft.com/Forums/en-US/MediaServices/thread/e9092ec6-2dfc-44cb-adce-1dc935309d2a
  [미디어 서비스 미리 보기:  지원되는 기능]: http://social.msdn.microsoft.com/Forums/en-US/MediaServices/thread/eb946433-16f2-4eac-834d-4057335233e0
  [미디어 서비스 후속 릴리스:  계획된 기능 지원]: http://social.msdn.microsoft.com/Forums/en-US/MediaServices/thread/431ef036-0939-4784-a939-0ecb31151ded
  [미디어 서비스 미리 보기 계정 설정]: http://go.microsoft.com/fwlink/?linkid=247287
  [Azure Media Services SDK for .NET]: http://go.microsoft.com/fwlink/?LinkID=256500
  [웹 플랫폼 설치 관리자]: http://go.microsoft.com/fwlink/?linkid=255386
  [Windows 8에 Azure SDK 설치]: http://www.windowsazure.com/ko-kr/develop/net/other-resources/windows-azure-on-windows-8/
  [Azure 미디어 서비스 설명서]: http://go.microsoft.com/fwlink/?linkid=245437
  [Azure CDN 시작]: http://msdn.microsoft.com/ko-kr/library/windowsazure/ff919705.aspx
  [미디어 서비스 포럼]: http://social.msdn.microsoft.com/Forums/en-US/MediaServices/threads
  [Media Services SDK for .NET 시작]: http://go.microsoft.com/fwlink/?linkid=252966
  [Media Services SDK for .NET을 사용하여 응용 프로그램 빌드]: http://go.microsoft.com/fwlink/?linkid=247821
  [Azure 관리 포털]: https://manage.windowsazure.com/
  [미디어 서비스 계정을 만드는 방법]: ../media-services-create-account/
  [미디어 서비스의 지원되는 파일 형식]: http://msdn.microsoft.com/ko-kr/library/dn535852.aspx



<!--HONumber=35_1-->
