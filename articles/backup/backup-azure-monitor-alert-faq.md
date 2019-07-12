---
title: Azure Backup 모니터링 경고 FAQ
description: '다음의 일반적인 질문에 대한 답변: Azure Backup 모니터링 경고'
services: backup
author: srinathvasireddy
manager: sivan
ms.service: backup
ms.topic: conceptual
ms.date: 07/08/2019
ms.author: srinathv
ms.openlocfilehash: bb684f65539b4429862b2dce0e378d8f659d2975
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67705038"
---
# <a name="azure-backup-monitoring-alert---faq"></a>Azure Backup 모니터링 경고-FAQ
이 문서에서는 Azure 모니터링 경고에 대 한 일반적인 질문에 답변 합니다.

## <a name="configure-azure-backup-reports"></a>Azure Backup 보고서 구성

### <a name="how-do-i-check-if-reporting-data-has-started-flowing-into-a-storage-account"></a>보고 데이터가 스토리지 계정으로 전송되기 시작했는지 확인하려면 어떻게 할까요?
구성한 저장소 계정으로 이동한 다음 컨테이너를 선택합니다. 컨테이너에 insights-logs-azurebackupreport에 대한 항목이 있으면 보고 데이터가 흐르기 시작한 것입니다.

### <a name="what-is-the-frequency-of-data-push-to-a-storage-account-and-the-azure-backup-content-pack-in-power-bi"></a>Power BI에서 Azure Backup 콘텐츠 팩 및 스토리지 계정으로 데이터 푸시가 수행되는 빈도는 어느 정도인가요?
  신규 사용자의 경우 저장소 계정에 데이터를 푸시하는 데 약 24시간 정도 걸립니다. 이 초기 푸시가 완료된 후에는 아래 그림에 표시된 빈도로 데이터 새로 고침이 진행됩니다.

  * **작업**, **경고**, **백업 항목**, **자격 증명 모음**, **보호된 서버** 및 **정책**과 관련된 데이터는 기록 시 고객 저장소 계정에 푸시됩니다.

  * **저장소**와 관련된 데이터는 24시간마다 고객 저장소 계정에 푸시됩니다.

       ![Azure Backup 보고서 데이터 푸시 빈도](./media/backup-azure-configure-reports/reports-data-refresh-cycle.png)

  * Power BI에는 [하루에 한 번 예약된 새로 고침](https://powerbi.microsoft.com/documentation/powerbi-refresh-data/#what-can-be-refreshed)이 있습니다. Power BI에서 콘텐츠 팩에 대한 데이터를 수동으로 새로 고칠 수 있습니다.

### <a name="how-long-can-i-retain-reports"></a>보고서를 보존할 수 있는 기간은 어느 정도인가요?
저장소 계정을 구성할 때 저장소 계정의 보고 데이터 보존 기간을 선택할 수 있습니다. [보고서용 스토리지 계정 구성](backup-azure-configure-reports.md#configure-storage-account-for-reports) 섹션의 6단계를 따르세요. [Excel에서 보고서를 분석](https://powerbi.microsoft.com/documentation/powerbi-service-analyze-in-excel/)하고 요구에 따라 더 오랜 보존 기간 동안 보고서를 저장할 수도 있습니다.

### <a name="will-i-see-all-my-data-in-reports-after-i-configure-the-storage-account"></a>스토리지 계정을 구성하면 보고서에서 내 데이터를 모두 볼 수 있나요?
 저장소 계정을 구성한 후 생성된 모든 데이터는 저장소 계정에 푸시되며 보고서에서 확인할 수 있습니다. 진행 중인 작업은 보고를 위해 푸시되지 않으며, 완료되거나 실패하면 보고서로 전송됩니다.

### <a name="if-i-already-configured-the-storage-account-to-view-reports-can-i-change-the-configuration-to-use-another-storage-account"></a>보고서를 보는 데 사용할 스토리지 계정을 이미 구성한 경우 다른 스토리지 계정을 사용하도록 구성을 변경할 수 있나요?
예, 다른 저장소 계정을 가리키도록 구성을 변경할 수 있습니다. Azure Backup 콘텐츠 팩에 연결하는 동안 새로 구성된 저장소 계정을 사용하면 됩니다. 또한 다른 저장소 계정이 구성된 후 새 데이터는 이 저장소 계정으로 전송됩니다. 구성을 변경하기 전의 오래된 데이터는 계속해서 이전 저장소 계정에 유지됩니다.

### <a name="can-i-view-reports-across-vaults-and-subscriptions"></a>여러 자격 증명 모음 및 구독의 보고서를 볼 수 있나요?
예, 다양한 자격 증명 모음에 동일한 저장소 계정을 구성하여 자격 증명 모음 간 보고서를 볼 수 있습니다. 여러 구독의 자격 증명 모음에 대해 동일한 저장소 계정을 구성할 수도 있습니다. 그런 다음 Power BI에서 Azure Backup 콘텐츠 팩에 연결하는 동안 이 저장소 계정을 사용하여 보고서를 볼 수 있습니다. 선택한 저장소 계정은 Recovery Services 자격 증명 모음과 동일한 지역에 있어야 합니다.

### <a name="how-long-does-it-take-for-the-azure-backup-agent-job-status-to-reflect-in-the-portal"></a>Azure 백업 에이전트 작업 상태가 포털에 반영되려면 얼마나 오래 걸리나요?
Azure Portal에서 Azure 백업 에이전트 작업 상태를 반영하는 데 최대 15분이 걸릴 수 있습니다.

### <a name="when-a-backup-job-fails-how-long-does-it-take-to-raise-an-alert"></a>백업 작업이 실패하는 경우 알림이 발생하는 데 얼마나 오래 걸리나요?
알림은 Azure 백업 실패 후 20분 이내에 발생합니다.

### <a name="is-there-a-case-where-an-email-wont-be-sent-if-notifications-are-configured"></a>알림이 구성된 경우 이메일이 전송되지 않는 경우가 있나요?
예. 알림이 전송되지 않는 경우는 다음과 같습니다.

* 알림이 매시간으로 구성되고 알림이 발생하고 한 시간 이내에 해결되는 경우
* 작업이 취소되는 경우
* 진행 중인 원래 백업 작업으로 인해 두 번째 백업 작업이 실패하는 경우

## <a name="recovery-services-vault"></a>Recovery Services 자격 증명 모음

### <a name="how-long-does-it-take-for-the-azure-backup-agent-job-status-to-reflect-in-the-portal"></a>Azure 백업 에이전트 작업 상태가 포털에 반영되려면 얼마나 오래 걸리나요?
Azure Portal에서 Azure 백업 에이전트 작업 상태를 반영하는 데 최대 15분이 걸릴 수 있습니다.

### <a name="when-a-backup-job-fails-how-long-does-it-take-to-raise-an-alert"></a>백업 작업이 실패하는 경우 알림이 발생하는 데 얼마나 오래 걸리나요?
알림은 Azure 백업 실패 후 20분 이내에 발생합니다.

### <a name="is-there-a-case-where-an-email-wont-be-sent-if-notifications-are-configured"></a>알림이 구성된 경우 이메일이 전송되지 않는 경우가 있나요?
예. 알림이 전송되지 않는 경우는 다음과 같습니다.

* 알림이 매시간으로 구성되고 알림이 발생하고 한 시간 이내에 해결되는 경우
* 작업이 취소되는 경우
* 진행 중인 원래 백업 작업으로 인해 두 번째 백업 작업이 실패하는 경우

## <a name="next-steps"></a>다음 단계

다른 FAQ를 읽어보세요.

- Azure VM에 대한 [일반 질문](backup-azure-vm-backup-faq.md)
- Azure Backup 에이전트에 대한 [일반 질문](backup-azure-file-folder-backup-faq.md)
