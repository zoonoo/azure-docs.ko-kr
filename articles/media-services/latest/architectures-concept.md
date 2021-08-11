---
title: Media Services 아키텍처
description: 이 문서에서는 Media Services에 관한 아키텍처를 설명합니다.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 11/20/2020
ms.author: inhenkel
ms.openlocfilehash: ad464eb1c0b6dec694c7c40868a0f95fcfeaf6e8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98891491"
---
# <a name="media-services-architectures"></a>Media Services 아키텍처

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

## <a name="live-streaming-digital-media"></a>디지털 미디어 라이브 스트리밍

라이브 스트리밍 솔루션을 사용하면 온라인 스트리밍 인터뷰, 회의, 스포츠 이벤트와 같은 비디오를 실시간으로 캡처하여 소비자에게 실시간으로 브로드캐스트할 수 있습니다. 이 솔루션에서 동영상은 비디오 카메라로 캡처되어 채널 입력 엔드포인트로 전송됩니다. 채널은 라이브 입력 스트림을 수신하여 스트리밍 엔드포인트를 통해 웹 브라우저나 모바일 앱으로 스트리밍할 수 있습니다. 채널은 스트림을 추가로 처리하고 제공하기 전에 스트림을 미리 보고 유효성을 검사하는 미리 보기 모니터링 엔드포인트도 제공합니다. 채널은 나중에 스트리밍하기 위해 수집된 콘텐츠도 기록하고 저장합니다(주문형 비디오).

이 솔루션은 Azure 관리되는 서비스, 즉 Media Services와 Content Delivery Network를 기반으로 합니다. 해당 서비스는 고가용성 환경에서 실행되고 패치되며 지원되므로 실행되는 환경이 아니라 솔루션에 집중할 수 있습니다.

Azure 아키텍처 센터에서 [라이브 스트리밍 디지털 미디어](/azure/architecture/solution-ideas/articles/digital-media-live-stream)를 참조하세요.

## <a name="video-on-demand-digital-media"></a>주문형 비디오 디지털 미디어

영화, 뉴스 클립, 스포츠 세그먼트, 교육 비디오 및 고객 지원 자습서와 같은 녹화된 비디오 콘텐츠를 비디오 지원 엔드포인트 디바이스, 모바일 애플리케이션 또는 데스크톱 브라우저로 스트림할 수 있는 기능을 제공하는 기본 주문형 비디오 솔루션입니다. 비디오 파일은 Azure Blob 스토리지에 업로드되고, 다중 비트 전송률 표준 형식으로 인코딩된 다음, 모든 주요 적응 비트 전송률 스트리밍 프로토콜(HLS, MPEG-DASH, Smooth)을 통해 Azure Media Player 클라이언트에 배포됩니다.

이 솔루션은 Azure 관리되는 서비스, 즉 Blob Storage, Content Delivery Network 및 Azure Media Player를 기반으로 빌드됩니다. 해당 서비스는 고가용성 환경에서 실행되고 패치되며 지원되므로 실행되는 환경이 아니라 솔루션에 집중할 수 있습니다.

Azure 아키텍처 센터에서 [주문형 비디오 디지털 미디어](/azure/architecture/solution-ideas/articles/digital-media-video)를 참조하세요.

## <a name="gridwich-media-processing-system"></a>Gridwich 미디어 처리 시스템

Gridwich 시스템은 Azure에서 미디어 자산을 처리하고 전송하기 위한 모범 사례를 포함하고 있습니다. Gridwich 시스템은 미디어에 따라 다르지만 메시지 처리와 이벤트 프레임워크는 모든 상태 비저장 이벤트 처리 워크플로에 적용할 수 있습니다.

Azure 아키텍처 센터에서 [Gridwich 미디어 처리 시스템](/azure/architecture/reference-architectures/media-services/gridwich-architecture)을 참조하세요.

## <a name="next-steps"></a>다음 단계

> [Azure Media Services 개요](media-services-overview.md)