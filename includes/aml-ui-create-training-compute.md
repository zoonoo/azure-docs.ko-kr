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
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66397460"
---
작업 영역에 연결된 컴퓨팅 리소스인 컴퓨팅 대상에서 실험이 실행됩니다.  컴퓨팅 대상을 만든 후 이후 실행에 다시 사용할 수 있습니다.

1. 아래쪽에서 **실행**을 선택하여 실험을 실행합니다.

     ![실험 실행](./media/aml-ui-create-training-compute/run-experiment.png)

1. **컴퓨팅 대상 설정** 대화 상자가 나타날 때 작업 영역에 이미 컴퓨팅 리소스가 있는 경우 지금 선택할 수 있습니다.  그렇지 않은 경우 **새로 만들기**를 선택합니다.

    > [!NOTE]
    > 그래픽 인터페이스는 Machine Learning 컴퓨팅 대상에서만 실험을 실행할 수 있으며, 다른 컴퓨팅 대상은 표시되지 않습니다.

1. 컴퓨팅 리소스에 이름을 제공합니다.

1. **실행**을 선택합니다.

    ![컴퓨팅 대상 설정](./media/aml-ui-create-training-compute/set-compute.png)

    이제 컴퓨팅 리소스가 만들어집니다. 실험의 오른쪽 맨 위에서 상태를 봅니다. 

    > [!NOTE]
    > 컴퓨팅 리소스를 만드는 데 약 5분이 걸립니다. 리소스가 만들어지면 다시 사용할 수 있으며 이후 실행에는 이 대기 시간을 건너뛸 수 있습니다.
    >
    > 컴퓨팅 리소스는 비용을 절감하기 위해 유휴 상태일 때 0개 노드로 자동 크기 조정됩니다.  지연 후 다시 사용할 경우 백업 크기를 조정하는 동안 약 5분의 대기 시간이 다시 발생할 수 있습니다.
