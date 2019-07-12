---
title: Azure Migrate에서 마이그레이션 도구 추가
description: Azure Migrate 허브에서 마이그레이션 도구를 추가 하는 방법에 설명 합니다.
author: rayne-wiselman
ms.service: azure-migrate
ms.manager: carmonm
ms.topic: article
ms.date: 07/09/2019
ms.author: raynew
ms.openlocfilehash: b3c77f052ed92db235b363e63859b9beb9e4f5a2
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811870"
---
# <a name="add-migration-tools"></a>마이그레이션 도구를 추가 합니다.

이 문서에서는 설명의 마이그레이션 도구를 추가 하는 방법 [Azure Migrate](migrate-overview.md)합니다.

Azure Migrate는 Azure를 평가 및 마이그레이션을 위해 도구의 허브를 제공합니다. 다른 Azure 서비스 및 타사 독립 소프트웨어 공급 업체 (ISV) 제품으로 제공 된 도구 네이티브 도구를 포함 합니다.

마이그레이션 도구를 추가 하려면 Azure Migrate 프로젝트를 아직 설정 하지 않은 경우,이 따라 [문서](how-to-add-tool-first-time.md)합니다.



## <a name="selecting-an-isv-tool"></a>ISV 도구 선택

원하는 경우는 [ISV 도구](migrate-services-overview.md#isv-integration) 마이그레이션에 대 한 라이선스를 얻거나 ISV 정책에 따라 무료 평가판에 등록 하 여 시작할 수 있습니다. 각 도구에서 Azure Migrate에 연결 하는 옵션이 있습니다. 도구를 배포 하 고 Azure Migrate로 도구 작업 영역을 연결할 도구 지침 및 설명서를 따릅니다. 

## <a name="select-a-migration-scenario"></a>마이그레이션 시나리오를 선택 합니다.

1. Azure Migrate 프로젝트에서 클릭 **개요**합니다.
2. 사용 하려는 마이그레이션 시나리오를 선택 합니다.

    - 컴퓨터 및 워크 로드를 Azure로 마이그레이션할, 선택 **평가 서버를 마이그레이션하고**합니다.
    - 온-프레미스 SQL 컴퓨터를 마이그레이션하도록 선택 **평가 데이터베이스 마이그레이션 및**합니다.
    - 온-프레미스 웹 앱을 마이그레이션하도록 선택 **평가 웹 앱을 마이그레이션 및**합니다.
    - 오프 라인 모드에서 Azure로 대량의 온-프레미스 데이터를 마이그레이션, 선택 **Data Box 주문**합니다.

    ![평가 시나리오](./media/how-to-migrate/assess-scenario.png)

## <a name="select-a-server-migration-tool"></a>서버 마이그레이션 도구를 선택 합니다.

1. 클릭 **평가 하 고 서버를 마이그레이션하려면**합니다.
2. **Azure Migrate-서버**마이그레이션 도구를 아직 추가 하지 않은 경우, 아래 **마이그레이션 도구**를 선택 **마이그레이션 도구를 추가 하려면 여기를 클릭 하십시오.** 합니다. 마이그레이션 도구를 이미 추가한 경우 **자세한 마이그레이션 도구를 추가**를 선택 **변경**합니다.

    > [!NOTE]
    > 다른 프로젝트를 이동 해야 하는 경우 **Azure Migrate-서버**옆에 **다양 한 마이그레이션 프로젝트에 대 한 자세한 내용은**, 클릭 **여기를 클릭**합니다.

3. **Azure Migrate**, 사용 하려는 마이그레이션 도구를 선택 합니다.
    - Azure 마이그레이션 Server Migration을 사용 하는 경우 설정 하 고 Azure Migrate 프로젝트에서 직접 마이그레이션을 실행 수 있습니다.
    - 타사 평가 도구를 사용 하는 경우 ISV에 대 한 제공 된 링크로 이동한 제공 하는 지침에 따라 마이그레이션을 실행 합니다.

## <a name="select-a-database-migration-tool"></a>데이터베이스 마이그레이션 도구를 선택 합니다.

1. 클릭 **평가 및 데이터베이스 마이그레이션**
2. **데이터베이스**, 클릭 **도구 추가**합니다.
3. 추가 도구 > **선택 마이그레이션 도구**, 데이터베이스를 마이그레이션하는 데 사용할 도구를 선택 합니다.

## <a name="select-a-web-app-migration-tool"></a>웹 앱 마이그레이션 도구를 선택 합니다.

1. 클릭 **평가 웹 앱을 마이그레이션 및**합니다.
2. 마이그레이션 도구를 Azure App Service에 대 한 링크를 따릅니다. 마이그레이션 도구를 사용 합니다.

    - **온라인 앱 평가**: 평가 하 고 Azure App Service Migration Assistant를 사용 하 여 온라인으로 공용 URL 사용 하 여 앱을 마이그레이션할 수 있습니다.
    - **.NET/PHP**: 내부.NET 및 PHP 응용 프로그램에 대 한 다운로드 하 고 Migration Assistant를 실행할 수 있습니다.

## <a name="order-an-azure-data-box"></a>Azure Data Box를 주문

많은 양의 데이터를 Azure로 마이그레이션할, 오프 라인 데이터 전송에 대 한 Azure DAta Box를 주문할 수 있습니다.

1. 클릭 **Data Box 주문**합니다.
2. **Azure Data Box 선택**, 구독을 지정 합니다. 
3. 전송에 Azure로 가져오기 됩니다. 데이터 원본 및 데이터에 대 한 Azure 지역 대상을 지정 합니다.

## <a name="next-steps"></a>다음 단계

마이그레이션에 대 한 Azure 마이그레이션 서버 마이그레이션을 사용 하 여 사용해 보십시오 [하이퍼-V](tutorial-migrate-hyper-v.md) 하거나 [VMware](tutorial-migrate-vmware.md) Vm.
