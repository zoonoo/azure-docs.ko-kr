---
title: Avere vFXT for Azure에 대한 지원을 받는 방법
description: Avere vFXT for Azure에 대한 지원 티켓을 여는 방법에 대해 설명합니다.
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: rohogue
ms.openlocfilehash: b3c604003f78a150067875fb2064ab5eca0600db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252559"
---
# <a name="get-help-with-your-system"></a>시스템 지원 받기

Azure 시스템에 대한 Avere vFXT에 대한 도움말을 보려면 지원을 받는 방법은 다음과 같습니다.

* **Avere vFXT 문제** - [아래](#open-a-support-ticket-for-your-avere-vfxt)에서 설명한 대로 Azure Portal을 사용하여 Avere vFXT에 대한 지원 티켓을 엽니다.
* **할당량** - 할당량 관련 문제가 있으면 [할당량 증가를 요청](#request-a-quota-increase)합니다.
* **설명서 및 예제** - 이 설명서 또는 예제에 문제가 있으면, 문제가 있는 페이지에서 아래쪽으로 스크롤하고 **피드백** 섹션을 사용하여 기존 문제를 검색하고 필요한 경우 새 문제를 제출합니다.

## <a name="open-a-support-ticket-for-your-avere-vfxt"></a>Avere vFXT에 대한 지원 티켓 열기

Avere vFXT를 배포하거나 사용하는 동안 문제가 발생하면 Azure Portal을 통해 지원을 요청합니다.

다음 단계에 따라 지원 티켓의 태그가 클러스터의 리소스로 지정되었는지 확인합니다. 티켓에 태그를 지정하면 올바른 지원 리소스로 라우팅하는 데 도움이 됩니다.

1. 에서 [https://portal.azure.com](https://portal.azure.com) **리소스 그룹을**선택합니다. 문제가 발생한 vFXT 클러스터가 포함된 리소스 그룹으로 이동한 다음 Avere 클러스터 가상 시스템 중 하나를 클릭합니다.

    ![원으로 표시된 특정 VM이 있는 Azure Portal 리소스 그룹 "개요" 패널의 스크린샷](media/avere-vfxt-ticket-vm.png)

1. VM 페이지의 왼쪽 패널에서 아래쪽으로 스크롤하고 **새 지원 요청**을 클릭합니다.

    ![이전 스크린샷의 VM에 대한 Azure Portal VM 페이지의 스크린샷 왼쪽 메뉴가 아래쪽으로 스크롤되고 '새 지원 요청'이 동그라미로 표시됩니다.](media/avere-vfxt-ticket-request.png)

1. 지원 요청의 첫 페이지에서 문제 유형을 선택하고 올바른 구독이 선택되었는지 확인합니다.

   **서비스**에서 **모든 서비스를** 클릭 하고 **저장소** 아래를 보면 **Avere vFXT를**선택합니다.

   짧은 요약을 추가하고 문제 유형을 선택합니다.

    ![Azure 포털에서 새 지원 요청 화면의 스크린샷입니다. 기본 탭이 선택됩니다. 화면 항목에는 문제 유형, 구독, 서비스, 요약 및 문제 유형이 포함됩니다.](media/ticket-basics.png)

   계속하려면 **다음**을 클릭합니다.

1. 지원 양식의 두 번째 페이지에는 요약 설명을 기반으로 문제를 해결하기 위한 제안사항이 포함되어 있습니다. 지원 티켓을 만들어야 하는 경우 하단의 **다음** 단추를 클릭합니다.

   ![선택한 솔루션 탭이 있는 새 지원 요청 화면의 스크린샷입니다. 가운데텍스트필드에는 '권장 솔루션'이라는 제목이 있으며 가능한 해결 방안을 설명합니다.](media/ticket-solutions.png)

1. 세 번째 페이지에서 세부 정보를 제공 - 여기에는 클러스터에 대한 정보, 문제가 발생한 시간, 심각도 및 연락 방법에 대한 정보가 포함됩니다. 정보를 입력하고 하단의 **다음** 단추를 클릭합니다.

   ![세부 정보 탭을 선택한 새 지원 요청 화면의 스크린샷입니다. 정보 필드는 '문제 세부 정보', '지원 방법', '연락처 정보' 범주로 구성됩니다.](media/ticket-details.png)

1. 최종 페이지의 정보를 검토하고 **에서 만들기를**클릭합니다. 확인 및 티켓 번호가 사용자의 이메일 주소로 전송되고, 지원 담당자가 연락을 드립니다.

## <a name="request-a-quota-increase"></a>할당량 증가 요청

Avere vFXT for Azure를 배포하는 데 필요한 구성 요소를 확인하려면 [vFXT 클러스터에 대한 할당량](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster)을 참조하세요. Azure Portal에서 [할당량 증가를 요청](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request)할 수 있습니다.
