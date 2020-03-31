---
title: Azure 미디어 서비스 고가용성 스트리밍
description: 지역 데이터 센터 가동 중단 또는 오류가 발생할 경우 보조 Media Services 계정에 장애 조치하는 방법을 알아봅니다.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.subservice: ''
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 02/24/2020
ms.author: juliako
ms.openlocfilehash: 1492dd392eabc4331f8e3d4604fb245a89dedff5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78899231"
---
# <a name="media-services-high-availability-streaming"></a>미디어 서비스 고가용성 스트리밍

현재 Azure Media Services는 지역 데이터 센터 가동 중단 또는 기본 구성 요소 또는 종속 서비스의 실패가 있는 경우 서비스의 즉각적인 장애 조치(failover)를 제공하지 않습니다. 이 문서에서는 주문형 비디오 교차 지역 스트리밍을 빌드하는 방법에 대한 지침을 제공합니다.

## <a name="prerequisites"></a>사전 요구 사항

[지역 간 인코딩 시스템을 구축하는 방법](media-services-high-availability-encoding.md) 검토

## <a name="how-to-build-video-on-demand-cross-region-streaming"></a>주문형 비디오 교차 지역 스트리밍을 구축하는 방법 

* 주문형 비디오 교차 지역 스트리밍에는 [자산,](assets-concept.md) [콘텐츠 키 정책(사용된](content-key-policy-concept.md) 경우), [스트리밍 정책](streaming-policy-concept.md)및 [스트리밍 로케이터가](streaming-locators-concept.md)포함됩니다. 
* 두 리전에서 정책을 만들고 최신 상태로 유지해야 합니다. 
* 스트리밍 로케이터를 만들 때 동일한 로케이터 ID 값, ContentKey ID 값 및 ContentKey 값을 사용하려고 합니다.  
* 콘텐츠를 인코딩하는 경우 지역 A의 콘텐츠를 인코딩하고 게시한 다음 인코딩된 콘텐츠를 지역 B에 복사하고 지역 A와 동일한 값을 사용하여 게시하는 것이 좋습니다.
* 원본 및 키 배달 서비스에 대 한 호스트 이름에 트래픽 관리자를 사용할 수 있습니다 (미디어 서비스 구성에서 사용자 지정 키 서버 URL 처럼 보임).

## <a name="next-steps"></a>다음 단계

체크 아웃:

* [자습서: URL을 기반으로 원격 파일을 인코딩하고 비디오를 스트리밍합니다.](stream-files-dotnet-quickstart.md)
* [코드 샘플](https://docs.microsoft.com/samples/browse/?products=azure-media-services)
