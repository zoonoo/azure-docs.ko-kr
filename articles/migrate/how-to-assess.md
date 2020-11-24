---
title: Azure Migrate에서 평가 도구 추가
description: Azure Migrate에서 평가 도구를 추가 하는 방법에 대해 알아봅니다.
ms.topic: how-to
ms.date: 11/23/2020
ms.openlocfilehash: 17113d167476c62a97031721b73183d0b0da18af
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2020
ms.locfileid: "95535033"
---
# <a name="add-assessment-tools"></a>평가 도구 추가

이 문서에서는 [Azure Migrate](./migrate-services-overview.md)에서 평가 도구를 추가 하는 방법을 설명 합니다. 

- 평가 도구를 추가 하려는 데 아직 Azure Migrate 프로젝트가 없는 경우이 [문서](create-manage-projects.md)를 따르세요.
- ISV 도구 또는 Movere를 추가한 경우에는이 도구를 사용 하 여 작업을 준비 하는 [단계를 수행](prepare-isv-movere.md)합니다.

## <a name="select-an-assessment-scenario"></a>평가 시나리오 선택

1. Azure Migrate 프로젝트에서 **개요** 를 클릭합니다.
2. 평가 시나리오를 선택 합니다.

    - Azure로의 마이그레이션을 위한 컴퓨터 및 작업을 검색 하 고 평가 하려면 **서버 평가 및 마이그레이션** 을 선택 합니다.
    - 온-프레미스 SQL Server 데이터베이스를 평가 하려면 **데이터베이스 평가 및 마이그레이션** 을 선택 합니다.
    - 온-프레미스 웹 앱을 평가 하거나 마이그레이션하려면 자세한 Web Apps **탐색** 을 선택  >  **Web Apps** 합니다.
    - 가상 데스크톱 인프라를 평가 하려면 **더 많은**  >  **가상 데스크톱 인프라** 탐색을 선택 합니다.

    ![평가 시나리오를 선택 하기 위한 옵션](./media/how-to-assess/assess-scenario.png)

## <a name="select-a-server-assessment-tool"></a>서버 평가 도구 선택 


1. 도구 추가:

    - 포털에서 **서버 평가 및 마이그레이션** 옵션을 사용 하 여 Azure Migrate 프로젝트를 만든 경우 Azure Migrate 서버 평가 도구가 프로젝트에 자동으로 추가 됩니다. 다른 평가 도구를 추가 하려면 **서버** 에서 **평가 도구** 옆에 있는 **도구 더 추가** 를 선택 합니다.
    
         ![다른 평가 도구를 추가 하는 단추](./media/how-to-assess/add-assessment-tool.png)

    - 다른 옵션을 사용 하 여 프로젝트를 만들었으며 아직 평가 도구가 없는 경우 **서버**  >  **평가 도구** 에서 **여기를 클릭** 합니다 .를 선택 합니다.

        ![첫 번째 평가 도구를 추가 하는 단추](./media/how-to-assess/no-assessment-tool.png)

2. **Azure Migrate**  >  **도구 추가** Azure Migrate에서 추가 하려는 도구를 선택 합니다. 그런 다음 **도구 추가** 를 선택 합니다.

    ![목록에서 평가 도구 선택](./media/how-to-assess/select-assessment-tool.png)



## <a name="select-a-database-assessment-tool"></a>데이터베이스 평가 도구 선택

1. 도구 추가:

    - 포털에서 **데이터베이스 평가 및 마이그레이션** 옵션을 사용 하 여 Azure Migrate 프로젝트를 만든 경우 데이터베이스 평가 도구가 프로젝트에 자동으로 추가 됩니다. 다른 평가 도구를 추가 하려면 **데이터베이스** 에서 **평가 도구** 옆에 있는 **도구 더 추가** 를 선택 합니다.

    - 다른 옵션을 사용 하 여 프로젝트를 만들고 데이터베이스 평가 도구를 아직 포함 하지 않은 **경우 데이터베이스**  >  **평가 도구** 에서 **여기를 클릭** 합니다 .를 선택 합니다.

2. **Azure Migrate**  >  **도구 추가** Azure Migrate에서 추가 하려는 도구를 선택 합니다. 그런 다음 **도구 추가** 를 선택 합니다.

    ![목록에서 데이터베이스 평가 도구 선택](./media/how-to-assess/select-database-assessment-tool.png)


## <a name="select-a-web-app-assessment-tool"></a>웹 앱 평가 도구 선택

포털에서 **더 많은** webapps 탐색 옵션을 사용 하 여 Azure Migrate 프로젝트를 만든 경우  >  **WebApps** 웹 앱 평가 도구가 프로젝트에 자동으로 추가 됩니다. 


1. 웹 앱 평가 도구가 프로젝트에 없는 경우 **Web Apps**  >  **평가 도구** 에서 **여기를 클릭** 하십시오 .를 선택 합니다.
    
    ![웹 앱 평가 도구 추가](./media/how-to-assess/no-web-app-assessment-tool.png)


2. **Azure Migrate**  >  **도구 추가** Azure Migrate에서 웹 앱 평가 도구를 선택 합니다. 그런 다음 **도구 추가** 를 선택 합니다.

    ![목록에서 데이터베이스 마이그레이션 도구를 선택 합니다.](./media/how-to-assess/select-web-app-assessment-tool.png)

 


## <a name="next-steps"></a>다음 단계

[VMware](./tutorial-discover-vmware.md) Vm, [hyper-v](./tutorial-discover-hyper-v.md)또는 [물리적 서버](./tutorial-discover-physical.md) 에 대 한 Azure Migrate 서버 평가 도구를 사용 하 여 평가에 대 한 온-프레미스 컴퓨터 검색