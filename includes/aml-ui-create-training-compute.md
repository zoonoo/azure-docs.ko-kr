---
title: 포함 파일
description: 포함 파일
services: machine-learning
author: peterclu
ms.service: machine-learning
ms.author: peterlu
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 10/09/2019
ms.openlocfilehash: f7cdfb8b9edbce5ef2b094cf6a603904d39a7404
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73493633"
---
작업 영역에 연결된 컴퓨팅 리소스인 컴퓨팅 대상에서 파이트라인이 실행됩니다. 컴퓨팅 대상을 만든 후 이후 실행에 다시 사용할 수 있습니다.

1. 캔버스 위쪽에 있는 **실행**을 선택하여 파이프라인을 실행합니다.

1. **설정** 창이 표시되면 **컴퓨팅 대상 선택**을 선택합니다.

    사용 가능한 컴퓨팅 대상이 이미 있는 경우 파이프라인을 실행하도록 선택할 수 있습니다.

    > [!NOTE]
    > 디자이너는 Machine Learning 컴퓨팅 대상에서만 실험을 실행할 수 있습니다. 다른 컴퓨팅 대상은 표시되지 않습니다.

1. 컴퓨팅 리소스에 이름을 제공합니다.

1. **저장**을 선택합니다.

    ![컴퓨팅 대상 설정](./media/aml-ui-create-training-compute/set-compute.png)

1. **실행**을 선택합니다.

1. **파이프라인 실행 설정** 대화 상자에서 **실험**에 대한 **+ 새 실험**을 선택합니다.

    > [!NOTE]
    > 실험 그룹은 유사한 파이프라인을 함께 실행합니다. 파이프라인을 여러 번 실행하는 경우 연속 실행에 대해 동일한 실험을 선택할 수 있습니다.

    * 설명이 포함된 **실험 이름**을 입력합니다.

    * **실행**을 선택합니다.
    
    캔버스의 오른쪽 위에서 실행 상태와 세부 정보를 볼 수 있습니다.

    > [!NOTE]
    > 컴퓨팅 리소스를 만드는 데 약 5분이 걸립니다. 리소스가 만들어지면 다시 사용할 수 있으며 이후 실행에는 이 대기 시간을 건너뛸 수 있습니다.
    >
    > 컴퓨팅 리소스는 비용을 절감하기 위해 유휴 상태일 때 0개 노드로 자동 크기 조정됩니다.  지연 후 다시 사용할 경우 백업 크기를 조정하는 동안 약 5분의 대기 시간이 다시 발생할 수 있습니다.
