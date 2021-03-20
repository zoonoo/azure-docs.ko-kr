---
title: Azure Percept 진한의 RTSP 비디오 스트림 보기
description: Azure Percept 진한에서 RTSP 비디오 스트림을 보는 방법에 대해 알아봅니다.
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/12/2021
ms.custom: template-how-to
ms.openlocfilehash: 5f77e99dc5c34867fef2b0ac47c709824fa4477d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102096006"
---
# <a name="view-your-azure-percept-dks-rtsp-video-stream"></a>Azure Percept 진한의 RTSP 비디오 스트림 보기

이 가이드에 따라 azure Percept Studio 내에서 Azure Percept의 RTSP 비디오 스트림을 볼 수 있습니다. 사용자의 장치에 배포 된 AI AI 모델의 추론는 웹 스트림에서 볼 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

- Azure Percept DK(Devkit)
- [Azure 구독](https://azure.microsoft.com/free/)
- [Azure Percept DK 설정 환경](./quickstart-percept-dk-set-up.md): devkit를 Wi-Fi 네트워크에 연결하고, IoT Hub를 만들고, devkit를 IoT Hub에 연결

## <a name="view-the-rtsp-video-stream"></a>RTSP 비디오 스트림 보기

1. Devkit의 전원을 켭니다.

1. [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819)로 이동합니다.

1. 개요 페이지의 왼쪽에서 **장치** 를 클릭 합니다.

    :::image type="content" source="./media/how-to-view-video-stream/overview-devices-inline.png" alt-text="Azure Percept Studio 개요 화면" lightbox="./media/how-to-view-video-stream/overview-devices.png":::

1. 목록에서 devkit를 선택 합니다.

    :::image type="content" source="./media/how-to-view-video-stream/select-device.png" alt-text="Azure Percept Studio에서 사용 가능한 장치의 스크린샷":::

1. **장치 스트림 보기** 를 클릭 합니다.

    :::image type="content" source="./media/how-to-view-video-stream/view-device-stream.png" alt-text="사용 가능한 비전 프로젝트 작업을 보여 주는 장치 페이지의 스크린샷":::

    이렇게 하면 Azure Percept 진한 라이브 웹 스트림을 보여 주는 별도의 탭이 열립니다.

    :::image type="content" source="./media/how-to-view-video-stream/webstream.png" alt-text="장치 웹 스트림의 스크린샷.":::

## <a name="next-steps"></a>다음 단계

[Azure PERCEPT 진한 원격 분석](./how-to-view-telemetry.md)을 보는 방법에 대해 알아봅니다.