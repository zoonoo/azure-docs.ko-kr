---
title: Avere vFXT 지원 사용 - Azure
description: Avere vFXT for Azure에서 지원 업로드를 사용하도록 설정하는 방법
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: bc21e06b704bfe3d25132092efbbf23f342acb14
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/31/2018
ms.locfileid: "50669290"
---
# <a name="enable-support-uploads"></a>지원 업로드 사용

The Avere vFXT for Azure는 클러스터에 대한 지원 데이터를 자동으로 업로드할 수 있습니다. 이러한 업로드를 통해 지원 담당자는 가능한 최고의 고객 서비스를 제공합니다.

## <a name="steps-to-enable-uploads"></a>업로드를 사용하도록 설정하는 단계

다음 단계에 따라 Avere 제어판에서 지원을 활성화합니다. (Avere 제어판을 여는 방법은 [vFXT 클러스터에 액세스](avere-vfxt-cluster-gui.md)를 참조하세요.)

1. 위쪽에서 **설정** 탭으로 이동합니다.
1. 왼쪽의 **지원** 링크를 클릭하고 개인 정보 취급 방침에 동의합니다.

   ![개인 정보 취급 방침 동의를 확인하는 스크린샷](media/avere-vfxt-privacy-policy.png)
1. **고객 정보** 왼쪽의 삼각형을 클릭하여 섹션을 확장합니다.
1. **고유한 클러스터 이름**에서 클러스터의 지원 이름을 설정합니다. 직원을 지원하도록 클러스터를 고유하게 식별해야 합니다.
1. **통계 모니터링**, **일반 정보 업로드** 및 **크래시 정보 업로드** 확인란을 선택합니다.
1. **업로드 정보 유효성 검사** 단추를 클릭합니다.
1. **제출**을 클릭합니다.
1. **SPS(Secure Proactive Support)** 왼쪽의 삼각형을 클릭하여 섹션을 확장합니다.
1. **SPS 링크 사용** 확인란을 선택합니다.
1. **제출**을 클릭합니다.

   ![지원을 사용하도록 설정하는 모든 단계가 포함된 스크린샷](media/avere-vfxt-support-info-steps.png)


## <a name="next-steps"></a>다음 단계

클러스터에 온-프레미스 저장소 시스템을 추가해야 하는 경우 [저장소 구성](avere-vfxt-add-storage.md)의 지침을 따릅니다. 

클러스터에 클라이언트를 연결할 준비가 되었으면 [Avere vFXT 클러스터 탑재](avere-vfxt-mount-clients.md)를 읽어보세요.