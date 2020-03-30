---
title: Azure 마이그레이션에서 마이그레이션 도구 추가
description: Azure 마이그레이션에서 마이그레이션 도구를 추가하는 방법을 알아봅니다.
author: rayne-wiselman
ms.service: azure-migrate
ms.manager: carmonm
ms.topic: article
ms.date: 11/18/2019
ms.author: raynew
ms.openlocfilehash: 6bcb65912627356215769ccc8380ee1a7404f6f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74185958"
---
# <a name="add-migration-tools"></a>마이그레이션 도구 추가

이 문서에서는 Azure 마이그레이션 에서 마이그레이션 도구를 추가하는 방법을 [설명합니다.](migrate-overview.md)

Azure 마이그레이션은 Azure로의 평가 및 마이그레이션을 위한 도구 허브를 제공합니다. 여기에는 기본 도구, 다른 Azure 서비스에서 제공하는 도구 및 타사 독립 소프트웨어 공급업체(ISV) 제품이 포함됩니다.

마이그레이션 도구를 추가하고 Azure 마이그레이션 프로젝트를 아직 설정하지 않은 경우 이 [문서를](how-to-add-tool-first-time.md)따르십시오.



## <a name="selecting-an-isv-tool"></a>ISV 도구 선택

마이그레이션을 위한 [ISV 도구를](migrate-services-overview.md#isv-integration) 선택하는 경우 ISV 정책에 따라 라이선스를 획득하거나 무료 평가판에 등록하여 시작할 수 있습니다. 각 도구에는 Azure Migrate에 연결할 수 있는 옵션이 있습니다. 도구를 배포하고 도구 지침 및 설명서를 따라 도구 작업 영역을 Azure 마이그레이션과 연결합니다. 

## <a name="select-a-migration-scenario"></a>마이그레이션 시나리오 선택

1. Azure Migrate 프로젝트에서 **개요**를 클릭합니다.
2. 사용할 마이그레이션 시나리오를 선택합니다.

    - 컴퓨터 및 워크로드를 Azure로 마이그레이션하려면 **서버 평가 및 마이그레이션을 선택합니다.**
    - 온-프레미스 SQL 컴퓨터를 마이그레이션하려면 **데이터베이스 평가 및 마이그레이션을 선택합니다.**
    - 온-프레미스 웹 앱을 마이그레이션하려면 **웹 앱 평가 및 마이그레이션을**선택합니다.
    - 오프라인 모드에서 Azure로 많은 양의 온-프레미스 데이터를 마이그레이션하려면 **데이터 상자 순서를**선택합니다.

    ![평가 시나리오](./media/how-to-migrate/assess-scenario.png)

## <a name="select-a-server-migration-tool"></a>서버 마이그레이션 도구 선택

1. **서버 평가 및 마이그레이션을 클릭합니다.**
2. **Azure 마이그레이션 - 서버에서**마이그레이션 도구에서 아직 마이그레이션 도구를 **Migration tools**추가하지 않은 경우 **여기를 클릭하여 마이그레이션 도구를 추가합니다.** 이미 마이그레이션 도구를 추가한 경우 마이그레이션 도구 추가에서 **변경 을** **선택합니다.**

    > [!NOTE]
    > **Azure 마이그레이션 - 서버에서**다른 프로젝트로 이동해야 하는 경우 **다른 마이그레이션 프로젝트에 대한 세부 정보 보기**옆의 여기를 **클릭하십시오.**

3. **Azure 마이그레이션에서**사용할 마이그레이션 도구를 선택합니다.
    - Azure 마이그레이션 서버 마이그레이션을 사용하는 경우 Azure 마이그레이션 프로젝트에서 직접 마이그레이션을 설정하고 실행할 수 있습니다.
    - 타사 평가 도구를 사용하는 경우 ISV에 제공된 링크를 탐색하고 제공된 지침에 따라 마이그레이션을 실행합니다.

## <a name="select-a-database-migration-tool"></a>데이터베이스 마이그레이션 도구 선택

1. **데이터베이스 평가 및 마이그레이션을 클릭합니다.**
2. 데이터베이스에서 도구 **추가를** **클릭합니다.**
3. 이동 선택 **도구**> 도구 추가 도구에서 데이터베이스를 마이그레이션하는 데 사용할 도구를 선택합니다.

## <a name="select-a-web-app-migration-tool"></a>웹 앱 마이그레이션 도구 선택

1. **웹 앱 평가 및 마이그레이션을 클릭합니다.**
2. Azure 앱 서비스의 마이그레이션 도구에 대한 링크를 따릅니다. 마이그레이션 도구를 사용하여 다음을 수행합니다.

    - **온라인 앱 평가**: Azure 앱 서비스 마이그레이션 도우미를 사용하여 공용 URL을 사용하여 앱을 온라인으로 평가하고 마이그레이션할 수 있습니다.
    - **.NET/PHP**: 내부 .NET 및 PHP 앱의 경우 마이그레이션 도우미를 다운로드하여 실행할 수 있습니다.

## <a name="order-an-azure-data-box"></a>Azure 데이터 상자 주문

많은 양의 데이터를 Azure로 마이그레이션하려면 오프라인 데이터 전송을 위해 Azure DAta Box를 주문할 수 있습니다.

1. **데이터 상자 순서를 클릭합니다.**
2. **Azure 데이터 상자 선택에서**구독을 지정합니다. 
3. 전송은 Azure로 가져오기입니다. 데이터에 대한 데이터 원본 및 Azure 지역 대상을 지정합니다.

## <a name="next-steps"></a>다음 단계

[하이퍼 V](tutorial-migrate-hyper-v.md) 또는 [VM웨어](tutorial-migrate-vmware.md) VM에 대한 서버 마이그레이션을 Azure 마이그레이션을 사용하여 마이그레이션을 사용해 보십시오.
