---
title: Synapse 작업 영역(미리 보기) 보호
description: 이 문서에서는 역할 및 액세스 제어를 사용하여 Synapse 작업 영역에서 작업을 제어하고 데이터에 액세스하는 방법을 설명합니다.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: mahi
ms.reviewer: jrasnick
ms.openlocfilehash: d02cd12552b3664dd7acaae0142fc939ee57f5f6
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83591984"
---
# <a name="secure-your-synapse-workspace-preview"></a>Synapse 작업 영역(미리 보기) 보호

이 문서에서는 역할 및 액세스 제어를 사용하여 작업을 제어하고 데이터에 액세스하는 방법을 설명합니다. 이러한 지침을 따르면 Azure Synapse Analytics에서 액세스를 간단하게 제어할 수 있습니다. 세 가지 보안 그룹 중 하나에 사용자를 추가하고 제거하기만 하면 됩니다.

## <a name="overview"></a>개요

Synapse 작업 영역(미리 보기)을 보호하려면 다음 항목을 구성하는 패턴을 따릅니다.

- Azure 역할(예: 소유자, 기여자 등의 기본 역할)
- Synapse 역할 - 이러한 역할은 Synapse에 고유하며 Azure 역할을 기반으로 하지 않습니다. 이러한 역할로는 다음과 같은 세 가지가 있습니다.
  - Synapse 작업 영역 관리자
  - Synapse SQL 관리자
  - Azure Synapse Analytics용 Apache Spark 관리자
- ADLSGEN2(Azure Data Lake Storage Gen 2)의 데이터 액세스 제어
- Synapse SQL 및 Spark 데이터베이스에 대한 액세스 제어

## <a name="steps-to-secure-a-synapse-workspace"></a>Synapse 작업 영역을 보호하는 단계

이 문서에서는 지침을 단순화하기 위해 표준 이름을 사용합니다. 표준 이름을 원하는 이름으로 바꾸세요.

|설정 | 예제 값 | Description |
| :------ | :-------------- | :---------- |
| **Synapse 작업 영역** | WS1 |  Synapse 작업 영역의 이름입니다. |
| **ADLSGEN2 계정** | STG1 | 작업 영역에 사용할 ADLS 계정입니다. |
| **컨테이너** | CNT1 | 작업 영역에서 기본적으로 사용하게 될 STG1의 컨테이너입니다. |
| **Active Directory 테넌트** | contoso | Active Directory 테넌트 이름입니다.|
||||

## <a name="step-1-set-up-security-groups"></a>1단계: 보안 그룹 설정

작업 영역에 대한 다음 세 가지 보안 그룹을 만들고 정보를 채웁니다.

- **WS1\_WSAdmins** – 작업 영역을 완벽하게 제어해야 하는 사용자를 위한 보안 그룹입니다.
- **WS1\_SparkAdmins** – 작업 영역의 Spark 측면을 완벽하게 제어해야 하는 사용자를 위한 보안 그룹입니다.
- **WS1\_SQLAdmins** – 작업 영역의 SQL 측면을 완벽하게 제어해야 하는 사용자를 위한 보안 그룹입니다.
- **WS1\_WSAdmins**를 **WS1\_SQLAdmins**에 추가합니다.
- **WS1\_WSAdmins**를 **WS1\_SparkAdmins**에 추가합니다.

## <a name="step-2-prepare-your-data-lake-storage-gen2-account"></a>2단계: Data Lake Storage Gen2 계정을 준비합니다.

스토리지에 대한 다음 정보를 확인합니다.

- 작업 영역에 사용할 ADLSGEN2 계정. 이 문서에서는 STG1이라고 하겠습니다.  STG1은 작업 영역의 "기본" 스토리지 계정으로 간주됩니다.
- Synapse 작업 영역에서 기본적으로 사용할 WS1 내의 컨테이너. 이 문서에서는 CNT1이라고 하겠습니다.  이 컨테이너는 다음 용도로 사용됩니다.
  - Spark 테이블의 지원 데이터 파일 저장
  - Spark 작업의 실행 로그

- Azure Portal을 사용하여 CNT1의 보안 그룹에 다음 역할을 할당합니다.

  - **WS1\_WSAdmins**를 **Storage Blob 데이터 기여자** 역할에 할당합니다.
  - **WS1\_SparkAdmins**를 **Storage Blob 데이터 기여자** 역할에 할당합니다.
  - **WS1\_SQLAdmins**를 **Storage Blob 데이터 기여자** 역할에 할당합니다.

## <a name="step-3-create-and-configure-your-synapse-workspace"></a>3단계: Synapse 작업 영역 만들기 및 구성

Azure Portal에서 다음과 같이 Synapse 작업 영역을 만듭니다.

- 작업 영역 WS1의 이름 지정
- 스토리지 계정으로 STG1 선택
- "파일 시스템"으로 사용할 컨테이너로 CNT1 선택
- Synapse Studio에서 WS1 열기
- **관리** > **액세스 제어**를 선택하고 다음 Synapse 역할에 보안 그룹 할당
  - **WS1\_WSAdmins**를 Synapse 작업 영역 관리자에게 할당
  - **WS1\_SparkAdmins**를 Synapse Spark 관리자에게 할당
  - **WS1\_SQLAdmins**를 Synapse SQL 관리자에게 할당

## <a name="step-4-configuring-data-lake-storage-gen2-for-use-by-synapse-workspace"></a>4단계: Synapse 작업 영역에서 사용할 Data Lake Storage Gen2 구성

Synapse 작업 영역에서 파이프라인을 실행하고 시스템 작업을 수행할 수 있도록 STG1 및 CNT1에 대한 액세스 권한이 필요합니다.

- Azure 포털 열기
- STG1 찾기
- CNT1으로 이동
- WS1의 MSI(관리되는 서비스 ID)가 CNT1의 **Storage Blob 데이터 기여자** 역할에 할당되었는지 확인
  - 할당되지 않았으면 할당합니다.
  - MSI의 이름이 작업 영역과 동일합니다. 이 경우 이름은 &quot;WS1&quot;입니다.

## <a name="step-5-configure-admin-access-for-sql-pools"></a>5단계: SQL 풀에 대한 관리자 액세스 구성

- Azure 포털 열기
- WS1으로 이동
- **설정**에서 **SQL Active Directory 관리자** 클릭
- **관리자 설정**을 클릭하고 WS1\_SQLAdmins 선택

## <a name="step-6-maintaining-access-control"></a>6단계: 액세스 제어 유지 관리

구성이 완료되었습니다.

이제 세 가지 보안 그룹에 사용자를 추가하고 제거하여 사용자의 액세스 권한을 관리할 수 있습니다.

Synapse 역할에 사용자를 수동으로 할당할 수 있지만, 이 경우 구성이 일관적이지 않게 됩니다. 수동으로 할당하지 말고, 보안 그룹에 사용자를 추가 또는 제거하기만 하세요.

## <a name="step-7-verify-access-for-users-in-the-roles"></a>7단계: 역할에서 사용자 액세스 확인

각 역할의 사용자는 다음 단계를 완료해야 합니다.

|   | 단계 | 작업 영역 관리자 | Spark 관리자 | SQL 관리자 |
| --- | --- | --- | --- | --- |
| 1 | CNT1에 parquet 파일 업로드 | YES | YES | YES |
| 2 | SQL 주문형을 사용하여 parquet 파일 읽기 | YES | 아니요 | YES |
| 3 | Spark 풀 만들기 | 예 [1] | 예 [1] | 아니요  |
| 4 | Notebook으로 parquet 파일 읽기 | YES | YES | 아니요 |
| 5 | Notebook에서 파이프라인을 만들고 파이프라인을 트리거하여 지금 실행 | YES | 아니요 | 아니요 |
| 6 | SQL 풀을 만들고 &quot;SELECT 1&quot; 같은 SQL 스크립트 | 예 [1] | 아니요 | 예[1] |

> [!NOTE]
> [1] SQL 또는 Spark 풀을 만들려면 사용자에게 적어도 Synapse 작업 영역에 대한 기여자 역할이 있어야 합니다.
> [!TIP]
>
> - 일부 단계는 역할에 따라 의도적으로 허용되지 않습니다.
> - 보안이 완전히 구성되지 않으면 일부 작업이 실패할 수 있습니다. 다음 표에 이러한 작업이 나열되어 있습니다.

## <a name="step-8-network-security"></a>8단계: 네트워크 보안

작업 영역 방화벽, 가상 네트워크 및 [Private Link](../../sql-database/sql-database-private-endpoint-overview.md)를 구성합니다.

## <a name="step-9-completion"></a>9단계: Completion

작업 영역이 완전히 구성되었으며 보안이 설정되었습니다.

## <a name="how-roles-interact-with-synapse-studio"></a>역할이 Synapse Studio와 상호 작용하는 방법

Synapse Studio는 사용자 역할에 따라 다르게 작동합니다. 사용자가 적절한 액세스 권한을 부여하는 역할에 할당되지 않으면 일부 항목이 숨겨지거나 사용하지 않도록 설정될 수 있습니다. 다음 표에는 Synapse Studio에 미치는 영향이 요약되어 있습니다.

| Task | 작업 영역 관리자 | Spark 관리자 | SQL 관리자 |
| --- | --- | --- | --- |
| Synapse Studio 열기 | YES | YES | YES |
| 홈 허브 보기 | YES | YES | YES |
| 데이터 허브 보기 | YES | YES | YES |
| 데이터 허브/연결된 ADLS Gen2 계정 및 컨테이너 보기 | 예 [1] | 예[1] | 예[1] |
| 데이터 허브/데이터베이스 보기 | YES | YES | YES |
| 데이터 허브/데이터베이스의 개체 보기 | YES | YES | YES |
| 데이터 허브/SQL 풀 데이터베이스의 데이터에 액세스 | YES   | 아니요   | YES   |
| 데이터 허브/SQL 주문형 데이터베이스의 데이터에 액세스 | 예 [2]  | 아니요  | 예 [2]  |
| 데이터 허브/Spark 데이터베이스의 데이터에 액세스 | 예 [2] | 예 [2] | 예 [2] |
| 개발 허브 사용 | YES | YES | YES |
| 개발 허브/SQL 스크립트 작성 | YES | 아니요 | YES |
| 개발 허브/Spark 작업 정의 작성 | YES | YES | 아니요 |
| 개발 허브/Notebook 작성 | YES | YES | 아니요 |
| 개발 허브/데이터 흐름 작성 | YES | 아니요 | 아니요 |
| 오케스트레이션 허브 사용 | YES | YES | YES |
| 오케스트레이션 허브/파이프라인 사용 | YES | 아니요 | 아니요 |
| 관리 허브 사용 | YES | YES | YES |
| 관리 허브/SQL 풀 | YES | 아니요 | YES |
| 관리 허브/Spark 풀 | YES | YES | 아니요 |
| 관리 허브/트리거 | YES | 아니요 | 아니요 |
| 관리 허브/연결된 서비스 | YES | YES | YES |
| 관리 허브/액세스 제어(Synapse 작업 영역 역할에 사용자 할당) | YES | 아니요 | 아니요 |
| 관리 서브/통합 런타임 | YES | YES | YES |
| 모니터 허브 사용 | YES | YES | YES |
| 모니터 허브/오케스트레이션/파이프라인 실행  | YES | 아니요 | 아니요 |
| 모니터 허브/오케스트레이션/트리거 실행  | YES | 아니요 | 아니요 |
| 모니터 허브/오케스트레이션/통합 런타임  | YES | YES | YES |
| 모니터 허브/활동/Spark 애플리케이션 | YES | YES | 아니요  |
| 모니터 허브/활동/SQL 요청 | YES | 아니요 | YES |
| 모니터 허브/활동/Spark 풀 | YES | YES | 아니요  |
| 모니터 허브/트리거 | YES | 아니요 | 아니요 |
| 관리 허브/연결된 서비스 | YES | YES | YES |
| 관리 허브/액세스 제어(Synapse 작업 영역 역할에 사용자 할당) | YES | 아니요 | 아니요 |
| 관리 서브/통합 런타임 | YES | YES | YES |


> [!NOTE]
> [1] 컨테이너의 데이터 액세스는 ADLS Gen2의 액세스 제어에 따라 다릅니다. </br>
> [2] SQL OD 테이블 및 Spark 테이블은 데이터를 ADLS Gen2에 저장하며 데이터에 액세스하려면 ADLS Gen2에 대한 적절한 권한이 필요합니다.

## <a name="next-steps"></a>다음 단계

[Synapse 작업 영역](../quickstart-create-workspace.md) 만들기
