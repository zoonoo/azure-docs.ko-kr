<properties 
	pageTitle="미디어 프로세서를 만드는 방법 - Azure" 
	description="Azure 미디어 서비스용 미디어 콘텐츠를 인코딩하거나 형식을 변환하거나 암호화하거나 암호 해독하기 위한 미디어 프로세서 구성 요소를 만드는 방법에 대해 알아봅니다." 
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
	ms.date="02/10/2015" 
	ms.author="juliako"/>


# 방법: 미디어 프로세서 인스턴스 가져오기

이 문서는 [워크플로 주문형 미디어 서비스 비디오](../media-services-video-on-demand-workflow) 시리즈의 일부입니다. 

## 개요

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
       <td>Azure Media Encoder</td>
       <td>Media Encoder를 사용하여 인코딩 작업을 실행할 수 있습니다.</td>
       <td><a href="http://msdn.microsoft.com/library/jj129582.aspx"> Azure Media Encoder용 태스크 기본 설정 문자열</a></td>
    </tr>
    <tr>
        <td>Microsoft Azure Media Packager</td>
        <td>미디어 자산을 .mp4에서 부드러운 스트리밍 형식으로 변환할 수 있습니다. 또한 미디어 자산을 부드러운 스트리밍에서 Apple HLS(HTTP 라이브 스트리밍) 형식으로 변환할 수 있습니다.</td>
		<td><a href="http://msdn.microsoft.com/library/hh973635.aspx">Azure Media Packager용 태스크 기본 설정 문자열</a></td>
    </tr>
    <tr>
        <td>Microsoft Azure Media Encryptor</td>
        <td>PlayReady Protection을 사용하여 미디어 자산을 암호화할 수 있습니다.</td>
        <td><a href="http://msdn.microsoft.com/library/hh973610.aspx">Azure Media Packager용 태스크 기본 설정 문자열</a></td>
    </tr>
    <tr>
        <td>Azure 미디어 인덱서</td>
        <td>미디어 파일과 콘텐츠를 검색 가능하도록 설정할 수 있으며 선택 캡션 트랙과 키워드를 생성할 수 있습니다.</td>
		<td>해당 없음</td>
    </tr>
    <tr>
        <td>저장소 암호 해독</td>
        <td>저장소 암호 해독을 사용하여 암호화된 미디어 자산의 암호를 해독할 수 있습니다.</td>
		<td>해당 없음</td>
    </tr>  </tbody>
</table>

<br />

## 미디어 프로세서 가져오기

>[AZURE.NOTE] 미디어 서비스 REST API를 사용할 때는 다음 사항을 고려해야 합니다.
>
>미디어 서비스에서 엔터티를 액세스할 때 HTTP 요청에 구체적인 헤더 필드 및 값 설정 해야 합니다. 자세한 내용은 [미디어 서비스 REST API 개발을 위한 설치](../media-services-rest-how-to-use)를 참조합니다.

>Https://media.windows.net에 연결 후, 다른 미디어 서비스 URI를 지정하는 301 리디렉션을 받게 됩니다. 설명한 대로 새 URI에 대한 후속 호출을 확인해야 [미디어 서비스 REST API](../media-services-rest-connect_programmatically/)를 사용하여 연결할 수 있습니다. 



다음 REST 호출에서는 이름으로 미디어 프로세서 인스턴스를 가져오는 방법을 보여줍니다 (이 경우 **Azure Media Encoder**). 

	
요청:

	GET https://media.windows.net/api/MediaProcessors()?$filter=Name%20eq%20'Azure%20Media%20Encoder' HTTP/1.1
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	User-Agent: Microsoft ADO.NET Data Services
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=juliakoams1&urn%3aSubscriptionId=bbbef702-e769-477b-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423635565&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=6zwXEn7YJzVJbVCNpqDUjBLuE5iUwsdJbWvJNvpY3%2b8%3d
	x-ms-version: 2.8
	Host: media.windows.net
	
응답:
	
	HTTP/1.1 200 OK
	Cache-Control: no-cache
	Content-Length: 273
	Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
	Server: Microsoft-IIS/8.5
	x-ms-client-request-id: 8a291764-4ed7-405d-aa6e-d3ebabb0b3f6
	request-id: dceeb559-48b5-48e1-81d3-d324b6203d51
	x-ms-request-id: dceeb559-48b5-48e1-81d3-d324b6203d51
	X-Content-Type-Options: nosniff
	DataServiceVersion: 3.0;
	X-Powered-By: ASP.NET
	Strict-Transport-Security: max-age=31536000; includeSubDomains
	Date: Wed, 11 Feb 2015 00:19:56 GMT
	
	{"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#MediaProcessors","value":[{"Id":"nb:mpid:UUID:1b1da727-93ae-4e46-a8a1-268828765609","Description":"Azure Media Encoder","Name":"Azure Media Encoder","Sku":"","Vendor":"Microsoft","Version":"4.4"}]}


## 다음 단계
미디어 프로세서 인스턴스를 가져오는 방법을 알아보았으므로 이제 Azure Media Encoder를 사용하여 자산을 인코드하는 방법을 보여주는 [자산을 인코드하는 방법][] 항목으로 이동합니다.

[자산을 인코드하는 방법]: ../media-services-rest-encode-asset/
[Azure Media Encoder용 태스크 기본 설정 문자열]: http://msdn.microsoft.com/library/jj129582.aspx
[방법: 프로그래밍 방식으로 미디어 서비스에 연결]: ../media-services-rest-connect_programmatically/
<!--HONumber=45--> 
