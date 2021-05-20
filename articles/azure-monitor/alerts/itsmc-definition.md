---
title: Log Analytics의 IT 서비스 관리 커넥터
description: 이 문서에서는 ITSMC(IT 서비스 관리 커넥터)의 개요 및 이를 사용하여 Log Analytics에서 ITSM 작업 항목을 모니터 및 관리하고 문제를 신속하게 해결하는 방법에 대한 정보를 제공합니다.
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 05/24/2018
ms.custom: references_regions
ms.openlocfilehash: 98f53ec1b6506a6d47146377e837576254f445e2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103601069"
---
# <a name="connect-azure-to-itsm-tools-by-using-it-service-management-solution"></a>IT 서비스 관리 솔루션을 사용하여 ITSM 도구에 Azure 연결

:::image type="icon" source="media/itsmc-overview/itsmc-symbol.png":::

이 문서에서는 ITSM(IT 서비스 관리) 작업 항목을 중앙에서 관리하기 위해 Log Analytics에서 ITSMC(IT 서비스 관리 커넥터)를 구성하는 방법에 대한 정보를 제공합니다.

## <a name="add-it-service-management-connector"></a>IT 서비스 관리 커넥터 추가

연결을 만들 수 있으려면 ITSMC를 설치해야 합니다.

1. Azure Portal에서 **리소스 만들기** 를 선택합니다.

   ![리소스를 만들기 위한 메뉴 항목을 보여주는 스크린샷입니다.](media/itsmc-overview/azure-add-new-resource.png)

2. Azure Marketplace에서 **IT 서비스 관리 커넥터** 를 검색합니다. **만들기** 를 선택합니다.

   ![Azure Marketplace에서 만들기 단추를 보여주는 스크린샷입니다.](media/itsmc-overview/add-itsmc-solution.png)

3. **LA 작업 영역** 섹션에서 ITSMC를 설치하려는 Log Analytics 작업 영역을 선택합니다.
   > [!NOTE]
   > Log Analytics 작업 영역에 ITSMC를 설치할 수 있는 지역은 미국 동부, 미국 서부 2, 미국 중남부, 미국 중서부, US Gov 애리조나, US Gov 버지니아, 캐나다 중부, 서유럽, 영국 남부, 동남 아시아, 일본 동부, 인도 중부 및 오스트레일리아 남동부 지역입니다.

4. **Log Analytics 작업 영역** 섹션에서 ITSMC 리소스를 만들려는 리소스 그룹을 선택합니다.

   ![Log Analytics 작업 영역 섹션을 보여주는 스크린샷입니다.](media/itsmc-overview/itsmc-solution-workspace.png)
   
   > [!NOTE]
   > Microsoft OMS(Operations Management Suite)에서 Azure Monitor로의 진행 중인 전환에 따라 OMS 작업 영역은 이제 *Log Analytics 작업 영역* 이라고 부릅니다.

5. **확인** 을 선택합니다.

ITSMC 리소스가 배포되면 창 오른쪽 위 모서리에 알림이 표시됩니다.

## <a name="create-an-itsm-connection"></a>ITSM 연결 만들기

ITSMC를 설치한 경우 ITSMC에서의 연결을 허용하도록 ITSM 도구를 준비해야 합니다. 연결하려는 ITSM 제품을 기준으로 다음 링크 중 하나를 선택하여 지침을 확인합니다.

- [ServiceNow](./itsmc-connections-servicenow.md)
- [System Center Service Manager](./itsmc-connections-scsm.md)
- [Cherwell](./itsmc-connections-cherwell.md)
- [Provance](./itsmc-connections-provance.md)

ITSM 도구를 준비한 후 이 단계를 완료하여 연결을 만듭니다.

1. **모든 리소스** 에서 **ServiceDesk(*작업 영역 이름*)** 를 찾습니다.

   ![Azure Portal의 최신 리소스를 보여주는 스크린샷입니다.](media/itsmc-definition/create-new-connection-from-resource.png)

1. 왼쪽 창의 **작업 영역 데이터 소스** 아래에서 **ITSM 연결** 을 선택합니다.

   ![ITSM 연결 메뉴 항목을 보여주는 스크린샷입니다.](media/itsmc-overview/add-new-itsm-connection.png)

1. **연결 추가** 를 선택합니다.

1. 사용 중인 ITSM 제품에 따라 연결 설정을 지정합니다.

    - [ServiceNow](./itsmc-connections-servicenow.md)
    - [System Center Service Manager](./itsmc-connections-scsm.md)
    - [Cherwell](./itsmc-connections-cherwell.md)
    - [Provance](./itsmc-connections-provance.md)

   > [!NOTE]
   > 기본적으로 ITSMC는 24시간 마다 한 번씩 연결의 구성 데이터를 새로 고칩니다. 수정한 내용 또는 템플릿 업데이트 내용을 즉시 반영하기 위해 연결 데이터를 새로 고치려면 연결 창에서 **동기화** 단추를 선택합니다.
   >
   > ![연결 창의 동기화 단추를 보여주는 스크린샷입니다.](media/itsmc-overview/itsmc-connections-refresh.png)

## <a name="create-itsm-work-items-from-azure-alerts"></a>Azure Alerts에서 ITSM 작업 항목 만들기

ITSM 연결을 만든 후 ITMC를 사용하여 Azure 경고를 기준으로 ITSM 도구에서 작업 항목을 만들 수 있습니다. 작업 항목을 만들려면 작업 그룹에서 ITSM 작업을 사용합니다.

작업 그룹은 Azure 경고에 대해 모듈식으로 그리고 재사용 가능한 방식으로 작업을 트리거할 수 있는 방법을 제공합니다. Azure Portal에서 메트릭 경고, 작업 로그 경고, Log Analytics 경고에 작업 그룹을 사용할 수 있습니다.

> [!NOTE]
> ITSM 연결을 만든 후 동기화 프로세스가 완료될 때까지 30분 정도 기다려야 합니다.

## <a name="define-a-template"></a>템플릿 정의

일부 작업 항목 유형은 ITSM 도구에서 정의하는 템플릿을 사용할 수 있습니다. 템플릿을 사용하여 작업 그룹의 고정 값에 따라 자동으로 채워지는 필드를 정의할 수 있습니다. 작업 그룹을 정의할 때 사용할 템플릿을 정의할 수 있습니다. 템플릿을 만드는 방법에 대한 정보는 ServiceNow 문서에서 찾을 수 있습니다. - (여기)[https://docs.servicenow.com/bundle/paris-platform-administration/page/administer/form-administration/task/t_CreateATemplateUsingTheTmplForm.html ].

작업 그룹을 만들려면 다음을 수행하세요.

1. Azure Portal에서 **경고** 를 선택합니다.
2. 화면 위쪽 메뉴에서 **관리 작업** 을 선택합니다.

    ![관리 작업 메뉴 항목을 보여주는 스크린샷입니다.](media/itsmc-overview/action-groups-selection-big.png)

   **작업 그룹 만들기** 창이 표시됩니다.

3. 작업 그룹을 만들려는 **구독** 및 **리소스 그룹** 을 선택합니다. 작업 그룹에 대해 **작업 그룹 이름** 및 **표시 이름** 에 값을 제공합니다. 그런 후 **다음: 알림** 을 선택합니다.

    ![작업 그룹 만들기 창을 보여주는 스크린샷입니다.](media/itsmc-overview/action-groups-details.png)

4. **알림** 탭에서 **다음: 작업** 을 선택합니다.
5. **작업** 탭의 **작업 유형** 목록에서 **ITSM** 을 선택합니다. **이름** 에 작업 이름을 제공합니다. 그런 후 **세부 정보 편집** 을 나타내는 펜 단추를 선택합니다.

    ![작업 그룹 만들기에 대한 선택 항목을 보여주는 스크린샷입니다.](media/itsmc-definition/action-group-pen.png)

6. **구독** 목록에서 Log Analytics 작업 영역이 포함된 구독을 선택합니다. **연결** 목록에서 ITSM 커넥터 이름을 선택합니다. 그 다음 작업 영역 이름이 표시됩니다. 예를 들면 *MyITSMConnector(MyWorkspace)* 입니다.

7. **작업 항목** 유형을 선택합니다.

8. 즉시 사용 가능한 필드에 고정 값을 채우려면 **사용자 지정 템플릿 사용** 을 선택합니다. 그렇지 않으면 **템플릿** 목록에서 기존 [템플릿](#define-a-template)을 선택하고 템플릿 필드에 고정 값을 입력합니다.

9. ITSM 작업 그룹 만들기를 위해 인터페이스의 마지막 섹션에서 각 경고에 대해 생성할 작업 항목 수를 정의할 수 있습니다.

   > [!NOTE]
   > 이 섹션은 로그 검색 경고와만 관련이 있습니다. 다른 모든 경고 유형의 경우 경고당 하나의 작업 항목을 만듭니다.

   * **작업 항목** 드롭다운 목록에서 **인시던트** 또는 **경고** 를 선택한 경우 각 구성 항목에 대해 개별 작업 항목을 만들 수 있습니다.
    
     ![작업 항목으로 선택된 인시던트가 포함된 ITSM 티켓 영역을 보여주는 스크린샷입니다.](media/itsmc-overview/itsm-action-configuration.png)
    
     * **각 구성 항목에 대해 개별 작업 항목 만들기** 확인란을 선택하면 모든 경고의 모든 구성 항목이 새 작업 항목을 만듭니다. 영향을 받는 동일한 구성 항목에 대해 여러 경고가 발생하기 때문에 각 구성 항목에 대해 둘 이상의 작업 항목이 존재합니다.

       예를 들어 구성 항목이 3개인 경고는 작업 항목을 3개 만듭니다. 하나의 구성 항목을 포함하는 경고는 하나의 작업 항목을 만듭니다.
        
     * **각 구성 항목에 대해 개별 작업 항목 만들기** 확인란을 선택 취소하면 ITSMC가 각 경고 규칙에 대해 단일 작업 항목을 만들고 영향을 받는 모든 구성 항목에 추가합니다. 이전 항목이 닫히면 새 작업 항목이 생성됩니다.

       >[!NOTE]
       > 이 경우 일부 발생한 경고가 ITSM 도구에서 새 작업 항목을 생성하지 않습니다.

       예를 들어 3개의 구성 항목을 포함하는 경고가 하나의 작업 항목을 만듭니다. 이전 예제와 동일한 경고 규칙에 대해 경고에 하나의 구성 항목이 포함된 경우 생성된 작업 항목에 있는 영향을 받는 항목 목록에 해당 구성 항목이 추가됩니다. 하나의 구성 항목을 포함하는 다른 경고 규칙에 대한 경고가 하나의 작업 항목을 만듭니다.

   * **작업 항목** 드롭다운 목록에서 **이벤트** 를 선택한 경우 각 로그 항목 또는 각 구성 항목에 대해 개별 작업 항목을 만들도록 선택할 수 있습니다.
    
     ![이벤트가 작업 항목으로 선택된 ITSM 티켓 영역을 보여주는 스크린샷입니다.](media/itsmc-overview/itsm-action-configuration-event.png)

     * **각 로그 항목에 개별 작업 항목 만들기(구성 항목 필드가 채워지지 않습니다. 작업 항목 수가 증가할 수 있습니다.)** 를 선택한 경우 로그 검색 경고 쿼리의 검색 결과에서 각 행에 대해 작업 항목이 생성됩니다. 작업 항목의 페이로드에 있는 설명 속성에는 검색 결과의 행이 포함됩니다.
      
     * **각 구성 항목에 대해 개별 작업 항목 만들기** 를 선택하면 모든 경고의 각 구성 항목이 새 작업 항목을 만듭니다. 각 구성 항목은 ITSM 시스템의 2개 이상의 작업 항목을 포함할 수 있습니다. 이 옵션은 작업 항목 유형으로 **인시던트** 를 선택한 후 표시되는 확인란을 선택하는 것과 동일합니다.

10. **확인** 을 선택합니다.

Azure 경고 규칙을 만들거나 편집할 때는 ITSM 작업을 갖는 작업 그룹을 사용합니다. 경고가 트리거되면 ITSM 도구에서 작업 항목이 생성되거나 업데이트됩니다.

> [!NOTE]
> ITSM 작업의 가격 책정에 대한 자세한 내용은 작업 그룹의 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/monitor/)를 참조하세요.
>
> ITSM 작업을 사용하여 보낼 때 경고 규칙 정의의 간단한 설명 필드는 40자로 제한됩니다.

## <a name="next-steps"></a>다음 단계

* [ITSMC에서 문제 해결](./itsmc-resync-servicenow.md)
