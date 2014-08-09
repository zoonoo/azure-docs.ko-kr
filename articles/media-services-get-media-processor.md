<properties linkid="develop-media-services-how-to-guides-create-media-processor" urlDisplayName="Create a Media Processor" pageTitle="How to Create a Media Processor - Azure" metaKeywords="" description="Learn how to create a media processor component to encode, convert format, encrypt, or decrypt media content for Azure Media Services. Code samples are written in C# and use the Media Services SDK for .NET." metaCanonical="" services="media-services" documentationCenter="" title="How to: Get a Media Processor Instance" authors="migree" solutions="" manager="" editor="" />

방법: 미디어 프로세서 인스턴스 가져오기
=======================================

이 문서는 Azure 미디어 서비스 프로그래밍을 소개하는 시리즈 중 하나입니다. 이전 항목은 [방법: 암호화된 자산 만들기 및 저장소에 업로드](http://go.microsoft.com/fwlink/?LinkID=301733&clcid=0x409)

미디어 서비스에서 미디어 프로세서는 미디어 콘텐츠 인코딩, 형식 변환, 암호화 또는 암호 해독과 같은 특정 처리 작업을 다루는 구성 요소입니다. 일반적으로 미디어 콘텐츠 인코드, 암호화 또는 형식 변환 작업을 만들 때 미디어 프로세서를 만듭니다.

다음 표에서는 각 사용 가능한 미디어 프로세서의 이름과 설명을 제공합니다.

<table data-morhtml="true" border="2" cellspacing="0" cellpadding="5" style="border: 2px solid #000000;">
  <thead data-morhtml="true">
    <tr data-morhtml="true">
       <th data-morhtml="true">미디어 프로세서 이름</th>
       <th data-morhtml="true">설명</th>
	<th data-morhtml="true">추가 정보</th>
    </tr>
  </thead>
  <tbody data-morhtml="true">
    <tr data-morhtml="true">
       <td data-morhtml="true">Azure Media Encoder</td>
       <td data-morhtml="true">Media Encoder를 사용하여 인코딩 작업을 실행할 수 있습니다.</td>
       <td data-morhtml="true"><a data-morhtml="true" href="http://msdn.microsoft.com/ko-kr/library/jj129582.aspx"> Azure Media Encoder용 태스크 기본 설정 문자열</a></td>
    </tr>
    <tr data-morhtml="true">
        <td data-morhtml="true">Azure Media Packager</td>
        <td data-morhtml="true">미디어 자산을 .mp4에서 부드러운 스트리밍 형식으로 변환할 수 있습니다. 또한 미디어 자산을 부드러운 스트리밍에서 Apple HLS(HTTP 라이브 스트리밍) 형식으로 변환할 수 있습니다.</td>
		<td data-morhtml="true"><a data-morhtml="true" href="http://msdn.microsoft.com/ko-kr/library/hh973635.aspx">Azure Media Packager용 태스크 기본 설정 문자열</a></td>
    </tr>
    <tr data-morhtml="true">
        <td data-morhtml="true">Azure Media Encryptor</td>
        <td data-morhtml="true">PlayReady Protection을 사용하여 미디어 자산을 암호화할 수 있습니다.</td>
        <td data-morhtml="true"><a data-morhtml="true" href="http://msdn.microsoft.com/ko-kr/library/hh973610.aspx">Azure Media Packager용 태스크 기본 설정 문자열</a></td>
    </tr>
    <tr data-morhtml="true">
        <td data-morhtml="true">저장소 암호 해독</td>
        <td data-morhtml="true">저장소 암호 해독을 사용하여 암호화된 미디어 자산의 암호를 해독할 수 있습니다.</td>
		<td data-morhtml="true">해당 없음</td>
    </tr>
  </tbody>
</table>

<br />

다음 메서드는 미디어 프로세서 인스턴스를 가져오는 방법을 보여 줍니다. 이 코드 예제에서는 **\_context**라는 모듈 수준 변수를 사용하여 [How to: 프로그래밍 방식으로 미디어 서비스에 연결](http://www.windowsazure.com/ko-kr/develop/media-services/how-to-guides/set-up-computer-for-media-services)(영문) 섹션에 설명된 대로 서버 컨텍스트를 참조한다고 가정합니다.

``` {}
private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
{
     var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
        ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

    if (processor == null)
        throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

    return processor;
}
```

다음 단계
---------

미디어 프로세서 인스턴스를 가져오는 방법을 알아보았으므로 이제 Azure Media Encoder를 사용하여 자산을 인코드하는 방법을 보여 주는 [자산을 인코드하는 방법](http://go.microsoft.com/fwlink/?LinkId=301753)(영문) 항목으로 이동합니다.

