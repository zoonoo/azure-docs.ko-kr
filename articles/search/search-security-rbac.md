---
title: Azure 관리 권한을 위한 Azure 역할 설정
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search 관리에 대한 관리 작업을 제어 및 위임하기 위한 Azure Portal의 Azure RBAC(Azure 역할 기반 관리 제어)입니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/16/2020
ms.openlocfilehash: ee122727100ec0abad0dfe93b9e5f1be0276cb8e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92519502"
---
# <a name="set-azure-roles-for-administrative-access-to-azure-cognitive-search"></a>Azure Cognitive Search에 대한 관리 권한을 위한 Azure 역할 설정

Azure에서는 포털 또는 Resource Manager API를 통해 관리되는 모든 서비스에 대해 [전역 역할 기반 권한 부여 모델](../role-based-access-control/role-assignments-portal.md) 을 제공합니다. 소유자, 참가자 및 읽기 권한자 역할은 Active Directory 사용자, 그룹 및 각 역할에 할당된 보안 주체에 대한 *서비스 관리* 수준을 결정합니다. 

> [!Note]
> 서비스의 콘텐츠를 보호하기 위한 Azure RBAC(Azure 역할 기반 액세스 제어)는 없습니다. 서비스 자체에 대한 인증된 요청에는 관리자 API 키 또는 쿼리 API 키를 사용합니다. 검색 결과에 대한 ID 기반 액세스의 경우 보안 필터를 만들어서 ID를 기준으로 결과를 잘라 요청자에게 액세스 권한이 없어야 하는 문서를 제거할 수 있습니다. 자세한 내용은 [보안 필터](search-security-trimming-for-azure-search.md)를 참조하세요.

## <a name="management-tasks-by-role"></a>역할별 관리 작업

Azure Cognitive Search의 경우 역할은 다음 관리 작업을 지원하는 사용 권한 수준과 관련이 있습니다.

| 역할 | Task |
| --- | --- |
| 소유자 |api-key, 인덱스, 인덱서, 인덱서 데이터 원본 및 인덱서 일정 등 서비스 또는 해당 서비스의 개체를 만들거나 삭제합니다.<p>개수 및 스토리지 크기를 포함하여 서비스 상태를 봅니다.<p>역할 멤버 자격을 추가하거나 삭제합니다(소유자만 역할 멤버 자격을 관리할 수 있음).<p>구독 관리자 및 서비스 소유자는 소유자 역할의 자동 멤버 자격을 갖습니다. |
| 참가자 | Azure 역할 관리를 제외하고 소유자와 같은 수준의 액세스 권한입니다. 예를 들어, 참여자는 개체를 만들거나 삭제하거나, [api-keys](search-security-api-keys.md)를 보고 다시 생성할 수 있지만 역할 멤버 자격을 수정할 수는 없습니다.<br><br>[Search Service 기여자](../role-based-access-control/built-in-roles.md#search-service-contributor)는 일반 기여자 기본 제공 역할에 해당합니다. |
| 판독기 |서비스 엔드포인트, 구독, 리소스 그룹, 지역, 계층 및 용량과 같은 서비스 필수 요소를 봅니다. 모니터링 탭에서 초당 평균 쿼리와 같은 서비스 메트릭을 볼 수도 있습니다. 이 역할의 멤버는 인덱스, 인덱서, 데이터 원본 또는 기술 세트 정보는 볼 수 없습니다. 여기에는 서비스에 있는 인덱스 수와 같은 해당 개체에 대한 사용량 데이터가 포함됩니다. |

역할은 서비스 엔드포인트에 대한 액세스 권한을 부여하지 않습니다. 인덱스 관리, 인덱스 채우기 및 검색 데이터 쿼리와 같은 Search 서비스 작업은 역할이 아니라 api-key를 통해 제어합니다. 자세한 내용은 [api-key 관리](search-security-api-keys.md)를 참조하세요.

## <a name="permissions-table"></a>권한 테이블

다음 표에서는 Azure Cognitive Search에서 허용되는 작업 및 특정 작업에 대한 액세스의 잠금을 해제하는 키를 요약합니다.

Azure RBAC 권한은 포털 작업 및 서비스 관리(서비스 또는 해당 API 키 만들기, 삭제 또는 변경)에 적용됩니다. API 키는 서비스가 존재하고 서비스의 콘텐츠 작업에 적용된 후에 생성됩니다. 또한 포털의 콘텐츠 관련 작업(예: 개체 생성 또는 삭제)의 경우 Azure RBAC 소유자 또는 기여자가 묵시적 관리자 API 키를 사용하여 서비스와 상호 작용합니다.

| 작업 | 제어 |
|-----------|-------------------------|
| 서비스 만들기 | Azure RBAC 권한: 소유자 또는 기여자 |
| 서비스 크기 조정 | Azure RBAC 권한: 소유자 또는 기여자|
| 서비스 삭제 | Azure RBAC 권한: 소유자 또는 기여자 |
| 관리자 또는 쿼리 키 관리 | Azure RBAC 권한: 소유자 또는 기여자|
| 포털 또는 관리 API에서 서비스 정보 보기 | Azure RBAC 권한: 소유자, 기여자 또는 읽기 권한자  |
| 포털 또는 관리 API에서 개체 정보 및 메트릭 보기 | Azure RBAC 권한: 소유자 또는 기여자 |
| 서비스에서 개체를 만들고, 수정하고, 삭제합니다. <br>인덱스 및 구성 요소 부분(분석기 정의, 점수 매기기 프로필, CORS 옵션 포함), 인덱서, 데이터 원본, 동의어, 확인기 | 포털을 사용하는 경우 API, Azure RBAC 소유자 또는 기여자를 사용할 때 관리자 키 |
| 인덱스 쿼리 | 포털을 사용하는 경우 API, Azure RBAC 소유자 또는 기여자를 사용할 때 관리자 또는 쿼리 키 |
| 통계, 개수 및 개체의 목록 반환 등 개체에 대한 시스템 정보 쿼리 | 포털을 사용하는 경우 API, Azure RBAC 소유자 또는 기여자를 사용할 때 관리자 키 |

## <a name="next-steps"></a>다음 단계

+ [Powershell을 사용하여 관리](search-manage-powershell.md) 
+ [Azure Cognitive Search의 성능 및 최적화](search-performance-optimization.md)
+ [Azure RBAC(Azure 역할 기반 액세스 제어)란?](../role-based-access-control/overview.md)
