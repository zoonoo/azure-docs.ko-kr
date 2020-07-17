---
title: Azure Media Player 변경 로그
description: Azure Media Player 변경 로그입니다.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/20/2020
ms.openlocfilehash: a1a55ceec2679034125ddd202402cabcbf71e17e
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2020
ms.locfileid: "83698315"
---
# <a name="changelog"></a>Changelog #

## <a name="224-official-update-february-22-2019"></a>2.2.4(2019년 2월 22일 공식 업데이트) ##

### <a name="bug-fixes-224"></a>버그 수정 2.2.4 ###

- [버그 수정][AMP][접근성] 오류 화면이 표시되면 연결 가능한 가상 탭이 제거됨
- [버그 수정][AMP] IE11 및 Edge에 대한 'M' 바로 가기 키가 수정됨
- [버그 수정][AMP] CEA708 캡션에 대한 예외가 수정됨
- [버그 수정][AMP] Edge 브라우저에서 비디오가 멈추는 문제가 해결됨

### <a name="changes-224"></a>변경된 기능 2.2.4 ###

- [변경][AMP] 조각 암호 해독 오류가 발생하면 플레이어에서 현재 및 다양한 조각을 다시 시도하여 재생을 복구함
- [변경][AMP] AMP에서 겹치는 비디오 또는 오디오 조각이 더 많이 허용됨

## <a name="223-official-update-january-9-2019"></a>2.2.3(2019년 1월 9일 공식 업데이트) ##

### <a name="features"></a>기능 ###

- [기능][HLS] Safari HLS 재생을 위한 오디오 트랙 메뉴가 추가됨

### <a name="bug-fixes-223"></a>버그 수정 2.2.3 ###

- [버그 수정][AMP][접근성] 라이브 브로드캐스트 재생 중에 키보드를 사용하여 "라이브" 단추를 선택할 수 없음
- [버그 수정][AMP] MSE 테스트 실패로 인한 가양성 0x0400003 오류가 수정됨
- [버그 수정][AMP] 라이브 스트림을 시작할 때 비디오가 멈출 수 있는 문제가 해결됨

### <a name="changes-223"></a>변경된 기능 2.2.3 ###

- [변경][AMP] 더 효율적인 진단을 위해 로그에 더 많은 정보가 추가됨
- [변경][AMP] 동일한 화면 해상도에서 둘 이상의 비트 전송률을 사용할 수 있는 경우 모든 비트 전송률을 선택할 수 있음

## <a name="222-official-update"></a>2.2.2(공식 업데이트) ##

### <a name="bug-fixes-222"></a>버그 수정 2.2.2 ###

- [버그 수정][AMP] 플레이어에서 일시적인 네트워크 중단이 발생하면 재생이 즉시 중지됨
- [버그 수정][AMP][접근성] 키보드에서 오류 대화 상자에 액세스할 수 없음
- [버그 수정][AMP] 지원되지 않는 오류 대신 오디오 전용 자산을 재생하면 무한 회전자가 표시됨

### <a name="changes-222"></a>변경된 기능 2.2.2 ###

- [변경][AMP] 보급 알림 UI에 대한 지역화된 문자열이 추가됨

## <a name="221-official-update"></a>2.2.1(공식 업데이트) ##

### <a name="features-221"></a>기능 2.2.1 ###

- [기능][CMAF] HLS CMAF 지원이 추가됨

### <a name="bug-fixes"></a>버그 수정 ###

- [버그 수정][AMP] 재시도 논리에서 지워지지 않은 타이머로 인해 재생 오류가 발생함
- [버그 수정][AMP][Firefox] 라이브 프로그램을 중지하면 Firefox 및 Chrome에서 종료됨 이벤트가 발생하지 않음
- [버그 수정][AMP] 플레이어 옵션에서 컨트롤이 false로 설정된 경우에도 컨트롤이 setsource 뒤에 표시됨

### <a name="changes"></a>변경 ###

- [변경][라이브 캡션] CEA 캡션에 대한 API 이름이 608에서 708로 변경됨. 자세한 내용은 [CEA708 캡션 설정](https://docs.microsoft.com/javascript/api/azuremediaplayer/amp.player.cea708captionssettings)-->을 참조하세요.

## <a name="220-official-release"></a>2.2.0(공식 릴리스) ##

### <a name="features-220"></a>기능 2.2.0 ###

- [기능][Azurehtml5JS][Flash][LiveCaptions] Azurehtml5JS 및 FlashSS 기술에서 지우기 및 AES 콘텐츠에 대한 CEA 708 캡션이 지원됨

### <a name="bug-fixes-220"></a>버그 수정 2.2.0 ###

- [버그 수정] Chrome/Edge에서 Flash 버전 검색이 작동하지 않음

### <a name="changes-220"></a>변경된 기능 2.2.0 ###

- [변경][AMP][추론] 추론 프로필 이름이 QuickStartive에서 LowLatency로 변경됨
- [변경][Flash] 새 Adobe Flash 업데이트를 사용하여 AES 콘텐츠를 재생하도록 설정할 수 있도록 Flash Player에서 버전 검색이 변경됨

## <a name="219-official-hotfix"></a>2.1.9(공식 핫픽스) ##

### <a name="bug-fixes-219"></a>버그 수정 2.1.9 ###

- [버그 수정][라이브] 라이브 스트림이 주문형 비디오/라이브 보관으로 전환되면 예외가 발생함

### <a name="changes-219"></a>변경된 기능 2.1.9 ###

- [변경][Flash][AES] Adobe에서 Flash 30부터 사용을 차단하여 sharedbytearray를 AES 암호 해독에 사용하지 않도록 Flash 기술 논리가 수정되었습니다. v30의 버그로 인해 Adobe에서 새 Flash 버전을 배포한 경우에만 재생이 작동합니다. 자세한 내용은 [알려진 문제](azure-media-player-known-issues.md)를 참조하세요.

## <a name="218-official-update"></a>2.1.8(공식 업데이트) ##

### <a name="bug-fixes-218"></a>버그 수정 2.1.8 ###

- [버그 수정] 경우에 따라 회전자에서 사후 검색 및 사전 재생을 표시하지 않음
- [버그 수정] 음소거 옵션을 사용하도록 설정되면 플레이어에서 음소거가 시작되지 않음
- [버그 수정] 컨트롤이 false로 설정되면 볼륨 슬라이더가 표시됨
- [버그 수정] 사용자가 라이브 에지로 건너뛰면 경우에 따라 재생이 반복됨
- [버그 수정][Firefox] 로드 시 경우에 따라 플레이어에서 JavaScript 예외가 throw됨
- [버그 수정][접근성] 키보드 컨트롤을 사용하여 선택하면 재생/일시 중지/볼륨 단추의 포커스 윤곽선이 손실됨
- [버그 수정] 플레이어의 메모리 누출이 삭제되는 문제가 해결됨
- [버그 수정] 플레이어 오류가 발생한 후 src()를 호출해도 원본이 다시 설정되지 않음
- [버그 수정][라이브] 브로드캐스트가 종료된 후 사용자가 라이브 단추를 클릭하면 AMP가 계속 로드 중 상태임
- [버그 수정][Chrome] 브라우저가 백그라운드에서 최소화하면 플레이어가 중지되고 재생이 실패함

### <a name="changes-218"></a>변경된 기능 2.1.8 ###

- [변경] Flash 30에서 현재 지원되지 않는 AES 콘텐츠를 재생하면 0x0600001 오류가 업데이트되었습니다. 자세한 내용은 [알려진 문제](azure-media-player-known-issues.md)를 참조하세요.
- [변경] 매니페스트에서 404를 요청하거나 빈 매니페스트가 반환되는 경우 라이브 시나리오에 대한 추가 재시도 횟수가 추가되었습니다.

## <a name="217-official-update"></a>2.1.7(공식 업데이트) ##

### <a name="features-217"></a>기능 2.1.7 ###

- [기능][AzureHtml5JS] 미디어 원본 버퍼에서 부실 데이터를 플러시하는 구성 옵션이 추가됨

### <a name="bug-fixes-217"></a>버그 수정 2.1.7 ###

- [버그 수정][접근성][화면 읽기 프로그램] 제목이 설정되지 않으면 플레이어에서 포함한 빈 헤더가 제거됨
- [버그 수정][UWA] 유니버설 Windows 앱에서 재생하면 AMP에서 예외가 throw됨
- [버그 수정][OSX] OSx의 Safari에서 setActiveTextTrack()이 작동하지 않음
- [버그 수정][라이브] 플레이어를 삭제하고 다시 초기화한 후에 라이브 에지를 클릭하면 예외가 발생함
- [버그 수정][스킨] 특정 자산에 대한 현재 시간이 잘림
- [버그 수정][DRM] 여러 CENC DRM을 지원하는 브라우저에서 재생을 지원하기 위한 수정이 포함됨

### <a name="changes-217"></a>변경된 기능 2.1.7 ###

- [변경][샘플][접근성] 언어 태그가 모든 샘플에 추가됨

## <a name="216-official-update"></a>2.1.6(공식 업데이트) ##

### <a name="bug-fixes-216"></a>버그 수정 2.1.6 ###

- [버그 수정] AMP에서 특정 자산에 대해 잘못된 기간을 표시함
- [버그 수정][FairPlay-HLS] Fairplay 오류가 UI로 전파되지 않음
- [버그 수정] AMP 2.1.5에서 사용자 지정 추론 속성이 무시됨

### <a name="changes-216"></a>변경된 기능 2.1.6 ###

- [변경][FairPlayDRM] PlayReady 및 Widevine 구현을 통해 패리티를 유지하기 위해 FairPlay에 대한 인증서 요청 및 라이선스 요청 시간 제한이 제거됨
- [변경] 흐린 콘텐츠를 방지하기 위해 기타 추론이 향상됨

### <a name="features-216"></a>기능 2.1.6 ###

- [기능] mpd-time-cmaf 형식 지원이 추가됨

## <a name="215-official-hotfix"></a>2.1.5(공식 핫픽스) ##

### <a name="bug-fixes-215"></a>버그 수정 2.1.5 ###

- [버그 수정][캡션] 플레이어에서 VTT 스타일 지정이 올바르게 렌더링되지 않음
- [버그 수정][접근성] 라이브 단추에 aria 레이블이 없음

## <a name="214-official-update"></a>2.1.4(공식 업데이트) ##

### <a name="bug-fixes-214"></a>버그 수정 2.1.4 ###

- [버그 수정][접근성][포커스] 사용자가 탭 이동하여 컨트롤 막대에서 전체 화면 단추의 오른쪽에 추가된 사용자 지정 단추에 포커스를 둘 수 없음
- [버그 수정][IE11][볼륨 막대] 볼륨 팝업으로 탭 이동하면 전체 화면 모드의 IE11에서 전체 비디오 화면이 플래시됨
- [버그 수정][스킨|플러시] 컨트롤 막대 팝업과 볼륨 막대 팝업 사이에 공간이 표시됨
- [버그 수정][AMP][캡션] 기존 플레이어에서 원본이 변경되면 이전에 포함된 트랙이 지워지지 않음
- [버그 수정][접근성][내레이터] 화면 읽기 프로그램에서 볼륨 컨트롤을 잘못 읽음
- [버그 수정][FlashSS] 경우에 따라 Flash 기술에서 이벤트 재생이 실행되지 않음
- [버그 수정][AMP][포커스] 플레이어에 포커스가 있고 전체 화면 모드에 있는 경우 재생/일시 중지하려면 두 번 클릭해야 함
- [버그 수정][AMP][스킨] 특정 자산의 진행률 표시줄에 잘못된 기간이 표시됨
- [버그 수정][광고][광고 버틀러] VAST 파서에서 진행률 이벤트가 없는 VAST 파일을 처리하지 않음
- [버그 수정][SDN][AMP 2.1.1] Hive SDN 플러그 인 지원 문제가 해결됨
- [버그 수정][접근성] 사용자가 포커스를 볼륨 단추에 두면 내레이터에서 "자정 음소거 단추"를 읽음

### <a name="changes-214"></a>변경된 기능 2.1.4 ###

- [변경][접근성][보조 기술] 보조 기술을 사용하여 환경을 향상시키기 위해 이제 aria-live 속성이 단추에 있음
- [변경][접근성][볼륨 단추|내레이터] 탭 이동 기능 및 슬라이더 동작을 수정하여 볼륨 단추의 접근성이 향상되었습니다. 이러한 변경을 통해 키보드 사용자가 플레이어의 볼륨을 쉽게 수정할 수 있습니다.
- [변경] 비활성 상황에 맞는 메뉴의 시간 제한이 3초에서 5초로 증가함
- [변경][접근성][광도] 캡션 설정에서 드롭다운 메뉴의 광도 대비 비율이 향상됨

## <a name="213-official-update"></a>2.1.3(공식 업데이트) ##

### <a name="bug-fixes-213"></a>버그 수정 2.1.3 ###

- [버그 수정][플러그 인|제목 오버레이] 제목 오버레이 플러그 인에서 AMP v2.X 이상과 관련된 JS 예외가 throw됨
- [버그 수정] 로깅이 해제된 경우에도 원본 설정 이벤트를 JavaScript 콘솔로 보냄
- [버그 수정][스킨] 마우스로 종료 기간 표시줄 위를 가리키면 플레이어 시간 팁이 플레이어의 컨텍스트 외부에 렌더링됨
- [버그 수정][접근성][화면 읽기 프로그램] 뷰어에서 플레이어에 포커스가 있으면 내레이터에서 "지역 랜드마크" 또는 "비디오 플레이어 지역 랜드마크"를 읽음
- [버그 수정][AMP] CSS를 통해 플레이어 윤곽선을 사용하지 않도록 설정할 수 없음
- [버그 수정][접근성] 사용자가 전체 화면 모드에 있으면 탭 이동하여 전체 플레이어에 포커스를 둘 수 없음
- [버그 수정][스킨][라이브] 스킨에서 일본어로 지역화된 라이브 텍스트에 반응하지 않음
- [버그 수정][스킨] 스트림이 60분을 초과하면 기간 및 현재 시간이 잘림 - [버그 수정][iPhone|라이브] 플레이어에서 현재 시간/기간에 대한 텍스트를 컨트롤 막대에 표시함
- [버그 수정][AMP] 플레이어 추론 API를 호출하면 JavaScript 예외가 발생함
- [버그 수정][네이티브 Html5|iOS] "playsinline" 비디오 태그 속성이 플레이어에 전파되지 않음
- [버그 수정][iOS|iframe] 플레이어가 iframe에 로드되면 플레이어의 iPhone에서 전체 화면을 시작할 수 없음
- [버그 수정][AMP][추론] 플레이어 옵션에 관계없이 AMP에서 항상 하이브리드 프로필을 사용하여 작동함
- [버그 수정][AMP|Win8.1] 웹 보기를 사용하여 Win8.1 앱에서 호스팅되면 throw함

### <a name="changes-213"></a>변경된 기능 2.1.3 ###

- [변경][AMP] CDN 엔드포인트 정보가 FragmentDownloadComplete 이벤트에 추가됨
- [변경][AMP][라이브] 라이브 스트리밍 대기 시간이 향상되고 최적화됨

## <a name="212-official-hotfix"></a>2.1.2(공식 핫픽스) ##

### <a name="bug-fixes-212"></a>버그 수정 2.1.2 ####

- [버그 수정][접근성][Windows 내레이터] 사용자가 진행률 표시줄 컨텍스트를 사용하고 현재 시간이 0:00이면 내레이터에서 "자정 진행"을 읽음
- [버그 수정][스킨] 로고 크기가 JavaScript 코드로 하드 코딩됨
- [접근성][바로 가기 키] 플레이어를 클릭하면 바로 가기 키를 사용하도록 설정되지 않음

### <a name="changes-212"></a>변경된 기능 2.1.2 ####

- [변경][로깅] 플레이어에서 매니페스트를 로드하지 못하면 매니페스트 URL이 기록됨

### <a name="features-212"></a>기능 2.1.2 ###

- [변경][성능][최적화] 플레이어 로드 및 시작 시간이 향상됨

## <a name="211-official-update"></a>2.1.1(공식 업데이트) ##

### <a name="bug-fixes-211"></a>버그 수정 2.1.1 ####

- [버그 수정][iOS] 자동 재생이 false로 설정되면 iOS용 Safari에서 무한 회전자가 발생함
- [버그 수정] 콘텐츠 기간보다 더 긴 시간이 검색되면 무한 회전자가 발생함
- [버그 수정] 바로 가기 키에서 플레이어의 작업 컨텍스트를 가져오려면 여러 키보드 탭이 필요함
- [버그 수정] 특정 자산에서 플레이어 크기를 조정하면 비디오가 몇 초 동안 멈춤
- [버그 수정] 사용자가 여러 번 빠르게 검색하면 무한 회전자가 발생함(검색 완료 후)
- [버그 수정] 비활성 상태의 컨트롤 막대가 숨겨지지 않음
- [버그 수정] AMP를 호스팅하는 웹앱을 열면 웹 페이지가 두 번 로드될 수 있음
- [버그 수정] Flash 기술을 통해 특정 자산의 콘텐츠를 재생하는 동안 무한 반복됨
- [버그 수정] 기타 옵션 메뉴가 타사 플러그 인에서 표시되지 않음
- [버그 수정][스킨|튜브][라이브] 플레이어가 프로그램의 라이브 에지에 있으면 두 개의 라이브 아이콘이 표시됨
- [버그 수정][스킨] 로고를 사용하지 않도록 설정할 수 없음
- [버그 수정][DD+ 콘텐츠] Dolby Digital 오디오 트랙이 포함된 자산에 대한 연속 회전자가 표시됨
- [버그 수정] 라이브 스트림 중에 오디오 언어 트랙이 전환되면 최신 AMP가 멈춤
- [버그 수정] 회전자가 백그라운드에서 사라지는 문제가 해결됨
- [버그 수정] AES Flash 토큰 정적 샘플의 무한 회전자 버그가 수정됨

### <a name="changes-211"></a>변경된 기능 2.1.1 ####

- [변경] Widevine Https 요구 사항에 대한 오류 코드가 추가됨. Chrome v58부터 Widevine 콘텐츠는 `https://` 프로토콜을 통해 로드/재생해야 합니다. 그렇지 않으면 재생이 실패합니다.
- [변경] 콘텐츠를 로드하면 보조 기술에서 "비디오 로드 중"이라고 설명할 수 있도록 회전자 로드에 대한 aria 레이블이 추가됨  

## <a name="210-official-release"></a>2.1.0(공식 릴리스) ##

### <a name="features-210"></a>기능 2.1.0 ###

- [기능][AzureHtml5JS] pre- mid- post-rolls에 대한 VOD 광고가 지원됨
- [기능][베타][AzureHtml5JS] pre- mid- post-rolls에 대한 라이브 광고가 지원됨
- [기능] 새 - AMP-flush 스킨 옵션이 추가됨
- [기능] 화면 읽기 프로그램/보조 기술과의 통합을 향상시키기 위해 향상된 aria 레이블이 추가됨
- [기능][스킨] 스킨에서 이제 모든 아이콘과 단추를 고대비 모드로 명확하게 표시함

### <a name="bug-fixes-210"></a>버그 수정 2.1.0 ###

- [버그 수정] 접근성 및 UI 수가 수정됨
- [버그 수정] IE9에서 AMP가 올바르게 로드되지 않음

### <a name="changes-210"></a>변경된 기능 2.1.0 ###

- [변경] 광고 작업을 수용할 수 있도록 플레이어에서 DOM 요소가 재구성됨
- [변경] 스킨 개발을 위해 CSS에서 SCSS로 전환됨
- [변경][샘플] VOD 광고 샘플이 추가됨
- [변경][샘플] 재생 속도 샘플이 추가됨
- [변경][샘플] 플러시 스킨 샘플이 추가됨

## <a name="200-beta-release"></a>2.0.0(베타 릴리스) ##

- [변경] VJS5로 업데이트됨
- [기능] 플레이어 응답성 유체용 새 유체 API가 추가됨
- [기능] 재생 속도
- [변경] 스킨을 위해 CSS에서 SCSS로 전환됨

## <a name="183-official-hotfix-update"></a>1.8.3(공식 핫픽스 업데이트) ##

### <a name="bug-fixes-183"></a>버그 수정 1.8.3 ###

- [버그 수정][AzureHtml5JS] 음수 DTS를 사용하는 특정 자산이 Chrome에서 재생되지 않음

## <a name="182-official-hotfix-update"></a>1.8.2(공식 핫픽스 업데이트) ##

### <a name="bug-fixes-182"></a>버그 수정 1.8.2 ###

- [버그 수정][AzureHtml5JS] 더 높은 오디오 비트 전송률이 AzureHtml5JS를 통해 재생되지 않음

## <a name="181-official-update"></a>1.8.1(공식 업데이트) ##

### <a name="bug-fixes-181"></a>버그 수정 1.8.1 ###

- [버그 수정][iOS] 네이티브 플레이어에서 캡션/자막이 표시되지 않음
- [버그 수정][AMP] 인증 토큰을 사용하여 추가된 CDN 지원 스트리밍 URL이 재생되지 않음
- [버그 수정][FairPlay] FairPlay 오류 코드에 기술 ID(ErrorCode의 [31-28] 비트)가 누락됨(자세한 내용은 오류 코드 참조)
- [버그 수정][Safari][PlayReady] Safari의 PlayReady 콘텐츠에서 무한 회전자가 발생함

### <a name="changes-181"></a>변경된 기능 1.8.1 ###

- [변경][Html5] VideoTag의 이벤트를 포함하도록 네이티브 Html5 기술 상세 정보 로그가 변경됨

## <a name="180-official-update"></a>1.8.0(공식 업데이트) ##

### <a name="features-180"></a>기능 1.8.0 ###

- [기능][DRM] FairPlay 지원이 추가됨(자세한 내용은 [보호된 콘텐츠](azure-media-player-protected-content.md) 참조)

### <a name="bug-fixes-180"></a>버그 수정 1.8.0 ###

- [버그 수정][AMP] 네트워크가 제한되면 사용자 검색에서 대기 이벤트가 트리거되지 않음
- [버그 수정][FlashSS] Flash 기술에서 품질이 선택되면 예외가 throw됨
- [버그 수정][AMP] 품질이 동적으로 선택되면 상황에 맞는 메뉴에 표시됨
- [버그 수정][스킨] 상황에 맞는 메뉴의 마지막 메뉴 항목을 선택하는 것이 어려움

### <a name="changes-180"></a>변경된 기능 1.8.0 ###

- [변경] 플레이어가 현재 Chrome EME 요구 사항에 맞게 업데이트됨
- [변경] 기본 techOrder가 새 html5FairPlayHLS 기술에 맞게 변경됨(자세한 내용은 [보호된 콘텐츠](azure-media-player-protected-content.md) 참조)
- [변경][AzureHtml5JS] Safari에서 MPEG-Dash 재생을 사용하도록 설정됨
- [변경][샘플] 다중 DRM 샘플이 FairPlay에 맞게 변경됨

## <a name="174-official-hotfix-update"></a>1.7.4(공식 핫픽스 업데이트) ##

### <a name="bug-fixes-174"></a>버그 수정 1.7.4 ###

- [버그 수정][Chrome] 사용자가 플레이어의 컨텍스트를 사용하면 검색 핸들 주위에 파란색 윤곽선이 표시됨
- [버그 수정][IE9] IE9에서 플레이어가 로드되면 JavaScript 예외가 throw됨

## <a name="173-official-hotfix-update"></a>1.7.3(공식 핫픽스 업데이트) ##

### <a name="bug-fixes-173"></a>버그 수정 1.7.3 ###

- [버그 수정][AzureHtml5JS] 제한된 네트워크에서 플레이어 시간이 초과됨

### <a name="changes-173"></a>변경된 기능 1.7.3 ###

- [변경] AES 콘텐츠의 암호를 해독하기 위해 Edge에서 Webcrypto를 사용하도록 설정됨
- [변경] 캐시된 청크를 고려하도록 AMP 추론이 최적화됨
- [변경][AzureHtml5JS] 대역폭 예측 대기 시간을 줄여 추론이 최적화됨

## <a name="172-official-hotfix-update"></a>1.7.2(공식 핫픽스 업데이트) ##

### <a name="features-172"></a>기능 1.7.2 ###
<!---API needs onboarding. Removed link to API until remedied.--->
- [기능][AzureHtml5JS|Firefox] Firefox 47 이상용 EME를 사용하여 Widevine 재생을 사용하도록 설정됨
- [기능] 플레이어 삭제 이벤트가 추가됨
<!-- ([disposing](index.html#static-amp.eventname.disposing)) -->

### <a name="bug-fixes-172"></a>버그 수정 1.7.2 ###

- [버그 수정] 인코딩된 Akamai CDN URL 쿼리 매개 변수가 올바르게 디코딩되지 않음
- [버그 수정] manifestPlayableWindowLength()에서 예외가 throw됨
- [버그 수정] 비디오 다시 보기가 종료되면 뷰어에서 항상 비디오 재생을 클릭할 수는 없음
- [버그 수정] 반응형 크기 조정이 빠른 창 크기 변경에 맞지 않음
- [버그 수정][Edge|IE] 반응형 크기 조정이 width=x, height=auto의 페이지 로드에 적용되지 않음
- [버그 수정][Android|Chrome] 콘텐츠가 암호화되지 않은 경우 Chrome에서 DRM 콘텐츠를 재생할 수 있는 권한을 요청함
- [버그 수정][접근성][Edge] 키보드 컨트롤에서 상황에 맞는 메뉴 항목을 올바르게 선택하지 않음
- [버그 수정][접근성] 고대비 모드에서 표시되는 테두리가 누락됨
- [버그 수정][FlashSS] 플레이어 삭제로 인해 예외가 발생하면 마우스 위로 이벤트 수신기가 제거되지 않음
- [버그 수정][FlashSS] 인코딩된 공백이 있는 매니페스트 URL 구문 분석 문제
- [버그 수정][iOS] tech.featuresVolumeControl을 평가하면 발생하는 형식 오류

### <a name="changes-172"></a>변경된 기능 1.7.2 ###

- [변경][DRM] 콘텐츠가 암호화된 경우에만 확인하도록 원본이 설정되면 DRM 검사로 이동됨
- [변경][AES] 키 전송 요청에서 정의되지 않은 형식/일반 본문이 제거됨
- [변경][접근성] 컨텍스트가 속성 대신 플레이어에 있으면 이제 Windows 내레이터에서 "Media Player"를 읽음

## <a name="171-official-hotfix-update"></a>1.7.1(공식 핫픽스 업데이트) ##

### <a name="features-171"></a>기능 1.7.1 ###

- [기능] 하이브리드 추론 프로필 옵션이 추가됨(이 프로필은 기본적으로 설정됨)

### <a name="bug-fixes-171"></a>버그 수정 1.7.1 ###

- [버그 수정] 반응형 설계가 HTML5 표준(width=100%, height=auto)에 따라 작동하지 않음
- [버그 수정] 너비 및 높이에 대한 백분율 값이 v1.7.0에서 예상대로 작동하지 않음

## <a name="170-official-update"></a>1.7.0(공식 업데이트) ##

### <a name="features-170"></a>기능 1.7.0 ###
<!---API needs onboarding. Removed link until remedied.--->
- [기능][AzureHtml5JS][FlashSS] 현재 시간의 인코더 미디어 시간(초)을 가져오는 currentMediaTime()이 추가됨
- [기능][FlashSS] videoBufferData() 및 audioBufferData()를 사용하여 원격 분석 API 다운로드가 구현됨<!-- (see [BufferData](index.html#amp.bufferdata) for more details) -->
- [기능][FlashSS] 'downloadbitratechanged' 이벤트가 추가됨
- [기능] 이전 버전의 플레이어에 비해 로드 시간이 향상됨
- [기능] 오류가 JavaScript 콘솔에 기록됨

### <a name="bug-fixes-170"></a>버그 수정 1.7.0 ###

- [버그 수정] 쿼리 문자열 매개 변수가 있는 인코딩된 포스터 URL이 플레이어에 표시되지 않음
- [버그 수정] 기술이 로드되지 않고 API amp.Player.poster()가 호출되면 예외가 throw됨
- [버그 수정] 삭제 후 함수에서 플레이어에 액세스하려고 하면 예외가 throw됨
- [버그 수정][접근성] 진행률 표시줄 검색 헤드의 포커스에 대한 윤곽선이 누락됨
- [버그 수정][접근성] 상황에 맞는 메뉴에 고대비 모드의 그림자가 있음
- [버그 수정][iOS] 네이티브 플레이어 WebVTT 캡션 재생이 작동하지 않음
- [버그 수정][AzureHtml5JS] HTTPS 사이트에서 HTTP 스트림을 재생하면 콘텐츠가 혼합되어 무한 회전자가 대신 발생하는 0x0100002 오류가 표시됨
- [버그 수정][AzureHtml5JS] 인식된 무한 버퍼링 상태를 표시하는 루프 상태 검사 오류가 발생하는 끝 세그먼트가 누락됨
- [버그 수정][AzureHtml5JS] useManifestForLabel=false 및 세 문자 언어 코드가 사용되면 메뉴의 오디오 트랙 이름이 잘못됨
- [버그 수정][AzureHtml5JS|Chrome] Chrome에서 JavaScript를 사용하는 기간의 부동 소수점 부정확으로 인해 콘텐츠 끝에서 무한 버퍼 상태가 인식됨
- [버그 수정][FlashSS] Flash Player가 만들어지면 치명적이지 않은 간헐적 오류가 일시적으로 표시됨
- [버그 수정][FlashSS] "조각 URL(...)에서 FLVTags를 생성하지 못했습니다."와 관련된 반올림 부정확 실패로 인해 비디오 및 오디오 스트림에서 다른 시간 단위를 사용하면 재생이 실패함
- [버그 수정][FlashSS] 인코딩된 공백이 있는 매니페스트 URL 구문 분석 문제
- [버그 수정][FlashSS] techOrder에서 다음 기술로 대체하는 대신 재생에서 오류가 발생하는 Flash Player 버전이 11.4 이상인지를 확인하는 검사가 누락됨
- [버그 수정][FlashSS][AES] 밑줄이 있는 AES 토큰을 수락하는 문제
- [버그 수정][SilverlightSS|OSX] 프로토콜(HTTP 또는 HTTPS) 대신 "//" 접두사를 매니페스트에 지정하면 무한 회전자가 발생하는 로컬 파일로 인식됨

### <a name="changes-170"></a>변경된 기능 1.7.0 ###

- [변경][FlashSS] SWF 스크립트("MSAdaptiveStreamingPlugin-osmf2.0.swf" 및 "StrobeMediaPlayback.2.0.swf")가 "StrobeMediaPlayback.2.0.swf"라는 단일 SWF에 병합됨
- [변경][FlashSS] 더 정확한 오류 코드를 가져오도록 오류 코드 전파가 업데이트됨(예: 404는 이제 0x30200000 일반 오류 대신 0x30200194가 됨)

## <a name="163-official-hotfix-update"></a>1.6.3(공식 핫픽스 업데이트) ##

### <a name="bug-fixes-163"></a>버그 수정 1.6.3 ###

- [버그 수정] 플레이어를 삭제한 후 바로 가기 키 이벤트 처리기가 실행되면 JavaScript 런타임 예외가 발생함
- [버그 수정][Android][AzureHtml5JS] 셀룰러 네트워크를 사용하는 모바일 디바이스에서 재생되지 않음
- [버그 수정] UI를 확보하기 위해 웹 작업자로 실행되도록 Forge가 업데이트됨

## <a name="162-official-hotfix-update"></a>1.6.2(공식 핫픽스 업데이트) ##

### <a name="features-162"></a>기능 1.6.2 ###

- [기능] 지역화를 위한 추가 언어가 추가됨(자세한 내용은 설명서 참조)

### <a name="bug-fixes-162"></a>버그 수정 1.6.2 ###

- [버그 수정][IE9-10] window.blur()를 최소화하는 IE9/IE10 버그로 인해 플레이어에서 최소화된 브라우저 창 주위의 영역을 클릭함
- [버그 수정][FlashSS] 밑줄이 있는 AES 토큰을 수락하지 않음

## <a name="161-official-hotfix-update"></a>1.6.1(공식 핫픽스 업데이트) ##

### <a name="bug-fixes-161"></a>버그 수정 1.6.1 ###

- [버그 수정][FlashSS|Edge,IE][SilverlightSS|IE] 다른 입력 UI 요소 또는 다른 IE/Edge UI 요소에서 포커스를 가져올 수 없음
- [버그 수정] Forge가 정의되지 않으면 AES 재생이 실패함
- [버그 수정][Android][AzureHtml5JS|Chrome] 상태 검사 루프에 있으면 연속 회전자에서 콘텐츠가 재생되지 않음
- [버그 수정][IE9] IE 9에서 지원하지 않는 console.log()에서 예외가 발생함

## <a name="160-official-update"></a>1.6.0(공식 업데이트) ##

### <a name="features-160"></a>기능 1.6.0 ###

- [기능] azuremediaplayer.min.js의 크기가 33% 감소됨
- [기능][AzureHtml5JS|Edge][테스트되지 않음] Edge에서 DD+ 오디오 스트림이 지원됨(초기 선택 후에는 코덱이 전환되지 않음). 지금 앱에서 올바른 오디오 스트림을 선택해야 합니다.
- [기능] 바로가기 키 컨트롤(자세한 내용은 문서 참조)
- [기능] 정확한 검색 시간을 가리키는 진행률 시간 팁
- [기능] setupDone 메서드가 플러그 인에 있으면 비동기 플러그 인 검색을 허용함

### <a name="bug-fixes-160"></a>버그 수정 1.6.0 ###

- [버그 수정] getMemoryLog(true)에서 메모리 로그가 플러시되지 않음
- [버그 수정] 마우스 이동 시 비트 전송률 선택 상자가 다시 설정되어 마우스 제어를 통해 더 낮은 비트 전송률을 선택하는 문제가 발생함
- [버그 수정] DRM 검사를 수행하면 앱에서 Mac Office 작동이 중단됨
- [버그 수정] 실수로 CSS 클래스를 쉽게 덮어씀
- [버그 수정][Chrome] 사용자 에이전트 문자열 브라우저의 업데이트 ID가 Edge임
- [버그 수정][AzureHtml5JS] 캡션 단추가 Edge(Win10) 또는 Chrome(Mac)의 도구 모음에 표시되지 않음
- [버그 수정][Android][AzureHtml5JS|Chrome] 짧은 비디오에 대한 endOfStream() 호출에서 InvalidStateError 예외가 발생함
- [버그 수정][Firefox] 브라우저 기능을 확인하면 Firefox에서 발생하는 DRM 경고가 제거됨
- [버그 수정][Html5] 자막/캡션이 프로그레시브 mp4 콘텐츠와 함께 표시되지 않음
- [버그 수정][FlashSS] 일치하는 타임스탬프가 있는 메시지가 반대 순서로 기록됨
- [버그 수정][접근성][Chrome|Firefox] 탭 및 선택 컨트롤에서 첫 번째 메뉴 항목이 자동으로 선택됨
- [버그 수정][접근성] 볼륨 단추를 제어하는 탭

### <a name="changes-160"></a>변경된 기능 1.6.0 ###

- [변경] 품질 수준 선택에서 AES 암호 해독 시간이 사용됨
- [변경] HLS v3 이전에 HLS v4를 다중 오디오 스트림에 사용하도록 URL 재작성기가 업데이트됨
- [변경] nativeControlsForTouch가 기본적으로 false로 설정됨(제대로 작동하려면 false여야 함)

## <a name="150-official-update"></a>1.5.0(공식 업데이트) ##

### <a name="features-150"></a>기능 1.5.0 ###

- [기능] 일반 웹 보안이 향상됨(주입, XSS 등의 방지)
- [기능] sourceset 이벤트 및 options.sdn에 대한 SDN 플러그 인 통합 후크
- [기능] 재생 중 5XX 및 4XX 오류에 대한 견고성 처리

### <a name="bug-fixes-150"></a>버그 수정 1.5.0 ###

- [버그 수정] 유니코드 대신 HTML 엔터티 글꼴 코드를 단추에 사용하도록 CSS 축소가 업데이트됨
- [버그 수정][AzureHtml5JS] 항상 protectionInfo에서 첫 번째 요소의 토큰을 선택하는 다중 DRM 콘텐츠에서 두 번째 DRM이 실패함
- [버그 수정][AzureHtml5JS] 세그먼트가 누락된 영역에서 검색하면 검색이 완료되지 않음
- [버그 수정][AzureHtml5JS|Edge] PlayReady 재생에 대한 Edge 업데이트에서 접두사가 있는 EME를 사용하도록 설정됨
- [버그 수정][AzureHtml5JS|Firefox] 보호된 콘텐츠를 위해 Firefox v42 이상(MSE 포함)을 Silverlight로 대체할 수 있도록 EME 검사가 업데이트됨
- [버그 수정][FlashSS] error.message가 숫자에서 자세한 문자열로 업데이트됨

### <a name="changes-150"></a>변경된 기능 1.5.0 ###

- [변경] 포스터가 현재 절대 URL로만 작동함

## <a name="140-official-update"></a>1.4.0(공식 업데이트) ##

### <a name="features-140"></a>기능 1.4.0 ###

- [기능][AzureHtml5JS|Chrome] 간단한 Widevine DRM 지원
- [기능][AzureHtml5JS] 재생 중 404/412 오류에 대한 견고성 처리

### <a name="bug-fixes-140"></a>버그 수정 1.4.0 ###

- [버그 수정][FlashSS] 매개 변수 유효성 검사가 향상됨

## <a name="130-official-update"></a>1.3.0(공식 업데이트) ##

### <a name="features-130"></a>기능 1.3.0 ###

- [기능][AzureHtml5JS][FlashSS] 동일한 코덱 다중 오디오 콘텐츠의 오디오 전환

### <a name="bug-fixes-130"></a>버그 수정 1.3.0 ###

- [버그 수정][AzureHtml5JS|Chrome] 일시적인 무한 회전자
- [버그 수정][AzureHtml5JS|IE][Windows Phone] Windows Phone에서 재생 문제가 발생하는 예외
- [버그 수정][FlashSS] 추가 인스턴스에 대한 자동 재생이 false로 설정되지 않음
- [버그 수정] UI 메뉴 크기 조정 문제

## <a name="120-official-update"></a>1.2.0(공식 업데이트) ##

### <a name="features-120"></a>기능 1.2.0 ###

- [기능][AzureHtml5JS|Firefox] MSE를 사용하도록 설정되면 지원됨
- [기능] 앱에서 대체 기술 이진 파일(swf, xap)에 대한 경로를 더 이상 제공할 필요가 없습니다. 경로는 Azure Media Player 스크립트를 기준으로 하는 상대 경로입니다.

### <a name="bug-fixes-120"></a>버그 수정 1.2.0 ###

- [버그 수정][AzureHtml5JS|Chrome] 플레이어가 백그라운드로 있으면 라이브 에지 뒤로 이동함
- [버그 수정][AzureHtml5JS|Edge] 전체 화면이 작동하지 않음
- [버그 수정][AzureHtml5JS] 옵션에 설정되면 로깅을 사용하도록 올바르게 설정되지 않음
- [버그 수정][Flash] 대기 이벤트 중에 "버퍼링" 및 버퍼링 아이콘이 모두 표시됨
- [버그 수정] 초기 대역폭 요청이 실패하더라도 재생을 계속하도록 허용됨
- [버그 수정] 정의되지 않은 옵션을 사용하여 초기화하면 플레이어가 로드되지 않음
- [버그 수정] 이미 삭제된 플레이어를 삭제하려고 하면 vdata 예외가 발생함
- [버그 수정] 품질 막대 아이콘이 잘못 매핑됨

## <a name="111-official-hotfix-update"></a>1.1.1(공식 핫픽스 업데이트) ##

### <a name="bug-fixes-111"></a>버그 수정 1.1.1 ###

- [버그 수정] 이전의 IE 전체 화면 문제
- [버그 수정] 플러그 인을 더 이상 덮어쓰지 않음

## <a name="110-official-update"></a>1.1.0(공식 업데이트) ##

### <a name="features-110"></a>기능 1.1.0 ###

- [기능] UI 지역화 문자열 업데이트

### <a name="bug-fixes-110"></a>버그 수정 1.1.0 ###

- [버그 수정] 큰 재생 단추의 대비가 충분하지 않음
- [버그 수정] 시각적 탭 포커스 표시기
- [버그 수정] 이제 올바른 해상도 정보를 사용하여 비트 전송률 메뉴가 선택됨
- [버그 수정] 이제 기타 옵션 메뉴의 크기가 동적으로 조정됨
- [버그 수정] 다양한 UI 문제

## <a name="100-official-release"></a>1.0.0(공식 릴리스) ##

### <a name="features-100"></a>기능 1.0.0 ###

- [기능] 탭 컨트롤, 포커스 컨트롤, 화면 읽기 프로그램, 고대비 UI에 대한 기본 접근성 테스트
- [기능] UI가 업데이트됨
- [기능] 개발 로그가 기록됨
- [기능] 캡션/자막 트랙을 동적으로 설정하는 API
- [기능] 기본 지역화 기능
- [기능] 기술 간 오류 코드 통합
- [기능] Flash 또는 Silverlight와 같은 플러그 인이 설치되지 않은 경우에 대한 새 오류 코드
- [기능][AzureHtml5JS] 기본 진단 이벤트가 구현됨

### <a name="bug-fixes-100"></a>버그 수정 1.0.0 ###
<!---What is that actually supposed to say?--->
- [버그 수정][AzureHtml5JS] 사소한 부정확이 타임스탬프에 있으면 MPD 업데이트에서 라이브 재생이 멈춤
- [버그 수정][AzureHtml5JS] 몇 가지 라이브 재생 문제가 완화됨
- [버그 수정][AzureHtml5JS] 창 크기 추론이 설정되고 더 높은 해상도 화면으로 이동하면 버퍼가 플러시됨
- [버그 수정][AzureHtml5JS] Chrome에서 이제 종료된 이벤트를 올바르게 표시합니다. 이전에 *AzureHtml5JS를 사용할 때 Chrome에서 종료된 이벤트를 제대로 보내지 않습니다. 기본 브라우저에 문제가 있습니다.* 라고 알려진 문제와 연결되어 있습니다.
- [버그 수정][AzureHtml5JS] Safari를 이 기술에 사용하지 않도록 설정됩니다. 이는 *AzureHtml5JS 기술을 사용하는 OSX Yosemite와 관련된 재생 문제입니다. MSE 구현 문제가 있습니다. 임시 완화: 이러한 사용자 에이전트에 대한 기술 순서로 flashSS, silverlightSS 강제 적용*
- [버그 수정][FlashSS] 오류가 발생하면 loadstart가 실행됨

## <a name="020-beta"></a>0.2.0(베타) ##

### <a name="features-020"></a>기능 0.2.0 ###

- [기능] 주문형 및 라이브에 대한 PlayReady 및 AES 테스트가 완료됨('호환성 매트릭스' 참조)
- [기능] 불연속성 처리
- [기능] 2^53보다 큰 타임스탬프 지원
- [기능] URL 쿼리 매개 변수가 매니페스트 요청에 유지됨
- [기능][테스트되지 않음] `QuickStart` 및 `HighQuality` 추론 프로필 지원
- [기능][테스트되지 않음] AzureHtml5JS 및 FlashSS에서 비트 전송률, 너비 및 높이에 대한 비디오 스트림 정보 공개
- [기능][테스트되지 않음] AzureHtml5JS 및 FlashSS에서 비트 전송률 선택(API 설명서 참조)

### <a name="bug-fixes-020"></a>버그 수정 0.2.0 ###

- [버그 수정] 큰 재생 단추가 이제 WP8.1에 표시됨
- [버그 수정] 여러 라이브 재생 문제가 해결됨
- [버그 수정] 음소거 해제 단추가 이제 UI에서 작동함
- [버그 수정] UI 로드 환경이 자동 재생 모드로 업데이트됨
- [버그 수정] AMD 로더 문제 및 메서드 충돌 정의
- [버그 수정] WP 8.1 Cordova 앱 로드 문제
- [버그 수정] 보호된 콘텐츠에서 플랫폼/기술 지원 ProtectionType을 쿼리하여 재생에 적합한 기술을 선택합니다.  이전에 '_Chrome(데스크톱)/Safari 8(OSX Yosemite)의 PlayReady 콘텐츠가 현재 Silverlight 플레이어로 대체되지 않습니다._ '라고 알려진 문제가 해결되었습니다.
- [버그 수정] 해당 머신에 기본적으로 설치되지 않은 미디어 파운데이션으로 인해 WinServer 2012 R2에서 catch되지 않은 예외가 발생합니다.  구현되지 않은 HTML 비디오 태그 API를 사용하려고 하면 오류가 throw됩니다. 현재 완화 방법은 해당 오류를 catch하여 오류를 throw하는 대신 false를 반환하는 것입니다.
- [버그 수정] 재생 중에 결함을 방지하기 위해 검색 또는 HTTP 실패 후에 항상 초기화 세그먼트를 가져옵니다.
- [버그 수정] 오류가 발생한 경우 시뮬레이션된 진행률 및 시간 업데이트 추적을 해제합니다.
- [버그 수정] 마우스 오른쪽 단추 클릭 메뉴가 제거됨
- [버그 수정][AzureHtml5JS] 잘못된 토큰이 PlayReady 콘텐츠에 설정되면 오류 메시지가 표시되지 않음
- [버그 수정][AzureHtml5JS] 라이브 재생 중에 전체 화면으로 이동하면 창 크기 추론이 고려되지 않음
- [버그 수정][FlashSS] Azure Media Player 메시지만 표시되도록 Strobe Media Player에서 표시하는 메시지가 제거됨
- [버그 수정][SilverlightSS] 기간을 초과하거나 0보다 작다고 검색되면 '검색됨' 이벤트를 가져오지 않음

## <a name="010-beta-release"></a>0.1.0(베타 릴리스) ##

초기 시험판

## <a name="next-steps"></a>다음 단계 ##

- [Azure Media Player 빠른 시작](azure-media-player-quickstart.md)
