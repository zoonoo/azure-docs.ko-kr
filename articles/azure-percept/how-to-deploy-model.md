---
title: Azure Percept에 비전 AI 모델 배포
description: Azure Percept Studio에서 Azure Percept에 비전 AI 모델을 배포 하는 방법을 알아봅니다.
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/12/2021
ms.custom: template-how-to
ms.openlocfilehash: 01bd3709050d8a2b57c1bf51920308188546fb31
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102035486"
---
# <a name="deploy-a-vision-ai-model-to-your-azure-percept-dk"></a>Azure Percept에 비전 AI 모델 배포

이 가이드에 따라 Azure Percept Studio 내에서 Azure Percept 진한 비전 AI 모델을 배포 합니다.

## <a name="prerequisites"></a>필수 구성 요소

- Azure Percept DK(Devkit)
- [Azure 구독](https://azure.microsoft.com/free/)
- [Azure Percept DK 설정 환경](./quickstart-percept-dk-set-up.md): devkit를 Wi-Fi 네트워크에 연결하고, IoT Hub를 만들고, devkit를 IoT Hub에 연결

## <a name="model-deployment"></a>모델 배포

1. Devkit의 전원을 켭니다.

1. [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819)로 이동합니다.

1. 개요 페이지의 왼쪽에서 **장치** 를 클릭 합니다.

    :::image type="content" source="./media/how-to-deploy-model/overview-devices-inline.png" alt-text="Azure Percept Studio 개요 화면" lightbox="./media/how-to-deploy-model/overview-devices.png":::

1. 목록에서 devkit를 선택 합니다.

    :::image type="content" source="./media/how-to-deploy-model/select-device.png" alt-text="Percept devices 목록.":::

1. 미리 학습 된 샘플 비전 모델 중 하나를 배포 하려는 경우 다음 페이지에서 **샘플 모델 배포** 를 클릭 합니다. 기존 [사용자 지정 비 코드 비전 솔루션](./tutorial-nocode-vision.md)을 배포 하려면 **Custom Vision 프로젝트 배포** 를 클릭 합니다.

    :::image type="content" source="./media/how-to-deploy-model/deploy-model.png" alt-text="배포를 위한 모델 선택.":::

1. 코드 없는 비전 솔루션을 배포 하기로 한 경우 프로젝트 및 기본 설정 모델 반복을 선택 하 고 **배포** 를 클릭 합니다.

1. 샘플 모델을 배포 하려면 모델을 선택 하 고 **장치에 배포** 를 클릭 합니다.

1. 모델 배포에 성공 하면 화면 오른쪽 위에 상태 메시지가 표시 됩니다. 추론 모델을 보려면 상태 메시지에서 **스트림 보기** 링크를 클릭 하 여 Devkit의 비전 SOM에서 RTSP 비디오 스트림을 확인 합니다.

## <a name="next-steps"></a>다음 단계

[Azure PERCEPT 진한 원격 분석](how-to-view-telemetry.md)을 보는 방법에 대해 알아봅니다.