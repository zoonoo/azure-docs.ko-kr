---
title: Azure Media Services v3 질문과 대답 | Microsoft Docs
description: 이 문서에서는 Azure Media Services v3에 대해 자주 묻는 질문과 대답을 제공합니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/05/2019
ms.author: juliako
ms.openlocfilehash: a447c359c38c2173ea42b6d717067fc8b3a88f9a
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55875494"
---
# <a name="azure-media-services-v3-frequently-asked-questions"></a>Azure Media Services v3 질문과 대답

이 문서에서는 AMS(Azure Media Services) v3에 대해 자주 묻는 질문과 대답을 제공합니다.

## <a name="v3-apis"></a>v3 API

### <a name="how-do-i-configure-media-reserved-units"></a>미디어 예약 단위를 구성하려면 어떻게 할까요?

Media Services v3 또는 Video Indexer에 의해 트리거되는 오디오 분석 및 비디오 분석 작업의 경우 10개의 S3 MRU를 사용하여 계정을 프로비전하는 것이 좋습니다. 10개가 넘는 S3 MRU가 필요한 경우 [Azure Portal](https://portal.azure.com/)을 사용하여 지원 티켓을 엽니다.

자세한 내용은 [CLI를 사용하여 미디어 처리 크기 조정](media-reserved-units-cli-how-to.md)을 참조하세요.

### <a name="what-is-the-recommended-method-to-process-videos"></a>비디오 처리에 권장하는 방법은 무엇입니까?

비디오를 가리키는 HTTP(s) URL을 사용하여 작업을 제출하는 것이 좋습니다. 자세한 내용은 [HTTP(s) 수집](job-input-from-http-how-to.md)을 참조하세요. 비디오를 처리하기 위해 입력 비디오를 사용하여 자산을 만들 필요가 없습니다.

### <a name="how-does-pagination-work"></a>페이지 매김은 어떻게 작동하나요?

페이지 매김을 사용할 때는 항상 다음 링크를 사용하여 컬렉션을 열거하고, 특정 페이지 크기에 따라 달라지지 않아야 합니다. 자세한 내용과 예제는 [필터링, 정렬, 페이징](entities-overview.md)을 참조하세요.

## <a name="live-streaming"></a>라이브 스트리밍 

###  <a name="how-to-insert-breaksvideos-and-image-slates-during-live-stream"></a>라이브 스트리밍 도중에 중단/비디오 및 이미지 슬레이트를 삽입하는 방법은 무엇입니까?

Media Services v3 라이브 인코딩은 아직 라이브 스트리밍 도중에 비디오 또는 이미지 슬레이트를 삽입하는 기능을 지원하지 않습니다. 

[라이브 온-프레미스 인코더](recommended-on-premises-live-encoders.md)를 사용하여 원본 비디오를 바꿀 수 있습니다. Telestream Wirecast, Switcher Studio(iOS), OBS Studio(무료 앱) 등 여러 앱에서 원본을 바꾸는 기능을 제공합니다.

## <a name="media-services-v2-vs-v3"></a>Media Services v2와 v3 비교 

### <a name="can-i-use-the-azure-portal-to-manage-v3-resources"></a>Azure Portal을 사용하여 v3 리소스를 관리할 수 있나요?

아직 없습니다. 지원되는 SDK 중 하나를 사용할 수 있습니다. 이 문서 집합에 있는 자습서와 샘플을 참조하세요.

### <a name="is-there-an-assetfile-concept-in-v3"></a>v3에 AssetFile 개념이 있나요?

Media Services를 Storage SDK 종속성과 분리하기 위해 AssetFiles가 AMS API에서 제거되었습니다. 이제 Media Services가 아닌 Storage에서 해당 Storage에 속한 정보를 보관합니다. 

자세한 내용은 [Media Services v3로 마이그레이션](migrate-from-v2-to-v3.md)을 참조하세요.

### <a name="where-did-client-side-storage-encryption-go"></a>클라이언트 쪽 저장소 암호화는 어디에 있나요?

이제 서버 쪽 스토리지 암호화(기본적으로 설정됨)를 사용하는 것이 좋습니다. 자세한 내용은 [미사용 데이터에 대한 Azure Storage 서비스 암호화](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)를 참조하세요.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Media Services v3 개요](media-services-overview.md)
