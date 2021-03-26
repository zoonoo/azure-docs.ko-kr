---
title: Azure Percept DK 개요
description: Azure Percept에 대 한 자세한 정보
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: conceptual
ms.date: 03/23/2021
ms.custom: template-concept
ms.openlocfilehash: b20448a88cee6c4ea4af0bcbfaa6ec465f2e28bb
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105560967"
---
# <a name="azure-percept-dk-overview"></a>Azure Percept DK 개요

Azure Percept 진한은 [Azure Percept Studio](./overview-azure-percept-studio.md)를 사용 하 여 비전 및 오디오 AI 솔루션을 개발 하기 위해 설계 된 edge AI 개발 키트입니다. Azure Percept 진한은 [Microsoft 온라인 스토어](https://go.microsoft.com/fwlink/p/?LinkId=2155270)에서 구매할 수 있습니다.

> [!div class="nextstepaction"]
> [지금 구입](https://go.microsoft.com/fwlink/p/?LinkId=2155270)

</br>

> [!VIDEO https://www.youtube.com/embed/Qj8NGn-7s5A]

## <a name="key-features"></a>주요 특징

- 에 지에서 AI를 실행 합니다. 기본 제공 하드웨어 가속을 사용 하면 개발자 키트는 클라우드에 연결 하지 않고도 AI 모델을 실행할 수 있습니다.

- 기본 제공 되는 신뢰 보안의 하드웨어 루트입니다. [Azure Percept 보안](./overview-percept-security.md)에 대해 자세히 알아보세요.

- [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819) 및 기타 azure 서비스 (예: Azure IoT Hub, azure Cognitive Services 및 [라이브 비디오 분석과](https://docs.microsoft.com/azure/media-services/live-video-analytics-edge/overview)의 원활한 통합.

- AI 오디오 솔루션을 빌드하기 위한 선택적 액세서리 인 [Azure Percept Audio](./overview-azure-percept-audio.md)와 호환 됩니다.

- ONNX 및 TensorFlow와 같은 타사 AI 도구를 지원 합니다.

- 무한 장치 탑재 구성을 허용 하는 80/20 railing 시스템과의 통합입니다. [80/20 통합](./overview-8020-integration.md)에 대해 자세히 알아보세요.

## <a name="hardware-components"></a>하드웨어 구성 요소

- Azure Percept 진한 캐리어 보드:
    - NXP iMX8m 프로세서
    - 신뢰할 수 있는 플랫폼 모듈 (TPM) 버전 2.0
    - Wi-Fi 및 Bluetooth 연결
    - 자세한 내용은 [Azure PERCEPT 진한 데이터 시트](./azure-percept-dk-datasheet.md) 를 참조 하세요.

- Azure Percept 비전 시스템-모듈 (SoM):
    - Intel Movidius 방대한 X (MA2085) 비전 처리 단위 (VPU)
    - RGB 카메라 센서
    - 자세한 내용은 [Azure Percept 비전 데이터 시트](./azure-percept-vision-datasheet.md) 를 참조 하세요.

## <a name="getting-started-with-azure-percept-dk"></a>Azure Percept 진한 Azure 시작

- 개발자 키트를 설정 합니다.
    - [Azure Percept 진한 Unbox 및 조립](./quickstart-percept-dk-unboxing.md)
    - [Azure Percept 진한 설치 환경 완료](./quickstart-percept-dk-set-up.md)

- 비전 및 오디오 솔루션 빌드를 시작 합니다.
    - [Azure Percept Studio에서 코드 없는 비전 솔루션 만들기](./tutorial-nocode-vision.md)
    - [Azure Percept Studio에서 코드 없는 음성 솔루션 만들기](./tutorial-no-code-speech.md) (Azure Percept Audio 액세서리 필요)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Microsoft online store에서 Azure Percept 진한 구매](https://go.microsoft.com/fwlink/p/?LinkId=2155270)