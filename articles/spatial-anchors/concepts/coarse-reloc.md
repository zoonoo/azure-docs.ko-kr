---
title: 거친 지역화
description: 거친 재지역화를 사용하여 가까운 앵커를 찾는 방법에 대해 알아봅니다.
author: bucurb
manager: dacoghl
services: azure-spatial-anchors
ms.author: bobuc
ms.date: 09/18/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: 4c1604eaad1ebdedf6a360a647fe5b9f95c829c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76844397"
---
# <a name="coarse-relocalization"></a>광역 위치 재결정

거친 재현지화는 질문에 대한 초기 답변을 제공하는 기능입니다: *현재 내 장치는 어디에 있습니까 / 어떤 콘텐츠를 관찰해야 합니까?* 응답은 정확하지 않지만 대신 양식에 있습니다: *이러한 앵커에 가깝습니다.*

거친 재지역화는 다양한 장치 내 센서 판독값을 앵커 생성 및 쿼리와 연결하여 작동합니다. 실외 시나리오의 경우 센서 데이터는 일반적으로 장치의 GPS(글로벌 포지셔닝 시스템) 위치입니다. GPS를 사용할 수 없거나 신뢰할 수 없는 경우(예: 실내) 센서 데이터는 WiFi 액세스 포인트와 범위 내 블루투스 비콘으로 구성됩니다. 수집된 모든 센서 데이터는 Azure Spatial 앵커가 장치에서 약 100미터 내에 있는 앵커를 빠르게 결정하는 데 사용되는 공간 인덱스를 유지하는 데 기여합니다.

거친 재현지화를 통해 활성화된 앵커의 빠른 조회는 세계적인 규모의 앵커(예: 수백만 개의 지리적 분산) 앵커를 통해 지원되는 응용 프로그램의 개발을 단순화합니다. 앵커 관리의 복잡성은 모두 숨겨져 있으므로 멋진 응용 프로그램 논리에 더 집중할 수 있습니다. 모든 앵커 무거운 리프팅Azure 공간 앵커에 의해 무대 뒤에서 당신을 위해 수행됩니다.

## <a name="collected-sensor-data"></a>수집된 센서 데이터

앵커 서비스에 보낼 수 있는 센서 데이터는 다음 중 하나입니다.

* GPS 위치 : 위도, 경도, 고도.
* 범위 내 WiFi 액세스 포인트의 신호 강도.
* 범위 내 블루투스 비콘의 신호 강도.

일반적으로 응용 프로그램은 GPS, WiFi 또는 BLE 데이터에 액세스하기 위해 장치별 권한을 획득해야 합니다. 또한 위의 센서 데이터 중 일부는 특정 플랫폼에서 의도적으로 사용할 수 없습니다. 이러한 상황을 고려하기 위해 센서 데이터 수집은 선택 사항이며 기본적으로 해제됩니다.

## <a name="set-up-the-sensor-data-collection"></a>센서 데이터 수집 설정

먼저 센서 지문 공급자를 만들고 세션을 인식하도록 하겠습니다.

# <a name="c"></a>[C #](#tab/csharp)

```csharp
// Create the sensor fingerprint provider
sensorProvider = new PlatformLocationProvider();

// Create and configure the session
cloudSpatialAnchorSession = new CloudSpatialAnchorSession();

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession.LocationProvider = sensorProvider;
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
// Create the sensor fingerprint provider
ASAPlatformLocationProvider *sensorProvider;
sensorProvider = [[ASAPlatformLocationProvider alloc] init];

// Create and configure the session
cloudSpatialAnchorSession = [[ASACloudSpatialAnchorSession alloc] init];

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession.locationProvider = sensorProvider;
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
// Create the sensor fingerprint provider
var sensorProvider: ASAPlatformLocationProvider?
sensorProvider = ASAPlatformLocationProvider()

// Create and configure the session
cloudSpatialAnchorSession = ASACloudSpatialAnchorSession()

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession!.locationProvider = sensorProvider
```

# <a name="java"></a>[Java](#tab/java)

```java
// Create the sensor fingerprint provider
PlatformLocationProvider sensorProvider = new PlatformLocationProvider();

// Create and configure the session
cloudSpatialAnchorSession = new CloudSpatialAnchorSession();

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession.setLocationProvider(sensorProvider);
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
// Create the sensor fingerprint provider
std::shared_ptr<PlatformLocationProvider> sensorProvider;
sensorProvider = std::make_shared<PlatformLocationProvider>();

// Create and configure the session
cloudSpatialAnchorSession = std::make_shared<CloudSpatialAnchorSession>();

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession->LocationProvider(sensorProvider);
```

# <a name="c-winrt"></a>[C++ 윈RT](#tab/cppwinrt)
```cpp
// Create the sensor fingerprint provider
PlatformLocationProvider sensorProvider = PlatformLocationProvider();

// Create and configure the session
cloudSpatialAnchorSession = CloudSpatialAnchorSession();

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession.LocationProvider(sensorProvider);
```
---

다음으로 거친 재현지화에 사용할 센서를 결정해야 합니다. 이 결정은 개발 중인 응용 프로그램에 따라 다르지만 다음 표의 권장 사항은 좋은 시작점을 제공해야 합니다.


|             | 실내 | 야외 |
|-------------|---------|----------|
| Gps         | 꺼짐 | 설정 |
| WiFi        | 설정 | 켜기(선택 사항) |
| BLE 비콘 | 켜기(주의 사항, 아래 참조) | 꺼짐 |


### <a name="enabling-gps"></a>GPS 사용

응용 프로그램에 이미 장치의 GPS 위치에 액세스할 수 있는 권한이 있다고 가정하면 Azure 공간 앵커를 사용하도록 구성할 수 있습니다.

# <a name="c"></a>[C #](#tab/csharp)

```csharp
sensorProvider.Sensors.GeoLocationEnabled = true;
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
ASASensorCapabilities *sensors = locationProvider.sensors;
sensors.geoLocationEnabled = true;
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
let sensors = locationProvider?.sensors
sensors.geoLocationEnabled = true
```

# <a name="java"></a>[Java](#tab/java)

```java
SensorCapabilities sensors = sensorProvider.getSensors();
sensors.setGeoLocationEnabled(true);
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
const std::shared_ptr<SensorCapabilities>& sensors = sensorProvider->Sensors();
sensors->GeoLocationEnabled(true);
```

# <a name="c-winrt"></a>[C++ 윈RT](#tab/cppwinrt)

```cpp
SensorCapabilities sensors = sensorProvider.Sensors()
sensors.GeoLocationEnabled(true);
```

---

응용 프로그램에서 GPS를 사용하는 경우 하드웨어에서 제공하는 판독값은 일반적으로 다음과 같습니다.

* 비동기 및 저주파(1Hz 미만).
* 신뢰할 수 없음 / 시끄러운 (평균 7m 표준 편차).

일반적으로 장치 OS와 Azure 공간 앵커는 이러한 문제를 완화하기 위해 원시 GPS 신호에 대한 일부 필터링 및 외삽을 수행합니다. 이 추가 처리에는 수렴을 위한 추가 시간이 필요하므로 최상의 결과를 얻으려면 다음을 수행해야 합니다.

* 응용 프로그램에서 가능한 한 빨리 하나의 센서 지문 공급자 만들기
* 여러 세션 사이에 센서 지문 공급자를 살아 있게 유지
* 여러 세션 간에 센서 지문 공급자 공유

앵커 세션 외부에서 센서 지문 공급자를 사용하려는 경우 센서 추정을 요청하기 전에 센서를 시작해야 합니다. 예를 들어 다음 코드는 지도에서 기기의 위치를 실시간으로 업데이트하는 데 적합합니다.

# <a name="c"></a>[C #](#tab/csharp)

```csharp
// Game about to start, start tracking the sensors
sensorProvider.Start();

// Game loop
while (m_isRunning)
{
    // Get the GPS estimate
    GeoLocation geoPose = sensorProvider.GetLocationEstimate();

    // Paint it on the map
    drawCircle(
        x: geoPose.Longitude,
        y: geoPose.Latitude,
        radius: geoPose.HorizontalError);
}

// Game ended, no need to track the sensors anymore
sensorProvider.Stop();
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
// Game about to start, start tracking the sensors
[sensorProvider start];

// Game loop
while (m_isRunning)
{
    // Get the GPS estimate
    ASAGeoLocation *geoPose = [sensorProvider getLocationEstimate];

    // Paint it on the map
    drawCircle(geoPose.longitude, geoPose.latitude, geoPose.horizontalError);
}

// Game ended, no need to track the sensors anymore
[sensorProvider stop];
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
// Game about to start, start tracking the sensors
sensorProvider?.start()

// Game loop
while m_isRunning
{
    // Get the GPS estimate
    var geoPose: ASAGeoLocation?
    geoPose = sensorProvider?.getLocationEstimate()

    // Paint it on the map
    drawCircle(geoPose.longitude, geoPose.latitude, geoPose.horizontalError)
}

// Game ended, no need to track the sensors anymore
sensorProvider?.stop()
```

# <a name="java"></a>[Java](#tab/java)

```java
// Game about to start, start tracking the sensors
sensorProvider.start();

// Game loop
while (m_isRunning)
{
    // Get the GPS estimate
    GeoLocation geoPose = sensorProvider.getLocationEstimate();

    // Paint it on the map
    drawCircle(geoPose.getLongitude(), geoPose.getLatitude(), geoPose.getHorizontalError());
}

// Game ended, no need to track the sensors anymore
sensorProvider.stop();
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
// Game about to start, start tracking the sensors
sensorProvider->Start();

// Game loop
while (m_isRunning)
{
    // Get the GPS estimate
    std::shared_ptr<GeoLocation> geoPose = sensorProvider->GetLocationEstimate();

    // Paint it on the map
    drawCircle(geoPose->Longitude(), geoPose->Latitude(), geoPose->HorizontalError());
}

// Game ended, no need to track the sensors anymore
sensorProvider->Stop();
```

# <a name="c-winrt"></a>[C++ 윈RT](#tab/cppwinrt)

```cpp
// Game about to start, start tracking the sensors
sensorProvider.Start();

// Game loop
while (m_isRunning)
{
    // Get the GPS estimate
    GeoLocation geoPose = sensorProvider.GetLocationEstimate();

    // Paint it on the map
    drawCircle(geoPose.Longitude(), geoPose.Latitude(), geoPose.HorizontalError());
}

// Game ended, no need to track the sensors anymore
sensorProvider.Stop();
```

---

### <a name="enabling-wifi"></a>Wi-Fi 사용

응용 프로그램에 이미 장치의 WiFi 상태에 액세스할 수 있는 권한이 있다고 가정하면 Azure 공간 앵커를 사용하도록 구성할 수 있습니다.

# <a name="c"></a>[C #](#tab/csharp)

```csharp
sensorProvider.Sensors.WifiEnabled = true;
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
ASASensorCapabilities *sensors = locationProvider.sensors;
sensors.wifiEnabled = true;
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
let sensors = locationProvider?.sensors
sensors.wifiEnabled = true
```

# <a name="java"></a>[Java](#tab/java)

```java
SensorCapabilities sensors = sensorProvider.getSensors();
sensors.setWifiEnabled(true);
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
const std::shared_ptr<SensorCapabilities>& sensors = sensorProvider->Sensors();
sensors->WifiEnabled(true);
```

# <a name="c-winrt"></a>[C++ 윈RT](#tab/cppwinrt)

```cpp
SensorCapabilities sensors = sensorProvider.Sensors()
sensors.WifiEnabled(true);
```

---

응용 프로그램에서 WiFi를 사용하는 경우 하드웨어에서 제공하는 판독값은 일반적으로 다음과 같습니다.

* 비동기 및 저주파(0.1Hz 미만).
* OS 수준에서 제한될 수 있습니다.
* 신뢰할 수 없음 / 시끄러운 (평균 3-dBm 표준 편차).

Azure 공간 앵커는 이러한 문제를 완화하기 위해 세션 중에 필터링된 WiFi 신호 강도 맵을 작성하려고 시도합니다. 최상의 결과를 얻으려면 다음을 수행해야 합니다.

* 첫 번째 앵커를 배치하기 전에 세션을 만듭니다.
* (즉, 한 세션에서 모든 앵커 및 쿼리를 생성)를 가능한 한 오랫동안 살아 있게 유지합니다.

### <a name="enabling-bluetooth-beacons"></a>블루투스 비콘 활성화

응용 프로그램에 이미 장치의 Bluetooth 상태에 액세스할 수 있는 권한이 있다고 가정하면 Azure 공간 앵커를 사용하도록 구성할 수 있습니다.

# <a name="c"></a>[C #](#tab/csharp)

```csharp
sensorProvider.Sensors.BluetoothEnabled = true;
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
ASASensorCapabilities *sensors = locationProvider.sensors;
sensors.bluetoothEnabled = true;
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
let sensors = locationProvider?.sensors
sensors.bluetoothEnabled = true
```

# <a name="java"></a>[Java](#tab/java)

```java
SensorCapabilities sensors = sensorProvider.getSensors();
sensors.setBluetoothEnabled(true);
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
const std::shared_ptr<SensorCapabilities>& sensors = sensorProvider->Sensors();
sensors->BluetoothEnabled(true);
```

# <a name="c-winrt"></a>[C++ 윈RT](#tab/cppwinrt)

```cpp
SensorCapabilities sensors = sensorProvider.Sensors();
sensors.BluetoothEnabled(true);
```

---

비콘은 일반적으로 UUD 및 MAC 주소를 포함한 모든 것을 구성할 수 있는 다목적 장치입니다. 이러한 유연성은 비콘이 UUD로 고유하게 식별되는 것으로 간주하기 때문에 Azure 공간 앵커에 문제가 될 수 있습니다. 이러한 고유성을 보장하지 못하면 공간 웜홀이 발생할 가능성이 큽입니다. 최상의 결과를 얻으려면 다음을 수행해야 합니다.

* 비콘에 고유한 UUD를 할당합니다.
* 일반적으로 그리드와 같은 일반 패턴으로 배포합니다.
* 센서 지문 공급자에 고유 비콘 UUD의 목록을 전달합니다.

# <a name="c"></a>[C #](#tab/csharp)

```csharp
sensorProvider.Sensors.KnownBeaconProximityUuids = new[]
{
    "22e38f1a-c1b3-452b-b5ce-fdb0f39535c1",
    "a63819b9-8b7b-436d-88ec-ea5d8db2acb0",
    . . .
};
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
NSArray *uuids = @[@"22e38f1a-c1b3-452b-b5ce-fdb0f39535c1", @"a63819b9-8b7b-436d-88ec-ea5d8db2acb0"];

ASASensorCapabilities *sensors = locationProvider.sensors;
sensors.knownBeaconProximityUuids = uuids;
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
let uuids = [String]()
uuids.append("22e38f1a-c1b3-452b-b5ce-fdb0f39535c1")
uuids.append("a63819b9-8b7b-436d-88ec-ea5d8db2acb0")

let sensors = locationProvider?.sensors
sensors.knownBeaconProximityUuids = uuids
```

# <a name="java"></a>[Java](#tab/java)

```java
String uuids[] = new String[2];
uuids[0] = "22e38f1a-c1b3-452b-b5ce-fdb0f39535c1";
uuids[1] = "a63819b9-8b7b-436d-88ec-ea5d8db2acb0";

SensorCapabilities sensors = sensorProvider.getSensors();
sensors.setKnownBeaconProximityUuids(uuids);
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
std::vector<std::string> uuids;
uuids.push_back("22e38f1a-c1b3-452b-b5ce-fdb0f39535c1");
uuids.push_back("a63819b9-8b7b-436d-88ec-ea5d8db2acb0");

const std::shared_ptr<SensorCapabilities>& sensors = sensorProvider->Sensors();
sensors->KnownBeaconProximityUuids(uuids);
```

# <a name="c-winrt"></a>[C++ 윈RT](#tab/cppwinrt)

```cpp
std::vector<winrt::hstring> uuids;
uuids.emplace_back("22e38f1a-c1b3-452b-b5ce-fdb0f39535c1");
uuids.emplace_back("a63819b9-8b7b-436d-88ec-ea5d8db2acb0");

SensorCapabilities sensors = sensorProvider.Sensors();
sensors.KnownBeaconProximityUuids(uuids);
```

---

Azure 공간 앵커는 알려진 비콘 근접 UUD 목록에 있는 블루투스 비콘만 추적합니다. 허용 목록에 있는 UUD를 갖도록 프로그래밍된 악성 비콘은 여전히 서비스 품질에 부정적인 영향을 미칠 수 있습니다. 따라서 배포를 제어할 수 있는 선별된 공간에서만 비콘을 사용해야 합니다.

## <a name="querying-with-sensor-data"></a>센서 데이터로 쿼리

연결된 센서 데이터로 앵커를 만든 후에는 장치에서 보고한 센서 판독값을 사용하여 앵커를 검색할 수 있습니다. 더 이상 서비스에 찾을 것으로 예상되는 알려진 앵커 목록을 제공할 필요가 없으며, 대신 온보드 센서에서 보고한 대로 장치의 위치를 알려주기만 하면 됩니다. 그런 다음 Azure 공간 앵커는 장치에 가까운 앵커 집합을 파악하고 시각적으로 일치시키려고 시도합니다.

쿼리가 센서 데이터를 사용하도록 하려면 먼저 "장치 근처" 기준을 작성합니다.

# <a name="c"></a>[C #](#tab/csharp)

```csharp
NearDeviceCriteria nearDeviceCriteria = new NearDeviceCriteria();

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria.DistanceInMeters = 5;

// Cap the number of anchors returned
nearDeviceCriteria.MaxResultCount = 25;

anchorLocateCriteria = new AnchorLocateCriteria();
anchorLocateCriteria.NearDevice = nearDeviceCriteria;
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
ASANearDeviceCriteria *nearDeviceCriteria = [[ASANearDeviceCriteria alloc] init];

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria.distanceInMeters = 5.0f;

// Cap the number of anchors returned
nearDeviceCriteria.maxResultCount = 25;

ASAAnchorLocateCriteria *anchorLocateCriteria = [[ASAAnchorLocateCriteria alloc] init];
anchorLocateCriteria.nearDevice = nearDeviceCriteria;
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
let nearDeviceCriteria = ASANearDeviceCriteria()!

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria.distanceInMeters = 5.0

// Cap the number of anchors returned
nearDeviceCriteria.maxResultCount = 25

let anchorLocateCriteria = ASAAnchorLocateCriteria()!
anchorLocateCriteria.nearDevice = nearDeviceCriteria
```

# <a name="java"></a>[Java](#tab/java)

```java
NearDeviceCriteria nearDeviceCriteria = new NearDeviceCriteria();

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria.setDistanceInMeters(5.0f);

// Cap the number of anchors returned
nearDeviceCriteria.setMaxResultCount(25);

AnchorLocateCriteria anchorLocateCriteria = new AnchorLocateCriteria();
anchorLocateCriteria.setNearDevice(nearDeviceCriteria);
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
auto nearDeviceCriteria = std::make_shared<NearDeviceCriteria>();

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria->DistanceInMeters(5.0f);

// Cap the number of anchors returned
nearDeviceCriteria->MaxResultCount(25);

auto anchorLocateCriteria = std::make_shared<AnchorLocateCriteria>();
anchorLocateCriteria->NearDevice(nearDeviceCriteria);
```

# <a name="c-winrt"></a>[C++ 윈RT](#tab/cppwinrt)

```cpp
NearDeviceCriteria nearDeviceCriteria = NearDeviceCriteria();

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria.DistanceInMeters(5.0f);

// Cap the number of anchors returned
nearDeviceCriteria.MaxResultCount(25);

// Set the session's locate criteria
anchorLocateCriteria = AnchorLocateCriteria();
anchorLocateCriteria.NearDevice(nearDeviceCriteria);
```

---

매개 `DistanceInMeters` 변수는 콘텐츠를 검색하기 위해 앵커 그래프를 탐색할 정도를 제어합니다. 예를 들어 매 미터마다 2의 일정한 밀도로 앵커가 있는 일부 공간을 채웠다고 가정합니다. 또한 장치의 카메라가 단일 앵커를 관찰하고 서비스가 성공적으로 배치되었습니다. 현재 관찰중인 단일 앵커가 아닌 근처에 배치한 모든 앵커를 검색하는 데 관심이 있을 것입니다. 배치한 앵커가 그래프에 연결되어 있다고 가정하면 서비스는 그래프의 가장자리를 따라 근처의 모든 앵커를 검색할 수 있습니다. 수행된 그래프 통과의 양은 `DistanceInMeters`; `DistanceInMeters`보다 가까운 곳에 있는 앵커에 연결된 앵커가 모두 제공됩니다.

큰 값은 성능에 `MaxResultCount` 부정적인 영향을 줄 수 있습니다. 응용 프로그램에 대한 합리적인 값으로 설정합니다.

마지막으로, 센서 기반 조회를 사용 하 여 세션을 알려 야 합니다.

# <a name="c"></a>[C #](#tab/csharp)

```csharp
cloudSpatialAnchorSession.CreateWatcher(anchorLocateCriteria);
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
[cloudSpatialAnchorSession createWatcher:anchorLocateCriteria];
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
cloudSpatialAnchorSession!.createWatcher(anchorLocateCriteria)
```

# <a name="java"></a>[Java](#tab/java)

```java
cloudSpatialAnchorSession.createWatcher(anchorLocateCriteria);
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
cloudSpatialAnchorSession->CreateWatcher(anchorLocateCriteria);
```

# <a name="c-winrt"></a>[C++ 윈RT](#tab/cppwinrt)

```cpp
cloudSpatialAnchorSession.CreateWatcher(anchorLocateCriteria);
```

---

## <a name="expected-results"></a>예상 결과

소비자 등급 GPS 장치는 일반적으로 매우 정확하지 않습니다. [Zandenbergen과 Barbeau (2011)에][6] 의한 연구는 보조 GPS (A-GPS)가있는 휴대 전화의 평균 정확도가 약 7 미터라고보고합니다 - 무시할 수있는 큰 가치! 이러한 측정 오류를 설명하기 위해 서비스는 앵커를 GPS 공간의 확률 분포로 처리합니다. 따라서 앵커는 이제 가장 가능성이 높은 공간 영역(즉, 95% 이상의 신뢰도)에 실제 알 수 없는 GPS 위치가 포함되어 있습니다.

GPS로 쿼리할 때도 동일한 추론이 적용됩니다. 이 장치는 실제 알 수없는 GPS 위치 주변의 또 다른 공간 신뢰 영역으로 표시됩니다. 근처 앵커를 발견하면 아래 이미지와 같이 장치의 신뢰 영역에 *충분히 가까운* 신뢰 영역이 있는 앵커를 찾는 것으로 변환됩니다.

![GPS를 가진 앵커 후보자의 선택](media/coarse-reloc-gps-separation-distance.png)

GPS 신호의 정확도는 앵커 생성과 쿼리 중에 모두 반환된 앵커 세트에 큰 영향을 미칩니다. 반대로 WiFi/비콘을 기반으로 하는 쿼리는 쿼리와 공통으로 하나 이상의 액세스 포인트/비콘이 있는 모든 앵커를 고려합니다. 이러한 의미에서, WiFi/비콘에 기초한 질의의 결과는 대부분 액세스 포인트/비콘의 물리적 범위, 및 환경 장애물에 의해 결정된다.

아래 표는 각 센서 유형에 대한 예상 검색 공간을 추정합니다.

| 센서      | 검색 공간 반경(약) | 세부 정보 |
|-------------|:-------:|---------|
| Gps         | 20 m - 30 m | 다른 요인 들 중 GPS 불확실성에 의해 결정 됩니다. 보고된 숫자는 7미터인 A-GPS를 가진 휴대 전화의 평균 GPS 정확도에 대해 추정됩니다. |
| WiFi        | 50 m - 100 m | 무선 액세스 포인트의 범위에 의해 결정됩니다. 주파수, 송신기 강도, 물리적 장애물, 간섭 등에 따라 다릅니다. |
| BLE 비콘 |  70 m | 비콘의 범위에 의해 결정됩니다. 주파수, 전송 강도, 물리적 장애물, 간섭 등에 따라 달라집니다. |

## <a name="per-platform-support"></a>플랫폼별 지원

다음 표에는 지원되는 각 플랫폼에서 수집된 센서 데이터와 플랫폼별 주의 사항을 요약한 것입니다.


|             | HoloLens | Android | iOS |
|-------------|----------|---------|-----|
| Gps         | 해당 없음 | 위치 [관리자][3] API(GPS 및 네트워크 모두)를 통해 지원 | [CLLocationManager][4] API를 통해 지원 |
| WiFi        | 3초마다 약 1회 스캔 속도로 지원 | 지원됩니다. API 수준 28부터 WiFi 스캔은 2분마다 4회 통화로 제한됩니다. Android 10에서 개발자 설정 메뉴에서 제한을 사용하지 않도록 선택할 수 있습니다. 자세한 내용은 Android [설명서를][5]참조하십시오. | 해당 없음 - 공용 API 없음 |
| BLE 비콘 | [에디스톤][1] 및 [아이비콘한정][2] | [에디스톤][1] 및 [아이비콘한정][2] | [에디스톤][1] 및 [아이비콘한정][2] |

## <a name="next-steps"></a>다음 단계

앱에서 거친 재지역화를 사용합니다.

> [!div class="nextstepaction"]
> [화합](../how-tos/set-up-coarse-reloc-unity.md)

> [!div class="nextstepaction"]
> [Objective-C](../how-tos/set-up-coarse-reloc-objc.md)

> [!div class="nextstepaction"]
> [Swift](../how-tos/set-up-coarse-reloc-swift.md)

> [!div class="nextstepaction"]
> [Java](../how-tos/set-up-coarse-reloc-java.md)

> [!div class="nextstepaction"]
> [C++/NDK](../how-tos/set-up-coarse-reloc-cpp-ndk.md)

> [!div class="nextstepaction"]
> [C++/WinRT](../how-tos/set-up-coarse-reloc-cpp-winrt.md)

<!-- Reference links in article -->
[1]: https://developers.google.com/beacons/eddystone
[2]: https://developer.apple.com/ibeacon/
[3]: https://developer.android.com/reference/android/location/LocationManager
[4]: https://developer.apple.com/documentation/corelocation/cllocationmanager?language=objc
[5]: https://developer.android.com/guide/topics/connectivity/wifi-scan
[6]: https://www.cambridge.org/core/journals/journal-of-navigation/article/positional-accuracy-of-assisted-gps-data-from-highsensitivity-gpsenabled-mobile-phones/E1EE20CD1A301C537BEE8EC66766B0A9
