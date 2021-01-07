---
title: Azure Media Services 지역 가용성 | Microsoft Docs
description: 이 문서에서는 Microsoft Azure Media Services 기능 및 서비스 지역 가용성에 대 한 개요를 제공 합니다.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/03/2020
ms.author: inhenkel
ms.custom: references_regions
ms.openlocfilehash: cf84e45ff43643c7e9a983b867194a7920aa1b57
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93351901"
---
# <a name="media-services-regional-availability"></a>Media Services 지역 가용성

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Media Services v2에는 새로운 특징 또는 기능이 추가되지 않습니다. [Media Services v3](../latest/media-services-overview.md)의 최신 버전을 확인하세요. 또한 [v2에서 v3로의 마이그레이션 지침](../latest/migrate-from-v2-to-v3.md)을 참조하세요.

Microsoft AMS(Azure Media Services)는 다양한 클라이언트(예: TV, PC 및 모바일 디바이스)로의 주문형 및 라이브 스트리밍 배달을 위해 비디오 또는 오디오 콘텐츠를 안전하게 업로드, 저장, 인코딩 및 패키지할 수 있습니다.

AMS는 전 세계 여러 지역에서 작동 하므로 응용 프로그램을 빌드할 위치를 유연 하 게 선택할 수 있습니다. 이 문서에서는 Microsoft Azure Media Services 기능 및 서비스 지역 가용성에 대 한 개요를 제공 합니다.

전체 Azure 글로벌 인프라에 대 한 자세한 내용은 [azure 지역](https://azure.microsoft.com/global-infrastructure/geographies/)을 참조 하세요.

## <a name="ams-accounts"></a>AMS 계정

[지역별 Azure 제품](https://azure.microsoft.com/global-infrastructure/services/?products=media-services&regions=all) 을 사용 하 여 Media Services를 특정 지역에서 사용할 수 있는지 여부를 확인 합니다.

## <a name="streaming-endpoints"></a>스트리밍 엔드포인트

Media Services 고객은 **표준** 스트리밍 엔드포인트나 **프리미엄** 스트리밍 엔드포인트를 선택할 수 있습니다.

|이름|상태|지역
|---|---|---|
|표준|GA|모두|
|프리미엄|GA|모두|

## <a name="live-encoding"></a>라이브 인코딩

독일, 브라질 남부, 인도 서 부, 인도 남부 및 인도 중부를 제외한 모든 지역에서 사용할 수 있습니다.

## <a name="encoding-media-processors"></a>미디어 프로세서 인코딩

AMS에서는 두 가지 주문형 인코더인 **Media Encoder Standard** 및 **Media Encoder Premium 워크플로** 를 제공합니다. 자세한 내용은 [Azure 주문형 미디어 인코더의 개요 및 비교](media-services-encode-asset.md)를 참조하세요.

|미디어 프로세서 이름|상태|영역
|---|---|---|
|미디어 인코더 표준|GA|모두|
|미디어 인코더 Premium 워크플로|GA|중국을 제외한 모든 지역|

## <a name="analytics-media-processors"></a>분석 미디어 프로세서

미디어 분석은 조직과 기업이 비디오 파일에서 실질적인 통찰력을 끌어내기 쉽도록 만드는 언어 및 시각 구성 요소 모음입니다. 자세한 내용은 [Azure Media Services 분석 개요](./legacy-components.md)를 참조하세요.

> [!NOTE]
> 일부 분석 미디어 프로세서는 더 이상 사용되지 않을 예정입니다. 사용 중지 날짜는 [레거시 구성 요소](legacy-components.md) 토픽을 참조하세요.

|미디어 프로세서 이름|상태|지역
|---|---|---|
|Azure 미디어 얼굴 탐지기|미리 보기|모두|
|Azure Media Indexer|GA|모두|
|Azure 미디어 동작 탐지기|미리 보기|모두|
|Azure 미디어 OCR|미리 보기|모두|
|Azure Media Redactor|GA|모두|
|Azure 미디어 비디오 미리 보기|미리 보기|모두|

## <a name="protection"></a>보호

Microsoft Azure Media Services를 사용하면 컴퓨터를 떠날 때부터 스토리지, 처리 및 배달에 이르는 과정 내내 미디어를 보호할 수 있습니다. 자세한 내용은 [AMS 콘텐츠 보호](media-services-content-protection-overview.md)를 참조하세요.

|암호화|상태|영역|
|---|---|---| 
|스토리지|GA|모두|
|AES-128 키|GA|모두|
|Fairplay|GA|모두|
|PlayReady|GA|모두|
|Widevine|GA|독일, 연방 정부 및 중국을 제외한 모든입니다.

> [!NOTE]
> Widevine은 Google Inc.에서 제공하는 서비스로, Google Inc.의 서비스 약관 및 개인정보처리방침을 따릅니다.

## <a name="reserved-units-rus"></a>RU(예약 단위)

프로비전되는 예약 단위의 수에 따라 특정 계정에서 동시에 처리할 수 있는 미디어 작업의 수가 결정됩니다.

모든 지역에서 사용 가능합니다.

## <a name="reserved-unit-ru-type"></a>RU(예약 단위) 형식

Media Services 계정은 미디어 처리 작업을 완료 하는 속도를 결정 하는 예약 단위 형식과 연결 됩니다. S1, S2 또는 S3 예약 단위 유형 중에서 선택할 수 있습니다.

|RU 형식 이름|상태|영역
|---|---|---|
|S1|GA|모두|
|S2|GA|브라질 남부 및 인도 서부를 제외한 모든 지역|
|S3|GA|인도 서부를 제외한 모든 지역|

## <a name="next-steps"></a>다음 단계

[Media Services v3로 마이그레이션](../latest/media-services-overview.md)

## <a name="provide-feedback"></a>피드백 제공

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]