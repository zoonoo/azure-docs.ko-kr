---
title: 변환 개요
description: 변환 하는 방법에 대 한 자세한 정보
author: viv-liu
ms.author: viviali
ms.date: 10/11/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: corywink
ms.openlocfilehash: 629ae6f0e8b50eae5e791cafa6c67b638685ecb3
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72952512"
---
# <a name="transform-your-iot-data-preview-features"></a>IoT 데이터 변환 (미리 보기 기능)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

응용 프로그램 플랫폼인 IoT Central은 IoT 데이터를 실행 가능한 결과를 구동 하는 비즈니스 통찰력으로 변환 하는 데 도움이 되는 몇 가지 주요 패싯을 제공 합니다. IoT Central는 IoT 데이터를 외부 시스템으로 확장 하 여 lob (기간 업무) 응용 프로그램 및 사용자 지정 응용 프로그램과의 통합을 만드는 방법을 제공 합니다. 모바일 서비스를 통해 기술 전문가에 게 알리는 규칙을 만들어 장치 상태 및 작업을 모니터링할 수 있습니다. 원시 원격 분석 데이터를 Azure의 서비스로 내보내 사용자 지정 분석 및 기계 학습으로 특정 비즈니스 통찰력을 생성할 수 있습니다. 공용 Api를 사용 하 여 장치를 모니터링 하 고 제어 하 고 IoT Central 앱을 관리 하는 서비스 및 도구를 만들 수 있습니다. 

![IoT Central의 변환 개요](media/overview-iot-central-transform/transform.PNG)

## <a name="monitor-device-health-and-operations-using-rules"></a>규칙을 사용 하 여 장치 상태 및 작업 모니터링
컴퓨터를 연결 하 고 데이터를 보내면 문제가 발생 한 컴퓨터를 식별 하거나 오류 메시지를 전송 하 여 가동 중지 시간을 최소화 하면서 정상적으로 실행 되도록 수정할 수 있습니다. IoT Central 앱에서 규칙을 작성 하 여 장치에서 보내는 원격 분석을 모니터링 하 고 임계값에 도달 하거나 특정 이벤트 메시지를 보낸 경우 사용자에 게 경고할 수 있습니다. 규칙에 대 한 전자 메일 작업 및 웹 후크와 같은 작업을 구성 하 여 적절 한 사용자와 적절 한 다운스트림 시스템에 알릴 수 있습니다. 규칙에 대 한 자세한 내용은 여기를 참조 하세요.

## <a name="run-custom-analytics-and-processing-on-your-exported-data"></a>내보낸 데이터에 대 한 사용자 지정 분석 및 처리 실행
원시 IoT 원격 분석을 처리 하 고 최종 결과를 저장 하는 사용자 지정 분석 파이프라인을 빌드하여 공장 바닥에서 컴퓨터 효율성 추세를 확인 하거나 향후 에너지 사용량을 예측 하는 등의 매우 사용자 지정 비즈니스 통찰력을 생성할 수 있습니다. 사용자에 게 가장 중요 한 도구에서 데이터를 분석, 저장 및 시각화할 수 있도록 원격 분석, 장치 속성 및 수명 주기 및 장치 템플릿 변경 정보를 다른 Azure 서비스에 내보내기 위해 IoT Central 앱에서 데이터 내보내기를 만들 수 있습니다. 데이터 내보내기에 대 한 자세한 내용은 여기를 참조 하세요.

## <a name="build-custom-iot-solutions-and-integrations-with-apis"></a>Api를 사용 하 여 사용자 지정 IoT 솔루션 및 통합 빌드
장치를 원격으로 제어 하 고 새 장치를 설정 하거나 기존 lob (기간 업무) 응용 프로그램과의 사용자 지정 통합을 사용 하 여 IoT 장치 및 비즈니스에 맞게 조정 된 데이터와 상호 작용할 수 있는 모바일 동반 앱과 같은 IoT 솔루션을 빌드할 수 있습니다. 장치 모델링, 온 보 딩, 관리 및 데이터 액세스에 대 한 백본으로 IoT Central를 사용할 수 있습니다. 이 학습 모듈의 공용 API에 대해 자세히 알아보세요.
