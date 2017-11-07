---
title: "Azure Time Series Insights 환경에 IoT Hub 이벤트 원본을 추가하는 방법 | Microsoft Docs"
description: "이 자습서에서는 IoT Hub에 연결된 이벤트 원본을 Time Series Insights 환경에 추가하는 방법을 다룹니다."
keywords: 
services: time-series-insights
documentationcenter: 
author: sandshadow
manager: almineev
editor: cgronlun
ms.assetid: 
ms.service: tsi
ms.devlang: na
ms.topic: how-to-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/19/2017
ms.author: edett
ms.openlocfilehash: 3b98728ced81256d05b1bed2c92fc66c5ca61b98
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-add-an-iot-hub-event-source"></a>IoT Hub 이벤트 원본을 추가하는 방법

이 자습서에서는 Azure Portal을 사용하여 IoT Hub에서 읽는 이벤트 원본을 Time Series Insights 환경에 추가하는 방법을 다룹니다.

## <a name="prerequisites"></a>필수 조건

IoT Hub를 만들고 이벤트를 기록합니다. IoT Hub에 대한 자세한 내용은 <https://azure.microsoft.com/services/iot-hub/>를 참조하세요.

> [소비자 그룹] 각 Time Series Insights 이벤트 원본에는 다른 소비자와 공유되지 않은 전용 소비자 그룹 자체가 있어야 합니다. 같은 소비자 그룹에서 여러 읽기 권한자가 이벤트를 소비하는 경우 모든 읽기 권한자에게 오류가 표시될 수 있습니다. 자세한 내용은 [IoT Hub 개발자 가이드](../iot-hub/iot-hub-devguide.md)를 참조하세요.

## <a name="choose-an-import-option"></a>가져오기 옵션 선택

이벤트 원본에 대한 설정을 직접 입력하거나 사용할 수 있는 IoT Hub에서 IoT Hub를 선택할 수 있습니다.
**가져오기 옵션** 선택기에서 다음 옵션 중 하나를 선택합니다.

* IoT Hub 설정 직접 입력
* 사용 가능한 구독에서 IoT Hub 사용

### <a name="select-an-available-iot-hub"></a>사용 가능한 IoT Hub를 선택

다음 테이블에는 이벤트 원본으로 사용할 수 있는 IoT Hub를 선택하는 경우 새 이벤트 원본 탭의 각 옵션이 해당 설명과 함께 설명되어 있습니다.

| 속성 이름 | 설명 |
| --- | --- |
| 이벤트 원본 이름 | 이벤트 원본의 이름입니다. 이 이름은 Time Series Insights 환경 내에서 고유해야 합니다.
| 원본 | **IoT Hub**를 선택하여 IoT Hub 이벤트 원본을 만듭니다.
| 구독 ID | 이 IoT Hub가 만들어진 구독을 선택합니다.
| IoT Hub 이름 | IoT Hub 이름을 선택합니다.
| IoT Hub 정책 이름 | IoT Hub 설정 탭에서 찾을 수 있는 공유 액세스 정책을 선택합니다. 각 공유 액세스 정책에는 이름, 사용자가 설정한 사용 권한 및 액세스 키가 있습니다. 이벤트 원본에 대한 공유 액세스 정책에는 **서비스 연결** 사용 권한이 *반드시* 있어야 합니다.
| IoT Hub 소비자 그룹 | IoT Hub에서 이벤트를 읽는 소비자 그룹입니다. 이벤트 원본에 대한 전용 소비자 그룹을 사용하는 것이 좋습니다.

### <a name="provide-iot-hub-settings-manually"></a>IoT Hub 설정 직접 입력

다음 테이블에는 설정을 직접 입력하는 경우 새 이벤트 원본 탭의 각 속성과 해당 설명이 나와 있습니다.

| 속성 이름 | 설명 |
| --- | --- |
| 이벤트 원본 이름 | 이벤트 원본의 이름입니다. 이 이름은 Time Series Insights 환경 내에서 고유해야 합니다.
| 원본 | **IoT Hub**를 선택하여 IoT Hub 이벤트 원본을 만듭니다.
| 구독 ID | 이 IoT Hub가 만들어진 구독입니다.
| 리소스 그룹 | 이 IoT Hub가 만들어진 구독입니다.
| IoT Hub 이름 | IoT Hub의 이름입니다. IoT Hub를 만들 때 사용자가 지은 특정 이름입니다.
| IoT Hub 정책 이름 | IoT Hub 설정 탭에서 찾을 수 있는 공유 액세스 정책입니다. 각 공유 액세스 정책에는 이름, 사용자가 설정한 사용 권한 및 액세스 키가 있습니다. 이벤트 원본에 대한 공유 액세스 정책에는 **서비스 연결** 사용 권한이 *반드시* 있어야 합니다.
| IoT Hub 정책 키 | 서비스 버스 네임스페이스에 대한 액세스를 인증하는 데 사용되는 공유 액세스 키 기본 또는 보조 키를 여기에 입력합니다.
| IoT Hub 소비자 그룹 | IoT Hub에서 이벤트를 읽는 소비자 그룹입니다. 이벤트 원본에 대한 전용 소비자 그룹을 사용하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

1. 데이터 액세스 정책을 사용자 환경의 [데이터 액세스 정책 정의](time-series-insights-data-access.md)에 추가
1. [Time Series Insights 포털](https://insights.timeseries.azure.com)에서 환경에 액세스