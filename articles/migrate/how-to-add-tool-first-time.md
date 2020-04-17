---
title: Azure 마이그레이션에서 평가/마이그레이션 도구 추가
description: Azure 마이그레이션 프로젝트를 만들고 평가/마이그레이션 도구를 추가하는 방법을 설명합니다.
ms.topic: how-to
ms.date: 04/16/2020
ms.openlocfilehash: 48bdea31d17ea1ddf0b983af962dce30b22d8dcf
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537732"
---
# <a name="add-an-assessmentmigration-tool-for-the-first-time"></a>처음으로 평가/마이그레이션 도구에 추가

이 문서에서는 [Azure 마이그레이션](migrate-overview.md) 프로젝트에 처음으로 평가 또는 마이그레이션 도구를 추가하는 방법을 설명합니다.  
Azure Migrate는 온-프레미스 앱 및 워크로드, 프라이빗/퍼블릭 클라우드 VM을 Azure로 검색, 평가 및 마이그레이션하는 중앙 허브를 제공합니다. 허브는 평가 및 마이그레이션을 위한 Azure 마이그레이션 도구뿐만 아니라 다른 도구 및 ISV(독립 소프트웨어 공급업체) [제품을](migrate-services-overview.md#isv-integration) 제공합니다. 

## <a name="create-a-project-and-add-a-tool"></a>프로젝트 만들기 및 도구 추가

Azure 구독에서 새 Azure 마이그레이션 프로젝트를 설정하고 도구를 추가합니다.

- Azure Migrate 프로젝트는 평가하거나 마이그레이션하는 환경에서 수집된 검색, 평가 및 마이그레이션 메타데이터를 저장하는 데 사용됩니다. 
- 프로젝트에서 검색된 자산을 추적하고 평가 및 마이그레이션을 오케스트레이션할 수 있습니다.

1. Azure Portal > **모든 서비스**에서 **Azure Migrate**를 검색합니다.
2. **서비스** 아래에서 **Azure Migrate**를 선택합니다.

    ![Azure Migrate 설정](./media/how-to-add-tool-first-time/azure-migrate-search.png)

3. **개요**에서 **서버 평가 및 마이그레이션**을 클릭합니다.
4. **서버 검색, 평가 및 마이그레이션** 아래에서 **서버 평가 및 마이그레이션**을 클릭합니다.

    ![서버 검색 및 평가](./media/how-to-add-tool-first-time/assess-migrate.png)

1. **서버 검색, 평가 및 마이그레이션**에서 **도구 추가**를 클릭합니다.
2. **프로젝트 마이그레이션**에서 Azure 구독을 선택하고, 아직 없는 경우 리소스 그룹을 만듭니다.
3. **프로젝트 세부 정보에서**프로젝트 이름과 프로젝트를 작성할 지역을 지정합니다.  [공용](migrate-support-matrix.md#supported-geographies-public-cloud) 및 [정부 클라우드에](migrate-support-matrix.md#supported-geographies-azure-government)대해 지원되는 지역을 검토합니다.

    ![Azure Migrate 프로젝트 만들기](./media/how-to-add-tool-first-time/migrate-project.png)

    - 프로젝트에 대해 지정된 지리는 온-프레미스 VM에서 수집된 메타데이터를 저장하는 데 사용됩니다. 실제 마이그레이션에 대한 대상 지역을 선택할 수 있습니다.
    - 특정 지역에 프로젝트를 배포해야 하는 경우 다음 API를 사용하여 프로젝트를 만듭니다. 위치와 함께 구독 ID, 리소스 그룹 이름 및 프로젝트 이름을 지정합니다. 지역/지역을 검토하여 [공용](migrate-support-matrix.md#supported-geographies-public-cloud) 및 [정부 클라우드를 검토합니다.](migrate-support-matrix.md#supported-geographies-azure-government)

        `PUT /subscriptions/<subid>/resourceGroups/<rg>/providers/Microsoft.Migrate/MigrateProjects/<mymigrateprojectname>?api-version=2018-09-01-preview "{location: 'centralus', properties: {}}"`   


4. **다음을**클릭하고 평가 또는 마이그레이션 도구를 추가합니다.

    > [!NOTE]
    > 프로젝트를 만들 때 하나 이상의 평가 또는 마이그레이션 도구를 추가해야 합니다.

5. **평가 선택 도구에서**평가 도구를 추가합니다. 평가 도구가 필요하지 않은 경우 지금 > 다음에 대한 평가 도구 추가 **건너뛰기(건너뛰기)를****선택합니다.** 
2. **마이그레이션 선택 도구에서**필요에 따라 마이그레이션 도구를 추가합니다. 지금 당장 마이그레이션 도구가 필요하지 않은 경우 지금 > 다음에 대한 마이그레이션 도구 추가 **건너뛰기(건너뛰기)를****선택합니다.**
3. **검토 + 도구 추가**에서 설정을 검토하고, **도구 추가**를 클릭합니다.

프로젝트를 만든 후 서버 및 워크로드, 데이터베이스 및 웹 앱의 평가 및 마이그레이션을 위한 추가 도구를 선택할 수 있습니다.

## <a name="create-additional-projects"></a>추가 프로젝트 만들기

경우에 따라 추가 Azure 마이그레이션 프로젝트를 만들어야 할 수 있습니다. 예를 들어 다른 지역에 데이터 센터가 있거나 메타데이터를 다른 지역에 저장해야 하는 경우입니다. 다음과 같이 추가 프로젝트를 만듭니다.

1. 현재 Azure 마이그레이션 프로젝트에서 **서버** 또는 데이터베이스 를 **클릭합니다.**
2. 오른쪽 상단 모서리에서 현재 프로젝트 이름 옆의 **변경을** 클릭합니다.
3. **설정에서** **새 프로젝트를 만들려면 여기를**클릭하십시오.
4. 이전 절차에서 설명한 대로 새 프로젝트를 만들고 새 도구를 추가합니다.

## <a name="next-steps"></a>다음 단계

[추가 평가](how-to-assess.md) 및 [마이그레이션](how-to-migrate.md) 도구를 추가하는 방법에 대해 알아봅니다. 
