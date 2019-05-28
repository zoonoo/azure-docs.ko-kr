---
title: Azure Media Services의 스트리밍 로케이터 | Microsoft Docs
description: 이 문서에서는 스트리밍 로케이터의 개념과 Azure Media Services에서 이러한 로케이터를 사용하는 방법을 설명합니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 05/22/2019
ms.author: juliako
ms.openlocfilehash: 9a14399117971807c1d18f8eb5fab7d6e6cef2d5
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66120345"
---
# <a name="streaming-locators"></a>스트리밍 로케이터

출력 자산의 비디오를 재생할 클라이언트에 사용할 수 있도록 하려면 [스트리밍 로케이터](https://docs.microsoft.com/rest/api/media/streaminglocators)를 만든 다음, 스트리밍 URL을 빌드해야 합니다. .NET 샘플은 [스트리밍 로케이터 가져오기](stream-files-tutorial-with-api.md#get-a-streaming-locator)를 참조하세요.

**스트리밍 로케이터**를 만드는 과정을 게시라고 합니다. 기본적으로 **스트리밍 로케이터**는 API 호출을 수행한 직후부터 유효하며, 선택적인 시작 및 종료 시간을 구성하지 않는 한 삭제될 때까지 지속됩니다. 

만들 때를 **스트리밍 로케이터**를 지정 해야 합니다는 **자산** 이름 및 **스트리밍 정책** 이름입니다. 자세한 내용은 다음 항목을 참조하십시오.

* [Assets](assets-concept.md)
* [스트리밍 정책](streaming-policy-concept.md)
* [콘텐츠 키 정책](content-key-policy-concept.md)

> [!IMPORTANT]
> * 날짜/시간 형식의 **스트리밍 로케이터** 속성은 언제나 UTC 형식입니다.
> * Media Services 계정에 대한 제한된 정책 세트를 설계하고 동일한 옵션이 필요할 때마다 스트리밍 로케이터에 해당 세트를 다시 사용하는 것이 좋습니다. 자세한 내용은 [할당량 및 제한 사항](limits-quotas-constraints.md)을 참조하세요.

## <a name="associate-filters-with-streaming-locators"></a>스트리밍 로케이터를 사용 하 여 연결 필터

참조 [필터: 스트리밍 로케이터를 사용 하 여 연결](filters-concept.md#associate-filters-with-streaming-locator)합니다.

## <a name="filter-order-page-streaming-locator-entities"></a>필터, 순서 및 스트리밍 로케이터 엔터티 페이지

[Media Services 엔터티 필터링, 순서 지정, 페이징](entities-overview.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

[자습서: .NET](stream-files-tutorial-with-api.md)를 사용하여 비디오 업로드, 인코딩 및 스트림
