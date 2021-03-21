---
title: 작업 항목 통합 (미리 보기)-Application Insights
description: Application Insights 데이터를 포함 하는 GitHub 또는 Azure DevOps에서 작업 항목을 만드는 방법에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 02/9/2021
ms.openlocfilehash: ba0a67bad3ba47191414d6b406ab6cb4e6b7da78
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101731921"
---
# <a name="work-item-integration-preview"></a>작업 항목 통합(미리 보기)

작업 항목 통합 기능을 사용 하면 관련 Application Insights 데이터가 포함 된 GitHub 또는 Azure DevOps에서 작업 항목을 쉽게 만들 수 있습니다.

> [!IMPORTANT]
> 작업 항목 통합은 현재 공개 미리 보기 상태입니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="create-and-configure-a-work-item-template"></a>작업 항목 템플릿 만들기 및 구성

1. 작업 항목 템플릿을 만들려면 Application Insights 리소스로 이동 하 고 왼쪽의 **작업 항목** 선택에서 맨 위에 있는 **새 템플릿 만들기** *를 선택 합니다* .

    :::image type="content" source="./media/work-item-integration/create-work-item-template.png" alt-text=" 새 템플릿 만들기가 선택 된 작업 항목 탭의 스크린샷" lightbox="./media/work-item-integration/create-work-item-template.png":::

    현재 템플릿이 없는 경우 종단 간 트랜잭션 세부 정보 탭에서 작업 항목 템플릿을 만들 수도 있습니다. 이벤트를 선택 하 고 오른쪽에서 **작업 항목 만들기** 를 선택한 다음 **통합 문서 템플릿으로 시작** 합니다.

    :::image type="content" source="./media/work-item-integration/create-template-from-transaction-details.png" alt-text=" 작업 항목 만들기를 사용 하는 종단 간 트랜잭션 세부 정보 탭의 스크린샷, 선택한 통합 문서 템플릿으로 시작 합니다." lightbox="./media/work-item-integration/create-template-from-transaction-details.png":::

2. **새 템플릿 만들기** 를 선택한 후 추적 시스템을 선택 하 고, 통합 문서 이름을 지정할 때 선택한 추적 시스템에 연결 하 고, 템플릿을 저장할 영역을 선택할 수 있습니다. 기본값은 Application Insights 리소스가 있는 지역입니다. URL 매개 변수는 리포지토리의 기본 URL (예: `https://github.com/myusername/reponame` 또는) `https://mydevops.visualstudio.com/myproject` 입니다.

    :::image type="content" source="./media/work-item-integration/create-workbook.png" alt-text=" 새 작업 항목 만들기 통합 문서 서식 파일의 스크린샷":::

## <a name="create-a-work-item"></a>작업 항목 만들기

 성능, 오류, 가용성 또는 기타 탭에서 액세스할 수 있는 종단 간 트랜잭션 세부 정보에서 새 템플릿에 액세스할 수 있습니다.

1. 작업 항목을 만들려면 종단 간 트랜잭션 세부 정보로 이동 하 여 이벤트를 선택 하 고 **작업 항목 만들기** 를 선택한 다음 작업 항목 템플릿을 선택 합니다.

    :::image type="content" source="./media/work-item-integration/create-work-item.png" alt-text=" 작업 항목 만들기가 선택 된 종단 간 트랜잭션 세부 정보 탭의 스크린샷" lightbox="./media/work-item-integration/create-work-item.png":::

1. 브라우저에서 새 탭이 선택 추적 시스템으로 열립니다. Azure DevOps에서 버그나 작업을 만들 수 있으며 GitHub에서 리포지토리에 새 문제를 만들 수 있습니다. Application Insights에서 제공 하는 컨텍스트 정보를 사용 하 여 새 작업 항목을 자동으로 만듭니다.

    :::image type="content" source="./media/work-item-integration/github-work-item.png" alt-text=" 자동으로 만들어진 GitHub 문제 스크린샷" lightbox="./media/work-item-integration/github-work-item.png":::

    :::image type="content" source="./media/work-item-integration/azure-devops-work-item.png" alt-text=" Azure DevOps에서 자동으로 버그를 만드는 스크린샷" lightbox="./media/work-item-integration/azure-devops-work-item.png":::

## <a name="edit-a-template"></a>템플릿 편집

템플릿을 편집 하려면 *구성* 아래의 **작업 항목** 탭으로 이동 하 여 업데이트 하려는 통합 문서 옆에 있는 연필 아이콘을 선택 합니다.

:::image type="content" source="./media/work-item-integration/edit-template.png" alt-text=" 연필 편집 아이콘이 선택 된 작업 항목 탭의 스크린샷":::

![위쪽의 편집 편집 아이콘 ](./media/work-item-integration/edit-icon.png) 을 선택 하 여 템플릿 편집을 시작 합니다. 작업 항목 템플릿은 [Azure Monitor 통합 문서](../visualize/workbooks-overview.md)를 기반으로 합니다. 작업 항목 정보는 키워드 쿼리 언어를 사용 하 여 생성 됩니다. 쿼리를 수정 하 여 팀에 필요한 컨텍스트를 더 추가할 수 있습니다. 편집을 완료 한 후에는 ![ ](./media/work-item-integration/save-icon.png) 상단 도구 모음에서 저장 아이콘 저장 아이콘을 선택 하 여 통합 문서를 저장 합니다.

:::image type="content" source="./media/work-item-integration/edit-workbook.png" alt-text=" 편집 모드의 작업 항목 템플릿 통합 문서 스크린샷" lightbox="./media/work-item-integration/edit-workbook.png":::

작업 항목 구성을 두 개 이상 만들고 각 시나리오에 맞는 사용자 지정 통합 문서를 만들 수 있습니다. 환경 전체에서 표준 구현을 Azure Resource Manager 여 통합 문서를 배포할 수도 있습니다.