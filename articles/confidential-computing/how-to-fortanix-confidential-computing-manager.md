---
title: Azure 관리 되는 응용 프로그램의 Fortanix 기밀 컴퓨팅 관리자
description: Azure Portal에서 관리 되는 응용 프로그램에 CCM (Fortanix 기밀 컴퓨팅 관리자)을 배포 하는 방법에 대해 알아봅니다.
author: JBCook
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.workload: infrastructure
ms.topic: how-to
ms.date: 02/03/2021
ms.author: jencook
ms.openlocfilehash: 757ce9b7502316bbc8a5b8f27ba672048b7bbace
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102563424"
---
# <a name="fortanix-confidential-computing-manager-in-an-azure-managed-application"></a>Azure 관리 되는 응용 프로그램의 Fortanix 기밀 컴퓨팅 관리자

이 문서에서는 Azure Portal에서 Fortanix 기밀 컴퓨팅 관리자가 관리 하는 응용 프로그램을 배포 하는 방법을 보여 줍니다.

Fortanix는 Azure 인프라를 기반으로 구축 된 제품 및 서비스를 포함 하는 타사 소프트웨어 공급 업체입니다. Azure에서 유사한 기밀 컴퓨팅 서비스를 제공 하는 다른 타사 공급자가 있습니다.

> [!NOTE]
>이 문서에서 참조 하는 제품은 Microsoft에서 제어 하 고 있지 않습니다. Microsoft는 편의를 위해서만이 정보를 제공 하 고, 이러한 타사 제품에 대 한 참조는 Microsoft의 보증을 암시 하지 않습니다.

## <a name="prerequisites"></a>전제 조건

- 변환 된 응용 프로그램 이미지를 푸시하는 개인 Docker 레지스트리
- Azure 구독이 없는 경우 시작하기 전에 [계정을 만드세요](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/).

## <a name="deploy-a-confidential-computing-manager-through-an-azure-managed-application"></a>Azure 관리 되는 응용 프로그램을 통해 기밀 컴퓨팅 관리자 배포

1. [Azure 포털](https://portal.azure.com/)로 이동합니다.

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/azure-portal.png" alt-text="Azure Portal.":::

2. 검색 창에서 "Fortanix 기밀 컴퓨팅 관리자"를 검색 하 고 Fortanix CCM에 대 한 Marketplace 목록을 찾습니다. **Azure에서 Fortanix 기밀 컴퓨팅 관리자를** 선택 합니다.

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/search-marketplace-listing.png" alt-text="Marketplace 목록.":::

3. CCM 관리 응용 프로그램을 만드는 페이지가 열립니다. **만들기** 를 선택 합니다.

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/create-managed-application.png" alt-text="응용 프로그램을 만듭니다.":::

4. 모든 필수 필드를 입력 합니다.
   1. 관리 되는 응용 프로그램 세부 정보 섹션의 **관리 되는 리소스 그룹** 필드에는 사용자가 필요한 경우 수정할 수 있는 기본값이 포함 됩니다.
   2. **지역** 필드에서 **오스트레일리아 동부**, **오스트레일리아 남동쪽**, **미국 동부**, **미국 서 부 2**, **유럽 서부**, **북부**, **캐나다 중부**, **캐나다 동부** 또는 **미국 동부 2 EUAP** 를 선택 합니다.

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/required-fields.png" alt-text="필수 필드":::

   **검토 + 만들기** 를 선택 하 여 Fortanix CCM 관리 응용 프로그램을 만듭니다.

5. 세부 정보를 검토 하 고 유효성 검사가 통과 되 면 **위의 사용 약관에 동의 함** 확인란을 선택 하 고 **만들기** 를 선택 하 여 관리 되는 응용 프로그램을 만듭니다.

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/review-details.png" alt-text="세부 정보를 검토 합니다.":::

6. Fortanix CCM 배포가 시작 되 고 배포가 진행 되 고 있음을 알립니다.

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/deployment-progress.png" alt-text="배포 진행률.":::

7. 배포가 완료 되 면 **리소스로 이동** 단추를 선택 하 여 배포 된 CCM 관리 응용 프로그램의 "개요" 페이지로 이동 하 여 계산 노드를 등록 합니다.

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/fortanix-resource.png" alt-text="Azure Portal에 성공적으로 배포 된 것을 보여 주는 스크린샷":::

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/fortanix-overview.png" alt-text="Azure Portal의 기밀 컴퓨팅 리소스에 대 한 개요를 보여 주는 스크린샷":::

## <a name="enroll-the-compute-node-in-fortanix-ccm"></a>Fortanix CCM에서 계산 노드 등록

1. 왼쪽 탐색 메뉴에서 **기밀 컴퓨팅 관리자** 를 선택 합니다. **그림 9** 에 표시 된 대로 Fortanix CCM에 로그인 하 고 계정을 만듭니다.

    등록, 로그인 및 CCM에서 계정을 만드는 방법에 대 한 자세한 내용은 [Ccm 시작](https://support.fortanix.com/hc/en-us/articles/360034373551-User-s-Guide-Logging-in)을 참조 하세요.
    
    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/fortanix-login.png" alt-text="Fortanix 기밀 컴퓨팅 관리자 로그인을 보여 주는 스크린샷":::
    
2. CCM 관리 콘솔에서 조인 토큰을 가져오려면 먼저 **노드 등록** 단추를 선택 합니다. 그런 다음 노드 등록 창에서 **복사** 단추를 선택 하 여 조인 토큰을 복사 합니다.

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/get-join-token.png" alt-text="조인 토큰을 가져오는 과정을 보여 주는 스크린샷":::

3. 이제 노드 에이전트를 등록 하려면 **기밀 컴퓨팅 노드 에이전트** 탭을 선택 하 고 **추가** 를 선택 하 여 CCM 노드 에이전트를 추가 합니다.

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/add-node-agent.png" alt-text="노드 에이전트를 추가 하는 방법을 보여 주는 스크린샷":::

4.  CCM 노드 에이전트 양식에서 모든 필수 필드를 채웁니다. **조인 토큰** 에서 2 단계에서 복사한 조인 토큰을 붙여 넣습니다. **검토 + 제출** 을 선택 하 여 확인 합니다.

    CCM 계산 노드를 등록 하는 방법에 대 한 자세한 내용은 [Compute 노드 등록](https://support.fortanix.com/hc/en-us/articles/360043085652-User-s-Guide-Compute-Nodes)을 참조 하세요.
    
    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/enroll-compute-node.png" alt-text="계산 노드를 등록 하는 것을 보여 주는 스크린샷":::
    
5. 유효성 검사를 통과 한 후 **제출** 을 선택 하 여 노드 에이전트 만들기를 완료 합니다.

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/node-agent-created.png" alt-text="노드 에이전트가 생성 된 것을 보여 주는 스크린샷":::

6. 배포 상태를 확인 하려면 **개요** 탭으로 이동 하 고 **관리 되는 리소스 그룹** 링크를 선택 합니다.

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/node-enrolled.png" alt-text="노드가 등록 되어 있음을 보여 주는 스크린샷":::
    
    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/managed-resource-group.png" alt-text="배포 상태 확인을 보여 주는 스크린샷":::

7. 이제 배포 상태가 아직 진행 중 이며 노드 에이전트가 성공적으로 등록 되는 데 몇 분 정도 걸릴 것입니다.

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/deployment-in-progress.png" alt-text="진행 중인 배포를 보여 주는 스크린샷":::

8. 노드 에이전트가 성공적으로 등록 되 면 상태가 "성공"으로 변경 됩니다.

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/deployment-succeeded.png" alt-text="배포가 성공 했음을 보여 주는 스크린샷":::

9. 이제 CCM 관리 응용 프로그램에서 계산 노드 페이지로 이동 하면 노드가 **활성** 상태 이며 성공적으로 등록 된 것을 알 수 있습니다.

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/node-active-state.png" alt-text="성공적으로 등록 된 노드를 보여 주는 스크린샷":::

## <a name="clean-up-resources"></a>리소스 정리

또한 사용자는 기밀 컴퓨팅 노드 에이전트 페이지에서 CCM 노드 에이전트를 삭제할 수 있습니다. 노드 에이전트를 삭제 하려면 노드 에이전트를 선택 하 고 위쪽 표시줄에서 **삭제** 단추를 선택 합니다.

:::image type="content" source="media/how-to-fortanix-confidential-computing-manager/delete-node-agent.png" alt-text="노드 에이전트를 삭제 하는 것을 보여 주는 스크린샷":::

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Azure 관리 되는 앱을 사용 하 여 Fortanix의 기밀 컴퓨팅 관리자를 사용 하 여 노드를 등록 했습니다. 노드 등록을 통해 응용 프로그램 이미지를 기밀 컴퓨팅 가상 컴퓨터를 기반으로 하 여 실행할 수 있습니다. Azure의 기밀 컴퓨팅 가상 머신에 대한 자세한 내용은 [Virtual Machines 솔루션](virtual-machine-solutions.md)을 참조하세요.

Azure의 기밀 컴퓨팅 제품에 대해 자세히 알아보려면 [azure 기밀 컴퓨팅](overview.md)을 참조 하세요.

[Anjuna](https://azuremarketplace.microsoft.com/marketplace/apps/anjuna-5229812.aee-az-v1) 및 [scone](https://sconedocs.github.io)와 같이 Azure에서 다른 타사 제품을 사용 하 여 유사한 작업을 완료 하는 방법을 알아봅니다.

