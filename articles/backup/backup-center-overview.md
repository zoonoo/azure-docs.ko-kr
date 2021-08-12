---
title: Backup 센터 개요
description: 이 문서에서는 Azure의 Backup 센터를 대략적으로 설명합니다.
ms.topic: conceptual
ms.date: 09/30/2020
ms.openlocfilehash: b42bb2719d03108212f62428dc97ed814899c63c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102506057"
---
# <a name="overview-of-backup-center"></a>Backup 센터 개요

Backup 센터는 Azure에서 기업이 대규모 백업을 제어, 모니터링, 운영, 분석하는 데 사용할 수 있는 **단일 통합 관리 환경** 을 제공합니다. 따라서 Azure의 기본 관리 환경과 일치합니다.

Backup 센터의 몇 가지 주요 이점은 다음과 같습니다.

* **백업을 관리하기 위한 단일 창** - Backup 센터는 대규모의 분산형 Azure 환경에서 잘 작동하도록 디자인되었습니다. Backup 센터를 사용하여 여러 워크로드 유형, 자격 증명 모음, 구독, 지역 및 [Azure Lighthouse](../lighthouse/overview.md) 테넌트에 걸쳐 있는 백업을 효율적으로 관리할 수 있습니다.
* **데이터 원본 중심 관리** – Backup 센터는 백업 중인 데이터 원본(예: VM 및 데이터베이스)을 중심으로 하는 뷰와 필터를 제공합니다. 이를 통해 리소스 소유자 또는 백업 관리자는 항목이 백업되는 자격 증명 모음에 집중하지 않고도 항목의 백업을 모니터링하고 관련 작업을 수행할 수 있습니다. 이 디자인의 핵심 기능은 데이터 원본 구독, 데이터 원본 리소스 그룹 및 데이터 원본 태그와 같은 데이터 원본별 속성을 기준으로 뷰를 필터링하는 기능입니다. 예를 들어, 조직에서 다른 부서에 속한 VM에 여러 다른 태그를 할당하는 방법을 따르는 경우 Backup 센터를 사용하여 자격 증명 모음 태그에 집중하지 않고도 백업되는 기본 VM의 태그를 기준으로 백업 정보를 필터링할 수 있습니다.
* **연결된 환경** – Backup 센터는 대규모 관리를 가능하게 하는 기존 Azure 서비스에 대한 네이티브 통합을 제공합니다. 예를 들어, Backup 센터는 [Azure Policy](../governance/policy/overview.md) 환경을 사용하여 백업을 관리하는 데 도움을 줍니다. 또한 백업에 대한 자세한 보고서를 볼 수 있도록 하기 위해 [Azure 통합 문서](../azure-monitor/visualize/workbooks-overview.md)와 [Azure Monitor 로그](../azure-monitor/logs/data-platform-logs.md)를 활용합니다. 따라서 Backup 센터에서 제공하는 다양한 기능을 사용하기 위해 새로운 원칙을 익힐 필요가 없습니다. 또한 Backup 센터에서 커뮤니티 리소스를 검색할 수도 있습니다.

## <a name="supported-scenarios"></a>지원되는 시나리오

* Backup 센터는 현재 Azure VM 백업, Azure VM 백업의 SQL, Azure VM 백업의 SAP HANA, Azure Files 백업, Azure Blob 백업, Azure Managed Disks 백업 및 Azure Database for PostgreSQL Server 백업에서 지원됩니다.
* 지원되는 시나리오와 지원되지 않는 시나리오 상세 목록은 [지원 매트릭스](backup-center-support-matrix.md)를 참조하세요.

## <a name="get-started"></a>시작

Backup 센터 사용을 시작하려면 Azure Portal에서 **Backup 센터** 를 검색하고 **Backup 센터** 대시보드로 이동합니다.

![Backup 센터 검색](./media/backup-center-overview/backup-center-search.png)

표시되는 첫 번째 화면은 **개요** 입니다. 여기에는 **작업** 및 **백업 인스턴스** 의 두 가지 타일이 포함되어 있습니다.

![Backup 센터 타일](./media/backup-center-overview/backup-center-overview-widgets.png)

**작업** 타일에서 지난 24시간 동안 백업 자산에서 트리거된 모든 백업 및 복원 관련 작업의 요약된 보기를 볼 수 있습니다. 완료, 실패 및 진행 중인 작업 수에 대한 정보를 볼 수 있습니다. 이 타일에서 숫자를 선택하면 특정 데이터 원본 유형, 작업 유형 및 상태의 작업에 대한 자세한 정보를 확인할 수 있습니다.

**백업 인스턴스** 타일에서 백업 자산에 있는 모든 백업 인스턴스의 요약 보기를 확인할 수 있습니다. 예를 들어, 보호를 위해 구성된 인스턴스 수와 비교하여 일시 삭제된 상태의 백업 인스턴스 수를 확인할 수 있습니다. 이 타일에서 숫자를 선택하면 특정 데이터 원본 유형 및 보호 상태의 백업 인스턴스에 대한 자세한 정보를 확인할 수 있습니다. 또한 기본 데이터 원본을 찾을 수 없는(데이터 원본이 삭제되었거나 데이터 원본에 대한 액세스 권한이 없을 수 있음) 모든 백업 인스턴스를 볼 수 있습니다.

Backup 센터의 기능을 이해하려면 다음 비디오를 시청하세요.

> [!VIDEO https://www.youtube.com/embed/pFRMBSXZcUk?t=497]

[다음 단계](#next-steps)에 따라 Backup 센터에서 제공하는 다양한 기능과 이러한 기능을 사용하여 백업 자산의 효율성을 관리하는 방법을 이해합니다.

## <a name="next-steps"></a>다음 단계

* [백업 모니터링 및 운영](backup-center-monitor-operate.md)
* [백업 공간 제어](backup-center-govern-environment.md)
* [백업에 대한 인사이트 얻기](backup-center-obtain-insights.md)
* [Backup 센터를 사용하여 작업 수행](backup-center-actions.md)