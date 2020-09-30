---
title: '자습서: 파이프라인을 사용하여 오케스트레이션 시작하기'
description: 이 자습서에서는 Synapse Studio를 사용하여 파이프라인 및 활동을 오케스트레이션하는 방법에 대해 알아봅니다.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 07/20/2020
ms.openlocfilehash: 72eea7c46dd005cd16ae5b8f0022c1174dd28f27
ms.sourcegitcommit: 0194a29a960e3615f96a2d9d8a7e681cf3e8f9ab
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/10/2020
ms.locfileid: "89667481"
---
# <a name="orchestrate-with-pipelines"></a>파이프라인을 사용하여 오케스트레이션

이 자습서에서는 Synapse Studio를 사용하여 파이프라인 및 활동을 오케스트레이션하는 방법에 대해 알아봅니다. 

## <a name="overview"></a>개요

Azure Synapse에서는 다양한 작업을 오케스트레이션할 수 있습니다.

1. Synapse Studio에서 **오케스트레이션** 허브로 이동합니다.
1. **+**  > **파이프라인**을 선택하여 새 파이프라인을 만듭니다.
1. **개발** 허브로 이동하여 이전에 만든 Notebooks 중 하나를 선택합니다.
1. 해당 Notebook을 파이프라인으로 끕니다.
1. 파이프라인에서 **트리거 추가** > **새로 만들기/편집**을 차례로 선택합니다.
1. **트리거 선택**에서 **새로 만들기**를 선택한 다음, **되풀이**에서 트리거가 1시간마다 실행되도록 설정합니다.
1. **확인**을 선택합니다.
1. **모두 게시**를 선택합니다. 파이프라인이 1시간마다 실행됩니다.
1. 다음 시간을 기다리지 않고 지금 파이프라인을 실행하려면 **트리거 추가** > **새로 만들기/편집**을 차례로 선택합니다.



## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Power BI를 사용하여 데이터 시각화](get-started-visualize-power-bi.md)
                                 
