---
title: 포함 파일
description: 포함 파일
services: data-factory
author: memildin
ms.service: data-factory
ms.topic: include
ms.date: 03/17/2020
ms.author: memildin
ms.custom: include file
ms.openlocfilehash: 7d81799f7fbdb2b41db421daa1a85ec8cde511eb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "79538832"
---
### <a name="the-data-model-of-the-schema"></a>스키마의 데이터 모델

|필드|설명|
|----|----|
|**AlertName**|경고 표시 이름|
|**AlertType**|고유한 경고 식별자|
|**ConfidenceLevel**|필드 이 경고의 신뢰도 수준 (높음/낮음)입니다.|
|**ConfidenceScore**|필드 보안 경고의 숫자 신뢰도 표시기|
|**설명**|경고에 대 한 설명 텍스트|
|**표시 이름**|경고의 표시 이름입니다.|
|**EndTime**|경고의 영향 종료 시간 (경고에 영향을 주는 마지막 이벤트의 시간)|
|**엔터티**|경고와 관련 된 엔터티의 목록입니다. 이 목록은 다양 한 형식의 여러 엔터티를 포함할 수 있습니다.|
|**ExtendedLinks 링크**|필드 경고와 관련 된 모든 링크에 대 한 모음입니다. 이 모음에는 다양 한 형식에 대 한 링크 조합이 포함 될 수 있습니다.|
|**ExtendedProperties**|경고와 관련 된 추가 필드 모음|
|**IsIncident**|경고가 인시던트 인지 아니면 정기 경고 인지를 결정 합니다. 인시던트는 여러 경고를 하나의 보안 인시던트에 집계 하는 보안 경고입니다.|
|**ProcessingEndTime**|경고가 생성 된 UTC 타임 스탬프|
|**ProductComponentName**|필드 경고를 생성 한 제품 내의 구성 요소 이름입니다.|
|**ProductName**|상수 (' Azure Security Center ')|
|**ProviderName**|unused|
|**RemediationSteps**|보안 위협을 수정 하기 위해 수행할 수동 작업 항목|
|**ResourceId**|영향을 받는 리소스의 전체 식별자|
|**심각도**|경고 심각도 (높음/보통/낮음/정보)|
|**SourceComputerId**|영향을 받는 서버의 고유 GUID (경고가 서버에서 생성 된 경우)|
|**SourceSystem**|unused|
|**StartTime**|경고의 영향 시작 시간 (경고에 영향을 주는 첫 번째 이벤트의 시간)|
|**SystemAlertId**|이 보안 경고 인스턴스의 고유 식별자|
|**TenantId**|검색 된 리소스가 있는 구독의 부모 Azure Active directory 테 넌 트 식별자입니다.|
|**TimeGenerated**|평가가 발생 한 UTC 타임 스탬프 (Security Center의 검색 시간) (DiscoveredTimeUTC와 동일)|
|**형식**|상수 (' SecurityAlert ')|
|**이름의**|경고를 제공한 공급 업체의 이름 (예: ' Microsoft ')|
|**VendorOriginalId**|unused|
|**WorkspaceResourceGroup**|VM, 서버, 가상 머신 확장 집합 또는 작업 영역에 보고 하는 App Service 인스턴스에서 경고가 생성 되는 경우 해당 작업 영역 리소스 그룹 이름이 포함 됩니다.|
|**WorkspaceSubscriptionId**|VM, 서버, 가상 머신 확장 집합 또는 작업 영역에 보고 하는 App Service 인스턴스에서 경고가 생성 되는 경우 해당 작업 영역 subscriptionId가 포함 됩니다.|
|||
