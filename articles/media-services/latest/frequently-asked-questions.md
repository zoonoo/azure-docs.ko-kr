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
ms.date: 11/05/2018
ms.author: juliako
ms.openlocfilehash: 26661a213b5df5424bf9ab9ad799345ae35620ea
ms.sourcegitcommit: f0c2758fb8ccfaba76ce0b17833ca019a8a09d46
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2018
ms.locfileid: "51036770"
---
# <a name="azure-media-services-v3-frequently-asked-questions"></a>Azure Media Services v3 질문과 대답

이 문서에서는 AMS(Azure Media Services) v3에 대해 자주 묻는 질문과 대답을 제공합니다.

## <a name="can-i-use-the-azure-portal-to-manage-v3-resources"></a>Azure Portal을 사용하여 v3 리소스를 관리할 수 있나요?

아직 없습니다. 지원되는 SDK 중 하나를 사용할 수 있습니다. 이 문서 집합에 있는 자습서와 샘플을 참조하세요.

## <a name="is-there-an-api-for-configuring-media-reserved-units"></a>미디어 예약 단위를 구성하기 위한 API가 있나요?

현재 [미디어 처리 크기 조정](../previous/media-services-scale-media-processing-overview.md)에 설명된 대로 AMS v2 API를 사용하여 미디어 예약 단위를 구성해야 합니다. 

**VideoAnalyzerPreset** 및/또는 **AudioAnalyzerPreset**을 사용하는 경우 Media Services 계정을 10개의 S3 미디어 예약 단위로 설정합니다.

## <a name="does-v3-asset-have-no-assetfile-concept"></a>V3 Asset에는 AssetFile 개념이 없나요?

Media Services를 Storage SDK 종속성과 분리하기 위해 AssetFiles가 AMS API에서 제거되었습니다. 이제 Media Services가 아닌 Storage에서 해당 Storage에 속한 정보를 보관합니다. 

## <a name="where-did-client-side-storage-encryption-go"></a>클라이언트 쪽 저장소 암호화는 어디에 있나요?

이제 서버 쪽 스토리지 암호화(기본적으로 설정됨)를 사용하는 것이 좋습니다. 자세한 내용은 [미사용 데이터에 대한 Azure Storage 서비스 암호화](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)를 참조하세요.

## <a name="what-is-the-recommended-upload-method"></a>권장되는 업로드 방법은 무엇인가요?

HTTP(s) 수집을 사용하는 것이 좋습니다. 자세한 내용은 [HTTP(s) 수집](job-input-from-http-how-to.md)을 참조하세요.

## <a name="how-does-pagination-work"></a>페이지 매김은 어떻게 작동하나요?

Media Services는 OData를 지원하는 리소스에 대해 $top을 지원하지만, $top에 전달되는 값은 1,000 미만이어야 합니다(예: 페이지 매김에 대한 페이지 크기).

이렇게 하면 $top(예: 가장 최근의 100개 항목)을 사용하여 작은 항목 샘플을 얻거나 페이지 매김을 사용하는 모든 항목을 페이징할 수 있습니다. 

Media Services에서는 사용자가 지정한 페이지 크기로 데이터를 페이징하도록 지원하지 않습니다.

자세한 내용은 [필터링, 정렬, 페이징](assets-concept.md#filtering-ordering-paging)을 참조하세요.

## <a name="how-to-retrieve-an-entity-in-media-services-v3"></a>Media Services v3에서 엔터티를 검색하는 방법은 무엇인가요?

v3은 **Azure Resource Manager**에서 빌드된 관리 및 운영 기능을 모두 제공하는 통합된 API 화면을 기반으로 합니다. **Azure Resource Manager**에 따라 리소스 이름은 항상 고유합니다. 따라서 리소스 이름에 대해 고유 식별자 문자열(예: GUID)을 사용할 수 있습니다. 

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Media Services v3 개요](media-services-overview.md)
