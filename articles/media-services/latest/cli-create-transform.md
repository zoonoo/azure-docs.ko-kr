---
title: Azure CLI 스크립트 예제 - 변환 만들기 | Microsoft Docs
description: Azure CLI 스크립트를 사용하여 Transform을 만듭니다.
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
ms.date: 05/01/2019
ms.author: juliako
ms.openlocfilehash: 86f455f25bb41833774bb7ad7d81688aad812b5a
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2019
ms.locfileid: "65236808"
---
# <a name="cli-example-create-a-transform"></a>CLI 예제: Transform 만들기

이 문서의 Azure CLI 스크립트는 변환을 만드는 방법을 보여줍니다. Transform은 비디오 또는 오디오 파일(흔히 "레시피"라고도 함)을 처리하는 간단한 작업 워크플로를 설명합니다. 원하는 이름 및 "레시피"를 가진 Transform이 이미 존재하는지 항상 확인해야 합니다. 그럴 경우 다시 사용해야 합니다.

## <a name="prerequisites"></a>필수 조건 

[Media Services 계정 만들기](create-account-cli-how-to.md)

[!INCLUDE [media-services-cli-instructions.md](../../../includes/media-services-cli-instructions.md)]

## <a name="example-script"></a>예제 스크립트

[!code-azurecli-interactive[main](../../../cli_scripts/media-services/create-transform/Create-Transform.sh "Create a transform")]

## <a name="next-steps"></a>다음 단계

[Media Services 개요](media-services-overview.md)
