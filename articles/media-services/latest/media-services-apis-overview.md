---
title: V3 Api-Azure 사용 하 여 개발 | Microsoft Docs
description: 이 문서에서는 Media Services v3을 사용 하 여 개발 하는 경우 엔터티 및 Api에 적용 되는 규칙을 설명 합니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 04/08/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 18b72ceaee0ca0747a0bf2144d5f9ffddbee8b8c
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/11/2019
ms.locfileid: "59492144"
---
# <a name="developing-with-media-services-v3-apis"></a>V3 Api를 Media Services를 사용 하 여 개발

이 문서에서는 Media Services v3을 사용 하 여 개발 하는 경우 엔터티 및 Api에 적용 되는 규칙을 설명 합니다.

## <a name="naming-conventions"></a>명명 규칙

Azure Media Services v3 리소스 이름(예: 자산, 작업, 변환)은 Azure Resource Manager 명명 제약 조건에 따라 달라집니다. Azure Resource Manager에 따라 리소스 이름은 항상 고유합니다. 따라서 리소스 이름에 대해 고유 식별자 문자열(예: GUID)을 사용할 수 있습니다. 

Media Services 리소스 이름에는 '<', '>', '%', '&', ':', '&#92;', '?', '/', '*', '+', '.', 작은 따옴표 또는 제어 문자가 포함될 수 없습니다. 다른 문자를 모두 허용합니다. 리소스 이름의 최대 길이는 260자입니다. 

Azure Resource Manager 이름 지정에 대한 자세한 내용은 [명명 요구 사항](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/resource-api-reference.md#arguments-for-crud-on-resource) 및 [명명 규칙](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)을 참조하세요.

## <a name="v3-api-design-principles-and-rbac"></a>v3 API 디자인 원칙 및 RBAC

v3 API의 핵심 디자인 원칙 중 하나는 API를 더 안전하게 만드는 것입니다. v3 Api 반환 하지 않는 암호 또는 자격 증명에 **가져옵니다** 하거나 **목록** 작업 합니다. 키는 항상 null이거나, 비어 있거나, 응답에서 삭제됩니다. 사용자 암호 또는 자격 증명 얻기 위해 별도 작업 메서드를 호출 해야 합니다. 합니다 **판독기** Asset.ListContainerSas, StreamingLocator.ListContentKeys, ContentKeyPolicies.GetPolicyPropertiesWithSecrets 등의 작업을 호출할 수 있도록 역할 작업을 호출할 수 없습니다. 별도 작업이 필요 합니다. 원하는 경우 사용자 지정 역할을 더 세부적인 RBAC 보안 권한을 설정할 수 있습니다.

자세한 내용은 다음을 참조하세요.

- [기본 제공 역할 정의](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)
- [RBAC를 사용 하 여 액세스를 관리 하려면](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-rest)
- [Media Services 계정에 대 한 역할 기반 액세스 제어](rbac-overview.md)
- [콘텐츠 키 정책-.NET 받기](get-content-key-policy-dotnet-howto.md)합니다.

## <a name="long-running-operations"></a>장기 실행 작업

작업 표시 `x-ms-long-running-operation` Azure Media services에서 [swagger 파일](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/streamingservice.json) 긴 작업을 실행 합니다. 

Azure 비동기 작업을 추적 하는 방법에 대 한 자세한 내용은 참조 하세요 [비동기 작업](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations#monitor-status-of-operation)합니다.

Media Services에는 다음과 같은 장기 실행 작업에 있습니다.

* LiveEvent 만들기
* LiveEvent 업데이트
* LiveEvent 삭제
* LiveEvent 시작
* LiveEvent 중지
* LiveEvent 다시 설정
* LiveOutput 만들기
* LiveOutput 삭제
* StreamingEndpoint 만들기
* StreamingEndpoint 업데이트
* StreamingEndpoint 삭제
* StreamingEndpoint 시작
* StreamingEndpoint 중지
* StreamingEndpoint 크기 조정

## <a name="filtering-ordering-paging-of-media-services-entities"></a>Media Services 엔터티 필터링, 순서 지정, 페이징

참조 [필터링, 정렬, 페이징 Azure Media Services 엔터티](entities-overview.md)

## <a name="next-steps"></a>다음 단계

[SDK/도구를 사용하여 Media Services v3 API로 개발 시작](developers-guide.md)
