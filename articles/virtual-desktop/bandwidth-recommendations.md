---
title: 원격 세션에 대 한 대역폭 권장 사항-Azure
description: 원격 세션에 사용할 수 있는 대역폭 권장 사항입니다.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 09/30/2019
ms.author: helohr
ms.openlocfilehash: 1a88cfb2fcd5f0b607e7a2b43ae833789a584e34
ms.sourcegitcommit: 8bae7afb0011a98e82cbd76c50bc9f08be9ebe06
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2019
ms.locfileid: "71695313"
---
# <a name="bandwidth-recommendations-for-remote-sessions"></a>원격 세션에 대 한 대역폭 권장 사항

원격 Windows 세션을 사용 하는 경우 네트워크의 사용 가능한 대역폭이 환경의 품질에 크게 영향을 줍니다. 응용 프로그램 및 디스플레이 해상도 마다 다른 네트워크 구성이 필요 하므로 네트워크가 요구에 맞게 구성 되었는지 확인 하는 것이 중요 합니다. 이 문서에서는 각 워크 로드에 대해 권장 되는 대역폭을 설명 합니다.

>[!NOTE]
>다음 권장 사항은 0.1% 미만의 네트워크에 적용 됩니다.

## <a name="applications"></a>애플리케이션

다음 표에는 원활한 사용자 환경에 대 한 최소 요구 사항이 정리 되어 있습니다. 

|작업        |샘플 애플리케이션                                                                                           |권장 대역폭|
|----------------|--------------------------------------------------------------------------------------------------------------|---------------------|
|비사무직 근로자     |Microsoft Word, Outlook, Excel, Adobe Reader                                                                  |1.5 @ no__t-0Mbps             |
|Office 작업자   |Microsoft Word, Outlook, Excel, Adobe Reader, PowerPoint, 사진 뷰어                                        |3 @ no__t-0Mbps               |
|지식 근로자|Microsoft Word, Outlook, Excel, Adobe Reader, PowerPoint, 사진 뷰어, Java                                  |5 @ no__t-0Mbps               |
|Power worker    |Microsoft Word, Outlook, Excel, Adobe Reader, PowerPoint, 사진 뷰어, Java, CAD/CAM, 그림/게시|15 @ no__t-0Mbps              |

네트워크에 추가 되는 스트레스는 앱 워크 로드의 출력 프레임 비율과 디스플레이 해상도에 따라 달라 집니다. 프레임 속도나 디스플레이 해상도가 증가 하는 경우 대역폭 요구 사항도 증가 합니다. 예를 들어 고해상도 디스플레이를 사용 하는 광원 워크 로드에는 일반 또는 낮음 해상도를 사용 하는 가벼운 워크 로드 보다 더 많은 사용 가능한 대역폭이 필요 합니다.

다른 시나리오의 대역폭 요구 사항은 다음과 같이 사용 하는 방법에 따라 달라질 수 있습니다.

- 음성 또는 비디오 회의
- 실시간 통신
- 4K 비디오 스트리밍

로그인 .VSI와 같은 시뮬레이션 도구를 사용 하 여 배포에서 이러한 시나리오를 로드 해야 합니다. 네트워크 요구 사항을 더 잘 이해 하려면 로드 크기를 변경 하 고, 스트레스 테스트를 실행 하 고, 원격 세션에서 일반적인 사용자 시나리오를 테스트 합니다. 

## <a name="display-resolutions"></a>디스플레이 해상도

다른 디스플레이 해상도를 사용 하려면 사용 가능한 다른 대역폭이 필요 합니다. 다음 표에서는 프레임 속도 (fps)가 프레임 속도 인 일반적인 디스플레이 해상도에서 부드러운 사용자 환경을 위해 권장 하는 대역폭을 보여 줍니다. 정적 텍스트 읽기와 같이 프레임 속도의 프레임 속도와 관련 된 시나리오에서는 사용 가능한 대역폭이 적게 필요 합니다. 

|30 fps의 일반적인 디스플레이 해상도    |권장 대역폭|
|-----------------------------------------|---------------------|
|1024 × 768 px 정보                      |1.5 Mbps             |
|1280 × 720 px 정보                      |3mbps               |
|1920 × 1080 px 정보                     |5Mbps               |
|3840 × 2160 px (4K) 정보                |15mbps              |
