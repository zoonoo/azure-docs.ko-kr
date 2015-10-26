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
	ms.date="09/05/2015" 
	ms.author="juliako"/>

#Azure 미디어 인코더 형식 및 코덱

이 문서에는 Azure 미디어 인코더에서 사용할 수 있는 가장 일반적인 입력 및 출력 파일 형식 목록이 포함되어 있습니다.


##입력 파일 형식(컨테이너)
 
파일 형식(파일 확장명)|지원됨
---|---
3GPP, 3GPP2(.3gp, .3g2, .3gp2) |예
ASF(Advanced Systems Format)(.asf) |예
AVCHD(Advanced Video Coding High Definition) [MPEG-2 전송 스트림](.mts, .m2ts) |예
AVI(Audio-Video Interleaved)(.avi) |예
Digital camcorder MPEG-2(MOD)(.mod) |예
DVD TS(전송 스트림) 파일(.ts) |예
DVD VOB(비디오 개체) 파일(.vob) |예
Expression Encoder Screen Capture 코덱 파일(.xesc) |예
MP4(.mp4, .m4a, .m4v)/ISMV(.isma, .ismv) |예
MPEG-1 시스템 스트림(.mpeg, .mpg) |예
MPEG-2 비디오 파일(.m2v) |예
WMV(Windows Media 비디오)(.wmv) |예
AC-3(Dolby Digital) 오디오(.ac3)|예
AIFF(Audio Interchange File Format)(.aiff)|예
브로드캐스트 웨이브 형식(.bwf)|예
MP3(MPEG-1 Audio Layer 3)(.mp3)|예
MPEG-4 오디오 책(.m4b)|예
WAVE 파일(.wav)|예
Windows Media 오디오(.wma)|예
Adobe® Flash® F4V |아니요		
MXF/SMPTE 377M |제한 
GXF |아니요		 
[DVR-MS(Microsoft Digital Video Recording)](https://msdn.microsoft.com/library/windows/desktop/dd692984)|아니요
Matroska/WebM |아니요


일부 압축되지 않은 형식이 지원됩니다. 자세한 내용은 [지원되는 압축되지 않은 비디오 형식](#uncompressed)을 참조하세요.

##입력 비디오 코덱

입력 비디오 코덱|지원됨
---|--- 
H.264(Baseline, Main 및 High Profile) |예
AVC 8비트/10비트, 최대 4:2:2, AVCIntra 포함 |8비트 4:2:0만
Avid DNxHD(MXF) |아니요
DVCPro/DVCProHD(MXF) |아니요
JPEG2000 |아니요
MPEG-2(Simple, Main Profile 및 4:2:2 Profile) |최대 4:2:2 프로필
MPEG-1(MPEG-PS 포함) |예
Windows Media 비디오/VC-1 |예
Canopus HQ/HQX |예
MPEG-4 v2(Simple Visual Profile 및 Advanced Simple Profile) |예
[Theora](https://en.wikipedia.org/wiki/Theora) |아니요
VC-1(Simple, Main 및 Advanced Profile) |예
Windows Media 비디오(Simple, Main 및 Advanced Profile) |예
DV(DVC, DVHD, DVSD, DVSL) |예
Grass Valley HQ/HQX |예
 

##입력 오디오 코덱

입력 오디오 코덱|지원됨
---|---
AES(SMPTE 331M 및 302M, AES3-2003) |아니요
Dolby® E |아니요
Dolby® Digital(AC3) |예
Dolby® Digital Plus(E-AC3) |아니요
AAC(AAC-LC, HE-AAC v1 AAC-LC 코어 및 HE-AAC v2 AAC-LC 코어, 최대 5.1)|예
MPEG Layer 2|예|예|예
MP3(MPEG-1 Audio Layer 3)|예
Windows Media 오디오 9(Windows Media 오디오 Standard, Windows Media 오디오 Professional 및 Windows Media 오디오 무손실) |예
WAV/PCM|예
[FLAC](https://en.wikipedia.org/wiki/FLAC)|아니요
[Opus](https://en.wikipedia.org/wiki/Opus_(audio_format) |아니요
[Vorbis](https://en.wikipedia.org/wiki/Vorbis)|아니요


##입력 이미지 파일 형식

파일 형식(파일 확장명) | 지원됨
---|---
비트맵(.bmp) | 예
GIF, 애니메이션 GIF(.gif)| 예
JPEG(.jpeg, .jpg)| 예
PNG(.png)| 예
TIFF(.tif)| 예
WPF 캔버스 XAML(.xaml)| 예


##출력 형식 및 코덱

다음 표에는 내보내기에 지원되는 코덱 및 파일 형식이 나열되어 있습니다.

파일 형식|비디오 코덱|오디오 코덱
---|---|---
Windows Media(*.wmv; *.wma)|VC-1(Advanced, Main 및 Simple Profiles)|Windows Media 오디오 Standard, Windows Media 오디오 Professional, Windows Media 오디오 음성, Windows Media 오디오 무손실
MP4(*.mp4)|H.264(High, Main 및 Baseline Profiles)|AAC-LC, HE-AAC v1, HE-AAC v2, Dolby Digital Plus
부드러운 스트리밍 파일 형식(PIFF 1.1)(*.ismv; *.isma)|VC-1(Advanced Profile)<p>H.264(High, Main, and Baseline Profiles) |Windows Media 오디오 Standard, Windows Media 오디오 Professional<p><p>AAC-LC, HE-AAC v1, HE-AAC v2

미디어 서비스에서 지원되는 추가 코덱 및 파일은 [Windows DirectShow 필터](https://msdn.microsoft.com/library/windows/desktop/dd375464.aspx)를 참조하세요.

##<a id="uncompressed"></a>지원되는 압축되지 않은 비디오 형식 

Azure 미디어 서비스는 압축되지 않은 비디오 데이터 가져오기를 지원합니다.

다음은 지원되는 압축되지 않은 형식의 일부 목록입니다.

압축되지 않은 비디오 형식|설명
---|---
표준 YVU9 형식의 압축되지 않은 데이터|평면 YUV 형식입니다. 픽셀마다 Y 샘플이 있고 각 줄의 가로 방향으로 네 번째 픽셀마다 U 및 V 샘플이 있으며, 세로 줄마다 Y 샘플이 있고 네 번째 세로 줄마다 U 및 V 샘플이 있습니다. 픽셀당 9비트입니다.
YUV 411 형식 데이터|픽셀마다 Y 샘플이 있고 각 줄의 가로 방향으로 네 번째 픽셀마다 U 및 V 샘플이 있으며 모든 세로 줄이 샘플링됩니다. 바이트 순서(가장 낮은 바이트 먼저)는 U0, Y0, V0, Y1, U4, Y2, V4, Y3, Y4, Y5, Y6, Y7입니다. 여기서 접미사 0은 맨 왼쪽 픽셀이고 증가하는 숫자는 왼쪽에서 오른쪽으로 증가하는 픽셀입니다. 각 12바이트 블록은 8개의 이미지 픽셀입니다.
Y41P 형식 데이터|픽셀마다 Y 샘플이 있고 각 줄의 가로 방향으로 네 번째 픽셀마다 U 및 V 샘플이 있으며 모든 세로 줄이 샘플링됩니다. 바이트 순서(가장 낮은 바이트 먼저)는 U0, Y0, V0, Y1, U4, Y2, V4, Y3, Y4, Y5, Y6, Y7입니다. 여기서 접미사 0은 맨 왼쪽 픽셀이고 증가하는 숫자는 왼쪽에서 오른쪽으로 증가하는 픽셀입니다. 각 12바이트 블록은 8개의 이미지 픽셀입니다.
YUY2 형식 데이터|UYVY와 동일하지만 픽셀 순서가 다릅니다. 바이트 순서(가장 낮은 바이트 먼저)는 Y0, U0, Y1, V0, Y2, U2, Y3, V2, Y4, U4, Y5, V4입니다. 여기서 접미사 0은 맨 왼쪽 픽셀이고 증가하는 숫자는 왼쪽에서 오른쪽으로 증가하는 픽셀입니다. 각 4바이트 블록은 2개의 이미지 픽셀입니다.
YVYU 형식 데이터|압축된 YUV 형식입니다. UYVY와 동일하지만 픽셀 순서가 다릅니다. 바이트 순서(가장 낮은 바이트 먼저)는 Y0, V0, Y1, U0, Y2, V2, Y3, U2, Y4, V4, Y5, U4입니다. 여기서 접미사 0은 맨 왼쪽 픽셀이고 증가하는 숫자는 왼쪽에서 오른쪽으로 증가하는 픽셀입니다. 각 4바이트 블록은 2개의 이미지 픽셀입니다.
UYVY 형식 데이터|압축된 YUV 형식입니다. 픽셀마다 Y 샘플이 있고 각 줄의 가로 방향으로 두 번째 픽셀마다 U 및 V 샘플이 있으며 모든 세로 줄이 샘플링됩니다. 가장 많이 사용되는 다양한 YUV 4:2:2 형식입니다. 바이트 순서(가장 낮은 바이트 먼저)는 U0, Y0, V0, Y1, U2, Y2, V2, Y3, U4, Y4, V4, Y5입니다. 여기서 접미사 0은 맨 왼쪽 픽셀이고 증가하는 숫자는 왼쪽에서 오른쪽으로 증가하는 픽셀입니다. 각 4바이트 블록은 2개의 이미지 픽셀입니다.
YUV 211 형식 데이터|압축된 YUV 형식입니다. 두 번째 픽셀마다 Y 샘플이 있고 각 줄의 가로 방향으로 네 번째 픽셀마다 U 및 V 샘플이 있으며 모든 세로 줄이 샘플링됩니다. 바이트 순서(가장 낮은 바이트 먼저)는 Y0, U0, Y2, V0, Y4, U4, Y6, V4, Y8, U8, Y10, V8입니다. 여기서 접미사 0은 맨 왼쪽 픽셀이고 증가하는 숫자는 왼쪽에서 오른쪽으로 증가하는 픽셀입니다. 각 4바이트 블록은 4개의 이미지 픽셀입니다.
Cirrus Logic Jr YUV 411 형식|Y, U 및 V 샘플당 비트 수가 8보다 작은 Cirrus Logic Jr YUV 411 형식입니다. 픽셀마다 Y 샘플이 있고 각 줄의 가로 방향으로 네 번째 픽셀마다 U 및 V 샘플이 있으며 모든 세로 줄이 샘플링됩니다.
Indeo 생성 YVU9 형식|마지막 프레임과의 차이점에 대한 추가 정보가 있는 Indeo 생성 YVU9 형식입니다. 픽셀당 9.5비트이지만 9로 보고됩니다.


##미디어 서비스 학습 경로

여기서 AMS 학습 경로를 볼 수 있습니다.

- [AMS 라이브 스트리밍 워크플로](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-live/)
- [AMS 주문형 스트리밍 워크플로](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-on-demand/)

<!---HONumber=Oct15_HO3-->