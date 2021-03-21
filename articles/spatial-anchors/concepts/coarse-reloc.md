---
title: 광역 위치 재결정
description: 정교 하지 않은 재 지역화를 사용 하는 방법과 시기에 대해 알아봅니다. 거칠게 지역화를 사용 하면 가까운 앵커를 찾을 수 있습니다.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 01/28/2021
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.custom: devx-track-csharp
ms.openlocfilehash: 3f0b04183c4df469d4f723486103790c4f97671b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104656178"
---
# <a name="coarse-relocalization"></a>광역 위치 재결정

정교 하지 않은 지역화는 다음과 같은 질문에 대 한 대략적인 대답을 제공 하 여 대규모 지역화를 가능 하 게 해 주는 기능입니다. 
- *이제 장치는 어디에 있나요?* 
- *관찰 해야 하는 콘텐츠는 무엇 인가요?* 
 
응답이 정확 하지 않습니다. 다음 형식으로 되어 있습니다. *이러한 앵커에 가깝습니다. 그 중 하나를 찾으려고 시도* 합니다.

정교 하지 않은 다시 지역화는 나중에 빠른 쿼리에 사용 되는 다양 한 장치 센서 판독값으로 앵커에 태그를 지정 하는 방식으로 작동 합니다. 옥외 시나리오에서 센서 데이터는 일반적으로 장치의 GPS (글로벌 위치 시스템) 위치입니다. 실내에서와 같이 GPS를 사용할 수 없거나 신뢰할 수 없는 경우 센서 데이터는 범위의 Wi-Fi 액세스 지점과 Bluetooth 탐지 장치로 구성 됩니다. 수집 된 센서 데이터는 장치에 가까운 앵커를 신속 하 게 확인 하기 위해 Azure 공간 앵커에서 사용 되는 공간 인덱스를 유지 관리 하는 데 기여 합니다.

## <a name="when-to-use-coarse-relocalization"></a>정교 하지 않은 재 지역화를 사용 하는 경우

테니스 경기장 보다 큰 공간에서 35 개 이상의 공간 앵커를 처리 하려는 경우 정교 하지 않은 고급 지역화 공간 인덱싱이 유용할 수 있습니다.

정교 하지 않은 다중 지역화로 설정 된 앵커의 빠른 조회는 수백만 개의 지리적으로 분산 된 앵커와 같이 세계 규모의 컬렉션에서 지원 되는 응용 프로그램 개발을 간소화 하도록 설계 되었습니다. 공간 인덱싱의 복잡성은 모두 숨겨져 있으므로 응용 프로그램 논리에 집중할 수 있습니다. 모든 어려운 작업은 Azure 공간 앵커에 의해 백그라운드에서 수행 됩니다.

## <a name="using-coarse-relocalization"></a>정교 하지 않은 재 지역화 사용

대략적인 다시 지역화를 사용 하 여 Azure 공간 앵커를 만들고 쿼리 하는 일반적인 워크플로는 다음과 같습니다.
1.  센서 지문 공급자를 만들고 구성 하 여 원하는 센서 데이터를 수집 합니다.
2.  Azure 공간 앵커 세션을 시작 하 고 앵커를 만듭니다. 센서 지문 인식을 사용 하도록 설정 되어 있으므로 앵커는 거칠게 다시 지역화에 의해 공간적으로 인덱싱됩니다.
3.  공간 앵커 세션에서 전용 검색 조건을 통해 정교 하지 않은 재 지역화를 사용 하 여 주변 앵커를 쿼리 합니다.

이러한 자습서 중 하나를 참조 하 여 응용 프로그램에서 정교 하지 않은 재 지역화를 설정할 수 있습니다.
* [Unity의 광역 위치 재결정](../how-tos/set-up-coarse-reloc-unity.md)
* [Objective-C의 광역 위치 재결정](../how-tos/set-up-coarse-reloc-objc.md)
* [Swift의 광역 위치 재결정](../how-tos/set-up-coarse-reloc-swift.md)
* [Java의 광역 위치 재결정](../how-tos/set-up-coarse-reloc-java.md)
* [C++/NDK의 광역 위치 재결정](../how-tos/set-up-coarse-reloc-cpp-ndk.md)
* [C++/WinRT의 광역 위치 재결정](../how-tos/set-up-coarse-reloc-cpp-winrt.md)

## <a name="sensors-and-platforms"></a>센서 및 플랫폼

### <a name="platform-availability"></a>플랫폼 가용성

이러한 유형의 센서 데이터를 앵커 서비스로 보낼 수 있습니다.

* GPS 위치: 위도, 경도, 고도
* 범위 내 Wi-Fi 액세스 지점의 신호 강도
* 범위 내 Bluetooth 오류 신호 강도

이 표에는 지원 되는 플랫폼에서 센서 데이터의 가용성이 요약 되어 있으며 알고 있어야 하는 정보를 제공 합니다.

|                 | HoloLens | Android | iOS |
|-----------------|----------|---------|-----|
| **GPS**         | 아니요<sup>1</sup>  | 예<sup>2</sup> | 예<sup>3</sup> |
| **Wi-Fi**        | 예<sup>4</sup> | 예<sup>5</sup> | 아니요  |
| **오류 있는 오류** | 예<sup>6</sup> | 예<sup>6</sup> | 예<sup>6</sup>|


<sup>1</sup> 외부 GPS 장치를 HoloLens와 연결할 수 있습니다. GPS 추적기를 사용 하 여 HoloLens를 사용 하려는 경우 [지원](../spatial-anchor-support.md) 담당자에 게 문의 하세요.<br/>
<sup>2</sup> [LOCATIONMANAGER][3] API (GPS 및 네트워크 모두)를 통해 지원 됩니다.<br/>
<sup>3</sup> [cllocationmanager][4] api를 통해 지원 됩니다.<br/>
<sup>4</sup> 약 3 초 마다 한 번 검색의 속도로 지원 됩니다. <br/>
<sup>5</sup> API 수준 28부터 Wi-Fi 검색은 2 분 마다 4 번의 호출로 제한 됩니다. Android 10부터 **개발자 설정** 메뉴에서이 제한을 사용 하지 않도록 설정할 수 있습니다. 자세한 내용은 [Android 설명서][5]를 참조 하세요.<br/>
<sup>6</sup> [Eddystone][1] 및 [ibeacon 장치로][2]제한 됩니다.

### <a name="which-sensor-to-enable"></a>사용할 센서

센서 선택은 개발 중인 응용 프로그램 및 플랫폼에 따라 달라 집니다.
이 다이어그램은 지역화 시나리오에 따라 사용할 수 있는 센서의 조합을 결정 하기 위한 시작점을 제공 합니다.

![다양 한 시나리오에 사용할 수 있는 센서를 보여 주는 다이어그램](media/coarse-relocalization-enabling-sensors.png)

다음 섹션에서는 각 센서 유형의 이점 및 제한 사항에 대 한 자세한 정보를 제공 합니다.

### <a name="gps"></a>GPS

GPS는 실외 시나리오에 대 한 이동 옵션입니다.
응용 프로그램에서 GPS를 사용 하는 경우 하드웨어에서 제공 하는 판독값은 일반적으로 다음과 같습니다.

* 비동기 및 낮은 빈도 (1 Hz 미만)
* 불안정/잡음이 있습니다 (평균, 7-m 표준 편차).

일반적으로 장치 OS 및 공간 앵커는 이러한 문제를 완화 하기 위해 원시 GPS 신호의 일부 필터링 및 보 외의 작업을 수행 합니다. 이러한 추가 처리를 위해서는 수렴 시간이 필요 하므로 최상의 결과를 위해서는 다음을 수행 해야 합니다.

* 응용 프로그램에서 가능한 한 빨리 센서 지문 공급자를 만듭니다.
* 센서 지문 공급자를 여러 세션 간에 유지 합니다.
* 여러 세션 간에 센서 지문 공급자를 공유 합니다.

소비자 등급 GPS 장치는 일반적으로 정확 하지 않습니다. [Zandenbergen 및 바 Beau (2011)][6] 의 연구는 gps (-gps)을 지 원하는 휴대폰의 중간 정확도를 약 7 미터로 보고 합니다. 이것은 무시 해도 큰 값입니다. 이러한 측정 오류를 고려 하 여 서비스는 앵커를 GPS 공간의 확률 배포판으로 처리 합니다. 따라서 앵커는 가장 가능성이 높은 (95% 이상)의 공간 영역에 해당 하는 true, unknown GPS 위치를 포함 합니다.

GPS를 사용 하 여 쿼리 하는 경우에도 동일한 추론을 적용 합니다. 장치는 진정한 알려지지 않은 GPS 위치 주변의 다른 공간 신뢰 지역으로 표시 됩니다. 주변 앵커를 검색 하면 여기에 설명 된 대로 장치 신뢰도 영역에 *충분히 가까운* 신뢰 지역이 있는 앵커를 찾습니다.

![GPS를 사용 하 여 앵커 후보를 찾는 방법을 보여 주는 다이어그램입니다.](media/coarse-reloc-gps-separation-distance.png)

### <a name="wi-fi"></a>Wi-Fi

HoloLens 및 Android에서 Wi-Fi 신호 강도는 실내 거칠게 다시 지역화를 사용 하도록 설정 하는 좋은 방법일 수 있습니다.
이점은 추가 설정이 필요 없는 Wi-Fi 액세스 지점 (예: 사무실 공간 및 쇼핑 malls)의 잠재적 즉각적인 가용성입니다.

> [!NOTE]
> iOS는 Wi-Fi 신호 강도를 읽기 위한 API를 제공 하지 않으므로 Wi-fi를 통해 사용 하도록 설정 된 정교 하지 않은 재 지역화에 사용할 수 없습니다.

응용 프로그램에서 Wi-Fi를 사용 하는 경우 하드웨어에서 제공 하는 판독값은 일반적으로 다음과 같습니다.

* 비동기 및 낮은 빈도 (0.1 Hz 미만)
* 잠재적으로 OS 수준에서 제한 됩니다.
* 불안정/잡음이 있습니다 (평균, 3-dBm 표준 편차).

공간 앵커는 이러한 문제를 완화 하려는 시도에서 세션 중에 Wi-Fi 신호 강도의 필터링 된 맵을 빌드하려고 합니다. 최상의 결과를 위해 다음을 시도 합니다.

* 첫 번째 앵커를 넣기 전에 세션을 만듭니다.
* 세션을 최대한 오랫동안 활성 상태로 유지 합니다. 즉, 한 세션에서 모든 앵커와 쿼리를 만듭니다.

### <a name="bluetooth-beacons"></a>Bluetooth 오류 신호
<a name="beaconsDetails"></a>

Bluetooth 오류를 신중 하 게 배포 하는 것은 GPS이 없거나 부정확 한 대규모의 대규모의 비 지역화 시나리오에 적합 한 솔루션입니다. 세 플랫폼 모두에서 지원 되는 유일한 실내 방법 이기도 합니다.

오류 신호는 일반적으로 Uuid 및 MAC 주소를 포함 하 여 모든 항목을 구성할 수 있는 다양 한 장치입니다. Azure 공간 앵커에는 해당 Uuid에 의해 고유 하 게 식별 되는 오류 신호가 필요 합니다. 이러한 고유성이 보장 되지 않을 경우 잘못 된 결과가 발생할 수 있습니다. 최상의 결과를 얻으려면

* 고유 Uuid를 오류 신호에 할당 합니다.
* 공간을 균일 하 게 처리 하는 방식으로 오류 신호를 배포 하 여 모든 시점에서 세 개 이상의 오류 신호에 도달할 수 있습니다.
* 고유한 오류 신호 Uuid 목록을 센서 지문 공급자에 전달 합니다.

Bluetooth의 라디오 신호는 장애물의 영향을 받으며 다른 라디오 신호를 방해할 수 있습니다. 공간이 일관 되 게 적용 되는지 추측 하기 어려울 수 있습니다. 더 나은 고객 환경을 보장 하기 위해 오류 신호 검사를 수동으로 테스트 하는 것이 좋습니다. 후보 장치를 사용 하 여 공간을 탐색 하 고 범위 내에 Bluetooth를 표시 하는 응용 프로그램을 사용 하 여 테스트를 수행할 수 있습니다. 검사를 테스트 하는 동안 공간의 모든 전략적 위치에서 세 개 이상의 오류 신호에 도달할 수 있는지 확인 합니다. 오류 신호가 너무 많으면 그 사이에 더 많은 간섭을 초래 하 고 정교 하지 않은 지역화의 정확도를 반드시 향상 시킬 수 없습니다.

일반적으로 Bluetooth 신호는 공간에 장애물이 없는 경우 80 미터를 포함 합니다.
따라서 장애가 크지 않은 공간의 경우 40 미터 마다 표 패턴에 탐지 장치를 배포할 수 있습니다.

배터리 부족으로 인 한 신호는 결과에 영향을 주므로 낮은 또는 충전 되지 않은 배터리에 대해 주기적으로 배포를 모니터링 해야 합니다.

Azure 공간 앵커는 알려진 오류 신호 근접 Uuid 목록에 있는 Bluetooth 오류 표시만 추적 합니다. 하지만 allowlisted Uuid를 포함 하는 악성 오류 신호는 서비스 품질에 부정적인 영향을 줄 수 있습니다. 따라서 신호 배포를 제어할 수 있는 큐 레이트 공간에서 최상의 결과를 얻을 수 있습니다.

### <a name="sensor-accuracy"></a>센서 정확도

앵커 생성 중과 쿼리 중에 GPS 신호의 정확도는 반환 되는 앵커 집합에 상당한 영향을 미칩니다. 반면, Wi-fi/오류 신호를 기반으로 하는 쿼리는 쿼리와 공통적으로 하나 이상의 액세스 지점/신호를 포함 하는 모든 앵커를 고려 합니다. 이러한 의미에서 Wi-fi/오류 신호를 기반으로 하는 쿼리 결과는 주로 액세스 요소/오류 신호 및 환경적 장애물의 물리적 범위에 따라 결정 됩니다.
이 표에서는 각 센서 유형에 대해 예상 되는 검색 공간을 예상 합니다.

| 센서      | 검색-공간 반경 (근사치) | 세부 정보 |
|-------------|:-------:|---------|
| **GPS**         | 20 m-30 m | 다른 요인 중에서 GPS 불확실성에 의해 결정 됩니다. 보고 된 숫자는-GPS: 7 미터를 사용 하 여 휴대폰의 GPS 정확도에 대해 예상 됩니다. |
| **Wi-Fi**        | 50 m-100 m | 무선 액세스 지점의 범위에 따라 결정 됩니다. 빈도, 전송기 강도, 물리적 장애물, 간섭 등에 따라 달라 집니다. |
| **오류 있는 오류** |  70 m | 신호 범위에 따라 결정 됩니다. 빈도, 전송 강도, 물리적 장애물, 간섭 등에 따라 달라 집니다. |

<!-- Reference links in article -->
[1]: https://developers.google.com/beacons/eddystone
[2]: https://developer.apple.com/ibeacon/
[3]: https://developer.android.com/reference/android/location/LocationManager
[4]: https://developer.apple.com/documentation/corelocation/cllocationmanager?language=objc
[5]: https://developer.android.com/guide/topics/connectivity/wifi-scan
[6]: https://www.cambridge.org/core/journals/journal-of-navigation/article/positional-accuracy-of-assisted-gps-data-from-highsensitivity-gpsenabled-mobile-phones/E1EE20CD1A301C537BEE8EC66766B0A9
