---
title: Azure Monitor 통합 문서 링크 작업
description: Azure Monitor 통합 문서에서 링크 작업을 사용 하는 방법
ms.topic: conceptual
ms.date: 01/07/2021
author: lgayhardt
ms.author: lagayhar
ms.openlocfilehash: 2d1abe6d77dd4293dc70a07f3b8fd5d9da78d01e
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100616039"
---
# <a name="link-actions"></a>링크 작업

링크 작업은 통합 문서 링크 단계 또는 [그리드](../visualize/workbooks-grid-visualizations.md), [제목](../visualize/workbooks-tile-visualizations.md)또는 [그래프](../visualize/workbooks-graph-visualizations.md)의 열 설정을 통해 액세스할 수 있습니다.

## <a name="general-link-actions"></a>일반 링크 작업

| 링크 작업 | 클릭에 대 한 작업 |
|:------------- |:-------------|
| `Generic Details` | 속성 그리드 컨텍스트 보기에서 행 값을 표시 합니다. |
| `Cell Details` | 속성 그리드 컨텍스트 뷰의 셀 값을 표시 합니다. 정보를 포함 하는 동적 형식 (예: 위치, 역할 인스턴스 등의 요청 속성이 있는 json)이 셀에 포함 된 경우에 유용 합니다. |
| `Url` | 셀의 값은 유효한 http url 이어야 하며, 셀은 새 탭에서 해당 url을 여는 링크입니다.|

## <a name="application-insights"></a>Application Insights

| 링크 작업 | 클릭에 대 한 작업 |
|:------------- |:-------------|
| `Custom Event Details` | 셀에서 사용자 지정 이벤트 ID ()를 사용 하 여 Application Insights 검색 세부 정보를 엽니다 `itemId` . |
| `* Details` | 종속성, 예외, 페이지 보기, 요청 및 추적을 제외 하 고 사용자 지정 이벤트 세부 정보와 유사 합니다. |
| `Custom Event User Flows` | 셀의 사용자 지정 이벤트 이름에서 피벗 된 Application Insights 사용자 흐름 환경을 엽니다. |
| `* User Flows` | 예외, 페이지 보기 및 요청을 제외 하 고 사용자 지정 이벤트 사용자 흐름와 비슷합니다. |
| `User Timeline` | 셀에서 사용자 ID ()를 사용 하 여 사용자 시간 표시 막대를 엽니다 `user_Id` . |
| `Session Timeline` | 셀 값에 대 한 Application Insights 검색 환경을 엽니다. 예를 들어 텍스트 ' abc '를 검색 합니다. 여기서 abc는 셀의 값입니다. |

`*` 위의 테이블에 대 한 와일드 카드를 나타냅니다.

## <a name="azure-resource"></a>Azure 리소스

| 링크 작업 | 클릭에 대 한 작업 |
|:------------- |:-------------|
| `ARM Deployment` | Azure Resource Manager 템플릿을 배포 합니다.  이 항목을 선택 하면 작성자가 열려는 Azure Resource Manager 템플릿, 템플릿에 대 한 매개 변수 등을 구성할 수 있도록 추가 필드가 표시 됩니다. [Azure Resource Manager 배포 링크 설정을 참조](#azure-resource-manager-deployment-link-settings)하세요. |
| `Create Alert Rule` | 리소스에 대 한 경고 규칙을 만듭니다.  |
| `Custom View` | 사용자 지정 보기를 엽니다. 이 항목을 선택 하면 작성자가 보기 확장, 보기 이름 및 보기를 여는 데 사용 되는 매개 변수를 구성할 수 있는 추가 필드가 표시 됩니다. [사용자 지정 뷰를 참조](#custom-view-link-settings)하세요. |
| `Metrics` | 메트릭 보기를 엽니다.  |
| `Resource overview` | 셀의 리소스 ID 값을 기반으로 포털에서 리소스의 뷰를 엽니다. 또한 작성자는 `submenu` 리소스 뷰에서 특정 메뉴 항목을 여는 값을 선택적으로 설정할 수 있습니다. |
| `Workbook (template)` | 통합 문서 템플릿을 엽니다.  이 항목을 선택 하면 작성자가 열려는 템플릿을 구성할 수 있도록 추가 필드가 표시 됩니다.  |

## <a name="link-settings"></a>링크 설정

링크 렌더러를 사용 하는 경우 다음 설정을 사용할 수 있습니다.

![링크 설정의 스크린샷](./media/workbooks-link-actions/link-settings.png)

| 설정 | 설명 |
|:------------- |:-------------|
| `View to open` | 작성자가 위에 열거 된 작업 중 하나를 선택할 수 있습니다. |
| `Menu item` | "리소스 개요"를 선택 하면 리소스 개요에서 열 메뉴 항목을 엽니다. 이를 사용 하 여 리소스에 대 한 "개요" 대신 경고 또는 활동 로그를 열 수 있습니다. 각 Azure에 대 한 메뉴 항목 값은 서로 다릅니다 `Resourcetype` .|
| `Link label` | 지정 된 경우이 값은 표 형태 열에 표시 됩니다. 이 값을 지정 하지 않으면 셀의 값이 표시 됩니다. 열 지도 또는 아이콘과 같이 다른 값을 표시 하려면 렌더러를 사용 하지 말고 `Link` 적절 한 렌더러를 사용 하 고 옵션을 선택 `Make this item a link` 합니다. |
| `Open link in Context Blade` | 지정 하는 경우 링크가 전체 뷰로 열리는 대신 창의 오른쪽에 팝업 "컨텍스트" 뷰로 열립니다. |

옵션을 사용 하는 경우 `Make this item a link` 다음 설정을 사용할 수 있습니다.

| 설정 | 설명 |
|:------------- |:-------------|
| `Link value comes from` | 링크를 사용 하 여 셀을 렌더러에서 표시 하는 경우이 필드는 링크에 사용할 "링크" 값을 가져올 위치를 지정 합니다 .이 필드는 작성자가 표에 있는 다른 열의 드롭다운 목록에서 선택할 수 있도록 합니다. 예를 들어 셀은 열 지도 값이 될 수 있지만 링크를 클릭 하 여 행의 리소스 ID에 대 한 리소스 개요를 열어야 합니다. 이 경우 필드에서 제공 되는 링크 값을 설정 합니다 `Resource Id` .
| `View to open` | 위와 동일. |
| `Menu item` | 위와 동일. |
| `Open link in Context Blade` | 위와 동일. |

## <a name="azure-resource-manager-deployment-link-settings"></a>Azure Resource Manager 배포 링크 설정

선택한 링크 형식이 `ARM Deployment` 작성자가 추가 설정을 지정 해야 Azure Resource Manager 배포를 열 수 있습니다. 구성에는 두 가지 주요 탭이 있습니다.

### <a name="template-settings"></a>템플릿 설정

이 섹션에서는 템플릿이 제공 되는 위치 및 Azure Resource Manager 배포를 실행 하는 데 사용 되는 매개 변수를 정의 합니다.

| 원본 | 설명 |
|:------------- |:-------------|
|`Resource group id comes from` | 리소스 ID는 배포 된 리소스를 관리 하는 데 사용 됩니다. 구독은 배포 된 리소스와 비용을 관리 하는 데 사용 됩니다. 리소스 그룹은 폴더와 같이 모든 리소스를 구성 하 고 관리 하는 데 사용 됩니다. 이 값을 지정 하지 않으면 배포에 실패 합니다. `Cell` `Column` `Static Value` `Parameter` [링크 소스](#link-sources)에서,, 또는를 선택 합니다.|
|`ARM template URI from` | Azure Resource Manager 템플릿 자체에 대 한 URI입니다. 템플릿을 배포 하는 사용자가 템플릿 URI에 액세스할 수 있어야 합니다. `Cell` `Column` `Parameter` `Static Value` [링크 소스](#link-sources)에서,, 또는를 선택 합니다. 초보자를 위해 [빠른 시작 템플릿을](https://azure.microsoft.com/resources/templates/)살펴보세요.|
|`ARM Template Parameters` | 이 섹션에서는 위에 정의 된 템플릿 URI에 사용 되는 템플릿 매개 변수를 정의 합니다. 이러한 매개 변수는 실행 페이지에 템플릿을 배포 하는 데 사용 됩니다. 표에는 템플릿 URI에 정의 된 이름을 사용 하 여 매개 변수를 채우고 정적 빈 값으로 설정 하는 데 사용할 수 있는 확장 도구 모음 단추가 있습니다. 이 옵션은 표에 매개 변수가 없고 템플릿 URI가 설정 된 경우에만 사용할 수 있습니다. 아래쪽 섹션은 매개 변수 출력의 모양에 대 한 미리 보기입니다. 최신 변경 내용으로 미리 보기를 업데이트 하려면 새로 고침을 선택 합니다. 매개 변수는 일반적으로 값 이지만, 참조는 사용자가 액세스할 수 있는 keyvault 암호를 가리킬 수 있는 항목입니다. <br/><br/> **템플릿 뷰어 블레이드 제한 사항** -참조 매개 변수를 올바르게 렌더링 하지 않으며 null/값으로 표시 되므로 사용자가 템플릿 뷰어 탭에서 참조 매개 변수를 올바르게 배포할 수 없습니다.|

![Azure Resource Manager 템플릿 설정의 스크린샷](./media/workbooks-link-actions/template-settings.png)

### <a name="ux-settings"></a>UX 설정

이 섹션에서는 Azure Resource Manager 배포를 실행 하기 전에 사용자에 게 표시 되는 내용을 구성 합니다.

| 원본 | 설명 |
|:------------- |:-------------|
|`Title from` | 실행 보기에 사용 되는 제목입니다. `Cell` `Column` `Parameter` `Static Value` [링크 소스](#link-sources)에서,, 또는를 선택 합니다.|
|`Description from` | 사용자가 템플릿을 배포 하려는 경우 사용자에 게 유용한 설명을 제공 하는 데 사용 되는 markdown 텍스트입니다. `Cell` `Column` `Parameter` `Static Value` [링크 소스](#link-sources)에서,, 또는를 선택 합니다. <br/><br/> **참고:** `Static Value` 를 선택 하면 여러 줄 텍스트 상자가 표시 됩니다. 이 입력란에서를 사용 하 여 매개 변수를 확인할 수 있습니다 `{paramName}` . 열 이름 뒤에을 추가 하 여 열을 매개 변수로 취급할 수도 있습니다 `_column` `{columnName_column}` . 아래 예제 이미지에서는를 작성 하 여 열을 참조할 수 있습니다 `VMName` `{VMName_column}` . 콜론 뒤의 값은 [매개 변수 포맷터](../visualize/workbooks-parameters.md#parameter-options)(이 경우)입니다 `value` .|
|`Run button text from` | Azure Resource Manager 템플릿을 배포 하기 위해 실행 (실행) 단추에 사용 되는 레이블입니다. Azure Resource Manager 템플릿 배포를 시작 하기 위해 사용자가 선택 하는 항목입니다.|

![Azure Resource Manager UX 설정의 스크린샷](./media/workbooks-link-actions/ux-settings.png)

이러한 구성을 설정한 후 사용자가 링크를 선택 하면 UX 설정에 설명 된 UX를 사용 하 여 뷰가 열립니다. 사용자가 선택 하는 경우 `Run button text from` [템플릿 설정](#template-settings)의 값을 사용 하 여 Azure Resource Manager 템플릿을 배포 합니다. 템플릿 보기를 배포 하기 전에 사용자가 템플릿 및 매개 변수를 검사 하는 템플릿 뷰어 탭을 엽니다.

![Azure Resource Manager 뷰의 실행 스크린샷](./media/workbooks-link-actions/run-tab.png)

## <a name="custom-view-link-settings"></a>사용자 지정 보기 링크 설정

이를 사용 하 여 Azure Portal에서 사용자 지정 보기를 열 수 있습니다. 모든 구성 및 설정을 확인 합니다. 잘못 된 값은 포털에서 오류를 발생 시키거나 보기를 올바르게 열지 못했습니다. 또는를 통해 설정을 구성 하는 방법에는 두 가지가 있습니다 `Form` `URL` .

> [!NOTE]
> 메뉴를 포함 하는 뷰는 컨텍스트 탭에서 열 수 없습니다. 메뉴가 있는 뷰가 컨텍스트 탭에서 열리도록 구성 된 경우 링크를 선택 하면 컨텍스트 탭이 표시 되지 않습니다.

### <a name="form"></a>Form

| 원본 | 설명 |
|:------------- |:-------------|
|`Extension name` | 뷰의 이름을 호스팅하는 확장 프로그램의 이름입니다.|
|`View name` | 열 보기의 이름입니다.|

#### <a name="view-inputs"></a>입력 보기

입력에는 grid와 JSON 이라는 두 가지 유형이 있습니다. `Grid`단순 키 및 값 탭 입력에 사용 하거나 `JSON` 중첩 된 JSON 입력을 지정 하려면 선택 합니다.

- 그리드
    - `Parameter Name`: 뷰 입력 매개 변수의 이름입니다.
    - `Parameter Comes From`: 뷰 매개 변수 값을 가져와야 합니다. `Cell` `Column` `Parameter` `Static Value` [링크 소스](#link-sources)에서,, 또는를 선택 합니다.
    > [!NOTE]
    > `Static Value`를 선택 하면 텍스트 상자에서 대괄호 링크를 사용 하 여 매개 변수를 확인할 수 있습니다 `{paramName}` . 열 이름 뒤에을 추가 하 여 열을 매개 변수 열로 처리할 수 있습니다 `_column` `{columnName_column}` .

    - `Parameter Value`:에 따라 `Parameter Comes From` 사용 가능한 매개 변수, 열 또는 정적 값의 드롭다운이 됩니다.

    ![열 편집 설정의 스크린샷 폼의 사용자 지정 보기 설정을 표시 합니다.](./media/workbooks-link-actions/custom-tab-settings.png)
- JSON
    - 편집기에서 JSON 형식으로 탭 입력을 지정 합니다. `Grid` `{paramName}` 매개 변수 및 열에 대해를 사용 하 여 모드, 매개 변수 및 열을 참조할 수 있습니다 `{columnName_column}` . 를 선택 하면 `Show JSON Sample` 확인 된 모든 매개 변수 및 사용자 열에 대해 보기 입력에 대 한 예상 출력이 표시 됩니다.

    ![JSON에 대 한 뷰 입력으로 열려 있는 사용자 지정 보기 설정을 보여 주는 스크린샷](./media/workbooks-link-actions/custom-tab-json.png)

### <a name="url"></a>URL

확장, 뷰 이름 및 보기를 여는 데 필요한 모든 입력을 포함 하는 포털 URL을 붙여넣습니다. 선택한 후에 `Initialize Settings` 는 `Form` 작성자가 보기 입력을 추가/수정/제거 하도록를 채웁니다.

![URL의 사용자 지정 보기 설정 표시 열 설정을 보여 주는 스크린샷 ](./media/workbooks-link-actions/custom-tab-settings-url.png)

## <a name="workbook-template-link-settings"></a>통합 문서 (템플릿) 링크 설정

선택한 링크 형식이 인 경우 `Workbook (Template)` 작성자는 추가 설정을 지정 하 여 올바른 통합 문서 템플릿을 열어야 합니다. 아래 설정에는 각 설정에 대 한 적절 한 값을 그리드에서 찾는 방법에 대 한 옵션이 있습니다.

| 설정 | 설명 |
|:------------- |:-------------|
| `Workbook owner Resource Id` | 통합 문서를 "소유" 하는 Azure 리소스의 리소스 ID입니다. 일반적으로 Application Insights 리소스 또는 Log Analytics 작업 영역입니다. Azure Monitor 내에서 리터럴 문자열일 수도 있습니다 `"Azure Monitor"` . 통합 문서가 저장 되 면 통합 문서가 연결 됩니다. |
| `Workbook resources` | 통합 문서에 사용 되는 기본 리소스를 지정 하는 Azure 리소스 Id의 배열입니다. 예를 들어 열려는 템플릿에 가상 컴퓨터 메트릭이 표시 되는 경우 여기에 있는 값은 가상 컴퓨터 리소스 Id입니다.  여러 번, 소유자 및 리소스는 동일한 설정으로 설정 됩니다. |
| `Template Id` | 열려는 템플릿의 ID를 지정 합니다. 갤러리의 커뮤니티 템플릿인 경우 (가장 일반적인 경우) `Community-` 템플릿과 같이 템플릿에 대 한 경로에 접두사를 붙입니다 `Community-Workbooks/Performance/Apdex` `Workbooks/Performance/Apdex` . 저장 된 통합 문서/템플릿에 대 한 링크인 경우 해당 항목의 전체 Azure 리소스 ID입니다. |
| `Workbook Type` | 열려는 통합 문서 템플릿의 종류를 지정 합니다. 가장 일반적인 경우는 `Default` 또는 `Workbook` 옵션을 사용 하 여 현재 통합 문서의 값을 사용 합니다. |
| `Gallery Type` | 이는 열리는 템플릿의 "갤러리" 보기에 표시 되는 갤러리 유형을 지정 합니다. 가장 일반적인 경우는 `Default` 또는 `Workbook` 옵션을 사용 하 여 현재 통합 문서의 값을 사용 합니다. |
|`Location comes from` | 특정 통합 문서 리소스를 여는 경우에는 위치 필드를 지정 해야 합니다. Location을 지정 하지 않으면 통합 문서 콘텐츠를 찾기가 훨씬 느립니다. 위치를 알고 있으면 지정 합니다. 위치를 알 수 없거나 특정 위치가 없는 템플릿을 여는 경우에는이 필드를 "Default"로 그대로 둡니다.|
|`Pass specific parameters to template` | 특정 매개 변수를 템플릿에 전달 하려면 선택 합니다. 이 확인란이 선택 된 경우 지정 된 매개 변수만 템플릿에 전달 되 고, 현재 통합 문서의 모든 매개 변수가 템플릿에 전달 되 고,이 경우이 매개 변수 값이 작동 하려면 두 통합 문서에서 매개 변수 *이름이* 동일 해야 합니다.|
|`Workbook Template Parameters` | 이 섹션에서는 대상 템플릿에 전달 되는 매개 변수를 정의 합니다. 이름은 대상 템플릿의 매개 변수 이름과 일치 해야 합니다. ,, 및에서 값을 선택 `Cell` `Column` `Parameter` `Static Value` 합니다. 대상 템플릿에 해당 매개 변수를 전달 하기 위해 이름 및 값은 비워 둘 수 없습니다.|

위의 각 설정에 대해 작성자는 연결 된 통합 문서의 값을 가져올 위치를 선택 해야 합니다. [링크 소스](#link-sources) 참조

통합 문서 링크가 열리면 위의 설정에서 구성 된 모든 값이 새 통합 문서 보기에 전달 됩니다.

![통합 문서 링크 설정의 스크린샷](./media/workbooks-link-actions/workbook-link-settings.png)

![통합 문서 템플릿 매개 변수 설정의 스크린샷](./media/workbooks-link-actions/workbook-template-link-settings-parameter.png)

## <a name="link-sources"></a>소스 링크

| 원본 | 설명 |
|:------------- |:-------------|
| `Cell` | 그러면 표에서 해당 셀의 값이 링크 값으로 사용 됩니다. |
| `Column` | 이를 선택 하면 작성자가 표에서 다른 열을 선택할 수 있도록 다른 필드가 표시 됩니다.  행에 대 한 해당 열의 값은 링크 값에 사용 됩니다. 이는 일반적으로 필드를로 설정 하 여 표의 각 행이 다른 템플릿을 열거나, `Template Id` `column` `Workbook resources` 필드가 Azure 리소스 ID를 포함 하는 열로 설정 된 경우 다른 리소스에 대해 동일한 통합 문서 템플릿을 여는 데 사용 됩니다. |
| `Parameter` | 이를 선택 하면 작성자가 매개 변수를 선택할 수 있도록 다른 필드가 표시 됩니다. 링크를 클릭 하면 해당 매개 변수의 값이 값에 사용 됩니다. |
| `Static value` | 이를 선택 하면 연결 된 통합 문서에서 사용 되는 정적 값을 author 형식으로 사용할 수 있도록 다른 필드가 표시 됩니다. 이는 표에 있는 모든 행이 필드에 대해 동일한 값을 사용 하는 경우에 일반적으로 사용 됩니다. |
| `Step` | 통합 문서의 현재 단계에 설정 된 값을 사용 합니다. 이는 연결 된 통합 문서의 통합 문서 리소스를 현재 통합 문서가 아닌 *쿼리/메트릭 단계에서* 사용 되는 것으로 설정 하는 쿼리 및 메트릭 단계에서 일반적입니다. |
| `Workbook` | 현재 통합 문서에 설정 된 값을 사용 합니다. |
| `Default` | 값을 지정 하지 않은 경우 사용 되는 기본값을 사용 합니다. 이는 갤러리 유형에 서 일반적입니다. 여기서 기본 갤러리는 소유자 리소스의 유형에 의해 설정 됩니다. |

## <a name="next-steps"></a>다음 단계

- 통합 문서 리소스에 대 한 액세스를 [제어](../visualize/workbooks-access-control.md) 하 고 공유 합니다.
- [통합 문서에서 그룹](../visualize/workbooks-groups.md)을 사용 하는 방법을 알아봅니다.