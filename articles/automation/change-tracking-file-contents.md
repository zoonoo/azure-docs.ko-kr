---
title: Azure Automation으로 파일 콘텐츠 변경 내용 보기
description: 변경 내용 추적의 파일 콘텐츠 변경 기능을 사용하여 변경된 파일의 내용을 봅니다.
services: automation
ms.subservice: change-inventory-management
ms.date: 07/03/2018
ms.topic: conceptual
ms.openlocfilehash: 57c3c2c7a0c923921c727ccea7839940457bc1ee
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81682991"
---
# <a name="view-contents-of-a-file-that-is-being-tracked-with-change-tracking"></a>변경 내용 추적으로 추적 중인 파일의 콘텐츠 보기

파일 콘텐츠 추적을 사용하면 변경 내용 추적으로 추적하고 있는 변경 전후의 파일 콘텐츠를 볼 수 있습니다. 이렇게 하려면 각 변경 작업 수행 후 스토리지 계정에 파일 콘텐츠를 저장합니다.

## <a name="requirements"></a>요구 사항

* 파일 콘텐츠를 저장하려면 Resource Manager 배포 모델을 사용하는 표준 스토리지 계정이 필요합니다. 프리미엄 및 클래식 배포 모델 스토리지 계정은 사용하면 안 됩니다. 저장소 계정에 대한 자세한 내용은 [Azure 저장소 계정 정보를 참조하십시오.](../storage/common/storage-create-storage-account.md)

* 사용된 저장소 계정에는 1개의 자동화 계정만 연결할 수 있습니다.

* [변경 사항은](automation-change-tracking.md) 자동화 계정에서 활성화되어 있습니다.

## <a name="enable-file-content-tracking"></a>파일 콘텐츠 추적 사용

1. Azure Portal에서 Automation 계정을 연 후 **변경 내용 추적**을 선택합니다.
2. 상단 메뉴에서 **설정 편집**을 선택합니다.
3. **파일 콘텐츠**를 선택하고 **연결**을 클릭합니다. **변경 내용 추적을 위한 콘텐츠 위치 추가** 창이 열립니다.

   ![enable](./media/change-tracking-file-contents/enable.png)

4. 파일 콘텐츠를 저장하는 데 사용할 구독 및 스토리지 계정을 선택합니다. 기존의 모든 추적된 파일에 대해 파일 콘텐츠 추적을 사용하려면 **모든 설정에 대한 파일 콘텐츠 업로드**에 대해 **켜기**를 선택합니다. 나중에 각 파일 경로에 대해 이 설정을 변경할 수 있습니다.

   ![스토리지 계정 설정](./media/change-tracking-file-contents/storage-account.png)

5. 사용하도록 설정하면 스토리지 계정 및 SAS URI가 표시됩니다. SAS URI는 365일 후에 만료되며 **다시 생성** 단추를 클릭하여 다시 만들 수 있습니다.

   ![계정 키 나열](./media/change-tracking-file-contents/account-keys.png)

## <a name="add-a-file"></a>파일 추가

다음 단계에서는 파일에 대한 변경 내용 추적을 설정하는 과정을 안내합니다.

1. **변경 추적의**설정 편집 페이지에서 Windows **파일** 또는 **Linux 파일** 탭을 선택하고 **추가를** 클릭합니다.

1. 파일 경로에 대한 정보를 입력하고 **모든 설정에 대한 파일 콘텐츠 업로드**에서 True를 선택합니다. 이 설정은 해당 파일 경로에 대해서만 파일 콘텐츠 추적을 사용합니다.

   ![linux 파일 추가](./media/change-tracking-file-contents/add-linux-file.png)

## <a name="viewing-the-contents-of-a-tracked-file"></a>추적된 파일의 콘텐츠 보기

1. 파일 또는 경로의 파일에 대한 변경 내용이 검색되면 포털에 표시됩니다. 변경 내용 목록에서 파일 변경을 선택합니다. 변경 세부 정보 창이 표시됩니다.

   ![변경 내용 나열](./media/change-tracking-file-contents/change-list.png)

1. 세부 정보 변경 창에는 파일 정보 전후의 표준이 표시됩니다. **파일 컨텐츠 변경 내용을** 보려면 파일 콘텐츠 변경 내용 보기를 선택합니다.

   ![변경 세부 정보](./media/change-tracking-file-contents/change-details.png)

1. 새 페이지에는 파일 콘텐츠가 나란히 보기로 표시됩니다. **인라인**을 선택하여 변경 내용의 인라인 보기를 표시할 수도 있습니다.

   ![파일 변경 내용 보기](./media/change-tracking-file-contents/view-file-changes.png)

## <a name="next-steps"></a>다음 단계

솔루션 사용에 대한 자세한 내용은 변경 내용 추적에 대한 자습서를 참조하세요.

> [!div class="nextstepaction"]
> [환경 변경 문제 해결](automation-tutorial-troubleshoot-changes.md)

* [Azure Monitor 로그에서 로그 검색을](../log-analytics/log-analytics-log-searches.md) 사용하여 자세한 변경 변경 추적 데이터를 볼 수 있습니다.

