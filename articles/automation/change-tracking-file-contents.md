---
title: Azure Automation에서 변경 내용 추적 및 인벤토리 관리
description: 이 문서에서는 변경 내용 추적 및 인벤토리를 사용 하 여 사용자 환경에서 발생 하는 소프트웨어 및 Microsoft 서비스 변경 내용을 추적 하는 방법을 설명 합니다.
services: automation
ms.subservice: change-inventory-management
ms.date: 07/03/2018
ms.topic: conceptual
ms.openlocfilehash: 8ca1bd7a724d3256bc2e171ce39fd6a06e2e5935
ms.sourcegitcommit: 31236e3de7f1933be246d1bfeb9a517644eacd61
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82779300"
---
# <a name="manage-change-tracking-and-inventory"></a>변경 내용 추적 및 인벤토리 관리

추적할 새 파일 또는 레지스트리 키를 추가 하면 Azure Automation [변경 내용 추적 및 인벤토리](change-tracking.md) 기능을 사용 하도록 설정할 수 있습니다. 이 문서에는이 기능을 사용 하는 절차가 포함 되어 있습니다.

## <a name="enable-the-full-change-tracking-and-inventory-feature"></a>전체 변경 내용 추적 및 인벤토리 기능 사용

[AZURE SECURITY CENTER FIM (파일 무결성 모니터링)](https://docs.microsoft.com/azure/security-center/security-center-file-integrity-monitoring)을 사용 하도록 설정한 경우 아래에 설명 된 대로 전체 변경 내용 추적 및 인벤토리 기능을 사용할 수 있습니다. 이 프로세스에서는 설정이 제거 되지 않습니다.

> [!NOTE]
> 전체 변경 내용 추적 및 인벤토리 기능을 사용 하도록 설정 하면 추가 요금이 발생할 수 있습니다. [Automation 가격 책정](https://azure.microsoft.com/pricing/details/automation/)을 참조 하세요.

1. 작업 영역으로 이동 하 여 [설치 된 모니터링 솔루션 목록](../azure-monitor/insights/solutions.md#list-installed-monitoring-solutions)에서 해당 작업 영역을 찾아 모니터링 솔루션을 제거 합니다.
2. 솔루션 이름을 클릭 하 여 요약 페이지를 열고 [모니터링 솔루션 제거](../azure-monitor/insights/solutions.md#remove-a-monitoring-solution)에 설명 된 대로 **삭제**를 클릭 합니다.
3. 변경 내용 추적 및 인벤토리를 다시 사용 하도록 설정 하려면 Automation 계정으로 이동 하 고 **구성 관리**아래에서 **변경 내용 추적** 을 선택 합니다.
4. Log Analytics 작업 영역 및 Automation 계정을 선택 하 고 작업 영역 설정을 확인 한 다음 **사용**을 클릭 합니다.

## <a name="onboard-machines-to-change-tracking-and-inventory"></a><a name="onboard"></a>변경 내용 추적 및 인벤토리에 컴퓨터 등록

변경 내용 추적을 시작 하려면 Azure Automation에서 변경 내용 추적 및 인벤토리를 사용 하도록 설정 해야 합니다. 다음은이 기능에 컴퓨터를 등록 하는 데 권장 되는 방법입니다. 

* [가상 머신에서 온보드](automation-onboard-solutions-from-vm.md)
* [여러 컴퓨터 검색에서 온보드](automation-onboard-solutions-from-browse.md)
* [Automation 계정에서 온보드](automation-onboard-solutions-from-automation-account.md)
* [Azure Automation runbook에서 온보드](automation-onboard-solutions.md)

## <a name="track-files"></a>파일 추적

### <a name="configure-file-tracking-on-windows"></a>Windows에서 파일 추적 구성

다음 단계를 사용 하 여 Windows 컴퓨터에서 파일 추적을 구성 합니다.

1. Automation 계정의 **구성 관리**아래에서 **변경 내용 추적** 을 선택 합니다. 
2. **설정 편집**(기어 기호)을 클릭합니다.
3. 작업 영역 구성 페이지에서 **Windows 파일**을 선택 하 고 **+ 추가** 를 클릭 하 여 추적할 새 파일을 추가 합니다.
4. 변경 내용 추적에 대 한 Windows 파일 추가 창에서 추적할 파일의 정보를 입력 하 고 **저장**을 클릭 합니다. 다음 표에서는 정보에 사용할 수 있는 속성을 정의 합니다.

    |속성  |Description  |
    |---------|---------|
    |사용     | 설정이 적용 되 면 True이 고, 그렇지 않으면 False입니다.        |
    |Item Name     | 추적할 파일의 이름입니다.        |
    |그룹     | 논리적으로 파일을 그룹화하는 그룹 이름입니다.        |
    |경로 입력     | 파일을 확인할 경로입니다 (예: **c:\temp\\\*.txt**). 와 `%winDir%\System32\\\*.*`같은 환경 변수를 사용할 수도 있습니다.       |
    |경로 유형     | 경로의 유형입니다. 가능한 값은 File and Directory입니다.        |    
    |재귀     | 추적할 항목을 찾을 때 재귀가 사용 되 면 True이 고, 그렇지 않으면 False입니다.        |    
    |파일 콘텐츠 업로드 | 추적 된 변경 내용에 대 한 파일 콘텐츠를 업로드 하려면 True이 고, 그렇지 않으면 False입니다.|

5. **파일 콘텐츠 업로드**에 대해 True를 지정 해야 합니다. 이 설정은 표시 된 파일 경로에 대 한 파일 콘텐츠 추적을 사용 하도록 설정 합니다.

### <a name="configure-file-tracking-on-linux"></a>Linux에서 파일 추적 구성

다음 단계를 사용하여 Linux 컴퓨터에서 추적할 파일을 구성합니다.

1. Automation 계정의 **구성 관리**아래에서 **변경 내용 추적** 을 선택 합니다. 
2. **설정 편집**(기어 기호)을 클릭합니다.
3. 작업 영역 구성 페이지에서 **Linux 파일**을 선택 하 고 **+ 추가** 를 클릭 하 여 추적할 새 파일을 추가 합니다.
4. 변경 내용 추적에 대 한 Linux 파일 추가 창에서 추적할 파일이 나 디렉터리에 대 한 정보를 입력 하 고 **저장**을 클릭 합니다. 다음 표에서는 정보에 사용할 수 있는 속성을 정의 합니다.

    |속성  |Description  |
    |---------|---------|
    |사용     | 설정이 적용 되 면 True이 고, 그렇지 않으면 False입니다.        |
    |Item Name     | 추적할 파일의 이름입니다.        |
    |그룹     | 논리적으로 파일을 그룹화하는 그룹 이름입니다.        |
    |경로 입력     | 파일을 확인할 경로 (예: **/etc/***.ca)입니다.       |
    |경로 유형     | 경로의 유형입니다. 가능한 값은 File and Directory입니다.        |
    |재귀     | 추적할 항목을 찾을 때 재귀가 사용 되 면 True이 고, 그렇지 않으면 False입니다.        |
    |sudo 사용     | 항목을 확인할 때 sudo를 사용 하려면 True이 고, 그렇지 않으면 False입니다.         |
    |링크     | 디렉터리를 트래버스할 때 기호화 된 링크를 처리 하는 방법을 결정 하는 설정입니다. 가능한 값은 다음과 같습니다.<br> 무시 - 바로 가기 링크가 무시되고 참조된 파일/디렉터리가 포함되지 않습니다.<br>Follow-재귀 중에 기호화 된 링크를 따르고 참조 된 파일/디렉터리도 포함 합니다.<br>관리 - 기호화된 링크를 따르고 반환된 콘텐츠를 변경할 수 있도록 허용합니다. **참고** -파일 콘텐츠 검색을 지원 하지 않으므로이 옵션을 선택 하지 않는 것이 좋습니다.    |
    |파일 콘텐츠 업로드 | 추적 된 변경 내용에 대 한 파일 콘텐츠를 업로드 하려면 True이 고, 그렇지 않으면 False입니다. |

5. **파일 콘텐츠 업로드**에 대해 True를 지정 해야 합니다. 이 설정은 표시 된 파일 경로에 대 한 파일 콘텐츠 추적을 사용 하도록 설정 합니다.

   ![Linux 파일 추가](./media/change-tracking-file-contents/add-linux-file.png)

## <a name="track-file-contents"></a>파일 내용 추적

파일 콘텐츠 추적을 사용 하면 추적 된 변경 전후에 파일의 내용을 볼 수 있습니다. 이 기능은 각 변경이 발생 한 후 저장소 계정에 파일 내용을 저장 합니다. 다음은 파일 내용을 추적 하기 위해 따라야 하는 몇 가지 규칙입니다.

* 파일 콘텐츠를 저장하려면 Resource Manager 배포 모델을 사용하는 표준 스토리지 계정이 필요합니다. 

* Premium 및 클래식 배포 모델 저장소 계정을 사용 하지 마세요. [Azure Storage 계정 정보](../storage/common/storage-create-storage-account.md)를 참조 하세요.

* 사용 하는 저장소 계정은 하나의 Automation 계정에만 연결할 수 있습니다.

* Automation 계정에서 [변경 내용 추적 및 인벤토리](change-tracking.md) 를 사용 하도록 설정 합니다.

### <a name="enable-tracking-for-file-content-changes"></a>파일 콘텐츠 변경 내용 추적 사용

1. Azure Portal에서 Automation 계정을 열고 **구성 관리**아래에서 **변경 내용 추적** 을 선택 합니다.
2. **설정 편집**(기어 기호)을 클릭합니다.
3. **파일 콘텐츠**를 선택하고 **연결**을 클릭합니다. 이렇게 선택 하면 변경 내용 추적에 대 한 콘텐츠 위치 추가 창이 열립니다.

   ![콘텐츠 위치 사용](./media/change-tracking-file-contents/enable.png)

4. 파일 콘텐츠를 저장 하는 데 사용할 구독 및 저장소 계정을 선택 합니다. 

5. 기존의 모든 추적된 파일에 대해 파일 콘텐츠 추적을 사용하려면 **모든 설정에 대한 파일 콘텐츠 업로드**에 대해 **켜기**를 선택합니다. 나중에 각 파일 경로에 대해이 설정을 변경할 수 있습니다.

   ![저장소 계정 설정](./media/change-tracking-file-contents/storage-account.png)

6. 변경 내용 추적 및 인벤토리는 파일 콘텐츠 변경 내용 추적을 사용 하는 경우 저장소 계정 및 SAS (공유 액세스 서명) Uri를 표시 합니다. 서명은 365 일 후에 만료 되며 **다시 생성**을 클릭 하 여 다시 만들 수 있습니다.

   ![계정 키 나열](./media/change-tracking-file-contents/account-keys.png)

### <a name="view-the-contents-of-a-tracked-file"></a>추적 된 파일의 내용 보기

변경 내용 추적와 인벤토리가 추적 된 파일에 대 한 변경 내용을 감지 하면 변경 내용 창에서 파일 내용을 볼 수 있습니다.  

![변경 내용 나열](./media/change-tracking-file-contents/change-list.png)

1. Azure Portal에서 Automation 계정을 열고 **구성 관리**아래에서 **변경 내용 추적** 을 선택 합니다.

2. 변경 내용 목록에서 파일을 선택 하 고 파일 내용 **보기 변경** 을 선택 하 여 파일 내용을 확인 합니다. 변경 세부 정보 창에는 이전 및 이후 파일 정보에 대 한 표준 정보가 표시 됩니다.

   ![변경 세부 정보](./media/change-tracking-file-contents/change-details.png)

3. 나란히 보기에서 파일 콘텐츠를 볼 수 있습니다. **인라인으로** 를 선택 하 여 변경 내용의 인라인 뷰를 볼 수 있습니다.

## <a name="track-registry-keys"></a>레지스트리 키 추적

다음 단계를 사용하여 Windows 컴퓨터에서 추적할 레지스트리 키를 구성합니다.

1. Automation 계정의 **구성 관리**아래에서 **변경 내용 추적** 을 선택 합니다. 
2. **설정 편집**(기어 기호)을 클릭합니다.
3. 작업 영역 구성 페이지에서 **Windows 레지스트리**를 선택 합니다.
4. **+ 추가** 를 클릭 하 여 추적할 새 레지스트리 키를 추가 합니다.
5. 변경 내용 추적에 대 한 Windows 레지스트리 추가 창에서 추적할 키에 대 한 정보를 입력 한 다음 **저장**을 클릭 합니다. 다음 표에서는 정보에 사용할 수 있는 속성을 정의 합니다.

    |속성  |Description  |
    |---------|---------|
    |사용     | 설정이 적용 되 면 True이 고, 그렇지 않으면 False입니다.        |
    |Item Name     | 추적할 레지스트리 키의 이름입니다.        |
    |그룹     | 레지스트리 키를 논리적으로 그룹화 하는 그룹 이름입니다.        |
    |Windows 레지스트리 키   | 경로를 사용 하는 키 이름입니다 (예: **HKEY_LOCAL_MACHINE \Software\microsoft\windows\currentversion\explorer\user Shell Folderss\common Startup**).      |

## <a name="search-logs-for-change-records"></a>변경 레코드에 대 한 로그 검색

변경 레코드에 대 한 Azure Monitor 로그에 대해 다양 한 검색을 수행할 수 있습니다. 변경 내용 추적 페이지가 열려 있는 상태에서 **Log Analytics** 를 클릭 하 여 로그 페이지를 엽니다. 다음 표에서는 변경 레코드에 대 한 샘플 로그 검색을 제공 합니다.

|쿼리  |설명  |
|---------|---------|
|ConfigurationData<br>&#124; 여기서 ConfigDataType = = "Microsoft services" 및 SvcStartupType = = "Auto"<br>&#124; where SvcState == "중지됨"<br>&#124; summarize arg_max(TimeGenerated, *) by SoftwareName, Computer         | 자동으로 설정 되었지만 중지 된 것으로 보고 된 Microsoft 서비스에 대 한 최신 인벤토리 레코드를 표시 합니다. 결과는 지정 된 소프트웨어 이름 및 컴퓨터에 대 한 최신 레코드로 제한 됩니다.    |
|ConfigurationChange<br>&#124; where ConfigChangeType == "소프트웨어" and ChangeCategory == "제거됨"<br>&#124; order by TimeGenerated desc|제거 되는 소프트웨어에 대 한 변경 레코드를 표시 합니다.|

## <a name="create-alerts-on-changes"></a>변경에 대 한 경고 만들기

다음 예제에서는 **C:\windows\system32\drivers\etc\hosts** 파일이 컴퓨터에서 수정 된 것을 보여 줍니다. 이 파일은 Windows에서 호스트 이름을 IP 주소로 확인 하는 데 사용 하기 때문에 중요 합니다. 이 작업은 DNS 보다 우선 하므로 연결 문제가 발생할 수 있습니다. 악의적인 웹 사이트 또는 위험한 웹 사이트에 대 한 트래픽 리디렉션이 발생할 수도 있습니다.

![호스트 파일 변경 내용을 보여 주는 차트](./media/change-tracking-file-contents/changes.png)

이 예를 사용 하 여 변경에 대 한 경고를 만드는 단계를 설명 해 보겠습니다.

1. Automation 계정의 **구성 관리**아래에서 **변경 내용 추적** 을 선택 하 고 **Log Analytics**를 선택 합니다. 
2. 로그 검색에서 쿼리 `ConfigurationChange | where FieldsChanged contains "FileContentChecksum" and FileSystemPath contains "hosts"`를 사용 하 여 **hosts** 파일의 콘텐츠 변경 내용을 찾습니다. 이 쿼리는 "hosts" 라는 단어를 포함 하는 정규화 된 경로가 있는 파일에 대 한 내용 변경을 검색 합니다. 를 사용 하 `FileSystemPath == "c:\windows\system32\drivers\etc\hosts"`는 등의 방법으로 경로 부분을 정규화 된 형식으로 변경 하 여 특정 파일에 대해 요청할 수도 있습니다.

3. 쿼리가 원하는 결과를 반환한 후 로그 검색에서 **새 경고 규칙** 을 클릭 하 여 경고 만들기 페이지를 엽니다. Azure Portal에서 **Azure Monitor** 를 통해이 페이지로 이동할 수도 있습니다. 

4. 쿼리를 다시 확인 하 고 경고 논리를 수정 하십시오. 여기서는 환경의 모든 컴퓨터에서 변경 내용이 여러 개 검색되면 경고를 트리거할 수 있습니다.

    ![호스트 파일에 대 한 변경 내용을 추적 하기 위한 쿼리로 변경](./media/change-tracking-file-contents/change-query.png)

5. 경고 논리가 설정 된 후 트리거되는 경고에 대 한 응답으로 작업을 수행 하는 작업 그룹을 할당 합니다. 이 경우 전자 메일을 보내도록 설정 하 고 ITSM (IT Service Management) 티켓을 만듭니다. 

    ![변경 시 경고 하도록 작업 그룹 구성](./media/change-tracking/action-groups.png)

## <a name="next-steps"></a>다음 단계

* 변경 내용 추적 및 인벤토리의 기본 정보는 [변경 내용 추적 및 인벤토리 개요](change-tracking.md)를 참조 하세요.
* Azure VM에 대 한 변경 문제를 해결 하려면 [변경 내용 추적 및 인벤토리 문제 해결](troubleshoot/change-tracking.md)을 참조 하세요.
* [Azure Monitor 로그의 로그 검색](../log-analytics/log-analytics-log-searches.md) 을 사용 하 여 자세한 변경 내용 추적 데이터를 볼 수 있습니다.