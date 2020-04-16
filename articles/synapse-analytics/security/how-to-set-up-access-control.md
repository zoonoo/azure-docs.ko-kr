---
title: 시냅스 작업 공간 보호(미리 보기)
description: 이 문서에서는 역할 및 액세스 제어를 사용하여 활동을 제어하고 Synapse 작업 영역의 데이터에 액세스하는 방법을 설명합니다.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: mahi
ms.reviewer: jrasnick
ms.openlocfilehash: 94699f2153fa8d1df3ab85c184f32792c7ae0b59
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428876"
---
# <a name="secure-your-synapse-workspace-preview"></a>시냅스 작업 공간 보호(미리 보기)

이 문서에서는 역할 및 액세스 제어를 사용하여 활동 및 데이터에 대한 액세스를 제어하는 방법을 설명합니다. 이러한 지침에 따라 Azure Synapse Analytics의 액세스 제어가 간소화됩니다. 세 개의 보안 그룹 중 하나에 사용자를 추가하고 제거하면 됩니다.

## <a name="overview"></a>개요

시냅스 작업 영역(미리 보기)을 보호하려면 다음 항목을 구성하는 패턴을 따릅니다.

- Azure 역할(예: 소유자, 기여자 등과 같은 기본 제공 역할)
- 시냅스 역할 – 이러한 역할은 시냅스고유이며 Azure 역할을 기반으로 하지 않습니다. 다음 세 가지 역할이 있습니다.
  - 시냅스 작업 영역 관리자
  - 시냅스 SQL 관리자
  - 시냅스 스파크 관리자
- Azure 데이터 레이크 저장소 세대 2(ADLSGEN2)의 데이터에 대한 액세스 제어입니다.
- 시냅스 SQL 및 스파크 데이터베이스에 대한 액세스 제어

## <a name="steps-to-secure-a-synapse-workspace"></a>시냅스 작업 영역을 보호하는 단계

이 문서에서는 표준 이름을 사용하여 지침을 단순화합니다. 선택한 이름으로 바꿉니다.

|설정 | 예제 값 | Description |
| :------ | :-------------- | :---------- |
| **시냅스 작업 공간** | WS1 |  Synapse 작업 영역에 있는 이름입니다. |
| **ADLSGEN2 계정** | STG1 | 작업 영역과 함께 사용할 ADLS 계정입니다. |
| **컨테이너** | CNT1 | 작업 영역이 기본적으로 사용할 STG1의 컨테이너입니다. |
| **활성 디렉터리 테넌트** | c:\contoso | 활성 디렉터리 테넌트 이름입니다.|
||||

## <a name="step-1-set-up-security-groups"></a>1단계: 보안 그룹 설정

작업 영역에 대해 세 개의 보안 그룹을 만들고 채웁니다.

- **WS1\_WSAdmins** - 작업 영역을 완벽하게 제어해야 하는 사용자를 위한
- **WS1\_SparkAdmins** – 작업 영역의 스파크 측면을 완벽하게 제어해야 하는 사용자를 위한
- **WS1\_SQLAdmins** - 작업 영역의 SQL 측면을 완벽하게 제어해야 하는 사용자를 위한 사용자
- **WS1\_** **SQLAdmins에\_WS1** 관리자 추가
- **WS1\_** **SparkAdmins에\_WS1** 관리자 추가

## <a name="step-2-prepare-your-data-lake-storage-gen2-account"></a>2단계: 데이터 레이크 스토리지 Gen2 계정 준비

저장소에 대한 이 정보를 식별합니다.

- 작업 영역에 사용할 ADLSGEN2 계정입니다. 이 문서를 STG1이라고 합니다.  STG1은 작업 영역에 대한 "기본" 저장소 계정으로 간주됩니다.
- 시냅스 작업 영역이 기본적으로 사용하는 WS1 내부의 컨테이너입니다. 이 문서를 CNT1이라고 합니다.  이 컨테이너는 다음과 같은 데 사용됩니다.
  - 스파크 테이블에 대한 백업 데이터 파일 저장
  - 스파크 작업에 대한 실행 로그

- Azure 포털을 사용하여 보안 그룹에 CNT1에서 다음과 같은 역할을 할당합니다.

  - **스토리지 Blob 데이터 기여자** 역할에 **WS1\_WSAdmins** 할당
  - **스토리지 Blob 데이터 기여자** 역할에 **WS1\_SparkAdmins** 할당
  - **저장소 Blob 데이터 기여자** 역할에 **WS1\_SQLAdmins** 할당

## <a name="step-3-create-and-configure-your-synapse-workspace"></a>3단계: 시냅스 작업 공간 생성 및 구성

Azure 포털에서 시냅스 작업 영역을 만듭니다.

- 작업 영역 이름 WS1
- 저장소 계정에 대해 STG1을 선택합니다.
- "파일 시스템"으로 사용 중인 컨테이너에 대해 CNT1을 선택합니다.
- 시냅스 스튜디오에서 WS1 오픈
- **액세스 제어** **관리를** > 선택하면 다음 Synapse 역할에 보안 그룹을 할당합니다.
  - 시냅스 작업 영역 관리자에 **WS1\_WSAdmins** 할당
  - 시냅스 스파크 관리자에게 **WS1\_SparkAdmins** 할당
  - 시냅스 SQL 관리자에 **WS1\_SQLAdmins** 할당

## <a name="step-4-configuring-data-lake-storage-gen2-for-use-by-synapse-workspace"></a>4단계: 시냅스 작업 공간에서 사용할 데이터 레이크 스토리지 Gen2 구성

Synapse 작업 영역은 파이프라인을 실행하고 시스템 작업을 수행할 수 있도록 STG1 및 CNT1에 액세스해야 합니다.

- Azure 포털 열기
- STG1 찾기
- CNT1로 이동
- WS1에 대한 MSI가 CNT1의 **Azure Blob 데이터 기여자** 역할에 할당되어 있는지 확인합니다.
  - 할당되지 않은 경우 할당합니다.
  - MSI의 이름은 작업 영역과 같습니다. 이 경우 &quot;WS1&quot;.

## <a name="step-5-configure-admin-access-for-sql-pools"></a>5단계: SQL 풀에 대한 관리자 액세스 구성

- Azure 포털 열기
- WS1로 이동
- **설정에서** **SQL 활성 디렉터리 관리자를** 클릭합니다.
- **관리자 설정을** 클릭하고\_WS1 SQLAdmins를 선택합니다.

## <a name="step-6-maintaining-access-control"></a>6단계: 액세스 제어 유지

구성이 완료되었습니다.

이제 사용자에 대한 액세스를 관리하기 위해 세 개의 보안 그룹에 사용자를 추가하고 제거할 수 있습니다.

사용자를 시냅스 역할에 수동으로 할당할 수 있지만 그렇게 할 경우 일관되게 구성되지는 않습니다. 대신 보안 그룹에 사용자를 추가하거나 제거합니다.

## <a name="step-7-verify-access-for-users-in-the-roles"></a>단계 7: 역할의 사용자에 대한 액세스 확인

각 역할의 사용자는 다음 단계를 완료해야 합니다.

|   | 단계 | 작업 영역 관리자 | 스파크 관리자 | SQL 관리자 |
| --- | --- | --- | --- | --- |
| 1 | 쪽모이 세공 파일을 CNT1에 업로드 | YES | YES | YES |
| 2 | 주문형 SQL을 사용하여 마루 파일 읽기 | YES | 아니요 | YES |
| 3 | 스파크 풀 만들기 | 예 [1] | 예 [1] | 아니요  |
| 4 | 전자 필기장으로 마루 파일 읽기 | YES | YES | 아니요 |
| 5 | 전자 필기장에서 파이프라인 만들기 및 지금 실행하려면 파이프라인 트리거 | YES | 아니요 | 아니요 |
| 6 | SQL 풀을 만들고 SELECT 1과 &quot;같은 SQL 스크립트를 실행합니다.&quot; | 예 [1] | 아니요 | 예[1] |

> [!NOTE]
> [1] SQL 또는 Spark 풀을 만들려면 사용자가 Synapse 작업 영역에 최소한 참여자 역할이 있어야 합니다.
> [!TIP]
>
> - 역할에 따라 일부 단계는 의도적으로 허용되지 않습니다.
> - 보안이 완전히 구성되지 않은 경우 일부 작업이 실패할 수 있습니다. 이러한 작업은 표에 기록되어 있습니다.

## <a name="step-8-network-security"></a>8단계: 네트워크 보안

작업 영역 방화벽, 가상 네트워크 및 [개인 링크를](../../sql-database/sql-database-private-endpoint-overview.md)구성합니다.

## <a name="step-9-completion"></a>9단계: 완료

이제 작업 영역이 완전히 구성되고 보호됩니다.

## <a name="how-roles-interact-with-synapse-studio"></a>역할이 시냅스 스튜디오와 상호 작용하는 방법

시냅스 스튜디오는 사용자 역할에 따라 다르게 행동합니다. 사용자가 적절한 액세스 권한을 부여하는 역할에 할당되지 않은 경우 일부 항목은 숨김 또는 비활성화될 수 있습니다. 다음 표에는 시냅스 스튜디오에 미치는 영향을 요약한 것입니다.

| Task | 작업 영역 관리자 | 스파크 관리자 | SQL 관리자 |
| --- | --- | --- | --- |
| 시냅스 스튜디오 오픈 | YES | YES | YES |
| 홈 허브 보기 | YES | YES | YES |
| 데이터 허브 보기 | YES | YES | YES |
| 데이터 허브 / 연결된 ADLSGen2 계정 및 컨테이너 참조 | 예 [1] | 예[1] | 예[1] |
| 데이터 허브 / 데이터베이스 보기 | YES | YES | YES |
| 데이터 허브 / 데이터베이스의 개체 보기 | YES | YES | YES |
| SQL 풀 데이터베이스의 데이터 허브 / 액세스 데이터 | YES   | 아니요   | YES   |
| SQL 온디맨드 데이터베이스의 데이터 허브 / 액세스 데이터 | 예 [2]  | 아니요  | 예 [2]  |
| 스파크 데이터베이스의 데이터 허브 / 액세스 데이터 | 예 [2] | 예 [2] | 예 [2] |
| 개발 허브 사용 | YES | YES | YES |
| 허브 / 저자 SQL 스크립트 개발 | YES | 아니요 | YES |
| 허브 개발 / 저자 스파크 작업 정의 | YES | YES | 아니요 |
| 허브 / 저자 노트북 개발 | YES | YES | 아니요 |
| 허브 / 저자 데이터 흐름 개발 | YES | 아니요 | 아니요 |
| 오케스트레이션 허브 사용 | YES | YES | YES |
| 오케스트레이션 허브 / 파이프라인 사용 | YES | 아니요 | 아니요 |
| 관리 허브 사용 | YES | YES | YES |
| 허브/SQL 풀 관리 | YES | 아니요 | YES |
| 허브 / 스파크 풀 관리 | YES | YES | 아니요 |
| 허브/ 트리거 관리 | YES | 아니요 | 아니요 |
| 허브 / 연결된 서비스 관리 | YES | YES | YES |
| 허브 / 액세스 제어 관리(사용자를 시냅스 작업 영역 역할에 할당) | YES | 아니요 | 아니요 |
| 허브/통합 런타임 관리 | YES | YES | YES |

> [!NOTE]
> [1] 컨테이너의 데이터에 대한 액세스는 ADLSGen2 [2] SQL OD 테이블의 액세스 제어에 따라 달라지며, 스파크 테이블은 데이터를 ADLSGen2에 저장하고 액세스하려면 ADLSGen2에 대한 적절한 권한이 필요합니다.

## <a name="next-steps"></a>다음 단계

[시냅스 작업 영역](../quickstart-create-workspace.md) 만들기
