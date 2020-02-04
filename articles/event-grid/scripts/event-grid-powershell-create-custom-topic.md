---
title: Azure PowerShell 스크립트 샘플 - 사용자 지정 항목 만들기 | Microsoft Docs
description: 이 문서에서는 Event Grid 사용자 지정 항목을 만드는 방법을 보여주는 샘플 Azure PowerShell 스크립트를 제공합니다.
services: event-grid
documentationcenter: na
author: spelluru
ms.service: event-grid
ms.devlang: powershell
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2020
ms.author: tomfitz
ms.openlocfilehash: 900f31ff87e6a588fc1d1e3efd854437d2c8c1b9
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/24/2020
ms.locfileid: "76719945"
---
# <a name="create-event-grid-custom-topic-with-powershell"></a>PowerShell을 사용하여 Event Grid 사용자 지정 항목 만들기

이 스크립트는 Event Grid 사용자 지정 항목을 만듭니다.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>샘플 스크립트

[!code-powershell[main](../../../powershell_scripts/event-grid/create-custom-topic/create-custom-topic.ps1 "Create custom topic")]

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용하여 사용자 지정 항목을 만듭니다. 표에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [New-AzEventGridTopic](https://docs.microsoft.com/powershell/module/az.eventgrid/new-azeventgridtopic) | Event Grid 사용자 지정 항목을 만듭니다. |

## <a name="next-steps"></a>다음 단계

* 관리되는 애플리케이션에 대한 소개는 [Azure Managed Application 개요](../overview.md)를 참조하세요.
* PowerShell에 대한 자세한 내용은 [Azure PowerShell 설명서](https://docs.microsoft.com/powershell/azure/get-started-azureps)를 참조하세요.
