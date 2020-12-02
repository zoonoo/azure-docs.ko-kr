---
title: Media Services 아키텍처
titleSuffix: Azure Media Services
description: 이 문서에서는 Media Services에 대 한 아키텍처를 설명 합니다.
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
ms.openlocfilehash: e00079190371b3ae0e318aaadd8cf724d9d339d0
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2020
ms.locfileid: "96512724"
---
# <a name="media-services-architectures"></a>Media Services 아키텍처

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

## <a name="live-streaming-digital-media"></a>디지털 미디어 라이브 스트리밍

라이브 스트리밍 솔루션을 사용 하면 실시간으로 비디오를 캡처 하 고 실시간으로 소비자에 게 브로드캐스트할 수 있습니다. 이 솔루션에서 비디오는 비디오 카메라로 캡처되고 채널 입력 끝점으로 전송 됩니다. 채널은 라이브 입력 스트림을 수신 하 고 스트리밍 끝점을 통해 웹 브라우저 또는 모바일 앱에 스트리밍할 수 있도록 합니다. 채널에서는 추가 처리 및 배달 전에 스트림을 미리 보고 유효성을 검사 하는 미리 보기 모니터링 끝점도 제공 합니다. 채널은 나중에 스트리밍할 수 있도록 수집 콘텐츠를 기록 하 고 저장할 수도 있습니다 (주문형 비디오).

이 솔루션은 Azure 관리 서비스: Media Services 및 Content Delivery Network를 기반으로 합니다. 이러한 서비스는 고가용성 환경에서 실행 되 고 패치 되며 지원 되므로 사용자가에서 실행 되는 환경 대신 솔루션에 집중할 수 있습니다.

Azure 아키텍처 센터에서 [라이브 스트리밍 디지털 미디어](https://docs.microsoft.com/azure/architecture/solution-ideas/articles/digital-media-live-stream) 를 참조 하세요.

## <a name="instant-broadcasting-on-serverless-architecture"></a>서버 리스 아키텍처에서 즉시 브로드캐스트

서버 리스 코드를 사용 하 여 일대다 실시간 통신과 업데이트를 간소화 합니다. 이 아키텍처는 Azure Functions, SignalR 서비스, 저장소 계정 및 SQL database를 사용 합니다.

Azure 아키텍처 센터에서 [서버 리스 아키텍처의 인스턴트 브로드캐스팅](https://docs.microsoft.com/azure/architecture/solution-ideas/articles/instant-broadcasting-on-serverless-architecture) 을 참조 하세요.

## <a name="video-on-demand-digital-media"></a>주문형 비디오 디지털 미디어

영화, 뉴스 클립, 스포츠 세그먼트, 교육 비디오 및 고객 지원 자습서와 같은 기록 된 비디오 콘텐츠를 비디오 가능 끝점 장치, 모바일 응용 프로그램 또는 데스크톱 브라우저에 스트리밍할 수 있는 기능을 제공 하는 기본 주문형 비디오 솔루션입니다. 비디오 파일은 Azure Blob storage에 업로드 되 고 다중 비트 전송률 표준 형식으로 인코딩된 다음 모든 주요 적응 비트 전송률 스트리밍 프로토콜 (HLS, MPEG 대시, 부드러운)을 통해 Azure Media Player 클라이언트에 배포 됩니다.

이 솔루션은 Blob Storage, Content Delivery Network 및 Azure Media Player Azure 관리 서비스를 기반으로 합니다. 이러한 서비스는 고가용성 환경에서 실행 되 고 패치 되며 지원 되므로 사용자가에서 실행 되는 환경 대신 솔루션에 집중할 수 있습니다.

Azure 아키텍처 센터에서 [주문형 비디오 디지털 미디어](https://docs.microsoft.com/azure/architecture/solution-ideas/articles/digital-media-video) 를 참조 하세요.

## <a name="gridwich-media-processing-system"></a>Gridwich 미디어 처리 시스템

Gridwich 시스템은 Azure에서 미디어 자산을 처리 하 고 제공 하기 위한 모범 사례를 구체화 합니다. Gridwich 시스템은 미디어와 관련이 있지만 메시지 처리 및 이벤트 프레임 워크는 모든 상태 비저장 이벤트 처리 워크플로에 적용 될 수 있습니다.

Azure 아키텍처 센터에서 [Gridwich media processing system](https://docs.microsoft.com/azure/architecture/reference-architectures/media-services/gridwich-architecture) 을 참조 하세요.

## <a name="next-steps"></a>다음 단계

> [Azure Media Services 개요](media-services-overview.md)