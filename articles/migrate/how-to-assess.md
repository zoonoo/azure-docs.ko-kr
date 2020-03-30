---
title: Azure 마이그레이션에서 평가 도구 추가
description: Azure 마이그레이션에서 평가 도구를 추가하는 방법을 알아봅니다.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.manager: carmonm
ms.date: 11/19/2019
ms.author: raynew
ms.openlocfilehash: 64af78abd8f82b41d4a03fbb56c96e3038cef5a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74185905"
---
# <a name="add-assessment-tools"></a>평가 도구 추가

이 문서에서는 [Azure 마이그레이션](migrate-overview.md)에서 평가 도구를 추가하는 방법에 대해 설명합니다.

Azure 마이그레이션은 Azure로의 평가 및 마이그레이션을 위한 도구 허브를 제공합니다. 여기에는 Azure 마이그레이션 도구뿐만 아니라 다른 도구 및 ISV(독립 소프트웨어 공급업체) 제품이 포함됩니다.

평가 도구를 추가하려는 데 아직 Azure 마이그레이션 프로젝트가 없는 경우 이 [문서를](how-to-add-tool-first-time.md)따르십시오.

## <a name="select-a-tool"></a>도구 선택

평가를 위해 Azure가 아닌 마이그레이션 도구를 선택하는 경우 도구 정책에 따라 라이선스를 얻거나 무료 평가판에 등록하는 것으로 시작합니다. 도구에는 Azure 마이그레이션에 연결하는 옵션이 있습니다. 지침 및 설명서를 따라 도구를 Azure 마이그레이션에 연결합니다. 도구에 대해 [자세히 알아보세요.](migrate-services-overview.md)


## <a name="select-an-assessment-scenario"></a>평가 시나리오 선택

1. Azure Migrate 프로젝트에서 **개요**를 클릭합니다.
2. 사용할 평가 시나리오를 선택합니다.

    - Azure로 마이그레이션할 컴퓨터 및 워크로드를 검색하고 평가하려면 **서버 평가 및 마이그레이션을 선택합니다.**
    - 온-프레미스 SQL 컴퓨터를 평가하려면 **데이터베이스 평가 및 마이그레이션을 선택합니다.**
    - 온-프레미스 웹 앱을 평가하려면 **웹 앱 평가 및 마이그레이션을 선택합니다.**

    ![평가 시나리오](./media/how-to-assess/assess-scenario.png)

## <a name="select-a-server-assessment-tool"></a>서버 평가 도구 선택 

1. **서버 평가 및 마이그레이션을 클릭합니다.**
2. **Azure 마이그레이션 - 서버에서** **평가**도구에서 평가 도구를 추가하지 않은 경우 여기를 클릭하여 평가 **도구를 추가합니다.** 평가 도구를 이미 추가한 경우 평가 도구 추가에서 **변경 을** **선택합니다.**

    > [!NOTE]
    > **Azure 마이그레이션 - 서버에서**다른 프로젝트로 이동해야 하는 경우 **다른 마이그레이션 프로젝트에 대한 세부 정보 보기**옆의 여기를 **클릭하십시오.**

3. **Azure 마이그레이션에서**사용할 평가 도구를 선택합니다.

    - Azure 마이그레이션 서버 평가를 사용하는 경우 Azure 마이그레이션 프로젝트에서 직접 평가를 설정, 실행 및 볼 수 있습니다.
    - 다른 평가 도구를 사용하는 경우 해당 사이트에 제공된 링크로 이동하여 제공한 지침에 따라 평가를 실행합니다.


## <a name="select-a-database-assessment-tool"></a>데이터베이스 평가 도구 선택

1. **데이터베이스 평가 및 마이그레이션을 클릭합니다.**
2. 데이터베이스에서 도구 **추가를** **클릭합니다.**
3. **평가 선택 도구**> 도구 추가에서 데이터베이스를 평가하는 데 사용할 도구를 선택합니다.

## <a name="select-a-web-app-assessment-tool"></a>웹 앱 평가 도구 선택

1. **웹 앱 평가 및 마이그레이션을 클릭합니다.**
2. Azure 앱 서비스의 마이그레이션 도구에 대한 링크를 따릅니다. 마이그레이션 도구를 사용하여 다음을 수행합니다.

    - **온라인 앱 평가**: Azure 앱 서비스 마이그레이션 도우미를 사용하여 온라인에서 공용 URL을 사용하여 앱을 평가할 수 있습니다.
    - **.NET/PHP**: 내부 .NET 및 PHP 앱의 경우 마이그레이션 도우미를 다운로드하여 실행할 수 있습니다.



## <a name="next-steps"></a>다음 단계

[VMware](tutorial-prepare-vmware.md) VM, [Hyper-V](tutorial-prepare-hyper-v.md)또는 [물리적 서버에](tutorial-prepare-physical.md) 대한 Azure 마이그레이션 서버 평가를 사용하여 평가를 사용해 보십시오.
