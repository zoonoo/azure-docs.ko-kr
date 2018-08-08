---
title: Azure Backup 중앙 보고 콘텐츠 팩 업데이트
description: Power BI에서 Azure Backup 콘텐츠 팩 업데이트에 대한 정보
services: backup
documentationcenter: ''
author: adigan
manager: shivamg
editor: ''
ms.assetid: 59df5bec-d959-457d-8731-7b20f7f1013e
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/24/2018
ms.author: adigan;
ms.openlocfilehash: 07774234849d96b9a44678b68ca7b13b6b4830f9
ms.sourcegitcommit: a5eb246d79a462519775a9705ebf562f0444e4ec
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/26/2018
ms.locfileid: "39267179"
---
# <a name="updating-azure-backup-central-reporting-content-pack"></a>Azure Backup 중앙 보고 콘텐츠 팩 업데이트 

[Azure Backup 콘텐츠 팩](https://docs.microsoft.com/azure/backup/backup-azure-configure-reports#view-reports-in-power-bi)은 백업에 대한 보고서를 중앙에서 보는 데 사용할 수 있습니다. 콘텐츠 팩은 더 많은 기능을 추가 하고 버그를 수정하기 위해 정기적으로 업데이트됩니다. 이 문서는 콘텐츠 팩을 업데이트하고 업데이트를 지연시키기 위한 단계 및 시간 경과에 따라 수행되는 업데이트를 안내합니다.

## <a name="how-to-get-updates-to-the-content-pack"></a>콘텐츠 팩 업데이트를 가져오는 방법

### <a name="to-get-the-updated-content-pack"></a>업데이트된 콘텐츠 팩을 가져오려면
콘텐츠 팩의 복사본은 아무 것도 변경하지 않은 경우 자동으로 업데이트됩니다. 콘텐츠 팩이 변경된 경우 PowerBI에서 알림 및 동일한 내용의 이메일 알림을 받게 됩니다. 편의에 따라 업데이트된 콘텐츠 팩을 가져오도록 선택할 수 있습니다. 

### <a name="to-delay-the-update"></a>업데이트를 지연시키려면
[사용자 지정 작업 영역](https://youtu.be/26zyOtyHPJM?t=1m57s)에 콘텐츠 팩을 가져오는 것이 좋습니다. 이제 보고서를 편집할 능력이 있습니다.
위에서 설명한 대로 콘텐츠 팩이 변경된 경우 PowerBI에 알림이 표시됩니다. 나중에 콘텐츠 팩을 가져오도록 선택할 수 있습니다. 

## <a name="coming-soon"></a>서비스 예정
   
Azure Backup 콘텐츠 팩은 MAB 및 Azure VM Backup에 대한 현재의 지원 외에 IaaS VM 백업의 SQL 및 SC DPM처럼 더 많은 워크 로드를 지원하기 위해 업데이트됩니다. 하나의 중앙 위치에서 모든 백업 데이터를 곧 보고 분석할 수 있다는 것을 의미합니다. [보고서를 사용자 지정하여](https://youtu.be/26zyOtyHPJM) 조직의 요구에 맞출 수 있습니다.

워크로드에서 보고서를 보다 의미 있게 만들려면 Azure Backup 콘텐츠 팩을 사용하여 미리 구성된 보고서 집합이 변경됩니다. 예정된 보고서 집합의 미리 보기는 다음에서 사용할 수 있습니다.

### <a name="summary"></a>요약
   
![요약](.\media\backup-azure-central-reporting\AzBackup-Central-Reporting-Summary.png)

### <a name="billing"></a>결제

![결제](.\media\backup-azure-central-reporting\AzBackup-Central-Reporting-Billing.png)

### <a name="compliance"></a>규정 준수

![규정 준수](.\media\backup-azure-central-reporting\AzBackup-Central-Reporting-Compliance.png)

### <a name="storage"></a>Storage

![Storage](.\media\backup-azure-central-reporting\AzBackup-Central-Reporting-Storage.png)

### <a name="backup-items"></a>Backup 항목
![BackupItems](.\media\backup-azure-central-reporting\AzBackup-Central-Reporting-BackupItem.png)

### <a name="alerts"></a>Alerts

![Alerts](.\media\backup-azure-central-reporting\AzBackup-Central-Reporting-Alerts.png)

### <a name="jobs"></a>교육

![교육](.\media\backup-azure-central-reporting\AzBackup-Central-Reporting-Jobs.png)
    

## <a name="next-steps"></a>다음 단계:

* [조직에서 보고서 공유](https://youtu.be/26zyOtyHPJM)
* [Azure Backup - FAQ](backup-azure-backup-faq.md)
