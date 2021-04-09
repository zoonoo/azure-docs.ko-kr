---
title: '자습서: 파이프라인과의 통합 시작'
description: 이 자습서에서는 Synapse Studio를 사용하여 파이프라인과 활동을 통합하는 방법에 대해 알아봅니다.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: pipeline
ms.topic: tutorial
ms.date: 12/31/2020
ms.openlocfilehash: 2ea7c3c440fcf95e4512464333efe8461788bceb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98219405"
---
# <a name="integrate-with-pipelines"></a>파이프라인과 통합

이 자습서에서는 Synapse Studio를 사용하여 파이프라인과 활동을 통합하는 방법에 대해 알아봅니다. 

## <a name="overview"></a>개요

Azure Synapse에서는 다양한 작업을 통합할 수 있습니다.

1. Synapse Studio에서 **통합** 허브로 이동합니다.
1. **+**  > **파이프라인** 을 선택하여 새 파이프라인을 만듭니다. 새 파이프라인 개체를 클릭하여 파이프라인 디자이너를 엽니다.
1. **작업** 에서 **Synapse** 폴더를 확장하고 **Notebook** 개체를 디자이너로 끌어옵니다.
1. Notebook 작업 속성의 **설정** 탭을 선택합니다. 드롭다운 목록을 사용하여 현재 Synapse 작업 영역에서 Notebook을 선택합니다. 
1. 파이프라인에서 **트리거 추가** > **새로 만들기/편집** 을 차례로 선택합니다.
1. **트리거 선택** 에서 **새로 만들기** 를 선택하고 **반복** 을 "1시간마다"로 설정합니다.
1. **확인** 을 선택합니다. 
1. **모두 게시** 를 선택합니다. 


## <a name="monitor-pipeline"></a>파이프라인 모니터링

1. 파이프라인이 게시된 후 다음 시간을 기다리지 않고 즉시 파이프라인을 실행하려면 **트리거 추가** > **지금 트리거** 를 선택합니다.
1. Synapse Studio에서 **Monitor** 허브로 이동하고 **파이프라인 실행** 을 선택하여 파이프라인 실행 진행률을 모니터링합니다.



## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Power BI를 사용하여 데이터 시각화](get-started-visualize-power-bi.md)
