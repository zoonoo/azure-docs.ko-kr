---
title: IoT Edge 할당량 및 제한 사항에 대한 Live Video Analytics - Azure
description: 이 문서에서는 IoT Edge 할당량 및 제한 사항에 대한 Live Video Analytics를 설명합니다.
ms.topic: conceptual
ms.date: 05/22/2020
ms.openlocfilehash: 68c7b91bb1051348b5a8e52f841d443894f0a632
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97400527"
---
# <a name="quotas-and-limitations"></a>할당량 및 제한 사항

이 문서에서는 IoT Edge 모듈에서 Live Video Analytics의 할당량 및 제한 사항을 열거합니다.

## <a name="maximum-period-of-disconnected-use"></a>연결되지 않은 최대 사용 기간

Edge 모듈에서는 인터넷 연결이 일시적으로 끊어질 수 있습니다. 모듈 연결이 36시간 넘게 끊어진 상태를 유지할 경우 실행 중이던 모든 그래프 인스턴스가 비활성화됩니다. 모든 추가 직접 메서드 호출은 차단됩니다.

Edge 모듈을 작동 상태로 다시 시작하려면 모듈이 Azure Media Services 계정과 성공적으로 통신할 수 있도록 인터넷 연결을 복원해야 합니다.

## <a name="maximum-number-of-graph-instances"></a>그래프 인스턴스의 최대 수

모듈당 최대 1,000개의 그래프 인스턴스(GraphInstanceSet을 통해 만들어짐)가 지원됩니다.

## <a name="maximum-number-of-graph-topologies"></a>그래프 토폴로지의 최대 수

GraphTopologySet를 통해 생성된 모듈당 최대 50개의 그래프 토폴로지가 지원됩니다.

## <a name="limitations-on-graph-topologies-at-preview"></a>미리 보기의 그래프 토폴로지 제한 사항

미리 보기 릴리스에서는 다른 노드에 대한 제한 사항을 미디어 그래프 토폴로지와 함께 적용할 수 있습니다.

* RTSP 원본
   * 그래프 토폴로지마다 하나의 RTSP 원본만 허용됩니다.
* 동작 감지 프로세서
   * RTSP 원본에서 직접적으로 다운스트림이어야 합니다.
   * HTTP 또는 gRPC 확장 프로세서의 다운스트림으로 사용할 수 없습니다.
* 신호 게이트 프로세서
   * RTSP 원본에서 직접적으로 다운스트림이어야 합니다.
* 자산 싱크 
   * RTSP 원본 또는 신호 게이트 프로세서에서 직접적으로 다운스트림이어야 합니다.
* 파일 싱크
   * 신호 게이트 프로세서에서 직접적으로 다운스트림이어야 합니다.
   * HTTP나 gRPC 확장 프로세서 또는 동작 감지 프로세서에서 직접적으로 다운스트림일 수 없습니다.
* IoT Hub 싱크
   * IoT Hub 원본에서 직접적으로 다운스트림일 수 없습니다.

## <a name="limitations-on-media-service-operations-at-preview"></a>미리 보기의 미디어 서비스 작업 제한 사항

미리 보기가 출시되었을 때 IoT Edge의 Live Video Analytics는 다음을 지원하지 않습니다.

* 중단 없이 한 구독에서 다른 구독으로 미디어 서비스 계정을 마이그레이션하는 기능
* 미디어 서비스 계정에서 둘 이상의 스토리지 계정을 사용하는 기능
* 다시 시작하지 않고 모듈의 원하는 속성에 있는 서비스 주체 정보를 동적으로 변경하는 기능

RTSP 프로토콜을 지원하는 IP 카메라만 사용할 수 있습니다. [ONVIF 규격 제품](https://www.onvif.org/conformant-products) 페이지에서 RTSP를 지원하는 IP 카메라를 찾을 수 있습니다. G, S 또는 T 프로필을 준수하는 디바이스를 찾습니다.

또한 이러한 카메라는 H.264 비디오 및 AAC 오디오를 사용하도록 구성해야 합니다. 다른 코덱은 현재 지원되지 않습니다. 

## <a name="next-steps"></a>다음 단계

[개요](overview.md)
