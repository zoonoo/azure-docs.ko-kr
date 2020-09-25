---
title: Azure Communication Services 통화 클라이언트 라이브러리 개요
titleSuffix: An Azure Communication Services concept document
description: 통화 클라이언트 라이브러리에 대한 개요를 제공합니다.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 9cc00cfa9f44c69a5880d53c0b7ac623f60be16b
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90945765"
---
# <a name="calling-client-library-overview"></a>통화 클라이언트 라이브러리 개요

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]

*클라이언트* 및 *서비스*에 대한 두 가지 별도의 통화 클라이언트 라이브러리 제품군이 있습니다. 현재 사용할 수 있는 클라이언트 라이브러리는 최종 사용자 환경(웹 사이트 및 네이티브 앱)을 위한 것입니다.

서비스 클라이언트 라이브러리는 아직 사용할 수 없으며, 봇 및 기타 서비스와의 통합에 적합한 원시 음성 및 비디오 데이터 평면에 대한 액세스를 제공합니다.

## <a name="calling-client-library-capabilities"></a>통화 클라이언트 라이브러리 기능

다음 목록에서는 현재 Azure Communication Services 통화 클라이언트 라이브러리에서 사용할 수 있는 기능 세트를 보여 줍니다.

| 기능 그룹 | 기능                                                                                                          | JS  | Java(Android) | Objective-C(iOS) 
| ----------------- | ------------------------------------------------------------------------------------------------------------------- | ---  | -------------- | -------------
| 핵심 기능 | 두 사용자 간에 일 대 일 호출 배치                                                                           | ✔️   | ✔️            | ✔️  
|                   | 세 명 이상의 사용자를 포함하는 그룹 통화(최대 350 사용자) 배치                                                       | ✔️   | ✔️            | ✔️ 
|                   | 세 명 이상의 사용자가 있는 그룹 호출로 일 대 일 호출 승격                                 | ✔️   | ✔️            | ✔️ 
|                   | 시작된 후 그룹 호출 조인                                                                              | ✔️   | ✔️            | ✔️ 
|                   | 진행 중인 그룹 호출에 참가하기 위해 다른 VoIP 참가자 초대                                                       | ✔️   | ✔️            | ✔️ 
|                   | 마이크 음소거/음소거 해제                                                                                                     | ✔️   | ✔️            | ✔️         
|                   | 카메라 간 전환                                                                                              | ✔️   | ✔️            | ✔️           
|                   | 로컬 보류/유지 취소                                                                                                  | ✔️   | ✔️            | ✔️           
|                   | 활성 스피커                                                                                                      | ✔️   | ✔️            | ✔️           
|                   | 호출용 스피커 선택                                                                                            | ✔️   | ✔️            | ✔️           
|                   | 통화에 대한 마이크 선택                                                                                         | ✔️   | ✔️            | ✔️           
|                   | 참가자의 상태 표시<br/>*유휴, 초기 미디어, 연결, 연결됨, 보류 중, 로비 내, 연결 끊김*         | ✔️   | ✔️            | ✔️           
|                   | 호출 상태 표시<br/>*초기 미디어, 수신, 연결 중, 울림, 연결됨, 보류 중, 연결 끊기, 연결 끊어짐* | ✔️   | ✔️            | ✔️           
|                   | 참가자가 음소거되어 있는지 여부 표시                                                                                      | ✔️   | ✔️            | ✔️           
|                   | 참가자가 전화를 끊은 이유 표시                                                                       | ✔️   | ✔️            | ✔️     
| 화면 공유    | 애플리케이션 내에서 전체 화면 공유                                                                 | ✔️   | ❌            | ❌           
|                   | 특정 애플리케이션 공유(실행 중인 애플리케이션 목록에서)                                                | ✔️   | ❌            | ❌           
|                   | 열려 있는 탭 목록에서 웹 브라우저 탭 공유                                                                  | ✔️   | ❌            | ❌           
|                   | 참가자가 원격 화면 공유를 볼 수 있음                                                                            | ✔️   | ✔️            | ✔️         
| 명단            | 참가자 리스트                                                                                                   | ✔️   | ✔️            | ✔️           
|                   | 참가자 제거                                                                                                | ✔️   | ✔️            | ✔️         
| PSTN              | PSTN 참가자를 사용하여 일 대 일 호출 배치                                                                     | ✔️   | ✔️            | ✔️   
|                   | PSTN 참가자를 사용하여 그룹 호출 배치                                                                           | ✔️   | ✔️            | ✔️
|                   | PSTN 참가자를 사용하여 일 대 일 호출을 그룹 호출로 승격                                                 | ✔️   | ✔️            | ✔️
|                   | 그룹 호출에서 PSTN 참가자로 전화 걸기                                                                    | ✔️   | ✔️            | ✔️   
| 일반           | 최대 350 참가자를 포함하는 그룹 호출 참여                                                       |  ✔️  | ✔️            | ✔️    
|                   | 오디오 테스트 서비스를 사용하여 마이크, 스피커 및 카메라 테스트(8:echo123을 호출하여 사용 가능)                   |  ✔️  | ✔️            | ✔️   

## <a name="calling-client-library-browser-support"></a>통화 클라이언트 라이브러리 브라우저 지원

다음 표에서는 현재 사용할 수 있는 지원되는 브라우저 및 버전의 세트를 나타냅니다.

|                                  | Windows          | macOS          | Ubuntu | Linux  | Android | iOS    |
| -------------------------------- | ---------------- | -------------- | ------- | ------ | ------ | ------ |
| **통화 클라이언트 라이브러리** | Chrome*, 새 Edge | Chrome *, Safari** | Chrome*  | Chrome* | Chrome* | Safari** |


*이전 두 릴리스 외에도 최신 버전의 Chrome이 지원됩니다.<br/>

**Safari 버전 13.1+가 지원됩니다. Safari macOS에 대한 보내는 비디오는 아직 지원되지 않지만 iOS에서 지원됩니다. 보내는 화면 공유는 데스크톱 iOS에서만 지원됩니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [통화 시작](../../quickstarts/voice-video-calling/getting-started-with-calling.md)

자세한 내용은 다음 항목을 참조하세요.
- 일반적인 [통화 흐름](../call-flows.md) 숙지
- [통화 형식](../voice-video-calling/about-call-types.md)에 대한 자세한 정보
- [PSTN 솔루션 계획](../telephony-sms/plan-solution.md)
