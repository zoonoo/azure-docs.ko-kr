---
title: Azure Media Player 기능 목록
description: Azure Media Player에 대 한 기능 참조입니다.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/20/2020
ms.openlocfilehash: e5595620a2f888b06ad5b35d2e8a008f23861463
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81727230"
---
# <a name="feature-list"></a>기능 목록 #
다음은 테스트 된 기능 및 지원 되지 않는 기능 목록입니다.

|                                         | 테스트 | 부분적으로 테스트 됨 | 테스트 되지 않은 | 않음 | 참고                                                                                                                |
|:----------------------------------------|--------|------------------|----------|-------------|:---------------------------------------------------------------------------------------------------------------------|
| 재생                                |        |                  |          |             |                                                                                                                      |
| 기본 주문형 재생                | X      |                  |          |             | Azure Media Services 에서만 스트림을 지원 합니다.                                                                      |
| 기본 라이브 재생                     | X      |                  |          |             | Azure Media Services 에서만 스트림을 지원 합니다.                                                                      |
| AES                                     | X      |                  |          |             | Azure Media Services 키 배달 서비스 지원                                                                   |
| 다중 DRM                               |        | X                |          |             |                                                                                                                      |
| PlayReady                               | X      |                  |          |             | Azure Media Services 키 배달 서비스 지원                                                                   |
| Widevine                                |        | X                |          |             | 매니페스트에 설명 된 Widevine PSSH 상자를 지원 합니다.                                                                    |
| FairPlay                                |        | X                |          |             | Azure Media Services 키 배달 서비스 지원                                                                   |
| Techs                                   |        |                  |          |             |                                                                                                                      |
| MSE/EME (AzureHtml5JS)                  | X      |                  |          |             |                                                                                                                      |
| 플래시 대체 (Flashss 기술은)                | X      |                  |          |             | 모든 기능을이 기술에서 사용할 수 있는 것은 아닙니다.                                                                         |
| Silverlight 대체 SilverlightSS      | X      |                  |          |             | 모든 기능을이 기술에서 사용할 수 있는 것은 아닙니다.                                                                         |
| 네이티브 HLS 통과 (Html5)         |        | X                |          |             | 플랫폼 제한으로 인해이 기술에서 모든 기능을 사용할 수 있는 것은 아닙니다.                                            |
| 기능                                |        |                  |          |             |                                                                                                                      |
| API 지원                             | X      |                  |          |             | 알려진 문제 목록을 참조 하세요.                                                                                                |
| 기본 UI                                | X      |                  |          |                                                                                                                                    |
| JavaScript를 통한 초기화       | X      |                  |          |             |                                                                                                                      |
| 비디오 태그를 통해 초기화        |        | X                |          |             |                                                                                                                      |
| 세그먼트 주소 지정 시간 기반         | X      |                  |          |             |                                                                                                                      |
| 세그먼트 주소 지정-인덱스 기반        |        |                  |          | X           |                                                                                                                      |
| 세그먼트 주소 지정 바이트 기반         |        |                  |          | X           |                                                                                                                      |
| Azure Media Services URL 재작성 기       |        | X                |          |             |                                                                                                                      |
| 접근성-캡션 및 자막  |        | X                |          |             |  주문형에 대해 지원 되는 WebVTT, 라이브 CEA 708 부분 테스트                                                       |
| 접근성-바로 가기 키                 | X      |                  |          |             |                                                                                                                      |
| 접근성-고대비           |        | X                |          |             |                                                                                                                      |
| 내게 필요한 옵션-탭 포커스               |        | X                |          |             |                                                                                                                      |
| 오류 메시지                         |        | X                |          |             | Techs 간에 오류 메시지가 일치 하지 않습니다.                                                                         |
| 이벤트 트리거                        | X      |                  |          |             |                                                                                                                      |
| 진단                             |        | X                |          |             | 진단 정보는 AzureHtml5JS tech 에서만 제공 되며 SilverlightSS 기술에서 부분적으로 사용할 수 있습니다. |
| 사용자 지정 가능한 기술 순서                 |        | X                |          |             |                                                                                                                      |
| 추론-기본                      | X      |                  |          |             |                                                                                                                      |
| 추론-사용자 지정              |        |                  | X        |             | 사용자 지정은 AzureHtml5JS tech 에서만 사용할 수 있습니다.                                                          |
| 불연속성                         | X      |                  |          |             |                                                                                                                      |
| 비트 전송률 선택                          | X      |                  |          |             | 이 API는 AzureHtml5JS 및 Flashss 기술은 techs 에서만 사용할 수 있습니다.                                                    |
| 다중 오디오 스트림                      |        | X                |          |             | 프로그래밍 오디오 스위치는 AzureHtml5JS 및 Flashss 기술은 techs에서 지원 되며, AzureHtml5JS, Flashss 기술은 및 네이티브 Html5 (Safari)에서 UI를 선택할 때 사용할 수 있습니다.  대부분의 플랫폼에서 오디오 스트림을 전환 하려면 동일한 코덱 전용 데이터 (동일한 코덱, 채널, 샘플링 주기 등)가 필요 합니다. |
| UI 지역화                         |        | X                |          |             |                                                                                                                      |
| 다중 인스턴스 재생                 |        |                  |          | X           | 이 시나리오는 일부 techs 경우에만 작동할 수 있지만 현재 지원 되지 않으며 테스트 되지 않았습니다. Iframe을 사용 하 여이 작업을 수행할 수도 있습니다. |
| 광고 지원                             |        | x                |          |             | AMP는 AzureHtml5JS 기술에 대 한 VOD를 위해 방대한 규격의 ad 서버에서 중간 및 후 롤 선형 광고를 삽입할 수 있도록 지원 합니다. |
| 분석                               |        | X                |          |             | AMP는 선택한 분석 백 엔드에 보내기 위해 분석 및 진단 이벤트를 수신 하는 기능을 제공 합니다.  플랫폼 제한으로 인해 모든 이벤트 및 속성은 techs에서 사용할 수 없습니다.                                                                            |
| 사용자 지정 스킨                            |        |                  | X        |             | AMP에서 컨트롤을 false로 설정 하 고 고유한 HTML 및 CSS를 사용 합니다.           |
| 검색 표시줄 스크러빙                      |        |                  |          | X           |                                                                                                                      |
| 트릭 재생                              |        |                  |          | X           |                                                                                                                      |
| 오디오만                              |        |                  |          | X           | 는 적응 스트리밍을 위해 일부 techs에서 작동할 수 있지만 현재 지원 되지 않으며 AzureHtml5JS에서 작동 하지 않습니다. 프로그레시브 MP3 재생은 플랫폼에서 지원 되는 경우 HTML5 기술에 사용할 수 있습니다.                                                                                                        |
| 비디오만                              |        |                  |          | X           | 는 적응 스트리밍을 위해 일부 techs에서 작동할 수 있지만 현재 지원 되지 않으며 AzureHtml5JS에서 작동 하지 않습니다.      |
| 여러 기간 프레젠테이션               |        |                  |          | X                                                                                                                                  |
| 다중 카메라 각도                  |        |                  |          | X           |                                                                                                                      |
| 재생 속도                          |        | X                |          |             | 재생 속도는 Chrome의 부분 버그로 인 한 모바일 사례를 제외 하 고 대부분의 시나리오에서 지원 됩니다.                 |

## <a name="next-steps"></a>다음 단계 ##
- [Azure Media Player 빠른 시작](azure-media-player-quickstart.md)