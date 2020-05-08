---
title: Azure Migrate의 마이그레이션 도구 추가
description: Azure Migrate에서 마이그레이션 도구를 추가 하는 방법에 대해 알아봅니다.
ms.topic: article
ms.date: 04/26/2020
ms.openlocfilehash: e7d36a642f41b77e3b4c4125bae64f02def3b306
ms.sourcegitcommit: 0fda81f271f1a668ed28c55dcc2d0ba2bb417edd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2020
ms.locfileid: "82901460"
---
# <a name="add-migration-tools"></a>마이그레이션 도구 추가

이 문서에서는 [Azure Migrate](migrate-overview.md)의 마이그레이션 도구를 추가 하는 방법을 설명 합니다.

- 마이그레이션 도구를 추가 하 고 Azure Migrate 프로젝트를 아직 설정 하지 않은 경우이 [문서](how-to-add-tool-first-time.md)를 따릅니다.
- 마이그레이션을 위해 ISV 도구를 추가한 경우 해당 단계에 [따라](prepare-isv-movere.md)도구를 사용 하 여 작업을 준비 합니다.

## <a name="select-a-migration-scenario"></a>마이그레이션 시나리오 선택

1. Azure Migrate 프로젝트에서 **개요**를 클릭합니다.
2. 사용할 마이그레이션 시나리오를 선택 합니다.

    - 컴퓨터 및 워크 로드를 Azure로 마이그레이션하려면 **서버 평가 및 마이그레이션**을 선택 합니다.
    - 온-프레미스 SQL 컴퓨터를 마이그레이션하려면 **데이터베이스 평가 및 마이그레이션**을 선택 합니다.
    - 온-프레미스 웹 앱을 마이그레이션하려면 **웹 앱 평가 및 마이그레이션**을 선택 합니다.
    - 많은 양의 온-프레미스 데이터를 오프 라인 모드에서 Azure로 마이그레이션하려면 **주문 Data Box**를 선택 합니다.

    ![평가 시나리오](./media/how-to-migrate/assess-scenario.png)

## <a name="select-a-server-migration-tool"></a>서버 마이그레이션 도구 선택

1. **서버 평가 및 마이그레이션을**클릭 합니다.
2. **Azure Migrate 서버**에서 마이그레이션 도구를 아직 추가 하지 않은 경우 마이그레이션 **도구에서**마이그레이션 도구를 **추가 하려면 여기를 클릭**하십시오 .를 선택 합니다. 마이그레이션 도구를 이미 추가한 경우 추가 **마이그레이션 도구 추가**에서 **변경**을 선택 합니다.

    > [!NOTE]
    > 다른 프로젝트로 이동 해야 하는 경우 **다른 마이그레이션 프로젝트에 대 한 세부 정보 보기**옆에 있는 **Azure Migrate 서버**에서 **여기**를 클릭 하십시오 .를 클릭 합니다.

3. **Azure Migrate**에서 사용 하려는 마이그레이션 도구를 선택 합니다.
    - Azure Migrate Server 마이그레이션을 사용 하는 경우 Azure Migrate 프로젝트에서 직접 마이그레이션을 설정 하 고 실행할 수 있습니다.
    - 타사 평가 도구를 사용 하는 경우 ISV에 대해 제공 된 링크로 이동 하 여 제공 하는 지침에 따라 마이그레이션을 실행 합니다.

## <a name="select-a-database-migration-tool"></a>데이터베이스 마이그레이션 도구 선택

1. **데이터베이스 평가 및 마이그레이션을** 클릭 합니다.
2. **데이터베이스**에서 **도구 추가**를 클릭 합니다.
3. 도구 추가 > **마이그레이션 도구 선택**에서 데이터베이스를 마이그레이션하는 데 사용할 도구를 선택 합니다.

## <a name="select-a-web-app-migration-tool"></a>웹 앱 마이그레이션 도구 선택

1. **웹 앱 평가 및 마이그레이션을**클릭 합니다.
2. Azure App Service에 대 한 마이그레이션 도구의 링크를 따릅니다. 마이그레이션 도구를 사용 하 여 다음을 수행 합니다.

    - **온라인으로 앱 평가**: Azure App Service Migration Assistant를 사용 하 여 온라인 URL을 사용 하 여 앱을 평가 하 고 마이그레이션할 수 있습니다.
    - **.Net/php**: 내부 .NET 및 php 앱의 경우 Migration Assistant를 다운로드 하 여 실행할 수 있습니다.

## <a name="order-an-azure-data-box"></a>Azure Data Box 주문

Azure로 많은 양의 데이터를 마이그레이션하려면 오프 라인 데이터 전송에 대 한 Azure DAta Box를 주문할 수 있습니다.

1. **Data Box 주문을**클릭 합니다.
2. **Azure Data Box 선택**에서 구독을 지정 합니다. 
3. 전송은 Azure로 가져오기 됩니다. 데이터 원본 및 데이터에 대 한 Azure 지역 대상을 지정 합니다.

## <a name="next-steps"></a>다음 단계

[Hyper-v](tutorial-migrate-hyper-v.md) 또는 [VMware](tutorial-migrate-vmware.md) vm에 대 한 Azure Migrate 서버 마이그레이션을 사용 하 여 마이그레이션을 시도 합니다.
