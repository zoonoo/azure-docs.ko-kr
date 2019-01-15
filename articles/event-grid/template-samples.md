---
title: Azure Resource Manager 템플릿 샘플 - Event Grid | Microsoft Docs
description: Event Grid를 위한 Azure Resource Manager 템플릿 샘플
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.date: 01/04/2019
ms.author: tomfitz
ms.openlocfilehash: 788d23c7bddd90c1e12a118742c651eb9759529c
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/07/2019
ms.locfileid: "54062964"
---
# <a name="azure-resource-manager-templates-for-event-grid"></a>Event Grid를 위한 Azure Resource Manager 템플릿

템플릿에서 사용할 JSON 구문 및 속성은 [Microsoft.EventGrid 리소스 종류](/azure/templates/microsoft.eventgrid/allversions)를 참조하세요. 다음 표는 Event Grid를 위한 Azure Resource Manager 템플릿 링크를 포함합니다.

| | |
|-|-|
|**Event Grid 구독**||
| [웹후크 엔드포인트를 사용한 사용자 지정 항목 및 구독](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid)| Event Grid 사용자 지정 항목을 배포합니다. 웹후크 엔드포인트를 사용하여 해당 사용자 지정 항목에 대한 구독을 만듭니다. |
| [EventHub 엔드포인트를 사용한 사용자 지정 항목 구독](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-event-hubs-handler)| 사용자 지정 항목에 대한 Event Grid 구독을 만듭니다. 구독은 엔드포인트에 대해 Event Hub를 사용합니다. |
| [Azure 구독 또는 리소스 그룹 구독](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-resource-events-to-webhook)| 리소스 그룹 또는 Azure 구독에 대한 이벤트를 구독합니다. 배포 중 대상으로 지정하는 리소스 그룹은 이벤트의 원본입니다. 구독은 엔드포인트에 웹후크를 사용합니다. |
| [Blob Storage 계정 및 구독](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-subscription-and-storage)| Azure Blob Storage 계정을 배포하고 해당 스토리지 계정에 대한 이벤트를 구독합니다. |
| | |
