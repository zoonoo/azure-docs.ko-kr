---
title: "포털을 통해 Azure Event Hubs를 사용하도록 설정 | Microsoft Docs"
description: "Azure Portal을 사용하여 Event Hubs 캡처 기능을 사용하도록 설정합니다."
services: event-hubs
documentationcenter: 
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/28/2017
ms.author: sethm
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: 1f3d373944b909db290f6cf2da7bf12a8a00e1c5
ms.contentlocale: ko-kr
ms.lasthandoff: 06/28/2017


---

# Azure Portal을 사용하여 Event Hubs 캡처를 사용하도록 설정
<a id="enable-event-hubs-capture-using-the-azure-portal" class="xliff"></a>

이벤트 허브 생성 시 [Azure Portal](https://portal.azure.com)을 사용하여 캡처를 구성할 수 있습니다. **이벤트 허브 만들기** 포털 블레이드에서 **켜기** 단추를 클릭하여 캡처를 사용하도록 설정합니다. 그런 다음 블레이드의 **컨테이너** 섹션을 클릭하여 Storage 계정 및 컨테이너를 구성합니다. Event Hubs 캡처는 저장소에서 서비스 간 인증을 사용하므로 저장소 연결 문자열을 지정할 필요가 없습니다. 리소스 선택기가 저장소 계정에 대한 리소스 URI를 자동으로 선택합니다. Azure Resource Manager를 사용하는 경우 이 URI를 문자열로 명백히 제공해야 합니다.

기본 시간은 5분입니다. 최소값은 1, 최대값은 15입니다. **크기**의 범위는 10-500MB입니다.

![][1]

## 기존 이벤트 허브에 캡처 추가
<a id="adding-capture-to-an-existing-event-hub" class="xliff"></a>

캡처는 Event Hubs 네임스페이스에 있는 기존 이벤트 허브에 구성할 수 있습니다. 이 기능은 이전 **메시지** 또는 **혼합** 형식 네임스페이스에 사용할 수 없습니다. 기존 이벤트 허브에서 캡처를 사용하거나 캡처 설정을 변경하려면 **필수** 블레이드를 로드할 네임스페이스를 클릭한 다음 캡처 설정을 사용하거나 변경할 이벤트 허브를 클릭합니다. 마지막으로 다음 그림과 같이 열린 블레이드의 **속성** 섹션을 클릭합니다.

![][2]

[1]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture1.png
[2]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture2.png

## 다음 단계
<a id="next-steps" class="xliff"></a>

Azure Resource Manager 템플릿을 사용하여 Event Hubs 캡처를 구성할 수도 있습니다. 자세한 내용은 [Azure Resource Manager 템플릿을 사용하여 캡처를 사용하도록 설정](event-hubs-resource-manager-namespace-event-hub-enable-capture.md)을 참조하세요.

