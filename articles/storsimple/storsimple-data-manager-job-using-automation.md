---
title: "Azure Automation을 사용하여 작업 트리거 | Microsoft Docs"
description: "Azure Automation을 사용하여 StorSimple 데이터 관리자 작업을 트리거하는 방법에 대해 알아보기(비공개 미리 보기)"
services: storsimple
documentationcenter: NA
author: vidarmsft
manager: syadav
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 03/16/2017
ms.author: vidarmsft
ms.openlocfilehash: 9691408bcd80afb6eba534f26749b76dd3bfe315
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2017
---
# <a name="use-azure-automation-to-trigger-a-job-private-preview"></a>Azure Automation을 사용하여 작업 트리거(비공개 미리 보기)

이 문서에서는 Azure Automation을 사용하여 StorSimple 데이터 관리자 작업을 트리거하는 방법에 대해 설명합니다.

## <a name="prerequisites"></a>필수 조건

시작하기 전에 다음 항목이 있어야 합니다.

*   Azure Powershell을 설치합니다. [Azure Powershell을 다운로드합니다](https://azure.microsoft.com/documentation/articles/powershell-install-configure/).
*   데이터 변환 작업을 초기화하는 구성 설정 및 이러한 설정을 가져오는 지침도 여기에 포함됩니다.
*   리소스 그룹 내의 하이브리드 데이터 리소스에서 올바르게 구성된 작업 정의입니다.
*   `DataTransformationApp.zip` [zip](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/raw/master/Azure%20Automation%20For%20Data%20Manager/DataTransformationApp.zip) 파일을 github 리포지토리에서 다운로드합니다.
*   `Get-ConfigurationParams.ps1` [스크립트](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/blob/master/Azure%20Automation%20For%20Data%20Manager/Get-ConfigurationParams.ps1)를 github 리포지토리에서 다운로드합니다.
*   `Trigger-DataTransformation-Job.ps1` [스크립트](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/blob/master/Azure%20Automation%20For%20Data%20Manager/Trigger-DataTransformation-Job.ps1)를 github 리포지토리에서 다운로드합니다.

## <a name="step-by-step"></a>단계별 과정

### <a name="get-azure-active-directory-permissions-for-the-automation-job-to-run-the-job-definition"></a>작업 정의 실행을 위해 자동화 작업에 대한 Azure Active Directory 사용 권한 가져오기

1. Active Directory에 대한 구성 매개 변수를 검색하려면 다음 단계를 수행합니다.

    1. 로컬 컴퓨터에서 Windows PowerShell을 엽니다. [Azure PowerShell](https://azure.microsoft.com/downloads/)이 설치되었는지 확인합니다.
    1. `Get-ConfigurationParams.ps1` 스크립트(위에서 다운로드한 폴더에 있음)를 실행합니다. PowerShell 창에 다음 명령을 입력합니다.

        ```
        .\Get-ConfigurationParams.ps1 -SubscriptionName "AzureSubscriptionName" -ActiveDirectoryKey "AnyRandomPassword" -AppName "ApplicationName"
         ```

        ActiveDirectoryKey는 나중에 사용하는 암호입니다. 사용자가 선택한 암호를 입력합니다. AppName에는 아무 문자열이나 사용할 수 있습니다.

2. 이 스크립트는 Automation Runbook을 트리거하는 동안 사용해야 하는 다음 값을 출력합니다. 이러한 값을 기록해 둡니다.

    - 클라이언트 ID
    - 테넌트 ID
    - Active Directory 키(위에서 입력한 것과 동일)
    - 구독 ID

### <a name="set-up-the-automation-account"></a>Automation 계정 설정

1. Azure에서 로그인하고 사용자의 Automation 계정을 엽니다.
2. **자산** 타일을 클릭하여 자산 목록을 엽니다.
3. **모듈** 타일을 클릭하여 모듈 목록을 엽니다.
4. **+ 모듈 추가** 단추를 클릭하면 모듈 추가 블레이드가 시작됩니다.

    ![Automation 계정 설정](./media/storsimple-data-manager-job-using-automation/add-module1m.png)

5. 로컬 컴퓨터에서 `DataTransformationApp.zip` 파일을 클릭한 후, **확인**을 클릭하여 모듈을 가져옵니다.

   Azure Automation이 사용자 계정에 모듈을 가져오면 모듈에 대한 메타데이터를 추출합니다. 이 작업에는 몇 분 정도 걸릴 수 있습니다.

   ![Automation 계정 설정](./media/storsimple-data-manager-job-using-automation/add-module2m.png)

   

6. 모듈이 배포되는 도중 및 완료 시점에 알림이 표시됩니다.  **모듈** 타일에서 상태를 확인할 수도 있습니다.

### <a name="to-import-the-runbook-that-triggers-the-job-definition"></a>작업 정의를 트리거하는 Runbook을 가져오려면 다음을 수행합니다.

1. Azure 포털에서 자동화 계정을 엽니다.
2. **Runbook** 타일을 클릭하여 Runbook 목록을 엽니다.
3. **+ Runbook 추가**를 클릭한 다음, **기존 Runbook 가져오기**를 클릭합니다.

   ![기존 Runbook 가져오기](./media/storsimple-data-manager-job-using-automation/import-a-runbook.png)

4. **Runbook 파일**을 클릭하고 `Trigger-DataTransformation-Job.ps1`을 가져올 파일을 선택합니다.
5. **만들기**를 클릭하여 Runbook을 가져옵니다. 새 Runbook이 Automation 계정의 Runbook 목록에 표시됩니다.
7. **Trigger-DataTransformation-Job** Runbook을 클릭한 다음 **편집**을 클릭합니다.
8. 확인 메시지가 표시되면 **게시**, **예**를 차례로 클릭합니다.


### <a name="to-run-the-runbook"></a>Runbook을 실행하려면 다음을 수행합니다.
1. Azure 포털에서 자동화 계정을 엽니다.
2. **Runbook** 타일을 클릭하여 Runbook 목록을 엽니다.
3. **Trigger-DataTransformation-Job**을 클릭합니다.
4. **시작**을 클릭하여 runbook을 시작합니다.

   ![Runbook 시작](./media/storsimple-data-manager-job-using-automation/run-runbook1m.png)

5. **Runbook 시작** 블레이드에서 모든 매개 변수를 입력합니다. **확인**을 클릭하여 데이터 변환 작업을 제출합니다.

   ![Runbook 시작](./media/storsimple-data-manager-job-using-automation/run-runbook2m.png)


## <a name="next-steps"></a>다음 단계

[StorSimple 데이터 관리자 UI를 사용하여 데이터를 변환합니다](storsimple-data-manager-ui.md).