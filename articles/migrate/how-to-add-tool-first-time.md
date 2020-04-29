---
title: Azure Migrate에서 평가/마이그레이션 도구 추가
description: Azure Migrate 프로젝트를 만들고 평가/마이그레이션 도구를 추가 하는 방법을 설명 합니다.
ms.topic: how-to
ms.date: 04/16/2020
ms.openlocfilehash: 48bdea31d17ea1ddf0b983af962dce30b22d8dcf
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81537732"
---
# <a name="add-an-assessmentmigration-tool-for-the-first-time"></a>처음으로 평가/마이그레이션 도구에 추가

이 문서에서는 [Azure Migrate](migrate-overview.md) 프로젝트에 평가 또는 마이그레이션 도구를 처음으로 추가 하는 방법을 설명 합니다.  
Azure Migrate는 Azure에 대 한 온-프레미스 앱 및 워크 로드 및 사설/공용 클라우드 Vm의 검색, 평가 및 마이그레이션을 추적 하는 중앙 허브를 제공 합니다. 허브는 기타 도구 및 ISV (독립 소프트웨어 공급 업체) [제품](migrate-services-overview.md#isv-integration) 뿐만 아니라 평가 및 마이그레이션에 대 한 Azure Migrate 도구를 제공 합니다. 

## <a name="create-a-project-and-add-a-tool"></a>프로젝트 만들기 및 도구 추가

Azure 구독에 새 Azure Migrate 프로젝트를 설정 하 고 도구를 추가 합니다.

- Azure Migrate 프로젝트는 평가 하거나 마이그레이션하는 환경에서 수집 된 검색, 평가 및 마이그레이션 메타 데이터를 저장 하는 데 사용 됩니다. 
- 프로젝트에서 검색 된 자산을 추적 하 고 평가 및 마이그레이션을 오케스트레이션 할 수 있습니다.

1. Azure Portal > **모든 서비스**에서 **Azure Migrate**를 검색합니다.
2. **서비스** 아래에서 **Azure Migrate**를 선택합니다.

    ![Azure Migrate 설정](./media/how-to-add-tool-first-time/azure-migrate-search.png)

3. **개요**에서 **서버 평가 및 마이그레이션**을 클릭합니다.
4. **서버 검색, 평가 및 마이그레이션** 아래에서 **서버 평가 및 마이그레이션**을 클릭합니다.

    ![서버 검색 및 평가](./media/how-to-add-tool-first-time/assess-migrate.png)

1. **서버 검색, 평가 및 마이그레이션**에서 **도구 추가**를 클릭합니다.
2. **프로젝트 마이그레이션**에서 Azure 구독을 선택하고, 아직 없는 경우 리소스 그룹을 만듭니다.
3. 프로젝트 **세부 정보**에서 프로젝트 이름을 지정 하 고 프로젝트를 만들려는 geography를 지정 합니다.  [퍼블릭](migrate-support-matrix.md#supported-geographies-public-cloud) 및 [정부 클라우드](migrate-support-matrix.md#supported-geographies-azure-government)에 대해 지원되는 지역을 검토합니다.

    ![Azure Migrate 프로젝트 만들기](./media/how-to-add-tool-first-time/migrate-project.png)

    - 프로젝트에 대해 지정된 지리는 온-프레미스 VM에서 수집된 메타데이터를 저장하는 데 사용됩니다. 실제 마이그레이션에 대한 대상 지역을 선택할 수 있습니다.
    - 지리의 특정 영역 내에 프로젝트를 배포 해야 하는 경우 다음 API를 사용 하 여 프로젝트를 만듭니다. 위치와 함께 구독 ID, 리소스 그룹 이름 및 프로젝트 이름을 지정 합니다. [공용](migrate-support-matrix.md#supported-geographies-public-cloud) 및 [정부 클라우드의](migrate-support-matrix.md#supported-geographies-azure-government)지역/지역을 검토 합니다.

        `PUT /subscriptions/<subid>/resourceGroups/<rg>/providers/Microsoft.Migrate/MigrateProjects/<mymigrateprojectname>?api-version=2018-09-01-preview "{location: 'centralus', properties: {}}"`   


4. **다음**을 클릭 하 고 평가 또는 마이그레이션 도구를 추가 합니다.

    > [!NOTE]
    > 프로젝트를 만들 때 하나 이상의 평가 또는 마이그레이션 도구를 추가 해야 합니다.

5. **평가 도구 선택**에서 평가 도구를 추가 합니다. 평가 도구가 필요 하지 않은 경우 > 지금**다음** **에 대 한 평가 도구 추가 건너뛰기**를 선택 합니다. 
2. **마이그레이션 도구 선택**에서 필요에 따라 마이그레이션 도구를 추가 합니다. 지금은 마이그레이션 도구가 필요 하지 않은 경우 >  **지금 마이그레이션 도구 추가 건너뛰기**를**선택 합니다.**
3. **검토 + 도구 추가**에서 설정을 검토하고, **도구 추가**를 클릭합니다.

프로젝트를 만든 후에는 서버와 작업, 데이터베이스 및 웹 앱의 평가 및 마이그레이션을 위한 추가 도구를 선택할 수 있습니다.

## <a name="create-additional-projects"></a>추가 프로젝트 만들기

경우에 따라 Azure Migrate 프로젝트를 추가로 만들어야 할 수도 있습니다. 예를 들어 다른 지역에 데이터 센터가 있거나 다른 지리에 메타 데이터를 저장 해야 하는 경우입니다. 다음과 같이 추가 프로젝트를 만듭니다.

1. 현재 Azure Migrate 프로젝트에서 **서버** 또는 **데이터베이스**를 클릭 합니다.
2. 오른쪽 위 모서리에서 현재 프로젝트 이름 옆의 **변경** 을 클릭 합니다.
3. **설정**에서 **새 프로젝트를 만들려면 여기를 클릭**하십시오 .를 선택 합니다.
4. 이전 절차에 설명 된 대로 새 프로젝트를 만들고 새 도구를 추가 합니다.

## <a name="next-steps"></a>다음 단계

[평가](how-to-assess.md) 및 [마이그레이션](how-to-migrate.md) 도구를 추가 하는 방법에 대해 알아봅니다. 
