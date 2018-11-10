---
title: Avere vFXT for Azure에 대한 지원을 받는 방법
description: Avere vFXT for Azure에 대한 지원 티켓을 여는 방법에 대해 설명합니다.
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: d621511cbb6983f8ad57ea8305823039475f40d0
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/31/2018
ms.locfileid: "50669550"
---
# <a name="get-help-with-your-system"></a>시스템 지원 받기

Avere vFXT for Azure에 대한 도움이 필요한 경우 지원을 받을 수 있는 다양한 방법은 다음과 같습니다.

* **Avere vFXT 문제** - [아래](#open-a-support-ticket-for-your-avere-vfxt)에서 설명한 대로 Azure Portal을 사용하여 Avere vFXT에 대한 지원 티켓을 엽니다.
* **할당량** - 할당량 관련 문제가 있으면 [할당량 증가를 요청](#request-a-quota-increase)합니다.
* **설명서 및 예제** - 이 설명서 또는 예제에 문제가 있으면, 문제가 있는 페이지에서 아래쪽으로 스크롤하고 **피드백** 섹션을 사용하여 기존 문제를 검색하고 필요한 경우 새 문제를 제출합니다.  

## <a name="open-a-support-ticket-for-your-avere-vfxt"></a>Avere vFXT에 대한 지원 티켓 열기

Avere vFXT를 배포하거나 사용하는 동안 문제가 발생하면 Azure Portal을 통해 지원을 요청합니다.  

다음 단계에 따라 지원 티켓의 태그가 클러스터의 리소스로 지정되었는지 확인합니다. 티켓에 태그를 지정하면 올바른 지원 리소스로 라우팅하는 데 도움이 됩니다. 

1. [https://portal.azure.com](https://portal.azure.com)에서 **리소스 그룹**을 선택합니다.

   !["리소스 그룹"이 원으로 표시된 Azure Portal 왼쪽 메뉴의 스크린샷](media/avere-vfxt-ticket-rg.png)

1. 문제가 발생한 vFXT 클러스터가 포함된 리소스 그룹을 찾아서 Avere 가상 머신 중 하나를 클릭합니다.

    ![원으로 표시된 특정 VM이 있는 Azure Portal 리소스 그룹 "개요" 패널의 스크린샷](media/avere-vfxt-ticket-vm.png)

1. VM 페이지의 왼쪽 패널에서 아래쪽으로 스크롤하고 **새 지원 요청**을 클릭합니다.

    ![이전 스크린샷의 VM에 대한 Azure Portal VM 페이지의 스크린샷 아래쪽으로 스크롤되고 원으로 표시된 "새 지원 요청"이 있는 왼쪽 메뉴](media/avere-vfxt-ticket-request.png)

1. 지원 요청 페이지 중 하나에서 **모든 서비스**를 클릭하고, **저장소** 아래에서 **Avere vFXT**를 선택합니다.

    !["기본 사항" 머리글 및 주위가 원으로 표시된 "서비스" 항목이 있는 Azure Portal 새 지원 요청 화면의 스크린샷. "모든 서비스" 단추가 선택되고 드롭다운 메뉴 필드의 값이 "Avere vFXT"입니다.](media/avere-vfxt-ticket-service.png)

1. 두 번째 페이지에서 문제와 가장 가깝게 일치하는 문제 유형과 범주를 선택합니다. 문제가 발생한 시간이 포함된 짧은 제목과 설명을 추가합니다. 

   !["문제" 머리글이 있는 새 지원 요청 화면의 스크린샷(완성해야 하는 많은 필드가 있음)](media/avere-vfxt-ticket-problem.png)

1. 세 번째 페이지에서 연락처 정보를 입력하고 **만들기**를 클릭합니다. 확인 및 티켓 번호가 사용자의 이메일 주소로 전송되고, 지원 담당자가 연락을 드립니다.

## <a name="request-a-quota-increase"></a>할당량 증가 요청

Avere vFXT for Azure를 배포하는 데 필요한 구성 요소를 확인하려면 [vFXT 클러스터에 대한 할당량](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster)을 참조하세요. Azure Portal에서 [할당량 증가를 요청](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request)할 수 있습니다.