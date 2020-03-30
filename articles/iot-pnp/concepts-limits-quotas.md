---
title: 제한 및 할당량 IoT 플러그 앤 플레이 미리보기 | 마이크로 소프트 문서
description: IoT 플러그 앤 플레이 미리 보기를 사용할 때 적용되는 제한, 할당량 및 제한을 이해합니다.
author: miagdp
ms.author: miag
ms.date: 12/26/2019
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 48ecaaba6d956efd9da75d0582fa06d231cb3f80
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75531380"
---
# <a name="iot-plug-and-play-preview-limits-quotas-and-throttles"></a>IoT 플러그 앤 플레이 미리 보기 제한, 할당량 및 스로틀

이 문서에서는 공개 미리 보기에 적용되는 IoT 플러그 앤 플레이 별 제한, 할당량 및 제한에 대해 설명합니다. 기존 [IoT Hub 할당량 및 제한도](../iot-hub/iot-hub-devguide-quotas-throttling.md) 적용됩니다.

## <a name="iot-hub"></a>IoT Hub

공개 미리 보기의 경우 IoT 허브에 다음과 같은 제한 및 할당량이 적용됩니다.

| 제한, 제한 및 제한 | 값 | 메모 |
|-----|-----|-----|
| 허브당 등록할 수 있는 장치 기능 모델(DPM) 또는 인터페이스 수 | 1500 ||
| 장치당 등록할 수 있는 최대 인터페이스 수 | 40 ||
| 장치당 등록할 수 있는 최대 DcM 수 | 1 ||
| 인터페이스/DCM 파일의 최대 크기 | 512 문자 ||
| 인터페이스 이름의 최대 크기 | 256 문자 ||
| 속성 이름의 최대 크기  | 64바이트, 깊이 7수준(첫 번째 수준은 `$iotin`예약됨) | 허용된 문자: a-z, A-Z, 0-9(첫 번째 문자가 아님) 및 밑줄. |
| 속성 값의 최대 크기 | 512바이트 ||
| 명령 이름의 최대 크기 | 100바이트 ||
| 디바이스 쌍 크기 | [IoT 허브 제한과](../iot-hub/iot-hub-devguide-device-twins.md#device-twin-size) 동일 ||
| SKU 에서 해결 API 호출(단위에 관계 없이) | 초당 100요청 ||

## <a name="model-repository"></a>모델 리포지토리

공개 미리 보기의 경우 모델 리포지토리에 다음과 같은 제한 및 할당량이 적용됩니다.

| 제한, 제한 및 제한| 값 |
|-----|-----|
| Azure Active Directory 테넌트당 회사 모델 리포지토리 수 | 1 |
| 모델 리포지토리당 권한 부여 키 수 | 10  |
| 회사 모델 리포지토리당 모델 수(DPM 또는 인터페이스)| 1500  |
| Azure Active Directory 테넌트당 공용 모델 리포지토리의 모델 수(DcM 또는 인터페이스)| 1500  |
| 회사 모델 리포지토리에서 삭제되는 DPM 또는 인터페이스 수 | 초당 10개의 쿼리(QPS)|
| 테넌트에서 생성/업데이트중인 모델 리포지토리 수| 1 QPS |
| 모델 리포지토리에서 생성/업데이트/삭제되는 권한 부여 키 수 | 1 QPS|
| 회사 모델 리포지토리에서 생성되는 DPM 수 | 10 QPS |
| 회사 모델 리포지토리에서 생성되는 인터페이스 수 | 10 QPS|
| 공용 모델 리포지토리에서 생성되는 DPM 수 | 10 QPS|
| 공용 모델 리포지토리에서 생성되는 인터페이스 수 | 10 QPS|

## <a name="parser-library"></a>파서 라이브러리

파서 라이브러리는 [디지털 트윈 정의 언어에](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL)적용되는 제한을 따릅니다.

## <a name="next-steps"></a>다음 단계

다음 단계는 [IoT 플러그 앤 플레이 장치에 연결하고 상호 작용하는](./howto-develop-solution.md)방법을 알아보는 것입니다.
