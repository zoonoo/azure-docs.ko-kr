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
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "79538832"
---
### <a name="the-data-model-of-the-schema"></a>스키마의 데이터 모델

|필드|Description|
|----|----|
|**AlertName**|경고 표시 이름|
|**AlertType**|고유한 경고 식별자|
|**ConfidenceLevel**|(선택 사항) 이 경고의 신뢰 수준(높음/낮음)|
|**ConfidenceScore**|(선택 사항) 보안 경고의 숫자 신뢰도 표시기|
|**설명**|경고에 대한 설명 텍스트|
|**표시 이름**|경고의 표시 이름|
|**EndTime**|경고의 영향 종료 시간(경고에 기여하는 마지막 이벤트 시간)|
|**엔터티**|경고와 관련된 엔터티의 목록입니다. 이 목록에는 다양한 유형의 엔터티가 혼합되어 있을 수 있습니다.|
|**ExtendedLinks**|(선택 사항) 경고와 관련된 모든 링크에 대한 모음. 이 모음에는 다양한 유형의 엔터티가 혼합되어 있을 수 있습니다.|
|**ExtendedProperties**|경고와 관련된 추가 필드의 모음|
|**IsIncident**|경고가 인시던트인지 아니면 정기 경고인지 결정합니다. 인시던트는 여러 경고를 하나의 보안 인시던트로 집계하는 보안 경고입니다.|
|**ProcessingEndTime**|경고가 생성된 UTC 타임스탬프|
|**ProductComponentName**|(선택 사항) 경고를 생성한 제품 내부의 구성 요소 이름.|
|**ProductName**|상수('Azure Security Center')|
|**ProviderName**|unused|
|**RemediationSteps**|보안 위협을 수정하기 위해 수행하는 수동 작업 항목|
|**ResourceId**|영향을 받는 리소스의 전체 식별자|
|**심각도**|경고 심각도(높음/보통/낮음/정보)|
|**SourceComputerId**|영향을 받는 서버의 고유 GUID(경고가 서버에서 생성된 경우)|
|**SourceSystem**|unused|
|**StartTime**|경고의 영향 시작 시간(경고에 기여하는 첫 번째 이벤트 시간)|
|**SystemAlertId**|이 보안 경고 인스턴스의 고유 식별자|
|**TenantId**|검색된 리소스가 있는 구독의 부모 Azure Active Directory 테넌트의 식별자|
|**TimeGenerated**|평가가 발생한 UTC 타임스탬프(Security Center의 검색 시간)(DiscoveredTimeUTC와 동일)|
|**형식**|상수('SecurityAlert')|
|**VendorName**|경고를 제공한 공급업체의 이름(예: 'Microsoft')|
|**VendorOriginalId**|unused|
|**WorkspaceResourceGroup**|VM, 서버, Virtual Machine Scale Set 또는 작업 영역에 보고하는 App Service 인스턴스에서 경고가 생성되는 경우 해당 작업 영역 리소스 그룹 이름 포함|
|**WorkspaceSubscriptionId**|VM, 서버, Virtual Machine Scale Set 또는 작업 영역에 보고하는 App Service 인스턴스에서 경고가 생성되는 경우 해당 작업 영역 subscriptionId 포함|
|||
