---
title: Azure Percept DK 개요
description: Azure Percept에 대 한 자세한 정보
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: conceptual
ms.date: 02/18/2021
ms.custom: template-concept
ms.openlocfilehash: 4fd0a7cb575a109d1393527b48de3fa4e3446167
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101663467"
---
# <a name="azure-percept-dk-overview"></a>Azure Percept DK 개요

Azure Percept 진한은 비전 AI 개념 증명을 개발 하기 위해 설계 된 edge AI 개발 키트입니다. [Azure Percept Studio](./overview-azure-percept-studio.md)와 함께 사용 하는 경우 다양 한 시각 ai 응용 프로그램에 대 한 edge ai 솔루션을 빌드하기 위한 강력 하 고 사용 하기 간편한 플랫폼이 됩니다. [Microsoft online 스토어](https://go.microsoft.com/fwlink/p/?LinkId=2155270)에서 구매할 수 있습니다.

:::image type="content" source="./media/overview-azure-percept-dk/dk-image.png" alt-text="이미지로.":::

## <a name="key-features"></a>주요 기능

- 에 **지에서 AI를 실행 하는 기능** 입니다. 기본 제공 하드웨어 가속을 사용 하면 클라우드에 연결 하지 않고도 비전 AI 모델을 실행할 수 있습니다.
- **기본 제공 되는 신뢰 보안의 하드웨어 루트** 입니다. 자세한 내용은 [Azure Percept 보안](./overview-percept-security.md) 개요를 참조 하세요.
- **[Azure Percept Studio](./overview-azure-percept-studio.md) 및 기타 azure 서비스와의 원활한 통합** 예: Azure IoT Hub, Azure Cognitive Services 및 [라이브 비디오 분석](https://docs.microsoft.com/azure/media-services/live-video-analytics-edge/overview)
- **상위 AI 플랫폼을 지원** 합니다. 예: ONNX 및 TensorFlow.
- **80/20 railing 시스템과의 통합** 입니다. 프로덕션 환경에서 프로토타입 빌드를 용이 하 게 합니다. [8/20 통합](./overview-8020-integration.md)에 대해 자세히 알아보세요.

## <a name="hardware-components"></a>하드웨어 구성 요소

- Azure Percept 진한 캐리어 보드
    - NXP iMX8m 프로세서
    - 신뢰할 수 있는 플랫폼 모듈 (TPM) 버전 2.0
    - WiFi 및 Bluetooth 연결
    - 전체 [데이터 시트](./azure-percept-dk-datasheet.md) 보기
- 모듈 (SoM)의 Azure Percept 비전 시스템
    - Intel Movidius 방대한 X (MA2085) 비전 처리 단위 (VPU)
    - 두 번째를 추가할 수 있는 기능을 제공 하는 RGB 카메라 센서
    - 전체 [데이터 시트](./azure-percept-vision-datasheet.md) 보기

## <a name="get-started-with-the-azure-percept-dk"></a>Azure Percept 진한 Azure 시작

- 이러한 빠른 시작 완료
    - [Azure Percept 진한 Unbox 및 조립](./quickstart-percept-dk-unboxing.md)
    - [Azure Percept 진한 사용자를 설정 하 고 첫 번째 비전 AI 모델 실행](./quickstart-percept-dk-set-up.md)
- 이러한 자습서를 사용 하 여 개념 증명 작성 시작
    - [Azure Percept Studio에서 코드 없는 비전 솔루션 만들기](./tutorial-nocode-vision.md)
    - [Azure Percept Studio에서 음성 도우미 만들기](./tutorial-no-code-speech.md)

## <a name="next-steps"></a>다음 단계

[Microsoft online store](https://go.microsoft.com/fwlink/p/?LinkId=2155270)에서 AZURE Percept 진한 주문을 합니다.