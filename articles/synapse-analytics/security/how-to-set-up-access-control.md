---
title: Synapse 작업 영역 보호 (미리 보기)
description: 이 문서에서는 역할 및 액세스 제어를 사용 하 여 Synapse 작업 영역에서 작업을 제어 하 고 데이터에 액세스 하는 방법을 설명 합니다.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: mahi
ms.reviewer: jrasnick
ms.openlocfilehash: ae8be848b5d12e01865fe6bd3b394b460252aa3e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81606000"
---
# <a name="secure-your-synapse-workspace-preview"></a>Synapse 작업 영역 보호 (미리 보기)

이 문서에서는 역할 및 액세스 제어를 사용 하 여 작업을 제어 하 고 데이터에 액세스 하는 방법을 설명 합니다. 이러한 지침에 따라 Azure Synapse Analytics의 액세스 제어가 간소화 됩니다. 3 개의 보안 그룹 중 하나에 사용자를 추가 하 고 제거 하기만 하면 됩니다.

## <a name="overview"></a>개요

Synapse 작업 영역 (미리 보기)을 보호 하려면 다음 항목을 구성 하는 패턴을 따릅니다.

- Azure 역할 (예: 소유자, 참가자 등 기본 제공)
- Synapse roles – 이러한 역할은 Synapse에 고유 하며 Azure 역할을 기반으로 하지 않습니다. 다음 세 가지 역할이 있습니다.
  - Synapse 작업 영역 관리자
  - Synapse SQL 관리자
  - Synapse Spark 관리자
- ADLSGEN2 (Azure Data Lake Storage Gen 2)의 데이터에 대 한 액세스 제어.
- Synapse SQL 및 Spark 데이터베이스에 대 한 액세스 제어

## <a name="steps-to-secure-a-synapse-workspace"></a>Synapse 작업 영역을 보호 하는 단계

이 문서에서는 표준 이름을 사용 하 여 지침을 단순화 합니다. 원하는 이름으로 바꿉니다.

|설정 | 예제 값 | Description |
| :------ | :-------------- | :---------- |
| **Synapse 작업 영역** | WS1 |  Synapse 작업 영역에 포함 되는 이름입니다. |
| **ADLSGEN2 계정** | STG1 | 작업 영역에서 사용할 ADLS 계정입니다. |
| **컨테이너** | CNT1 | 작업 영역에서 STG1의 컨테이너는 기본적으로 사용 됩니다. |
| **Active directory 테 넌 트** | c:\contoso | active directory 테 넌 트 이름입니다.|
||||

## <a name="step-1-set-up-security-groups"></a>1 단계: 보안 그룹 설정

작업 영역에 대해 3 개의 보안 그룹을 만들고 채웁니다.

- **WS1\_wsadmins** – 작업 영역을 완벽 하 게 제어 해야 하는 사용자
- **WS1\_SparkAdmins** – 작업 영역의 Spark 측면을 완벽 하 게 제어 해야 하는 사용자를 위한 것입니다.
- **WS1\_sqladmins** – 작업 영역의 SQL 측면을 완벽 하 게 제어 해야 하는 사용자를 위한 것입니다.
- **WS1\_Sqladmins** 에 **\_WS1 wsadmins** 추가
- **WS1\_SparkAdmins** 에 **\_WS1 wsadmins** 추가

## <a name="step-2-prepare-your-data-lake-storage-gen2-account"></a>2 단계: Data Lake Storage Gen2 계정 준비

저장소에 대 한 다음 정보를 확인 합니다.

- 작업 영역에 사용할 ADLSGEN2 계정입니다. 이 문서에서는 STG1를 호출 합니다.  STG1은 작업 영역에 대 한 "기본" 저장소 계정으로 간주 됩니다.
- WS1 내의 컨테이너는 Synapse 작업 영역이 기본적으로 사용 합니다. 이 문서에서는 CNT1를 호출 합니다.  이 컨테이너는 다음에 사용 됩니다.
  - Spark 테이블의 지원 데이터 파일 저장
  - Spark 작업에 대 한 실행 로그

- Azure Portal를 사용 하 여 CNT1의 보안 그룹에 다음 역할을 할당 합니다.

  - **WS1\_Wsadmins** 를 **Storage Blob 데이터 참가자** 역할에 할당 합니다.
  - **\_WS1 SparkAdmins** 을 **저장소 Blob 데이터 참가자** 역할에 할당 합니다.
  - **WS1\_Sqladmins** 를 **Storage Blob 데이터 참가자** 역할에 할당 합니다.

## <a name="step-3-create-and-configure-your-synapse-workspace"></a>3 단계: Synapse 작업 영역 만들기 및 구성

Azure Portal에서 Synapse 작업 영역을 만듭니다.

- 작업 영역 이름 WS1
- 저장소 계정에 대해 STG1를 선택 합니다.
- "Filesystem"으로 사용 되는 컨테이너에 대해 CNT1를 선택 합니다.
- Synapse Studio에서 WS1 열기
- **관리** > **Access Control** 선택 하 여 보안 그룹을 다음 Synapse 역할에 할당 합니다.
  - Synapse 작업 영역 관리자에 **\_WS1 wsadmins** 할당
  - Synapse Spark 관리자에 **WS1\_SparkAdmins** 할당
  - Synapse SQL 관리자에 **\_WS1 sqladmins** 할당

## <a name="step-4-configuring-data-lake-storage-gen2-for-use-by-synapse-workspace"></a>4 단계: Synapse 작업 영역에서 사용 하도록 Data Lake Storage Gen2 구성

Synapse 작업 영역에는 파이프라인을 실행 하 고 시스템 작업을 수행할 수 있도록 STG1 및 CNT1에 대 한 액세스 권한이 필요 합니다.

- Azure 포털 열기
- STG1 찾기
- CNT1로 이동 합니다.
- WS1에 대 한 MSI (관리 서비스 ID)가 CNT1의 **Azure Blob 데이터 참가자** 역할에 할당 되어 있는지 확인 합니다.
  - 할당 됨이 표시 되지 않으면 할당 합니다.
  - MSI는 작업 영역과 동일한 이름을 갖습니다. 이 경우 &quot;WS1&quot;됩니다.

## <a name="step-5-configure-admin-access-for-sql-pools"></a>5 단계: SQL 풀에 대 한 관리자 액세스 구성

- Azure 포털 열기
- WS1로 이동 합니다.
- **설정**에서 **SQL Active Directory 관리자** 를 클릭 합니다.
- **관리자 설정** 을 클릭 하 고\_WS1 sqladmins를 선택 합니다.

## <a name="step-6-maintaining-access-control"></a>6 단계: 액세스 제어 유지 관리

구성이 완료 되었습니다.

이제 사용자에 대 한 액세스를 관리 하기 위해 세 가지 보안 그룹에 사용자를 추가 하 고 제거할 수 있습니다.

Synapse 역할에 사용자를 수동으로 할당할 수는 있지만 그럴 경우 일관 되 게 구성 되지 않습니다. 대신 보안 그룹에 사용자를 추가 하거나 제거 합니다.

## <a name="step-7-verify-access-for-users-in-the-roles"></a>7 단계: 역할의 사용자에 대 한 액세스 확인

각 역할의 사용자는 다음 단계를 완료 해야 합니다.

|   | 단계 | 작업 영역 관리자 | Spark 관리자 | SQL 관리자 |
| --- | --- | --- | --- | --- |
| 1 | CNT1에 parquet 파일 업로드 | YES | YES | YES |
| 2 | 주문형 SQL을 사용 하 여 parquet 파일 읽기 | YES | 아니요 | YES |
| 3 | Spark 풀 만들기 | 예 [1] | 예 [1] | 아니요  |
| 4 | 노트북을 사용 하 여 parquet 파일을 읽습니다. | YES | YES | 아니요 |
| 5 | 노트북에서 파이프라인을 만들고 파이프라인을 트리거하여 지금 실행 | YES | 아니요 | 아니요 |
| 6 | SQL 풀을 만들고 SELECT 1과 &quot;같은 sql 스크립트를 실행 합니다.&quot; | 예 [1] | 아니요 | 예 [1] |

> [!NOTE]
> [1] SQL 또는 Spark 풀을 만들려면 사용자에 게 Synapse 작업 영역에 대 한 참가자 역할이 하나 이상 있어야 합니다.
> [!TIP]
>
> - 일부 단계는 역할에 따라 의도적으로 허용 되지 않습니다.
> - 보안이 완전히 구성 되지 않은 경우 일부 작업이 실패할 수 있습니다. 이러한 작업은 표에 나와 있습니다.

## <a name="step-8-network-security"></a>8 단계: 네트워크 보안

작업 영역 방화벽, 가상 네트워크 및 [개인 링크](../../sql-database/sql-database-private-endpoint-overview.md)를 구성 합니다.

## <a name="step-9-completion"></a>9 단계: 완료

이제 작업 영역이 완전히 구성 되 고 보안이 유지 됩니다.

## <a name="how-roles-interact-with-synapse-studio"></a>역할이 Synapse Studio와 상호 작용 하는 방법

Synapse Studio는 사용자 역할에 따라 다르게 동작 합니다. 사용자가 적절 한 액세스 권한을 부여 하는 역할에 할당 되지 않은 경우 일부 항목이 숨겨지거나 사용 하지 않도록 설정 될 수 있습니다. 다음 표에는 Synapse Studio에 미치는 영향이 요약 되어 있습니다.

| 작업 | 작업 영역 관리자 | Spark 관리자 | SQL 관리자 |
| --- | --- | --- | --- |
| Synapse Studio 열기 | YES | YES | YES |
| 홈 허브 보기 | YES | YES | YES |
| 데이터 허브 보기 | YES | YES | YES |
| 데이터 허브/연결 된 ADLSGen2 계정 및 컨테이너 참조 | 예 [1] | 예 [1] | 예 [1] |
| 데이터 허브/데이터베이스 참조 | YES | YES | YES |
| 데이터 허브/데이터베이스의 개체 참조 | YES | YES | YES |
| SQL 풀 데이터베이스의 데이터 허브/액세스 데이터 | YES   | 아니요   | YES   |
| SQL 주문형 데이터베이스의 데이터 허브/데이터 액세스 | 예 [2]  | 아니요  | 예 [2]  |
| Spark 데이터베이스의 데이터 허브/액세스 데이터 | 예 [2] | 예 [2] | 예 [2] |
| 개발 허브 사용 | YES | YES | YES |
| 허브/작성자 SQL 스크립트 개발 | YES | 아니요 | YES |
| 허브/작성자 Spark 작업 정의 개발 | YES | YES | 아니요 |
| Hub/author 노트북 개발 | YES | YES | 아니요 |
| Hub/author 데이터 흐름 개발 | YES | 아니요 | 아니요 |
| 오케스트레이션 허브 사용 | YES | YES | YES |
| 허브/사용 파이프라인 오케스트레이션 | YES | 아니요 | 아니요 |
| 관리 허브 사용 | YES | YES | YES |
| 허브/SQL 풀 관리 | YES | 아니요 | YES |
| 허브/Spark 풀 관리 | YES | YES | 아니요 |
| 허브/트리거 관리 | YES | 아니요 | 아니요 |
| 허브/연결 된 서비스 관리 | YES | YES | YES |
| 허브/Access Control 관리 (Synapse 작업 영역 역할에 사용자 할당) | YES | 아니요 | 아니요 |
| 허브/통합 런타임 관리 | YES | YES | YES |

> [!NOTE]
> [1] 컨테이너의 데이터에 대 한 액세스는 ADLSGen2 [2] SQL OD 테이블 및 Spark 테이블의 액세스 제어에 따라 ADLSGen2에 데이터를 저장 하 고 ADLSGen2에 대 한 적절 한 권한이 있어야 합니다.

## <a name="next-steps"></a>다음 단계

[Synapse 작업 영역](../quickstart-create-workspace.md) 만들기
