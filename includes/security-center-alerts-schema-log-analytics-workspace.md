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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79538832"
---
### <a name="the-data-model-of-the-schema"></a>스키마의 데이터 모델

|필드|설명|
|----|----|
|**AlertName**|경고 표시 이름|
|**AlertType**|고유 경고 식별자|
|**신뢰 수준**|(선택 사항) 이 경고의 신뢰 수준(높음/낮음)|
|**신뢰도 점수**|(선택 사항) 보안 경고의 숫자 신뢰도 표시기|
|**설명**|경고에 대한 설명 텍스트|
|**DisplayName**|경고의 표시 이름|
|**Endtime**|경고의 영향 종료 시간(경고에 기여하는 마지막 이벤트의 시간)|
|**Entities**|경고와 관련된 엔터티 목록입니다. 이 목록에는 다양한 유형의 엔티티가 혼합된 것으로 만들 수 있습니다.|
|**확장 링크**|(선택 사항) 경고와 관련된 모든 링크에 대한 가방입니다. 이 가방은 다양한 유형에 대한 링크의 혼합물을 보유 할 수 있습니다|
|**확장 속성**|경고와 관련된 추가 필드 가방|
|**이스인시내**|경고가 인시던트 인지 일반 경고인지 확인합니다. 인시던트는 여러 경고를 하나의 보안 인시던트에 집계하는 보안 경고입니다.|
|**ProcessingEndTime**|경고가 생성된 UTC 타임스탬프|
|**제품 구성 요소 이름**|(선택 사항) 경고를 생성한 제품 내부의 구성 요소 이름입니다.|
|**ProductName**|상수('Azure 보안 센터')|
|**ProviderName**|unused|
|**수정 단계**|보안 위협을 시정하기 위해 취할 수동 작업 항목|
|**Resourceid**|영향을 받는 리소스의 전체 식별자|
|**심각도**|경고 심각도(높음/중간/낮음/정보)|
|**SourceComputerId**|영향을 받는 서버에 대한 고유한 GUID(서버에서 경고가 생성되는 경우)|
|**SourceSystem**|unused|
|**Starttime**|경고의 영향 시작 시간(경고에 기여하는 첫 번째 이벤트의 시간)|
|**시스템 경고ID**|이 보안 경고 인스턴스의 고유 식별자|
|**테넌트 ID**|검색된 리소스가 상주하는 구독의 상위 Azure Active 디렉터리 테넌트의 식별자|
|**TimeGenerated**|평가가 진행된 UTC 타임스탬프(보안 센터의 스캔 시간) (발견된 시간UTC와 동일)|
|**유형**|상수('보안 경고')|
|**공급업체 이름**|경고를 제공한 공급업체의 이름(예: 'Microsoft')|
|**벤더 오리지널아이드**|unused|
|**작업 공간리소스 그룹**|작업 영역에 보고하는 VM, 서버, 가상 시스템 규모 집합 또는 앱 서비스 인스턴스에서 경고가 생성되는 경우 해당 작업 영역 리소스 그룹 이름이 포함됩니다.|
|**작업 영역 구독ID**|작업 영역에 보고하는 VM, 서버, 가상 시스템 규모 집합 또는 앱 서비스 인스턴스에서 경고가 생성되는 경우 해당 작업 영역 구독Id를 포함합니다.|
|||
