---
title: Synapse RBAC 역할
description: 이 문서에서는 기본 제공 Synapse RBAC 역할에 대해 설명 합니다.
author: billgib
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: security
ms.date: 12/1/2020
ms.author: billgib
ms.reviewer: jrasnick
ms.openlocfilehash: bae214fb8eff9747bf3f79323d8eb9ce1cb6cdb7
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2020
ms.locfileid: "96531601"
---
# <a name="synapse-rbac-roles"></a>Synapse RBAC 역할

이 문서에서는 기본 제공 되는 Synapse RBAC 역할, 부여 하는 사용 권한 및 사용 될 수 있는 범위에 대해 설명 합니다.  

## <a name="whats-changed-since-the-preview"></a>Preview 이후 변경 된 내용
미리 보기 중에 제공 된 Synapse RBAC 역할에 익숙한 사용자의 경우 다음 변경 내용이 적용 됩니다.
- 작업 영역 관리자의 이름이 **Synapse Administrator** 로 바뀜
- Apache Spark 관리자는 **관리자 Apache Spark Synapse** 이름이 바뀌고 SQL 스크립트를 포함 하 여 게시 된 모든 코드 아티팩트를 볼 수 있는 권한이 있습니다.  이 역할은 더 이상 Synapse Credential 사용자 역할이 필요한 작업 영역 MSI를 사용할 수 있는 권한을 부여 하지 않습니다.  파이프라인을 실행 하려면이 권한이 필요 합니다. 
- SQL 관리자는 **Sql 관리자** 의 이름을 Synapse, Spark 노트북 및 작업을 포함 하 여 게시 된 모든 코드 아티팩트를 볼 수 있는 권한이 있습니다.  이 역할은 더 이상 Synapse Credential 사용자 역할이 필요한 작업 영역 MSI를 사용할 수 있는 권한을 부여 하지 않습니다. 파이프라인을 실행 하려면이 권한이 필요 합니다.
- 구체적 분석 런타임이 아닌 개발 및 운영 사용자를 지 원하는 데 중점을 둔 **새로운 세부적인 SYNAPSE RBAC 역할이** 도입 되었습니다.  
- **새 하위 수준 범위** 는 여러 역할에 대해 도입 됩니다.  이러한 범위를 통해 특정 리소스나 개체로 역할을 제한할 수 있습니다.

>[!Note]
>- **새 역할 및 하위 수준 범위는 현재 미리 보기** 상태입니다.  이러한 새로운 역할 및 범위를 사용 하 여 완전 하 게 지원 되 고 사용에 대 한 피드백을 제공 하는 것이 좋습니다.

## <a name="built-in-synapse-rbac-roles-and-scopes"></a>기본 제공 Synapse RBAC 역할 및 범위

다음 표에서는 기본 제공 역할 및 해당 역할을 사용할 수 있는 범위에 대해 설명 합니다.

>[!Note]
> 모든 범위에서 Synapse RBAC 역할을 가진 사용자는 작업 영역 범위에 Synapse 사용자 역할을 자동으로 포함 합니다. 

|역할 |사용 권한|범위|
|---|---|-----|
|Synapse 관리자  |서버를 사용 하지 않는 SQL 풀, Apache Spark 풀 및 통합 런타임에 대 한 전체 Synapse 액세스.  게시 된 모든 코드 아티팩트에 대 한 만들기, 읽기, 업데이트 및 삭제 액세스 권한을 포함 합니다.  작업 영역 시스템 id 자격 증명에 대 한 계산 연산자, 연결 된 Data Manager 및 자격 증명 사용자 권한이 포함 되어 있습니다.  Synapse RBAC 역할 할당을 포함 합니다.  계산 리소스를 만들고, 삭제 하 고, 관리 하려면 Azure 권한이 필요 합니다. </br></br>_아티팩트를 읽고 쓸 수 있는 </br> 작업은 Spark 작업에 대 한 모든 작업을 수행할 수 있습니다. </br> Spark 풀 로그 보기는 </br> 저장 된 노트북을 볼 수 있고, 파이프라인 출력 </br> 은 연결 된 서비스에서 저장 한 암호를 사용할 수 있습니다. 또는 자격 증명은 </br> sql,, 및 사용 권한이 있는 sql server가 아닌 끝점에 연결할 수 있습니다. `db_datareader` `db_datawriter` `connect` `grant` </br> 현재 범위에서 Synapse RBAC 역할을 할당 하 고_|작업 영역 </br> Spark 풀<br/>통합 런타임 </br>연결된 서비스</br>자격 증명 |
|Synapse Apache Spark 관리자</br>|Apache Spark 풀에 대 한 전체 Synapse 액세스.  게시 된 Spark 작업 정의, 노트북 및 해당 출력, 라이브러리, 연결 된 서비스 및 자격 증명에 대 한 액세스를 만들고, 읽고, 업데이트 하 고, 삭제 합니다.  다른 모든 게시 된 코드 아티팩트에 대 한 읽기 액세스를 포함 합니다. 자격 증명을 사용 하 고 파이프라인을 실행할 수 있는 권한을 포함 하지 않습니다. 액세스 부여는 포함 하지 않습니다. </br></br>_Spark 아티팩트의 모든 작업을 수행할 수 있습니다. </br> spark 작업에 대 한 모든 작업을 수행할 수 있습니다._|작업 영역</br>Spark 풀|
|Synapse SQL 관리자|서버를 사용 하지 않는 SQL 풀에 대 한 전체 Synapse 액세스.  게시 된 SQL 스크립트, 자격 증명 및 연결 된 서비스에 대 한 액세스를 만들고, 읽고, 업데이트 하 고, 삭제 합니다.  다른 모든 게시 된 코드 아티팩트에 대 한 읽기 액세스를 포함 합니다.  자격 증명을 사용 하 고 파이프라인을 실행할 수 있는 권한을 포함 하지 않습니다. 액세스 부여는 포함 하지 않습니다. </br></br>*Sql 스크립트에 대 한 모든 작업을 수행할 수 있습니다 sql <br/> `db_datareader` , `db_datawriter` , `connect` 및 `grant` 사용 권한을 사용 하 여 sql server가 아닌 끝점에 연결할 수 있습니다.*|작업 영역|
|Synapse 기여자|서버를 사용 하지 않는 SQL 풀, Apache Spark 풀, 통합 런타임에 대 한 전체 Synapse 액세스.  에는 자격 증명 및 연결 된 서비스를 비롯 하 여 게시 된 모든 코드 아티팩트와 해당 출력에 대 한 만들기, 읽기, 업데이트 및 삭제 권한이 포함 되어 있습니다.  Compute 연산자 사용 권한을 포함 합니다. 자격 증명을 사용 하 고 파이프라인을 실행할 수 있는 권한을 포함 하지 않습니다. 액세스 부여는 포함 하지 않습니다. </br></br>_아티팩트를 읽고 쓸 수 있는 경우 </br> 저장 된 노트북 및 파이프라인 출력을 볼 수 있습니다. </br> spark 작업에 대 한 모든 작업은 </br> spark 풀 로그를 볼 수 있습니다._|작업 영역 </br> Spark 풀<br/> 통합 런타임|
|Synapse 아티팩트 게시자|게시 된 코드 아티팩트와 해당 출력에 대 한 만들기, 읽기, 업데이트 및 삭제 권한이 있습니다. 에는 코드 또는 파이프라인을 실행 하거나 액세스 권한을 부여할 수 있는 권한이 포함 되지 않습니다. </br></br>_게시 된 아티팩트를 읽고 아티팩트를 게시 하 여 </br> 저장 된 노트북, Spark 작업 및 파이프라인 출력을 볼 수 있음_|작업 영역
|Synapse 아티팩트 사용자|게시 된 코드 아티팩트 및 해당 출력에 대 한 읽기 액세스입니다. 새 아티팩트를 만들 수 있지만 추가 권한 없이는 변경 내용을 게시 하거나 코드를 실행할 수 없습니다.|작업 영역
|Synapse Compute 연산자 |Spark 작업 및 노트북을 제출 하 고 로그를 봅니다.  모든 사용자가 제출한 Spark 작업 취소를 포함 합니다. 파이프라인을 실행 하 고 파이프라인 실행 및 출력을 보기 위해 작업 영역 시스템 id에 대 한 추가 자격 증명 사용 권한이 필요 합니다. </br></br>_다른 사용자가 제출한 작업을 비롯 하 여 작업을 제출 하 고 취소할 </br> 수 있습니다._|작업 영역</br>Spark 풀</br>통합 런타임|
|Synapse 자격 증명 사용자|파이프라인 실행과 같은 활동에서 자격 증명 및 연결 된 서비스 내에서 암호의 런타임 및 구성 시간 사용. 파이프라인을 실행 하려면 작업 영역 시스템 id로 범위가 지정 된이 역할이 필요 합니다. </br></br>_자격 증명으로 범위가 지정 되어 자격 증명으로 보호 되는 연결 된 서비스를 통해 데이터에 액세스할 수 있도록 허용 합니다 (compute 사용 권한 필요). </br> 작업 영역 시스템 id 자격 증명으로 보호 되는 파이프라인 (추가 계산 사용 권한 포함)을 실행할 수 있습니다._|작업 영역 </br>연결된 서비스</br>자격 증명
|Synapse 연결 된 Data Manager|관리 되는 개인 끝점, 연결 된 서비스 및 자격 증명의 생성 및 관리 자격 증명으로 보호 되는 연결 된 서비스를 사용 하는 관리 개인 끝점을 만들 수 있습니다.|작업 영역|
|Synapse 사용자|SQL 풀, Apache Spark 풀, 통합 런타임 및 게시 된 연결 된 서비스와 자격 증명에 대 한 세부 정보를 나열 하 고 표시 합니다. 다른 게시 된 코드 아티팩트를 포함 하지 않습니다.  새 아티팩트를 만들 수 있지만 추가 권한 없이 실행 하거나 게시할 수 없습니다.</br></br>_Spark 풀 (통합 런타임)을 나열 하 고 읽을 수 있습니다._|작업 영역, Spark 풀</br>연결된 서비스 </br>자격 증명|

## <a name="synapse-rbac-roles-and-the-actions-they-permit"></a>Synapse RBAC 역할 및 허용 되는 작업

>[!Note]
>- 아래 표에 나열 된 모든 작업에는 "Synapse/..." 접두사가 붙습니다.</br>
>- 모든 아티팩트 읽기, 쓰기 및 삭제 작업은 라이브 서비스의 게시 된 아티팩트와 관련 됩니다.  이러한 권한은 연결 된 Git 리포지토리의 아티팩트에 대 한 액세스에 영향을 주지 않습니다.  

다음 표에서는 기본 제공 역할 및 각 역할에서 지 원하는 작업/사용 권한을 보여 줍니다.

역할|동작
--|--
Synapse 관리자|작업 영역/읽기</br>작업 영역/역할 할당/쓰기, 삭제</br>workspace/managedPrivateEndpoint/write, delete</br>작업 영역/bigDataPools/useCompute/작업</br>작업 영역/bigDataPools/viewLogs/작업</br>작업 영역/integrationRuntimes/useCompute/작업</br>작업 영역/아티팩트/읽기</br>작업 영역/노트북/쓰기, 삭제</br>workspace/sparkJobDefinitions/write, delete</br>workspace/sqlScripts/write, delete</br>workspace/데이터 흐름/write, delete</br>작업 영역/파이프라인/쓰기, 삭제</br>작업 영역/트리거/쓰기, 삭제</br>작업 영역/데이터 집합/쓰기, 삭제</br>작업 영역/라이브러리/쓰기, 삭제</br>workspace/Linkedservices.json 및 datasets.json/write, delete</br>작업 영역/자격 증명/쓰기, 삭제</br>작업 영역/노트북/viewOutputs/작업</br>작업 영역/파이프라인/viewOutputs/작업</br>workspace/Linkedservices.json 및 datasets.json/useSecret/작업</br>작업 영역/자격 증명/기능/작업|
|Synapse Apache Spark 관리자|작업 영역/읽기</br>작업 영역/bigDataPools/useCompute/작업</br>작업 영역/bigDataPools/viewLogs/작업</br>작업 영역/노트북/viewOutputs/작업</br>작업 영역/아티팩트/읽기</br>작업 영역/노트북/쓰기, 삭제</br>workspace/sparkJobDefinitions/write, delete</br>작업 영역/라이브러리/쓰기, 삭제</br>workspace/Linkedservices.json 및 datasets.json/write, delete</br>작업 영역/자격 증명/쓰기, 삭제|
|Synapse SQL 관리자|작업 영역/읽기</br>작업 영역/아티팩트/읽기</br>workspace/sqlScripts/write, delete</br>workspace/Linkedservices.json 및 datasets.json/write, delete</br>작업 영역/자격 증명/쓰기, 삭제|
|Synapse 기여자|작업 영역/읽기</br>작업 영역/bigDataPools/useCompute/작업</br>작업 영역/bigDataPools/viewLogs/작업</br>작업 영역/integrationRuntimes/useCompute/작업</br>작업 영역/integrationRuntimes/viewLogs/작업</br>작업 영역/아티팩트/읽기</br>작업 영역/노트북/쓰기, 삭제</br>workspace/sparkJobDefinitions/write, delete</br>workspace/sqlScripts/write, delete</br>workspace/데이터 흐름/write, delete</br>작업 영역/파이프라인/쓰기, 삭제</br>작업 영역/트리거/쓰기, 삭제</br>작업 영역/데이터 집합/쓰기, 삭제</br>작업 영역/라이브러리/쓰기, 삭제</br>workspace/Linkedservices.json 및 datasets.json/write, delete</br>작업 영역/자격 증명/쓰기, 삭제</br>작업 영역/노트북/viewOutputs/작업</br>작업 영역/파이프라인/viewOutputs/작업|
|Synapse 아티팩트 게시자|작업 영역/읽기</br>작업 영역/아티팩트/읽기</br>작업 영역/노트북/쓰기, 삭제</br>workspace/sparkJobDefinitions/write, delete</br>workspace/sqlScripts/write, delete</br>workspace/데이터 흐름/write, delete</br>작업 영역/파이프라인/쓰기, 삭제</br>작업 영역/트리거/쓰기, 삭제</br>작업 영역/데이터 집합/쓰기, 삭제</br>작업 영역/라이브러리/쓰기, 삭제</br>workspace/Linkedservices.json 및 datasets.json/write, delete</br>작업 영역/자격 증명/쓰기, 삭제</br>작업 영역/노트북/viewOutputs/작업</br>작업 영역/파이프라인/viewOutputs/작업|
|Synapse 아티팩트 사용자|작업 영역/읽기</br>작업 영역/아티팩트/읽기</br>작업 영역/노트북/viewOutputs/작업</br>작업 영역/파이프라인/viewOutputs/작업|
|Synapse Compute 연산자 |작업 영역/읽기</br>작업 영역/bigDataPools/useCompute/작업</br>작업 영역/bigDataPools/viewLogs/작업</br>작업 영역/integrationRuntimes/useCompute/작업</br>작업 영역/integrationRuntimes/viewLogs/작업|
|Synapse 자격 증명 사용자|작업 영역/읽기</br>workspace/Linkedservices.json 및 datasets.json/useSecret/작업</br>작업 영역/자격 증명/기능/작업|
|Synapse 연결 된 Data Manager|작업 영역/읽기</br>workspace/managedPrivateEndpoint/write, delete</br>workspace/Linkedservices.json 및 datasets.json/write, delete</br>작업 영역/자격 증명/쓰기, 삭제|
|Synapse 사용자|작업 영역/읽기|

## <a name="synapse-rbac-actions-and-the-roles-that-permit-them"></a>Synapse RBAC 작업 및이를 허용 하는 역할

다음 표에서는 Synapse 작업 및 이러한 작업을 허용 하는 기본 제공 역할을 보여 줍니다.

작업|역할
--|--
작업 영역/읽기|Synapse 관리자</br>Synapse Apache Spark 관리자</br>Synapse SQL 관리자</br>Synapse 기여자</br>Synapse 아티팩트 게시자</br>Synapse 아티팩트 사용자</br>Synapse Compute 연산자 </br>Synapse 자격 증명 사용자</br>Synapse 연결 된 Data Manager</br>Synapse 사용자 
작업 영역/역할 할당/쓰기, 삭제|Synapse 관리자
workspace/managedPrivateEndpoint/write, delete|Synapse 관리자</br>Synapse 연결 된 Data Manager
작업 영역/bigDataPools/useCompute/작업|Synapse 관리자</br>Synapse Apache Spark 관리자</br>Synapse 기여자</br>Synapse Compute 연산자 
작업 영역/bigDataPools/viewLogs/작업|Synapse 관리자</br>Synapse Apache Spark 관리자</br>Synapse 기여자</br>Synapse Compute 연산자 
작업 영역/integrationRuntimes/useCompute/작업|Synapse 관리자</br>Synapse 기여자</br>Synapse Compute 연산자 
작업 영역/아티팩트/읽기|Synapse 관리자</br>Synapse Apache Spark 관리자</br>Synapse SQL 관리자</br>Synapse 기여자</br>Synapse 아티팩트 게시자</br>Synapse 아티팩트 사용자
작업 영역/노트북/쓰기, 삭제|Synapse 관리자</br>Synapse Apache Spark 관리자</br>Synapse 기여자</br>Synapse 아티팩트 게시자
workspace/sparkJobDefinitions/write, delete|Synapse 관리자</br>Synapse Apache Spark 관리자</br>Synapse 기여자</br>Synapse 아티팩트 게시자
workspace/sqlScripts/write, delete|Synapse 관리자</br>Synapse SQL 관리자</br>Synapse 기여자</br>Synapse 아티팩트 게시자
workspace/데이터 흐름/write, delete|Synapse 관리자</br>Synapse 기여자</br>Synapse 아티팩트 게시자
작업 영역/파이프라인/쓰기, 삭제|Synapse 관리자</br>Synapse 기여자</br>Synapse 아티팩트 게시자
작업 영역/트리거/쓰기, 삭제|Synapse 관리자</br>Synapse 기여자</br>Synapse 아티팩트 게시자
작업 영역/데이터 집합/쓰기, 삭제|Synapse 관리자</br>Synapse 기여자</br>Synapse 아티팩트 게시자
작업 영역/라이브러리/쓰기, 삭제|Synapse 관리자</br>Synapse Apache Spark 관리자</br>Synapse 기여자</br>Synapse 아티팩트 게시자
workspace/Linkedservices.json 및 datasets.json/write, delete|Synapse 관리자</br>Synapse 기여자</br>Synapse 아티팩트 게시자</br>Synapse 연결 된 Data Manager
작업 영역/자격 증명/쓰기, 삭제|Synapse 관리자</br>Synapse 기여자</br>Synapse 아티팩트 게시자</br>Synapse 연결 된 Data Manager
작업 영역/노트북/viewOutputs/작업|Synapse 관리자</br>Synapse Apache Spark 관리자</br>Synapse 기여자</br>Synapse 아티팩트 게시자</br>Synapse 아티팩트 사용자
작업 영역/파이프라인/viewOutputs/작업|Synapse 관리자</br>Synapse 기여자</br>Synapse 아티팩트 게시자</br>Synapse 아티팩트 사용자
workspace/Linkedservices.json 및 datasets.json/useSecret/작업|Synapse 관리자</br>Synapse 자격 증명 사용자
작업 영역/자격 증명/기능/작업|Synapse 관리자</br>Synapse 자격 증명 사용자

## <a name="synapse-rbac-scopes-and-their-supported-roles"></a>Synapse RBAC 범위 및 지원 되는 역할

아래 표에서는 Synapse RBAC 범위와 각 범위에서 할당할 수 있는 역할을 나열 합니다. 

>[!note]
>개체를 만들거나 삭제 하려면 상위 수준 범위에서 사용 권한이 있어야 합니다.

Scope|역할
--|--
작업 영역 |Synapse 관리자</br>Synapse Apache Spark 관리자</br>Synapse SQL 관리자</br>Synapse 기여자</br>Synapse 아티팩트 게시자</br>Synapse 아티팩트 사용자</br>Synapse Compute 연산자 </br>Synapse 자격 증명 사용자</br>Synapse 연결 된 Data Manager</br>Synapse 사용자
Apache Spark 풀 | Synapse 관리자 </br>Synapse 기여자 </br> Synapse Compute 연산자
통합 런타임 | Synapse 관리자 </br>Synapse 기여자 </br> Synapse Compute 연산자
연결된 서비스 |Synapse 관리자 </br>Synapse 자격 증명 사용자
자격 증명 |Synapse 관리자 </br>Synapse 자격 증명 사용자
 
>[!note]
>모든 아티팩트 역할 및 작업은 작업 영역 수준에서 범위가 지정 됩니다. 

## <a name="next-steps"></a>다음 단계

작업 영역에 대 한 [SYNAPSE RBAC 역할 할당을 검토 하는 방법](./how-to-review-synapse-rbac-role-assignments.md) 에 대해 알아봅니다.

[SYNAPSE RBAC 역할을 할당 하는 방법](./how-to-manage-synapse-rbac-role-assignments.md) 알아보기