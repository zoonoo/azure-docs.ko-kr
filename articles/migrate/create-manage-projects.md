---
title: Azure Migrate 프로젝트 만들기 및 관리
description: Azure Migrate에서 프로젝트를 찾고 만들고 관리 하 고 삭제 합니다.
ms.topic: how-to
ms.date: 07/23/2020
ms.openlocfilehash: d60868f9d0d4c60291cfd92a9e8d11fd3f9a42b9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87071799"
---
# <a name="create-and-manage-azure-migrate-projects"></a>Azure Migrate 프로젝트 만들기 및 관리

이 문서에서는 [Azure Migrate](migrate-services-overview.md) 프로젝트를 만들고, 관리 하 고, 삭제 하는 방법을 설명 합니다.


## <a name="create-a-project-for-the-first-time"></a>처음으로 프로젝트 만들기

Azure Migrate를 처음 설정할 때 프로젝트를 만들고 평가 또는 마이그레이션 도구를 추가 합니다. 처음으로 설정 하려면 [다음 지침을 따르세요](how-to-add-tool-first-time.md) .

## <a name="create-additional-projects"></a>추가 프로젝트 만들기

Azure Migrate 프로젝트가 이미 있고 추가 프로젝트를 만들려는 경우 다음을 수행 합니다.  

1. [Azure 공용 포털](https://portal.azure.com) 또는 [Azure Government](https://portal.azure.us)에서 **Azure Migrate**를 검색 합니다.
2. Azure Migrate 대시보드 > **서버**에서 오른쪽 위 모서리에 있는 **변경** 을 선택 합니다.

   ![Azure Migrate 프로젝트 변경](./media/create-manage-projects/switch-project.png)

3. 새 프로젝트를 만들려면 **여기를 클릭**하십시오 .를 선택 합니다.

   ![두 번째 Azure Migrate 프로젝트 만들기](./media/create-manage-projects/create-new-project.png)


## <a name="find-a-project"></a>프로젝트 찾기

다음과 같이 프로젝트를 찾습니다.

1. [Azure Portal](https://portal.azure.com)에서 **Azure Migrate**를 검색 합니다.
2. Azure Migrate 대시보드 > **서버**에서 오른쪽 위 모서리에 있는 **변경** 을 선택 합니다.

    ![기존 Azure Migrate 프로젝트로 전환](./media/create-manage-projects/switch-project.png)

3. 적절 한 구독 및 Azure Migrate 프로젝트를 선택 합니다.


[이전 버전](migrate-services-overview.md#azure-migrate-versions) 의 Azure Migrate에서 프로젝트를 만든 경우 다음과 같이 찾습니다.

1. [Azure Portal](https://portal.azure.com)에서 **Azure Migrate**를 검색 합니다.
2. Azure Migrate 대시보드의 이전 버전에서 프로젝트를 만든 경우 이전 프로젝트를 참조 하는 배너가 나타납니다. 배너를 선택 합니다.

    ![기존 프로젝트 액세스](./media/create-manage-projects/access-existing-projects.png)

3. 이전 프로젝트의 목록을 검토 합니다.


## <a name="delete-a-project"></a>프로젝트 삭제

삭제하는 방법은 다음과 같습니다.

1. 프로젝트가 생성 된 Azure 리소스 그룹을 엽니다.
2. 리소스 그룹 페이지에서 **숨겨진 유형 표시**를 선택 합니다.
3. 삭제할 마이그레이션 프로젝트와 연결 된 리소스를 선택 합니다.
    - 리소스 유형은 **Microsoft. 마이그레이션/migrateprojects**입니다.
    - 리소스 그룹이 Azure Migrate 프로젝트에 독점적으로 사용 되는 경우 전체 리소스 그룹을 삭제할 수 있습니다.

다음 사항에 유의하세요.

- 를 삭제 하면 검색 된 컴퓨터에 대 한 프로젝트와 메타 데이터가 모두 삭제 됩니다.
- 이전 버전의 Azure Migrate을 사용 하는 경우 프로젝트를 만든 Azure 리소스 그룹을 엽니다. 삭제할 마이그레이션 프로젝트를 선택 합니다 (리소스 종류는 **마이그레이션 프로젝트**).
- Azure Log Analytics 작업 영역에서 종속성 분석을 사용 하는 경우:
    - 서버 평가 도구에 Log Analytics 작업 영역을 연결한 경우 작업 영역이 자동으로 삭제 되지 않습니다. 여러 시나리오에 동일한 Log Analytics 작업 영역을 사용할 수 있습니다.
    - Log Analytics 작업 영역을 삭제 하려면 수동으로 작업을 수행 합니다.
- 프로젝트 삭제는 취소할 수 없습니다. 삭제 된 개체는 복구할 수 없습니다.

### <a name="delete-a-workspace-manually"></a>수동으로 작업 영역 삭제

1. 프로젝트에 연결된 Log Analytics 작업 영역을 찾습니다.

    - Azure Migrate 프로젝트를 삭제 하지 않은 경우 **Essentials**  >  **서버 평가**에서 작업 영역에 대 한 링크를 찾을 수 있습니다.
       ![LA 작업 영역 ](./media/create-manage-projects/loganalytics-workspace.png) .
       
    - 이미 Azure Migrate 프로젝트를 삭제 한 경우 Azure Portal의 왼쪽 창에서 **리소스 그룹** 을 선택 하 고 작업 영역을 찾습니다.
       
2. [지침에 따라](../azure-monitor/platform/delete-workspace.md) 작업 영역을 삭제 합니다.

## <a name="next-steps"></a>다음 단계

Azure Migrate 프로젝트에 [평가](how-to-assess.md) 또는 [마이그레이션](how-to-migrate.md) 도구를 추가 합니다.
