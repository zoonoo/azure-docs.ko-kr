<properties 
	pageTitle="미디어 인코더 Premium 워크플로 형식 및 코덱" 
	description="이 항목에서는 미디어 인코더 Premium 워크플로 형식 및 코덱에 대한 개요를 제공합니다." 
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
	ms.date="06/29/2015" 
	ms.author="juliako"/>

#미디어 인코더 Premium 워크플로 형식 및 코덱


**참고** 중국에서는 이 항목에서 설명하는 미디어 인코더 Premium 워크플로 미디어 프로세서를 사용할 수 없습니다.

이 문서에는 **미디어 인코더 Premium 워크플로** 인코더의 공개 미리 보기 버전에서 지원하는 입력 및 출력 파일 형식과 코덱 목록이 포함되어 있습니다.

[미디어 인코더 Premium 워크플로 입력 형식 및 코덱](#input_formats)

[미디어 인코더 Premium 워크플로 출력 형식 및 코덱](#output_formats)

**미디어 인코더 Premium 워크플로**는 [이](#closed_captioning) 섹션에 설명된 선택 캡션을 지원합니다.


##<a id="input_formats"></a>미디어 인코더 Premium 워크플로 입력 형식 및 코덱

다음 섹션에는 이 미디어 프로세서에서 입력으로 지원되는 코덱 및 파일 형식이 나열되어 있습니다.

###입력 컨테이너/파일 형식

- Adobe® Flash® F4V
- MXF/SMPTE 377M
- GXF
- MPEG-2 전송 스트림
- MPEG-2 프로그램 스트림
- MPEG-4/MP4
- Windows Media/ASF
- AVI(압축되지 않은 8비트/10비트)

###입력 비디오 코덱

- AVC 8비트/10비트, 최대 4:2:2, AVCIntra 포함
- Avid DNxHD(MXF)
- DVCPro/DVCProHD(MXF)
- JPEG2000
- MPEG-2(최대 422 프로필 및 높은 수준, XDCAM, XDCAM HD, XDCAM IMX, CableLabs® 및 D10과 같은 변형 포함)
- MPEG-1
- Windows Media 비디오/VC-1

###입력 오디오 코덱

- AES(SMPTE 331M 및 302M, AES3-2003)
- Dolby® E
- Dolby® Digital(AC3)
- AAC(AAC-LC, AAC-HE 및 AAC-HEv2, 최대 5.1)
- MPEG Layer 2
- MP3(MPEG-1 Audio Layer 3)
- Windows Media 오디오
- WAV/PCM
 
##<a id="output_format"></a>미디어 인코더 Premium 워크플로 출력 형식 및 코덱

다음 섹션에는 이 미디어 프로세서에서 출력으로 지원되는 코덱 및 파일 형식이 나열되어 있습니다.

###출력 컨테이너/파일 형식

- Adobe® Flash® F4V
- MXF(OP1a, XDCAM 및 AS02)
- DPP(AS11 포함)
- GXF
- MPEG-4/MP4
- Windows Media/ASF
- AVI(압축되지 않은 8비트/10비트)
- 부드러운 스트리밍 파일 형식(PIFF 1.3)
- MPEG-TS 


###출력 비디오 코덱

- AVC(H.264, 8비트, 최대 High Profile, 수준 5.2, 4K Ultra HD, AVC Intra)
- Avid DNxHD(MXF)
- DVCPro/DVCProHD(MXF)
- MPEG-2(최대 422 프로필 및 높은 수준, XDCAM, XDCAM HD, XDCAM IMX, CableLabs® 및 D10과 같은 변형 포함)
- MPEG-1
- Windows Media 비디오/VC-1
- JPEG 축소판 그림 만들기

###출력 오디오 코덱

- AES(SMPTE 331M 및 302M, AES3-2003)
- Dolby® Digital(AC3)
- Dolby® Digital Plus(E-AC3) 최대 7.1
- AAC(AAC-LC, AAC-HE 및 AAC-HEv2, 최대 5.1)
- MPEG Layer 2
- MP3(MPEG-1 Audio Layer 3)
- Windows Media 오디오

##<a id="closed_captioning"></a>선택 캡션 지원

수집 시 **미디어 인코더 Premium 워크플로**는 다음을 지원합니다.

1. SCC 파일
1. SMPTE-TT 파일
1. CEA-608/CEA-708 – 사용자 데이터에 수반됨(H.264 기초 스트림, ATSC/53, SCTE20의 SEI 메시지) 또는 MXF/GXF 파일의 보조 데이터에 수반됨
1. STL 자막 파일

출력 시 다음 옵션을 사용할 수 있습니다.

1. CEA 608을 CEA 708로 변환
1. CEA-608/CEA-708 통과(H.264 기본 스트림의 SEI 메시지에 포함되거나 MXF 파일의 보조 데이터로 전달됨)
1. SCC
1. SMPTE 시간 제한 텍스트(SMPTE RP2052당 소스 CEA 608, DFXP 파일 만들기 포함)
1. SRT 자막 파일
1. DVB 자막 스트림

참고: 위의 출력 형식 중 일부는 Azure 미디어 서비스에서 스트리밍을 통해 배달되지 않습니다.

##알려진 문제

입력된 비디오에 자막이 없는 경우, 출력 자산은 빈 TTML 파일을 포함합니다.

<!---HONumber=July15_HO2-->