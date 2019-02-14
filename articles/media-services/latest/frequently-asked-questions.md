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
ms.openlocfilehash: be4c08bc31c8811655230ab89b48271f4c2b3164
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/06/2019
ms.locfileid: "55756583"
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

Media Services는 OData를 지원하는 리소스에 대해 $top을 지원하지만, $top에 전달되는 값은 1,000 미만이어야 합니다(예: 페이지 매김에 대한 페이지 크기).

이렇게 하면 $top(예: 가장 최근의 100개 항목)을 사용하여 작은 항목 샘플을 얻거나 페이지 매김을 사용하는 모든 항목을 페이징할 수 있습니다. 

Media Services에서는 사용자가 지정한 페이지 크기로 데이터를 페이징하도록 지원하지 않습니다.

자세한 내용은 [필터링, 정렬, 페이징](entities-overview.md)을 참조하세요.

### <a name="how-to-retrieve-an-entity-in-media-services-v3"></a>Media Services v3에서 엔터티를 검색하는 방법은 무엇인가요?

v3은 **Azure Resource Manager**에서 빌드된 관리 및 운영 기능을 모두 제공하는 통합된 API 화면을 기반으로 합니다. **Azure Resource Manager**에 따라 리소스 이름은 항상 고유합니다. 따라서 고유 식별자 문자열(예: GUID)을 리소스에 사용할 수 있습니다.

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
