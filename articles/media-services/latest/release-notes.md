---
title: Azure Media Services v3 릴리스 정보 | Microsoft 문서
description: 최신 개발 정보를 확인할 수 있도록 이 문서에서는 Azure Media Services v3의 최신 업데이트에 대한 정보를 제공합니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 09/25/2018
ms.author: juliako
ms.openlocfilehash: ed2550c1df4645933fb968c54ee536995c810136
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/26/2018
ms.locfileid: "47219332"
---
# <a name="azure-media-services-v3-preview-release-notes"></a>Azure Media Services v3(미리 보기) 릴리스 정보 

최신 개발 정보를 확인할 수 있도록 이 문서에서는 다음과 같은 정보를 제공합니다.

* 최신 릴리스
* 알려진 문제
* 버그 수정
* 사용되지 않는 기능
* 변경 계획

## <a name="may-07-2018"></a>2018년 5월 7일

### <a name="net-sdk"></a>.Net SDK

.Net SDK에는 다음과 같은 기능이 있습니다.

1. 미디어 콘텐츠를 인코딩하거나 분석할 수 있는 **Transform** 및 **Job**. 예를 들어 [스트림 파일](stream-files-tutorial-with-api.md) 및 [분석](analyze-videos-tutorial-with-api.md)을 참조하세요.
2. 최종 사용자 장치에 콘텐츠를 게시하고 스트리밍하는 **StreamingLocator**.
3. 콘텐츠 전송 시 키 배달 및 콘텐츠 보호(DRM)를 구성하는 **StreamingPolicy** 및 **ContentKeyPolicy**.
4. 라이브 스트리밍 콘텐츠의 수집 및 보관을 구성하는 **LiveEvent** 및**LiveOutput**.
5. Azure Storage에 미디어 콘텐츠를 저장하고 게시하는 **Asset**. 
6. 실시간 및 주문형 미디어 콘텐츠에 대한 동적 패키징, 암호화 및 스트리밍을 구성 및 확장하는 **StreamingEndpoint**.

### <a name="known-issues"></a>알려진 문제

* 작업을 제출할 때는 HTTPS URL, SAS URL 또는 Azure Blob Storage에 있는 파일의 경로를 사용하여 원본 비디오를 수집하도록 지정할 수 있습니다. 현재 AMS v3은 HTTPS URL을 통한 청크 분할 전송 인코딩을 지원하지 않습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [개요](media-services-overview.md)
