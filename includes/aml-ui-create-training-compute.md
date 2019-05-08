---
title: 포함 파일
description: 포함 파일
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 05/06/2019
ms.openlocfilehash: cf35651f7dd839e8792029851b9bfe278036624c
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028517"
---
실험 계산 대상, 작업 영역에 연결 된 계산 리소스에서 실행 됩니다.  계산 대상을 만든 후 이후 실행에 대 한 다시 사용할 수 있습니다.

1. 선택 **실행** 실험을 실행 하려면 맨 아래에 있습니다.

     ![실험 실행](./media/aml-ui-create-training-compute/run-experiment.png)

1. 경우는 **계산 대상 설정** 경우 작업 영역에 이미 계산 리소스를 선택할 수 있습니다이 이제 대화 상자가 나타납니다.  선택이 고, 그렇지 **새로 만들기**합니다.

    > [!NOTE]
    > 만 있는 시각적 인터페이스 Machine Learning Compute 대상에 실험을 실행할 수 있습니다. 다른 계산 대상은 표시 되지 않습니다.

1. 계산 리소스에 대 한 이름을 제공 합니다.

1. **실행**을 선택합니다.

    ![계산 대상 설정](./media/aml-ui-create-training-compute/set-compute.png)

    계산 리소스 이제 만들어질 수 있습니다. 실험의 오른쪽 위 모서리에 있는 상태를 봅니다. 

    > [!NOTE]
    > 계산 리소스를 만들려면 약 5 분이 걸립니다. 리소스를 만든 후 다시 사용할 수 있으며이 대기 시간 이후 실행을 건너뛸 수 있습니다.
    >
    > 계산 리소스 비용을 절감할 수 유휴 상태일 때 0 노드로 자동 크기 조정이 됩니다.  잠시 후 다시 사용 하는 경우 다시 확장 하는 동안 약 5 분 대기 시간을 다시 발생할 수 있습니다.
