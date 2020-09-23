---
title: Backup Center를 사용 하 여 백업 모니터링 및 작동
description: 이 문서에서는 Backup Center를 사용 하 여 대규모로 백업을 모니터링 하 고 작동 하는 방법을 설명 합니다.
ms.topic: conceptual
ms.date: 09/01/2020
ms.openlocfilehash: 29e09e502e83ea67af290f206ee0e68b847b2069
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90996675"
---
# <a name="monitor-and-operate-backups-using-backup-center"></a>Backup Center를 사용 하 여 백업 모니터링 및 작동

백업 관리자는 백업 센터를 단일 창으로 사용 하 여 작업 및 백업 인벤토리를 매일 모니터링할 수 있습니다. 또한 백업 센터를 사용 하 여 주문형 백업 요청에 대 한 응답, 백업 복원, 백업 정책 만들기 등의 일반 작업을 수행할 수 있습니다.

## <a name="supported-scenarios"></a>지원되는 시나리오

* 백업 센터는 현재 Azure VM 백업 및 Azure Database for PostgreSQL 서버 백업에 대해 지원 됩니다.
* 지원 되는 시나리오 및 지원 되지 않는 시나리오에 대 한 자세한 목록은 [지원 매트릭스](backup-center-support-matrix.md) 를 참조 하세요.

## <a name="backup-instances"></a>백업 인스턴스

백업 센터는 백업 공간에서 백업 인스턴스를 쉽게 검색 하 고 검색할 수 있도록 합니다.

백업 센터에서 **백업 인스턴스** 탭을 선택 하면 액세스 권한이 있는 모든 백업 인스턴스의 세부 정보를 볼 수 있습니다.

 각 백업 인스턴스에 대해 다음과 같은 정보를 볼 수 있습니다.

* Datasource 구독
* Datasource 리소스 그룹
* 최신 복구 지점
* 백업 인스턴스와 연결 된 자격 증명 모음
* 보호 상태

 다음 매개 변수를 기준으로 백업 인스턴스 목록을 필터링 할 수도 있습니다.

* Datasource 구독
* Datasource 리소스 그룹
* 데이터 원본 위치
* Datasource 유형
* Vault
* 보호 상태
* Datasource 태그

표 형태의 항목을 마우스 오른쪽 단추로 클릭 하면 리소스 탐색, 주문형 백업 및 복원 트리거 또는 백업 중지와 같이 지정 된 백업 인스턴스에서 작업을 수행할 수 있습니다.

![백업 센터-인스턴스](./media/backup-center-monitor-operate/backup-center-instances.png)

## <a name="backup-jobs"></a>백업 작업

백업 센터에서 백업 공간에 생성 된 모든 작업에 대 한 자세한 정보를 보고 실패 한 작업에 대 한 적절 한 조치를 취할 수 있습니다.

백업 센터에서 **backup 작업** 메뉴 항목을 선택 하면 모든 작업을 볼 수가 제공 됩니다. 각 작업에는 다음 정보가 포함 됩니다.

* 작업과 연결 된 백업 인스턴스
* Datasource 구독
* Datasource 리소스 그룹
* 데이터 원본 위치
* 작업 작업
* 작업 상태
* 작업 시작 시간
* 작업 기간

표에서 항목을 선택 하면 지정 된 작업에 대 한 자세한 정보를 볼 수 있습니다. 항목을 마우스 오른쪽 단추로 클릭 하면 리소스를 탐색 하 여 필요한 작업을 수행할 수 있습니다.

![백업 센터-작업](./media/backup-center-monitor-operate/backup-center-jobs.png)

**백업 작업** 탭을 사용 하 여 지난 7 일 동안의 작업을 볼 수 있습니다. 이전 작업을 보려면 [Backup Reports](backup-center-obtain-insights.md)를 사용 합니다.

## <a name="vaults"></a>자격 증명 모음

백업 센터에서 **자격 증명 모음** 메뉴 항목을 선택 하면 사용자가 액세스할 수 있는 모든 [Recovery Services 자격](backup-azure-recovery-services-vault-overview.md) 증명 모음 및 [백업 자격 증명 모음](backup-vault-overview.md) 목록을 볼 수 있습니다. 다음 매개 변수를 사용 하 여 목록을 필터링 할 수 있습니다.

* 자격 증명 모음 구독
* 자격 증명 모음 리소스 그룹
* 자격 증명 모음 이름
* 정책과 연결 된 데이터 원본 유형

목록에서 항목을 선택 하면 지정 된 자격 증명 모음으로 이동할 수 있습니다.

![백업 센터-자격 증명 모음](./media/backup-center-monitor-operate/backup-center-vaults.png)

## <a name="backup-policies"></a>Backup 정책

백업 센터에서 백업 정책에 대 한 주요 정보를 보고 편집할 수 있습니다.

**백업 정책** 메뉴 항목을 선택 하면 백업 공간에서 만든 모든 정책을 볼 수 있습니다. 자격 증명 모음 구독, 리소스 그룹, 데이터 원본 유형 및 자격 증명 모음을 기준으로 목록을 필터링 할 수 있습니다. 표에서 항목을 마우스 오른쪽 단추로 클릭 하면 해당 정책에 대 한 연결 된 항목을 보거나, 정책을 편집 하거나, 필요한 경우 삭제할 수 있습니다.

![백업 센터-정책](./media/backup-center-monitor-operate/backup-center-policies.png)

## <a name="next-steps"></a>다음 단계

* [백업 공간 관리](backup-center-govern-environment.md)
* [백업 센터를 사용 하 여 작업 수행](backup-center-actions.md)
* [백업에 대 한 통찰력 얻기](backup-center-obtain-insights.md)
