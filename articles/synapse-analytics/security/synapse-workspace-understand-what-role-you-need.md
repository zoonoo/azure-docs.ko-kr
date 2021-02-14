---
title: Synapse에서 일반적인 작업을 수행 하는 데 필요한 역할 이해
description: 이 문서에서는 특정 작업을 수행 하는 데 필요한 기본 제공 Synapse RBAC 역할에 대해 설명 합니다.
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: security
ms.date: 12/1/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: ba00e6f4fe97b0614483fd04ddee9fc768558db5
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/10/2021
ms.locfileid: "100102107"
---
# <a name="understand-the-roles-required-to-perform-common-tasks-in-synapse"></a>Synapse에서 일반적인 작업을 수행 하는 데 필요한 역할 이해

이 문서는 Synapse Studio에서 작업을 수행 하는 데 필요한 Synapse RBAC (역할 기반 액세스 제어) 또는 Azure RBAC 역할을 이해 하는 데 도움이 됩니다.  

## <a name="synapse-studio-access-control-and-workflow-summary"></a>Synapse Studio access control 및 워크플로 요약 

### <a name="accessing-synapse-studio-and-viewing-its-content"></a>Synapse Studio에 액세스 및 콘텐츠 보기

- Synapse RBAC 역할이 할당 되었거나 작업 영역에 대 한 Azure 소유자, 참가자 또는 읽기 권한자 역할이 있는 경우 Synapse Studio를 열고 작업 영역의 세부 정보를 보고 해당 Azure 리소스 (SQL 풀, Spark 풀 또는 통합 런타임)를 나열할 수 있습니다.

### <a name="resource-management"></a>리소스 관리

- 작업 영역에서 Azure 소유자 또는 참가자 인 경우 SQL 풀, Apache Spark 풀 및 통합 런타임을 만들 수 있습니다.
- 작업 영역 또는 해당 리소스에 대 한 Azure 소유자 또는 참가자 인 경우 전용 SQL 풀을 일시 중지 하거나 크기를 조정 하 고, Spark 풀 또는 통합 런타임을 구성할 수 있습니다.

### <a name="viewing-and-editing-code-artifacts"></a>코드 아티팩트 보기 및 편집

- Synapse Studio에 액세스 하면 SQL 스크립트, 노트북, spark 작업, 연결 된 서비스, 파이프라인, 데이터 흐름, 트리거 및 자격 증명과 같은 새 코드 아티팩트를 만들 수 있습니다.  이러한 아티팩트는 추가 권한으로 게시 하거나 저장할 수 있습니다.  
- Synapse 아티팩트 사용자 인 경우 Synapse 아티팩트 게시자, Synapse 참여자 또는 Synapse 관리자는 이미 게시 된 코드 아티팩트를 나열 하 고, 열고, 편집할 수 있습니다.

### <a name="executing-your-code"></a>코드 실행

- Sql 풀에 필요한 SQL 권한이 정의 되어 있는 경우 sql 풀에서 sql 스크립트를 실행할 수 있습니다.  
- 작업 영역 또는 특정 Apache Spark 풀에 대해 Synapse Compute 연산자 권한이 있는 경우 노트북 및 Spark 작업을 실행할 수 있습니다.  
- 작업 영역 또는 특정 통합 런타임에 대 한 Compute 연산자 권한과 적절 한 자격 증명 권한을 사용 하 여 파이프라인을 실행할 수 있습니다.

### <a name="monitoring-and-managing-execution"></a>실행 모니터링 및 관리
- Synapse 사용자 인 경우 Apache Spark 풀에서 실행 중인 노트북 및 작업 상태를 검토할 수 있습니다.
- 작업 영역 또는 특정 Spark 풀 또는 파이프라인에 대 한 Synapse Compute 연산자 인 경우 로그를 검토 하 고 실행 중인 작업과 파이프라인을 취소할 수 있습니다.  

### <a name="publishing-and-saving-your-code"></a>코드 게시 및 저장

- Synapse 아티팩트 게시자, Synapse 참여자 또는 Synapse 관리자 인 경우 서비스에 새 코드 아티팩트 또는 업데이트 된 코드 아티팩트를 게시할 수 있습니다. 
- 작업 영역에서 git를 사용할 수 있고 Git 권한이 있는 경우 Git 리포지토리의 작업 분기에 코드 아티팩트를 커밋할 수 있습니다. Git를 사용 하도록 설정 하면 공동 작업 분기 에서만 게시를 수행할 수 있습니다.
- 코드 아티팩트에 대 한 변경 내용을 게시 하거나 커밋하지 않고 Synapse Studio를 닫으면 변경 내용이 손실 됩니다.


## <a name="tasks-and-required-roles"></a>작업 및 필수 역할

아래 표에는 각 작업, Synapse RBAC 또는 필요한 Azure RBAC 역할에 대 한 일반적인 작업이 나와 있습니다.  

>[!Note]
>- Synapse 관리자는 필요한 권한을 제공 하는 유일한 역할이 아닌 한 각 작업에 대해 나열 되지 않습니다.  Synapse 관리자는 다른 Synapse RBAC 역할에서 사용 하도록 설정 된 모든 작업을 수행할 수 있습니다.</br>
>- 필요한 최소 Synapse RBAC 역할이 표시 됩니다.
>- 모든 범위에서 모든 Synapse RBAC 역할은 작업 영역에서 사용자에 게 Synapse 사용자 권한을 제공 합니다.
>- 테이블에 표시 된 모든 Synapse RBAC 권한/작업은 Microsoft/Synapse/작업 영역을 접두사로 사용 합니다. </br>


작업 (...) |역할 (다음을 수행 해야 합니다.|Synapse RBAC 권한/작업
--|--|--
|작업 영역에서 Synapse Studio 열기|Synapse 사용자 또는|읽기
| |작업 영역의 Azure 소유자, 참가자 또는 읽기 권한자|없음
|SQL 풀, Apache Spark 풀, 통합 런타임 나열 및 구성 세부 정보 액세스|Synapse 사용자 또는|읽기|
||작업 영역의 Azure 소유자, 참가자 또는 읽기 권한자|없음
|연결 된 서비스, 자격 증명, 관리 되는 개인 끝점 나열|Synapse 사용자|읽기
SQL 풀|
전용 SQL 풀 또는 서버 리스 SQL 풀 만들기|작업 영역의 Azure 소유자 또는 참가자|없음
전용 SQL 풀 관리 (일시 중지, 크기 조정 또는 삭제)|SQL 풀 또는 작업 영역의 Azure 소유자 또는 참가자|없음
SQL 스크립트 만들기</br>|Synapse 사용자 또는 </br>작업 영역의 Azure 소유자 또는 참가자 </br>*Sql 스크립트를 실행 하거나 게시 하거나 변경 내용을 커밋하는 추가 SQL 권한이 필요* 합니다.|
게시 된 SQL 스크립트를 나열 하 고 엽니다.| Synapse 아티팩트 사용자, 아티팩트 게시자, Synapse 기여자|아티팩트/읽기
서버를 사용 하지 않는 SQL 풀에서 SQL 스크립트 실행|풀에 대 한 SQL 사용 권한 (Synapse 관리자에 게 자동으로 부여 됨)|없음
전용 SQL 풀에서 SQL 스크립트 실행|풀에 대 한 SQL 사용 권한|없음
업데이트 또는 삭제 된 새 SQL 스크립트 게시|Synapse 아티팩트 게시자, Synapse 기여자|sqlScripts/write, delete
Git 리포지토리에 대 한 SQL 스크립트 변경 내용 커밋|리포지토리에 대 한 Git 권한이 필요 합니다.|
작업 영역에서 Active Directory 관리자 할당 (Azure Portal의 작업 영역 속성을 통해)|작업 영역의 Azure 소유자 또는 참가자 |
APACHE SPARK 풀|
Apache Spark 풀 만들기|작업 영역의 Azure 소유자 또는 참가자|
응용 프로그램 Apache Spark 모니터링| Synapse 사용자|읽기
노트북 및 작업 실행 로그 보기 |Synapse Compute 연산자|
Apache Spark 풀에서 실행 중인 모든 노트북 또는 Spark 작업 취소|Apache Spark 풀에서 계산 연산자를 Synapse 합니다.|bigDataPools/useCompute
노트북 또는 작업 정의 만들기|Synapse 사용자 또는 </br>작업 영역의 Azure 소유자, 참가자 또는 읽기 권한자</br> *변경 내용을 실행, 게시 또는 커밋하려면 추가 사용 권한이 필요 합니다.*|읽기</br></br></br></br></br> 
저장 된 출력 검토를 포함 하 여 게시 된 노트북 또는 작업 정의를 나열 하 고 엽니다.|작업 영역에서 Synapse 아티팩트 사용자, Synapse 아티팩트 게시자, Synapse 기여자|아티팩트/읽기
노트북을 실행 하 고 해당 출력을 검토 합니다.|선택한 Apache Spark 풀에서 Synapse Apache Spark Administrator, Synapse Compute 연산자|bigDataPools/useCompute 
노트북 또는 작업 정의 (출력 포함)를 서비스에 게시 하거나 삭제 합니다.|작업 영역의 아티팩트 게시자 Synapse Apache Spark 관리자|노트북/쓰기, 삭제
Git 리포지토리에 대 한 노트북 또는 작업 정의 변경 내용 커밋|Git 권한|없음
파이프라인, 통합 런타임, 데이터 흐름, 데이터 집합 & 트리거|
Integration runtime 만들기, 업데이트 또는 삭제|작업 영역의 Azure 소유자 또는 참가자|
통합 런타임 상태 모니터링|Synapse 사용자|읽기, 파이프라인/viewOutputs
파이프라인 실행 검토|Synapse 아티팩트 게시자/Synapse 기여자|읽기, 파이프라인/viewOutputs 
파이프라인 만들기 |Synapse 사용자</br>*디버깅, 트리거 추가, 게시 또는 변경 내용 커밋에 대 한 추가 Synapse 권한이 필요 합니다.*|읽기
데이터 흐름 또는 데이터 집합 만들기 |Synapse 사용자</br>*변경 내용을 게시 하거나 커밋하려면 추가 Synapse 권한이 필요 합니다.*|읽기
게시 된 파이프라인 나열 및 열기 |Synapse 아티팩트 사용자 | 아티팩트/읽기
데이터 집합 데이터 미리 보기|WorkspaceSystemIdentity의 Synapse User + Synapse Credential User| 
기본 통합 런타임을 사용 하 여 파이프라인 디버그|Synapse 사용자 + Synapse 자격 증명 사용자 (WorkspaceSystemIdentity 자격 증명)|읽음 </br>자격 증명/
지금 트리거를 포함 하 여 트리거 만들기 (파이프라인을 실행할 수 있는 권한 필요)|WorkspaceSystemIdentity의 Synapse User + Synapse Credential User|읽기, 자격 증명/작업/작업
파이프라인 실행/실행|WorkspaceSystemIdentity의 Synapse User + Synapse Credential User|읽기, 자격 증명/작업/작업
데이터 복사 도구를 사용 하 여 데이터 복사|작업 영역 시스템 Id의 Synapse User + Synapse Credential User|읽기, 자격 증명/작업/작업
데이터 수집 (일정 사용)|Synapse Author + Synapse 자격 증명 사용자 (작업 영역 시스템 Id)|읽기, 자격 증명/작업/작업
새, 업데이트 또는 삭제 된 파이프라인, 데이터 흐름 또는 트리거를 서비스에 게시|작업 영역에서 아티팩트 게시자 Synapse|파이프라인/쓰기, 삭제</br>데이터 흐름/write, delete</br>트리거/쓰기, 삭제
파이프라인, 데이터 흐름, 데이터 집합 또는 트리거에 대 한 변경 내용을 Git 리포지토리로 커밋 |Git 권한|없음 
연결 된 서비스|
연결 된 서비스 만들기 (자격 증명 할당 포함)|Synapse 사용자</br>*자격 증명을 사용 하 여 연결 된 서비스를 사용 하거나 변경 내용을 게시 하거나 커밋하려면 추가 권한이 필요 합니다.*|읽기
게시 된 연결 된 서비스 나열 및 열기|Synapse 아티팩트 사용자|Linkedservices.json 및 datasets.json/write, delete  
자격 증명으로 보호 되는 연결 된 서비스에 대 한 연결 테스트|Synapse User + Synapse Credential User|자격 증명/작업/작업|
연결 된 서비스 게시|Synapse 아티팩트 게시자, Synapse 연결 된 Data Manager|Linkedservices.json 및 datasets.json/write, delete
Git 리포지토리에 연결 된 서비스 정의 커밋|Git 권한|없음
액세스 관리|
모든 범위에서 Synapse RBAC 역할 할당을 검토 합니다.|Synapse 사용자|읽기
사용자, 그룹 및 서비스 사용자에 대 한 Synapse RBAC 역할 할당 할당 및 제거| 작업 영역 또는 특정 작업 영역 항목 범위에서 관리자 Synapse|roleAssignments/write, delete 

>[!Note]
>다른 테 넌 트의 게스트 사용자는 할당 된 역할에 관계 없이 역할 할당을 검토, 추가 또는 변경할 수 없습니다. 

## <a name="next-steps"></a>다음 단계

[SYNAPSE RBAC 역할 할당을 검토 하는 방법](./how-to-review-synapse-rbac-role-assignments.md) 알아보기

[SYNAPSE RBAC 역할 할당을 관리 하는 방법](./how-to-manage-synapse-rbac-role-assignments.md)에 대해 알아봅니다. 