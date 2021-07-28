---
title: Azure IoT Central의 고객 데이터 보존 | Microsoft Docs
description: 이 문서에서는 Azure IoT Central 애플리케이션의 고객 데이터 보존에 대해 설명합니다.
author: dominicbetts
ms.author: dobett
ms.date: 11/02/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 9cf803ddd5a3c9329cadefffc35ae6d6999323a1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93280710"
---
# <a name="azure-iot-central-customer-data-residency"></a>Azure IoT Central 고객 데이터 보존

IoT Central은 다음 시나리오를 제외하고 고객이 지정한 지리 외부에 고객 데이터를 저장하지 않습니다.

- 새 사용자가 기존 IoT Central 애플리케이션에 추가된 경우 초대받은 사용자가 애플리케이션에 처음 액세스할 때까지 사용자의 메일 ID가 지리 외부에 저장될 수 있습니다.

- IoT Central 대시보드 지도 타일은 [Azure Maps](../../azure-maps/about-azure-maps.md)를 사용합니다. 기존 IoT Central 애플리케이션에 지도 타일을 추가하는 경우 Azure Maps 서비스의 지리적 위치 규칙에 따라 위치 데이터가 처리되거나 저장될 수 있습니다.
