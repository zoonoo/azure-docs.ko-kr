---
title: SAP HANA Backup 지원 매트릭스
description: 이 문서에서는 Azure 백업을 사용하여 Azure VM에서 SAP HANA 데이터베이스를 백업할 때 지원되는 시나리오 및 제한 사항에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: 8496dc4996cac68535bfe9be30e4b5f72e2d5721
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252442"
---
# <a name="support-matrix-for-backup-of-sap-hana-databases-on-azure-vms"></a>Azure VM의 SAP HANA 데이터베이스 백업에 대한 지원 매트릭스

Azure 백업은 Azure에 대한 SAP HANA 데이터베이스의 백업을 지원합니다. 이 문서에서는 Azure Backup을 사용하여 Azure VM에서 SAP HANA 데이터베이스를 백업할 때 지원되는 시나리오와 제한 사항에 대해 간략하게 요약합니다.

> [!NOTE]
> 이제 로그 백업 빈도를 최소 15분으로 설정할 수 있습니다. 로그 백업은 데이터베이스에 대한 전체 백업이 완료된 후에만 흐르기 시작합니다.

## <a name="scenario-support"></a>시나리오 지원

| **시나리오**               | **지원되는 구성**                                | **지원되지 않는 구성**                              |
| -------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| **토폴로지**               | Azure Linux VM에서만 실행되는 SAP HANA                    | 하나 대형 인스턴스 (HLI)                                   |
| **Geos**                   | **조지아:**<br> **미주** – 미국 중부, 미국 동부 2, 미국 동부, 미국 중북부, 미국 중남부, 미국 서부 2, 미국 서부, 미국 서부, 캐나다 중부, 캐나다 동부, 브라질 남부 <br> **아시아 태평양** – 오스트레일리아 중부, 오스트레일리아 중부 2, 오스트레일리아 동부, 호주 남동부, 일본 동부, 일본 서부, 한국 중부, 한국, 동아시아, 동남아시아, 중앙 인도, 남인도, 서인도, 중국 동부, 중국 북부, 중국 동부2, 중국 북부 2 <br> **유럽** – 서유럽, 북유럽, 프랑스 중부, 영국 남부, 영국 서부, 독일 북부, 독일 서부 중부, 스위스 북부, 스위스 서부, 중부 스위스 북부 <br> **아프리카 / ME** - 남아프리카 공화국, 남아프리카 공화국 서부, 아랍 에미리트 북부, 아랍 에미리트 중부  <BR>  **Azure Government 지역** | 프랑스 남부, 독일 중부, 독일 북동부, 미국 정부 아이오와 |
| **OS 버전**            | SP2, SP3 또는 SP4를 가진 SLES 12; SP1을 가진 SLES 15                              | RHEL                                                |
| **하나 버전**          | 하나 1.x에 SDC, 하나 2.x <MDC = SPS04 레브 46       | -                                                            |
| **HANA 배포**       | 단일 Azure VM에서 SAP HANA - 확장만 가능합니다. <br><br> 고가용성 배포의 경우 두 컴퓨터의 두 노드는 별도의 데이터 체인이 있는 개별 노드로 처리됩니다.               | 스케일 아웃 <br><br> 고가용성 배포에서는 백업이 보조 노드에 자동으로 장애 조치되지 않습니다. 백업 구성은 각 노드에 대해 별도로 수행해야 합니다.                                           |
| **하나 인스턴스**         | 단일 Azure VM에서 단일 SAP HANA 인스턴스 - 확장만 | 단일 VM에서 여러 SAP HANA 인스턴스                  |
| **HANA 데이터베이스 유형**    | 단일 데이터베이스 컨테이너(SDC) ON 1.x, 2.x의 다중 데이터베이스 컨테이너(MDC) | 하나 1.x의 MDC                                              |
| **HANA 데이터베이스 크기**     | HANA에서 보고한 2TB 전체 백업 크기)                   |                                                              |
| **백업 유형**           | 전체, 차동 및 로그 백업                          | 증분, 스냅샷                                       |
| **형식 복원**          | 지원되는 복원 유형에 대해 알아보려면 SAP HANA Note [1642148을](https://launchpad.support.sap.com/#/notes/1642148) 참조하십시오. |                                                              |
| **Backup 제한**          | SAP HANA 인스턴스당 최대 2TB의 전체 백업 크기         |                                                              |
| **특수 구성** |                                                              | SAP HANA + 동적 계층화 <br>  라마를 통한 복제        |

------

> [!NOTE]
> SAP HANA 네이티브 클라이언트(SAP HANA 스튜디오/ 조종석/ DBA 조종석)의 백업 및 복원 작업은 현재 지원되지 않습니다.

## <a name="next-steps"></a>다음 단계

* [Azure VM에서 실행되는 SAP HANA 데이터베이스를 백업하는](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database) 방법 알아보기
* [Azure VM에서 실행 중인 SAP HANA 데이터베이스를 복원하는](https://docs.microsoft.com/azure/backup/sap-hana-db-restore) 방법에 대해 알아봅니다.
* [Azure Backup을 사용하여 백업된 SAP HANA 데이터베이스를 관리하는 방법](sap-hana-db-manage.md)을 알아봅니다.
* [SAP HANA 데이터베이스를 백업할 때 발생하는 일반적인 문제를 해결하는 방법](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database-troubleshoot)을 알아봅니다.
