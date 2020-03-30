---
title: Azure 미디어 서비스 - 라이브 스트리밍에서 시간 별 메타데이터 시그널링
description: 이 사양은 Azure Media Services로 인과 스트리밍할 때 시간 제의 메타데이터를 시그널링하는 방법을 간략하게 설명합니다. 여기에는 일반 시간 제데이터 신호(ID3)에 대한 지원뿐만 아니라 광고 삽입 및 스플라이스 조건 시그널링을 위한 SCTE-35 시그널링이 포함됩니다.
services: media-services
documentationcenter: ''
author: johndeu
manager: femila
editor: johndeu
ms.assetid: 265b94b1-0fb8-493a-90ec-a4244f51ce85
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2019
ms.author: johndeu
ms.openlocfilehash: 551fb0cb9f3745a62d5d84f2c4878bbbbe5ad9a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79137325"
---
# <a name="signaling-timed-metadata-in-live-streaming"></a>라이브 스트리밍의 신호 타이밍 메타데이터 

최종 업데이트: 2019-08-22

### <a name="conformance-notation"></a>적합성 표기

이 문서의 키워드인 "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY" 및 "OPTIONAL"은 RFC 2119에서 설명한 대로 해석되어야 합니다.

## <a name="1-introduction"></a>1. 소개 

클라이언트 플레이어에 광고 또는 사용자 지정 메타데이터 이벤트를 삽입하는 신호를 표시하기 위해 브로드캐스터는 종종 비디오 내에 포함된 시간 제데이터를 사용합니다. 이러한 시나리오를 활성화하기 위해 Media Services는 라이브 스트리밍 채널의 수집 지점에서 클라이언트 응용 프로그램으로 시간 제의 메타데이터를 전송하는 지원을 제공합니다.
이 사양은 라이브 스트리밍 신호 내에서 시간 별 메타데이터에 대해 Media Services에서 지원하는 여러 모드를 간략하게 설명합니다.

1. [SCTE-35], [SCTE-214-1], [SCTE-214-3] 및 [RFC8216]에 의해 설명된 표준을 준수하는 신호

2. [SCTE-35] RTMP 광고 시그널링에 대한 레거시 [어도비 프라임타임] 사양을 준수하는 신호.
   
3. [SCTE-35]가 **아니며** [ID3v2] 또는 응용 프로그램 개발자가 정의한 다른 사용자 지정 스키마를 수행할 수 있는 메시지에 대한 일반 시간 제 메타데이터 시그널링 모드입니다.

## <a name="11-terms-used"></a>1.1 사용 약관

| 용어                | 정의                                                                                                                                                                                                                                    |
| ------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 광고 중단            | 하나 이상의 광고가 게재될 수 있는 위치 또는 지점 소용 및 배치 기회와 동일합니다.                                                                                                                     |
| 광고 의사 결정 서비스 | 어떤 광고와 기간을 사용자에게 표시할지 결정하는 외부 서비스입니다. 서비스는 일반적으로 파트너가 제공하며 이 문서의 범위를 벗어납니다.                                                                    |
| 큐                 | 예정된 광고 중단 시간 및 매개변수 표시입니다. 단서는 광고 중단으로 의한 보류 중인 스위치, 광고 중단 내의 다음 광고로의 보류 중인 전환, 광고 중단에서 주요 콘텐츠로의 보류 중인 전환을 나타낼 수 있습니다.           |
| 패키지 작성 도구            | Azure 미디어 서비스 "스트리밍 엔드포인트"는 DASH 및 HLS에 대한 동적 패키징 기능을 제공하며 미디어 업계에서 "패키지"라고 합니다.                                                                              |
| 프레젠테이션 시간   | 이벤트가 뷰어에 제공되는 시간입니다. 시간은 뷰어에 이벤트가 표시되는 미디어 타임라인의 순간을 나타냅니다. 예를 들어 SCTE-35 splice_info() 명령 메시지의 프레젠테이션 시간은 splice_time()입니다. |
| 도착 시간        | 이벤트 메시지가 도착하는 시간입니다. 이벤트 메시지가 이벤트의 프레젠테이션 시간에 앞서 보내지므로 이 시간은 일반적으로 이벤트의 프레젠테이션 시간과 구분됩니다.                                                    |
| 스파스 트랙        | 연속적이지 않으며, 부모 트랙 또는 컨트롤 트랙과 시간 동기화되는 미디어 트랙입니다.                                                                                                                                                  |
| 원본              | Azure 미디어 스트리밍 서비스                                                                                                                                                                                                             |
| 채널 싱크        | Azure 미디어 라이브 스트리밍 서비스                                                                                                                                                                                                        |
| HLS                 | Apple HTTP 라이브 스트리밍 프로토콜                                                                                                                                                                                                            |
| DASH                | HTTP를 통한 동적 적응 스트리밍                                                                                                                                                                                                          |
| Smooth              | 부드러운 스트리밍 프로토콜                                                                                                                                                                                                                     |
| MPEG2-TS            | MPEG-2 전송 스트림                                                                                                                                                                                                                      |
| RTMP                | 실시간 멀티미디어 프로토콜                                                                                                                                                                                                                 |
| uimsbf              | 부호 없는 정수, 가장 중요한 비트 우선                                                                                                                                                                                                 |

---

## <a name="12-normative-references"></a>1.2 규범 참조

다음 문서에는 이 문서의 참조를 통해 이 문서의 조항을 구성하는 조항이 포함되어 있습니다. 모든 문서는 표준 기관에 의해 개정될 수 있으며, 독자는 아래에 나열된 문서의 최신 버전을 적용 할 가능성을 조사하는 것이 좋습니다. 또한 참조된 문서의 최신 버전이 Azure Media Services에 대한 시간 적 메타데이터 사양의 이 버전과 호환되지 않을 수 있음을 알려줍니다.


| Standard          | 정의                                                                                                                                                                                                     |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [어도비 프라임타임] | [프라임타임 디지털 프로그램 삽입 시그널링 사양 1.2](https://www.adobe.com/content/dam/acom/en/devnet/primetime/PrimetimeDigitalProgramInsertionSignalingSpecification.pdf)                       |
| [어도비 플래시-AS]  | [플래시 액션스크립트 언어 참조](https://help.adobe.com/archive/en_US/as2/flashlite_2.x_3.x_aslr.pdf)                                                                                                   |
| [AMF0]            | ["작업 메시지 형식 AMF0"](https://download.macromedia.com/pub/labs/amf/amf0_spec_121207.pdf)                                                                                                              |
| [대시-IF-IOP]     | DASH 산업 포럼 인터롭 가이드 v 4.2[https://dashif-documents.azurewebsites.net/DASH-IF-IOP/master/DASH-IF-IOP.html](https://dashif-documents.azurewebsites.net/DASH-IF-IOP/master/DASH-IF-IOP.html)    |
| [HLS-TMD]         | HTTP 라이브 스트리밍을 위한 시간 제데이터 -[https://developer.apple.com/streaming](https://developer.apple.com/streaming)                                                                                        |
| [CMAF-ID3]        | [CMAF(공통 미디어 응용 프로그램 형식)의 시간 지정 메타데이터](https://github.com/AOMediaCodec/id3-emsg)                                                                                                        |
| [ID3v2]           | ID3 태그 버전 2.4.0[http://id3.org/id3v2.4.0-structure](http://id3.org/id3v2.4.0-structure)                                                                                                                |
| [ISO-14496-12]    | ISO/IEC 14496-12: 파트 12 ISO 베이스 미디어 파일 형식, 제4판 2012-07-15                                                                                                                                 |
| [MPEGDASH]        | 정보 기술 -- HTTP(DASH)를 통해 동적 적응형 스트리밍 -- 파트 1: 미디어 프레젠테이션 설명 및 세그먼트 형식. 2014년 5월. 게시. URL: https://www.iso.org/standard/65274.html         |
| [MPEGCMAF]        | 정보 기술 -- 멀티미디어 응용 프로그램 형식 (MPEG-A) -- 파트 19: 세분화 된 미디어에 대 한 일반적인 미디어 응용 프로그램 형식 (CMAF) . 2018년 1월. 게시. URL: https://www.iso.org/standard/71975.html |
| [MPEGCENC]        | 정보 기술 -- MPEG 시스템 기술 -- 파트 7: ISO 기본 미디어 파일 형식 파일의 일반적인 암호화. 2016년 2월. 게시. URL: https://www.iso.org/standard/68042.html                   |
| [MS-SSTR]         | ["마이크로소프트 부드러운 스트리밍 프로토콜", 2014년 5월 15일](https://docs.microsoft.com/openspecs/windows_protocols/ms-sstr/8383f27f-7efe-4c60-832a-387274457251)                                                     |
| [MS-SSTR-인제스트]  | [Azure 미디어 서비스 조각난 MP4 라이브 인제스트 사양](https://docs.microsoft.com/azure/media-services/media-services-fmp4-live-ingest-overview)                                                      |
| [RFC8216]         | 18. 판토, 에드; 더블유 5월. HTTP 라이브 스트리밍. 2017년 8월. 정보 제공용 메시지입니다. [https://tools.ietf.org/html/rfc8216](https://tools.ietf.org/html/rfc8216)                                                            |
| [RFC4648]         | Base16, Base32 및 Base64 데이터 인코딩 -[https://tools.ietf.org/html/rfc4648](https://tools.ietf.org/html/rfc4648)                                                                                     |
| [RTMP]            | ["Adobe의 실시간 메시징 프로토콜", 2012년 12월 21일](https://www.adobe.com/devnet/rtmp.html)                                                                                                            |
| [SCTE-35-2019]    | SCTE 35: 2019 - 케이블용 디지털 프로그램 삽입 큐잉 메시지 -https://www.scte.org/SCTEDocs/Standards/ANSI_SCTE%2035%202019r1.pdf                                                                       |
| [SCTE-214-1]      | SCTE 214-1 2016 – IP 기반 케이블 서비스 1부용 MPEG DASH: MPD 제약 조건 및 확장                                                                                                                 |
| [SCTE-214-3]      | SCTE 214-3 2015 IP 기반 케이블 서비스 부품 3에 대 한 MPEG 대시: 대시/FF 프로필                                                                                                                                  |
| [SCTE-224]        | SCTE 224 2018r1 – 이벤트 일정 및 알림 인터페이스                                                                                                                                                  |
| [SCTE-250]        | 이벤트 및 시그널링 관리 API(ESAM)                                                                                                                                                                      |

---


## <a name="2-timed-metadata-ingest"></a>2. 시간 적시 메타데이터 수집

Azure Media 서비스는 [RTMP] 및 부드러운 스트리밍 [MS-SSTR-Ingest] 프로토콜에 대해 실시간 대역 내 메타데이터를 지원합니다. 실시간 메타데이터는 고유한 사용자 지정 스키마(JSON, Binary, XML)를 사용하여 사용자 지정 이벤트를 정의하는 데 사용할 수 있을 뿐만 아니라 브로드캐스트 스트림에서 광고 시그널링을 위해 ID3 또는 SCTE-35와 같은 업계에서 정의된 형식을 사용할 수 있습니다. 

이 문서에서는 Azure Media Services의 지원되는 인제스트 프로토콜을 사용하여 사용자 지정 시간 지정 메타데이터 신호를 보내는 방법에 대한 세부 정보를 제공합니다. 또한 이 문서에서는 HLS, DASH 및 Smooth Streaming에 대한 매니페스트가 시간 표시 메타데이터 신호로 장식되는 방법과 HLS용 CMAF(MP4 조각) 또는 전송 스트림(TS) 세그먼트를 사용하여 콘텐츠가 전달될 때 대역 내 로 전송되는 방식에 대해서도 설명합니다. 

시간 제데이터에 대한 일반적인 사용 사례 시나리오는 다음과 같습니다.

 - SCTE-35 광고 신호로 실시간 이벤트 또는 선형 브로드캐스트에서 광고 중단 을 트리거
 - 클라이언트 응용 프로그램(브라우저, iOS 또는 Android)에서 이벤트를 트리거할 수 있는 사용자 지정 ID3 메타데이터
 - 클라이언트 응용 프로그램에서 이벤트를 트리거하기 위해 정의된 JSON, 바이너리 또는 XML 메타데이터
 - 라이브 인코더, IP 카메라 또는 드론의 원격 분석
 - 모션, 얼굴 감지 등과 같은 IP 카메라의 이벤트
 - 액션 카메라, 드론 또는 이동 장치의 지리적 위치 정보
 - 노래 가사
 - 선형 라이브 피드의 프로그램 경계
 - 라이브 피드에 표시할 이미지 또는 증강 메타데이터
 - 스포츠 점수 또는 게임 시계 정보
 - 브라우저의 비디오와 함께 표시되는 대화형 광고 패키지
 - 퀴즈 또는 설문 조사
  
Azure Media Services 라이브 이벤트 및 패키지러는 이러한 시간 별 메타데이터 신호를 수신하고 HLS 및 DASH와 같은 표준 기반 프로토콜을 사용하여 클라이언트 응용 프로그램에 도달할 수 있는 메타데이터 스트림으로 변환할 수 있습니다.


## <a name="21-rtmp-timed-metadata"></a>2.1 RTMP 시간 제데이터

[RTMP] 프로토콜을 사용하면 사용자 지정 메타데이터 및 SCTE-35 광고 신호를 비롯한 다양한 시나리오에 대해 시간 지정 메타데이터 신호를 전송할 수 있습니다. 

광고 신호(큐 메시지)는 [RTMP] 스트림 내에 포함된 [AMF0] 큐 메시지로 전송됩니다. 큐 메시지는 실제 이벤트 또는 [SCTE35] 광고 스플라이스 신호가 발생하기 전에 언젠가 전송될 수 있습니다. 이 시나리오를 지원하기 위해 이벤트의 실제 프레젠테이션 타임스탬프가 큐 메시지 내에서 전송됩니다. 자세한 내용은 [AMF0]을 참조하세요.

다음 [AMF0] 명령은 RTMP 인제스트에 대한 Azure 미디어 서비스에서 지원됩니다.

- **onUserDataEvent** - 사용자 지정 메타데이터 또는 [ID3v2] 시간 지정 메타데이터에 사용
- **onAdCue** - 주로 라이브 스트림에서 광고 배치 기회를 신호에 사용됩니다. 큐의 두 가지 형태가 지원됩니다, 간단한 모드와 "SCTE-35"모드. 
- **onCuePoint** - 정령 라이브 인코더와 같은 특정 온-프레미스 하드웨어 인코더에서 지원하여 [SCTE35] 메시지를 신호합니다. 
  

다음 표는 미디어 서비스가 "단순" 및 [SCTE35] 메시지 모드 모두에 대해 섭취할 AMF 메시지 페이로드의 형식을 설명합니다.

[AMF0] 메시지의 이름을 사용하여 동일한 유형의 여러 이벤트 스트림을 구분할 수 있습니다.  [SCTE-35] 메시지와 "단순" 모드 모두 AMF 메시지의 이름은 [Adobe-Primetime] 사양에서 요구하는 대로 "onAdCue"여야 합니다.  아래에 나열되지 않은 모든 필드는 인제스트 시 Azure 미디어 서비스에서 무시해야 합니다.

## <a name="211-rtmp-with-custom-metadata-using-onuserdataevent"></a>2.1.1 "onUserDataEvent"를 사용하여 사용자 정의 메타데이터가 있는 RTMP

RTMP 프로토콜을 사용하여 업스트림 인코더, IP 카메라, 드론 또는 장치에서 사용자 지정 메타데이터 피드를 제공하려면 "onUserDataEvent"[ [AMF0] 데이터 메시지 명령 유형을 사용합니다.

**"onUserDataEvent"** 데이터 메시지 명령은 미디어 서비스에서 캡처하고 대역 내 파일 형식뿐만 아니라 HLS, DASH 및 부드러운 스트리밍에 대한 매니페스트로 패키징될 다음 정의가 있는 메시지 페이로드를 수행해야 합니다.
0.5초(500ms)마다 한 번 이상 시간 초과 메타데이터 메시지를 보내지 않거나 라이브 스트림에 안정성 문제가 발생할 수 있습니다. 각 메시지는 프레임 수준 메타데이터를 제공해야 하는 경우 여러 프레임에서 메타데이터를 집계할 수 있습니다. 다중 비트 전송률 스트림을 보내는 경우 대역폭을 줄이고 비디오/오디오 처리에 대한 간섭을 피하기 위해 단일 비트 전송률에 메타데이터도 제공하는 것이 좋습니다. 

**"onUserDataEvent"에** 대한 페이로드는 [MPEGDASH] 이벤트스트림 XML 형식 메시지여야 합니다. 따라서 HLS 또는 DASH 프로토콜을 통해 제공되는 CMAF[MPEGCMAF] 콘텐츠에 대해 대역 내 'emsg' 페이로드로 전달할 수 있는 사용자 지정 정의 스키마를 쉽게 전달할 수 있습니다. 각 DASH 이벤트 스트림 메시지에는 URN 메시지 체계 식별자로 작동하며 메시지의 페이로드를 정의하는 schemeIdUri가 포함되어 있습니다. "[ID3v2]에 대한 "https://aomedia.org/emsg/ID3또는 **urn:scte:scte35:2013:bin** [SCTE-35]와 같은 일부 구성표는 상호 운용성을 위해 업계 컨소시엄에 의해 표준화됩니다. 모든 응용 프로그램 공급자는 제어하는 URL(소유 도메인)을 사용하여 고유한 사용자 지정 스키마를 정의할 수 있으며 원하는 경우 해당 URL에서 사양을 제공할 수 있습니다. 플레이어가 정의된 스키마에 대한 처리기가 있는 경우 페이로드 및 프로토콜을 이해해야 하는 유일한 구성 요소입니다.

[MPEG-DASH] EventStream XML 페이로드에 대한 스키마는 정의됩니다(DASH ISO-IEC-23009-1-3버전에서 발췌). 지금은 "EventStream"당 하나의 "이벤트 유형"만 지원됩니다. **EventStream**에서 여러 이벤트가 제공되는 경우 첫 번째 **이벤트** 요소만 처리됩니다.

```xml
  <!-- Event Stream -->
  <xs:complexType name="EventStreamType">
    <xs:sequence>
      <xs:element name="Event" type="EventType" minOccurs="0" maxOccurs="unbounded"/>
      <xs:any namespace="##other" processContents="lax" minOccurs="0" maxOccurs="unbounded"/>
    </xs:sequence>
    <xs:attribute ref="xlink:href"/>
    <xs:attribute ref="xlink:actuate" default="onRequest"/>
    <xs:attribute name="schemeIdUri" type="xs:anyURI" use="required"/>
    <xs:attribute name="value" type="xs:string"/>
    <xs:attribute name="timescale" type="xs:unsignedInt"/>
  </xs:complexType>
  <!-- Event  -->
  <xs:complexType name="EventType">
    <xs:sequence>
      <xs:any namespace="##other" processContents="lax" minOccurs="0" maxOccurs="unbounded"/>
    </xs:sequence>
    <xs:attribute name="presentationTime" type="xs:unsignedLong" default="0"/>
    <xs:attribute name="duration" type="xs:unsignedLong"/>
    <xs:attribute name="id" type="xs:unsignedInt"/>
    <xs:attribute name="contentEncoding" type="ContentEncodingType"/>
    <xs:attribute name="messageData" type="xs:string"/>
    <xs:anyAttribute namespace="##other" processContents="lax"/>
  </xs:complexType>
```


### <a name="example-xml-event-stream-with-id3-schema-id-and-base64-encoded-data-payload"></a>ID3 스키마 ID 및 base64 인코딩된 데이터 페이로드가 있는 XML 이벤트 스트림 예제입니다.  
```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <EventStream schemeIdUri="https://aomedia.org/emsg/ID3">
         <Event contentEncoding="Base64">
          -- base64 encoded ID3v2 full payload here per [CMAF-TMD] --
         </Event>
   <EventStream>
```

### <a name="example-event-stream-with-custom-schema-id-and-base64-encoded-binary-data"></a>사용자 지정 스키마 ID 및 base64 인코딩된 바이너리 데이터가 있는 이벤트 스트림 예제  
```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <EventStream schemeIdUri="urn:example.org:custom:binary">
         <Event contentEncoding="Base64">
          -- base64 encoded custom binary data message --
         </Event>
   <EventStream>
```

### <a name="example-event-stream-with-custom-schema-id-and-custom-json"></a>사용자 지정 스키마 ID 및 사용자 지정 JSON이 있는 이벤트 스트림 예제  
```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <EventStream schemeIdUri="urn:example.org:custom:JSON">
         <Event>
          [
            {"key1" : "value1"},
            {"key2" : "value2"}
          ]
         </Event>
   <EventStream>
```

### <a name="built-in-supported-scheme-id-uris"></a>기본 제공 지원 구성표 ID URI
| 구성표 ID URI                 | 설명                                                                                                                                                                                                                                          |
| ----------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| https:\//aomedia.org/emsg/ID3 | CMAF 호환 [MPEGCMAF] 조각화된 MP4에서 [ID3v2] 메타데이터를 시간 별 메타데이터로 수행할 수 있는 방법을 설명합니다. 자세한 내용은 [CMAF(공통 미디어 응용 프로그램 형식)의 시간 지정 메타데이터를](https://github.com/AOMediaCodec/id3-emsg) 참조하십시오. |

### <a name="event-processing-and-manifest-signaling"></a>이벤트 처리 및 매니페스트 시그널링

유효한 **"onUserDataEvent"** 이벤트를 받으면 Azure Media Services는 EventStreamType([MPEGDASH]에 정의됨)과 일치하는 유효한 XML 페이로드를 찾고 XML 페이로드를 구문 분석한 다음 라이브 아카이브에 저장하고 미디어 서비스 패키지로 전송하기 위해 [MPEGCMAF] MP4 조각 'emsg' 버전 1 상자로 변환합니다.   패키지는 라이브 스트림에서 'emsg'상자를 감지하고 다음을 수행합니다.

- (a) HLS 시간 제데이터 사양 [HLS-TMD]에 따라 HLS 클라이언트에 전달하기 위해 TS 세그먼트에 "동적으로 패키지"하거나
- (b) HLS 또는 DASH를 통해 CMAF 단편으로 전달하기 위해 통과하거나 
- (c) 스무스 스트리밍 [MS-SSTR]을 통해 전달을 위한 희소 트랙 신호로 변환합니다.

HLS용 인밴드 'emsg' 형식CMAF 또는 TS PES 패킷 외에도 DASH(MPD) 및 스무스 스트리밍에 대한 매니페스트에는 대역 내 이벤트 스트림(스무스 스트리밍의 스파스 스트림 트랙이라고도 함)에 대한 참조가 포함됩니다. 

개별 이벤트 또는 해당 데이터 페이로드는 HLS, DASH 또는 Smooth 매니페스트에서 직접 출력되지 않습니다. 

### <a name="additional-informational-constraints-and-defaults-for-onuserdataevent-events"></a>onUserDataEvent 이벤트에 대한 추가 정보 제약 조건 및 기본값

- EventStream 요소에서 시간 척도가 설정되지 않은 경우 RTMP 1 kHz 타임스케일이 기본적으로 사용됩니다.
- onUserDataEvent 메시지의 배달은 최대 500ms마다 한 번으로 제한됩니다. 이벤트를 더 자주 전송하는 경우 라이브 피드의 대역폭과 안정성에 영향을 줄 수 있습니다.

## <a name="212-rtmp-ad-cue-signaling-with-onadcue"></a>2.1.2 RTMP 광고 큐 신호 "onAdCue"

Azure Media Services는 라이브 스트림에서 다양한 실시간 동기화된 메타데이터를 신호하는 데 사용할 수 있는 여러 [AMF0] 메시지 유형을 수신하고 응답할 수 있습니다.  [Adobe-Primetime] 사양은 "단순" 및 "SCTE-35" 모드라는 두 가지 큐 유형을 정의합니다. "단순" 모드의 경우 Media Services는 "단순 모드" 신호에 대해 정의된 아래 표와 일치하는 페이로드를 사용하여 "onAdCue"라는 단일 AMF 큐 메시지를 지원합니다.  

다음 섹션에서는 HLS, DASH 및 Microsoft 스무스 스트리밍을 위해 클라이언트 매니페스트로 전달되는 기본 "스플라이스아웃" 광고 신호를 표시하는 데 사용할 수 있는 RTMP "단순" 모드 페이로드를 보여 줍니다. 이는 고객이 복잡한 SCTE-35 기반 광고 신호 배포 또는 삽입 시스템이 없고 기본 온-프레미스 인코더를 사용하여 API를 통해 큐 메시지를 보내는 시나리오에 매우 유용합니다. 일반적으로 온-프레미스 인코더는 REST 기반 API를 지원하여 이 신호를 트리거하며, 이 신호는 비디오에 IDR 프레임을 삽입하고 새 GOP를 시작하여 비디오 스트림을 "스플라이스 조건"으로 처리합니다.

## <a name="213--rtmp-ad-cue-signaling-with-onadcue---simple-mode"></a>2.1.3 RTMP 광고 큐 신호 "onAdCue" - 간단한 모드

| 필드 이름 | 필드 형식 | 필수 여부 | 설명                                                                                                                                                                                                                                                                        |
| ---------- | ---------- | --------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| type       | String     | 필수  | 이벤트 메시지입니다.  단순 모드 스플라이스를 지정하려면 "SpliceOut"이어야 합니다.                                                                                                                                                                                                         |
| id         | String     | 필수  | 스플라이스 또는 세그먼트를 설명하는 고유 식별자입니다. 메시지의 이 인스턴스를 식별합니다.                                                                                                                                                                                       |
| duration   | Number     | 필수  | 스플라이스의 재생 시간입니다. 단위는 소수 자릿수 초입니다.                                                                                                                                                                                                                           |
| elapsed    | Number     | Optional  | 선국(tune in)을 지원하기 위해 신호가 반복되는 경우. 이 필드는 스플라이스가 시작된 이후 경과한 프레젠테이션 시간의 양이어야 합니다. 단위는 소수 자릿수 초입니다. 단순 모드를 사용하는 경우, 이 값은 스플라이스의 원래 재생 시간을 초과하지 않아야 합니다. |
| time       | Number     | 필수  | 프레젠테이션 시간에 있는 스플라이스의 시간이어야 합니다. 단위는 소수 자릿수 초입니다.                                                                                                                                                                                                |

---
 
#### <a name="example-mpeg-dash-manifest-output-when-using-adobe-rtmp-simple-mode"></a>어도비 RTMP 단순 모드를 사용할 때 예 MPEG DASH 매니페스트 출력

Adobe 단순 모드를 사용하는 예제 [3.3.2.1 MPEG DASH .mpd 이벤트스트림](#3321-example-mpeg-dash-mpd-manifest-signaling-of-rtmp-streaming-using-adobe-simple-mode) 보기

단일 [기간 및 Adobe 단순 모드로 예제 3.3.3.1 DASH 매니페스트](#3331-example-mpeg-dash-manifest-mpd-with-single-period-eventstream-using-adobe-simple-mode-signals) 참조

#### <a name="example-hls-manifest-output-when-using-adobe-rtmp-simple-mode"></a>Adobe RTMP 단순 모드를 사용할 때 HLS 매니페스트 출력 예

Adobe [단순 모드 및 EXT-X-CUE 태그를 사용한 HLS 매니페스트 예 3.2.2 참조](#322-apple-hls-with-adobe-primetime-ext-x-cue-legacy)

## <a name="214-rtmp-ad-cue-signaling-with-onadcue---scte-35-mode"></a>2.1.4 RTMP 광고 큐 신호 "onAdCue" - SCTE-35 모드

전체 SCTE-35 페이로드 메시지를 HLS 또는 DASH 매니페스트로 전달해야 하는 고급 브로드캐스트 프로덕션 워크플로우로 작업하는 경우 [Adobe-Primetime] 사양의 "SCTE-35 모드"를 사용하는 것이 가장 좋습니다.  이 모드는 온-프레미스 라이브 인코더로 직접 전송되는 대역 내 SCTE-35 신호를 지원하며, 이 신호는 [Adobe-Primetime] 사양에 지정된 "SCTE-35 모드"를 사용하여 RTMP 스트림으로 신호를 인코딩합니다. 

일반적으로 SCTE-35 메시지는 온-프레미스 인코더의 MPEG-2 전송 스트림(TS) 입력에만 나타날 수 있습니다. SCTE-35가 포함된 전송 스트림 수집을 구성하고 Adobe SCTE-35 모드에서 RTMP로 통과하도록 설정하는 방법에 대한 자세한 내용은 인코더 제조업체에 문의하십시오.

이 시나리오에서는 **"onAdCue"[** [AMF0] 메시지 형식을 사용 하 여 온-프레미스 인코더에서 다음 페이로드를 보내야 합니다.

| 필드 이름 | 필드 형식 | 필수 여부 | 설명                                                                                                                                                                                                                                                                                                                                                                                                        |
| ---------- | ---------- | --------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| cue        | String     | 필수  | 이벤트 메시지입니다.  [SCTE-35] 메시지의 경우 HLS, Smooth 및 Dash 클라이언트로 메시지를 보내려면 base64 인코딩된 [RFC4648] 이진 splice_info_section()이어야 합니다.                                                                                                                                                                                                                               |
| type       | String     | 필수  | 메시지 구성표를 식별하는 URN 또는 URL입니다. [SCTE-35] 메시지의 경우 [Adobe-Primetime]을 준수하여 HLS, Smooth 및 Dash 클라이언트로 메시지를 보내려면 **"scte35"여야** **합니다.** 선택적으로, URN "urn:scte:scte35:2013:bin"은 또한 [SCTE-35] 메시지를 신호하는데 사용될 수 있다.                                                                                                        |
| id         | String     | 필수  | 스플라이스 또는 세그먼트를 설명하는 고유 식별자입니다. 메시지의 이 인스턴스를 식별합니다.  동일한 의미 체계를 갖는 메시지의 값은 동일해야 합니다.                                                                                                                                                                                                                                                       |
| duration   | Number     | 필수  | 알려진 경우 이벤트 또는 광고 스플라이스 세그먼트의 재생 시간입니다. 알 수 없는 경우 값은 **0이어야 합니다.**                                                                                                                                                                                                                                                                                                                    |
| elapsed    | Number     | Optional  | 선국(tune in)하기 위해 [SCTE-35] 광고 신호가 반복되는 경우. 이 필드는 스플라이스가 시작된 이후 경과한 프레젠테이션 시간의 양이어야 합니다. 단위는 소수 자릿수 초입니다. [SCTE-35] 모드에서 이 값은 스플라이스 또는 세그먼트의 지정된 원래 재생 시간을 초과할 수 있습니다.                                                                                                                   |
| time       | Number     | 필수  | 이벤트 또는 광고 스플라이스의 프레젠테이션 시간입니다.  프레젠테이션 시간과 기간은 [ISO-14496-12] 부속서 I에 정의된 대로 유형 1 또는 2의 SAP(스트림 액세스 포인트)와 **일치해야 합니다.** HLS 송신의 경우 시간과 지속 시간이 세그먼트 경계와 **일치해야 합니다.** 동일한 이벤트 스트림 내의 다른 이벤트 메시지에 대한 프레젠테이션 시간과 재생 시간은 겹치지 않아야 합니다. 단위는 소수 자릿수 초입니다. |

---

#### <a name="example-mpeg-dash-mpd-manifest-with-scte-35-mode"></a>SCTE-35 모드와 예 MPEG DASH .mpd 매니페스트
[SCTE-35를 가진 섹션 3.3.3.2 예제 DASH 매니페스트](#3332-example-mpeg-dash-manifest-mpd-with-multi-period-eventstream-using-adobe-scte35-mode-signaling) 참조

#### <a name="example-hls-manifest-m3u8-with-scte-35-mode-signal"></a>SCTE-35 모드 신호가 있는 HLS 매니페스트 .m3u8 예
[섹션 3.2.1.1 예 SCTE-35와 HLS 매니페스트](#3211-example-hls-manifest-m3u8-showing-ext-x-daterange-signaling-of-scte-35) 참조

## <a name="215-rtmp-ad-signaling-with-oncuepoint-for-elemental-live"></a>2.1.5 정령 라이브에 대한 "onCuePoint"와 RTMP 광고 신호

정령 라이브 온-프레미스 인코더는 RTMP 신호의 광고 마커를 지원합니다. Azure 미디어 서비스는 현재 RTMP에 대한 "onCuePoint" 광고 마커 유형만 지원합니다.  이는 **"ad_markers"을**"onCuePoint"로 설정하여 엘리멘탈 미디어 라이브 인코더 설정 또는 API의 Adobe RTMP 그룹 설정에서 활성화할 수 있습니다.  자세한 내용은 원소 라이브 문서를 참조하십시오. RTMP 그룹에서 이 기능을 사용하면 Azure Media 서비스에서 처리할 Adobe RTMP 출력에 SCTE-35 신호를 전달합니다.

"onCuePoint" 메시지 유형은 [Adobe-Flash-AS]에 정의되어 있으며 정령 라이브 RTMP 출력에서 전송될 때 다음과 같은 페이로드 구조를 가집니다.


| 속성   | 설명                                                                                                                                                                                                                     |
| ---------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| name       | 이름은 **'scte35'로**정령 라이브해야합니다.                                                                                                                                                                              |
| time       | 타임라인 중에 비디오 파일에서 큐 포인트가 발생한 시간(초)                                                                                                                                           |
| type       | 큐 포인트의 형식은 **"이벤트"로**설정해야 합니다.                                                                                                                                                                             |
| 매개 변수 | Id 및 DURATION을 포함하여 SCTE-35 메시지의 정보를 포함하는 이름/값 쌍 문자열의 연관 배열입니다. 이러한 값은 Azure Media Services에서 구문 분석되고 매니페스트 데코레이션 태그에 포함됩니다. |


이 광고 마커 모드를 사용하면 HLS 매니페스트 출력이 Adobe "단순" 모드와 유사합니다.


#### <a name="example-mpeg-dash-mpd-single-period-adobe-simple-mode-signals"></a>예 MPEG 대시 MPD, 단일 기간, 어도비 간단한 모드 신호

~~~ xml
<?xml version="1.0" encoding="utf-8"?>
<MPD xmlns="urn:mpeg:dash:schema:mpd:2011" 
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" profiles="urn:mpeg:dash:profile:isoff-live:2011" type="dynamic" publishTime="2020-01-07T18:58:03Z" minimumUpdatePeriod="PT0S" timeShiftBufferDepth="PT58M56S" availabilityStartTime="2020-01-07T17:44:47Z" minBufferTime="PT7S">
    <Period start="PT0S">
        <EventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="scte35" timescale="10000000">
            <Event presentationTime="1583497601000000" duration="300000000" id="1085900"/>
            <Event presentationTime="1583500901666666" duration="300000000" id="1415966"/>
            <Event presentationTime="1583504202333333" duration="300000000" id="1746033"/>
            <Event presentationTime="1583507502666666" duration="300000000" id="2076066"/>
            <Event presentationTime="1583510803333333" duration="300000000" id="2406133"/>
            <Event presentationTime="1583514104000000" duration="300000000" id="2736200"/>
            <Event presentationTime="1583517404666666" duration="300000000" id="3066266"/>
            <Event presentationTime="1583520705333333" duration="300000000" id="3396333"/>
            <Event presentationTime="1583524006000000" duration="300000000" id="3726400"/>
            <Event presentationTime="1583527306666666" duration="300000000" id="4056466"/>
            <Event presentationTime="1583530607333333" duration="300000000" id="4386533"/>
        </EventStream>
        <AdaptationSet id="1" group="1" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="video" mimeType="video/mp4" codecs="avc1.4D400C" maxWidth="256" maxHeight="144" startWithSAP="1">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="scte35"/>
            <SegmentTemplate timescale="10000000" presentationTimeOffset="1583486678426666" media="QualityLevels($Bandwidth$)/Fragments(video=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(video=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="1583495318000000" d="64000000" r="34"/>
                    <S d="43000000"/>
                    <S d="21000000"/>
                    <!-- ... Truncated for brevity of sample-->

                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="1583495318000000" type="0" wallClockTime="2020-01-07T17:59:10.957Z" presentationTime="1583495318000000"/>
            <Representation id="1_V_video_3750956353252827751" bandwidth="149952" width="256" height="144"/>
        </AdaptationSet>
        <AdaptationSet id="2" group="5" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="audio" mimeType="audio/mp4" codecs="mp4a.40.2" lang="en">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="scte35"/>
            <Label>ambient</Label>
            <SegmentTemplate timescale="10000000" presentationTimeOffset="1583486678426666" media="QualityLevels($Bandwidth$)/Fragments(ambient=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(ambient=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="1583495254426666" d="64000000" r="35"/>
                    <S d="43093334"/>
                    <S d="20906666"/>
                    <!-- ... Truncated for brevity of sample-->

                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="1583495254426666" type="0" wallClockTime="2020-01-07T17:59:04.600Z" presentationTime="1583495254426666"/>
            <Representation id="5_A_ambient_9125670592623055209" bandwidth="96000" audioSamplingRate="48000"/>
        </AdaptationSet>
    </Period>
</MPD>
~~~

#### <a name="example-hls-playlist-adobe-simple-mode-signals-using-ext-x-cue-tag-truncated--for-brevity"></a>예 HLS 재생 목록, EXT-X-CUE 태그를 사용하여 어도비 간단한 모드 신호 (잘린 "..." 간결함)

다음 예제에서는 Adobe "단순" 모드 신호와 레거시 [Adobe-Primetime] EXT-X-CUE 태그를 사용하여 RTMP 인제스트 스트림에 대한 미디어 서비스 동적 패키지의 출력을 보여 주었습니다.  

~~~
#EXTM3U
#EXT-X-VERSION:8
#EXT-X-MEDIA-SEQUENCE:0
#EXT-X-TARGETDURATION:7
#EXT-X-INDEPENDENT-SEGMENTS
#EXT-X-PROGRAM-DATE-TIME:2020-01-07T17:44:47Z
#EXTINF:6.400000,no-desc
Fragments(video=1583486742000000,format=m3u8-aapl-v8)
#EXTINF:6.400000,no-desc
Fragments(video=1583486806000000,format=m3u8-aapl-v8)
...
#EXTINF:6.166667,no-desc
Fragments(video=1583487638000000,format=m3u8-aapl-v8)
#EXT-X-CUE:ID=95766,TYPE="SpliceOut",DURATION=30.000000,TIME=158348769.966667
#EXTINF:0.233333,no-desc
Fragments(video=1583487699666666,format=m3u8-aapl-v8)
#EXT-X-CUE:ID=95766,TYPE="SpliceOut",DURATION=30.000000,TIME=158348769.966667,ELAPSED=0.233333
#EXTINF:6.400000,no-desc
Fragments(video=1583487702000000,format=m3u8-aapl-v8)
#EXT-X-CUE:ID=95766,TYPE="SpliceOut",DURATION=30.000000,TIME=158348769.966667,ELAPSED=6.633333
#EXTINF:6.400000,no-desc
Fragments(video=1583487766000000,format=m3u8-aapl-v8)
#EXT-X-CUE:ID=95766,TYPE="SpliceOut",DURATION=30.000000,TIME=158348769.966667,ELAPSED=13.033333
#EXTINF:6.400000,no-desc
Fragments(video=1583487830000000,format=m3u8-aapl-v8)
#EXT-X-CUE:ID=95766,TYPE="SpliceOut",DURATION=30.000000,TIME=158348769.966667,ELAPSED=19.433333
#EXTINF:6.400000,no-desc
Fragments(video=1583487894000000,format=m3u8-aapl-v8)
#EXT-X-CUE:ID=95766,TYPE="SpliceOut",DURATION=30.000000,TIME=158348769.966667,ELAPSED=25.833333
#EXTINF:4.166667,no-desc
Fragments(video=1583487958000000,format=m3u8-aapl-v8)
#EXTINF:2.233333,no-desc
Fragments(video=1583487999666666,format=m3u8-aapl-v8)
#EXTINF:6.400000,no-desc
Fragments(video=1583488022000000,format=m3u8-aapl-v8)
...

~~~

### <a name="216-cancellation-and-updates"></a>2.1.6 취소 및 업데이트

동일한 프레젠테이션 시간과 ID를 사용하여 여러 개의 메시지를 보내면 메시지를 취소하거나 업데이트할 수 있습니다. 프레젠테이션 시간과 ID는 이벤트를 고유하게 식별하며, 미리 받기 제약 조건을 충족하는 특정 프레젠테이션 시간 동안 받은 마지막 메시지는 처리되는 메시지입니다. 업데이트된 이벤트는 이전에 받은 메시지를 대체합니다. 미리 받기 제약 조건은 4초입니다. 프레젠테이션 시간보다 최소 4초 전에 받은 메시지가 처리됩니다.

## <a name="22-fragmented-mp4-ingest-smooth-streaming"></a>2.2 조각난 MP4 수집(부드러운 스트리밍)

라이브 스트림 인제스트에 대한 요구 사항은 [MS-SSTR-Ingest]를 참조하십시오. 다음 섹션에서는 타이밍 프레젠테이션 메타데이터의 수집에 대한 세부 정보를 제공합니다.  시간 표시 프레젠테이션 메타데이터는 라이브 서버 매니페스트 상자(MS-SSTR 참조)와 무비 박스('moov')에 정의되어 있는 스파스 트랙으로 수집됩니다.  

각 스파스 조각은 영화 조각 상자('moof')와 미디어 데이터 상자('mdat')로 구성되며, 여기서 'mdat' 상자는 이진 메시지입니다.

광고의 프레임 정확한 삽입을 달성하기 위해 인코더는 큐를 삽입해야 하는 프레젠테이션 시간에 조각을 분할해야 합니다.  [ISO-14496-12] 부속서 I에 정의된 대로 새로 생성된 IDR 프레임 또는 유형 1 또는 2의 SAP(스트림 액세스 포인트)로 시작하는 새 조각을 만들어야 합니다. 이를 통해 Azure Media Packager는 HLS 매니페스트와 새 기간이 프레임 정확한 스플라이스 컨디셔닝된 프레젠테이션 시간에 시작되는 DASH 다기간 매니페스트를 적절하게 생성할 수 있습니다.

### <a name="221-live-server-manifest-box"></a>2.2.1 라이브 서버 매니페스트 상자

스파스 트랙은 ** \<텍스트\> 스트림** 항목이 있는 라이브 서버 매니페스트 상자에 **선언되어야** 하며 다음 특성이 설정되어 **있어야 합니다.**

| **특성 이름** | **필드 유형** | **필수?** | **설명**                                                                                                                                                                                                              |
| ------------------ | -------------- | ------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| systemBitrate      | Number         | 필수      | 알 수 없는 가변 비트레이트트랙을 나타내는 "0"이어야 **합니다.**                                                                                                                                                          |
| parentTrackName    | String         | 필수      | 스파스 트랙 시간 코드가 시간 척도가 정렬된 상위 트랙의 **이름이어야 합니다.** 부모 트랙은 스파스 트랙이 될 수 없습니다.                                                                             |
| manifestOutput     | 부울        | 필수      | 스파스 트랙이 Smooth 클라이언트 매니페스트에 포함되었음을 나타내기 위해 "true"여야 **합니다.**                                                                                                                        |
| Subtype            | String         | 필수      | 네 문자 코드 "DATA"여야 **합니다.**                                                                                                                                                                                  |
| 구성표             | String         | 필수      | 메시지 구성표를 식별하는 URN 또는 URL이어야 **합니다.** [SCTE-35] 메시지의 경우 [SCTE-35]를 준수하는 HLS, Smooth 및 Dash 클라이언트로 메시지를 보내려면 "urn:scte:scte:scte:2013:bin"이어야 **합니다.** |
| trackName          | String         | 필수      | 스파스 트랙의 **이름이어야 합니다.** trackName은 동일한 구성표로 여러 이벤트 스트림을 구분하는 데 사용할 수 있습니다. 각 고유 이벤트 스트림에는 고유한 트랙 이름이 **있어야 합니다.**                                |
| timescale          | Number         | Optional      | 상위 트랙의 시간 척도여야 **합니다.**                                                                                                                                                                               |

---

### <a name="222-movie-box"></a>2.2.2 동영상 상자

무비 박스('moov')는 스파스 트랙의 스트림 헤더의 일부로 라이브 서버 매니페스트 상자를 따릅니다.

'moov' 상자에는 [ISO-14496-12]에 정의된 **트랙헤더박스('tkhd')** 상자가 **포함되어야 합니다.**

| **필드 이름** | **필드 유형**          | **필수?** | **설명**                                                                                                    |
| -------------- | ----------------------- | ------------- | ------------------------------------------------------------------------------------------------------------------ |
| duration       | 부호 없는 64비트 정수 | 필수      | **SHOULD** 트랙 박스에는 샘플이 0이고 트랙 상자의 샘플의 총 지속 시간이 0이기 때문에 0이어야합니다. |

---

'moov' 상자에는 다음과 같은 제약 조건이 있는 [ISO-14496-12]에 정의된 **핸들러박스('hdlr')가** **포함되어야** 합니다.

| **필드 이름** | **필드 유형**          | **필수?** | **설명**       |
| -------------- | ----------------------- | ------------- | --------------------- |
| handler_type   | 부호 없는 32비트 정수 | 필수      | **'메타'여야 합니다.** |

---

'stsd' 상자에는 [ISO-14496-12]에 정의된 코딩 이름이 있는 MetaDataSampleEntry 상자가 **포함되어야 합니다.**  예를 들어 SCTE-35 메시지의 경우 코딩 이름은 'scte'여야 **합니다.**

### <a name="223-movie-fragment-box-and-media-data-box"></a>2.2.3 동영상 조각 상자 및 미디어 데이터 상자

스파스 트랙 조각은 동영상 조각 상자('moof')와 미디어 데이터 상자('mdat')로 구성됩니다.

> [!NOTE]
> 광고의 프레임 정확한 삽입을 달성하기 위해 인코더는 큐를 삽입해야 하는 프레젠테이션 시간에 조각을 분할해야 합니다.  [ISO-14496-12] 부속서 I에 정의된 대로 새로 생성된 IDR 프레임 또는 유형 1 또는 2의 SAP(스트림 액세스 포인트)로 시작하는 새 조각을 만들어야 합니다.
> 

무비프래그박스('moof') 상자에는 다음 필드가 있는 [MS-SSTR]에 정의된 **트랙프래더확장헤더더그박스('uuid')** 상자가 **포함되어야 합니다.**

| **필드 이름**         | **필드 유형**          | **필수?** | **설명**                                                                                           |
| ---------------------- | ----------------------- | ------------- | --------------------------------------------------------------------------------------------------------- |
| fragment_absolute_time | 부호 없는 64비트 정수 | 필수      | 이벤트의 도착 시간이어야 **합니다.** 이 값은 메시지를 부모 트랙과 맞춥니다.           |
| fragment_duration      | 부호 없는 64비트 정수 | 필수      | 이벤트 기간이어야 **합니다.** 재생 시간을 알 수 없다는 것을 나타내기 위해 재생 시간이 0이 될 수 있습니다. |

---


MediaDataBox('mdat') 상자에는 다음 형식이 **있어야 합니다.**

| **필드 이름**          | **필드 유형**                   | **필수?** | **설명**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| ----------------------- | -------------------------------- | ------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 버전                 | 부호 없는 32비트 정수(uimsbf) | 필수      | 'mdat' 상자의 내용 형식을 결정합니다. 인식할 수 없는 버전은 무시됩니다. 현재 지원되는 유일한 버전은 1입니다.                                                                                                                                                                                                                                                                                                                                                                      |
| id                      | 부호 없는 32비트 정수(uimsbf) | 필수      | 메시지의 이 인스턴스를 식별합니다. 동일한 의미 체계를 갖는 메시지의 값은 동일해야 합니다. 즉, ID가 동일한 이벤트 메시지 상자 하나만 처리하면 됩니다.                                                                                                                                                                                                                                                                                                                            |
| presentation_time_delta | 부호 없는 32비트 정수(uimsbf) | 필수      | 트랙프래더디드헤더박스에 지정된 fragment_absolute_time presentation_time_delta 이벤트의 프레젠테이션 시간이어야 **합니다.** 프레젠테이션 시간과 기간은 [ISO-14496-12] 부속서 I에 정의된 대로 유형 1 또는 2의 SAP(스트림 액세스 포인트)와 **일치해야 합니다.** HLS 송신의 경우 시간과 지속 시간이 세그먼트 경계와 **일치해야 합니다.** 동일한 이벤트 스트림 내에서 서로 다른 이벤트 메시지의 프레젠테이션 시간과 기간은 겹치지 **않아야 합니다.** |
| message                 | 바이트 배열                       | 필수      | 이벤트 메시지입니다. [SCTE-35] 메시지의 경우 메시지는 이진 splice_info_section()입니다. [SCTE-35] 메시지의 경우 [SCTE-35]를 준수하는 HLS, Smooth 및 Dash 클라이언트로 메시지를 보내려면 이 메시지가 splice_info_section()여야 **합니다.** [SCTE-35] 메시지의 경우 이진 splice_info_section()은 'mdat' 상자의 페이로드이며 base64가 **인코딩되지 않습니다.**                                                                                                                     |

---


### <a name="224-cancellation-and-updates"></a>2.2.4 취소 및 업데이트

동일한 프레젠테이션 시간과 ID를 사용하여 여러 개의 메시지를 보내면 메시지를 취소하거나 업데이트할 수 있습니다.  프레젠테이션 시간과 ID는 이벤트를 고유하게 식별합니다. 미리 받기 제약 조건을 충족하는 특정 프레젠테이션 시간 동안 받은 마지막 메시지는 처리되는 메시지입니다. 업데이트된 메시지는 이전에 받은 메시지를 대체합니다.  미리 받기 제약 조건은 4초입니다. 프레젠테이션 시간보다 최소 4초 전에 받은 메시지가 처리됩니다. 


## <a name="3-timed-metadata-delivery"></a>3 타이밍 메타데이터 배달

이벤트 스트림 데이터는 Media Services에 불투명합니다. Media Services는 수집 엔드포인트와 클라이언트 엔드포인트 간에 세 가지 정보만 전달합니다. 다음 속성은 [SCTE-35] 및/또는 클라이언트의 스트리밍 프로토콜을 준수하여 클라이언트에 전달됩니다.

1.  구성표 - 메시지 구성표를 식별하는 URN 또는 URL입니다.
2.  프레젠테이션 시간 - 미디어 타임라인의 이벤트에 대한 프레젠테이션 시간입니다.
3.  재생 시간 - 이벤트의 재생 시간입니다.
4.  ID – 이벤트에 대한 선택적 고유 식별자입니다.
5.  메시지 - 이벤트 데이터입니다.

## <a name="31-microsoft-smooth-streaming-manifest"></a>3.1 마이크로 소프트 스트리밍 매니페스트  

스파스 메시지 트랙의 포맷 방법에 대한 자세한 내용은 스파스 트랙 핸들링 [MS-SSTR]을 참조하십시오. [SCTE35] 메시지의 경우 부드러운 스트리밍은 base64 인코딩된 splice_info_section()를 스파스 조각으로 출력합니다.
StreamIndex에는 "DATA"의 하위 유형이 **있어야** 하며 사용자 지정 속성에는 이름="스키마" 및 값="urn:scte:scte35:2013:bin"이 있는 특성이 **포함되어야 합니다.**

#### <a name="smooth-client-manifest-example-showing-base64-encoded-scte35-splice_info_section"></a>base64 인코딩 [SCTE35] splice_info_section()
~~~ xml
<?xml version="1.0" encoding="utf-8"?>
<SmoothStreamingMedia MajorVersion="2" MinorVersion="0" TimeScale="10000000" IsLive="true" Duration="0"
  LookAheadFragmentCount="2" DVRWindowLength="6000000000">

  <StreamIndex Type="video" Name="video" Subtype="" Chunks="0" TimeScale="10000000"
    Url="QualityLevels({bitrate})/Fragments(video={start time})">
    <QualityLevel Index="0" Bitrate="230000"
      CodecPrivateData="250000010FC3460B50878A0B5821FF878780490800704704DC0000010E5A67F840" FourCC="WVC1"
      MaxWidth="364" MaxHeight="272"/>
    <QualityLevel Index="1" Bitrate="305000"
      CodecPrivateData="250000010FC3480B50878A0B5821FF87878049080894E4A7640000010E5A67F840" FourCC="WVC1"
      MaxWidth="364" MaxHeight="272"/>
    <c t="0" d="20000000" r="300" />
  </StreamIndex>
  <StreamIndex Type="audio" Name="audio" Subtype="" Chunks="0" TimeScale="10000000"
    Url="QualityLevels({bitrate})/Fragments(audio={start time})">
    <QualityLevel Index="0" Bitrate="96000" CodecPrivateData="1000030000000000000000000000E00042C0"
      FourCC="WMAP" AudioTag="354" Channels="2" SamplingRate="44100" BitsPerSample="16" PacketSize="4459"/>
    <c t="0" d="20000000" r="300" />
  </StreamIndex>
  <StreamIndex Type="text" Name="scte35-sparse-stream" Subtype="DATA" Chunks="0" TimeScale="10000000"
    ParentStreamIndex="video" ManifestOutput="true" 
    Url="QualityLevels({bitrate})/Fragments(captions={start time})">
    <QualityLevel Index="0" Bitrate="0" CodecPrivateData="" FourCC="">
      <CustomAttributes>
        <Attribute Name="Scheme" Value="urn:scte:scte35:2013:bin"/>
      </CustomAttributes>
    </QualityLevel>
    <!-- The following <c> and <f> fragments contains the base64-encoded [SCTE35] splice_info_section() message -->
    <c t="600000000" d="300000000">    <f>PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0idXRmLTgiPz48QWNxdWlyZWRTaWduYWwgeG1sbnM9InVybjpjYWJsZWxhYnM6bWQ6eHNkOnNpZ25hbGluZzozLjAiIGFjcXVpc2l0aW9uUG9pbnRJZGVudGl0eT0iRVNQTl9FYXN0X0FjcXVpc2l0aW9uX1BvaW50XzEiIGFjcXVpc2l0aW9uU2lnbmFsSUQ9IjRBNkE5NEVFLTYyRkExMUUxQjFDQTg4MkY0ODI0MDE5QiIgYWNxdWlzaXRpb25UaW1lPSIyMDEyLTA5LTE4VDEwOjE0OjI2WiI+PFVUQ1BvaW50IHV0Y1BvaW50PSIyMDEyLTA5LTE4VDEwOjE0OjM0WiIvPjxTQ1RFMzVQb2ludERlc2NyaXB0b3Igc3BsaWNlQ29tbWFuZFR5cGU9IjUiPjxTcGxpY2VJbnNlcnQgc3BsaWNlRXZlbnRJRD0iMzQ0NTY4NjkxIiBvdXRPZk5ldHdvcmtJbmRpY2F0b3I9InRydWUiIHVuaXF1ZVByb2dyYW1JRD0iNTUzNTUiIGR1cmF0aW9uPSJQVDFNMFMiIGF2YWlsTnVtPSIxIiBhdmFpbHNFeHBlY3RlZD0iMTAiLz48L1NDVEUzNVBvaW50RGVzY3JpcHRvcj48U3RyZWFtVGltZXM+PFN0cmVhbVRpbWUgdGltZVR5cGU9IkhTUyIgdGltZVZhbHVlPSI1MTUwMDAwMDAwMDAiLz48L1N0cmVhbVRpbWVzPjwvQWNxdWlyZWRTaWduYWw+</f>
    </c>
    <c t="1200000000" d="400000000">      <f>PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0idXRmLTgiPz48QWNxdWlyZWRTaWduYWwgeG1sbnM9InVybjpjYWJsZWxhYnM6bWQ6eHNkOnNpZ25hbGluZzozLjAiIGFjcXVpc2l0aW9uUG9pbnRJZGVudGl0eT0iRVNQTl9FYXN0X0FjcXVpc2l0aW9uX1BvaW50XzEiIGFjcXVpc2l0aW9uU2lnbmFsSUQ9IjRBNkE5NEVFLTYyRkExMUUxQjFDQTg4MkY0ODI0MDE5QiIgYWNxdWlzaXRpb25UaW1lPSIyMDEyLTA5LTE4VDEwOjE0OjI2WiI+PFVUQ1BvaW50IHV0Y1BvaW50PSIyMDEyLTA5LTE4VDEwOjE0OjM0WiIvPjxTQ1RFMzVQb2ludERlc2NyaXB0b3Igc3BsaWNlQ29tbWFuZFR5cGU9IjUiPjxTcGxpY2VJbnNlcnQgc3BsaWNlRXZlbnRJRD0iMzQ0NTY4NjkxIiBvdXRPZk5ldHdvcmtJbmRpY2F0b3I9InRydWUiIHVuaXF1ZVByb2dyYW1JRD0iNTUzNTUiIGR1cmF0aW9uPSJQVDFNMFMiIGF2YWlsTnVtPSIxIiBhdmFpbHNFeHBlY3RlZD0iMTAiLz48L1NDVEUzNVBvaW50RGVzY3JpcHRvcj48U3RyZWFtVGltZXM+PFN0cmVhbVRpbWUgdGltZVR5cGU9IkhTUyIgdGltZVZhbHVlPSI1MTYyMDAwMDAwMDAiLz48L1N0cmVhbVRpbWVzPjwvQWNxdWlyZWRTaWduYWw+</f>
    </c>
  </StreamIndex>
</SmoothStreamingMedia>
~~~

## <a name="32-apple-hls-manifest-decoration"></a>3.2 애플 HLS 매니페스트 장식

Azure Media Services는 라이브 또는 온디맨드 이벤트 중에 광고 사용 정보를 알리기 위한 다음 HLS 매니페스트 태그를 지원합니다. 

- 애플 HLS에 정의된 EXT-X-DATERANGE [RFC8216]
- [어도비 프라임타임]에 정의된 EXT-X-CUE-이 모드는 "레거시"로 간주됩니다. 고객은 가능하면 EXT-X-DATERANGE 태그를 채택해야 합니다.

각 태그에 대한 데이터 출력은 사용되는 인제스트 신호 모드에 따라 달라집니다. 예를 들어 Adobe Simple 모드의 RTMP 인제스트는 전체 SCTE-35 base64 인코딩페이로드를 포함하지 않습니다.

## <a name="321-apple-hls-with-ext-x-daterange-recommended"></a>3.2.1 EXT-X-DATERANGE가있는 애플 HLS (권장)

Apple HTTP 라이브 스트리밍 [RFC8216] 사양은 [SCTE-35] 메시지의 시그널링을 허용합니다. 메시지는 [RFC8216] 섹션당 EXT-X-DATERANGE 태그의 세그먼트 재생 목록에 삽입됩니다."SCTE-35를 EXT-X-DATERANGE로 매핑".  클라이언트 응용 프로그램 계층은 M3U 재생 목록을 구문 분석하고 M3U 태그를 처리하거나 Apple 플레이어 프레임워크를 통해 이벤트를 수신할 수 있습니다.  

Azure 미디어 서비스(버전 3 API)에서 **권장되는** 방법은 [RFC8216]을 따르고 매니페스트에서 [SCTE35] 광고 사용 예용 장식에 대한 EXT-X_DATERANGE 태그를 사용하는 것입니다.

## <a name="3211-example-hls-manifest-m3u8-showing-ext-x-daterange-signaling-of-scte-35"></a>3.2.1.1 예 HLS 매니페스트 .m3u8 SCTE-35의 EXT-X-DATERANGE 신호

다음 예제 는 미디어 서비스 동적 패키지의 HLS 매니페스트 출력은 스트림의 SCTE-35 이벤트를 신호하는 [RFC8216]의 EXT-X-DATERANGE 태그의 사용을 보여줍니다. 또한 이 스트림에는 [어도비 프라임타임]에 대한 "레거시" EXT-X-CUE 태그가 포함되어 있습니다.

~~~
#EXTM3U
#EXT-X-VERSION:8
#EXT-X-MEDIA-SEQUENCE:0
#EXT-X-TARGETDURATION:2
#EXT-X-INDEPENDENT-SEGMENTS
#EXT-X-PROGRAM-DATE-TIME:2020-01-07T19:40:50Z
#EXTINF:1.501500,no-desc
Fragments(video=22567545,format=m3u8-aapl-v8)
#EXTINF:1.501500,no-desc
Fragments(video=22702680,format=m3u8-aapl-v8)
#EXTINF:1.501500,no-desc
Fragments(video=22837815,format=m3u8-aapl-v8)
#EXTINF:1.501500,no-desc
Fragments(video=22972950,format=m3u8-aapl-v8)
#EXTINF:1.501500,no-desc
Fragments(video=23108085,format=m3u8-aapl-v8)
#EXTINF:1.234567,no-desc
Fragments(video=23243220,format=m3u8-aapl-v8)
#EXTINF:0.016689,no-desc
Fragments(video=23354331,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=0.000022
#EXTINF:0.250244,no-desc
Fragments(video=23355833,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=0.250267
#EXTINF:0.850856,no-desc
Fragments(video=23378355,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=1.101122
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:10.610Z",SCTE35-IN=0xFC30200000000005DD00FFF00F05000003EA7F4FFE0165E4D3000101010000607CE85A
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=0.000000,TIME=260.610344,CUE="/DAgAAAAAAXdAP/wDwUAAAPqf0/+AWXk0wABAQEAAGB86Fo="
#EXTINF:0.650644,no-desc
Fragments(video=23454932,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=1.751767
#EXTINF:0.050044,no-desc
Fragments(video=23513490,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=1.801811
#EXTINF:1.451456,no-desc
Fragments(video=23517994,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=3.253267
#EXTINF:1.501500,no-desc
Fragments(video=23648625,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=4.754767
#EXTINF:1.501500,no-desc
Fragments(video=23783760,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=6.256267
#EXTINF:1.501500,no-desc
Fragments(video=23918895,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=7.757767
#EXTINF:1.501500,no-desc
Fragments(video=24054030,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=9.259267
#EXTINF:1.501500,no-desc
Fragments(video=24189165,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=10.760767
#EXTINF:1.501500,no-desc
Fragments(video=24324300,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=12.262267
#EXTINF:1.501500,no-desc
Fragments(video=24459435,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=13.763767
#EXTINF:1.501500,no-desc
Fragments(video=24594570,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=15.265267
#EXTINF:1.501500,no-desc
Fragments(video=24729705,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=16.766767
#EXTINF:1.501500,no-desc
Fragments(video=24864840,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=18.268267
#EXTINF:1.501500,no-desc
Fragments(video=24999975,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=19.769767
#EXTINF:1.501500,no-desc
Fragments(video=25135110,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=21.271267
#EXTINF:1.501500,no-desc
Fragments(video=25270245,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=22.772767
#EXTINF:1.501500,no-desc
Fragments(video=25405380,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=24.274267
#EXTINF:1.501500,no-desc
Fragments(video=25540515,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=25.775767
#EXTINF:1.501500,no-desc
Fragments(video=25675650,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=27.277267
#EXTINF:1.501500,no-desc
Fragments(video=25810785,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=28.778767
#EXTINF:1.501500,no-desc
Fragments(video=25945920,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=30.280267
#EXTINF:1.501500,no-desc
Fragments(video=26081055,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=31.781767
#EXTINF:1.501500,no-desc
Fragments(video=26216190,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=33.283267
#EXTINF:1.501500,no-desc
Fragments(video=26351325,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=34.784767
#EXTINF:1.501500,no-desc
Fragments(video=26486460,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=36.286267
#EXTINF:1.501500,no-desc
Fragments(video=26621595,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=37.787767
#EXTINF:1.501500,no-desc
Fragments(video=26756730,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=39.289267
#EXTINF:1.501500,no-desc
Fragments(video=26891865,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=40.790767
#EXTINF:1.501500,no-desc
Fragments(video=27027000,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=42.292267
#EXTINF:1.501500,no-desc
Fragments(video=27162135,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=43.793767
#EXTINF:1.501500,no-desc
Fragments(video=27297270,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=45.295267
#EXTINF:1.501500,no-desc
Fragments(video=27432405,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=46.796767
#EXTINF:1.501500,no-desc
Fragments(video=27567540,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=48.298267
#EXTINF:1.501500,no-desc
Fragments(video=27702675,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=49.799767
#EXTINF:1.501500,no-desc
Fragments(video=27837810,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=51.301267
#EXTINF:1.501500,no-desc
Fragments(video=27972945,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=52.802767
#EXTINF:1.501500,no-desc
Fragments(video=28108080,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=54.304267
#EXTINF:1.501500,no-desc
Fragments(video=28243215,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=55.805767
#EXTINF:1.501500,no-desc
Fragments(video=28378350,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=57.307267
#EXTINF:1.501500,no-desc
Fragments(video=28513485,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=58.808767
#EXTINF:1.501500,no-desc
Fragments(video=28648620,format=m3u8-aapl-v8)

~~~


## <a name="322-apple-hls-with-adobe-primetime-ext-x-cue-legacy"></a>어도비 프라임 타임 EXT-X-CUE와 3.2.2 애플 HLS (레거시)

또한 [어도비 프라임타임] "SCTE-35 모드"에 정의된 대로 EXT-X-CUE 태그를 사용하는 Azure Media 서비스(버전 2 및 3 API)에서 제공되는 "레거시" 구현도 있습니다. 이 모드에서 Azure 미디어 서비스는 EXT-X-CUE 태그에 base64 인코딩된 [SCTE-35] splice_info_section()를 포함합니다.  

"레거시" EXT-X-CUE 태그는 아래와 같이 정의되며 [Adobe-Primetime] 사양에서 규범적으로 참조될 수도 있습니다. 필요한 경우 레거시 SCTE35 시그널링에만 사용해야 하며, 그렇지 않으면 권장태그가 [RFC8216]에서 EXT-X-DATERANGE로 정의됩니다. 

| **특성 이름** | **유형**                      | **필수?**                             | **설명**                                                                                                                                                                                                                                                                          |
| ------------------ | ----------------------------- | ----------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| CUE                | 따옴표가 붙은 문자열                 | 필수                                  | [RFC4648]에 설명된 대로 base64 인코딩된 문자열로 인코딩된 메시지입니다. [SCTE-35] 메시지의 경우 이 splice_info_section()입니다.                                                                                                                                      |
| TYPE               | 따옴표가 붙은 문자열                 | 필수                                  | 메시지 구성표를 식별하는 URN 또는 URL입니다. [SCTE-35] 메시지의 경우 형식은 특수 값 "scte35"를 취합니다.                                                                                                                                                                          |
| ID                 | 따옴표가 붙은 문자열                 | 필수                                  | 이벤트에 대한 고유 식별자입니다. 메시지가 수집될 때 ID를 지정하지 않으면 Azure Media Services에서 고유 ID를 생성합니다.                                                                                                                                              |
| DURATION           | 10진수 부동 소수점 숫자 | 필수                                  | 이벤트의 재생 시간입니다. 알 수 없는 경우 값은 **0이어야 합니다.** 단위는 소수 자릿수 초입니다.                                                                                                                                                                                           |
| ELAPSED            | 10진수 부동 소수점 숫자 | 선택 사항이지만, 슬라이딩 시간 범위에 필수임 | 슬라이딩 프레젠테이션 창을 지원하기 위해 신호가 반복되는 경우 이 필드는 이벤트가 시작된 이후 경과된 프레젠테이션 시간의 양이어야 **합니다.** 단위는 소수 자릿수 초입니다. 이 값은 스플라이스 또는 세그먼트의 지정된 원래 재생 시간을 초과할 수 있습니다. |
| TIME               | 10진수 부동 소수점 숫자 | 필수                                  | 이벤트의 프레젠테이션 시간입니다. 단위는 소수 자릿수 초입니다.                                                                                                                                                                                                                        |


HLS 플레이어 애플리케이션 계층에서는 TYPE을 사용하여 메시지 형식을 식별하고, 메시지를 디코딩하며, 필요한 시간 변환을 적용하고, 이벤트를 처리합니다.  이벤트는 이벤트 타임스탬프에 따라 부모 트랙의 세그먼트 재생 목록에서 시간 동기화됩니다.  이러한 이벤트는 가장 가까운 세그먼트(#EXTINF 태그) 앞에 삽입됩니다.

### <a name="323-hls-m3u8-manifest-example-using-legacy-adobe-primetime-ext-x-cue"></a>3.2.3 HLS .m3u8 매니페스트 예는 "레거시" 어도비 프라임타임 EXT-X-CUE를 사용하여

다음 예제에서는 어도비 프라임타임 EXT-X-CUE 태그를 사용하여 HLS 매니페스트 장식을 보여 주실 수 있습니다.  "CUE" 매개 변수에는 TYPE 및 Duration 속성만 포함되어 있는데 이는 Adobe "단순" 모드 시그널링을 사용하는 RTMP 소스임을 의미합니다.  이것이 SCTE-35 모드 신호인 경우, 태그에는 [3.2.1.1 예에서](#3211-example-hls-manifest-m3u8-showing-ext-x-daterange-signaling-of-scte-35)볼 수 있듯이 base64 인코딩된 이진 SCTE-35 페이로드가 포함됩니다.

~~~
#EXTM3U
#EXT-X-VERSION:4
#EXT-X-PLAYLIST-TYPE:VOD
#EXT-X-ALLOW-CACHE:NO
#EXT-X-MEDIA-SEQUENCE:0
#EXT-X-TARGETDURATION:11
#EXT-X-PROGRAM-DATE-TIME:2019-12-10T09:18:14Z
#EXTINF:10.010000,no-desc
Fragments(video=4011540820,format=m3u8-aapl)
#EXTINF:10.010000,no-desc
Fragments(video=4011550830,format=m3u8-aapl)
#EXTINF:10.010000,no-desc
Fragments(video=4011560840,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000
#EXTINF:8.008000,no-desc
Fragments(video=4011570850,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=0.593000
#EXTINF:4.170000,no-desc
Fragments(video=4011578858,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=4.763000
#EXTINF:9.844000,no-desc
Fragments(video=4011583028,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=14.607000
#EXTINF:10.010000,no-desc
Fragments(video=4011592872,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=24.617000
#EXTINF:10.010000,no-desc
Fragments(video=4011602882,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=34.627000
#EXTINF:10.010000,no-desc
Fragments(video=4011612892,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=44.637000
#EXTINF:10.010000,no-desc
Fragments(video=4011622902,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=54.647000
#EXTINF:10.010000,no-desc
Fragments(video=4011632912,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=64.657000
#EXTINF:10.010000,no-desc
Fragments(video=4011642922,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=74.667000
#EXTINF:10.010000,no-desc
Fragments(video=4011652932,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=84.677000
#EXTINF:10.010000,no-desc
Fragments(video=4011662942,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=94.687000
#EXTINF:10.010000,no-desc
Fragments(video=4011672952,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=104.697000
#EXTINF:10.010000,no-desc
Fragments(video=4011682962,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=114.707000
#EXTINF:10.010000,no-desc
Fragments(video=4011692972,format=m3u8-aapl)
#EXTINF:8.008000,no-desc
Fragments(video=4011702982,format=m3u8-aapl)

~~~

### <a name="324-hls-message-handling-for-legacy-adobe-primetime-ext-x-cue"></a>3.2.4 "레거시" 어도비 프라임타임 EXT-X-CUE를 위한 HLS 메시지 처리

이벤트는 각 비디오 및 오디오 트랙의 세그먼트 재생 목록에서 신호를 받습니다. EXT-X-CUE 태그의 위치는 항상 첫 번째 HLS 세그먼트(스플라이스 아웃 또는 세그먼트 시작) 직전 또는 [Adobe-Primetime]에서 요구하는 시간 및 DURATION 속성이 참조하는 마지막 HLS 세그먼트(스플라이스 인 또는 세그먼트 끝)직후여야 **합니다.**

슬라이딩 프레젠테이션 창이 활성화되면 EXT-X-CUE 태그는 스플라이스 또는 세그먼트가 항상 세그먼트 재생 목록에 완전히 설명될 수 있을 정도로 자주 **반복되어야 하며,** [Adobe-Primetime]에서 요구하는 대로 스플라이스 또는 세그먼트가 활성화된 시간을 나타내기 위해 ELAPSED 특성을 사용해야 **합니다.**

슬라이딩 프레젠테이션 시간 범위를 사용하도록 설정되면, 참조하는 미디어 시간이 슬라이딩 프레젠테이션 시간 범위를 벗어나는 경우 EXT-X-CUE 태그가 세그먼트 재생 목록에서 제거됩니다.

## <a name="33-dash-manifest-decoration-mpd"></a>3.3 대시 매니페스트 장식 (MPD)

[MPEGDASH]는 이벤트를 신호하는 세 가지 방법을 제공합니다.

1.  MPD 이벤트 스트림에서 신호된 이벤트
2.  이벤트 메시지 상자('emsg')를 사용하여 대역 내 신호
3.  1과 2의 조합

MPD EventStream에서 신호가 있는 이벤트는 클라이언트가 MPD를 다운로드할 때 즉시 모든 이벤트에 액세스할 수 있기 때문에 VOD 스트리밍에 유용합니다. 다운스트림 SSAI 공급업체가 다중 기간 MPD 매니페스트에서 신호를 구문 분석하고 광고 콘텐츠를 동적으로 삽입해야 하는 SSAI 시그널링에도 유용합니다.  대역 내('emsg') 솔루션은 클라이언트가 MPD를 다시 다운로드할 필요가 없거나 클라이언트와 원본 간에 SSAI 매니페스트 조작이 발생하지 않는 라이브 스트리밍에 유용합니다. 

DASH에 대한 Azure 미디어 서비스 기본 동작은 MPD EventStream 및 이벤트 메시지 상자('emsg')를 사용하여 대역 내 모두 신호를 보내는 것입니다.

[RTMP] 또는 [MS-SSTR-Ingest] 위에 인스트된 큐 메시지는 대역 내 'emsg' 상자 및/또는 MPD 이벤트 스트림을 사용하여 DASH 이벤트에 매핑됩니다. 

DASH에 대한 대역 내 SCTE-35 시그널링은 [SCTE-214-3] 및 [DASH-IF-IOP] 섹션 13.12.2('SCTE35 이벤트')에 정의된 정의 및 요구 사항을 따릅니다. 

대역 내 [SCTE-35] 캐리지의 경우 이벤트 메시지 상자('emsg')는 schemeId = "urn:scte:scte35:2013:bin"을 사용합니다. MPD 매니페스트 장식의 경우 EventStream schemeId는 "urn:scte:scte35:2014:xml+bin"을 사용합니다.  이 형식은 인제스트에 도착한 전체 SCTE-35 메시지의 이진 base64 인코딩된 출력을 포함하는 이벤트의 XML 표현입니다. 

DASH에서 [SCTE-35] 큐 메시지의 지체에 대한 규범참조 정의는 [SCTE-214-1] 초 6.7.4(MPD) 및 [SCTE-214-3] 초 7.3.2(SCTE 35 큐 메시지 의 캐리지)에서 사용할 수 있습니다.

### <a name="331-mpeg-dash-mpd-eventstream-signaling"></a>3.3.1 MPEG 대시(MPD) 이벤트스트림 시그널링

이벤트의 매니페스트(MPD) 장식은 기간 요소 내에 나타나는 EventStream 요소를 사용하여 MPD에 신호를 보올 것입니다. 사용되는 schemeId는 "urn:scte:scte35:2014:xml+bin"입니다.

> [!NOTE]
> 간결한 목적을 위해 [SCTE-35]는 완전히 구문 분석된 큐 메시지의 캐리지 대신 Signal.Binary 요소(Signal.SpliceInfoSection 요소)의 base64 인코딩 섹션을 사용할 수 있습니다.
> Azure 미디어 서비스는 MPD 매니페스트에서 시그널링에 이 'xml+bin' 접근 방식을 사용합니다.
> 이것은 또한 [DASH-IF-IOP]에 사용되는 권장 되는 방법입니다 - [DASH IF IOP 지침의 '광고 삽입 이벤트 스트림'이라는](https://dashif-documents.azurewebsites.net/DASH-IF-IOP/master/DASH-IF-IOP.html#ads-insertion-event-streams) 제목의 섹션을 참조하십시오.
> 

EventStream 요소에 포함되는 특성은 다음과 같습니다.

| **특성 이름** | **유형**                | **필수?** | **설명**                                                                                                                                                                                                                                                                                                                                                                         |
| ------------------ | ----------------------- | ------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| scheme_id_uri      | 문자열                  | 필수      | 메시지의 구성표를 식별합니다. 이 구성표는 라이브 서버 매니페스트 상자에 있는 Scheme 특성의 값으로 설정됩니다. 값은 메시지 구성표를 식별하는 URN 또는 URL이어야 **합니다.** 지원되는 출력 체계Id는 서비스가 MPD의 간결성을 위해 현재 "xml+bin"만 지원하므로 [SCTE-214-1] 초 6.7.4(MPD)당 "urn:scte:scte:2014:xml+bin"이어야 합니다. |
| value              | 문자열                  | Optional      | 메시지의 의미 체계를 사용자 지정하기 위해 구성표 소유자가 추가로 사용하는 문자열 값입니다. 동일한 구성표로 여러 이벤트 스트림을 구분하려면 값을 이벤트 스트림의 이름([MS-SSTR-Ingest]의 트랙 이름 또는 [RTMP] 인제스트의 AMF 메시지 이름)으로 **설정해야 합니다.**                                                                         |
| 시간 간격          | 부호 없는 32비트 정수 | 필수      | 시간 척도(초당 틱)입니다.                                                                                                                                                                                                                                                                                                                                                     |


### <a name="332-example-event-streams-for-mpeg-dash"></a>3.3.2 MPEG DASH에 대한 예제 이벤트 스트림

#### <a name="3321-example-mpeg-dash-mpd-manifest-signaling-of-rtmp-streaming-using-adobe-simple-mode"></a>3.3.2.1 예 MPEG DASH .mpd 어도비 간단한 모드를 사용하여 RTMP 스트리밍의 매니페스트 신호

다음 예제에서는 Adobe "단순" 모드 시그널링을 사용하여 RTMP 스트림에 대한 미디어 서비스 동적 패키지러에서 발췌한 EventStream을 보여 주었습니다.

~~~ xml
<!-- Example EventStream element using "urn:com:adobe:dpi:simple:2015" Adobe simple signaling per [Adobe-Primetime] -->
    <EventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="simplesignal" timescale="10000000">
        <Event presentationTime="1583497601000000" duration="300000000" id="1085900"/>
        <Event presentationTime="1583500901666666" duration="300000000" id="1415966"/>
        <Event presentationTime="1583504202333333" duration="300000000" id="1746033"/>
        <Event presentationTime="1583507502666666" duration="300000000" id="2076066"/>
        <Event presentationTime="1583510803333333" duration="300000000" id="2406133"/>
        <Event presentationTime="1583514104000000" duration="300000000" id="2736200"/>
        <Event presentationTime="1583517404666666" duration="300000000" id="3066266"/>
        <Event presentationTime="1583520705333333" duration="300000000" id="3396333"/>
        <Event presentationTime="1583524006000000" duration="300000000" id="3726400"/>
        <Event presentationTime="1583527306666666" duration="300000000" id="4056466"/>
        <Event presentationTime="1583530607333333" duration="300000000" id="4386533"/>
    </EventStream>
~~~

#### <a name="3322-example-mpeg-dash-mpd-manifest-signaling-of-an-rtmp-stream-using-adobe-scte-35-mode"></a>3.3.2.2 예 MPEG DASH .mpd 매니페스트 어도비 SCTE-35 모드를 사용 하 여 RTMP 스트림의 매니페스트 신호

다음 예제에서는 Adobe SCTE-35 모드 시그널링을 사용하여 RTMP 스트림에 대한 미디어 서비스 동적 패키지러에서 발췌한 EventStream을 보여 주었습니다.

~~~ xml
<!-- Example EventStream element using xml+bin style signaling per [SCTE-214-1] -->

      <EventStream schemeIdUri="urn:scte:scte35:2014:xml+bin" value="scte35" timescale="10000000">
        <Event presentationTime="2595092444" duration="11011000" id="1002">
            <Signal xmlns="http://www.scte.org/schemas/35/2016">
                <Binary>/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==</Binary>
            </Signal>
        </Event>
        <Event presentationTime="2606103444" id="1002">
            <Signal xmlns="http://www.scte.org/schemas/35/2016">
                <Binary>/DAgAAAAAAXdAP/wDwUAAAPqf0/+AWXk0wABAQEAAGB86Fo=</Binary>
            </Signal>
        </Event>
      </EventStream>
~~~

> [!IMPORTANT]
> 프리젠 테이션시간은 메시지의 도착 시간이 아니라 기간 시작 시간을 기준으로 변환된 [SCTE-35] 이벤트의 프레젠테이션 시간입니다.
> [MPEGDASH]는 Event@presentationTime "기간의 시작을 기준으로 이벤트의 프레젠테이션 시간을 지정합니다.
> 프레젠테이션 시간(초)의 값은 이 특성의 값과 EventStream@timescale 특성 값의 분할입니다.
> 없는 경우 프레젠테이션 시간 값은 0입니다.

#### <a name="3331-example-mpeg-dash-manifest-mpd-with-single-period-eventstream-using-adobe-simple-mode-signals"></a>3.3.3.1 예 MPEG DASH 매니페스트 (MPD) 단일 기간, EventStream, 어도비 간단한 모드 신호를 사용 하 여

다음 예제에서는 Adobe "단순" 모드 광고 신호 방법을 사용하여 소스 RTMP 스트림에 대한 Media Services 동적 패키지의 출력을 보여 주습니다. 출력은 "urn:com:adobe:dpi:2015"로 설정된 schemeId Uri를 사용하여 EventStream을 표시하고 값 속성은 "simplesignal"으로 설정된 단일 기간 매니페스트입니다.
각 간단한 신호는 들어오는 단순 @presentationTime신호에 따라 채워지는 및 @duration속성과 @id 함께 이벤트 요소에 제공됩니다.

~~~ xml
<?xml version="1.0" encoding="utf-8"?>
<MPD xmlns="urn:mpeg:dash:schema:mpd:2011" 
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" profiles="urn:mpeg:dash:profile:isoff-live:2011" type="static" mediaPresentationDuration="PT28M1.680S" minBufferTime="PT3S">
    <Period>
        <EventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="simplesignal" timescale="1000">
            <Event presentationTime="4011578265" duration="119987" id="4011578265"/>
        </EventStream>
        <AdaptationSet id="1" group="1" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="video" mimeType="video/mp4" codecs="avc1.4D4028" maxWidth="1920" maxHeight="1080" startWithSAP="1">
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="simplesignal"/>
            <ProducerReferenceTime id="4011460740" type="0" wallClockTime="2020-01-25T19:35:54.740Z" presentationTime="4011460740"/>
            <SegmentTemplate timescale="1000" presentationTimeOffset="4011460740" media="QualityLevels($Bandwidth$)/Fragments(video=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(video=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="4011460740" d="2002" r="57"/>
                    <S d="1401"/>
                    <S d="601"/>
                    <S d="2002"/>

                     <!--> ... video segments truncated for sample brevity </-->

                </SegmentTimeline>
            </SegmentTemplate>
            <Representation id="1_V_video_14759481473095519504" bandwidth="6000000" width="1920" height="1080"/>
            <Representation id="1_V_video_1516803357996956148" bandwidth="4000000" codecs="avc1.4D401F" width="1280" height="720"/>
            <Representation id="1_V_video_5430608182379669372" bandwidth="2600000" codecs="avc1.4D401F" width="960" height="540"/>
            <Representation id="1_V_video_3780180650986497347" bandwidth="1000000" codecs="avc1.4D401E" width="640" height="360"/>
            <Representation id="1_V_video_13759117363700265707" bandwidth="699000" codecs="avc1.4D4015" width="480" height="270"/>
            <Representation id="1_V_video_6140004908920393176" bandwidth="400000" codecs="avc1.4D4015" width="480" height="270"/>
            <Representation id="1_V_video_10673801877453424365" bandwidth="200000" codecs="avc1.4D400D" width="320" height="180"/>
        </AdaptationSet>
        <AdaptationSet id="2" group="5" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="audio" mimeType="audio/mp4" codecs="mp4a.40.2">
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="simplesignal"/>
            <ProducerReferenceTime id="4011460761" type="0" wallClockTime="2020-01-25T19:35:54.761Z" presentationTime="4011460761"/>
            <Label>audio</Label>
            <SegmentTemplate timescale="1000" presentationTimeOffset="4011460740" media="QualityLevels($Bandwidth$)/Fragments(audio=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(audio=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="4011460761" d="1984"/>
                    <S d="2005" r="1"/>
                    <S d="2006"/>

                    <!--> ... audio segments truncated for example brevity </-->

                </SegmentTimeline>
            </SegmentTemplate>
            <Representation id="5_A_audio_17504386117102112482" bandwidth="128000" audioSamplingRate="48000"/>
        </AdaptationSet>
    </Period>
</MPD>

~~~

#### <a name="3332-example-mpeg-dash-manifest-mpd-with-multi-period-eventstream-using-adobe-scte35-mode-signaling"></a>3.3.3.2 예 MPEG DASH 매니페스트 (MPD) 다중 기간, 이벤트 스트림, 어도비 SCTE35 모드 시그널링을 사용 하 여

다음 예제에서는 Adobe SCTE35 모드 시그널링을 사용하여 소스 RTMP 스트림에 대한 미디어 서비스 동적 패키지의 출력을 보여 주며 있습니다.
이 경우 출력 매니페스트는 EventStream 요소가 있는 다중 기간 DASH.mpd이고 @schemeIdUri 속성은 "urn:scte:scte35:2014:2014:xml+bin"으로 설정되고 속성은 @value "scte35"로 설정됩니다. EventStream의 각 이벤트 요소에는 전체 base64 인코딩된 바이너리 SCTE35 신호가 포함되어 있습니다. 

~~~ xml
<?xml version="1.0" encoding="utf-8"?>
<MPD xmlns="urn:mpeg:dash:schema:mpd:2011" 
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" profiles="urn:mpeg:dash:profile:isoff-live:2011" type="dynamic" publishTime="2020-01-07T19:42:44Z" minimumUpdatePeriod="PT0S" timeShiftBufferDepth="PT58M56S" availabilityStartTime="2020-01-07T19:40:50Z" minBufferTime="PT4S">
    <Period start="PT2M48.168S" id="main-content_0">
        <AdaptationSet id="1" group="1" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="video" mimeType="video/mp4" codecs="avc1.640020" maxWidth="1280" maxHeight="720" startWithSAP="1">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
            <SegmentTemplate timescale="90000" presentationTimeOffset="15135120" media="QualityLevels($Bandwidth$)/Fragments(video=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(video=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="15135120" d="135135" r="59"/>
                    <S d="111111"/>
                    <S d="1502"/>
                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="15135120" type="0" wallClockTime="2020-01-07T19:40:50Z" presentationTime="15135120"/>
            <Representation id="1_V_video_5322324134428436312" bandwidth="3500000" width="1280" height="720"/>
            <Representation id="1_V_video_16981495139092747609" bandwidth="2200000" width="960" height="540"/>
            <Representation id="1_V_video_1384718563016940751" bandwidth="1350000" codecs="avc1.64001F" width="704" height="396"/>
            <Representation id="1_V_video_4425970933904124207" bandwidth="850000" codecs="avc1.64001E" width="512" height="288"/>
            <Representation id="1_V_video_11952982975776937431" bandwidth="550000" codecs="avc1.640016" width="384" height="216"/>
            <Representation id="1_V_video_10673801877453424365" bandwidth="200000" codecs="avc1.640015" width="340" height="192"/>
        </AdaptationSet>
        <AdaptationSet id="2" group="5" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="audio" mimeType="audio/mp4" codecs="mp4a.40.5" lang="en">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
            <Label>audio</Label>
            <SegmentTemplate timescale="44100" presentationTimeOffset="7416208" media="QualityLevels($Bandwidth$)/Fragments(audio=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(audio=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="7417856" d="133120"/>
                    <S d="132096" r="1"/>
                    
                    <!--> ... aduio segments truncated for sample brevity </-->

                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="7417856" type="0" wallClockTime="2020-01-07T19:40:50.037Z" presentationTime="7417856"/>
            <Representation id="5_A_audio_17504386117102112482" bandwidth="128000" audioSamplingRate="44100"/>
        </AdaptationSet>
    </Period>
    <Period start="PT4M19.509S" id="scte-35_0">
        <EventStream schemeIdUri="urn:scte:scte35:2014:xml+bin" value="scte35" timescale="10000000">
            <Event presentationTime="2595092444" duration="11011000" id="1002">
                <Signal xmlns="http://www.scte.org/schemas/35/2016">
                    <Binary>/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==</Binary>
                </Signal>
            </Event>
            <Event presentationTime="2606103444" id="1002">
                <Signal xmlns="http://www.scte.org/schemas/35/2016">
                    <Binary>/DAgAAAAAAXdAP/wDwUAAAPqf0/+AWXk0wABAQEAAGB86Fo=</Binary>
                </Signal>
            </Event>
        </EventStream>
        <AdaptationSet id="1" group="1" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="video" mimeType="video/mp4" codecs="avc1.640020" maxWidth="1280" maxHeight="720" startWithSAP="1">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
            <SegmentTemplate timescale="90000" presentationTimeOffset="23355832" media="QualityLevels($Bandwidth$)/Fragments(video=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(video=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="23355833" d="22522"/>
                    <S d="76577"/>
                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="23355833" type="0" wallClockTime="2020-01-07T19:42:21.341Z" presentationTime="23355833"/>
            <Representation id="1_V_video_5322324134428436312" bandwidth="3500000" width="1280" height="720"/>
            <Representation id="1_V_video_16981495139092747609" bandwidth="2200000" width="960" height="540"/>
            <Representation id="1_V_video_1384718563016940751" bandwidth="1350000" codecs="avc1.64001F" width="704" height="396"/>
            <Representation id="1_V_video_4425970933904124207" bandwidth="850000" codecs="avc1.64001E" width="512" height="288"/>
            <Representation id="1_V_video_11952982975776937431" bandwidth="550000" codecs="avc1.640016" width="384" height="216"/>
            <Representation id="1_V_video_10673801877453424365" bandwidth="200000" codecs="avc1.640015" width="340" height="192"/>
        </AdaptationSet>
        <AdaptationSet id="2" group="5" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="audio" mimeType="audio/mp4" codecs="mp4a.40.5" lang="en">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
            <Label>audio</Label>
            <SegmentTemplate timescale="44100" presentationTimeOffset="11444358" media="QualityLevels($Bandwidth$)/Fragments(audio=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(audio=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="11446272" d="49152"/>
                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="11446272" type="0" wallClockTime="2020-01-07T19:42:21.384Z" presentationTime="11446272"/>
            <Representation id="5_A_audio_17504386117102112482" bandwidth="128000" audioSamplingRate="44100"/>
        </AdaptationSet>
    </Period>
    <Period start="PT4M20.610S" id="main-content_1">
        <AdaptationSet id="1" group="1" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="video" mimeType="video/mp4" codecs="avc1.640020" maxWidth="1280" maxHeight="720" startWithSAP="1">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
            <SegmentTemplate timescale="90000" presentationTimeOffset="23454931" media="QualityLevels($Bandwidth$)/Fragments(video=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(video=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="23454932" d="58558"/>
                    <S d="4504"/>
                    <S d="130631"/>
                    <S d="135135" r="12"/>
                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="23454932" type="0" wallClockTime="2020-01-07T19:42:22.442Z" presentationTime="23454932"/>
            <Representation id="1_V_video_5322324134428436312" bandwidth="3500000" width="1280" height="720"/>
            <Representation id="1_V_video_16981495139092747609" bandwidth="2200000" width="960" height="540"/>
            <Representation id="1_V_video_1384718563016940751" bandwidth="1350000" codecs="avc1.64001F" width="704" height="396"/>
            <Representation id="1_V_video_4425970933904124207" bandwidth="850000" codecs="avc1.64001E" width="512" height="288"/>
            <Representation id="1_V_video_11952982975776937431" bandwidth="550000" codecs="avc1.640016" width="384" height="216"/>
            <Representation id="1_V_video_10673801877453424365" bandwidth="200000" codecs="avc1.640015" width="340" height="192"/>
        </AdaptationSet>
        <AdaptationSet id="2" group="5" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="audio" mimeType="audio/mp4" codecs="mp4a.40.5" lang="en">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
            <Label>audio</Label>
            <SegmentTemplate timescale="44100" presentationTimeOffset="11492916" media="QualityLevels($Bandwidth$)/Fragments(audio=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(audio=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="11495424" d="28672"/>
                    <S d="1024"/>
                    <S d="131072"/>
                    <S d="132096"/>
                    <S d="133120"/>
                    <S d="132096" r="1"/>
                    <S d="133120"/>
                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="11495424" type="0" wallClockTime="2020-01-07T19:42:22.499Z" presentationTime="11495424"/>
            <Representation id="5_A_audio_17504386117102112482" bandwidth="128000" audioSamplingRate="44100"/>
        </AdaptationSet>
    </Period>
</MPD>

~~~
### <a name="334-mpeg-dash-in-band-event-message-box-signaling"></a>3.3.4 MPEG 대시 대역 내 이벤트 메시지 상자 신호

대역 내 이벤트 스트림을 사용하려면 MPD에 적응 설정 수준의 InbandEventStream 요소가 있어야 합니다.  이 요소에는 필수 schemeIdUri 특성과 선택적 시간 표시기 특성이 있으며 이벤트 메시지 상자('emsg')에도 나타납니다.  MPD에 정의되지 않은 구성표 식별자가 있는 이벤트 메시지 상자는 없어야 **합니다.**

대역 내 [SCTE-35] 캐리지의 경우 신호는 schemeId = "urn:scte:scte35:2013:bin"을 사용해야 **합니다.**
[SCTE-35] 대역 내 메시지의 캐리지의 규범적 정의는 [SCTE-214-3] 초 7.3.2(SCTE 35 큐 메시지 의 캐리지)에 정의되어 있습니다.

다음 세부 사항은 [SCTE-214-3]을 준수하여 'emsg'에서 클라이언트가 예상해야 하는 특정 값을 간략하게 설명합니다.

| **필드 이름**          | **필드 유형**          | **필수?** | **설명**                                                                                                                                                                                                                                                                                        |
| ----------------------- | ----------------------- | ------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| scheme_id_uri           | 문자열                  | 필수      | 메시지의 구성표를 식별합니다. 이 구성표는 라이브 서버 매니페스트 상자에 있는 Scheme 특성의 값으로 설정됩니다. 값은 메시지 구성표를 식별하는 URN이어야 **합니다.** [SCTE-35] 메시지의 경우[SCTE-214-3]를 준수하는 "urn:scte:scte:scte35:2013:bin"이어야 **합니다.**          |
| 값                   | 문자열                  | 필수      | 메시지의 의미 체계를 사용자 지정하기 위해 구성표 소유자가 추가로 사용하는 문자열 값입니다. 동일한 구성표를 사용하여 여러 이벤트 스트림을 구별하기 위해 값을 이벤트 스트림의 이름(부드러운 수집의 경우 trackName 또는 RTMP 수집의 경우 AMF 메시지 이름)으로 설정합니다. |
| 시간 간격               | 부호 없는 32비트 정수 | 필수      | 'emsg' 상자 내의 시간 및 기간 필드의 시간별 틱(ticks)의 시간척도입니다.                                                                                                                                                                                                            |
| Presentation_time_delta | 부호 없는 32비트 정수 | 필수      | 이벤트의 프레젠테이션 시간에 대한 미디어 프레젠테이션 시간 델타 및 이 세그먼트에서 가장 빠른 프레젠테이션 시간입니다. 프레젠테이션 시간과 기간은 [ISO-14496-12] 부속서 I에 정의된 대로 유형 1 또는 2의 SAP(스트림 액세스 포인트)와 **일치해야 합니다.**                                  |
| event_duration          | 부호 없는 32비트 정수 | 필수      | 이벤트의 재생 시간이거나 알 수 없는 재생 시간을 나타내는 0xFFFFFFFF입니다.                                                                                                                                                                                                                              |
| Id                      | 부호 없는 32비트 정수 | 필수      | 메시지의 이 인스턴스를 식별합니다. 동일한 의미 체계를 갖는 메시지의 값은 동일해야 합니다. 메시지가 수집될 때 ID를 지정하지 않으면 Azure Media Services에서 고유 ID를 생성합니다.                                                                                        |
| Message_data            | 바이트 배열              | 필수      | 이벤트 메시지입니다. [SCTE-35] 메시지의 경우 메시지 데이터는 [SCTE-214-3]을 준수하는 이진 splice_info_section() 입니다.                                                                                                                                                                        |


#### <a name="example-inbandevenstream-entity-for-adobe-simple-mode"></a>Adobe 단순 모드에 대한 인반벤스트림 엔터티 예
~~~ xml

      <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
~~~

### <a name="335-dash-message-handling"></a>3.3.5 대시 메시지 처리

이벤트는 비디오 및 오디오 트랙 모두에 대해 'emsg' 상자 내에서 대역 내 로 신호를 받습니다.  presentation_time_delta가 15초 이하인 모든 세그먼트 요청에 대해 신호가 발생합니다. 

슬라이딩 프레젠테이션 시간 범위를 사용하도록 설정되면, 이벤트 메시지의 시간과 재생 시간의 합이 매니페스트에 있는 미디어의 데이터 시간보다 작은 경우 이벤트 메시지가 MPD에서 제거됩니다.  즉, 참조하는 미디어 시간이 슬라이딩 프레젠테이션 시간 범위를 벗어나는 경우 이벤트 메시지가 매니페스트에서 제거됩니다.

## <a name="4-scte-35-ingest-implementation-guidance-for-encoder-vendors"></a>4. 인코더 벤더를 위한 SCTE-35 인제스트 구현 지침

다음 지침은 인코더 공급업체가 이 사양을 구현하는 데 영향을 미칠 수 있는 일반적인 문제입니다.  아래 지침은 실제 파트너 피드백을 기반으로 수집되어 다른 사용자용으로 이 사양을 보다 쉽게 구현할 수 있습니다. 

[SCTE-35] 메시지는 [MS-SSTR-Ingest] 및 [RTMP] 인제스트에 대한 **"scte35"에** 대한 구성표 **"urn:scte:scte35:2013:bin"을** 사용하여 이진 형식으로 가져옵니다. MPEG-2 전송 스트림 PTS(프레젠테이션 시간스탬프)를 기반으로 하는 [SCTE-35] 타이밍을 쉽게 변환할 수 있도록 하기 위해 PTS(splice_time()의 pts_time + pts_adjustment)와 미디어 타임라인 간의 매핑은 이벤트 프레젠테이션 시간(부드러운 수집의 fragment_absolute_time 필드 및 RTMP 수집의 시간 필드)에서 제공됩니다. 33비트 PTS 값이 대략 26.5시간마다 롤오버되기 때문에 매핑이 필요합니다.

원활한 스트리밍 수집 [MS-SSTR-Ingest]는 미디어 데이터 상자('mdat')에 [SCTE-35]에 정의된 **splice_info_section()를** **포함해야 합니다.** 

RTMP 인제스트의 경우 AMF 메시지의 큐 속성은 [SCTE-35]에 정의된 base64 인코딩된 **splice_info_section()로** 설정됩니다.  

메시지에 위에서 설명한 형식이 있으면 위에 정의된 HLS, Smooth 및 DASH 클라이언트로 전송됩니다.  

Azure Media Services 플랫폼으로 구현을 테스트할 때 먼저 "통과" LiveEvent로 테스트를 시작한 다음 인코딩 LiveEvent에서 테스트로 이동하십시오.

---

## <a name="change-history"></a>변경 내역

| Date     | 변경                                                                                                             |
| -------- | ------------------------------------------------------------------------------------------------------------------- |
| 07/2/19  | SCTE35 지원을 위한 RTMP 인제스트 개정, 원소 라이브용 RTMP "onCuePoint" 추가                                  |
| 08/22/19 | 사용자 지정 메타데이터에 대한 OnUserDataEvent를 RTMP에 추가하도록 업데이트되었습니다.                                                          |
| 1/08/20  | RTMP 단순 및 RTMP SCTE35 모드에서 오류가 수정되었습니다. "onCuePoint"에서 "onAdCue"로 변경되었습니다. 단순 모드 테이블이 업데이트되었습니다. |

## <a name="next-steps"></a>다음 단계
Media Services 학습 경로 보기.

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>피드백 제공
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
