---
title: Azure PowerShell 스크립트 샘플 - Log Analytics 작업 영역 만들기 | Microsoft Docs
description: Azure PowerShell 스크립트 샘플 - Log Analytics 작업 영역 만들기
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
tags: ''
ms.assetid: ''
ms.service: log-analytics
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/07/2017
ms.author: magoedte
ms.openlocfilehash: 4dc98fc3912f9d9c659ecfcaa3df253d30bfcc78
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "66129183"
---
# <a name="create-a-log-analytics-workspace-with-powershell"></a>PowerShell을 사용하여 Log Analytics 작업 영역 만들기

이 스크립트는 데이터 수집, 분석 및 작업을 시작하려는 경우 필요한 Azure Log Analytics 작업 영역을 통해 신속하게 시작하고 실행할 수 있습니다.  

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>샘플 스크립트

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../powershell_scripts/log-analytics/log-analytics-create-new-resource/log-analytics-create-new-resource.ps1 "Create new Log Analytics workspace")]

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용하여 구독에 새 Log Analytics 작업 영역을 만듭니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [Get-AzOperationalInsightsWorkspace](/powershell/module/az.operationalinsights/get-azoperationalinsightsworkspace) | 기존 작업 영역에 대한 정보를 가져옵니다. |
| [New-AzOperationalInsightsWorkspace](/powershell/module/az.operationalinsights/new-azoperationalinsightsworkspace) | 지정된 리소스 그룹 및 위치에 작업 영역을 만듭니다. |


## <a name="next-steps"></a>다음 단계

Azure PowerShell 모듈에 대한 자세한 내용은 [Azure PowerShell 설명서](/powershell/azure/overview)를 참조하세요.

