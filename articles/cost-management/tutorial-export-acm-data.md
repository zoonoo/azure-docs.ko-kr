---
title: 자습서 - Azure Cost Management에서 내보낸 데이터를 만들고 관리 | Microsoft Docs
description: 이 문서에서는 외부 시스템에서 사용할 수 있도록 내보낸 Azure Cost Management 데이터를 만들고 관리하는 방법을 보여줍니다.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 09/21/2018
ms.topic: tutorial
ms.service: cost-management
manager: dougeby
ms.custom: ''
ms.openlocfilehash: 0765e79b90eed49742f5eead33063907eb1db1f4
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47030849"
---
# <a name="tutorial-create-and-manage-exported-data"></a>자습서: 내보낸 데이터를 만들고 관리

비용 분석 자습서를 읽으셨다면 Cost Management 데이터를 수동으로 다운로드하는 방법에 익숙하실 것입니다. 그러나 날마다 자동으로 Azure 저장소에 Cost Management 데이터를 내보내는 일별 되풀이 작업을 만들 수 있습니다. 내보낸 데이터는 CSV 형식이며 Cost Management에서 수집한 모든 정보를 포함하고 있습니다. Azure 저장소로 내보낸 데이터를 외부 시스템에 사용하고 사용자 고유의 사용자 지정 데이터와 결합할 수 있습니다. 그리고 내보낸 데이터를 대시보드나 기타 재무 시스템 같은 외부 시스템에서 사용할 수 있습니다.

이 자습서의 예제는 비용 관리 데이터를 내보낸 후 데이터 내보내기가 성공했는지 확인하는 단계를 안내합니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 일일 내보내기 만들기
> * 데이터가 수집되는지 확인

## <a name="prerequisites"></a>필수 조건

데이터 내보내기는 [모든 EA(기업계약)](https://azure.microsoft.com/pricing/enterprise-agreement/) 고객이 사용할 수 있습니다. 다음 Azure 사용 권한은 사용자 및 그룹별 데이터 내보내기에 대한 구독에 따라 지원됩니다.

- 소유자 – 구독에 대한 예약된 내보내기를 만들고, 수정하거나 삭제할 수 있습니다.
- 기여자 – 자신의 예약된 내보내기를 만들고, 수정하거나 삭제할 수 있습니다. 다른 사용자가 만든 예약된 내보내기의 이름을 수정할 수 있습니다.
- 읽기 권한자 - 사용 권한이 있는 내보내기를 예약할 수 있습니다.

Azure Storage 계정의 경우:
- 내보내기에 대한 사용 권한과 상관 없이 구성된 저장소 계정을 변경하려면 쓰기 사용 권한이 필요합니다.
- BLOB 또는 파일 저장소에 대해 Azure 저장소 계정이 구성되어 있어야 합니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인
[https://portal.azure.com](https://portal.azure.com/)에서 Azure Portal에 로그인합니다.

## <a name="create-a-daily-export"></a>일일 내보내기 만들기

비용 관리 + 청구 &gt; 구독 &gt; 내보내기 &gt; **추가**에서 구독 또는 리소스 그룹을 선택합니다.

내보내기의 이름을 입력하고 구독, Azure 저장소 계정, 컨테이너 및 파일 저장소 디렉터리 또는 BLOB 컨테이너를 지정한 다음, **만들기**를 클릭합니다.

![새 내보내기](./media/tutorial-export-acm-data/new-export01.png)

새 내보내기는 내보내기 목록에 표시됩니다. 기본적으로 새 내보내기가 사용되며, 매일 실행됩니다. 예정된 내보내기를 해제 또는 삭제하려면 목록에서 항목을 클릭하고 **사용 안 함** 또는 **삭제**를 클릭합니다.

처음에는 내보내기가 실행될 때까지 1~2시간이 걸릴 수 있습니다. 그러나 내보낸 파일에 데이터가 표시될 때까지는 최대 4시간이 걸릴 수 있습니다.

## <a name="verify-that-data-is-collected"></a>데이터가 수집되는지 확인

Azure Storage 탐색기를 사용하여 간단하게 Cost Management 데이터가 수집되는지 확인하고 내보낸 CSV 파일을 볼 수 있습니다.

내보내기 목록에서 저장소 계정 이름을 클릭합니다. 저장소 계정 페이지의 탐색기에서 [열기]를 클릭합니다. 확인 상자가 나타나면 **예**를 클릭하여 Azure Storage 탐색기에서 파일을 엽니다.

![저장소 계정 페이지](./media/tutorial-export-acm-data/storage-account-page.png)

Storage 탐색기에서 열고 싶은 컨테이너로 이동한 후 현재 월에 해당하는 폴더를 선택합니다. CSV 파일 목록이 표시됩니다. 파일 하나를 선택하고 **열기**를 클릭합니다.

![Storage 탐색기](./media/tutorial-export-acm-data/storage-explorer.png)

CSV 파일 확장명을 열도록 설정된 프로그램 또는 응용 프로그램에서 파일이 열립니다. 다음은 Excel 예제입니다.

![데이터 내보내기 예제](./media/tutorial-export-acm-data/example-export-data.png)

## <a name="access-exported-data-from-other-systems"></a>다른 시스템에서 내보낸 데이터에 액세스

Cost Management 데이터를 내보내는 목적 중 하나는 외부 시스템에서 데이터에 액세스하는 것입니다. 대시보드 시스템 또는 기타 재무 시스템을 사용할 수 있습니다. 이러한 시스템의 종류는 매우 다양하기 때문에 예제를 보여드리는 것이 별 의미가 없습니다.  하지만 [Azure Storage 소개](../storage/common/storage-introduction.md) 문서를 참조하여 응용 프로그램에서 데이터에 액세스하는 방법을 알아볼 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * 일일 내보내기 만들기
> * 데이터가 수집되는지 확인

그 다음 자습서로 넘어가서 유휴 리소스 및 미달 사용 리소스를 식별하여 최적화하고 효율성을 개선하는 방법을 알아보세요.

> [!div class="nextstepaction"]
> [최적화 권장 사항 검토 및 필요한 조치 수행](tutorial-acm-opt-recommendations.md)
