---
title: Azure VM의 SAP HANA 데이터베이스 백업 정보
description: 이 문서에서는 Azure 가상 컴퓨터에서 실행 중인 SAP HANA 데이터베이스 백업에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: 52c235c95cea73a0c51c62fcb55f7f711d2eff21
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79476460"
---
# <a name="about-sap-hana-database-backup-in-azure-vms"></a>Azure VM의 SAP HANA 데이터베이스 백업 정보

SAP HANA 데이터베이스는 낮은 복구 지점 목표(RPO)와 빠른 복구 시간 목표(RTO)가 필요한 미션 크리티컬 워크로드입니다. 이제 Azure [백업을](https://docs.microsoft.com/azure/backup/backup-overview)사용하여 [Azure VM에서 실행되는 SAP HANA 데이터베이스를 백업할](https://docs.microsoft.com/azure/backup/tutorial-backup-sap-hana-db) 수 있습니다.

Azure Backup은 SAP HANA의 기본 API를 활용하여 기본 백업 지원을 제공하기 위해 SAP에서 [백인트 인증을 받았습니다.](https://www.sap.com/dmc/exp/2013_09_adpd/enEN/#/d/solutions?id=8f3fd455-a2d7-4086-aa28-51d8870acaa5) Azure Backup의 이 제품은 Azure Backup의 **제로 인프라** 백업 진언과 일치하므로 백업 인프라를 배포하고 관리할 필요가 없습니다. 이제 Azure VM(M[시리즈 VM도](../virtual-machines/m-series.md) 지금 지원됩니다!)에서 실행되는 SAP HANA 데이터베이스를 원활하게 백업하고 복원하고 Azure Backup에서 제공하는 엔터프라이즈 관리 기능을 활용할 수 있습니다.

## <a name="added-value"></a>부가가치

Azure Backup을 사용하여 SAP HANA 데이터베이스를 백업하고 복원하면 다음과 같은 이점이 있습니다.

* **15분 복구 지점 목표(RPO)**: 이제 최대 15분의 중요한 데이터를 복구할 수 있습니다.
* **원클릭, 한 시한 복원**: 생산 데이터를 대체 HANA 서버로 쉽게 복원할 수 있습니다. 복원을 수행하기 위해 백업 및 카탈로그를 연결하면 모두 백그라운드에서 Azure에서 관리합니다.
* **장기 보존**: 엄격한 규정 준수 및 감사 요구 사항. 보존 기간을 기준으로 백업을 수년 동안 유지하며, 이 외에도 기본 제공 수명 주기 관리 기능에 의해 복구 지점이 자동으로 축소됩니다.
* **Azure의 백업 관리**: Azure Backup의 관리 및 모니터링 기능을 사용하여 관리 환경을 개선합니다. Azure CLI도 지원됩니다.

현재 지원되는 백업 및 복원 시나리오를 보려면 [SAP HANA 시나리오 지원 매트릭스를](https://docs.microsoft.com/azure/backup/sap-hana-backup-support-matrix#scenario-support)참조하십시오.

## <a name="backup-architecture"></a>백업 아키텍처

![백업 아키텍처 다이어그램](./media/sap-hana-db-about/backup-architecture.png)

* 백업 프로세스는 Azure에서 [복구 서비스 자격 증명 모음을 만들어](https://docs.microsoft.com/azure/backup/tutorial-backup-sap-hana-db#create-a-recovery-service-vault) 시작합니다. 이 자격 증명 모음은 시간이 지남에 따라 생성된 백업 및 복구 지점을 저장하는 데 사용됩니다.
* SAP HANA 서버를 실행하는 Azure VM이 볼트에 등록되고 백업할 데이터베이스가 [검색됩니다.](https://docs.microsoft.com/azure/backup/tutorial-backup-sap-hana-db#discover-the-databases) Azure Backup 서비스가 데이터베이스를 검색할 수 있도록 하려면 HANA 서버에서 루트 사용자로 [사전 등록 스크립트를](https://aka.ms/scriptforpermsonhana) 실행해야 합니다.
* 이 스크립트는 **AZUREWLBACKUPHANAUSER** DB 사용자와 **hdbuserstore에서**동일한 이름의 해당 키를 만듭니다. 스크립트가 수행하는 작업을 자세히 보려면 [사전 등록 스크립트가 수행하는](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does) 작업을 참조하십시오.
* 이제 Azure 백업 서비스가 등록된 SAP HANA 서버에 **HANA용 Azure 백업 플러그인을** 설치합니다.
* 사전 등록 스크립트에 의해 생성된 **AZUREWLBACKUPHANAUSER** DB 사용자는 **HANA용 Azure 백업 플러그인에서** 모든 백업 및 복원 작업을 수행하는 데 사용됩니다. 이 스크립트를 실행하지 않고 SAP HANA DB에 대한 백업을 구성하려고 하면 다음과 같은 오류가 발생할 수 **있습니다.**
* 검색된 데이터베이스에서 [백업을 구성하려면](https://docs.microsoft.com/azure/backup/tutorial-backup-sap-hana-db#configure-backup) 필요한 백업 정책을 선택하고 백업을 사용하도록 설정합니다.

* 백업이 구성되면 Azure Backup 서비스는 보호된 SAP HANA 서버의 데이터베이스 수준에서 다음과 같은 Backint 매개 변수를 설정합니다.
  * [catalog_backup_using_backint:true]
  * [enable_accumulated_catalog_backup:거짓]
  * 【parallel_data_backup_backint_channels:1】
  * [log_backup_timeout_s:900)]
  * [backint_response_timeout:7200]

>[!NOTE]
>이러한 매개 변수가 HOST 수준에 *없는지* 확인합니다. 호스트 수준 매개 변수는 이러한 매개 변수를 재정의하고 예기치 않은 동작을 일으킬 수 있습니다.
>

* **HANA용 Azure 백업 플러그인은** 모든 백업 일정 및 정책 세부 정보를 유지 관리합니다. 예약된 백업을 트리거하고 Backint API를 통해 **HANA 백업 엔진과** 통신합니다.
* **HANA 백업 엔진은** 백업할 데이터와 함께 백인트 스트림을 반환합니다.
* 전체 또는 차등 인 모든 예약된 백업 및 주문형 백업(Azure Portal에서 트리거)은 **HANA**에 대한 Azure 백업 플러그인에 의해 시작됩니다. 그러나 로그 백업은 **HANA 백업 엔진** 자체에 의해 관리되고 트리거됩니다.
* BackInt 인증 솔루션인 SAP HANA용 Azure 백업은 기본 디스크 또는 VM 형식에 의존하지 않습니다. 백업은 HANA에서 생성한 스트림에 의해 수행됩니다.

## <a name="using-azure-vm-backup-with-azure-sap-hana-backup"></a>Azure SAP HANA 백업을 사용하여 Azure VM 백업 사용

데이터베이스 수준 백업 및 복구를 제공하는 Azure의 SAP HANA 백업을 사용하는 것 외에도 Azure VM 백업 솔루션을 사용하여 OS 및 비데이터베이스 디스크를 백업할 수 있습니다.

[Backint 인증 Azure SAP HANA 백업 솔루션을](#backup-architecture) 데이터베이스 백업 및 복구에 사용할 수 있습니다.

[Azure VM 백업을](backup-azure-vms-introduction.md) 사용하여 OS 및 기타 비데이터베이스 디스크를 백업할 수 있습니다. VM 백업은 매일 한 번 수행되며 모든 디스크를 **백업합니다(쓰기 가속기(WA) 디스크** 및 **UltraDisks**제외). Azure SAP HANA 백업 솔루션을 사용하여 데이터베이스가 백업되므로 현재 미리 보기 중인 제외 디스크 기능을 사용하여 OS 및 비데이터베이스 디스크만 파일 일관성 백업을 수행할 수 있습니다.

>[!NOTE]
> Azure VM 백업과 함께 사전 게시 스크립트를 사용하면 데이터베이스의 데이터 볼륨을 앱에서 일관되게 백업할 수 있습니다. 그러나 로그 영역이 WA 디스크에 있는 경우 이러한 디스크의 스냅숏을 생성하면 로그 영역의 일관성이 보장되지 않을 수 있습니다. HANA는 이러한 정확한 이유로 로그 백업을 생성하는 명시적인 방법을 가지고 있습니다. SAP HANA에서 동일한 기능을 활성화하면 Azure SAP HANA 백업을 사용하여 백업할 수 있습니다.

SAP HANA를 실행하는 VM을 복원하려면 다음 단계를 따르십시오.

* Azure VM 백업에서 최신 복구 지점에서 [새 VM을 복원합니다.](backup-azure-arm-restore-vms.md) 또는 새 빈 VM을 만들고 최신 복구 지점에서 디스크를 연결합니다.
* WA 디스크는 백업되지 않으므로 복원되지 않습니다. 빈 WA 디스크 및 로그 영역을 만듭니다.
* IP, 시스템 이름 등과 같은 다른 모든 구성이 설정된 후 VM은 Azure 백업에서 DB 데이터를 수신하도록 설정됩니다.
* 이제 [Azure SAP HANA DB 백업에서](sap-hana-db-restore.md#restore-to-a-point-in-time-or-to-a-recovery-point) 원하는 시점에 DB를 VM으로 복원합니다.

## <a name="next-steps"></a>다음 단계

* [Azure VM에서 실행 중인 SAP HANA 데이터베이스를 복원하는](https://docs.microsoft.com/azure/backup/sap-hana-db-restore) 방법에 대해 알아봅니다.
* [Azure Backup을 사용하여 백업된 SAP HANA 데이터베이스를 관리하는 방법](https://docs.microsoft.com/azure/backup/sap-hana-db-manage)을 알아봅니다.
