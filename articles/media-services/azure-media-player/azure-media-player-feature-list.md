---
title: Azure Media Player 기능 목록
description: Azure Media Player에 대한 기능 참조입니다.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/05/2021
ms.openlocfilehash: 4b0666b439c284fd402b3f6e04bbaed14d6aa358
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106448766"
---
# <a name="feature-list"></a>기능 목록 #
다음은 테스트된 기능 및 지원되지 않는 기능 목록입니다.

| 기능 | 테스트됨 | 부분적으로 테스트됨 | 테스트되지 않음 | 지원되지 않음 | 참고 |
| ------- | ------ | ---------------- | -------- | ----------- | ----- |
| **재생**                                |        |                  |          |             |                                                                                                                      |
| 기본 주문형 재생                | X      |                  |          |             | Azure Media Services의 스트림만 지원                                                                      |
| 기본 라이브 재생                     | X      |                  |          |             | Azure Media Services의 스트림만 지원                                                                      |
| AES                                     | X      |                  |          |             | Azure Media Services 키 전달 서비스 지원                                                                   |
| 다중 DRM                               |        | X                |          |             |                                                                                                                      |
| PlayReady                               | X      |                  |          |             | Azure Media Services 키 전달 서비스 지원                                                                   |
| Widevine                                |        | X                |          |             | 매니페스트에 설명된 Widevine PSSH 상자 지원                                                                    |
| FairPlay                                |        | X                |          |             | Azure Media Services 키 전달 서비스 지원                                                                   |
| **Techs**                                   |        |                  |          |             |                                                                                                                      |
| MSE/EME(AzureHtml5JS)                  | X      |                  |          |             |                                                                                                                      |
| Flash 대체(FlashSS)                | X      |                  |          |             | 이 기술에서는 일부 기능을 사용할 수 없습니다.                                                                         |
| Silverlight 대체 SilverlightSS      | X      |                  |          |             | 이 기술에서는 일부 기능을 사용할 수 없습니다.                                                                         |
| 네이티브 HLS 통과(Html5)         |        | X                |          |             | 플랫폼 제한으로 인해 이 기술에서는 일부 기능을 사용할 수 없습니다.                                            |
| **기능**                                |        |                  |          |             |                                                                                                                      |
| API 지원                             | X      |                  |          |             | 알려진 문제 목록 참조                                                                                                |
| 기본 UI                                | X      |                  |          |                                                                                                                                    |
| JavaScript를 통한 초기화       | X      |                  |          |             |                                                                                                                      |
| 비디오 태그를 통한 초기화        |        | X                |          |             |                                                                                                                      |
| 세그먼트 주소 지정 - 시간 기반         | X      |                  |          |             |                                                                                                                      |
| 세그먼트 주소 지정 - 인덱스 기반        |        |                  |          | X           |                                                                                                                      |
| 세그먼트 주소 지정 - 바이트 기반         |        |                  |          | X           |                                                                                                                      |
| Azure Media Services URL 재작성기       |        | X                |          |             |                                                                                                                      |
| 접근성 - 캡션 및 자막  | X      |                 |          |             |  WebVTT(주문형), CEA 708(주문형 및 라이브) 및 IMSC1(주문형 및 라이브)                                                       |
| 접근성 - 바로 가기 키                 | X      |                  |          |             |                                                                                                                      |
| 접근성 - 고대비           |        | X                |          |             |                                                                                                                      |
| 접근성 - 탭 포커스               |        | X                |          |             |                                                                                                                      |
| 오류 메시지                         |        | X                |          |             | 기술 간에 오류 메시지가 일치하지 않습니다.                                                                         |
| 이벤트 트리거                        | X      |                  |          |             |                                                                                                                      |
| 진단                             |        | X                |          |             | 진단 정보는 AzureHtml5JS 기술에서만 사용할 수 있으며 SilverlightSS 기술에서는 부분적으로 사용할 수 있습니다. |
| 사용자 지정 가능한 기술 순서                 |        | X                |          |             |                                                                                                                      |
| 추론 - 기본                      | X      |                  |          |             |                                                                                                                      |
| 추론 - 사용자 지정              |        |                  | X        |             | 사용자 지정은 AzureHtml5JS 기술에서만 사용할 수 있습니다.                                                          |
| 불연속성                         | X      |                  |          |             |                                                                                                                      |
| 비트 전송률 선택                          | X      |                  |          |             | 이 API는 AzureHtml5JS 및 FlashSS 기술에서만 사용할 수 있습니다.                                                    |
| 다중 오디오 스트림                      |        | X                |          |             | 프로그래밍 방식 오디오 스위치는 AzureHtml5JS 및 FlashSS에서 지원되며 AzureHtml5JS, FlashSS 및 네이티브 Html5(Safari)에서 UI 선택을 통해 사용할 수 있습니다.  대부분의 플랫폼은 오디오 스트림을 전환하기 위해 동일한 코덱 프라이빗 데이터(동일한 코덱, 채널, 샘플링 속도 등)가 필요합니다. |
| UI 지역화                         |        | X                |          |             |                                                                                                                      |
| 다중 인스턴스 재생                 |        |                  |          | X           | 이 시나리오는 일부 기술에서 작동할 수 있지만 현재 지원되지 않으며 테스트되지 않았습니다. iframes를 사용하여 이 작업을 수행할 수도 있습니다. |
| 광고 지원                             |        | X                |          |             | AMP는 AzureHtml5JS 기술의 VOD용 VAST 규격 광고 서버에서 사전, 중간 및 사후 선형 롤 광고 삽입을 지원합니다. |
| 분석                               |        | X                |          |             | AMP는 원하는 분석 백 엔드에 보내기 위해 분석 및 진단 이벤트를 수신하는 기능을 제공합니다.  플랫폼 제한으로 인해 기술 전반에 걸쳐 일부 이벤트 및 속성을 사용할 수 없습니다.                                                                            |
| 사용자 지정 스킨                            |        |                  | X        |             | 이 시나리오는 AMP에서 컨트롤을 false로 설정하고 고유한 HTML 및 CSS를 사용하여 구현할 수 있습니다.           |
| 검색 표시줄 스크러빙                      |        |                  |          | X           |                                                                                                                      |
| Trick-Play                              |        |                  |          | X           |                                                                                                                      |
| 오디오 전용                              | X      |                  |          |           | AzureHtml5JS에서 지원됩니다. 프로그레시브 MP3 재생은 플랫폼에서 지원되는 경우 HTML5 기술에서 사용할 수 있습니다.                                                                                                        |
| 비디오 전용                              | X      |                  |          |           | AzureHtml5JS에서 지원됩니다.                                                                                                        |
| 여러 기간 프레젠테이션               |        |                  |          | X                                                                                                                                  |
| 다중 카메라 각도                  |        |                  |          | X           |                                                                                                                      |
| 재생 속도                          |        | X                |          |             | Chrome의 부분 버그로 인해 모바일 사례를 제외하고 대부분의 시나리오에서 재생 속도가 지원됩니다.                 |

## <a name="next-steps"></a>다음 단계 ##
- [Azure Media Player 빠른 시작](azure-media-player-quickstart.md)