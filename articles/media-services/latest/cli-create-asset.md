---
title: Azure CLI를 사용하여 Azure Media Services 자산에 콘텐츠 업로드
description: 이 토픽의 Azure CLI 스크립트는 콘텐츠를 업로드 할 Media Services Asset을 만드는 방법을 보여줍니다.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: azurecli
ms.topic: how-to
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 08/18/2020
ms.author: inhenkel
ms.custom: devx-track-azurecli
ms.openlocfilehash: 7b362ba30baf13211dc41a6043dcedd05bd506ea
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88585420"
---
# <a name="create-an-asset"></a>자산 만들기

이 문서에서는 Media Services 자산을 만드는 방법을 보여줍니다.  자산을 사용하여 인코딩 및 스트리밍을 위한 미디어 콘텐츠를 보관할 것입니다.  Media Services 자산에 대한 자세한 내용은 [Azure Media Services v3의 자산](assets-concept.md)을 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

[Media Services 계정 만들기](./create-account-howto.md)의 단계에 따라 자산을 만드는 데 필요한 Media Services 계정 및 리소스 그룹을 만듭니다.

## <a name="methods"></a>메서드

## <a name="cli"></a>[CLI](#tab/cli/)

[!INCLUDE [Create an asset with CLI](./includes/task-create-asset-cli.md)]

## <a name="cli-shell"></a>[CLI 셸](#tab/clishell/)

[!INCLUDE [media-services-cli-instructions.md](../../../includes/media-services-cli-instructions.md)]

## <a name="example-script"></a>예제 스크립트

[!code-azurecli-interactive[main](../../../cli_scripts/media-services/create-asset/Create-Asset.sh "Create an asset")]

---

## <a name="next-steps"></a>다음 단계

[자산 관리](manage-asset-concept.md)
