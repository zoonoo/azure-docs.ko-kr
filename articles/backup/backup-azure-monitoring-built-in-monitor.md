---
title: Azure 백업 보호 워크로드 모니터링
description: 이 문서에서는 Azure 포털을 사용하는 Azure Backup 워크로드에 대한 모니터링 및 알림 기능에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 03/05/2019
ms.assetid: 86ebeb03-f5fa-4794-8a5f-aa5cbbf68a81
ms.openlocfilehash: de5a82f5ad1d8113b27c07484f2f08f4cf97c759
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294933"
---
# <a name="monitoring-azure-backup-workloads"></a>Azure 백업 워크로드 모니터링

Azure Backup은 백업 요구 사항 및 인프라 토폴로지(온-프레미스 vs Azure)를 기반으로 여러 백업 솔루션을 제공합니다. 모든 백업 사용자 또는 관리자는 모든 솔루션에서 무슨 일이 일어나고 있는지 확인해야 하며 중요한 시나리오에서 알림을 받아야 합니다. 이 문서에서는 Azure Backup 서비스에서 제공하는 모니터링 및 알림 기능에 대해 자세히 설명합니다.

## <a name="backup-jobs-in-recovery-services-vault"></a>복구 서비스 자격 증명 모음의 백업 작업

Azure Backup은 Azure Backup에 의해 보호되는 워크로드에 대한 기본 제공 모니터링 및 경고 기능을 제공합니다. 복구 서비스 자격 증명 모음 설정에서 **모니터링** 섹션에서는 기본 제공 작업 및 경고를 제공합니다.

![RS 볼트 붙들기 모니터링](media/backup-azure-monitoring-laworkspace/rs-vault-inbuiltmonitoring.png)

작업은 백업 구성, 백업, 복원, 백업 삭제 등의 작업이 수행될 때 생성됩니다.

다음 Azure 백업 솔루션의 작업은 다음과 같습니다.

- Azure VM 백업
- Azure 파일 백업
- SQL 및 SAP HANA와 같은 Azure 워크로드 백업
- Azure Backup 에이전트(MAB)

시스템 센터 데이터 보호 관리자(SC-DPM), Microsoft Azure 백업 서버(MABS)의 작업은 표시되지 않습니다.

> [!NOTE]
> Azure VM 내의 SQL 및 SAP HANA 백업과 같은 Azure 워크로드에는 엄청난 수의 백업 작업이 있습니다. 예를 들어 로그 백업은 15분마다 실행될 수 있습니다. 따라서 이러한 DB 워크로드의 경우 사용자 트리거작업만 표시됩니다. 예약된 백업 작업이 표시되지 않습니다.

## <a name="backup-alerts-in-recovery-services-vault"></a>복구 서비스 볼트의 백업 경고

경고는 주로 관련 조치를 취할 수 있도록 사용자에게 알림을 받는 시나리오입니다. **백업 경고** 섹션에는 Azure Backup 서비스에서 생성된 경고가 표시됩니다. 이러한 경고는 서비스에 의해 정의되며 사용자는 경고를 만들 수 없습니다.

### <a name="alert-scenarios"></a>경고 시나리오

다음 시나리오는 서비스에서 경고 가능한 시나리오로 정의됩니다.

- 백업/복원 실패
- Azure Backup 에이전트(MAB)에 대한 경고와 함께 백업 성공
- 삭제 데이터로 유지 데이터/보호 중지로 보호 중지

### <a name="alerts-from-the-following-azure-backup-solutions-are-shown-here"></a>다음 Azure 백업 솔루션의 경고가 여기에 표시됩니다.

- Azure VM 백업
- Azure 파일 백업
- SQL, SAP HANA와 같은 Azure 워크로드 백업
- Azure Backup 에이전트(MAB)

> [!NOTE]
> 시스템 센터 데이터 보호 관리자(SC-DPM), Microsoft Azure 백업 서버(MABS)의 경고는 여기에 표시되지 않습니다.

### <a name="consolidated-alerts"></a>통합 경고

SQL 및 SAP HANA와 같은 Azure 워크로드 백업 솔루션의 경우 정책에 따라 최대 15분마다 로그 백업을 매우 자주 생성할 수 있습니다. 따라서 로그 백업 실패가 매우 빈번할 수도 있습니다(최대 15분마다). 이 시나리오에서는 각 오류 발생에 대 한 경고가 발생 하는 경우 최종 사용자가 압도 됩니다. 따라서 첫 번째 발생에 대한 경고가 전송되고 후속 오류가 동일한 근본 원인 으로 인해 발생하면 추가 경고가 생성되지 않습니다. 첫 번째 경고는 실패 횟수로 업데이트됩니다. 그러나 사용자가 경고를 비활성화하면 다음 발생이 다른 경고를 트리거하고 이 경고는 해당 발생에 대한 첫 번째 경고로 처리됩니다. Azure Backup이 SQL 및 SAP HANA 백업에 대한 경고 통합을 수행하는 방법입니다.

### <a name="exceptions-when-an-alert-is-not-raised"></a>경고가 발생하지 않는 경우 예외

오류에 대해 경고가 발생하지 않는 경우 몇 가지 예외가 있습니다. 아래에 이 계정과 키의 예제가 나와 있습니다.

- 사용자가 실행 중인 작업을 명시적으로 취소했습니다.
- 다른 백업 작업이 진행 중이기 때문에 작업이 실패합니다(이전 작업이 완료될 때까지 기다려야 하기 때문에 여기서는 아무 작업도 수행하지 않습니다).
- 백업된 Azure VM이 더 이상 존재하지 않으므로 VM 백업 작업이 실패합니다.
- [통합 경고](#consolidated-alerts)

위의 예외는 이러한 작업(주로 사용자가 트리거한)의 결과가 포털/PS/CLI 클라이언트에 즉시 표시된다는 것을 이해하여 설계되었습니다. 따라서 사용자는 즉시 인식하고 알림이 필요하지 않습니다.

### <a name="alert-types"></a>경고 유형

경고 심각도에 따라 경고는 다음 세 가지 유형으로 정의할 수 있습니다.

- **중요**: 원칙적으로 백업 또는 복구 실패(예약또는 사용자가 트리거됨)는 경고 생성으로 이어지며 위험 경고및 삭제 백업과 같은 파괴적인 작업으로 표시됩니다.
- **경고:** 백업 작업이 성공했지만 경고가 거의 없는 경우 경고 경고로 나열됩니다.
- **정보**: 현재 Azure Backup 서비스에서 정보 경고가 생성되지 않습니다.

## <a name="notification-for-backup-alerts"></a>백업 경고 알림

> [!NOTE]
> 알림 구성은 Azure Portal을 통해서만 수행할 수 있습니다. PS/CLI/REST API/Azure 리소스 관리자 템플릿 지원지원은 지원되지 않습니다.

경고가 발생하면 사용자에게 알림이 전송됩니다. Azure Backup은 전자 메일을 통해 붙박이 알림 메커니즘을 제공합니다. 경고가 생성될 때 알림을 받을 개별 이메일 주소 또는 메일 그룹을 지정할 수 있습니다. 또한 각 개별 경고에 대한 알림을 받을지 또는 시간별 다이제스트에서 알림을 받을지 여부를 선택할 수 있습니다.

![RS 볼트 붙들인 이메일 알림](media/backup-azure-monitoring-laworkspace/rs-vault-inbuiltnotification.png)

알림이 구성되면 환영 또는 소개 이메일을 받게 됩니다. 이렇게 하면 Azure Backup에서 경고가 발생하면 이러한 주소로 전자 메일을 보낼 수 있습니다.<br>

빈도가 시간당 다이제스트로 설정되고 경고가 1시간 이내에 발생및 해결된 경우 다가오는 시간별 다이제스트의 일부가 아닙니다.

> [!NOTE]
>
> - 삭제 데이터를 사용하여 **보호 중지와** 같은 파괴적인 작업이 수행되면 서비스 복구 자격 증명 모음에 대한 알림이 구성되지 않은 경우에도 경고가 발생되고 구독 소유자, 관리자 및 공동 관리자에게 전자 메일이 전송됩니다.
> - 성공적인 작업에 대한 알림을 구성하려면 [로그 분석을](backup-azure-monitoring-use-azuremonitor.md#using-log-analytics-workspace)사용합니다.

## <a name="inactivating-alerts"></a>알림 비활성화

활성 경고를 비활성화/해결하려면 비활성화하려는 경고에 해당하는 목록 항목을 클릭할 수 있습니다. 그러면 경고에 대한 자세한 정보가 표시되는 화면이 열리며 상단에 '비활성화' 버튼이 표시됩니다. 이 버튼을 클릭하면 경고 상태가 '비활성'으로 변경됩니다. 또한 해당 경고에 해당하는 목록 항목을 마우스 오른쪽 단추로 클릭하고 '비활성화'를 선택하여 경고를 비활성화할 수도 있습니다.

![RS 볼트 경고 비활성화](media/backup-azure-monitoring-laworkspace/vault-alert-inactivation.png)

## <a name="next-steps"></a>다음 단계

[Azure 모니터를 사용하여 Azure 백업 워크로드 모니터링](backup-azure-monitoring-use-azuremonitor.md)
