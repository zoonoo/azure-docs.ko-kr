---
title: 백업 센터 개요
description: 이 문서에서는 Azure에 대 한 백업 센터의 개요를 제공 합니다.
ms.topic: conceptual
ms.date: 09/30/2020
ms.openlocfilehash: 2857433e00a678603e30c8e5fc276020c4658f2e
ms.sourcegitcommit: 06ba80dae4f4be9fdf86eb02b7bc71927d5671d3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2020
ms.locfileid: "91614288"
---
# <a name="overview-of-backup-center"></a>백업 센터 개요

백업 센터는 엔터프라이즈에서 대규모 백업을 제어, 모니터링, 운영 및 분석 하는 데 사용할 수 있는 **단일 통합 관리 환경을** 제공 합니다. 따라서 Azure의 기본 관리 환경과 일치 합니다.

백업 센터의 주요 이점 중 일부는 다음과 같습니다.

* **백업을 관리 하는 단일 창** -백업 센터는 크고 분산 된 Azure 환경에서 잘 작동 하도록 설계 되었습니다. Backup Center를 사용 하 여 여러 작업 유형, 자격 증명 모음, 구독, 지역 및 [Azure Lighthouse](https://docs.microsoft.com/azure/lighthouse/overview) 테 넌 트에 걸친 백업을 효율적으로 관리할 수 있습니다.
* **데이터 원본 중심 관리** – 백업 센터는 백업 중인 데이터 원본 (예: vm 및 데이터베이스)을 중심으로 하는 뷰와 필터를 제공 합니다. 이렇게 하면 리소스 소유자 또는 백업 관리자가 항목이 백업 되는 자격 증명 모음에 집중 하지 않고도 항목의 백업을 모니터링 하 고 작업할 수 있습니다. 이 디자인의 핵심 기능은 datasource 구독, datasource 리소스 그룹 및 datasource 태그와 같은 datasource 별 속성을 기준으로 뷰를 필터링 하는 기능입니다. 예를 들어 조직에서 다른 부서에 속한 Vm에 서로 다른 태그를 할당 하는 방법을 따르는 경우 백업 센터를 사용 하 여 자격 증명 모음 태그에 집중 하지 않고도 백업 되는 기본 Vm의 태그를 기준으로 백업 정보를 필터링 할 수 있습니다.
* **연결 된 환경** – Backup 센터는 대규모 관리를 가능 하 게 하는 기존 Azure 서비스에 기본 통합을 제공 합니다. 예를 들어 백업 센터는 [Azure Policy](https://docs.microsoft.com/azure/governance/policy/overview) 환경을 사용 하 여 백업을 관리 하는 데 도움을 줍니다. 또한 백업에 대 한 자세한 보고서를 볼 수 있도록 [Azure 통합 문서](https://docs.microsoft.com/azure/azure-monitor/platform/workbooks-overview) 와 [Azure Monitor 로그](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-logs) 를 활용 합니다. 따라서 Backup Center에서 제공 하는 다양 한 기능을 사용 하기 위해 새로운 원리를 배울 필요가 없습니다.

## <a name="supported-scenarios"></a>지원되는 시나리오

* 백업 센터는 현재 Azure VM 백업 및 Azure Database for PostgreSQL 서버 백업에 대해 지원 됩니다.
* 지원 되는 시나리오 및 지원 되지 않는 시나리오에 대 한 자세한 목록은 [지원 매트릭스](backup-center-support-matrix.md) 를 참조 하세요.

## <a name="get-started"></a>시작

Backup center를 사용 하 여 시작 하려면 Azure Portal에서 **백업 센터** 를 검색 하 고 **Backup 센터 (미리 보기)** 대시보드로 이동 합니다.

![백업 센터 검색](./media/backup-center-overview/backup-center-search.png)

표시 되는 첫 번째 화면에는 **개요가**표시 됩니다. 여기에는 **작업** 및 **백업 인스턴스**라는 두 개의 타일이 포함 됩니다.

![백업 센터 타일](./media/backup-center-overview/backup-center-overview-widgets.png)

**작업** 타일에서 최근 24 시간 동안의 백업 공간에 트리거된 모든 백업 및 복원 관련 작업의 요약 된 보기를 볼 수 있습니다. 완료, 실패 및 진행 중인 작업 수에 대 한 정보를 볼 수 있습니다. 이 타일에서 숫자 중 하나를 선택 하면 특정 데이터 원본 유형, 작업 유형 및 상태에 대 한 작업에 대 한 자세한 정보를 볼 수 있습니다.

백업 **인스턴스** 타일의 백업 공간에 있는 모든 백업 인스턴스의 요약 된 보기를 볼 수 있습니다. 예를 들어 보호를 위해 여전히 구성 된 인스턴스 수와 비교할 때 일시 삭제 된 상태의 백업 인스턴스 수를 확인할 수 있습니다. 이 타일에서 숫자 중 하나를 선택 하면 특정 데이터 원본 유형 및 보호 상태에 대 한 백업 인스턴스에 대 한 자세한 정보를 볼 수 있습니다.

백업 센터의 기능을 이해 하려면 다음 비디오를 시청 하세요.

> [!VIDEO https://www.youtube.com/embed/pFRMBSXZcUk?t=497]

[다음 단계](#next-steps) 를 수행 하 여 백업 센터에서 제공 하는 다양 한 기능을 이해 하 고 이러한 기능을 사용 하 여 백업 공간을 효율적으로 관리 하는 방법을 알아보세요.

## <a name="next-steps"></a>다음 단계

* [백업 모니터링 및 작동](backup-center-monitor-operate.md)
* [백업 공간 관리](backup-center-govern-environment.md)
* [백업에 대 한 통찰력 얻기](backup-center-obtain-insights.md)
* [백업 센터를 사용 하 여 작업 수행](backup-center-actions.md)
