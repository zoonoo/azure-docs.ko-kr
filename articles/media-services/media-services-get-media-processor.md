<properties 
	pageTitle="미디어 프로세서를 만드는 방법 - Azure" 
	description="Azure 미디어 서비스용 미디어 콘텐츠를 인코딩하거나 형식을 변환하거나 암호화하거나 암호 해독하기 위한 미디어 프로세서 구성 요소를 만드는 방법에 대해 알아봅니다. 코드 샘플은 C#으로 작성되었으며 Media Services SDK for .NET을 사용합니다." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/13/2015" 
	ms.author="juliako"/>


#방법: 미디어 프로세서 인스턴스 가져오기

이 문서는 [미디어 서비스 주문형 비디오 워크플로](media-services-video-on-demand-workflow.md) 시리즈의 일부입니다.

##개요

미디어 서비스에서 미디어 프로세서는 미디어 콘텐츠 인코딩, 형식 변환, 암호화 또는 암호 해독과 같은 특정 처리 작업을 다루는 구성 요소입니다. 일반적으로 미디어 콘텐츠 인코드, 암호화 또는 형식 변환 작업을 만들 때 미디어 프로세서를 만듭니다.

다음 표에서는 각 사용 가능한 미디어 프로세서의 이름과 설명을 제공합니다.

<table border="2" cellspacing="0" cellpadding="5" style="border: 2px solid #000000;">
  <thead>
    <tr>
       <th>미디어 프로세서 이름</th>
       <th>설명</th>
	<th>추가 정보</th>
    </tr>
  </thead>
  <tbody>
    <tr>
       <td>Azure 미디어 인코더</td>
       <td>Azure 미디어 인코더를 사용하여 인코딩 작업을 실행할 수 있습니다.</td>
       <td><a href="http://msdn.microsoft.com/library/jj129582.aspx">Azure 미디어 인코더용 작업 기본 설정 문자열</a></td>
    </tr>
    <tr>
       <td>미디어 인코더 Premium 워크플로</td>
       <td>미디어 인코더 Premium 워크플로를 사용하여 인코딩 작업을 실행할 수 있습니다.</td>
       <td><a href="http://azure.microsoft.com/documentation/articles/media-services-encode-with-premium-workflow/">미디어 인코더 Premium 워크플로를 사용하여 인코딩</a></td>
    </tr>    
	<tr>
        <td>Azure 미디어 인덱서</td>
        <td>미디어 파일과 콘텐츠를 검색 가능하도록 설정할 수 있으며 선택 캡션 트랙과 키워드를 생성할 수 있습니다.</td>
		<td><a href="http://azure.microsoft.com/documentation/articles/media-services-index-content/">Azure 미디어 인덱서를 사용하여 미디어 파일 인덱싱</a></td>
    </tr>
    <tr>
        <td>Microsoft Azure Media Packager</td>
        <td>미디어 자산을 .mp4에서 부드러운 스트리밍 형식으로 변환할 수 있습니다. 또한 미디어 자산을 부드러운 스트리밍에서 Apple HLS(HTTP 라이브 스트리밍) 형식으로 변환할 수 있습니다.</td>
		<td><a href="http://msdn.microsoft.com/library/hh973635.aspx">Azure Media Packager용 작업 기본 설정 문자열</a></td>
    </tr>
    <tr>
        <td>Windows Azure Media Encryptor</td>
        <td>PlayReady Protection을 사용하여 미디어 자산을 암호화할 수 있습니다.</td>
        <td><a href="http://msdn.microsoft.com/library/hh973610.aspx">Azure Media Packager용 작업 기본 설정 문자열</a></td>
    </tr>
	<tr>
		<td>Azure 미디어 Hyperlapse(미리 보기)</td>
		<td>비디오 안정화를 통해 비디오에서 "범프"를 부드럽게 할 수 있습니다. 사용 가능한 클립으로 만들어 콘텐츠 속도를 높일 수도 있습니다.</td>
		<td><a href="http://go.microsoft.com/fwlink/?LinkId=613274">Azure 미디어 Hyperlapse</a></td>
	</tr>
    <tr>
        <td>저장소 암호 해독</td>
        <td>저장소 암호 해독을 사용하여 암호화된 미디어 자산의 암호를 해독할 수 있습니다.</td>
		<td>해당 없음</td>
    </tr>  </tbody>
</table>

<br />

##미디어 프로세서 가져오기

다음 메서드는 미디어 프로세서 인스턴스를 가져오는 방법을 보여 줍니다. 이 코드 예제에서는 **_context**라는 모듈 수준 변수를 사용하여 [방법: 프로그래밍 방식으로 미디어 서비스에 연결] 섹션에 설명된 대로 서버 컨텍스트를 참조한다고 가정합니다.

	private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
	{
	     var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
	        ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();
	
	    if (processor == null)
	        throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));
	
	    return processor;
	}

##다음 단계
미디어 프로세서 인스턴스를 가져오는 방법을 알아보았으므로 이제 Azure Media Encoder를 사용하여 자산을 인코드하는 방법을 보여 주는 [자산을 인코드하는 방법][](영문) 항목으로 이동합니다.

[자산을 인코드하는 방법]: media-services-encode-asset.md
[Task Preset Strings for the Azure Media Encoder]: http://msdn.microsoft.com/library/jj129582.aspx
[방법: 프로그래밍 방식으로 미디어 서비스에 연결]: ../media-services-set-up-computer/

<!---HONumber=July15_HO2-->