<properties 
	pageTitle="Azure 미디어 인코더 형식 및 코덱" 
	description="이 항목에서는 Azure 미디어 인코더 형식 및 코덱에 대한 개요를 제공합니다." 
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
	ms.date="02/27/2015" 
	ms.author="juliako"/>

#Azure 미디어 인코더 형식 및 코덱

인코더는 코덱을 사용하여 디지털 미디어를 압축합니다. 일반적으로 인코더에는 생성된 미디어의 속성(예: 사용된 코덱, 파일 형식, 해상도 및 비트 전송률)을 지정할 수 있는 다양한 설정이 있습니다. 파일 형식은 비디오를 압축하는 데 사용된 코덱에 대한 정보뿐만 아니라 압축된 비디오를 보관하는 컨테이너입니다. 

코덱에는 두 개의 구성 요소가 있으며, 전송을 위해 디지털 미디어 파일을 압축하는 구성 요소와 재생을 위해 디지털 미디어 파일의 압축을 푸는 구성 요소입니다. 코덱에는 오디오를 압축하고 압축을 푸는 오디오 코덱과 비디오를 압축하고 압축을 푸는 비디오 코덱이 있습니다. 코덱은 무손실 압축이나 손실 허용 압축을 사용할 수 있습니다. 무손실 코덱은 압축이 수행될 때 모든 정보를 유지합니다. 파일의 압축을 푸는 경우 결과 파일이 입력 미디어와 동일하므로 무손실 코덱은 보관 및 저장에 매우 적합합니다. 손실 허용 코덱은 인코딩할 때 일부 정보가 손실되고 원본보다 더 작은 파일을 생성하여 비디오 품질이 손상되므로 인터넷을 통한 스트리밍에 적합합니다. Azure 미디어 인코더에서 인코딩하는 데 사용되는 두 가지 주요 코덱은 H.264 및 VC-1입니다. 다른 코덱은 인코더의 파트너 에코시스템에서 사용할 수 있습니다.

코덱과 파일 형식 간의 차이점을 이해하는 것은 중요합니다. 코덱은 압축/압축 해제 알고리즘을 구현하는 소프트웨어이고 파일 형식은 압축된 비디오를 보관하는 컨테이너입니다. 자세한 내용은 [인코딩 및 패키징](http://blog-ndrouin.azurewebsites.net/streaming-media-terminology-explained/)(영문)을 참조하세요.

이 문서에는 Azure 미디어 인코더에서 사용할 수 있는 가장 일반적인 가져오기 및 내보내기 파일 형식 목록이 포함되어 있습니다.


[미디어 인코더 가져오기 형식](#import_formats)

[미디어 인코더 내보내기 형식](#export_formats)


##<a id="import_formats"></a>미디어 인코더 가져오기 형식 

다음 섹션에는 가져오기에 지원되는 코덱 및 파일 형식이 나열되어 있습니다.

###비디오 코덱

- H.264(Baseline, Main 및 High Profile)
- MPEG-1(MPEG-PS 포함)
- MPEG-2(Simple, Main Profile 및 4:2:2 Profile)
- MPEG-4 v2(Simple Visual Profile 및 Advanced Simple Profile)
- VC-1(Simple, Main 및 Advanced Profile)
- Windows Media 비디오(Simple, Main 및 Advanced Profile)
- DV(DVC, DVHD, DVSD, DVSL)
- Grass Valley HQ/HQX
 
###오디오 코덱

- AC-3(Dolby Digital 오디오)
- AAC(AAC-LC, HE-AAC v1 AAC-LC 코어 및 HE-AAC v2 AAC-LC 코어)
- MP3
- Windows Media 오디오 9(Windows Media 오디오 Standard, Windows Media 오디오 Professional 및 Windows Media 오디오 무손실)

###비디오 파일 형식
 
<table border="1">
<tr><th>파일 형식</th><th>파일 확장명</th></tr>
<tr><td>3GPP, 3GPP2</td><td>.3gp, .3g2, .3gp2</td></tr>
<tr><td>ASF(Advanced Systems Format)</td><td>.asf</td></tr>
<tr><td>AVCHD(Advanced Video Coding High Definition) [MPEG-2 전송 스트림]</td><td>.mts, .m2ts</td></tr>
<tr><td>AVI(Audio-Video Interleaved)</td><td>.avi</td></tr>
<tr><td>Digital camcorder MPEG-2(MOD)</td><td>.mod</td></tr>
<tr><td>DVD TS(전송 스트림) 파일</td><td>.ts</td></tr>
<tr><td>DVD VOB(비디오 개체) 파일</td><td>.vob</td></tr>
<tr><td>Expression Encoder Screen Capture 코덱 파일</td><td>.xesc</td></tr>
<tr><td>MP4</td><td>.mp4</td></tr>
<tr><td>MPEG-1 시스템 스트림</td><td>.mpeg, .mpg</td></tr>
<tr><td>MPEG-2 비디오 파일</td><td>.m2v</td></tr>
<tr><td>부드러운 스트리밍 파일 형식(PIFF 1.3)</td><td>.ismv</td></tr>
<tr><td>WMV(Windows Media 비디오)</td><td>.wmv</td></tr>
</table>

일부 압축되지 않은 형식이 지원됩니다. 자세한 내용은 [지원되는 압축되지 않은 비디오 형식]을 참조하세요.(#uncompressed)

###오디오 파일 형식

<table border="1">
<tr><th>파일 형식</th><th>파일 확장명</th></tr>
<tr><td>AC-3(Dolby Digital) 오디오</td><td>.ac3</td></tr>
<tr><td>AIFF(Audio Interchange File Format)</td><td>.aiff</td></tr>
<tr><td>브로드캐스트 웨이브 형식</td><td>.bwf</td></tr>
<tr><td>MP3(MPEG-1 Audio Layer 3)</td><td>.mp3</td></tr>
<tr><td>MP4 오디오</td><td>.m4A</td></tr>
<tr><td>MPEG-4 오디오 책</td><td>.m4b</td></tr>
<tr><td>WAVE 파일</td><td>.wav</td></tr>
<tr><td>Windows Media 오디오</td><td>.wma</td></tr>   
</table>

###이미지 파일 형식

<table border="1">
<tr><th>파일 형식</th><th>파일 확장명</th></tr>
<tr><td>비트맵</td><td>.bmp</td></tr>
<tr><td>GIF, 애니메이션 GIF</td><td>.gif</td></tr>
<tr><td>JPEG</td><td>.jpeg, .jpg</td></tr>
<tr><td>PNG</td><td>.png</td></tr>
<tr><td>TIFF</td><td>.tif</td></tr>
<tr><td>WPF 캔버스 XAML</td><td>.xaml</td></tr>
</table>


##<a id="export_formats"></a>미디어 인코더 내보내기 형식

다음 표에는 내보내기에 지원되는 코덱 및 파일 형식이 나열되어 있습니다.


<table border="1">
<tr><th>파일 형식</th><th>비디오 코덱</th><th>오디오 코덱</th></tr>
<tr><td>Windows Media(*.wmv; *.wma)</td><td>VC-1(Advanced, Main 및 Simple Profiles)</td><td>Windows Media 오디오 Standard, Windows Media 오디오 Professional, Windows Media 오디오 음성, Windows Media 오디오 무손실</td></tr>
<tr><td>MP4(*.mp4)</td><td>H.264(High, Main 및 Baseline Profiles)</td><td>AAC-LC, HE-AAC v1, HE-AAC v2, Dolby Digital Plus</td></tr>
<tr><td>부드러운 스트리밍 파일 형식(PIFF 1.1)(*.ismv; *.isma)</td><td>VC-1(Advanced Profile)<br/><br/>
H.264(High, Main 및 Baseline Profiles)</td><td>Windows Media 오디오 Standard, Windows Media 오디오 Professional<br/><br/>
AAC-LC, HE-AAC v1, HE-AAC v2</td></tr>
</table>

미디어 서비스에서 지원되는 추가 코덱 및 파일은 [Windows DirectShow 필터](https://msdn.microsoft.com/library/windows/desktop/dd375464.aspx)를 참조하세요.

##<a id="uncompressed"></a>지원되는 압축되지 않은 비디오 형식 

Azure 미디어 서비스는 압축되지 않은 비디오 데이터 가져오기를 지원합니다.

다음은 지원되는 압축되지 않은 형식의 일부 목록입니다.

<table border="1">
<tr><th>압축되지 않은 비디오 형식</th><th>설명</th></tr>
<tr><td>표준 YVU9 형식의 압축되지 않은 데이터</td><td>평면 YUV 형식입니다. 픽셀마다 Y 샘플이 있고 각 줄의 가로 방향으로 네 번째 픽셀마다 U 및 V 샘플이 있으며, 세로 줄마다 Y 샘플이 있고 네 번째 세로 줄마다 U 및 V 샘플이 있습니다. 픽셀당 9비트입니다.</td></tr>
<tr><td>YUV 411 형식 데이터</td><td>픽셀마다 Y 샘플이 있고 각 줄의 가로 방향으로 네 번째 픽셀마다 U 및 V 샘플이 있으며 모든 세로 줄이 샘플링됩니다. 바이트 순서(가장 낮은 바이트 먼저)는 U0, Y0, V0, Y1, U4, Y2, V4, Y3, Y4, Y5, Y6, Y7입니다. 여기서 접미사 0은 맨 왼쪽 픽셀이고 증가하는 숫자는 왼쪽에서 오른쪽으로 증가하는 픽셀입니다. 각 12바이트 블록은 8개의 이미지 픽셀입니다.</td></tr>
<tr><td>Y41P 형식 데이터</td><td>픽셀마다 Y 샘플이 있고 각 줄의 가로 방향으로 네 번째 픽셀마다 U 및 V 샘플이 있으며 모든 세로 줄이 샘플링됩니다. 바이트 순서(가장 낮은 바이트 먼저)는 U0, Y0, V0, Y1, U4, Y2, V4, Y3, Y4, Y5, Y6, Y7입니다. 여기서 접미사 0은 맨 왼쪽 픽셀이고 증가하는 숫자는 왼쪽에서 오른쪽으로 증가하는 픽셀입니다. 각 12바이트 블록은 8개의 이미지 픽셀입니다.</td></tr>
<tr><td>YUY2 형식 데이터</td><td>UYVY와 동일하지만 픽셀 순서가 다릅니다. 바이트 순서(가장 낮은 바이트 먼저)는 Y0, U0, Y1, V0, Y2, U2, Y3, V2, Y4, U4, Y5, V4입니다. 여기서 접미사 0은 맨 왼쪽 픽셀이고 증가하는 숫자는 왼쪽에서 오른쪽으로 증가하는 픽셀입니다. 각 4바이트 블록은 2개의 이미지 픽셀입니다.</td></tr>
<tr><td>YVYU 형식 데이터</td><td>압축된 YUV 형식입니다. UYVY와 동일하지만 픽셀 순서가 다릅니다. 바이트 순서(가장 낮은 바이트 먼저)는 Y0, V0, Y1, U0, Y2, V2, Y3, U2, Y4, V4, Y5, U4입니다. 여기서 접미사 0은 맨 왼쪽 픽셀이고 증가하는 숫자는 왼쪽에서 오른쪽으로 증가하는 픽셀입니다. 각 4바이트 블록은 2개의 이미지 픽셀입니다.</td></tr>
<tr><td>UYVY 형식 데이터</td><td>압축된 YUV 형식입니다. 픽셀마다 Y 샘플이 있고 각 줄의 가로 방향으로 두 번째 픽셀마다 U 및 V 샘플이 있으며 모든 세로 줄이 샘플링됩니다. 가장 많이 사용되는 다양한 YUV 4:2:2 형식입니다. 바이트 순서(가장 낮은 바이트 먼저)는 U0, Y0, V0, Y1, U2, Y2, V2, Y3, U4, Y4, V4, Y5입니다. 여기서 접미사 0은 맨 왼쪽 픽셀이고 증가하는 숫자는 왼쪽에서 오른쪽으로 증가하는 픽셀입니다. 각 4바이트 블록은 2개의 이미지 픽셀입니다.</td></tr>
<tr><td>YUV 211 형식 데이터</td><td>압축된 YUV 형식입니다. 두 번째 픽셀마다 Y 샘플이 있고 각 줄의 가로 방향으로 네 번째 픽셀마다 U 및 V 샘플이 있으며 모든 세로 줄이 샘플링됩니다. 바이트 순서(가장 낮은 바이트 먼저)는 Y0, U0, Y2, V0, Y4, U4, Y6, V4, Y8, U8, Y10, V8입니다. 여기서 접미사 0은 맨 왼쪽 픽셀이고 증가하는 숫자는 왼쪽에서 오른쪽으로 증가하는 픽셀입니다. 각 4바이트 블록은 4개의 이미지 픽셀입니다.</td></tr>
<tr><td>Cirrus Logic Jr YUV 411 형식</td><td>Y, U 및 V 샘플당 비트 수가 8보다 작은 Cirrus Logic Jr YUV 411 형식입니다. 픽셀마다 Y 샘플이 있고 각 줄의 가로 방향으로 네 번째 픽셀마다 U 및 V 샘플이 있으며 모든 세로 줄이 샘플링됩니다.</td></tr>
<tr><td>Indeo 생성 YVU9 형식</td><td>마지막 프레임과의 차이점에 대한 추가 정보가 있는 Indeo 생성 YVU9 형식입니다. 픽셀당 9.5비트이지만 9로 보고됩니다.</td></tr>
</table>


<!--HONumber=52--> 