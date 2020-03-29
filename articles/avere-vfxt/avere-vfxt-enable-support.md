---
title: Avere vFXT 지원 사용 - Azure
description: Avere vFXT for Azure에서 지원 업로드를 사용하도록 설정하는 방법
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 12/14/2019
ms.author: rohogue
ms.openlocfilehash: d12bbd1708ceb948aea982f9ed1ab36879e3751c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75415361"
---
# <a name="enable-support-uploads"></a>지원 업로드 사용

Azure용 Avere vFXT는 클러스터에 대한 지원 데이터를 자동으로 업로드할 수 있습니다. 이러한 업로드를 통해 지원 담당자는 가능한 최고의 고객 서비스를 제공합니다.

## <a name="steps-to-enable-uploads"></a>업로드를 사용하도록 설정하는 단계

다음 단계에 따라 Avere 제어판에서 지원을 활성화합니다. (제어판을 여는 방법을 알아보려면 [vFXT 클러스터 액세스 읽기).](avere-vfxt-cluster-gui.md)

1. 위쪽에서 **설정** 탭으로 이동합니다.
1. 왼쪽의 **지원** 링크를 클릭하고 개인 정보 취급 방침에 동의합니다.

   ![개인정보처리방침에 동의하는 확인 단추가 있는 Avere 제어판 및 팝업 창을 보여주는 스크린샷](media/avere-vfxt-privacy-policy.png)

1. 지원 구성 페이지에서 왼쪽의 삼각형을 클릭하여 **고객 정보** 섹션을 엽니다.
1. **업로드 정보 유효성 재검사** 단추를 클릭합니다.
1. **클러스터의**지원 이름을 고유 클러스터 이름으로 설정합니다. 이 이름은 직원을 지원하기 위해 클러스터를 고유하게 식별해야 합니다.
1. **통계 모니터링**, **일반 정보 업로드** 및 **크래시 정보 업로드** 확인란을 선택합니다.
1. **제출**을 클릭합니다.

   ![지원 설정 페이지의 완료된 고객 정보 섹션을 포함하는 스크린샷](media/avere-vfxt-support-info.png)

1. **SPS(Secure Proactive Support)** 왼쪽의 삼각형을 클릭하여 섹션을 확장합니다.
1. **SPS 링크 사용** 확인란을 선택합니다.
1. **제출**을 클릭합니다.

   ![지원 설정 페이지의 완료된 보안 주도적 지원 섹션을 포함하는 스크린샷](media/avere-vfxt-support-sps.png)

## <a name="next-steps"></a>다음 단계

온-프레미스 또는 기존 클라우드 스토리지 시스템을 클러스터에 추가해야 하는 경우 [저장소 구성의](avere-vfxt-add-storage.md)지침을 따릅니다.

클러스터에 클라이언트를 연결할 준비가 되었으면 [Avere vFXT 클러스터 탑재](avere-vfxt-mount-clients.md)를 읽어보세요.
