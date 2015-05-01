<properties 
	pageTitle="Azure 미디어 서비스 개요" 
	description="이 항목에서는 Azure 미디어 서비스에 대한 개요를 제공합니다." 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/26/2015" 
	ms.author="juliako"/>

# Azure 미디어 서비스 개요

Microsoft Azure 미디어 서비스는 개발자가 확장 가능한 미디어 관리 및 배달 응용 프로그램을 빌드할 수 있는 확장 가능한 클라우드 기반 플랫폼입니다. 미디어 서비스는 다양한 클라이언트(예: TV, PC 및 모바일 장치)로 주문형 및 라이브 스트리밍 배달을 위해 비디오 또는 오디오 콘텐츠를 안전하게 업로드, 저장, 인코딩 및 패키징할 수 있는 REST API를 기반으로 합니다.

전체 미디어 서비스를 사용하여 종단 간 워크플로를 작성할 수 있습니다. 또한 워크플로의 일부에 타사 구성 요소를 사용하도록 선택할 수 있습니다. 예를 들어 타사 인코더를 사용하여 인코딩합니다. 그런 다음 미디어 서비스를 사용하여 업로드, 보호, 패키징 및 배달합니다.

미디어 서비스 솔루션을 빌드하려면 다음을 사용할 수 있습니다.

- [미디어 서비스 REST API](https://msdn.microsoft.com/library/azure/hh973617.aspx)
- 사용 가능한 클라이언트 SDK 중 하나: [.NET용 Azure 미디어 서비스 SDK](https://github.com/Azure/azure-sdk-for-media-services), [Java용 Azure SDK](https://github.com/Azure/azure-sdk-for-java), [Node.js용 Azure 미디어 서비스](https://github.com/fritzy/node-azure-media), [Azure PHP SDK](https://github.com/Azure/azure-sdk-for-php)
- 기존 도구: [Azure 관리 포털](http://manage.windowsazure.com/) 또는 [Azure-Media-Services-Explorer](https://github.com/Azure/Azure-Media-Services-Explorer).

**SLA(서비스 수준 계약)**: 미디어 서비스는 미디어 서비스 인코딩을 위한 REST API 트랜잭션의 99.9% 가용성을 보장합니다. 하나 이상의 스트리밍 예약 단위를 구매하는 경우 주문형 스트리밍은 기존 미디어 콘텐츠에 대해 99.9% 가용성을 보장하는 요청을 서비스합니다. 가용성은 월간 청구 주기 동안 계산됩니다. 자세한 내용을 보려면 [SLA 문서](https://www.microsoft.com/download/details.aspx?id=39302)(영문)를 다운로드하세요.

다음 포스터는 미디어 만들기부터 소비까지의 Azure 미디어 서비스 워크플로를 보여 줍니다. 포스터는 [Azure 미디어 서비스 포스터](http://www.microsoft.com/download/details.aspx?id=38195)(영문)에서 다운로드할 수 있습니다.

![개요][개요]

## 개념

자세한 내용은 [개념](media-services-concepts.md)을 참조하세요.

## 미디어 환경 선택

비디오 콘텐츠를 공유하는 첫 번째 단계 중 하나는 클라이언트에 제공할 환경의 종류를 결정하는 것입니다. 고객이 비디오 콘텐츠를 보는 방법은 무엇일까요? 인터넷에 연결되어 있을까요? 콘텐츠를 컴퓨터에서 볼까요? 핸드헬드 장치에서 볼까요? 고객은 HD 비디오를 원할까요? 이러한 질문은 고객에게 최상의 환경을 제공하는 데 도움이 될 수 있습니다.

### 비디오 액세스
 
고객은 다음과 같은 네 가지 기본적인 방법으로 비디오에 액세스할 수 있습니다.

- 오프라인으로 보기 
- 점진적 다운로드
- 스트리밍\적은 비트 전송률 스트리밍

#### 오프라인으로 보기

오프라인으로 비디오를 보기 위해 고객은 전체 비디오를 컴퓨터나 장치에 다운로드합니다. 비디오는 용량이 매우 커서 다운로드를 완료하려면 시간이 걸릴 수 있습니다. 오프라인으로 보기의 장점은 장치에 다운로드된 다음에는 비디오를 보기 위해 네트워크 연결이 필요하지 않다는 점입니다. 

#### 점진적 다운로드

점진적 다운로드에서는 고객이 인터넷에 연결되어 있어야 하며 전체 비디오가 다운로드되기 전에 비디오 보기를 시작할 수 있습니다. 오프라인으로 보기와 점진적 다운로드 방법 모두 고객이 비디오를 보는 데 사용할 장치에 전체 비디오를 저장할 만큼 충분한 저장소 공간이 있어야 합니다.

#### 스트리밍

스트리밍 기술에도 인터넷 연결은 필요하지만 한 번에 비디오의 일부만 다운로드하고 표시된 후에는 삭제합니다. 따라서 보는 장치에 필요한 저장소가 매우 작습니다. 네트워크 연결의 처리량은 다를 수 있지만 고객은 네트워크 대역폭에 관계없이 비디오를 볼 수 있기를 바랍니다. 적응 비트 전송률 기술에서는 비디오 플레이어 응용 프로그램이 네트워크 상태를 확인하고 여러 가지 비트 전송률 중에서 선택할 수 있습니다. 네트워크 통신 성능이 저하되면 클라이언트에서 더 낮은 비트 전송률을 선택하여 플레이어가 더 낮은 비디오 품질로 비디오를 계속 재생하도록 할 수 있습니다. 네트워크 상태가 향상되면 클라이언트에서 더 높은 비트 전송률로 전환하여 향상된 비디오 품질을 제공할 수 있습니다. Azure 미디어 서비스는 HLS(HTTP 라이브 스트리밍), 부드러운 스트리밍, MPEG DASH 및 HDS와 같은 적응 비트 전송률 기술을 지원합니다.

### 장치 유형

또한 고객이 비디오를 보는 데 사용할 장치의 유형도 결정해야 합니다. 미디어 서비스는 웹 브라우저, 스마트폰, 태블릿, XBOX, 셋톱 박스 및 연결된 TV를 지원합니다.

#### 웹 브라우저

웹 브라우저는 Windows PC, Macintosh PC 및 스마트폰에서 실행할 수 있습니다. PC 또는 Macintosh PC에서 실행할 경우 큰 화면과 큰 저장소 용량을 사용할 수 있습니다. 따라서 더 높은 품질의 비디오를 스트리밍할 수 있습니다. Windows PC 또는 Macintosh PC에서는 미디어 서비스가 네이티브 응용 프로그램이나 HTML 호환 웹 브라우저를 사용하여 제공한 비디오를 볼 수 있습니다. 네이티브 응용 프로그램은 부드러운 스트리밍, Apple HLS, 점진적 다운로드 또는 오프라인으로 보기를 지원할 수 있습니다. HTML5 웹 페이지는 점진적 다운로드를 지원합니다.


#### 스마트폰

스마트폰은 화면이 작고 저장소 용량이 더 작습니다. 이러한 장치에는 스트리밍이 가장 적합합니다. 는 이러한 장치에 적합 합니다. iPhone, Windows Phone 및 Android 휴대폰이 지원되며, Iphone과 Android 휴대폰은 부드러운 스트리밍 및 HLS를 지원합니다. Windows Phone은 부드러운 스트리밍을 지원합니다.

### 태블릿

태블릿은 스마트폰보다 화면이 더 크지만 일반적으로 저장소 용량은 더 작습니다. 태블릿에는 스트리밍이 가장 적합합니다. 또한 저장소 용량이 더 큰 태블릿은 오프라인으로 보기뿐만 아니라 점진적 다운로드도 사용할 수 있습니다.

#### XBox

XBox 콘솔은 화면이 크고 저장소 용량도 더 크다는 장점이 있어 오프라인, 점진적 다운로드 및 스트리밍이 모두 적합합니다.
셋톱 박스 및 연결된 TV
이러한 장치 또한 화면은 크지만 저장소 용량이 최소여서 스트리밍이 가장 적합합니다.

### 장치별로 지원되는 기술

다음 표에서는 각 장치 유형 및 미디어 서비스에서 지원되는 클라이언트 기술을 보여 줍니다.
 
<table border="1">
<tr><th>장치</th><th>기술</th></tr>
<tr><td>iOS</td><td>부드러운 스트리밍, Apple HLS, 점진적 다운로드</td></tr>
<tr><td>Windows Phone 8</td><td>부드러운 스트리밍</td></tr>
<tr><td>Windows RT</td><td>부드러운 스트리밍</td></tr>
<tr><td>Windows</td><td>부드러운 스트리밍, 점진적 다운로드</td></tr>
<tr><td>Android 휴대폰</td><td>부드러운 스트리밍 및 Apple HLS</td></tr>
<tr><td>XBox</td><td>부드러운 스트리밍</td></tr>
<tr><td>Macintosh</td><td>Apple HLS, 점진적 다운로드</td></tr>
<tr><td>셋톱 박스, 연결된 TV</td><td>부드러운 스트리밍, Apple HLS, 점진적 다운로드</td></tr>
</table>


## Azure 미디어 서비스를 사용하여 주문형 미디어 제공

자세한 내용은 [Azure 미디어 서비스를 사용하여 주문형 미디어 제공](media-services-video-on-demand-workflow.md)을 참조하세요.

## Azure 미디어 서비스를 사용하여 라이브 스트리밍 제공

자세한 내용은 위에 언급된 작업을 수행하는 방법을 보여주는 항목에 대한 링크를 포함하는 [Azure 미디어 서비스를 사용하여 라이브 스트리밍 제공](media-services-live-streaming-workflow.md)을 참조하세요.

## 클라이언트

Azure 미디어 서비스는 iOS 장치, Android 장치, Windows, Windows Phone, Xbox 및 셋톱 박스를 포함한 대부분의 플랫폼에서 풍부한 동적 클라이언트 플레이어 응용 프로그램을 만드는 데 필요한 도구를 제공합니다.

- [부드러운 스트리밍 클라이언트 SDK](http://www.iis.net/downloads/microsoft/smooth-streaming) 
- [Microsoft Media Platform: 플레이어 프레임워크](http://playerframework.codeplex.com/) 
- [HTML5 플레이어 프레임워크 설명서](http://playerframework.codeplex.com/wikipage?title=HTML5%20Player&referringTitle=Documentation) 
- [OSMF용 Microsoft 부드러운 스트리밍 플러그인](https://www.microsoft.com/download/details.aspx?id=36057) 
- [iOS용 미디어 플레이어 프레임워크](https://github.com/Azure/azure-media-player-framework) 
- [Microsoft(r) 부드러운 스트리밍 클라이언트 이식 키트 라이선스](https://www.microsoft.com/mediaplatform/sspk.aspx) 
- Windows 8에서 비디오 응용 프로그램 빌드 
- [XBOX 비디오 응용 프로그램 개발](http://xbox.create.msdn.com/) 

자세한 내용은 [비디오 플레이어 응용 프로그램 개발](media-services-develop-video-players.md)을 참조하세요.

## 패턴 및 작업 방식 지침

[패턴 및 작업 방식 지침](https://wamsg.codeplex.com/)
[온라인 설명서](https://msdn.microsoft.com/library/dn735912.aspx)
[다운로드 가능한 전자책](https://www.microsoft.com/download/details.aspx?id=42629)

## 지원

[Azure 지원](http://azure.microsoft.com/support/options/)에서는 미디어 서비스를 포함하여 Azure에 대한 지원 옵션을 제공합니다.



<!-- Images -->
[개요]: ./media/media-services-overview/media-services-overview.png


<!--HONumber=52-->