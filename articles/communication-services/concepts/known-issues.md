---
title: Azure Communication Services-FAQ/알려진 문제
description: Azure Communication Services에 대 한 자세한 정보
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: troubleshooting
ms.service: azure-communication-services
ms.openlocfilehash: 2c6ac34d8daf00578cb1d03833a28eb8535708b7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103493648"
---
# <a name="faq--known-issues"></a>FAQ/알려진 문제
이 문서에서는 Azure Communication Services와 관련 된 알려진 문제 및 질문과 대답에 대 한 정보를 제공 합니다.

## <a name="faq"></a>FAQ

### <a name="why-is-the-quality-of-my-video-degraded"></a>비디오 품질이 저하 되는 이유는 무엇 인가요?

비디오 스트림의 품질은 해당 스트림을 시작 하는 데 사용 된 클라이언트 쪽 렌더러의 크기에 따라 결정 됩니다. 원격 스트림을 구독할 때 수신자는 발신자의 클라이언트 쪽 렌더러 차원을 기반으로 자체 해결 방법을 결정 합니다.

### <a name="why-is-it-not-possible-to-enumerateselect-micspeaker-devices-on-safari"></a>Safari에서 mic/스피커 장치를 열거/선택할 수 없는 이유는 무엇입니까?

응용 프로그램은 Safari iOS/iPad에서 mic/스피커 장치 (예: bluetooth)를 열거/선택할 수 없습니다. OS의 제한 사항입니다. 항상 하나의 장치만 있습니다.

MacOS에서 Safari의 경우-앱은 통신 서비스 장치 관리자 통해 스피커를 열거/선택할 수 없습니다 .이는 OS를 통해 선택 해야 합니다. MacOS에서 Chrome을 사용 하는 경우 앱은 통신 서비스 장치 관리자 통해 장치를 열거 하거나 선택할 수 있습니다.

## <a name="known-issues"></a>알려진 문제

이 섹션에서는 Azure Communication Services와 관련 된 알려진 문제에 대 한 정보를 제공 합니다.

### <a name="repeatedly-switching-video-devices-may-cause-video-streaming-to-temporarily-stop"></a>비디오 장치를 반복적으로 전환 하면 비디오 스트리밍이 일시적으로 중지 될 수 있습니다.

비디오 장치 간을 전환 하면 선택한 장치에서 스트림을 획득 하는 동안 비디오 스트림이 일시 중지 될 수 있습니다.

#### <a name="possible-causes"></a>가능한 원인
미디어 장치 간 스트리밍 및 전환은 계산 집약적입니다. 자주 전환 하면 성능이 저하 될 수 있습니다. 개발자는 다른 장치 스트림을 시작 하기 전에 중지 하는 것이 좋습니다.
