---
title: Backup 센터를 사용한 백업 모니터링 및 운영
description: 이 문서에서는 Backup 센터를 사용한 대규모 백업을 모니터링하고 운영하는 방법을 설명합니다.
ms.topic: conceptual
ms.date: 09/01/2020
ms.openlocfilehash: 86b81110d6abeb1425e18ee45dfe65a96f69687d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102506128"
---
# <a name="monitor-and-operate-backups-using-backup-center"></a>Backup 센터를 사용한 백업 모니터링 및 운영

백업 관리자로서 사용자는 단일 유리 창으로 Backup 센터를 사용하여 작업 및 백업 인벤토리를 매일 모니터링할 수 있습니다. 또한 Backup 센터를 사용하여 주문형 백업 요청에 대한 응답, 백업 복원, 백업 정책 만들기 등의 일반 작업을 수행할 수 있습니다.

## <a name="supported-scenarios"></a>지원되는 시나리오

* Backup 센터는 현재 Azure VM 백업, Azure VM 백업의 SQL, Azure VM 백업의 SAP HANA, Azure Files 백업, Azure Blob 백업, Azure Managed Disks 백업 및 Azure Database for PostgreSQL Server 백업에서 지원됩니다.
* 지원되는 시나리오와 지원되지 않는 시나리오 상세 목록은 [지원 매트릭스](backup-center-support-matrix.md)를 참조하세요.

## <a name="backup-instances"></a>Backup 인스턴스

Backup 센터는 백업 공간에서 백업 인스턴스에 대해 검색 및 검색 기능을 쉽게 할 수 있도록 합니다.

Backup 센터에서 **백업 인스턴스** 탭을 선택하면, 액세스 권한이 있는 모든 백업 인스턴스의 세부 정보를 볼 수 있습니다.

 각 백업 인스턴스에 대해 다음과 같은 정보를 볼 수 있습니다.

* 데이터 원본 구독
* 데이터 원본 리소스 그룹
* 최근 복구 지점
* 백업 인스턴스와 연결된 자격 증명 모음
* 보호 상태

 다음 매개 변수를 기준으로 백업 인스턴스 목록을 필터링할 수도 있습니다.

* 데이터 원본 구독
* 데이터 원본 리소스 그룹
* 데이터 원본 위치
* 데이터 원본 유형
* Vault
* 보호 상태
* 데이터 원본 태그

표에서 항목 하나를 마우스 오른쪽 단추로 클릭하면 리소스 탐색, 주문형 백업 및 복원 트리거 또는 백업 중지와 같이 지정된 백업 인스턴스에서 작업을 수행할 수 있습니다.

![Backup 센터 - 인스턴스](./media/backup-center-monitor-operate/backup-center-instances.png)

## <a name="backup-jobs"></a>Backup 작업

Backup 센터를 통해 백업 공간에 생성된 모든 작업에 대한 자세한 정보를 보고, 실패 작업에 대해 적절한 조치를 취할 수 있습니다.

Backup 센터에서 **Backup 작업** 메뉴 항목을 선택하면 모든 작업을 볼 수 있습니다. 각 작업에는 다음 정보가 들어 있습니다.

* 작업과 연결된 백업 인스턴스
* 데이터 원본 구독
* 데이터 원본 리소스 그룹
* 데이터 원본 위치
* 작업
* 작업 상태
* 작업 시작 시간
* 작업 기간

표에서 항목을 선택하면 지정된 작업에 대한 자세한 정보를 볼 수 있습니다. 하나의 항목을 마우스 오른쪽 단추로 클릭하면 리소스를 탐색하여 필요한 작업을 수행할 수 있습니다.

![Backup 센터 - 작업](./media/backup-center-monitor-operate/backup-center-jobs.png)

**Backup 작업** 탭을 사용하여 지난 7일 동안의 작업을 볼 수 있습니다. 이전 작업을 보려면 [Backup Reports](backup-center-obtain-insights.md)를 사용합니다.

## <a name="vaults"></a>자격 증명 모음

Backup 센터에서 **자격 증명 모음** 메뉴 항목을 선택하면, 액세스 권한이 있는 모든 [Recovery Services 자격 증명 모음](backup-azure-recovery-services-vault-overview.md) 및 [백업 자격 증명 모음](backup-vault-overview.md) 목록을 볼 수 있습니다. 다음 매개 변수를 사용하여 목록을 필터링할 수 있습니다.

* 자격 증명 모음 구독
* 자격 증명 모음 리소스 그룹
* 자격 증명 모음 이름
* 정책과 연결된 데이터 원본 유형

목록에서 항목을 선택하면 지정된 자격 증명 모음으로 이동할 수 있습니다.

![Backup 센터 - 자격 증명 모음](./media/backup-center-monitor-operate/backup-center-vaults.png)

## <a name="backup-policies"></a>Backup 정책

Backup 센터에서 Backup 정책에 대한 주요 정보를 보고 편집할 수 있습니다.

**Backup 정책** 메뉴 항목을 선택하면, 백업 공간에서 만든 모든 정책을 볼 수 있습니다. 자격 증명 모음 구독, 리소스 그룹, 데이터 원본 유형 및 자격 증명 모음을 기준으로 목록을 필터링할 수 있습니다. 표에서 항목을 마우스 오른쪽 단추로 클릭하면 해당 정책에 대한 연결된 항목을 보거나, 정책을 편집하거나, 필요한 경우 삭제할 수 있습니다.

![Backup 센터 - 정책](./media/backup-center-monitor-operate/backup-center-policies.png)

## <a name="next-steps"></a>다음 단계

* [백업 공간 제어](backup-center-govern-environment.md)
* [Backup 센터를 사용하여 작업 수행](backup-center-actions.md)
* [백업에 대한 인사이트 얻기](backup-center-obtain-insights.md)
