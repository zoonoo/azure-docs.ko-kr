---
title: Azure Migrate 프로젝트 만들기 및 관리
description: Azure Migrate에서 프로젝트를 찾고 만들고 관리 하 고 삭제 합니다.
ms.topic: how-to
ms.date: 11/23/2020
ms.openlocfilehash: 95f123188f7906cbd5c7a209c9fd01be006e9a7e
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2020
ms.locfileid: "95534926"
---
# <a name="create-and-manage-azure-migrate-projects"></a>Azure Migrate 프로젝트 만들기 및 관리

이 문서에서는 [Azure Migrate](migrate-services-overview.md) 프로젝트를 만들고, 관리 하 고, 삭제 하는 방법을 설명 합니다.

Azure Migrate 프로젝트는 평가하거나 마이그레이션하는 환경에서 수집된 검색, 평가 및 마이그레이션 메타데이터를 저장하는 데 사용됩니다. 프로젝트에서 검색 된 자산을 추적 하 고, 평가를 만들고, Azure로 마이그레이션을 오케스트레이션 할 수 있습니다.  

## <a name="verify-permissions"></a>권한 확인

Azure Migrate 프로젝트를 만들 수 있는 올바른 권한이 있는지 확인 합니다.

1. Azure Portal에서 관련 구독을 열고 **액세스 제어 (IAM)** 를 선택 합니다.
2. **액세스 확인** 에서 관련 계정을 찾고 권한 보기를 선택 합니다. *기여자* 또는 *소유자* 권한이 있어야 합니다. 


## <a name="create-a-project-for-the-first-time"></a>처음으로 프로젝트 만들기

Azure 구독에 새 Azure Migrate 프로젝트를 설정 합니다.

1. Azure Portal에서 *Azure Migrate* 를 검색 합니다.
2. **서비스** 에서 **Azure Migrate** 를 선택 합니다.
3. **개요** 에서 **서버 평가 및 마이그레이션** 을 선택합니다.

    ![서버 평가 및 마이그레이션 개요의 옵션](./media/create-manage-projects/assess-migrate-servers.png)

4. **서버** 에서 **프로젝트 만들기** 를 선택 합니다.

    ![프로젝트 만들기를 시작 하는 단추](./media/create-manage-projects/create-project.png)

5. **프로젝트 만들기** 에서 Azure 구독 및 리소스 그룹을 선택 합니다. 리소스 그룹이 없는 경우 리소스 그룹을 만듭니다.
6. **프로젝트 세부 정보** 에서 프로젝트 이름과 이 프로젝트를 만들려는 지역을 지정합니다.
    - 지리는 온-프레미스 컴퓨터에서 수집 된 메타 데이터를 저장 하는 데만 사용 됩니다. 마이그레이션할 대상 지역을 선택할 수 있습니다. 
    - [퍼블릭](migrate-support-matrix.md#supported-geographies-public-cloud) 및 [정부 클라우드](migrate-support-matrix.md#supported-geographies-azure-government)에 대해 지원되는 지역을 검토합니다.

8. **만들기** 를 선택합니다.

   ![페이지에서 입력 프로젝트 설정으로](./media/create-manage-projects/project-details.png)


Azure Migrate 프로젝트가 배포될 때까지 몇 분 정도 기다립니다.

## <a name="create-a-project-in-a-specific-region"></a>특정 지역에서 프로젝트 만들기

포털에서 프로젝트를 만들려는 지리를 선택할 수 있습니다. 특정 Azure 지역 내에서 프로젝트를 만들려는 경우 다음 API 명령을 사용 하 여 프로젝트를 만듭니다.

```rest
PUT /subscriptions/<subid>/resourceGroups/<rg>/providers/Microsoft.Migrate/MigrateProjects/<mymigrateprojectname>?api-version=2018-09-01-preview "{location: 'centralus', properties: {}}"
``````


## <a name="create-additional-projects"></a>추가 프로젝트 만들기

Azure Migrate 프로젝트가 이미 있고 추가 프로젝트를 만들려는 경우 다음을 수행 합니다.  

1. [Azure 공용 포털](https://portal.azure.com) 또는 [Azure Government](https://portal.azure.us)에서 **Azure Migrate** 를 검색 합니다.
2. Azure Migrate 대시보드 > **서버** 에서 오른쪽 위 모서리에 있는 **변경** 을 선택 합니다.

   ![Azure Migrate 프로젝트 변경](./media/create-manage-projects/switch-project.png)

3. 새 프로젝트를 만들려면 **여기를 클릭** 하십시오 .를 선택 합니다.


## <a name="find-a-project"></a>프로젝트 찾기

다음과 같이 프로젝트를 찾습니다.

1. [Azure Portal](https://portal.azure.com)에서 *Azure Migrate* 를 검색 합니다.
2. Azure Migrate 대시보드 > **서버** 에서 오른쪽 위 모서리에 있는 **변경** 을 선택 합니다.

    ![기존 Azure Migrate 프로젝트로 전환](./media/create-manage-projects/switch-project.png)

3. 적절 한 구독 및 Azure Migrate 프로젝트를 선택 합니다.


### <a name="find-a-legacy-project"></a>레거시 프로젝트 찾기

[이전 버전](migrate-services-overview.md#azure-migrate-versions) 의 Azure Migrate에서 프로젝트를 만든 경우 다음과 같이 찾습니다.

1. [Azure Portal](https://portal.azure.com)에서 *Azure Migrate* 를 검색 합니다.
2. Azure Migrate 대시보드의 이전 버전에서 프로젝트를 만든 경우 이전 프로젝트를 참조 하는 배너가 나타납니다. 배너를 선택 합니다.

    ![기존 프로젝트 액세스](./media/create-manage-projects/access-existing-projects.png)

3. 이전 프로젝트의 목록을 검토 합니다.


## <a name="delete-a-project"></a>프로젝트 삭제

삭제하는 방법은 다음과 같습니다.

1. 프로젝트가 생성 된 Azure 리소스 그룹을 엽니다.
2. 리소스 그룹 페이지에서 **숨겨진 유형 표시** 를 선택 합니다.
3. 삭제할 마이그레이션 프로젝트와 연결 된 리소스를 선택 합니다.
    - 리소스 유형은 **Microsoft. 마이그레이션/migrateprojects** 입니다.
    - 리소스 그룹이 Azure Migrate 프로젝트에 독점적으로 사용 되는 경우 전체 리소스 그룹을 삭제할 수 있습니다.

다음 사항에 유의하십시오.

- 를 삭제 하면 검색 된 컴퓨터에 대 한 프로젝트와 메타 데이터가 모두 삭제 됩니다.
- 이전 버전의 Azure Migrate을 사용 하는 경우 프로젝트를 만든 Azure 리소스 그룹을 엽니다. 삭제할 마이그레이션 프로젝트를 선택 합니다 (리소스 종류는 **마이그레이션 프로젝트**).
- Azure Log Analytics 작업 영역에서 종속성 분석을 사용 하는 경우:
    - 서버 평가 도구에 Log Analytics 작업 영역을 연결한 경우 작업 영역이 자동으로 삭제 되지 않습니다. 여러 시나리오에 동일한 Log Analytics 작업 영역을 사용할 수 있습니다.
    - Log Analytics 작업 영역을 삭제 하려면 수동으로 작업을 수행 합니다.
- 프로젝트 삭제는 취소할 수 없습니다. 삭제 된 개체는 복구할 수 없습니다.

### <a name="delete-a-workspace-manually"></a>수동으로 작업 영역 삭제

1. 프로젝트에 연결된 Log Analytics 작업 영역을 찾습니다.

    - Azure Migrate 프로젝트를 삭제 하지 않은 경우 **Essentials**  >  **서버 평가** 에서 작업 영역에 대 한 링크를 찾을 수 있습니다.
       ![LA 작업 영역 ](./media/create-manage-projects/loganalytics-workspace.png) .
       
    - 이미 Azure Migrate 프로젝트를 삭제 한 경우 Azure Portal의 왼쪽 창에서 **리소스 그룹** 을 선택 하 고 작업 영역을 찾습니다.
       
2. [지침에 따라](../azure-monitor/platform/delete-workspace.md) 작업 영역을 삭제 합니다.

## <a name="next-steps"></a>다음 단계

Azure Migrate 프로젝트에 [평가](how-to-assess.md) 또는 [마이그레이션](how-to-migrate.md) 도구를 추가 합니다.
