---
title: '자습서: 파이프라인을 사용하여 오케스트레이션 시작하기'
description: 이 자습서에서는 Synapse Studio를 사용하여 파이프라인 및 활동을 오케스트레이션하는 방법에 대해 알아봅니다.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: pipeline
ms.topic: tutorial
ms.date: 10/16/2020
ms.openlocfilehash: 42d2ac6cf6592f8e22b0a66aee84c3436d466572
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/21/2020
ms.locfileid: "92329886"
---
# <a name="orchestrate-with-pipelines"></a>파이프라인을 사용하여 오케스트레이션

이 자습서에서는 Synapse Studio를 사용하여 파이프라인 및 활동을 오케스트레이션하는 방법에 대해 알아봅니다. 

## <a name="overview"></a>개요

Azure Synapse에서는 다양한 작업을 오케스트레이션할 수 있습니다.

1. Synapse Studio에서 **통합** 허브로 이동합니다.
1. **+**  > **파이프라인** 을 선택하여 새 파이프라인을 만듭니다.
1. **개발** 허브로 이동하여 이전에 만든 Notebooks 중 하나를 선택합니다.
1. 해당 Notebook을 파이프라인으로 끕니다( **참고** : 파이프라인에서 실행하는 동안 필요한 [문서](https://docs.microsoft.com/azure/synapse-analytics/spark/synapse-spark-sql-pool-import-export#transfer-data-to-or-from-a-sql-pool-attached-with-the-workspace)에 지정된 대로 Notebook에 모듈 가져오기 단계 추가)
1. 파이프라인에서 **트리거 추가** > **새로 만들기/편집** 을 차례로 선택합니다.
1. **트리거 선택** 에서 **새로 만들기** 를 선택하고 **반복** 을 "1시간마다"로 설정합니다.
1. **확인** 을 선택합니다. 
1. **모두 게시** 를 선택합니다.
1. 다음 시간을 기다리지 않고 즉시 파이프라인을 실행하려면 **트리거 추가** > **지금 트리거** 를 선택합니다.



## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Power BI를 사용하여 데이터 시각화](get-started-visualize-power-bi.md)
                                 
