---
title: Azure PowerShell 스크립트 샘플 - 리소스 그룹 구독 및 리소스로 필터링 | Microsoft Docs
description: Azure PowerShell 스크립트 샘플 - 리소스 그룹 구독 및 리소스로 필터링
services: event-grid
documentationcenter: na
author: tfitzmac
ms.service: event-grid
ms.devlang: powershell
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/10/2018
ms.author: tomfitz
ms.openlocfilehash: 21719ab4e6b999f262ff53adf31d855d6e1833b4
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53271405"
---
# <a name="subscribe-to-events-for-a-resource-group-and-filter-for-a-resource-with-powershell"></a>PowerShell을 사용하여 리소스 그룹에 대한 이벤트를 구독하고 리소스 필터링

이 스크립트는 리소스 그룹에 대한 이벤트에 대한 Event Grid 구독을 만듭니다. 필터를 사용하여 리소스 그룹에서 지정된 리소스에 대한 이벤트만 가져옵니다.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

미리 보기 샘플 스크립트를 사용하려면 Event Grid 모듈이 필요합니다. 설치하려면 `Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`를 실행합니다.

## <a name="sample-script---stable"></a>샘플 스크립트 - 안정적

[!code-powershell[main](../../../powershell_scripts/event-grid/filter-events/filter-events.ps1 "Filter events")]

## <a name="sample-script---preview-module"></a>샘플 스크립트 - 미리 보기 모듈

[!code-powershell[main](../../../powershell_scripts/event-grid/filter-events-preview/filter-events-preview.ps1 "Filter events")]


## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용하여 이벤트 구독을 만듭니다. 표에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [New-AzureRmEventGridSubscription](https://docs.microsoft.com/powershell/module/azurerm.eventgrid/new-azurermeventgridsubscription) | Event Grid 구독을 만듭니다. |

## <a name="next-steps"></a>다음 단계

* 관리되는 응용 프로그램에 대한 소개는 [Azure Managed Application 개요](../overview.md)를 참조하세요.
* PowerShell에 대한 자세한 내용은 [Azure PowerShell 설명서](https://docs.microsoft.com/powershell/azure/get-started-azureps)를 참조하세요.
