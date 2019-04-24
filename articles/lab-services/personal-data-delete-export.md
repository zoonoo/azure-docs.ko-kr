---
title: Azure DevTest Labs에서 개인 데이터를 삭제하고 내보내는 방법 | Microsoft Docs
description: GDPR(일반 데이터 보호 규정)에 따라 사용자의 의무를 지원하기 위해 Azure DevLast Labs 서비스에서 개인 데이터 삭제 및 내보내는 방법에 대해 알아봅니다.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2018
ms.author: spelluru
ms.openlocfilehash: e681652c13e521bd33524e247db65088f47a794c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60394989"
---
# <a name="export-or-delete-personal-data-from-azure-devtest-labs"></a>Azure DevTest Labs에서 개인 데이터 내보내기 또는 삭제
이 문서에서는 Azure DevTest Labs 서비스에서 개인 데이터를 삭제하고 내보내는 단계를 제공합니다. 

## <a name="what-personal-data-does-devtest-labs-collect"></a>DevTest Labs에서는 어떤 개인 데이터를 수집하나요?
DevTest Labs에서는 사용자로부터 두 가지 주요 개인 데이터를 수집합니다. 이는 사용자 이메일 주소와 사용자 개체 ID입니다. 이 정보는 서비스가 랩 관리자 및 랩 사용자에게 서비스 중인 기능을 제공하는 데 중요합니다.

### <a name="user-email-address"></a>사용자 이메일 주소
DevTest Labs에서는 사용자 이메일 주소를 사용하여 랩 사용자에게 자동 종료 이메일 알림을 보냅니다. 이메일은 사용자에게 머신이 종료되었음을 알립니다. 원하는 경우 사용자는 종료를 연기하거나 건너뛸 수 있습니다. 랩 수준 또는 VM 수준에서 이메일 주소를 구성합니다.

**랩에서 이메일 설정:**

![랩 수준에서 이메일 설정](./media/personal-data-delete-export/lab-user-email.png)

**VM에서 이메일 설정:**

![VM 수준에서 이메일 설정](./media/personal-data-delete-export/vm-user-email.png)

### <a name="user-object-id"></a>사용자 개체 ID
DevTest Labs는 사용자 개체 ID를 사용하여 랩 관리자에게 월별 비용 추세와 리소스별 비용을 보여줍니다. 이를 통해 비용을 추적하고 랩에 대한 임계값을 관리할 수 있습니다. 

**당월의 예상 비용 추세:**
![당월의 예상 비용 추세](./media/personal-data-delete-export/estimated-cost-trend-per-month.png)

**리소스별 예상 월간 누계 비용:**
![리소스별 예상 월간 누계 비용](./media/personal-data-delete-export/estimated-month-to-date-cost-by-resource.png)


## <a name="why-do-we-need-this-personal-data"></a>이 개인 데이터가 필요한 이유는?
DevTest Labs 서비스는 작업 목적을 위해 개인 데이터를 사용합니다. 이 데이터는 서비스에서 주요 기능을 제공하는 데 중요합니다. 사용자 이메일 주소에 보존 정책을 설정하면 랩 사용자는 이메일 주소를 삭제한 후에는 적시에 자동 종료 이메일 알림을 받지 못합니다. 마찬가지로, 보존 정책에 따라 사용자 개체 ID가 삭제되는 경우 랩 관리자는 랩에 있는 머신의 월별 비용 추세 및 리소스별 비용을 볼 수 없습니다. 따라서 이 데이터는 사용자의 리소스가 랩에서 활성 상태인 동안 유지되어야 합니다.

## <a name="how-can-i-have-the-system-to-forget-my-personal-data"></a>어떻게 하면 시스템에서 내 개인 데이터를 잊어버릴 수 있나요?
랩 사용자로서 이 개인 데이터를 삭제하려는 경우 랩에서 해당 리소스를 삭제하면 됩니다. DevTest Labs 서비스는 삭제된 개인 데이터를 사용자가 삭제한 후 30일 동안 익명화합니다.

예를 들어 VM을 삭제하거나 이메일 주소를 제거하는 경우 DevTest Labs 서비스는 리소스가 삭제된 후 30일 동안 이 데이터를 익명화합니다. 삭제 후 30일 보존 정책은 랩 관리자에게 월별 정확한 비용 예측을 제공하는지 확인하는 것입니다.

## <a name="how-can-i-request-an-export-on-my-personal-data"></a>내 개인 데이터에 대한 내보내기는 어떻게 요청할 수 있나요?
랩 사용자인 경우 DevTest Labs 서비스가 저장하는 개인 데이터에 대한 내보내기를 요청할 수 있습니다. 내보내기를 요청하려면 랩의 **개요** 페이지에 있는 **개인 데이터** 옵션으로 이동합니다. **내보내기 요청** 단추를 선택하면 랩 관리자의 스토리지 계정에서 다운로드 가능한 Excel 파일 생성이 시작됩니다. 그런 다음, 랩 관리자에게 문의하여 이 데이터를 볼 수 있습니다.

1. 왼쪽 메뉴에서 **개인 데이터**를 선택합니다. 

    ![개인 데이터 페이지](./media/personal-data-delete-export/personal-data-page.png)
2. 랩을 포함하는 **리소스 그룹**을 선택합니다.

    ![리소스 그룹 선택](./media/personal-data-delete-export/select-resource-group.png)
3. 리소스 그룹에서 **스토리지 계정**을 선택합니다.
4. **스토리지 계정** 페이지에서 **Blob**을 선택합니다.

    ![Blob 타일 선택](./media/personal-data-delete-export/select-blobs-tile.png)
5. 컨테이너를 목록에서 **labresourceusage**라는 컨테이너를 선택합니다.

    ![Blob 컨테이너 선택](./media/personal-data-delete-export/select-blob-container.png)
6. 랩 이름을 딴 **폴더**를 선택합니다. 이 폴더의 랩에서 **디스크** 및 **가상 머신**에 대한 **csv** 파일을 찾습니다. 이러한 csv 파일을 다운로드하고, 액세스를 요청하는 랩 사용자의 콘텐츠를 필터링하고, 공유할 수 있습니다.

    ![CSV 파일 다운로드](./media/personal-data-delete-export/download-csv-file.png)

## <a name="next-steps"></a>다음 단계
다음 문서를 참조하세요. 

- [랩에 대한 정책 설정](devtest-lab-get-started-with-lab-policies.md)
- [질문과 대답](devtest-lab-faq.md)
