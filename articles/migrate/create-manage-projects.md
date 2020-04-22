---
title: Azure Migrate 프로젝트 만들기 및 관리
description: Azure 마이그레이션에서 프로젝트를 찾다, 만들고, 관리하고, 삭제합니다.
ms.topic: how-to
ms.date: 04/19/2020
ms.openlocfilehash: f5079ed979d98f2c6f0c654c860c6f176f366497
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81676386"
---
# <a name="create-and-manage-azure-migrate-projects"></a>Azure Migrate 프로젝트 만들기 및 관리

이 문서에서는 [Azure 마이그레이션](migrate-services-overview.md) 프로젝트를 생성, 관리 및 삭제하는 방법에 대해 설명합니다.


## <a name="create-a-project-for-the-first-time"></a>처음으로 프로젝트 만들기

Azure 마이그레이션을 처음 설정할 때 프로젝트를 만들고 평가 또는 마이그레이션 도구를 추가합니다. [이 지침에 따라](how-to-add-tool-first-time.md) 처음으로 설정하십시오.

## <a name="create-additional-projects"></a>추가 프로젝트 만들기

Azure 마이그레이션 프로젝트가 이미 있고 추가 프로젝트를 만들려는 경우 다음을 수행합니다.  

1. Azure [공용 포털](https://portal.azure.com) 또는 [Azure 정부에서](https://portal.azure.us) **Azure 마이그레이션을 검색합니다.**
2. Azure 마이그레이션 대시보드 > 서버에서 오른쪽 상단 모서리에서 **변경 을** **선택합니다.**

   ![Azure 마이그레이션 프로젝트 변경](./media/create-manage-projects/switch-project.png)

3. 새 프로젝트를 만들려면 **여기를**클릭하십시오.

   ![두 번째 Azure 마이그레이션 프로젝트 만들기](./media/create-manage-projects/create-new-project.png)


## <a name="find-a-project"></a>프로젝트 찾기

다음과 같이 프로젝트를 찾습니다.

1. Azure [포털에서](https://portal.azure.com)Azure **마이그레이션을 검색합니다.**
2. Azure 마이그레이션 대시보드 > 서버에서 오른쪽 상단 모서리에서 **변경 을** **선택합니다.**

    ![기존 Azure 마이그레이션 프로젝트로 전환](./media/create-manage-projects/switch-project.png)

3. 적절한 구독및 Azure 마이그레이션 프로젝트를 선택합니다.


[이전 버전의](migrate-services-overview.md#azure-migrate-versions) Azure Migrate에서 프로젝트를 만든 경우 다음과 같이 찾을 수 있습니다.

1. Azure [포털에서](https://portal.azure.com)Azure **마이그레이션을 검색합니다.**
2. Azure 마이그레이션 대시보드에서 이전 버전에서 프로젝트를 만든 경우 이전 프로젝트를 참조하는 배너가 나타납니다. 배너를 선택합니다.

    ![기존 프로젝트 액세스](./media/create-manage-projects/access-existing-projects.png)

3. 이전 프로젝트 목록을 검토합니다.


## <a name="delete-a-project"></a>프로젝트 삭제

다음과 같이 삭제하십시오.

1. 프로젝트가 만들어진 Azure 리소스 그룹을 엽니다.
2. 리소스 그룹 페이지에서 **숨겨진 형식 표시를**선택합니다.
3. 삭제할 마이그레이션 프로젝트와 관련 리소스를 선택합니다.
    - 리소스 유형은 **Microsoft.마이그레이션/마이그레이션 프로젝트입니다.**
    - 리소스 그룹이 Azure Migrate 프로젝트에서 단독으로 사용되는 경우 전체 리소스 그룹을 삭제할 수 있습니다.


다음 사항에 유의하세요.

- 삭제하면 프로젝트와 검색된 컴퓨터에 대한 메타데이터가 모두 삭제됩니다.
- 이전 버전의 Azure Migrate를 사용하는 경우 프로젝트가 만들어진 Azure 리소스 그룹을 엽니다. 삭제할 마이그레이션 프로젝트를 선택합니다(리소스 유형은 **마이그레이션 프로젝트).**
- Azure Log Analytics 작업 영역에서 종속성 분석을 사용하는 경우:
    - 로그 분석 작업 영역을 서버 평가 도구에 연결한 경우 작업 영역이 자동으로 삭제되지 않습니다. 여러 시나리오에 동일한 로그 분석 작업 영역을 사용할 수 있습니다.
    - Log Analytics 작업 영역을 삭제하려면 수동으로 삭제합니다.

### <a name="delete-a-workspace-manually"></a>작업 영역을 수동으로 삭제

1. 프로젝트에 연결된 Log Analytics 작업 영역을 찾습니다.

    - Azure 마이그레이션 프로젝트를 삭제하지 않은 경우 필수**서버 평가에서**작업 영역에 대한 링크를 찾을 수 **있습니다.** > 
       ![LA 작업](./media/create-manage-projects/loganalytics-workspace.png)공간 .
       
    - Azure 마이그레이션 프로젝트를 이미 삭제한 경우 Azure 포털의 왼쪽 창에서 **리소스 그룹을** 선택하고 작업 영역을 찾습니다.
       
2. [지침에 따라](https://docs.microsoft.com/azure/azure-monitor/platform/delete-workspace) 작업 영역을 삭제합니다.

## <a name="next-steps"></a>다음 단계

Azure 마이그레이션 프로젝트에 [평가](how-to-assess.md) 또는 [마이그레이션](how-to-migrate.md) 도구를 추가합니다.
