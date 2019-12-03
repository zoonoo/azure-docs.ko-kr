---
title: Java의 광역 위치 재결정
description: Java에서 디바이스 내 센서를 사용하여 앵커를 만들고 찾는 방법에 대해 자세히 설명합니다.
author: bucurb
manager: dacoghl
services: azure-spatial-anchors
ms.author: bobuc
ms.date: 09/19/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: 2aafaed67e68b5842767de32893eddd829b43164
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/21/2019
ms.locfileid: "74277217"
---
# <a name="how-to-create-and-locate-anchors-using-on-device-sensors-in-java"></a>Java에서 디바이스 내 센서를 사용하여 앵커를 만들고 찾는 방법

> [!div  class="op_single_selector"]
> * [Unity](set-up-coarse-reloc-unity.md)
> * [Objective-C](set-up-coarse-reloc-objc.md)
> * [Swift](set-up-coarse-reloc-swift.md)
> * [Android Java](set-up-coarse-reloc-java.md)
> * [C++/NDK](set-up-coarse-reloc-cpp-ndk.md)
> * [C++/WinRT](set-up-coarse-reloc-cpp-winrt.md)

Azure Spatial Anchors는 사용자가 만든 앵커와 디바이스 내 위치 센서 데이터를 연결할 수 있습니다. 이 데이터를 사용하여 디바이스 근처에 앵커가 있는지 여부를 신속하게 확인할 수도 있습니다. 자세한 내용은 [광역 위치 재결정](../concepts/coarse-reloc.md)을 참조하세요.

## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하려면 다음이 필요합니다.

- Java에 대한 기본 지식.
- [Azure Spatial Anchors 개요](../overview.md)를 자세히 읽었습니다.
- [5분 빠른 시작](../index.yml) 중 하나를 완료했습니다.
- [앵커 만들기 및 찾기 방법](../create-locate-anchors-overview.md)을 통해 읽습니다.

[!INCLUDE [Configure Provider](../../../includes/spatial-anchors-set-up-coarse-reloc-configure-provider.md)]

```java
// Create the sensor fingerprint provider
PlatformLocationProvider sensorProvider = new PlatformLocationProvider();

// Allow GPS
SensorCapabilities sensors = sensorProvider.getSensors();
sensors.setGeoLocationEnabled(true);

// Allow WiFi scanning
sensors.setWifiEnabled(true);

// Populate the set of known BLE beacons' UUIDs
String uuids[] = new String[2];
uuids[0] = "22e38f1a-c1b3-452b-b5ce-fdb0f39535c1";
uuids[1] = "a63819b9-8b7b-436d-88ec-ea5d8db2acb0";

// Allow the set of known BLE beacons
sensors.setBluetoothEnabled(true);
sensors.setKnownBeaconProximityUuids(uuids);
```

[!INCLUDE [Configure Provider](../../../includes/spatial-anchors-set-up-coarse-reloc-configure-session.md)]

```java
// Set the session's sensor fingerprint provider
cloudSpatialAnchorSession.setLocationProvider(sensorProvider);

// Configure the near-device criteria
NearDeviceCriteria nearDeviceCriteria = new NearDeviceCriteria();
nearDeviceCriteria.setDistanceInMeters(5.0f);
nearDeviceCriteria.setMaxResultCount(25);

// Set the session's locate criteria
AnchorLocateCriteria anchorLocateCriteria = new AnchorLocateCriteria();
anchorLocateCriteria.setNearDevice(nearDeviceCriteria);
cloudSpatialAnchorSession.createWatcher(anchorLocateCriteria);
```

[!INCLUDE [Locate](../../../includes/spatial-anchors-create-locate-anchors-locating-events.md)]

[!INCLUDE [Configure Provider](../../../includes/spatial-anchors-set-up-coarse-reloc-next-steps.md)]