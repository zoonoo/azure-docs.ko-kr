---
title: 네트워크 요구 사항
description: 최적의 환경을 위한 네트워크 요구 사항 및 최상의 네트워크 사례
author: florianborn71
ms.author: flborn
ms.date: 02/13/2020
ms.topic: reference
ms.openlocfilehash: 8fd77f7ac69296c8d59b703d202b23e2112bd912
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108764714"
---
# <a name="network-requirements"></a>네트워크 요구 사항

Azure 데이터 센터에 대한 안정적이고 낮은 대기 시간의 연결은 Azure Remote Rendering에서 좋은 사용자 환경을 제공하는 데 매우 중요 합니다. 네트워크 상태가 좋지 않으면 연결 끊김, 불안정, 떨림 또는 홀로그램 '흔들림'이 발생하고, 서버 측 장면 그래프를 업데이트할 때 눈에 보이는 지연이 발생할 수 있습니다.

## <a name="guidelines-for-network-connectivity"></a>네트워크 연결 지침

정확한 네트워크 요구 사항은 특정 사용 사례(예: 원격 장면 그래프의 수정 수 및 빈도, 렌더링된 보기의 복잡성)에 따라 다르지만 가능한 좋은 환경을 만들기 위한 여러 가지 지침이 있습니다.

* 네트워크에 경합 트래픽이 없는 것으로 가정하여 Azure Remote Rendering의 단일 사용자 세션에 대해 최소 **40Mbps 다운스트림** 및 **5Mbps 업스트림** 의 인터넷 연결을 지원해야 합니다. 더 나은 환경을 위해 더 높은 속도가 권장됩니다. 
* **Wi-Fi** 는 낮은 대기 시간, 높은 대역폭 및 안정적인 연결을 지원하기 때문에 권장되는 네트워크 유형입니다. 일부 모바일 네트워크에서는 좋지 않은 환경을 유발할 수 있는 끊김이 발생합니다. 
* **5GHz Wi-Fi 대역** 을 사용하면 2.4GHz Wi-Fi 대역보다 더 나은 결과를 얻을 수 있지만 두 가지 모두 괜찮습니다.
* 근처에 다른 Wi-Fi 네트워크가 있는 경우 다른 네트워크에서 사용하는 Wi-Fi 채널을 사용하지 마세요. [WifiInfoView](https://www.nirsoft.net/utils/wifi_information_view.html)와 같은 네트워크 검색 도구를 사용하여 Wi-Fi 네트워크에서 사용하는 채널에 경합 트래픽이 없는지 확인할 수 있습니다.
* **Wi-Fi 리피터** 또는 LAN-over-powerline 착신 전환을 사용하지 않는 것이 좋습니다.
* 동일한 Wi-Fi 네트워크에서 비디오 또는 게임 스트리밍처럼 **대역폭이 강한 트래픽 경합** 을 방지하세요.
* 동일한 액세스 포인트에 여러 디바이스가 있는 경우 요구 사항이 그에 따라 증가합니다. 환경에서 여러 액세스 포인트가 있는 경우 액세스 포인트에 장치 부하를 균등하게 분산합니다.
* **양호한 Wi-Fi 신호 강도** 는 필수입니다. 가능한 경우 Wi-Fi 액세스 포인트와 가까운 거리를 유지하고 클라이언트 디바이스와 액세스 포인트 간의 장애물을 없애세요.
* 항상 [지역](regions.md)의 **가장 가까운 Azure 데이터 센터** 에 연결해야 합니다. 데이터 센터가 가까울수록 네트워크 대기 시간이 낮아지고, 이는 홀로그램 안정성에 큰 영향을 미칩니다.

> [!NOTE]
> 다운스트림 대역폭은 주로 비디오 스트림에서 사용되며, 색 및 깊이 정보(모두 60Hz, 스테레오)로 분할됩니다.

## <a name="network-performance-tests"></a>네트워크 성능 테스트

네트워크 연결 품질이 Azure Remote Rendering을 실행하는 데 충분한지 여부를 초기에 파악한 경우 사용할 수 있는 기존 온라인 도구가 있습니다. Azure Remote Rendering 클라이언트 애플리케이션을 실행하려는 디바이스와 동일한 Wi-Fi에 연결된 강력한 성능의 랩톱에서 이러한 온라인 도구를 실행하는 것이 좋습니다. 휴대폰 또는 HoloLens2에서 테스트를 실행하여 얻은 결과는 일반적으로 유용하지 않은데, 저전력 엔드포인트 디바이스에서는 변화가 상당한 것으로 입증되었기 때문입니다. 랩톱을 두는 위치는 Azure Remote Rendering 클라이언트 애플리케이션을 실행하는 디바이스를 사용할 것으로 예상되는 위치와 거의 동일해야 합니다.

다음은 네트워크 연결의 빠른 테스트를 위한 몇 가지 간단한 단계입니다.

1. **www.speedtest.net과 같은 네트워크 테스트 도구를 실행하여 네트워크 연결의 전체 대기 시간 및 업스트림/다운스트림 대역폭에 대한 데이터를 가져옵니다.**
가장 가까운 서버를 선택하고 테스트를 실행합니다. 서버는 Azure Remote Rendering에서 연결할 Azure 데이터 센터가 아니지만 결과 데이터는 인터넷 연결 및 Wi-Fi의 성능을 이해하는 데 유용합니다.
   * Azure Remote Rendering **최소 요구 사항**: 약 40Mbps 다운스트림 및 5Mbps 업스트림.
   * Azure Remote Rendering **권장 사항**: 약 100Mbps 다운스트림 및 10Mbps 업스트림.
테스트를 여러 번 실행하고 최악의 결과를 가져오는 것이 좋습니다.
1. **Azure 데이터 센터에 대한 대기 시간을 측정하는 www.azurespeed.com과 같은 도구를 사용합니다**. 가장 가까운 Azure Remote Rendering에서 지원되는 Azure 데이터 센터([지원되는 지역](regions.md) 참조)를 선택하고 **대기 시간 테스트** 를 실행합니다. 표시되는 숫자에 변화가 있는 경우 안정화될 때까지 기다립니다.
   * Azure Remote Rendering의 **최소 요구 사항**: 대기 시간은 지속적으로 80ms 미만이어야 합니다.
   * Azure Remote Rendering의 **권장 사항**: 대기 시간은 지속적으로 40ms 미만이어야 합니다.

대기 시간이 낮은 것이 Azure Remote Rendering이 네트워크에서 제대로 작동할 것임을 보장하지는 않지만 이러한 테스트에서 성공적으로 통과한 상황에서 성능이 괜찮은 경우가 많았습니다.
Azure Remote Rendering을 실행할 때 불안정, 떨림 또는 홀로그램 흔들림 같은 아티팩트가 발생하는 경우 [문제 해결 가이드](../resources/troubleshoot.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* [빠른 시작: Unity를 사용하여 모델 렌더링](../quickstarts/render-model.md)
