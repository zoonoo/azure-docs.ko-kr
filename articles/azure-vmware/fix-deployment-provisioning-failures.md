---
title: Azure VMware 솔루션 배포 또는 프로 비전 실패에 대 한 지원
description: Azure vmware 솔루션 사설 클라우드에서 정보를 가져와 Azure VMware 솔루션 배포 또는 프로 비전 실패에 대 한 서비스 요청을 파일 합니다.
ms.topic: how-to
ms.date: 10/28/2020
ms.openlocfilehash: 27b645f4ca225fdd74bca6499b6581b3803e41a4
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94542408"
---
# <a name="open-a-support-request-for-an-azure-vmware-solution-deployment-or-provisioning-failure"></a>Azure VMware 솔루션 배포 또는 프로 비전 실패에 대 한 지원 요청 열기

이 문서에서는 [지원 요청](https://rc.portal.azure.com/#create/Microsoft.Support) 을 열고 Azure VMware 솔루션 배포 또는 프로 비전 오류에 대 한 주요 정보를 제공 하는 방법을 보여 줍니다. 

사설 클라우드에서 오류가 발생 한 경우 Azure Portal에서 지원 요청을 열어야 합니다. 지원 요청을 열려면 먼저 Azure Portal에서 몇 가지 주요 정보를 가져옵니다.

- 상관관계 ID
- Azure Express 경로 회로 ID
- 오류 메시지

## <a name="get-the-correlation-id"></a>상관 관계 ID 가져오기
 
Azure에서 사설 클라우드 또는 리소스를 만들 때 리소스에 대 한 상관 관계 ID가 리소스에 대해 자동으로 생성 됩니다. 요청을 신속 하 게 열고 해결 하려면 지원 요청에 사설 클라우드 상관 관계 ID를 포함 합니다.

Azure Portal에서 다음 두 가지 방법으로 리소스에 대 한 상관 관계 ID를 가져올 수 있습니다.

* **개요** 창
* 배포 로그
 
 ### <a name="get-the-correlation-id-from-the-resource-overview"></a>리소스 개요에서 상관 관계 ID를 가져옵니다.

다음은 상관 관계 ID가 선택 된 상태에서 실패 한 사설 클라우드 배포에 대 한 작업 세부 정보의 예입니다.

:::image type="content" source="media/fix-deployment-provisioning-failures/failed-private-cloud-deployment.png" alt-text="상관 관계 ID가 선택 된 실패 한 사설 클라우드 배포를 보여 주는 스크린샷":::

사설 클라우드 **개요** 창에서 배포 결과에 액세스 하려면 다음을 수행 합니다.

1. Azure Portal에서 사설 클라우드를 선택 합니다.

1. 왼쪽 메뉴에서 **개요** 를 선택 합니다.

배포가 시작 된 후에는 배포 결과가 사설 클라우드 **개요** 창에 표시 됩니다.

서비스 요청에 포함할 사설 클라우드 배포 상관 관계 ID를 복사 하 여 저장 합니다.

### <a name="get-the-correlation-id-from-the-deployment-log"></a>배포 로그에서 상관 관계 ID를 가져옵니다.

Azure Portal에서 배포 활동 로그를 검색 하 여 실패 한 배포에 대 한 상관 관계 ID를 가져올 수 있습니다.

배포 로그에 액세스 하려면:

1. Azure Portal에서 사설 클라우드를 선택 하 고 알림 아이콘을 선택 합니다.

   :::image type="content" source="media/fix-deployment-provisioning-failures/open-notifications.png" alt-text="Azure Portal의 알림 아이콘을 보여 주는 스크린샷":::

1. **알림** 창의 **활동 로그에서 추가 이벤트** 를 선택 합니다.

    :::image type="content" source="media/fix-deployment-provisioning-failures/more-events-in-activity-log.png" alt-text="알림 창에서 선택한 활동 로그 링크의 더 많은 이벤트를 보여 주는 스크린샷":::

1. 실패 한 배포와 해당 상관 관계 ID를 찾으려면 리소스 이름 또는 리소스를 만드는 데 사용한 기타 정보를 검색 합니다. 

    다음 예제에서는 pc03 이라는 사설 클라우드 리소스에 대 한 검색 결과를 보여 줍니다.
 
    :::image type="content" source="media/fix-deployment-provisioning-failures/find-past-deployments.png" alt-text="예제 사설 클라우드 리소스 및 PrivateCloud 만들기 또는 업데이트 창에 대 한 검색 결과를 보여 주는 스크린샷":::
 
1. **활동 로그** 창의 검색 결과에서 실패 한 배포의 작업 이름을 선택 합니다.

1. **PrivateCloud 만들기 또는 업데이트** 창에서 **JSON** 탭을 선택한 다음 `correlationId` 표시 된 로그에서를 찾습니다. 값을 복사 `correlationId` 하 여 지원 요청에 포함 합니다. 
 
## <a name="copy-error-messages"></a>오류 메시지 복사

배포 문제를 해결 하려면 Azure Portal 표시 되는 오류 메시지를 포함 합니다. 오류 요약을 보려면 경고 메시지를 선택 합니다.
 
:::image type="content" source="media/fix-deployment-provisioning-failures/summary-of-errors.png" alt-text="복사 아이콘이 선택 된 상태에서 오류 창의 요약 탭에 오류 정보를 표시 하는 스크린샷":::

오류 메시지를 복사 하려면 복사 아이콘을 선택 합니다. 복사 된 메시지를 저장 하 여 지원 요청에 포함 합니다.
 
## <a name="get-the-expressroute-id-uri"></a>Express 경로 ID (URI) 가져오기
 
아마도 사설 클라우드 Express 경로 회로를 사용 하 여 기존 사설 클라우드를 확장 하거나 피어 링 하려고 시도 하 고 실패 합니다. 이 시나리오에서는 지원 요청에 포함할 Express 경로 ID가 필요 합니다.

Express 경로 ID를 복사 하려면:

1. Azure Portal에서 사설 클라우드를 선택 합니다.
1. 왼쪽 메뉴의 **관리** 에서 **연결** 을 선택 합니다. 
1. 오른쪽 창에서 **express** 경로 탭을 선택 합니다.
1. **Express 경로 ID** 의 복사 아이콘을 선택 하 고 지원 요청에 사용할 값을 저장 합니다.
 
:::image type="content" source="media/fix-deployment-provisioning-failures/expressroute-id.png" alt-text="Express 경로 ID를 클립보드로 복사 합니다."::: 
 
## <a name="pre-validation-failures"></a>사전 유효성 검사 오류

배포 전 사설 클라우드 사전 유효성 검사에 실패 하면 상관 관계 ID가 생성 되지 않습니다. 이 시나리오에서는 지원 요청에 다음 정보를 제공할 수 있습니다.

- 오류 및 오류 메시지입니다. 이러한 메시지는 할당량 관련 문제의 경우와 같이 많은 오류에 유용할 수 있습니다. 이 문서에 설명 된 대로 이러한 메시지를 복사 하 여 지원 요청에 포함 하는 것이 중요 합니다.
- 다음을 포함 하 여 Azure VMware 솔루션 사설 클라우드를 만드는 데 사용한 정보
  - 위치
  - Resource group
  - 리소스 이름

## <a name="create-your-support-request"></a>지원 요청 만들기

지원 요청을 만드는 방법에 대 한 일반적인 내용은 [Azure 지원 요청을 만드는 방법](../azure-portal/supportability/how-to-create-azure-support-request.md)을 참조 하세요. 

Azure VMware 솔루션 배포 또는 프로 비전 실패에 대 한 지원 요청을 만들려면 다음을 수행 합니다.

1. Azure Portal에서 **도움말** 아이콘을 선택 하 고 **새 지원 요청** 을 선택 합니다.

    :::image type="content" source="media/fix-deployment-provisioning-failures/open-sr-on-avs.png" alt-text="Azure Portal 새 지원 요청 창의 스크린샷":::

1. 필요한 정보를 입력 하거나 선택 합니다.

   1. **기본 사항** 탭에서 다음을 수행합니다.

      1. **문제 유형** 에서 **구성 및 설정 문제** 를 선택 합니다.

      1. **문제 하위 형식** 에 대해 **사설 클라우드 프로 비전** 을 선택 합니다.

   1. **세부 정보** 탭에서 다음을 수행 합니다.

      1. 필요한 정보를 입력하거나 선택합니다.

      1. 이 정보가 요청 된 위치에 상관 관계 ID 또는 Express 경로 ID를 붙여 넣습니다. 이러한 값에 대 한 특정 텍스트 상자가 표시 되지 않는 경우 **문제에 대 한 세부 정보 제공** 텍스트 상자에 붙여넣습니다.

    1. **문제에 대 한 세부 정보 제공** 텍스트 상자에서 복사한 오류 메시지를 포함 한 오류 정보를 붙여넣습니다.

1. 항목을 검토 한 후 **만들기** 를 선택 하 여 지원 요청을 만듭니다.
