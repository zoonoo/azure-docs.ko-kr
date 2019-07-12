---
title: Azure Migrate에서 평가 도구 추가 | Microsoft Docs
description: Azure Migrate 허브에서 평가 도구를 추가 하는 방법에 설명 합니다.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/04/2019
ms.author: raynew
ms.openlocfilehash: d176e6276d69cd3465aa4943efa86ea1e6b0736d
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811649"
---
# <a name="add-assessment-tools"></a>평가 도구를 추가 합니다.

이 문서에서는 설명의 평가 도구를 추가 하는 방법 [Azure Migrate](migrate-overview.md)합니다.

Azure Migrate는 Azure를 평가 및 마이그레이션을 위해 도구의 허브를 제공합니다. 다른 Azure 서비스 및 타사 독립 소프트웨어 공급 업체 (ISV) 제품으로 제공 된 도구 네이티브 도구를 포함 합니다.

평가 도구를 추가 하려면 Azure Migrate 프로젝트를 아직 없는 경우이 작업 수행 [문서](how-to-add-tool-first-time.md)합니다.

## <a name="selecting-an-isv-tool"></a>ISV 도구 선택

원하는 경우는 [ISV 도구](migrate-services-overview.md#isv-integration) 평가 대 한 라이선스를 얻거나 ISV 정책에 따라 무료 평가판에 등록 하 여 시작할 수 있습니다. 각 도구에서 Azure Migrate에 연결 하는 옵션이 있습니다. Azure Migrate로 도구 작업 영역 연결 도구 지침 및 설명서를 수행 합니다. 


## <a name="select-an-assessment-scenario"></a>평가 시나리오를 선택 합니다.

1. Azure Migrate 프로젝트에서 클릭 **개요**합니다.
2. 사용 하려는 평가 시나리오를 선택 합니다.

    - 검색을 컴퓨터 및 Azure로의 마이그레이션에 대 한 워크 로드 평가 **평가 서버를 마이그레이션하고**합니다.
    - 온-프레미스 SQL 컴퓨터를 평가, 선택 **평가 데이터베이스 마이그레이션 및**합니다.
    - 온-프레미스 웹 앱을 평가, 선택 **평가 웹 앱을 마이그레이션 및**합니다.

    ![평가 시나리오](./media/how-to-assess/assess-scenario.png)

## <a name="select-a-server-assessment-tool"></a>서버 평가 도구를 선택 합니다. 

1. 클릭 **평가 하 고 서버를 마이그레이션하려면**합니다.
2. **Azure Migrate-서버**아래에서 평가 도구를 추가 하지 않은 경우 **평가 도구**를 선택 **평가 도구에 추가 하려면 여기를 클릭**합니다. 평가 도구를 이미 추가한 경우 **자세한 평가 도구를 추가**를 선택 **변경**합니다.

    > [!NOTE]
    > 다른 프로젝트를 이동 해야 하는 경우 **Azure Migrate-서버**옆에 **다양 한 마이그레이션 프로젝트에 대 한 자세한 내용은**, 클릭 **여기를 클릭**합니다.

3. **Azure Migrate**를 사용 하려면 평가 도구를 선택 합니다.

    
    ![평가 도구](./media/how-to-assess/assess-tool.png)

    - Azure Migrate Server 평가 사용 하는 경우 설정 수, 실행 및 Azure Migrate 프로젝트에서 직접 평가 확인 합니다.
    - 타사 평가 도구를 사용 하는 경우 해당 사이트에 대 한 제공 된 링크로 이동한 제공 하는 지침에 따라 평가 실행 합니다.


## <a name="select-a-database-assessment-tool"></a>데이터베이스 평가 도구 선택

1. 클릭 **평가 및 데이터베이스 마이그레이션**
2. **데이터베이스**, 클릭 **도구 추가**합니다.
3. 추가 도구 > **선택 평가 도구**, 데이터베이스를 평가 하는 데 사용할 도구를 선택 합니다.

## <a name="select-a-web-app-assessment-tool"></a>웹 앱 평가 도구 선택

1. 클릭 **평가 웹 앱을 마이그레이션 및**합니다.
2. 마이그레이션 도구를 Azure App Service에 대 한 링크를 따릅니다. 마이그레이션 도구를 사용 합니다.

    - **온라인 앱 평가**: Azure App Service Migration Assistant를 사용 하 여 온라인으로 공용 URL 사용 하 여 앱을 평가할 수 있습니다.
    - **.NET/PHP**: 내부.NET 및 PHP 응용 프로그램에 대 한 다운로드 하 고 Migration Assistant를 실행할 수 있습니다.



## <a name="next-steps"></a>다음 단계

평가 대 한 Azure Migrate Server 평가 사용 하 여 사용해 보십시오 [하이퍼-V](tutorial-prepare-hyper-v.md) 하거나 [VMware](tutorial-prepare-vmware.md) Vm.
