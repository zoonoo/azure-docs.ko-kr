---
title: 프로젝트 만들기 및 관리
description: Azure Migrate에서 프로젝트를 찾고 만들며 관리, 삭제합니다.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: how-to
ms.date: 11/23/2020
ms.openlocfilehash: 0c9102f8ca724e431bb478945c5f4ba0369643d6
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714844"
---
# <a name="create-and-manage-projects"></a>프로젝트 만들기 및 관리

이 문서에서는 [프로젝트](migrate-services-overview.md)를 만들고 관리, 삭제하는 방법을 설명합니다. 

클래식 Azure Migrate는 2024년 2월에 만료됩니다. 2024년 2월 이후에는 클래식 버전의 Azure Migrate는 더 이상 지원되지 않으며 클래식 프로젝트의 인벤토리 메타데이터가 삭제됩니다. 클래식 프로젝트를 사용하는 경우 해당 프로젝트를 삭제하고 단계에 따라 새 프로젝트를 만듭니다. 클래식 프로젝트나 구성 요소를 Azure Migrate로 업그레이드할 수는 없습니다. 만들기 프로세스를 시작하기 전에 [FAQ](./resources-faq.md#i-have-a-project-with-the-previous-classic-experience-of-azure-migrate-how-do-i-start-using-the-new-version)를 확인하세요.

프로젝트는 사용자가 평가하거나 마이그레이션하는 환경에서 수집된 검색, 평가 및 마이그레이션 메타데이터를 저장하는 데 사용됩니다. 프로젝트에서 검색된 자산을 추적하고, 평가를 만들며, Azure로의 마이그레이션을 오케스트레이션할 수 있습니다.  

## <a name="verify-permissions"></a>권한 확인

프로젝트를 만들 수 있는 올바른 권한이 있는지 확인합니다.

1. Azure Portal에서 관련 구독을 열고,  **액세스 제어(IAM)** 를 선택합니다.
2. **액세스 권한 확인** 에서 관련 계정을 찾고 이를 선택하여 권한을 확인합니다. *기여자* 또는 *소유자* 권한이 있어야 합니다. 


## <a name="create-a-project-for-the-first-time"></a>처음으로 프로젝트 만들기

Azure 구독에서 새 프로젝트를 설정합니다.

1. Azure Portal에서 *Azure Migrate* 를 검색합니다.
2. **서비스** 에서 **Azure Migrate** 를 선택합니다.
3. **개요** 에서 **서버 평가 및 마이그레이션** 을 선택합니다.

    :::image type="content" source="./media/create-manage-projects/assess-migrate-servers.png" alt-text="서버 평가 및 마이그레이션 개요의 옵션":::

4. **서버** 에서 **프로젝트 만들기** 를 선택합니다.

    :::image type="content" source="./media/create-manage-projects/create-project.png" alt-text="프로젝트 만들기를 시작하는 단추":::

5. **프로젝트 만들기** 에서 Azure 구독 및 리소스 그룹을 선택합니다. 리소스 그룹이 없는 경우 리소스 그룹을 만듭니다.
6. **프로젝트 세부 정보** 에서 프로젝트 이름과 이 프로젝트를 만들려는 지역을 지정합니다.
    - 지리는 온-프레미스 서버에서 수집된 메타데이터를 저장하는 데에만 사용됩니다. 마이그레이션 대상 지역을 선택할 수 있습니다. 
    - [퍼블릭](migrate-support-matrix.md#supported-geographies-public-cloud) 및 [정부 클라우드](migrate-support-matrix.md#supported-geographies-azure-government)에 대해 지원되는 지역을 검토합니다. 


    > [!Note]
    > **고급** 구성 섹션을 사용하여 프라이빗 엔드포인트 연결로 Azure Migrate 프로젝트를 만듭니다. [자세히 알아보기](how-to-use-azure-migrate-with-private-endpoints.md#create-a-project-with-private-endpoint-connectivity) 

7. **만들기** 를 선택합니다.

     :::image type="content" source="./media/create-manage-projects/project-details.png" alt-text="프로젝트 설정을 입력하는 페이지":::


프로젝트가 배포될 때까지 몇 분 정도 기다립니다.

## <a name="create-a-project-in-a-specific-region"></a>특정 지역에서 프로젝트 만들기

포털에서 프로젝트를 만들려는 지리를 선택할 수 있습니다. 특정 Azure 지역 내에서 프로젝트를 만들려는 경우 다음 API 명령을 사용하여 프로젝트를 만듭니다.

```rest
PUT /subscriptions/<subid>/resourceGroups/<rg>/providers/Microsoft.Migrate/MigrateProjects/<mymigrateprojectname>?api-version=2018-09-01-preview "{location: 'centralus', properties: {}}"
```

## <a name="create-additional-projects"></a>추가 프로젝트 만들기

프로젝트가 이미 있고 추가 프로젝트를 만들려는 경우 다음을 수행합니다.  

1. [Azure 공용 포털](https://portal.azure.com) 또는 [Azure Government](https://portal.azure.us)에서 **Azure Migrate** 를 검색합니다.
2. Azure Migrate 대시보드 > **서버** 에서 오른쪽 위 모서리에 있는 **변경** 을 선택합니다.

    :::image type="content" source="./media/create-manage-projects/switch-project.png" alt-text="프로젝트 변경":::

3. 새 프로젝트를 만들려면 **여기를 클릭** 를 선택합니다.


## <a name="find-a-project"></a>프로젝트 찾기

다음과 같이 프로젝트를 찾습니다.

1. [Azure Portal](https://portal.azure.com)에서 *Azure Migrate* 를 검색합니다.
2. Azure Migrate 대시보드 > **서버** 에서 오른쪽 위 모서리에 있는 **변경** 을 선택합니다.

    :::image type="content" source="./media/create-manage-projects/switch-project.png" alt-text="기존 프로젝트로 전환":::

3. 적합한 구독과 프로젝트를 선택합니다.


### <a name="find-a-classic-project"></a>클래식 프로젝트 찾기

[이전 버전](migrate-services-overview.md#azure-migrate-versions)의 Azure Migrate에서 프로젝트를 만든 경우 다음과 같이 찾습니다.

1. [Azure Portal](https://portal.azure.com)에서 *Azure Migrate* 를 검색합니다.
2. Azure Migrate 대시보드에서 이전 버전의 프로젝트를 만든 경우 이전 프로젝트를 참조하는 배너가 나타납니다. 배너를 선택합니다.

    :::image type="content" source="./media/create-manage-projects/access-existing-projects.png" alt-text="기존 프로젝트에 액세스":::

3. 이전 프로젝트의 목록을 검토합니다.


## <a name="delete-a-project"></a>프로젝트 삭제

삭제하는 방법은 다음과 같습니다.

1. 프로젝트가 만들어진 Azure 리소스 그룹을 엽니다.
2. 리소스 그룹 페이지에서 **숨겨진 유형 표시** 를 선택합니다.
3. 삭제하려는 프로젝트와 관련 리소스를 선택합니다.
    - 리소스 종류는 **Microsoft.Migrate/migrateprojects** 입니다.
    - 리소스 그룹이 프로젝트에서 독점적으로 사용되는 경우 전체 리소스 그룹을 삭제할 수 있습니다.

다음 사항에 유의하십시오.

- 삭제하면 검색된 서버에 대한 메타데이터와 프로젝트가 모두 삭제됩니다.
- 이전 버전의 Azure Migrate를 사용하는 경우 프로젝트가 만들어진 Azure 리소스 그룹을 엽니다. 삭제하려는 프로젝트를 선택합니다(리소스 종류: **마이그레이션 프로젝트**).
- Azure Log Analytics 작업 영역에서 종속성 분석을 사용하는 경우:
    - Log Analytics 작업 영역을 서버 평가 도구에 연결한 경우 작업 영역이 자동으로 삭제되지 않습니다. 여러 시나리오에 동일한 Log Analytics 작업 영역을 사용할 수 있습니다.
    - Log Analytics 작업 영역을 삭제하려는 경우 수동으로 수행합니다.
- 프로젝트 삭제는 취소할 수 없습니다. 삭제된 개체는 복구할 수 없습니다.

### <a name="delete-a-workspace-manually"></a>작업 영역을 수동으로 삭제

1. 프로젝트에 연결된 Log Analytics 작업 영역을 찾습니다.

    - 프로젝트를 삭제하지 않은 경우 **Essentials** > **서버 평가** 에서 작업 영역의 링크를 찾을 수 있습니다.
    :::image type="content" source="./media/create-manage-projects/loganalytics-workspace.png" alt-text="LA 작업 영역":::
       
    - 프로젝트를 이미 삭제한 경우 Azure Portal의 왼쪽 창에서 **리소스 그룹** 을 선택하고 작업 영역을 찾습니다.
       
2. [지침에 따라](../azure-monitor/logs/delete-workspace.md) 작업 영역을 삭제합니다.

## <a name="next-steps"></a>다음 단계

프로젝트에 [평가](how-to-assess.md) 또는 [마이그레이션](how-to-migrate.md) 도구를 추가합니다.