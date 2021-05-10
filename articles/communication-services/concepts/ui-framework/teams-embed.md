---
title: UI 프레임워크 Teams 포함
titleSuffix: An Azure Communication Services quickstart
description: 이 문서에서는 Azure Communication Services UI 프레임워크 Teams 포함 기능을 사용하여 턴키 통화 환경을 빌드하는 방법을 알아봅니다.
author: tophpalmer
ms.author: chpalm
ms.date: 11/16/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 8301f81b21db50814df1bc764cc1fae38b4f14de
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107307642"
---
# <a name="teams-embed"></a>Teams 포함

[!INCLUDE [Public Preview Notice](../../includes/private-preview-include.md)]


Teams 포함은 일반적인 B2C(business-to-customer) 및 B2B(business-to-business) 통화 상호 작용에 초점을 맞춘 Azure Communication Services 기능입니다. Teams 포함 시스템의 핵심은 [화상 및 음성 통화](../voice-video-calling/calling-sdk-features.md)이지만, Teams 포함 시스템은 Microsoft Teams 모임 기반의 전체 사용자 환경을 제공하기 위해 Azure의 통화 기본 요소를 기반으로 하여 빌드됩니다.

Teams 포함 SDK는 폐쇄형 소스이며, 이러한 기능을 턴키 방식의 복합 형식으로 사용할 수 있습니다. Teams 포함을 앱의 캔버스에 놓으면 SDK에서 전체 사용자 환경을 생성합니다. 이 사용자 환경은 Microsoft Teams 모임과 매우 비슷하므로 다음을 활용할 수 있습니다.

- 개발 시간 및 엔지니어링 복잡성 감소
- 최종 사용자의 Teams에 대한 지식
- [Teams 최종 사용자 학습 콘텐츠](https://support.microsoft.com/office/meetings-in-teams-e0b0ae21-53ee-4462-a50d-ca9b9e217b67)를 다시 사용하는 기능

Teams 포함은 다음을 포함하여 Teams 모임에서 지원되는 대부분의 기능을 제공합니다.

- 사용자가 오디오 및 비디오 디바이스를 구성하는 사전 모임 환경
- 오디오 및 비디오 디바이스를 구성하기 위한 모임 내 환경
- [비디오 배경](https://support.microsoft.com/office/change-your-background-for-a-teams-meeting-f77a2381-443a-499d-825e-509a140f4780): 참가자가 배경을 흐리게 하거나 바꿀 수 있도록 허용
- [비디오 갤러리에 대한 여러 옵션](https://support.microsoft.com/office/using-video-in-microsoft-teams-3647fc29-7b92-4c26-8c2d-8a596904cdae): 대형 갤러리, 함께 모드, 포커스, 고정 및 스포트라이트
- [콘텐츠 공유](https://support.microsoft.com/en-us/office/share-content-in-a-meeting-in-teams-fcc2bf59-aecd-4481-8f99-ce55dd836ce8): 참가자가 자신의 화면을 공유할 수 있도록 허용

이 UI와 다른 Azure 통신 SDK를 비교한 자세한 내용은 [UI SDK 개념 소개](ui-sdk-overview.md)를 참조하세요. 
