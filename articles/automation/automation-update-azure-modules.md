---
title: "Azure Automation에서 Azure 모듈 업데이트 | Microsoft Docs"
description: "이 문서에서는 Azure Automation에 기본적으로 제공되는 일반 Azure PowerShell 모듈을 즉시 업데이트하는 방법을 설명합니다."
services: automation
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: tysonn
ms.assetid: 
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/13/2017
ms.author: magoedte
translationtype: Human Translation
ms.sourcegitcommit: 7a2999b3b1a54668f6ef45433efabd5a495418fe
ms.openlocfilehash: ec84df70d4a77e3b81a88aa286fc492d92e3e753


---

# <a name="how-to-update-azure-powershell-modules-in-azure-automation"></a>Azure Automation에서 Azure PowerShell 모듈을 업데이트하는 방법

기본적으로 각 Automation 계정에 가장 일반적인 Azure PowerShell 모듈이 제공됩니다.  Azure 팀이 Azure 모듈을 정기적으로 업데이트하므로 새 버전이 출시되면 사용자가 계정에서 모듈을 업데이트할 수 있는 방법을 Automation 계정에 제공하고 있습니다.

## <a name="updating-azure-modules"></a>Azure 모듈 업데이트

1. Automation 계정의 모듈 계정 블레이드에는 **Azure 모듈 업데이트**라고 하는 옵션이 있습니다.  이 옵션은 항상 사용됩니다.<br><br> ![모듈 블레이드의 Azure 모듈 업데이트 옵션](media/automation-update-azure-modules/automation-update-azure-modules-option.png)

2. **Azure 모듈 업데이트**를 클릭하면 계속 진행할 것인지 묻는 확인 알림이 나타납니다.<br><br> ![Azure 모듈 업데이트 알림](media/automation-update-azure-modules/automation-update-azure-modules-notification.png)

3. **예**를 클릭하면 모듈 업데이트 프로세스가 시작됩니다.  업데이트 프로세스에서 다음 모듈을 업데이트하는 데 15-20분 정도 걸립니다.

  * Azure
  *    Azure.Storage
  *    AzureRm.Automation
  *    AzureRm.Compute
  *    AzureRm.Profile
  *    AzureRm.Resources
  *    AzureRm.Sql
  * AzureRm.Storage

    모듈이 이미 최신 상태이면 프로세스가 몇 초 안에 완료됩니다.  업데이트 프로세스가 완료되면 알림이 표시됩니다.<br><br> ![Azure 모듈 업데이트 상태 업데이트](media/automation-update-azure-modules/automation-update-azure-modules-updatestatus.png)

업데이트 프로세스의 일부로, 예약된 runbook의 일정은 최신 모듈을 사용하도록 업데이트됩니다.

runbook에서 이러한 Azure PowerShell 모듈의 cmdlet을 사용하여 Azure 리소스를 관리하는 경우 최신 모듈을 사용하도록 매달 이 업데이트 프로세스를 수행하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

통합 모듈에 대해 알아보고 Automation을 다른 시스템, 서비스 또는 솔루션과 통합하는 사용자 지정 모듈을 만드는 방법을 알아보려면 [통합 모듈](automation-integration-modules.md)을 참조하세요.


<!--HONumber=Feb17_HO2-->


