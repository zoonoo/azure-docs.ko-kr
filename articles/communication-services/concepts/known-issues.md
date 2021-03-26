---
title: Azure Communication Services-알려진 문제
description: Azure Communication Services에 대 한 자세한 정보
author: rinarish
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: troubleshooting
ms.service: azure-communication-services
ms.openlocfilehash: aa5530dd279e8b45382fe6841b6f193a652c0ba3
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105566798"
---
# <a name="known-issues-azure-communication-services-client-libraries"></a>알려진 문제: Azure Communication Services 클라이언트 라이브러리
이 문서에서는 Azure Communication Services 클라이언트 라이브러리와 관련 된 제한 사항 및 알려진 문제에 대 한 정보를 제공 합니다.

> [!IMPORTANT]
> 호출 환경의 품질에 영향을 줄 수 있는 여러 요인이 있습니다. 통신 서비스 네트워크 구성 및 테스트 모범 사례에 대 한 자세한 내용은 **[네트워크 요구 사항](https://docs.microsoft.com/azure/communication-services/concepts/voice-video-calling/network-requirements)** 설명서를 참조 하세요.


## <a name="javascript-client-library"></a>JavaScript 클라이언트 라이브러리

이 섹션에서는 Azure Communication Services의 클라이언트 라이브러리를 호출 하는 JavaScript 음성 및 비디오와 관련 된 알려진 문제에 대 한 정보를 제공 합니다.

### <a name="after-refreshing-the-page-user-is-not-removed-from-the-call-immediately"></a>페이지를 새로 고친 후에는 사용자가 호출에서 즉시 제거 되지 않습니다. 
사용자가 호출에 있고 페이지를 새로 고치도록 결정 한 경우 Communication Services 클라이언트 라이브러리에서 통신 서비스 미디어 서비스에 연결을 끊을 수 없습니다. 통신 서비스 미디어 서비스는 이러한 사용자를 호출에서 즉시 제거 하지 않지만, 사용자가 네트워크 연결 문제를 가정할 때 다시 참여할 때까지 대기 합니다. 미디어 서비스의 시간이 초과 되 면 사용자가 호출에서 제거 됩니다.

호출에 참여 하는 동안 최종 사용자가 응용 프로그램 페이지를 새로 고치지 않아도 되는 환경을 빌드하는 것이 좋습니다. 사용자가 페이지를 새로 고치면 앱에 대해이를 처리 하는 가장 좋은 방법은 새로 고친 후 응용 프로그램으로 다시 반환 된 후 사용자에 대해 동일한 통신 서비스 사용자 ID를 다시 사용 하는 것입니다.

호출에서 다른 참가자의 관점에서는 미리 정의 된 시간 (1-2 분) 동안 해당 사용자가 호출에 남아 있습니다. 사용자가 동일한 통신 서비스 사용자 ID로 다시 연결 하는 경우 컬렉션에 동일한 기존 개체로 표시 됩니다 `remoteParticipants` .
이전에 사용자가 비디오를 전송 하 `videoStreams` 는 경우 컬렉션은 서비스가 시간 초과 되어 제거 될 때까지 이전 스트림 정보를 유지 합니다 .이 시나리오에서는 응용 프로그램에서 컬렉션에 추가 된 새 스트림을 관찰 하 고 가장 높은 항목을 렌더링 하도록 결정할 수 있습니다 `id` . 


### <a name="its-not-possible-to-render-multiple-previews-from-multiple-devices-on-web"></a>웹의 여러 장치에서 여러 미리 보기를 렌더링 하는 것은 불가능 합니다.
이 문제는 알려진 제한 사항입니다. 자세한 내용은 [호출 클라이언트 라이브러리 개요](https://docs.microsoft.com/azure/communication-services/concepts/voice-video-calling/calling-sdk-features) 를 참조 하세요.

### <a name="enumeration-of-the-microphone-and-speaker-devices-is-not-possible-in-safari-when-the-application-runs-on-ios-or-ipados"></a>응용 프로그램이 iOS 또는 iPadOS에서 실행 되는 경우 Safari에서 마이크 및 스피커 장치를 열거할 수 없습니다. 
응용 프로그램은 Safari iOS/iPad에서 mic/스피커 장치 (예: Bluetooth)를 열거/선택할 수 없습니다. 이것은 알려진 운영 체제 제한 사항입니다.

MacOS에서 Safari를 사용 하는 경우 앱은 통신 서비스 장치 관리자 통해 스피커를 열거/선택할 수 없습니다. 이 시나리오에서는 OS를 통해 장치를 선택 해야 합니다. MacOS에서 Chrome을 사용 하는 경우 앱은 통신 서비스 장치 관리자 통해 장치를 열거 하거나 선택할 수 있습니다.

### <a name="audio-connectivity-is-lost-when-receiving-sms-messages-or-calls-during-an-ongoing-voip-call"></a>진행 중인 VoIP 통화 중에 SMS 메시지 또는 통화를 받을 때 오디오 연결이 끊어졌습니다.
모바일 브라우저는 백그라운드 상태에서 연결을 유지 하지 않습니다. 이로 인해 VoIP 호출이 텍스트 메시지 또는 응용 프로그램을 백그라운드로 푸시하는 들어오는 PSTN 호출에 의해 중단 된 경우 성능 저하가 발생할 수 있습니다.

<br/>클라이언트 라이브러리: 호출 (JavaScript)
<br/>브라우저: Safari, Chrome
<br/>운영 체제: iOS, Android

### <a name="repeatedly-switching-video-devices-may-cause-video-streaming-to-temporarily-stop"></a>비디오 장치를 반복적으로 전환 하면 비디오 스트리밍이 일시적으로 중지 될 수 있습니다.

비디오 장치 간을 전환 하면 선택한 장치에서 스트림을 획득 하는 동안 비디오 스트림이 일시 중지 될 수 있습니다.

#### <a name="possible-causes"></a>가능한 원인
미디어 장치 간 스트리밍 및 전환은 계산 집약적입니다. 자주 전환 하면 성능이 저하 될 수 있습니다. 개발자는 다른 장치 스트림을 시작 하기 전에 중지 하는 것이 좋습니다.

### <a name="bluetooth-headset-microphone-is-not-detected-therefore-is-not-audible-during-the-call-on-safari-on-ios"></a>Bluetooth 헤드셋 마이크가 검색 되지 않으므로 iOS에서 Safari를 호출 하는 동안에는 소리가 발생 하지 않습니다.
Bluetooth 헤드셋은 iOS의 Safari에서 지원 되지 않습니다. Bluetooth 장치는 사용 가능한 마이크 옵션에 나열 되지 않으며 Safari를 통해 Bluetooth를 사용 하는 경우 다른 참가자가 사용자를 듣지 못합니다.

#### <a name="possible-causes"></a>가능한 원인
알려진 macOS/iOS/iPadOS 운영 체제 제한 사항입니다. 

**Macos** 및 **iOS/iPadOS** 에서 safari를 사용 하는 경우 safari에서 스피커 열거/선택이 지원 되지 않으므로 통신 서비스 장치 관리자 통해 스피커 장치를 열거 하거나 선택할 수 없습니다. 이 시나리오에서는 운영 체제를 통해 장치 선택을 업데이트 해야 합니다.

### <a name="rotation-of-a-device-can-create-poor-video-quality"></a>장치의 회전이 비디오 품질이 떨어질 수 있습니다.
사용자가 장치를 회전할 때 비디오 품질이 저하 될 수 있습니다.

<br/>영향을 받는 장치: Google Pixel 5, Google Pixel 3a, Apple iPad 8 및 Apple iPad X
<br/>클라이언트 라이브러리: 호출 (JavaScript)
<br/>브라우저: Safari, Chrome
<br/>운영 체제: iOS, Android


### <a name="camera-switching-makes-the-screen-freeze"></a>카메라 전환으로 인해 화면이 동결 됩니다. 
통신 서비스 사용자가 JavaScript 호출 클라이언트 라이브러리를 사용 하 여 호출을 조인한 후 카메라 스위치 단추를 누르면 응용 프로그램을 새로 고치거 나 브라우저가 사용자에 의해 백그라운드로 푸시 될 때까지 UI가 완전히 응답 하지 않을 수 있습니다.

<br/>영향을 받는 장치: Google 픽셀 4a
<br/>클라이언트 라이브러리: 호출 (JavaScript)
<br/>브라우저: Chrome
<br/>운영 체제: iOS, Android


#### <a name="possible-causes"></a>가능한 원인
확인 중입니다.

### <a name="if-the-video-signal-was-stopped-while-the-call-is-in-connecting-state-the-video-will-not-be-sent-after-the-call-started"></a>호출이 "연결 중" 상태인 동안 비디오 신호가 중지 된 경우 통화가 시작 된 후 비디오가 전송 되지 않습니다. 
호출이 상태에 있는 동안 사용자가 신속 하 게 비디오를 설정/해제 하기로 결정 한 경우이로 인해 `Connecting` 호출에 대해 얻은 스트림과 관련 된 문제가 발생할 수 있습니다. 호출이 상태에 있는 동안 비디오를 설정/해제 하지 않아도 되는 방식으로 개발자가 앱을 빌드하는 것이 좋습니다 `Connecting` . 이 문제로 인해 다음과 같은 시나리오에서 비디오 성능이 저하 될 수 있습니다.

 - 사용자가 오디오로 시작한 다음 호출이 상태 이면 비디오를 시작 하 고 중지 `Connecting` 합니다.
 - 사용자가 오디오로 시작한 다음 호출이 상태 이면 비디오를 시작 하 고 중지 `Lobby` 합니다.


#### <a name="possible-causes"></a>가능한 원인
확인 중입니다.

###  <a name="sometimes-it-takes-a-long-time-to-render-remote-participant-videos"></a>원격 참가자 비디오를 렌더링 하는 데 시간이 오래 걸리는 경우가 있습니다.
진행 중인 그룹을 호출 하는 동안 _사용자 A_ 가 비디오를 보내고 _사용자 B가_ 통화를 조인 합니다. 사용자 B에 게 사용자 A의 비디오가 표시 되지 않거나 사용자 A의 비디오가 긴 지연 후 렌더링을 시작 하는 경우가 있습니다. 이는 추가 구성이 필요한 네트워크 환경에 의해 발생할 수 있습니다. 네트워크 구성 지침은 [네트워크 요구 사항](https://docs.microsoft.com/azure/communication-services/concepts/voice-video-calling/network-requirements) 설명서를 참조 하세요.
