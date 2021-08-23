---
title: Azure 역할을 통해 액세스 권한 부여
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search 관리에 대한 관리 작업을 제어 및 위임하기 위한 Azure Portal의 Azure RBAC(Azure 역할 기반 관리 제어)입니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 05/28/2021
ms.openlocfilehash: 4e79af03f4545e4af799c660314212d18dbc91ff
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110459967"
---
# <a name="authorize-access-through-azure-roles-in-azure-cognitive-search"></a>Azure Cognitive Search의 Azure 역할을 통해 액세스 권한 부여

Azure에서는 포털 또는 Resource Manager API를 통해 관리되는 모든 서비스에 대해 [전역 역할 기반 권한 부여 모델](../role-based-access-control/role-assignments-portal.md) 을 제공합니다. 권한 부여 모델은 소유자, 기여자 및 읽기 권한자 역할을 부여해 Active Directory 사용자, 그룹 및 각 역할에 할당된 보안 주체에 대한 *서비스 관리* 수준을 결정합니다. Cognitive Search는 이러한 세 가지 역할을 사용하여 검색 서비스 관리에 대한 액세스 권한을 부여합니다.

Cognitive Search는 다음을 지원하지 않습니다.

+ [사용자 지정 역할](../role-based-access-control/custom-roles.md).
+ 인덱스 만들기 또는 쿼리하기와 같은 콘텐츠 관련 작업, 또는 서비스의 기타 개체에 대한 Azure RBAC(역할 기반 액세스 제어)

  콘텐츠 작업을 수행하기 위한 권한 부여에는 [관리자 API 키 또는 쿼리 API 키](search-security-api-keys.md)가 필요합니다.

> [!Note]
> 검색 결과(간혹 행 수준의 보안으로 나타남)에 대한 ID 기반 액세스의 경우 보안 필터를 만들어서 ID를 기준으로 결과를 잘라 요청자에게 액세스 권한이 없어야 하는 문서를 제거할 수 있습니다. 자세한 내용은 [보안 필터](search-security-trimming-for-azure-search.md)를 참조하세요.

## <a name="roles-used-in-cognitive-search"></a>Cognitive Search에 사용되는 역할

Azure Cognitive Search의 경우 역할은 다음 관리 작업을 지원하는 사용 권한 수준과 관련이 있습니다.

| 역할 | Task |
| --- | --- |
| 소유자 |서비스 만들기 또는 삭제 API 키, 인덱스, 동의어 맵, 인덱서, 인덱서 데이터 원본 및 기술 세트 등의 서비스에서 개체를 만들거나 업데이트하거나 삭제합니다. </br></br>포털 또는 관리 REST API, Azure PowerShell 또는 Azure CLI를 통해 노출되는 모든 서비스 정보에 대한 모든 권한입니다. </br></br>역할 멤버 자격을 할당합니다.</br></br>구독 관리자 및 서비스 소유자는 소유자 역할의 자동 멤버 자격을 갖습니다. |
| 참가자 | 역할 할당을 제외하고 소유자와 같은 수준의 액세스 권한입니다. [Search Service 기여자](../role-based-access-control/built-in-roles.md#search-service-contributor)는 일반 기여자 기본 제공 역할에 해당합니다. |
| 판독기 | 부분적으로 서비스 정보에 대한 액세스가 제한됩니다. 포털에서 읽기 권한자 역할은 서비스 개요 페이지, Essentials 섹션 및 모니터링 탭의 정보에 액세스할 수 있습니다. 모든 기타 탭 및 페이지는 제한이 해제되어 있습니다. </br></br>Essentials 섹션 아래에 리소스 그룹, 상태, 위치, 구독 이름 및 ID, 태그, URL, 가격 책정 계층, 복제본, 파티션 및 검색 단위가 있습니다. </br></br>모니터링 탭에서 검색 대기 시간, 제한 요청의 백분율, 초당 평균 쿼리 등 서비스 메트릭을 확인합니다. </br></br>사용 탭(스토리지, 서비스에서 만든 인덱스 또는 인덱서 수) 또는 인덱스, 인덱서, 데이터 원본, 기술 세트 또는 디버그 세션 탭의 정보에 액세스할 수 없습니다. |

역할은 서비스 엔드포인트에 대한 액세스 권한을 부여하지 않습니다. 인덱스 관리, 인덱스 채우기 및 검색 데이터 쿼리와 같은 검색 서비스 작업은 역할이 아니라 API 키를 통해 제어합니다. 자세한 정보는 [API 키 관리](search-security-api-keys.md)를 참조하세요.

## <a name="tasks-and-permission-requirements"></a>작업 및 권한 요구 사항

다음 테이블에는 Azure Cognitive Search에서 허용되는 작업과 특정 작업 액세스를 잠금 해제하는 역할 또는 키가 요약되어 있습니다.

+ Azure RBAC 권한은 포털 작업 및 서비스 관리(서비스 또는 그 API 키의 제작, 삭제 또는 변경)에 적용됩니다.

+ API 키는 서비스가 존재하고 서비스의 콘텐츠 작업에 적용된 후에 생성됩니다.

또한 개체 생성 또는 삭제와 같은 포털의 콘텐츠 관련 작업의 경우 명시적 역할 멤버 자격(소유자 또는 참가자)과 포털의 내부 관리자 키 사용을 통해 모든 작업 및 정보에 대한 모든 권한을 지원합니다. 즉, 포털에서 인덱스를 만들거나 로드하는 경우 RBAC 멤버 자격을 통해 페이지 액세스 권한을 제공하지만 포털 자체는 관리자 키를 사용하여 서비스에서 작업을 인증합니다.

| 작업 | 제어 |
|-----------|-------------------------|
| 서비스 만들기 또는 삭제 | Azure RBAC 권한: 소유자 또는 기여자 |
| 네트워크 보안 구성(IP 방화벽) | Azure RBAC 권한: 소유자 또는 기여자 |
| 개인 엔드포인트를 만들거나 삭제 | Azure RBAC 권한: 소유자 또는 기여자 |
| 고객 관리 키 구현 | Azure RBAC 권한: 소유자 또는 기여자 |
| 복제본 또는 파티션 조정 | Azure RBAC 권한: 소유자 또는 기여자|
| 관리자 또는 쿼리 키 관리 | Azure RBAC 권한: 소유자 또는 기여자|
| 포털 또는 관리 API에서 서비스 정보 보기 | Azure RBAC 권한: 소유자, 기여자 또는 읽기 권한자  |
| 포털 또는 관리 API에서 개체 정보 및 메트릭 보기 | Azure RBAC 권한: 소유자 또는 기여자 |
| 서비스에서 개체를 만들고, 수정하고, 삭제합니다. <br>인덱스 및 구성 요소 부분(분석기 정의, 점수 매기기 프로필, CORS 옵션 포함), 인덱서, 데이터 원본, 기술 세트, 동의어, 제안기 | API를 사용하는 경우 관리자 키, 포털을 사용하는 경우 Azure RBAC 소유자 또는 기여자 |
| 인덱스 쿼리 | API를 사용하는 경우 관리자 또는 쿼리 키, 포털을 사용하는 경우 Azure RBAC 소유자 또는 기여자 |
| 통계, 개수 및 개체의 목록 반환 등 개체에 대한 시스템 정보 쿼리 | API를 사용하는 경우 관리자 키, 포털을 사용하는 경우 Azure RBAC 소유자 또는 기여자 |

## <a name="next-steps"></a>다음 단계

+ [Powershell을 사용하여 관리](search-manage-powershell.md) 
+ [Azure Cognitive Search의 성능 및 최적화](search-performance-optimization.md)
+ [Azure RBAC(Azure 역할 기반 액세스 제어)란?](../role-based-access-control/overview.md)
