---
title: Azure Percept Vision 데이터시트
description: 자세한 장치 사양은 Azure Percept 비전 데이터 시트를 확인 하세요.
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: reference
ms.date: 02/16/2021
ms.openlocfilehash: 8814192274a81938d53ffc68c02dfaa9ac1da8ad
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101662885"
---
# <a name="azure-percept-vision-datasheet"></a>Azure Percept Vision 데이터시트

아래에 나열 된 사양은 azure [Percept](./azure-percept-dk-datasheet.md)에 포함 된 Azure Percept 비전 장치에 대 한 것입니다.

|제품 사양           |값     |
|--------------------------------|---------------------|
|대상 산업               |제조업 <br> 스마트 빌딩 <br> 자동 <br> 소매 |
|주인공 시나리오                  |구매자 분석 <br> 상용 가용성 <br> 축소 축소 <br> 보안/감시 <br> 작업 영역 안전성|
|차원                      |42mm x 42mm x 40mm (하우징를 사용 하는 Azure Percept 비전 SoM 어셈블리) <br> 42mm x 42mm x 6mm (비전 SoM 칩)|
|관리 제어 평면        |Azure 장치 업데이트 (ADU)          |
|지원 되는 소프트웨어 및 서비스 |[Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) <br> [Azure IoT Edge](https://azure.microsoft.com/services/iot-edge/) <br> [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) <br> [ONNX Runtime](https://www.onnxruntime.ai/) <br> [OpenVINO](https://docs.openvinotoolkit.org/latest/index.html) <br> Azure 장치 업데이트 |
|AI 가속                 |Intel 카메라 ISP를 통합 하 고 0.7을 사용 하는 intel Movidius 방대한 X (MA2085) 비전 처리 장치 (VPU) |
|센서 및 시각적 표시기   |전체 시야 5670 카메라 <br> GSO Lens (해상도: 5MP, 30FPS, Distance: 50cm-infinity, FoV: 120도, 색: 와이드 동적 범위, 고정 된 포커스 롤링 셔터)          |
|카메라 지원                  |RGB, IR 및 깊이 카메라 <br> 2 카메라를 동시에 실행할 수 있습니다. |
|보안 Crypto-Controller      |ST-Micro STM32L462CE      |
|버전 관리/ID 구성 요소       |64kb EEPROM |
|메모리                          |LPDDR4 2GB     |
|고급                           |3.5 W     |
|포트                           |1x USB 3.0 유형 C <br> 2x MIPI 4 레인 (레인 당 최대 1.5 Gbps)     |
|컨트롤 인터페이스              |2x I2C <br> 2x SPI <br> 6x PWM) (GPIOs: 2x 클록, 2x 프레임 동기화, 2 배 사용 안 함) <br> 2 배 예비 GPIO |
|인증                   |CE <br> FCC      |
|운영 온도           |Percept C (Azure 비전 SoM 어셈블리와 하우징) <br> -10 ~ 70도 C (비전 SoM 칩) |
|터치 온도               |<= 48도 C |
|상대 습도               |8% ~ 90%    |