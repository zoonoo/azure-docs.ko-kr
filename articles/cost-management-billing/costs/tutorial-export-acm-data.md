---
title: 자습서 - Azure Cost Management에서 내보낸 데이터 만들기 및 관리
description: 이 문서에서는 내보낸 Azure Cost Management 데이터를 외부 시스템에서 사용할 수 있도록 만들고 관리하는 방법을 보여줍니다.
author: bandersmsft
ms.author: banders
ms.date: 02/12/2020
ms.topic: tutorial
ms.service: cost-management-billing
ms.reviewer: adwise
ms.custom: seodec18
ms.openlocfilehash: 04228a5f3f7e0b10e3cd881043af7861bb9fb347
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/03/2020
ms.locfileid: "78251130"
---
# <a name="tutorial-create-and-manage-exported-data"></a>자습서: 내보낸 데이터 만들기 및 관리

비용 분석 자습서를 읽으셨다면 Cost Management 데이터를 수동으로 다운로드하는 방법에 익숙하실 것입니다. 그러나 매일, 매주 또는 매월 자동으로 Azure Storage에 Cost Management 데이터를 내보내는 되풀이 작업을 만들 수 있습니다. 내보낸 데이터는 CSV 형식이며 Cost Management에서 수집한 모든 정보를 포함하고 있습니다. Azure Storage로 내보낸 데이터를 외부 시스템에 사용하고 사용자 고유의 사용자 지정 데이터와 결합할 수 있습니다. 그리고 내보낸 데이터를 대시보드나 기타 재무 시스템 같은 외부 시스템에서 사용할 수 있습니다.

Azure Storage로의 Azure 비용 데이터 예약된 내보내기를 만드는 방법에 대해서는 [Azure Cost Management를 사용하여 스토리지로의 내보내기를 예약하는 방법](https://www.youtube.com/watch?v=rWa_xI1aRzo) 비디오를 시청하세요.

이 자습서의 예제는 비용 관리 데이터를 내보낸 후 데이터 내보내기가 성공했는지 확인하는 단계를 안내합니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * 일일 내보내기 만들기
> * 데이터가 수집되는지 확인

## <a name="prerequisites"></a>사전 요구 사항
데이터 내보내기는 [EA(기업계약)](https://azure.microsoft.com/pricing/enterprise-agreement/) 및 [Microsoft 고객 계약](get-started-partners.md) 고객을 포함한 다양한 종류의 Azure 계정에 사용할 수 있습니다. 지원되는 계정 유형의 전체 목록을 보려면 [Cost Management 데이터 이해](understand-cost-mgt-data.md)를 참조하세요. 다음 Azure 사용 권한 또는 범위는 사용자 및 그룹별 데이터 내보내기에 대해 구독별로 지원됩니다. 범위에 대한 자세한 내용은 [범위 이해 및 작업](understand-work-scopes.md)을 참조하세요.

- 소유자 – 구독에 대한 예약된 내보내기를 만들고, 수정하거나 삭제할 수 있습니다.
- 기여자 – 자신의 예약된 내보내기를 만들고, 수정하거나 삭제할 수 있습니다. 다른 사용자가 만든 예약된 내보내기의 이름을 수정할 수 있습니다.
- 읽기 권한자 - 사용 권한이 있는 내보내기를 예약할 수 있습니다.

Azure Storage 계정의 경우:
- 내보내기에 대한 사용 권한과 상관 없이 구성된 스토리지 계정을 변경하려면 쓰기 사용 권한이 필요합니다.
- BLOB 또는 파일 스토리지에 대해 Azure 스토리지 계정이 구성되어 있어야 합니다.

새 구독이 있는 경우 Cost Management 기능을 즉시 사용할 수 없습니다. 모든 Cost Management 기능을 사용하려면 최대 48시간이 걸릴 수 있습니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인
[https://portal.azure.com](https://portal.azure.com/)에서 Azure Portal에 로그인합니다.

## <a name="create-a-daily-export"></a>일일 내보내기 만들기

데이터 내보내기를 만들거나 보려면 또는 내보내기를 예약하려면 Azure Portal에서 원하는 범위를 열고 메뉴에서 **비용 분석**을 선택합니다. 예를 들어 **구독**으로 이동하여 목록에서 구독을 선택한 다음, 메뉴에서 **비용 분석**을 선택합니다. 비용 분석 페이지의 맨 위에 있는 **내보내기**를 선택한 다음, 내보내기 옵션을 선택합니다. 예를 들어,**내보내기 예약**을 선택합니다.  

> [!NOTE]
> - 구독 외에도 리소스 그룹, 계정, 부서 및 등록에서 내보내기를 만들 수 있습니다. 범위에 대한 자세한 내용은 [범위 이해 및 작업](understand-work-scopes.md)을 참조하세요.
>- 청구 계정 범위 또는 고객 테넌트에서 파트너로 로그인한 경우 파트너 스토리지 계정에 연결된 Azure Storage 계정으로 데이터를 내보낼 수 있습니다. 그러나 CSP 테넌트에 활성 구독이 있어야 합니다.
>


**추가**를 선택하고 내보내기의 이름을 입력한 다음, **월간 누계 비용 매일 내보내기** 옵션을 선택합니다. **다음**을 선택합니다.

![내보내기 형식을 표시하는 새 내보내기 예제](./media/tutorial-export-acm-data/basics_exports.png)

Azure Storage 계정에 대한 구독을 지정하고 스토리지 계정을 선택합니다.  내보내기 파일을 보낼 스토리지 컨테이너 및 디렉터리 경로를 지정합니다. **다음**을 선택합니다.

![스토리지 계정 세부 정보를 표시하는 새 내보내기 예제](./media/tutorial-export-acm-data/storage_exports.png)

내보내기 세부 정보를 검토하고 **만들기**를 선택합니다.

새 내보내기는 내보내기 목록에 표시됩니다. 기본적으로 새 내보내기가 사용되도록 설정됩니다. 예정된 내보내기를 해제 또는 삭제하려면 목록에서 항목을 선택한 다음, **사용 안 함** 또는 **삭제**를 선택합니다.

처음에는 내보내기가 실행될 때까지 1~2시간이 걸릴 수 있습니다. 그러나 내보낸 파일에 데이터가 표시될 때까지는 최대 4시간이 걸릴 수 있습니다.

### <a name="export-schedule"></a>내보내기 예약

예약된 내보내기는 처음에 내보내기를 만든 요일과 시간의 영향을 받습니다. 예약된 내보내기를 만들면 이후의 각 내보내기 발생에 대해 동일한 빈도로 내보내기가 실행됩니다. 예를 들어 일별 빈도로 월간 누계 내보내기를 설정하는 경우 내보내기가 매일 실행됩니다. 마찬가지로 매주 내보내기의 경우에는 예약된 것과 동일한 요일에 내보내기가 매주 실행됩니다. 내보내기의 정확한 배달 시간은 보장되지 않으며, 내보낸 데이터는 런타임의 4시간 이내에 사용할 수 있습니다.
각 내보내기는 새 파일을 만들므로 이전의 내보내기를 덮어쓰지 않습니다.

내보내기 옵션에는 다음과 같은 두 가지 유형이 있습니다.

**월간 누계 비용 매일 내보내기** – 초기 내보내기가 즉시 실행됩니다. 후속 내보내기는 다음 날의 초기 내보내기와 같은 시간에 실행됩니다. 최신 데이터는 이전의 매일 내보내기에서 집계됩니다.

**사용자 지정** – 주별 누계 및 월간 누계 옵션을 사용하여 주간 및 월간 내보내기를 예약할 수 있습니다. *초기 내보내기는 즉시 실행됩니다.*

종량제, MSDN 또는 Visual Studio 구독이 있는 경우 송장 청구 기간이 달력상 월과 다를 수 있습니다. 구독 및 리소스 그룹의 형식의 경우 송장 기간 또는 달력상 월에 맞춰 조정된 내보내기를 만들 수 있습니다. 송장 월에 맞춰 조정된 내보내기를 만들려면 **사용자 지정**으로 이동한 다음, **Billing-period-to-date**를 선택합니다.  달력상 월에 맞춰 조정된 내보내기를 만들려면 **Month-to-date**를 선택합니다.
>
>

![새 내보내기 - 사용자 지정 주별 누계 선택 항목을 보여 주는 기본 사항 탭](./media/tutorial-export-acm-data/tutorial-export-schedule-weekly-week-to-date.png)

## <a name="verify-that-data-is-collected"></a>데이터가 수집되는지 확인

Azure Storage Explorer를 사용하여 간단하게 Cost Management 데이터가 수집되는지 확인하고 내보낸 CSV 파일을 볼 수 있습니다.

내보내기 목록에서 스토리지 계정 이름을 선택합니다. 스토리지 계정 페이지의 탐색기에서 열기를 선택합니다. 확인 상자가 나타나면 **예**를 선택하여 Azure Storage Explorer에서 파일을 엽니다.

![예제 정보 및 탐색기에서 열기 링크를 표시하는 스토리지 계정 페이지](./media/tutorial-export-acm-data/storage-account-page.png)

Storage Explorer에서 열고 싶은 컨테이너로 이동한 후 현재 월에 해당하는 폴더를 선택합니다. CSV 파일 목록이 표시됩니다. 파일을 하나 선택하고 **열기**를 선택합니다.

![Storage Explorer에 표시되는 예제 정보](./media/tutorial-export-acm-data/storage-explorer.png)

CSV 파일 확장명을 열도록 설정된 프로그램 또는 애플리케이션에서 파일이 열립니다. 다음은 Excel 예제입니다.

![Excel에 표시되는 내보낸 CSV 데이터 예제](./media/tutorial-export-acm-data/example-export-data.png)


## <a name="access-exported-data-from-other-systems"></a>다른 시스템에서 내보낸 데이터에 액세스

Cost Management 데이터를 내보내는 목적 중 하나는 외부 시스템에서 데이터에 액세스하는 것입니다. 대시보드 시스템 또는 기타 재무 시스템을 사용할 수 있습니다. 이러한 시스템의 종류는 매우 다양하기 때문에 예제를 보여드리는 것이 별 의미가 없습니다.  하지만 [Azure Storage 소개](../../storage/common/storage-introduction.md) 문서를 참조하여 애플리케이션에서 데이터에 액세스하는 방법을 알아볼 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 작업 방법을 알아보았습니다.

> [!div class="checklist"]
> * 일일 내보내기 만들기
> * 데이터가 수집되는지 확인

그 다음 자습서로 넘어가서 유휴 리소스 및 미달 사용 리소스를 식별하여 최적화하고 효율성을 개선하는 방법을 알아보세요.

> [!div class="nextstepaction"]
> [최적화 권장 사항 검토 및 필요한 조치 수행](tutorial-acm-opt-recommendations.md)
