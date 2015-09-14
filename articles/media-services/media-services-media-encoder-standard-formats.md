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
	ms.date="08/11/2015"
	ms.author="juliako"/>

#미디어 인코더 표준 형식 및 코덱


이 문서에는 미디어 인코더 표준에서 사용할 수 있는 가장 일반적인 가져오기 및 내보내기 파일 형식 목록이 포함되어 있습니다.


##입력 컨테이너/파일 형식

파일 형식(파일 확장명)|지원됨
---|---|---|---
FLV(H.264 및 AAC 코덱 포함)(.flv) |예 
MXF(.mxf) |예 
GXF(.gxf) |예 
MPEG2 PS, MPEG2-TS 3GP(.ts, .ps, .3gp) |예 
WMV(Windows Media Video)/ASF(.wmv, .asf) |예 
AVI(압축되지 않은 8비트/10비트)(.avi)|예 
MP4/ISMV(.ismv)|예 
[DVR-MS(Microsoft Digital Video Recording)](https://msdn.microsoft.com/library/windows/desktop/dd692984)(.dvr-ms) |예 
Matroska/WebM(.mkv) |예 
WAVE/WAV(.wav) |예 
 

##입력 비디오 코덱

입력 비디오 코덱|지원됨
---|---|---|---
AVC 8비트/10비트, 최대 4:2:2, AVCIntra 포함 |8비트 4:2:0 및 4:2:2 
Avid DNxHD(MXF) |예 
DVCPro/DVCProHD(MXF) |예 
JPEG 2000 |예 
MPEG-2(최대 422 프로필 및 높은 수준, XDCAM, XDCAM HD, XDCAM IMX, CableLabs® 및 D10과 같은 변형 포함)|최대 422 프로필 
MPEG-1 |예 
VC-1/WMV9 |예 
Canopus HQ/HQX |아니요 
Mpeg-4 2부 |예 
[Theora](https://en.wikipedia.org/wiki/Theora) |예 
압축되지 않은 YUV420 또는 mezzanine |예


##입력 오디오 코덱

입력 오디오 코덱|지원됨
---|---|---|---
AAC(AAC-LC, AAC-HE 및 AAC-HEv2, 최대 5.1)|예 
MPEG Layer 2|예 
MP3(MPEG-1 Audio Layer 3)|예 
Windows Media 오디오|예 
WAV/PCM|예 
[FLAC](https://en.wikipedia.org/wiki/FLAC)</a>|예 
[Opus](https://en.wikipedia.org/wiki/Opus_(audio_format) |예 
[Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a>|예 
AMR(Adaptive Multi-Rate)|예
AES(SMPTE 331M 및 302M, AES3-2003) |아니요 
Dolby® E |아니요 
Dolby® Digital(AC3) |아니요 
Dolby® Digital Plus(E-AC3) |아니요 


##출력 형식 및 코덱

다음 표에는 내보내기에 지원되는 코덱 및 파일 형식이 나열되어 있습니다.


파일 형식|비디오 코덱|오디오 코덱
---|---|---
MP4(* .mp4)<br/><br/>(다중 비트 전송률 MP4 컨테이너 포함) |H.264(High, Main 및 Baseline Profiles)|AAC-LC, HE-AAC v1, HE-AAC v2 
MPEG2-TS |H.264(High, Main 및 Baseline Profiles)|AAC-LC, HE-AAC v1, HE-AAC v2 

##참고 항목

[Azure 미디어 서비스로 주문형 콘텐츠 인코딩](media-services-encode-asset.md)

[미디어 인코더 표준으로 인코딩하는 방법](media-services-dotnet-encode-with-media-encoder-standard.md)

<!---HONumber=September15_HO1-->