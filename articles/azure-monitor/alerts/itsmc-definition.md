---
title: Log Analytics IT 서비스 관리 커넥터
description: 이 문서에서는 ITSMC (IT 서비스 관리 커넥터)의 개요와이를 사용 하 여 Log Analytics에서 ITSMC 작업 항목을 모니터링 및 관리 하 고 문제를 빠르게 해결 하는 방법에 대 한 정보를 제공 합니다.
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 05/24/2018
ms.custom: references_regions
ms.openlocfilehash: 98f53ec1b6506a6d47146377e837576254f445e2
ms.sourcegitcommit: 27cd3e515fee7821807c03e64ce8ac2dd2dd82d2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/16/2021
ms.locfileid: "103601069"
---
# <a name="connect-azure-to-itsm-tools-by-using-it-service-management-solution"></a>IT 서비스 관리 솔루션을 사용 하 여 Azure를 ITSM 도구에 연결

:::image type="icon" source="media/itsmc-overview/itsmc-symbol.png":::

이 문서에서는 ITSMC (IT Service Management) 작업 항목을 중앙에서 관리 하도록 Log Analytics에서 ITSMC (IT 서비스 관리 커넥터)를 구성 하는 방법에 대 한 정보를 제공 합니다.

## <a name="add-it-service-management-connector"></a>IT 서비스 관리 커넥터 추가

연결을 만들려면 ITSMC를 설치 해야 합니다.

1. Azure Portal에서 **리소스 만들기** 를 선택 합니다.

   ![리소스를 만들기 위한 메뉴 항목을 보여 주는 스크린샷](media/itsmc-overview/azure-add-new-resource.png)

2. Azure Marketplace에서 **IT 서비스 관리 커넥터** 를 검색 합니다. **만들기** 를 선택합니다.

   ![Azure Marketplace에서 만들기 단추를 보여 주는 스크린샷](media/itsmc-overview/add-itsmc-solution.png)

3. **LA 작업 영역** 섹션에서 itsmc를 설치 하려는 Log Analytics 작업 영역을 선택 합니다.
   > [!NOTE]
   > ITSMC는 미국 동부, 미국 서 부 2, 미국 중부, 미국 서 부, US Gov 애리조나, US Gov 버지니아, 캐나다 중부, 유럽 서부, 남부 영국, 동남 아시아, 일본 동부, 인도 중부 및 오스트레일리아 남동쪽 지역 에서만 Log Analytics 작업 영역에 설치할 수 있습니다.

4. **Log Analytics 작업 영역** 섹션에서 itsmc 리소스를 만들려는 리소스 그룹을 선택 합니다.

   ![Log Analytics 작업 영역 섹션을 보여 주는 스크린샷](media/itsmc-overview/itsmc-solution-workspace.png)
   
   > [!NOTE]
   > Microsoft Operations Management Suite (OMS)에서 Azure Monitor로 진행 중인 전환의 일부로 OMS 작업 영역을 *Log Analytics 작업 영역* 이라고 합니다.

5. **확인** 을 선택합니다.

ITSMC 리소스를 배포 하면 창의 오른쪽 위 모퉁이에 알림이 표시 됩니다.

## <a name="create-an-itsm-connection"></a>ITSM 연결 만들기

ITSMC를 설치한 후 ITSMC 도구를 준비 하 여 ITSMC에서 연결을 허용 해야 합니다. 연결 하려는 ITSM 제품을 기반으로 다음 링크 중 하나를 선택 합니다.

- [ServiceNow](./itsmc-connections-servicenow.md)
- [System Center Service Manager](./itsmc-connections-scsm.md)
- [Cherwell](./itsmc-connections-cherwell.md)
- [Provance](./itsmc-connections-provance.md)

ITSM 도구를 준비 후 다음 단계를 완료 하 여 연결을 만듭니다.

1. **모든 리소스** 에서 **servicedesk (*작업 영역 이름*)** 를 찾습니다.

   ![Azure Portal의 최근 리소스를 보여 주는 스크린샷](media/itsmc-definition/create-new-connection-from-resource.png)

1. 왼쪽 창의 **작업 영역 데이터 원본** 에서 **itsm 연결** 을 선택 합니다.

   ![ITSM 연결 메뉴 항목을 보여 주는 스크린샷](media/itsmc-overview/add-new-itsm-connection.png)

1. **연결 추가** 를 선택 합니다.

1. 사용 중인 ITSM 제품에 따라 연결 설정을 지정 합니다.

    - [ServiceNow](./itsmc-connections-servicenow.md)
    - [System Center Service Manager](./itsmc-connections-scsm.md)
    - [Cherwell](./itsmc-connections-cherwell.md)
    - [Provance](./itsmc-connections-provance.md)

   > [!NOTE]
   > 기본적으로 ITSMC는 24 시간 마다 한 번씩 연결의 구성 데이터를 새로 고칩니다. 사용자가 만든 편집 내용 또는 템플릿 업데이트를 반영 하도록 연결 데이터를 즉시 새로 고치려면 연결의 창에서 **동기화** 단추를 선택 합니다.
   >
   > ![연결 창의 동기화 단추를 보여 주는 스크린샷](media/itsmc-overview/itsmc-connections-refresh.png)

## <a name="create-itsm-work-items-from-azure-alerts"></a>Azure Alerts에서 ITSM 작업 항목 만들기

ITSM 연결을 만든 후 ITSM를 사용 하 여 Azure 경고를 기반으로 ITSM 도구에서 작업 항목을 만들 수 있습니다. 작업 항목을 만들려면 작업 그룹에서 ITSM 작업을 사용 합니다.

작업 그룹은 Azure 경고에 대 한 작업을 트리거하는 모듈화 된 재사용 가능한 방법을 제공 합니다. 메트릭 경고, 활동 로그 경고 및 Azure Portal에서 Log Analytics 경고와 함께 작업 그룹을 사용할 수 있습니다.

> [!NOTE]
> ITSM 연결을 만든 후 동기화 프로세스가 완료 될 때까지 30 분 정도 기다려야 합니다.

## <a name="define-a-template"></a>템플릿 정의

특정 작업 항목 형식에서는 ITSM 도구에서 정의한 템플릿을 사용할 수 있습니다. 템플릿을 사용 하면 작업 그룹의 고정 값에 따라 자동으로 채워지는 필드를 정의할 수 있습니다. 작업 그룹 정의의 일부로 사용 하려는 템플릿을 정의할 수 있습니다. 템플릿을 만드는 방법에 대 한 ServiceNow 문서 정보를 찾을 수 있습니다. (여기) [ https://docs.servicenow.com/bundle/paris-platform-administration/page/administer/form-administration/task/t_CreateATemplateUsingTheTmplForm.html ].

작업 그룹을 만들려면 다음을 수행 합니다.

1. Azure Portal에서  **경고** 를 선택 합니다.
2. 화면 위쪽의 메뉴에서 **작업 관리** 를 선택 합니다.

    ![작업 관리 메뉴 항목을 보여 주는 스크린샷](media/itsmc-overview/action-groups-selection-big.png)

   **작업 그룹 만들기** 창이 나타납니다.

3. 작업 그룹을 만들려는 **구독** 및 **리소스 그룹** 을 선택 합니다. 작업 그룹 **이름** 및 작업 그룹의 **표시 이름** 에 값을 제공 합니다. 그런 후 **다음: 알림** 을 선택 합니다.

    ![작업 그룹 만들기 창을 보여 주는 스크린샷](media/itsmc-overview/action-groups-details.png)

4. **알림** 탭에서 **다음: 작업** 을 선택 합니다.
5. **작업** 탭의 **동작 유형** 목록에서 **itsm** 을 선택 합니다. **이름** 에 동작의 이름을 입력 합니다. 그런 다음 **편집 세부 정보** 를 나타내는 펜 단추를 선택 합니다.

    ![작업 그룹을 만들기 위한 선택 항목을 보여 주는 스크린샷](media/itsmc-definition/action-group-pen.png)

6. **구독** 목록에서 Log Analytics 작업 영역을 포함 하는 구독을 선택 합니다. **연결** 목록에서 itsm 커넥터 이름을 선택 합니다. 그러면 작업 영역 이름이 뒤에 나옵니다. 예를 들어 *Myitsmconnector (MyWorkspace)* 가 있습니다.

7. **작업 항목** 형식을 선택 합니다.

8. 고정 값을 사용 하 여 기본 필드를 입력 하려면 **사용자 지정 템플릿 사용** 을 선택 합니다. 그렇지 않으면 **템플릿** 목록에서 기존 [템플릿을](#define-a-template) 선택 하 고 템플릿 필드에 고정 값을 입력 합니다.

9. ITSM 작업 그룹을 만들기 위한 인터페이스의 마지막 섹션에서 각 경고에 대해 생성 되는 작업 항목 수를 정의할 수 있습니다.

   > [!NOTE]
   > 이 섹션에서는 로그 검색 경고와 관련 된 내용만 설명 합니다. 다른 모든 경고 유형의 경우 경고 당 하나의 작업 항목을 만듭니다.

   * **작업 항목** 드롭다운 목록에서 **인시던트** 또는 **경고** 를 선택한 경우 각 구성 항목에 대해 개별 작업 항목을 만드는 옵션이 있습니다.
    
     ![작업 항목으로 선택 된 인시던트가 포함 된 I T S M 티켓 영역을 보여 주는 스크린샷](media/itsmc-overview/itsm-action-configuration.png)
    
     * **각 구성 항목에 대해 개별 작업 항목 만들기** 확인란을 선택 하는 경우 모든 경고의 모든 구성 항목이 새 작업 항목을 만듭니다. 영향을 받는 구성 항목에 대해 여러 경고가 발생 하므로 각 구성 항목에 대해 둘 이상의 작업 항목이 있습니다.

       예를 들어 세 개의 구성 항목을 포함 하는 경고는 세 개의 작업 항목을 만듭니다. 하나의 구성 항목을 포함 하는 경고는 하나의 작업 항목을 만듭니다.
        
     * **각 구성 항목에 대 한 개별 작업 항목 만들기** 확인란의 선택을 취소 하면 itsmc는 각 경고 규칙에 대 한 단일 작업 항목을 만들고 영향을 받는 모든 구성 항목에 추가 합니다. 이전 작업 항목이 닫히면 새 작업 항목이 생성 됩니다.

       >[!NOTE]
       > 이 경우 발생 한 일부 경고는 ITSM 도구에 새 작업 항목을 생성 하지 않습니다.

       예를 들어 세 개의 구성 항목을 포함 하는 경고는 하나의 작업 항목을 만듭니다. 이전 예제와 동일한 경고 규칙에 대 한 경고에 하나의 구성 항목이 있는 경우 해당 구성 항목은 만든 작업 항목의 영향을 받는 구성 항목 목록에 연결 됩니다. 하나의 구성 항목을 포함 하는 다른 경고 규칙에 대 한 경고는 하나의 작업 항목을 만듭니다.

   * **작업 항목** 드롭다운 목록에서 **이벤트** 를 선택한 경우 각 로그 항목에 대 한 개별 작업 항목 또는 각 구성 항목에 대해 개별 작업 항목을 만들도록 선택할 수 있습니다.
    
     ![이벤트가 작업 항목으로 선택 된 상태에서 I M S M 티켓 영역을 보여 주는 스크린샷](media/itsmc-overview/itsm-action-configuration-event.png)

     * **각 로그 항목에 대해 개별 작업 항목 만들기를 선택 하는 경우 (구성 항목 필드는 채워지지 않습니다. 많은 수의 작업 항목을 생성할 수 있습니다.)** 를 사용 하면 로그 검색 경고 쿼리의 검색 결과에 각 행에 대 한 작업 항목이 생성 됩니다. 작업 항목 페이로드의 설명 속성에는 검색 결과의 행이 포함 됩니다.
      
     * **각 구성 항목에 대해 개별 작업 항목 만들기** 를 선택 하는 경우 모든 경고의 모든 구성 항목이 새 작업 항목을 만듭니다. 각 구성 항목에는 ITSM 시스템에 둘 이상의 작업 항목이 있을 수 있습니다. 이 옵션은 작업 항목 형식으로 **인시던트** 를 선택한 후에 표시 되는 확인란을 선택 하는 것과 동일 합니다.

10. **확인** 을 선택합니다.

Azure 경고 규칙을 만들거나 편집할 때는 ITSM 작업이 있는 작업 그룹을 사용 합니다. 경고가 트리거될 때 작업 항목이 ITSM 도구에서 만들어지거나 업데이트 됩니다.

> [!NOTE]
> ITSM 작업의 가격 책정에 대 한 자세한 내용은 작업 그룹에 대 한 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/monitor/) 를 참조 하세요.
>
> ITSM 작업을 사용 하 여 보낼 때 경고 규칙 정의의 간단한 설명 필드는 40 자로 제한 됩니다.

## <a name="next-steps"></a>다음 단계

* [ITSMC의 문제 해결](./itsmc-resync-servicenow.md)
