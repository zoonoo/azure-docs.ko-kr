---
title: Azure IoT Central의 고객 데이터 상주 | Microsoft Docs
description: 이 문서에서는 Azure IoT Central 응용 프로그램의 고객 데이터 상주에 대해 설명 합니다.
author: dominicbetts
ms.author: dobett
ms.date: 11/02/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 9cf803ddd5a3c9329cadefffc35ae6d6999323a1
ms.sourcegitcommit: 58f12c358a1358aa363ec1792f97dae4ac96cc4b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93280710"
---
# <a name="azure-iot-central-customer-data-residency"></a>Azure IoT Central 고객 데이터 상주

IoT Central는 다음 시나리오를 제외 하 고 고객 지정 지리 외부에 고객 데이터를 저장 하지 않습니다.

- 새 사용자가 기존 IoT Central 응용 프로그램에 추가 되 면 초대 된 사용자가 응용 프로그램에 처음 액세스할 때까지 사용자의 전자 메일 ID가 지리 외부에 저장 될 수 있습니다.

- 대시보드 맵 타일 IoT Central [Azure Maps](../../azure-maps/about-azure-maps.md)를 사용 합니다. 기존 IoT Central 응용 프로그램에 지도 타일을 추가 하면 Azure Maps 서비스의 지리적 위치 규칙에 따라 위치 데이터가 처리 되거나 저장 될 수 있습니다.
