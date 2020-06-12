---
title: Azure Automation에서 변경 내용 추적 및 인벤토리 관리
description: 이 문서에서는 변경 내용 추적 및 인벤토리를 사용하여 사용자 환경에서 소프트웨어 및 Microsoft 서비스 변경 내용을 추적하는 방법을 설명합니다.
services: automation
ms.subservice: change-inventory-management
ms.date: 07/03/2018
ms.topic: conceptual
ms.openlocfilehash: 4b8bf6a3f583e4c17f61e0a46911990ac5cc827c
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/25/2020
ms.locfileid: "83830483"
---
# <a name="manage-change-tracking-and-inventory"></a>변경 내용 추적 및 인벤토리 관리

Azure Automation을 사용하여 사용자 환경의 머신에 대한 [변경 내용 추적 및 인벤토리](change-tracking.md) 기능을 사용하도록 설정할 수 있습니다. 이 기능은 레지스트리 키, 파일, 콘텐츠 등을 추적하고 사용 가능한 변경 내용을 작성합니다. 이 문서에는 이 기능을 사용하는 절차가 포함되어 있습니다.

## <a name="enable-the-full-change-tracking-and-inventory-feature"></a>전체 변경 내용 추적 및 인벤토리 기능 사용 설정

[Azure Security Center FIM(파일 무결성 모니터링)](https://docs.microsoft.com/azure/security-center/security-center-file-integrity-monitoring)을 사용하도록 설정한 경우 아래에 설명된 대로 머신에 대한 전체 변경 내용 추적 및 인벤토리 기능을 사용할 수 있습니다. 이 프로세스에서는 사용자의 설정을 제거하지 않습니다.

> [!NOTE]
> 전체 변경 내용 추적 및 인벤토리 기능을 사용하도록 설정하면 추가 요금이 발생할 수 있습니다. [Automation 가격 책정](https://azure.microsoft.com/pricing/details/automation/)을 참조하세요.

1. 작업 영역으로 이동하여 [설치된 모니터링 솔루션의 목록](../azure-monitor/insights/solutions.md#list-installed-monitoring-solutions)에서 해당 작업 영역을 찾아 모니터링 솔루션을 제거합니다.
2. [모니터링 솔루션 제거](../azure-monitor/insights/solutions.md#remove-a-monitoring-solution)에 자세히 설명된 대로 솔루션 이름을 클릭하여 요약 페이지를 연 다음 **삭제**를 클릭합니다.
3. 변경 내용 추적 및 인벤토리를 다시 사용하도록 설정하려면 Automation 계정으로 이동하고 **구성 관리** 아래에서 **변경 내용 추적** 또는 **인벤토리**를 선택합니다.
4. Log Analytics 작업 영역과 Automation 계정을 선택하고, 작업 영역 설정을 확인한 다음, **사용**을 클릭합니다.

## <a name="enable-machines-for-change-tracking-and-inventory"></a><a name="onboard"></a>머신에서 변경 내용 추적 및 인벤토리 사용 설정

변경 내용 추적을 시작하려면 Azure Automation에서 변경 내용 추적 및 인벤토리를 사용하도록 설정해야 합니다. 다음은 머신에 대해 이 기능을 사용하도록 설정하는 권장 및 지원되는 방법입니다. 

* [가상 머신에서 설정](automation-onboard-solutions-from-vm.md)
* [여러 머신 찾아보기에서 설정](automation-onboard-solutions-from-browse.md)
* [Automation 계정에서 설정](automation-onboard-solutions-from-automation-account.md)
* [Azure Automation Runbook에서 설정](automation-onboard-solutions.md)

## <a name="track-files"></a>파일 추적

### <a name="configure-file-tracking-on-windows"></a>Windows에서 파일 추적 구성

다음 단계를 사용하여 Windows 컴퓨터에서 파일 추적을 구성합니다.

1. Automation 계정의 **구성 관리** 아래에서 **변경 내용 추적**을 선택합니다. 
2. **설정 편집**(기어 기호)을 클릭합니다.
3. 작업 영역 구성 페이지에서 **Windows 파일**을 선택한 다음 **+ 추가**를 클릭하여 추적할 새 파일을 추가합니다.
4. 변경 내용 추적에 대해 Windows 파일 추가 창에서 추적할 파일에 대한 정보를 입력하고 **저장**을 클릭합니다. 다음 표에는 정보에 사용할 수 있는 속성이 정의되어 있습니다.

    |속성  |Description  |
    |---------|---------|
    |사용     | 설정이 적용되면 True이고, 그렇지 않으면 False입니다.        |
    |Item Name     | 추적할 파일의 이름입니다.        |
    |그룹     | 논리적으로 파일을 그룹화하는 그룹 이름입니다.        |
    |경로 입력     | 파일을 확인할 경로입니다(예: **c:\temp\\\*.txt**). `%winDir%\System32\\\*.*`와 같은 환경 변수를 사용할 수도 있습니다.       |
    |경로 유형     | 경로의 유형입니다. 가능한 값은 파일 및 디렉터리입니다.        |    
    |재귀     | 추적할 항목을 찾을 때 재귀가 사용되면 True이 고, 그렇지 않으면 False입니다.        |    
    |파일 콘텐츠 업로드 | 추적된 변경 내용에 대한 파일 콘텐츠를 업로드하려면 True이고, 그렇지 않으면 False입니다.|

5. **파일 콘텐츠 업로드**에 대해 True를 지정해야 합니다. 이 설정은 표시된 파일 경로에 대해 파일 콘텐츠 추적을 사용합니다.

### <a name="configure-file-tracking-on-linux"></a>Linux에서 파일 추적 구성

다음 단계를 사용하여 Linux 컴퓨터에서 추적할 파일을 구성합니다.

1. Automation 계정의 **구성 관리** 아래에서 **변경 내용 추적**을 선택합니다. 
2. **설정 편집**(기어 기호)을 클릭합니다.
3. 작업 영역 구성 페이지에서 **Linux 파일**을 선택한 다음 **+ 추가**를 클릭하여 추적할 새 파일을 추가합니다.
4. 변경 내용 추적에 대해 Linux 파일 추가 창에서 추적할 파일이나 디렉터리의 정보를 입력하고 **저장**을 클릭합니다. 다음 표에는 정보에 사용할 수 있는 속성이 정의되어 있습니다.

    |속성  |Description  |
    |---------|---------|
    |사용     | 설정이 적용되면 True이고, 그렇지 않으면 False입니다.        |
    |Item Name     | 추적할 파일의 이름입니다.        |
    |그룹     | 논리적으로 파일을 그룹화하는 그룹 이름입니다.        |
    |경로 입력     | 파일을 확인할 경로입니다(예: **/etc/*.conf**).       |
    |경로 유형     | 경로의 유형입니다. 가능한 값은 파일 및 디렉터리입니다.        |
    |재귀     | 추적할 항목을 찾을 때 재귀가 사용되면 True이 고, 그렇지 않으면 False입니다.        |
    |sudo 사용     | 항목을 확인할 때 sudo를 사용하려면 True이고, 그렇지 않으면 False입니다.         |
    |링크     | 디렉터리를 트래버스할 때 바로 가기 링크를 처리하는 방법을 결정하는 설정입니다. 가능한 값은 다음과 같습니다.<br> 무시 - 바로 가기 링크가 무시되고 참조된 파일/디렉터리가 포함되지 않습니다.<br>실행 - 재귀 중에 바로 가기 링크를 따르고 참조된 파일/디렉터리도 포함합니다.<br>관리 - 바로 가기 링크를 따르고 반환된 콘텐츠를 변경할 수 있도록 허용합니다. **참고** - 이 옵션은 파일 콘텐츠 검색을 지원하지 않으므로 권장하지 않습니다.    |
    |파일 콘텐츠 업로드 | 추적된 변경 내용에 대한 파일 콘텐츠를 업로드하려면 True이고, 그렇지 않으면 False입니다. |

5. **파일 콘텐츠 업로드**에 대해 True를 지정해야 합니다. 이 설정은 표시된 파일 경로에 대해 파일 콘텐츠 추적을 사용합니다.

   ![Linux 파일 추가](./media/change-tracking-file-contents/add-linux-file.png)

## <a name="track-file-contents"></a>파일 콘텐츠 추적

파일 콘텐츠 추적을 사용하면 추적된 변경 전후의 파일 콘텐츠를 볼 수 있습니다. 이 기능은 각 변경 작업 수행 후 스토리지 계정에 파일 콘텐츠를 저장합니다. 다음은 파일 콘텐츠를 추적하기 위해 따라야 하는 몇 가지 규칙입니다.

* 파일 콘텐츠를 저장하려면 Resource Manager 배포 모델을 사용하는 표준 스토리지 계정이 필요합니다. 

* 프리미엄 및 클래식 배포 모델 스토리지 계정을 사용하지 마세요. [Azure Storage 계정 정보](../storage/common/storage-create-storage-account.md)를 참조하세요.

* 사용하는 스토리지 계정은 하나의 Automation 계정에만 연결할 수 있습니다.

* Automation 계정에서 [변경 내용 추적 및 인벤토리](change-tracking.md)를 사용할 수 있습니다.

### <a name="enable-tracking-for-file-content-changes"></a>파일 콘텐츠 변경 내용 추적 사용 설정

1. Azure Portal에서 Automation 계정을 연 다음 **구성 관리** 아래에서 **변경 내용 추적**을 선택합니다.
2. **설정 편집**(기어 기호)을 클릭합니다.
3. **파일 콘텐츠**를 선택하고 **연결**을 클릭합니다. 그러면 변경 내용 추적을 위한 콘텐츠 위치 추가 창이 열립니다.

   ![콘텐츠 위치 사용 설정](./media/change-tracking-file-contents/enable.png)

4. 파일 콘텐츠를 저장하는 데 사용할 구독 및 스토리지 계정을 선택합니다. 

5. 기존의 모든 추적된 파일에 대해 파일 콘텐츠 추적을 사용하려면 **모든 설정에 대한 파일 콘텐츠 업로드**에 대해 **켜기**를 선택합니다. 나중에 각 파일 경로에 대해 이 설정을 변경할 수 있습니다.

   ![스토리지 계정 설정](./media/change-tracking-file-contents/storage-account.png)

6. 변경 내용 추적 및 인벤토리는 파일 콘텐츠 변경 내용 추적을 사용하는 경우 스토리지 계정 및 SAS(공유 액세스 서명) URI를 표시합니다. 서명은 365일 후 만료되고 **다시 생성**을 클릭하여 다시 만들 수 있습니다.

   ![계정 키 나열](./media/change-tracking-file-contents/account-keys.png)

### <a name="view-the-contents-of-a-tracked-file"></a>추적된 파일의 콘텐츠 보기

변경 내용 추적 및 인벤토리가 추적된 파일에 대한 변경 내용을 감지하면 변경 내용 정보 창에서 파일 콘텐츠를 볼 수 있습니다.  

![변경 내용 나열](./media/change-tracking-file-contents/change-list.png)

1. Azure Portal에서 Automation 계정을 연 다음 **구성 관리** 아래에서 **변경 내용 추적**을 선택합니다.

2. 변경 내용 목록에서 파일을 선택하고 **파일 콘텐츠 변경 내용 보기**를 선택하여 파일 콘텐츠를 확인합니다. 변경 내용 정보 창에는 표준 이전 및 이후 파일 정보가 표시됩니다.

   ![변경 세부 정보](./media/change-tracking-file-contents/change-details.png)

3. 파일 콘텐츠를 나란히 보기에서 볼 수 있습니다. **인라인**을 선택하여 변경 내용을 인라인 보기에 표시할 수도 있습니다.

## <a name="track-registry-keys"></a>레지스트리 키 추적

다음 단계를 사용하여 Windows 컴퓨터에서 추적할 레지스트리 키를 구성합니다.

1. Automation 계정의 **구성 관리** 아래에서 **변경 내용 추적**을 선택합니다. 
2. **설정 편집**(기어 기호)을 클릭합니다.
3. 작업 영역 구성 페이지에서 **Windows 레지스트리**를 선택합니다.
4. **+ 추가**를 클릭하여 추적할 새 레지스트리 키를 추가합니다.
5. 변경 내용 추적에 대해 Windows 레지스트리 추가 창에서 추적할 키에 대한 정보를 입력한 다음 **저장**을 클릭합니다. 다음 표에는 정보에 사용할 수 있는 속성이 정의되어 있습니다.

    |속성  |Description  |
    |---------|---------|
    |사용     | 설정이 적용되면 True이고, 그렇지 않으면 False입니다.        |
    |Item Name     | 추적할 레지스트리 키의 식별 이름입니다.        |
    |그룹     | 논리적으로 레지스트리 키를 그룹화하는 그룹 이름입니다.        |
    |Windows 레지스트리 키   | 경로가 포함된 키 이름입니다(예: **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders\Common Startup**).      |

## <a name="search-logs-for-change-records"></a>변경 레코드에 대한 로그 검색

변경 레코드에 대한 Azure Monitor 로그에서 다양한 검색을 수행할 수 있습니다. 변경 내용 추적 페이지가 열려 있는 상태에서 **Log Analytics**를 클릭하여 로그 페이지를 엽니다. 다음 표에서는 변경 레코드에 대한 로그 검색 샘플을 제공합니다.

|쿼리  |Description  |
|---------|---------|
|ConfigurationData<br>&#124; where   ConfigDataType == "Microsoft services" and SvcStartupType == "Auto"<br>&#124; where SvcState == "중지됨"<br>&#124; summarize arg_max(TimeGenerated, *) by SoftwareName, Computer         | 자동으로 설정되었지만 중지됨으로 보고된 Microsoft 서비스에 대한 최근의 인벤토리 레코드를 표시합니다. 결과는 지정된 소프트웨어 이름 및 컴퓨터에 대한 최신 레코드로 제한됩니다.    |
|ConfigurationChange<br>&#124; where ConfigChangeType == "소프트웨어" and ChangeCategory == "제거됨"<br>&#124; order by TimeGenerated desc|제거된 소프트웨어에 대한 변경 레코드를 표시합니다.|

## <a name="create-alerts-on-changes"></a>변경에 대한 경고 만들기

다음 예제에서는 머신에서 **C:\windows\system32\drivers\etc\hosts** 파일이 수정되었음을 보여 줍니다. 이 파일은 Windows가 호스트 이름을 IP 주소로 확인하는 데 사용하기 때문에 중요합니다. 이 작업은 DNS보다 우선하므로 연결 문제가 발생할 수 있습니다. 악의적인 또는 위험한 웹 사이트로 트래픽 리디렉션이 발생할 수도 있습니다.

![호스트 파일 변경 내용을 보여 주는 차트](./media/change-tracking-file-contents/changes.png)

이 예제를 사용하여 변경에 대한 경고를 만드는 단계를 설명하겠습니다.

1. Automation 계정의 **구성 관리** 아래에서 **변경 내용 추적**을 선택한 다음 **Log Analytics**를 선택합니다. 
2. 로그 검색에서 `ConfigurationChange | where FieldsChanged contains "FileContentChecksum" and FileSystemPath contains "hosts"` 쿼리를 사용하여 **hosts** 파일의 콘텐츠 변경 내용을 찾습니다. 이 쿼리는 "hosts"라는 단어를 포함하는 정규화된 경로를 갖는 파일에서 콘텐츠 변경을 검색합니다. 경로 부분을 정규화된 형식으로 변경하여(예: `FileSystemPath == "c:\windows\system32\drivers\etc\hosts"`를 사용) 특정 파일을 요청할 수도 있습니다.

3. 쿼리에서 원하는 결과가 반환되면 로그 검색에서 **새 경고 규칙**을 클릭하여 경고 만들기 페이지를 엽니다. Azure Portal에서 **Azure Monitor**를 통해 이 페이지로 이동할 수도 있습니다. 

4. 쿼리를 다시 확인하고 경고 논리를 수정합니다. 여기서는 환경의 모든 컴퓨터에서 변경 내용이 여러 개 검색되면 경고를 트리거할 수 있습니다.

    ![hosts 파일 변경 내용을 추적하기 위한 쿼리의 변경](./media/change-tracking-file-contents/change-query.png)

5. 경고 논리를 설정한 후에는 트리거되는 경고에 대응하여 작업을 수행할 작업 그룹을 할당합니다. 이 경우 이메일을 보내도록 설정하고 ITSM(IT 서비스 관리) 티켓을 만듭니다. 

    ![변경 시 경고하도록 작업 그룹 구성](./media/change-tracking/action-groups.png)

## <a name="next-steps"></a>다음 단계

* Log Analytics 작업 영역에 저장된 로그를 검색해야 하는 경우 [Azure Monitor 로그에서 로그 검색](../log-analytics/log-analytics-log-searches.md)을 참조하세요.
* 기능 오류를 해결하려면 [변경 내용 추적 및 인벤토리 문제 해결](troubleshoot/change-tracking.md)을 참조하세요.