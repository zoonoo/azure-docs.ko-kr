---
title: Synapse 역할 기반 액세스 제어
description: Azure Synapse Analytics의 역할 기반 액세스 제어에 대해 설명 하는 문서입니다.
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: security
ms.date: 12/1/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 9e96d6decba679c7a4764a77f1e9720000faf78c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100105150"
---
# <a name="what-is-synapse-role-based-access-control-rbac"></a>Synapse 역할 기반 access control (RBAC) 이란?

Synapse RBAC는 Synapse 작업 영역 및 해당 콘텐츠에 대 한 [AZURE RBAC](../../role-based-access-control/overview.md) 의 기능을 확장 합니다. 

Azure RBAC는 Synapse 작업 영역 및 해당 SQL 풀, Apache Spark 풀 및 통합 런타임을 만들고, 업데이트 하거나, 삭제할 수 있는 사람을 관리 하는 데 사용 됩니다.

Synapse RBAC는 다음을 수행할 수 있는 사람을 관리 하는 데 사용 됩니다.
- 코드 아티팩트를 게시 하 고 게시 된 코드 아티팩트를 나열 하거나 액세스 합니다. 
- Apaches Spark 풀 및 통합 런타임에 코드 실행
- 자격 증명으로 보호 되는 연결 된 (데이터) 서비스 액세스 
- 작업 실행을 모니터링 하거나 취소 하 고, 작업 출력 및 실행 로그를 검토 합니다.  

>[!Note]
>Synapse RBAC는 게시 된 SQL 스크립트에 대 한 액세스를 관리 하는 데 사용 되지만 서버 리스 SQL 풀에 대 한 제한 된 액세스 제어만 제공 하며 전용 SQL 풀에 대 한 액세스를 제어 하는 데 사용 _되지_ 않습니다.  Sql 풀에 대 한 액세스는 주로 SQL 보안을 사용 하 여 제어 됩니다.

## <a name="what-can-i-do-with-synapse-rbac"></a>Synapse RBAC로 무엇을 할 수 있나요?

Synapse RBAC로 수행할 수 있는 작업의 몇 가지 예는 다음과 같습니다.
  - 사용자가 Apache Spark 된 노트북 및 작업에 대 한 변경 내용을 라이브 서비스에 게시할 수 있습니다.
  - 사용자가 특정 Apache Spark 풀에서 노트북 및 spark 작업을 실행 하 고 취소할 수 있도록 허용 합니다.
  - 사용자가 작업 영역 시스템 id로 보호 되는 파이프라인을 실행 하 고 자격 증명으로 보호 된 연결 된 서비스의 데이터에 액세스할 수 있도록 특정 자격 증명을 사용할 수 있습니다. 
  - 관리자가 특정 Spark 풀에서 작업 실행을 관리, 모니터링 및 취소할 수 있도록 허용 합니다.    

## <a name="how-synapse-rbac-works"></a>Synapse RBAC 작동 방법
Azure RBAC와 마찬가지로 Synapse RBAC는 역할 할당을 만드는 방식으로 작동 합니다. 역할 할당은 보안 주체, 역할 정의, 범위라는 세 가지 요소로 구성됩니다.  

### <a name="security-principals"></a>보안 주체

_보안 주체_ 는 사용자, 그룹, 서비스 주체 또는 관리 id입니다.

### <a name="roles"></a>역할
 
_역할_ 은 특정 리소스 형식 또는 아티팩트 형식에 대해 수행할 수 있는 사용 권한 또는 작업의 컬렉션입니다.

Synapse은 여러 가상 사용자의 요구 사항과 일치 하는 작업의 컬렉션을 정의 하는 기본 제공 역할을 제공 합니다.
- 관리자는 작업 영역을 만들고 구성 하기 위한 모든 권한을 얻을 수 있습니다. 
- 개발자는 SQL 스크립트, 노트북, 파이프라인 및 데이터 흐름를 만들고 업데이트 하 고 디버그할 수 있지만 프로덕션 계산 리소스/데이터에는이 코드를 게시 하거나 실행할 수 없습니다.
- 운영자는 코드 또는 실행 출력에 액세스 하지 않고도 시스템 상태, 응용 프로그램 실행 및 검토 로그를 모니터링 하 고 관리할 수 있습니다.
- 보안 담당자는 코드, 계산 리소스 또는 데이터에 액세스 하지 않고도 끝점을 관리 하 고 구성할 수 있습니다.

기본 제공 Synapse 역할에 [대해 자세히 알아보세요](./synapse-workspace-synapse-rbac-roles.md) . 

### <a name="scopes"></a>범위

_범위_ 는 액세스에 적용 되는 리소스 또는 아티팩트를 정의 합니다.  Synapse는 계층적 범위를 지원 합니다.  상위 수준 범위에서 부여 된 사용 권한은 하위 수준의 개체에 상속 됩니다.  Synapse RBAC에서 최상위 범위는 작업 영역입니다.  작업 영역 범위에 역할을 할당 하면 작업 영역에서 적용 가능한 모든 개체에 대 한 사용 권한을 부여 합니다.  

작업 영역 내에서 현재 지원 되는 범위는 Apache Spark 풀, Integration runtime, 연결 된 서비스 및 자격 증명입니다. 

작업 영역 범위에서 코드 아티팩트에 대 한 액세스 권한이 부여 됩니다.  작업 영역 내에서 아티팩트 컬렉션에 대 한 액세스 권한은 이후 릴리스에서 지원 될 예정입니다.

## <a name="resolving-role-assignments-to-determine-permissions"></a>역할 할당을 확인 하 여 사용 권한 확인

역할 할당은 지정 된 범위에서 역할이 정의한 권한을 보안 주체에 부여 합니다.

Synapse RBAC는 Azure RBAC와 같은 추가 모델입니다. 여러 역할을 단일 보안 주체 및 여러 범위에 할당할 수 있습니다. 보안 주체의 사용 권한을 계산할 때 시스템은 주 서버에 할당 된 모든 역할과 해당 보안 주체를 직접적 또는 간접적으로 포함 하는 그룹으로 간주 합니다.  또한 각 할당의 범위는 적용 되는 사용 권한을 결정 하는 것으로 간주 됩니다.  

## <a name="enforcing-assigned-permissions"></a>할당 된 권한 적용

Synapse Studio에서는 특정 단추나 옵션이 회색으로 표시 되거나 필요한 권한이 없는 경우 작업을 시도할 때 권한 오류가 반환 될 수 있습니다. 

단추나 옵션을 사용 하지 않도록 설정 하면 단추나 옵션을 마우스로 가리키면 필요한 권한이 있는 도구 설명이 표시 됩니다.  Synapse 관리자에 게 문의 하 여 필요한 사용 권한을 부여 하는 역할을 할당 합니다. [여기](./synapse-workspace-synapse-rbac-roles.md)에서 특정 작업을 제공 하는 역할을 볼 수 있습니다.

## <a name="who-can-assign-synapse-rbac-roles"></a>Synapse RBAC 역할을 할당할 수 있는 사람은 누구 인가요?

Synapse 관리자만 Synapse RBAC 역할을 할당할 수 있습니다.  작업 영역 수준에서 Synapse 관리자는 모든 범위에서 액세스 권한을 부여할 수 있습니다.  하위 수준 범위의 Synapse 관리자는 해당 범위 에서만 액세스 권한을 부여할 수 있습니다. 

새 작업 영역을 만들면 작업 영역 범위에서 작성자에 게 자동으로 Synapse 관리자 역할이 부여 됩니다.   

## <a name="where-do-i-manage-synapse-rbac"></a>Synapse RBAC는 어디에서 관리 하나요?

Synapse RBAC는 관리 허브의 액세스 제어 도구를 사용 하 여 Synapse Studio 내에서 관리 됩니다. 

## <a name="next-steps"></a>다음 단계

기본 제공 [SYNAPSE RBAC 역할](./synapse-workspace-synapse-rbac-roles.md)을 이해 합니다.

작업 영역에 대 한 [SYNAPSE RBAC 역할 할당을 검토 하는 방법](./how-to-review-synapse-rbac-role-assignments.md) 에 대해 알아봅니다.

[SYNAPSE RBAC 역할을 할당 하는 방법](./how-to-manage-synapse-rbac-role-assignments.md) 알아보기