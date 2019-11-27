---
title: IoT hub의 IP 주소 이해 | Microsoft Docs
description: IoT hub IP 주소 및 해당 속성을 쿼리 하는 방법을 이해 합니다. IoT hub의 IP 주소는 재해 복구 또는 지역 장애 조치 (failover)와 같은 특정 시나리오에서 변경 될 수 있습니다.
author: philmea
ms.author: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 11/21/2019
ms.openlocfilehash: 5c77fb30ef60c1ad82d0a87442bc8af186c54321
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74383909"
---
# <a name="iot-hub-ip-addresses"></a>IP 주소 IoT Hub

IoT hub의 IP 주소 접두사는 주기적으로 *AzureIoTHub* [service 태그](../virtual-network/service-tags-overview.md)아래에 게시 됩니다. 적절 한 작업을 수행 하려면 IoT 장치에 *AzureIoTHub* service 태그 아래에 나열 된 주소 접두사에 대 한 아웃 바운드 연결이 있어야 합니다. IoT 응용 프로그램 서비스는 *EventHub* 서비스 태그 아래에 나열 된 주소 접두사에 대 한 아웃 바운드 연결을 추가로 보유 해야 합니다.


## <a name="best-practices"></a>모범 사례

* IoT hub의 IP 주소 접두사는 변경 될 수 있습니다. 이러한 변경 내용은 영향을 받기 전에 서비스 태그를 통해 정기적으로 게시 됩니다. 따라서 최신 서비스 태그를 정기적으로 검색 하 고 사용 하는 프로세스를 개발 하는 것이 중요 합니다. 이 프로세스는 [서비스 태그 검색 API](../virtual-network/service-tags-overview.md#service-tags-in-on-premises)를 통해 자동화할 수 있습니다.
* AzureIoTHub를 사용 *합니다. [ region name]* 태그를 지정 하 여 특정 지역의 IoT hub 끝점에서 사용 하는 IP 접두사를 식별 합니다. 데이터 센터 재해 복구를 고려 하거나 [지역 장애 조치 (failover)](iot-hub-ha-dr.md) 를 위해 IoT Hub 지역 쌍 지역의 IP 접두사에 대 한 연결도 사용 하도록 설정 해야 합니다.


## <a name="support-for-ipv6"></a>I p v 6에 대 한 지원 

I p v 6은 현재 IoT Hub에서 지원 되지 않습니다.