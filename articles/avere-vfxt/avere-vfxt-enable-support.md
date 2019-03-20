---
title: Avere vFXT 지원 사용 - Azure
description: Avere vFXT for Azure에서 지원 업로드를 사용하도록 설정하는 방법
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: fe096b2e2a75cc89e3ce5ef905d8e4c347cc153a
ms.sourcegitcommit: f7f4b83996640d6fa35aea889dbf9073ba4422f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/28/2019
ms.locfileid: "56992467"
---
# <a name="enable-support-uploads"></a>지원 업로드 사용

The Avere vFXT for Azure는 클러스터에 대한 지원 데이터를 자동으로 업로드할 수 있습니다. 이러한 업로드를 통해 지원 담당자는 가능한 최고의 고객 서비스를 제공합니다.

## <a name="steps-to-enable-uploads"></a>업로드를 사용하도록 설정하는 단계

다음 단계에 따라 Avere 제어판에서 지원을 활성화합니다. (Avere 제어판을 여는 방법은 [vFXT 클러스터에 액세스](avere-vfxt-cluster-gui.md)를 참조하세요.)

1. 위쪽에서 **설정** 탭으로 이동합니다.
1. 왼쪽의 **지원** 링크를 클릭하고 개인 정보 취급 방침에 동의합니다.

   ![개인정보처리방침에 동의하는 확인 단추가 있는 Avere 제어판 및 팝업 창을 보여주는 스크린샷](media/avere-vfxt-privacy-policy.png)

1. **고객 정보** 왼쪽의 삼각형을 클릭하여 섹션을 확장합니다.
1. **업로드 정보 유효성 재검사** 단추를 클릭합니다.
1. **고유한 클러스터 이름**에서 클러스터의 지원 이름을 설정합니다. 직원을 지원하도록 클러스터를 고유하게 식별해야 합니다.
1. **통계 모니터링**, **일반 정보 업로드** 및 **크래시 정보 업로드** 확인란을 선택합니다.
1. **제출**을 클릭합니다.

   ![지원 설정 페이지의 완료된 고객 정보 섹션을 포함하는 스크린샷](media/avere-vfxt-support-info.png)

1. **SPS(Secure Proactive Support)** 왼쪽의 삼각형을 클릭하여 섹션을 확장합니다.
1. **SPS 링크 사용** 확인란을 선택합니다.
1. **제출**을 클릭합니다.

   ![지원 설정 페이지의 완료된 보안 주도적 지원 섹션을 포함하는 스크린샷](media/avere-vfxt-support-sps.png)

## <a name="next-steps"></a>다음 단계

온-프레미스를 추가 해야 할 경우 또는 기존 클라우드 클러스터에 저장소 시스템의 지침을 따릅니다 [저장소 구성](avere-vfxt-add-storage.md)합니다. 

클러스터에 클라이언트를 연결할 준비가 되었으면 [Avere vFXT 클러스터 탑재](avere-vfxt-mount-clients.md)를 읽어보세요.