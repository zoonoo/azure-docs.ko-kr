<properties 
	pageTitle="미디어 인코더 표준 형식 및 코덱" 
	description="이 항목에서는 Azure 미디어 인코더 표준 형식 및 코덱에 대한 개요를 제공합니다." 
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
	ms.date="07/08/2015" 
	ms.author="juliako"/>

#Media Encoder 표준 형식 및 코덱


이 문서에는 미디어 인코더 표준에서 사용할 수 있는 가장 일반적인 가져오기 및 내보내기 파일 형식 목록이 포함되어 있습니다.


[미디어 인코더 가져오기 형식](#import_formats)

[미디어 인코더 내보내기 형식](#export_formats)


##<a id="import_formats"></a>미디어 인코더 가져오기 형식 

다음 섹션에는 가져오기에 지원되는 코덱 및 파일 형식이 나열되어 있습니다.


##비디오 코덱:

- MPEG-2
- H.264
- 압축되지 않은 YUV420 또는 mezzanine
- DNxHD
- VC-1/WMV9
- Mpeg-4 2부
- JPEG 2000
- Theora

###오디오 코덱

- PCM
- AAC(AAC-LC, AAC-HE 및 AAC-HEv2)
- WMA9/Pro
- MP3(MPEG-1 Audio Layer 3)
- FLAC
- Opus
- Vorbis
 
###형식

<table border="1">
<tr><th>파일 형식</th><th>파일 확장명</th></tr>
<tr><td>FLV(H.264 및 AAC 코덱 포함) </td><td>.flv</td></tr>
<tr><td>MP4/ISMV</td><td>.ismv</td></tr>
<tr><td>MPEG2 PS, MPEG2-TS 3GP</td><td>.ts,.ps,.3gp</td></tr>
<tr><td>MXF</td><td>.mxf</td></tr>
<tr><td>WMV/ASF</td><td>.mwv, .asf</td></tr>
<tr><td>DVR-MS</td><td>.dvr-ms </td></tr>
<tr><td>AVI</td><td>.avi</td></tr>
<tr><td>Matroska</td><td>.mkv</td></tr>
<tr><td>GXF</td><td>.gxf</td></tr>
<tr><td>WAVE/WAV </td><td>.wav</td></tr>
</table>

##<a id="export_formats"></a>미디어 인코더 내보내기 형식

다음 표에는 내보내기에 지원되는 코덱 및 파일 형식이 나열되어 있습니다.


<table border="1">
<tr><th>파일 형식</th><th>비디오 코덱</th><th>오디오 코덱</th></tr>
<tr><td>MP4(*.mp4)<br/><br/>(다중 비트 전송률 MP4 컨테이너 포함) </td><td>H.264(High, Main 및 Baseline Profiles)</td><td>AAC-LC, HE-AAC v1, HE-AAC v2 </td></tr>
<tr><td>MPEG2-TS </td><td>H.264(High, Main 및 Baseline Profiles)</td><td>AAC-LC, HE-AAC v1, HE-AAC v2 </td></tr>
</table>

##참고 항목

[Azure 미디어 서비스로 주문형 콘텐츠 인코딩](media-services-encode-asset.md)

<!---HONumber=July15_HO3-->