---
title: Azure Media Services 고가용성 스트리밍
description: 지역 데이터 센터 중단 또는 오류가 발생 하는 경우 보조 Media Services 계정으로 장애 조치 (failover) 하는 방법을 알아봅니다.
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "78899231"
---
# <a name="media-services-high-availability-streaming"></a>Media Services 고가용성 스트리밍

기본 구성 요소 또는 종속 서비스에 대 한 지역 데이터 센터 가동 중단 또는 오류가 발생 하는 경우 현재 Azure Media Services는 서비스의 즉각적인 장애 조치 (failover)를 제공 하지 않습니다. 이 문서에서는 주문형 비디오 지역 간 스트리밍을 빌드하는 방법에 대 한 지침을 제공 합니다.

## <a name="prerequisites"></a>사전 요구 사항

[지역 간 인코딩 시스템을 빌드하는 방법](media-services-high-availability-encoding.md) 검토

## <a name="how-to-build-video-on-demand-cross-region-streaming"></a>주문형 비디오 교차 지역 스트리밍을 빌드하는 방법 

* 주문형 비디오 교차 지역 스트리밍은 [자산](assets-concept.md), [콘텐츠 키 정책](content-key-policy-concept.md) (사용 되는 경우), [스트리밍 정책](streaming-policy-concept.md)및 [스트리밍 로케이터](streaming-locators-concept.md)를 복제 합니다. 
* 두 지역 모두에서 정책을 만들고 최신 상태로 유지 해야 합니다. 
* 스트리밍 로케이터를 만들 때 동일한 로케이터 ID 값, ContentKey ID 값 및 ContentKey 값을 사용 하는 것이 좋습니다.  
* 콘텐츠를 인코딩하는 경우 지역 A에서 콘텐츠를 인코딩하고 게시 한 다음, 인코딩된 콘텐츠를 지역 B에 복사 하 고 지역 A의 값과 동일한 값을 사용 하 여 게시 하는 것이 좋습니다.
* 원본 및 키 배달 서비스에 대 한 호스트 이름에 Traffic Manager를 사용할 수 있습니다. Media Services 구성에서는 사용자 지정 키 서버 URL 처럼 보입니다.

## <a name="next-steps"></a>다음 단계

체크 아웃:

* [자습서: URL을 기반으로 원격 파일 인코딩 및 비디오 스트림](stream-files-dotnet-quickstart.md)
* [코드 샘플](https://docs.microsoft.com/samples/browse/?products=azure-media-services)
