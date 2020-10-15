---
title: 백업 센터-FAQ
description: 이 문서는 백업 센터에 대 한 자주 묻는 질문에 답변 합니다.
ms.topic: conceptual
ms.date: 09/08/2020
ms.openlocfilehash: 7e227eb6a14d06791e52ec33e090afdcb01bab61
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2020
ms.locfileid: "92094044"
---
# <a name="backup-center---frequently-asked-questions"></a>백업 센터-질문과 대답

## <a name="management"></a>관리

### <a name="can-backup-center-be-used-across-tenants"></a>테 넌 트 간에 백업 센터를 사용할 수 있나요?

예, [Azure Lighthouse](https://docs.microsoft.com/azure/lighthouse/overview) 를 사용 하 고 다른 테 넌 트의 구독에 대 한 액세스 권한을 위임 하는 경우 백업 센터를 단일 창으로 사용 하 여 테 넌 트 간의 백업을 관리할 수 있습니다.

### <a name="can-backup-center-be-used-to-manage-both-recovery-services-vaults-and-backup-vaults"></a>백업 센터를 사용 하 여 Recovery Services 자격 증명 모음 및 백업 자격 증명 모음을 관리할 수 있나요?

예, 백업 센터는 [Recovery Services 자격 증명 모음](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) 및 [백업 자격 증명 모음](backup-vault-overview.md)을 모두 관리할 수 있습니다.

### <a name="is-there-a-delay-before-data-surfaces-in-backup-center"></a>백업 센터의 데이터 표면 앞에 지연이 있나요?

백업 센터는 실시간 정보를 제공 하기 위한 것입니다. 엔터티가 개별 자격 증명 모음 화면에 표시 되는 시간과 동일한 엔터티가 백업 센터에 표시 되는 시간 사이에 몇 초 정도 지연 될 수 있습니다.

## <a name="configuration"></a>구성

### <a name="do-i-need-to-configure-anything-to-see-data-in-backup-center"></a>백업 센터에서 데이터를 볼 수 있도록 아무것도 구성 해야 하나요?

아니요. 백업 센터는 즉시 제공 됩니다. 그러나 백업 센터에서 [백업 보고서](https://docs.microsoft.com/azure/backup/configure-reports) 를 보려면 자격 증명 모음에 대 한 보고를 구성 해야 합니다.

### <a name="do-i-need-to-have-any-special-permissions-to-use-backup-center"></a>백업 센터를 사용 하려면 특별 한 권한이 필요 한가요?

백업 센터에는 새 권한이 필요 하지 않습니다. 관리 중인 리소스에 대 한 올바른 수준의 Azure RBAC 액세스 권한이 있는 한, 이러한 리소스에 대해 백업 센터를 사용할 수 있습니다. 예를 들어 백업에 대 한 정보를 보려면 자격 증명 모음에 대 한 **읽기** 권한이 필요 합니다. 백업을 구성 하 고 다른 백업 관련 작업을 수행 하려면 **백업 참여자** 또는 **백업 운영자** 역할이 필요 합니다. [Azure Backup에 대 한 Azure 역할](https://docs.microsoft.com/azure/backup/backup-rbac-rs-vault)에 대해 자세히 알아보세요.

## <a name="pricing"></a>가격 책정

### <a name="do-i-need-to-pay-anything-extra-to-use-backup-explorer"></a>백업 탐색기를 사용 하기 위해 추가 비용을 지불 해야 하나요?

현재 backup Center를 사용 하는 데 추가 비용이 없습니다 (백업 비용과 별도로). 그러나 백업 센터에서 [백업 보고서](https://docs.microsoft.com/azure/backup/configure-reports) 를 사용 하는 경우 백업 보고서에 Azure Monitor 로그를 사용 하는 [데 비용이 듭니다](https://azure.microsoft.com/pricing/details/monitor/) .

## <a name="next-steps"></a>다음 단계

다른 FAQ를 읽어보세요.

* [Recovery Services 자격 증명 모음에 대 한 일반적인 질문](https://docs.microsoft.com/azure/backup/backup-azure-backup-faq)
* [Azure VM 백업에 대 한 일반적인 질문](https://docs.microsoft.com/azure/backup/backup-azure-vm-backup-faq)
