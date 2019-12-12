---
title: Azure CLI 스크립트 예제 - 변환 만들기 | Microsoft Docs
description: Transform은 비디오 또는 오디오 파일(흔히 "레시피"라고도 함)을 처리하는 간단한 작업 워크플로를 설명합니다. 이 문서의 Azure CLI 스크립트는 변환을 만드는 방법을 보여줍니다.
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
ms.openlocfilehash: c21a16d043f972042949d6340985774741b3df6a
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/06/2019
ms.locfileid: "74888618"
---
# <a name="cli-example-create-a-transform"></a>CLI 예제: Transform 만들기

이 문서의 Azure CLI 스크립트는 변환을 만드는 방법을 보여줍니다. Transform은 비디오 또는 오디오 파일(흔히 "레시피"라고도 함)을 처리하는 간단한 작업 워크플로를 설명합니다. 원하는 이름 및 "레시피"를 가진 Transform이 이미 존재하는지 항상 확인해야 합니다. 그럴 경우 다시 사용해야 합니다.

## <a name="prerequisites"></a>필수 조건 

[Media Services 계정 만들기](create-account-cli-how-to.md)

[!INCLUDE [media-services-cli-instructions.md](../../../includes/media-services-cli-instructions.md)]

> [!NOTE]
> [StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset)에 대한 사용자 지정 표준 인코더 사전 설정 JSON 파일의 경로만 지정할 수 있습니다. [사용자 지정 변환으로 인코딩](custom-preset-cli-howto.md) 예제를 참조하세요.
>
> [BuiltInStandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset)을 사용할 때는 파일 이름을 전달할 수 없습니다.

## <a name="example-script"></a>예제 스크립트

[!code-azurecli-interactive[main](../../../cli_scripts/media-services/create-transform/Create-Transform.sh "Create a transform")]

## <a name="next-steps"></a>다음 단계

[az ams transform(CLI)](https://docs.microsoft.com/cli/azure/ams/transform?view=azure-cli-latest)
