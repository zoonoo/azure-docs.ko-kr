---
title: Azure VMware 솔루션 배포 또는 프로 비전 오류에 대 한 도움말 보기
description: Azure vmware 솔루션 사설 클라우드에서 필요한 정보를 가져와 Azure VMware 솔루션 배포 또는 프로 비전 오류에 대 한 서비스 요청을 수행 하는 방법입니다.
ms.topic: how-to
ms.date: 06/09/2020
ms.openlocfilehash: 1f46dde895db417fd2b488a6203d5482e73d3c5e
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/28/2020
ms.locfileid: "92779495"
---
# <a name="get-help-with-azure-vmware-solution-deployment-or-provisioning-failures"></a>Azure VMware 솔루션 배포 또는 프로 비전 오류에 대 한 도움말 보기

이 문서는 Azure VMware 솔루션 배포 및 프로 비전 오류에 도움이 됩니다. 사설 클라우드에서 오류가 발생 한 경우 Azure Portal에서 SR ( [지원 요청](https://rc.portal.azure.com/#create/Microsoft.Support) )을 열어야 합니다. 

무엇 보다도 Azure Portal에서 몇 가지 주요 정보를 수집 해야 합니다.

- 상관관계 ID
- Express 경로 회로 ID

## <a name="collect-the-correlation-id"></a>상관 관계 ID 수집
 
상관 관계 ID는 Azure에서 사설 클라우드 또는 리소스를 만들 때 생성 됩니다. 각 Azure Resource Manager 배포는 상관 관계 ID도 생성 합니다. 이 ID를 사용 하 여 SR 생성 및 해결 속도를 높일 수 있습니다. 
 
다음은 상관 관계 ID가 강조 표시 된 실패 한 사설 클라우드 배포의 출력 예입니다.

:::image type="content" source="media/fix-deployment-provisioning-failures/failed-private-cloud-deployment.png" alt-text="상관 관계 ID를 사용 하 여 사설 클라우드를 배포 하지 못했습니다.":::

이 상관 관계 ID를 복사 하 고 저장 하 여 서비스 요청에 포함 합니다. 자세한 내용은이 문서의 끝에 있는 [지원 요청 만들기](#create-your-support-request) 를 참조 하세요.

사전 유효성 검사 단계에서 오류가 발생 하는 경우에는 상관 관계 ID가 생성 되지 않습니다. 이 경우 다음을 포함 하 여 Azure VMware 솔루션 사설 클라우드를 만들 때 사용한 정보를 제공할 수 있습니다.

- 위치
- Resource group
- 리소스 이름
 
### <a name="collect-a-summary-of-errors"></a>오류 요약 수집

오류에 대 한 세부 정보를 통해 문제를 해결할 수도 있습니다. 위의 화면에서 경고 메시지를 선택 하 여 오류에 대 한 요약을 확인 합니다.
 
 :::image type="content" source="media/fix-deployment-provisioning-failures/summary-of-errors.png" alt-text="상관 관계 ID를 사용 하 여 사설 클라우드를 배포 하지 못했습니다.":::

이 요약을 다시 복사 하 고 저장 하 여 SR에 포함 합니다.
 
### <a name="retrieve-past-deployments"></a>이전 배포 검색

알림 아이콘을 선택 하 여 액세스 한 배포 활동 로그에서 검색 하 여 실패 한 배포를 비롯 한 이전 배포를 검색할 수 있습니다.

:::image type="content" source="media/fix-deployment-provisioning-failures/open-notifications.png" alt-text="상관 관계 ID를 사용 하 여 사설 클라우드를 배포 하지 못했습니다.":::

알림에서 **활동 로그의 더 많은 이벤트** 를 선택 합니다.

:::image type="content" source="media/fix-deployment-provisioning-failures/more-events-in-activity-log.png" alt-text="상관 관계 ID를 사용 하 여 사설 클라우드를 배포 하지 못했습니다.":::

그런 다음 리소스 이름 또는 리소스를 만드는 데 사용 된 기타 정보를 검색 하 여 실패 한 배포와 해당 상관 관계 ID를 찾습니다. 다음 예제에서는 사설 클라우드 리소스 (pc03)에 대 한 검색 결과를 보여 줍니다.
 
:::image type="content" source="media/fix-deployment-provisioning-failures/find-past-deployments.png" alt-text="상관 관계 ID를 사용 하 여 사설 클라우드를 배포 하지 못했습니다.":::
 
실패 한 배포의 작업 이름을 선택 하면 세부 정보가 포함 된 창이 열립니다. JSON 탭을 선택 하 고 correlationId를 찾습니다. 를 복사 하 여 SR에 포함 합니다. 
 
## <a name="collect-the-expressroute-id-uri"></a>Express 경로 ID (URI) 수집
 
아마도 사설 클라우드 Express 경로 회로를 사용 하 여 기존 사설 클라우드를 확장 하거나 피어 링 하려고 시도 하 고 실패 합니다. 이 경우 Express 경로 ID가 필요 합니다. 

Azure Portal에서 **연결 > express** 경로를 선택 하 고 **express 경로 ID** 를 클립보드에 복사 합니다.
 
:::image type="content" source="media/fix-deployment-provisioning-failures/expressroute-id.png" alt-text="상관 관계 ID를 사용 하 여 사설 클라우드를 배포 하지 못했습니다."::: 
 
> [!NOTE]
> 경우에 따라 배포 전에 유효성 검사 확인이 실패할 수 있으며, 사용 가능한 유일한 정보는 오류 및 실패 메시지입니다. 이러한 오류는 많은 오류 (예를 들어, 할당량 관련 문제)에서 유용할 수 있으며 지원 요청에 이러한 메시지를 포함 하는 것이 중요 합니다. 이러한 정보를 수집 하려면 이전 섹션인 [오류 요약 수집](#collect-a-summary-of-errors)을 참조 하세요.

## <a name="create-your-support-request"></a>지원 요청 만들기

지원 요청을 만드는 방법에 대 한 일반적인 지침은 [Azure 지원 요청을 만드는 방법](../azure-portal/supportability/how-to-create-azure-support-request.md)을 참조 하세요. 

Azure VMware 솔루션 배포 또는 프로 비전 실패를 위한 SR 만들기에 대 한 구체적인 지침은 다음과 같습니다.

1. **도움말** 아이콘을 선택 하 고 **+ 새 지원 요청** 을 선택 합니다.

    :::image type="content" source="media/fix-deployment-provisioning-failures/open-sr-on-avs.png" alt-text="상관 관계 ID를 사용 하 여 사설 클라우드를 배포 하지 못했습니다.":::

2. 모든 필수 필드를 입력 하 고 **기본 사항** 탭에서 다음을 수행 합니다.

    - **문제 유형** 에서 **구성 및 설정 문제** 를 선택 합니다.

    - **문제 하위 형식** 에 대해 **사설 클라우드 프로 비전** 을 선택 합니다.

3. **세부 정보** 탭에서 다음을 수행 합니다.

    - 모든 필수 필드를 입력합니다.

    - 상관 관계 ID 또는 Express 경로 ID를 제공 된 특정 필드에 붙여넣습니다. 특정 필드가 표시 되지 않는 경우 **문제에 대 한 세부 정보를 입력** 하 여 텍스트 상자에 붙여 넣을 수 있습니다.

    - 복사한 오류 요약을 비롯 하 여 오류 정보를 해당 **문제에 대 한 세부 정보 제공** 아래의 텍스트 상자에 붙여넣습니다.

4. 을 검토 하 고 **만들기** 를 선택 하 여 SR를 만듭니다.
