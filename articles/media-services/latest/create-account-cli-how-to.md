---
title: Azure CLI를 사용하여 Media Services 계정 만들기 - Azure | Microsoft Docs
description: Azure Media Services 계정을 만들려면 빠른 시작의 단계를 수행합니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: seodec18
ms.date: 02/12/2019
ms.author: juliako
ms.openlocfilehash: ce022e22cc98dd218dff4840f686464233f99c72
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56176350"
---
# <a name="create-an-azure-media-services-account"></a>Azure Media Services 계정 만들기

Azure에서 암호화, 인코딩, 분석, 관리 및 스트리밍을 시작하려면 Media Services 계정을 만들어야 합니다. Media Services 계정을 만들 때 연결된 스토리지 계정도 만들거나 기존 스토리지 계정을 사용합니다.  

> [!NOTE]
> Media Services 계정 및 연결된 모든 스토리지 계정은 동일한 Azure 구독에 포함되어야 합니다. 추가 대기 시간 및 데이터 송신 비용이 발생하지 않도록 Media Services 계정과 동일한 위치에 있는 스토리지 계정을 사용하는 것이 좋습니다.

이 문서에서는 Azure CLI를 사용하여 새로운 Azure Media Services 계정을 만드는 단계를 설명합니다.  

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>필수 조건

활성 Azure 구독. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)을 만듭니다.

[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

## <a name="set-the-azure-subscription"></a>Azure 구독 설정

다음 명령에서 Media Services 계정에 사용할 Azure 구독 ID를 제공합니다. [구독](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)으로 이동하면 액세스 권한이 있는 구독 목록을 볼 수 있습니다.

```azurecli
az account set --subscription mySubscriptionId
```
 
[!INCLUDE [media-services-cli-create-v3-account-include](../../../includes/media-services-cli-create-v3-account-include.md)]
 
## <a name="next-steps"></a>다음 단계

[파일 스트리밍](stream-files-dotnet-quickstart.md)

## <a name="see-also"></a>참고 항목

[Azure CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)

