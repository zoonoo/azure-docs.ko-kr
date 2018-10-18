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
ms.date: 10/16/2018
ms.author: juliako
ms.openlocfilehash: eae9947b7dcbd6f2c52fb0d4abc65375aed7766e
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/17/2018
ms.locfileid: "49378791"
---
# <a name="cli-example-create-an-azure-event-grid-subscription"></a>CLI 예: Azure Event Grid 구독을 만들기 

이 문서의 Azure CLI 스크립트는 Job State Changes에 대한 계정 수준 Event Grid 구독을 만드는 방법을 보여줍니다.

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

CLI를 로컬로 설치하여 사용하도록 선택하는 경우 이 문서에서 Azure CLI 버전 2.0.20 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드가 필요한 경우, [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요. 

## <a name="example-script"></a>예제 스크립트

[!code-azurecli-interactive[main](../../../../cli_scripts/media-services/create-event-grid/Create-EventGrid.sh "Create an EventGrid subscription")]

## <a name="next-steps"></a>다음 단계

자세한 내용은 [Azure CLI 예](../cli-samples.md)를 참조하세요.
