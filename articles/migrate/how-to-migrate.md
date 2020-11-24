---
title: Azure Migrate의 마이그레이션 도구 추가
description: Azure Migrate에서 마이그레이션 도구를 추가 하는 방법에 대해 알아봅니다.
ms.topic: article
ms.date: 11/23/2020
ms.openlocfilehash: 5ff5fb54e077896fb6169ad53ce29483cd2c2f89
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2020
ms.locfileid: "95545337"
---
# <a name="add-migration-tools"></a>마이그레이션 도구 추가

이 문서에서는 [Azure Migrate](./migrate-services-overview.md)의 마이그레이션 도구를 추가 하는 방법을 설명 합니다.

- 마이그레이션 도구를 추가 하 고 Azure Migrate 프로젝트를 아직 설정 하지 않은 경우이 [문서](create-manage-projects.md)를 따릅니다.
- 마이그레이션을 위해 ISV 도구를 추가한 경우 해당 단계에 [따라](prepare-isv-movere.md)도구를 사용 하 여 작업을 준비 합니다.

## <a name="select-a-migration-scenario"></a>마이그레이션 시나리오 선택

1. Azure Migrate 프로젝트에서 **개요** 를 클릭합니다.
2. 사용할 마이그레이션 시나리오를 선택 합니다.

    - 컴퓨터 및 워크 로드를 Azure로 마이그레이션하려면 **서버 평가 및 마이그레이션** 을 선택 합니다.
    - 온-프레미스 데이터베이스를 마이그레이션하려면 **데이터베이스 평가 및 마이그레이션** 을 선택 합니다.
    - 온-프레미스 웹 앱을 마이그레이션하려면 자세한 Web Apps **탐색** 을 선택  >  **Web Apps** 합니다.
    - 데이터 상자를 사용 하 여 데이터를 Azure로 마이그레이션하려면 **더 많은**  >  **데이터 탐색 상자** 를 선택 합니다.

    ![마이그레이션 시나리오를 선택 하기 위한 옵션](./media/how-to-migrate/migrate-scenario.png)


## <a name="select-a-server-migration-tool"></a>서버 마이그레이션 도구 선택

1. 도구 추가:

    - 포털에서 **서버 평가 및 마이그레이션** 옵션을 사용 하 여 Azure Migrate 프로젝트를 만든 경우 Azure Migrate 서버 마이그레이션 도구가 프로젝트에 자동으로 추가 됩니다. 추가 마이그레이션 도구를 추가 하려면 **서버** 에서 **마이그레이션 도구** 옆에 있는 **도구 더 추가** 를 선택 합니다.
    
         ![추가 마이그레이션 도구를 추가 하는 단추](./media/how-to-migrate/add-migration-tools.png)

    - 다른 옵션을 사용 하 여 프로젝트를 만들었고 마이그레이션 도구가 아직 없는 경우 **서버**  >  **마이그레이션 도구** 에서 **여기를 클릭** 합니다 .를 선택 합니다.

    ![첫 번째 마이그레이션 도구를 추가 하는 단추](./media/how-to-migrate/no-migration-tool.png)

2. **Azure Migrate**  >  **도구 추가** Azure Migrate에서 추가 하려는 도구를 선택 합니다. 그런 다음 **도구 추가** 를 선택 합니다.

    ![목록에서 평가 도구 선택](./media/how-to-migrate/select-migration-tool.png)


## <a name="select-a-database-migration-tool"></a>데이터베이스 마이그레이션 도구 선택

포털에서 **데이터베이스 평가 및 마이그레이션** 옵션을 사용 하 여 Azure Migrate 프로젝트를 만든 경우 데이터베이스 마이그레이션 도구가 프로젝트에 자동으로 추가 됩니다. 

1. 데이터베이스 마이그레이션 도구가 프로젝트에 없는 경우 **데이터베이스**  >  **평가 도구** 에서 **여기를 클릭** 하십시오 .를 선택 합니다.
    
    ![데이터베이스 마이그레이션 도구 추가](./media/how-to-migrate/no-database-migration-tool.png)


2. **Azure Migrate**  >  **도구 추가** Azure Migrate에서 데이터베이스 마이그레이션 도구를 선택 합니다. 그런 다음 **도구 추가** 를 선택 합니다.

    ![목록에서 데이터베이스 마이그레이션 도구를 선택 합니다.](./media/how-to-migrate/select-database-migration-tool.png)

    

## <a name="select-a-web-app-migration-tool"></a>웹 앱 마이그레이션 도구 선택

포털에서 **더 많은** webapps 탐색 옵션을 사용 하 여 Azure Migrate 프로젝트를 만든 경우  >  **WebApps** 웹 앱 마이그레이션 도구가 프로젝트에 자동으로 추가 됩니다. 

1. 웹 앱 마이그레이션 도구가 프로젝트에 없는 경우 **웹 앱**  >  **평가 도구** 에서 **여기를 클릭** 합니다 .를 선택 합니다.

    ![웹 앱 마이그레이션 도구 추가](./media/how-to-migrate/no-web-app-migration-tool.png)
 

2. **Azure Migrate**  >  **도구 추가** Azure Migrate에서 웹 앱 마이그레이션 도구를 선택 합니다. 그런 다음 **도구 추가** 를 선택 합니다.

    ![목록에서 webapp 평가 도구를 선택 합니다.](./media/how-to-migrate/select-web-app-migration-tool.png)


## <a name="order-an-azure-data-box"></a>Azure Data Box 주문

많은 양의 데이터를 Azure로 마이그레이션하려면 오프 라인 데이터 전송에 대 한 Azure Data Box를 주문할 수 있습니다.

1. **개요** 에서 **자세히 탐색** 을 선택 합니다.
2. **자세히 알아보기** 에서 **데이터 상자** 를 선택 합니다.
3. **Data Box 시작** 에서 Data Box 순서를 지정할 때 사용할 구독 및 리소스 그룹을 선택 합니다.
4. **전송 형식은** Azure에 대 한 가져오기입니다. 데이터가 있는 국가 및 데이터를 전송 하려는 Azure 지역을 지정 합니다. 
5. **적용** 을 클릭 하 여 설정을 저장 합니다.

## <a name="next-steps"></a>다음 단계

[Hyper-v](tutorial-migrate-hyper-v.md) 또는 [VMware](tutorial-migrate-vmware.md) vm에 대 한 Azure Migrate 서버 마이그레이션 도구를 사용 하 여 마이그레이션을 시도 합니다.
