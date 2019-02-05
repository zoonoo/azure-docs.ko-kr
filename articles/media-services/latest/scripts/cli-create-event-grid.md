---
title: Azure CLI 스크립트 예제 - Azure Event Grid 구독 만들기 | Microsoft Docs
description: 이 항목의 Azure CLI 스크립트는 Job State Changes에 대한 계정 수준 Event Grid 구독을 만드는 방법을 보여줍니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 01/25/2019
ms.author: juliako
ms.openlocfilehash: 2fcacd559db0ab143ffa7ed49bbd546785765154
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/28/2019
ms.locfileid: "55098908"
---
# <a name="cli-example-create-an-azure-event-grid-subscription"></a>CLI 예제: Azure Event Grid 구독 만들기 

이 문서의 Azure CLI 스크립트는 Job State Changes에 대한 계정 수준 Event Grid 구독을 만드는 방법을 보여줍니다.

## <a name="prerequisites"></a>필수 조건 

[Media Services 계정 만들기](../create-account-cli-how-to.md)

[!INCLUDE [media-services-cli-instructions.md](../../../../includes/media-services-cli-instructions.md)]

## <a name="example-script"></a>예제 스크립트

[!code-azurecli-interactive[main](../../../../cli_scripts/media-services/create-event-grid/Create-EventGrid.sh "Create an EventGrid subscription")]

## <a name="next-steps"></a>다음 단계

자세한 내용은 [Azure CLI 예](../cli-samples.md)를 참조하세요.
