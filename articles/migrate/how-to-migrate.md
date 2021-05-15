---
title: Azure Migrate의 마이그레이션 도구 추가
description: Azure Migrate에서 마이그레이션 도구를 추가하는 방법을 알아봅니다.
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.topic: how-to
ms.date: 11/23/2020
ms.openlocfilehash: 97051e97ec9868f6941b579241e16e62fdd2162b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96751787"
---
# <a name="add-migration-tools"></a>마이그레이션 도구 추가

이 문서에서는 [Azure Migrate](./migrate-services-overview.md)에서 마이그레이션 도구를 추가하는 방법을 설명합니다.

- 마이그레이션 도구를 추가하고 싶고 Azure Migrate 프로젝트를 아직 설정하지 않은 경우 이 [문서](create-manage-projects.md)를 따릅니다.
- 마이그레이션을 위해 ISV 도구를 추가한 경우 [해당 단계에 따라](prepare-isv-movere.md) ISV 도구로 작업할 준비를 합니다.

## <a name="select-a-migration-scenario"></a>마이그레이션 시나리오 선택

1. Azure Migrate 프로젝트에서 **개요** 를 클릭합니다.
2. 사용하고자 하는 마이그레이션 시나리오를 선택합니다.

    - 컴퓨터 및 워크로드를 Azure로 마이그레이션하려면 **서버 평가 및 마이그레이션** 을 선택합니다.
    - 온-프레미스 데이터베이스를 마이그레이션하려면 **데이터베이스 평가 및 마이그레이션** 을 선택합니다.
    - 온-프레미스 웹앱을 평가하려면 **추가 탐색** > **웹앱** 을 선택합니다.
    - 데이터 상자를 사용하여 데이터를 Azure로 마이그레이션하려면 **추가 탐색** > **데이터 상자** 를 선택합니다.

    ![마이그레이션 시나리오 선택 옵션](./media/how-to-migrate/migrate-scenario.png)


## <a name="select-a-server-migration-tool"></a>서버 마이그레이션 도구 추가

1. 다음과 같이 도구를 추가합니다.

    - 포털에서 **서버 평가 및 마이그레이션** 옵션을 사용하여 Azure Migrate 프로젝트를 만든 경우 Azure Migrate 서버 마이그레이션 도구가 프로젝트에 자동으로 추가됩니다. 다른 마이그레이션 도구를 추가하려면 **서버** 에서 **마이그레이션 도구** 옆에 있는 **도구 더 추가** 를 선택합니다.
    
         ![다른 마이그레이션 도구를 추가하는 단추](./media/how-to-migrate/add-migration-tools.png)

    - 다른 옵션을 사용하여 프로젝트를 만들었고 마이그레이션 도구가 아직 없는 경우 **서버** > **마이그레이션 도구** 에서 **여기를 클릭** 을 선택합니다.

    ![첫 번째 마이그레이션 도구를 추가하는 단추](./media/how-to-migrate/no-migration-tool.png)

2. **Azure Migrate** > **도구 추가** 에서 추가하려는 도구를 선택합니다. 그런 다음, **도구 추가** 를 선택합니다.

    ![목록에서 평가 도구 선택](./media/how-to-migrate/select-migration-tool.png)


## <a name="select-a-database-migration-tool"></a>데이터베이스 마이그레이션 도구 선택

포털에서 **데이터베이스 평가 및 마이그레이션** 옵션을 사용하여 Azure Migrate 프로젝트를 만든 경우 데이터베이스 마이그레이션 도구가 프로젝트에 자동으로 추가됩니다. 

1. 데이터베이스 마이그레이션 도구가 프로젝트에 없는 경우 **데이터베이스** > **평가 도구** 에서 **여기를 클릭** 을 선택합니다.
    
    ![데이터베이스 마이그레이션 도구 추가](./media/how-to-migrate/no-database-migration-tool.png)


2. **Azure Migrate** > **도구 추가** 에서 데이터베이스 마이그레이션 도구를 선택합니다. 그런 다음, **도구 추가** 를 선택합니다.

    ![목록에서 데이터베이스 마이그레이션 도구 선택](./media/how-to-migrate/select-database-migration-tool.png)

    

## <a name="select-a-web-app-migration-tool"></a>웹앱 마이그레이션 도구 선택

포털에서 **추가 탐색** > **웹앱** 옵션을 사용하여 Azure Migrate 프로젝트를 만든 경우 웹앱 마이그레이션 도구가 프로젝트에 자동으로 추가됩니다. 

1. 웹앱 마이그레이션 도구가 프로젝트에 없는 경우 **웹앱** > **평가 도구** 에서 **여기를 클릭** 을 선택합니다.

    ![웹앱 마이그레이션 도구 추가](./media/how-to-migrate/no-web-app-migration-tool.png)
 

2. **Azure Migrate** > **도구 추가** 에서 웹앱 마이그레이션 도구를 선택합니다. 그런 다음, **도구 추가** 를 선택합니다.

    ![목록에서 웹앱 평가 도구 선택](./media/how-to-migrate/select-web-app-migration-tool.png)


## <a name="order-an-azure-data-box"></a>Azure Data Box 주문

대량의 데이터를 Azure로 마이그레이션하려면 오프라인 데이터 전송용 Azure Data Box를 주문할 수 있습니다.

1. **개요** 에서 **추가 탐색** 을 선택합니다.
2. **추가 탐색** 에서 **데이터 상자** 를 선택합니다.
3. **Data Box 시작** 에서 Data Box를 주문할 때 사용하려고 하는 구독 및 리소스 그룹을 선택합니다.
4. **전송 형식** 은 Azure로 가져오기입니다. 데이터가 있는 국가 및 데이터를 전송하려는 Azure 지역을 지정합니다. 
5. **적용** 을 선택하여 설정을 저장합니다.

## <a name="next-steps"></a>다음 단계

[Hyper-v](tutorial-migrate-hyper-v.md) 또는 [VMware](tutorial-migrate-vmware.md) VM용 Azure Migrate 서버 마이그레이션 도구를 사용하여 마이그레이션을 시도합니다.
