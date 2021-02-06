---
title: Azure Communication Services-알려진 문제
description: Azure Communication Services의 알려진 문제에 대 한 자세한 정보
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 10/03/2020
ms.topic: troubleshooting
ms.service: azure-communication-services
ms.openlocfilehash: e9e4b747d9d0ab39a1d0ecef6cf45e4cc0f9e2c5
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/06/2021
ms.locfileid: "99628146"
---
# <a name="known-issues-azure-communication-services"></a>알려진 문제: Azure 통신 서비스

이 문서에서는 Azure Communication Services와 관련 된 알려진 문제에 대 한 정보를 제공 합니다.

## <a name="video-streaming-quality-on-chromeandroid"></a>Chrome/Android의 비디오 스트리밍 품질 

Chrome Android에서 비디오 스트리밍 성능이 저하 될 수 있습니다.

### <a name="possible-causes"></a>가능한 원인
원격 스트림의 품질은 해당 스트림을 시작 하는 데 사용 된 클라이언트 쪽 렌더러의 해상도에 따라 달라 집니다. 원격 스트림을 구독할 때 수신자는 발신자의 클라이언트 쪽 렌더러 차원을 기반으로 자체 해결 방법을 결정 합니다.

## <a name="bluetooth-headset-microphones-are-not-detected"></a>Bluetooth 헤드셋 마이크가 검색 되지 않았습니다.

Bluetooth 헤드셋을 통신 서비스 호출에 연결 하는 데 문제가 발생할 수 있습니다.

### <a name="possible-causes"></a>가능한 원인
IOS에서 Bluetooth 마이크를 선택할 수 있는 옵션은 없습니다.


## <a name="repeatedly-switching-video-devices-may-cause-video-streaming-to-temporarily-stop"></a>비디오 장치를 반복적으로 전환 하면 비디오 스트리밍이 일시적으로 중지 될 수 있습니다.

비디오 장치 간을 전환 하면 선택한 장치에서 스트림을 획득 하는 동안 비디오 스트림이 일시 중지 될 수 있습니다.

### <a name="possible-causes"></a>가능한 원인
미디어 장치 간 스트리밍 및 전환은 계산 집약적입니다. 자주 전환 하면 성능이 저하 될 수 있습니다. 개발자는 다른 장치 스트림을 시작 하기 전에 중지 하는 것이 좋습니다.
