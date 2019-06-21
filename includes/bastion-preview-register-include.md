---
title: 포함 파일
description: 포함 파일
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: include
ms.date: 05/28/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: da4177fd54c0d8777f15175cea3a74a8b01c0954
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67305306"
---
1. Azure 계정에 로그인 하 고이 미리 보기에 대 한 온 보 딩 하려는 구독을 사용 하는 있는지 확인 합니다. 다음 예제를 사용하여 등록합니다.

    ```azurepowershell-interactive
    Register-AzProviderFeature -FeatureName AllowBastionHost -ProviderNamespace Microsoft.Network
    ```
2.  사용 하 여 구독을 다시 등록 합니다 *Microsoft.Network* 공급자 네임 스페이스입니다.

    ```azurepowershell-interactive
    Register-AzResourceProvider -ProviderNamespace Microsoft.Network
    ````
3. 확인 하려면 다음 명령을 사용 합니다 *AllowBastionHost* 기능이 구독에 등록 됩니다.

    ```azurepowershell-interactive
    Get-AzProviderFeature -ProviderNamespace Microsoft.Network
    ````

    등록이 완료까지 몇 분 정도 걸릴 수 있습니다.
