
<properties
	pageTitle="Resource Manager로 배포된 가상 컴퓨터 백업 관리 및 모니터링 | Microsoft Azure"
	description="Resource Manager로 배포된 가상 컴퓨터 백업을 관리하고 모니터링하는 방법을 알아봅니다."
	services="backup"
	documentationCenter=""
	authors="trinadhk"
	manager="shreeshd"
	editor=""/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/03/2016"
	ms.author="jimpark; markgal;"/>

# Azure 가상 컴퓨터 백업 관리 및 모니터링

> [AZURE.SELECTOR]
- [Azure VM 백업 관리](backup-azure-manage-vms.md)
- [클래식 VM 백업 관리](backup-azure-manage-vms-classic.md)

이 문서에서는 가상 컴퓨터 (VM)에 대한 백업을 관리하는 방법에 대한 지침을 제공할 뿐만 아니라 포털 대시보드에서 사용할 수 있는 백업 정보를 설명합니다. 복구 서비스 자격 증명 모음이 있는 VM을 사용하는 경우 이 문서의 지침을 적용합니다. 이 문서에서는 가상 컴퓨터 만들기에 대해 다루거나 가상 컴퓨터를 보호하는 방법을 설명하지 않습니다. 복구 서비스 자격 증명을 사용하여 Azure의 Azure Resource Manager로 배포된 VM을 보호하기 위한 입문서는 [먼저 보기: 복구 서비스 자격 증명 모음에 VM 백업](backup-azure-vms-first-look-arm.md)을 참조하세요.

## 액세스 자격 증명 모음 및 보호된 가상 컴퓨터

Azure 포털에서 복구 서비스 자격 증명 모음 대시보드는 다음을 포함하는 자격 증명 모음에 대한 정보에 액세스를 제공합니다.

- 가장 최근 복원 지점이기도 한 가장 최근 백업 스냅숏 <br>
- 백업 정책 <br>
- 백업 스냅숏의 전체 크기 <br>
- 자격 증명 모음으로 보호되는 가상 컴퓨터의 수 <br>

가상 컴퓨터 백업을 사용하는 다양한 관리 작업은 대시보드에서 자격 증명 모음을 열기 시작합니다. 그러나 여러 항목(또는 여러 가상 컴퓨터)를 보호하기 위해 자격 증명 모음을 사용할 수 있으므로 특정 가상 컴퓨터에 대한 세부 정보를 보려면 자격 증명 모음 항목 대시보드를 열어야 합니다. 다음 절차는 *자격 증명 모음 대시보드*를 열고 *자격 증명 모음 항목 대시보드*를 진행하는 방법을 보여 줍니다. 절차 모두에는 대시보드 명령에 고정을 사용하여 Azure 대시보드에 자격 증명 모음 및 자격 증명 모음 항목을 추가하는 방법을 알려주는 "팁"이 있습니다. 대시보드에 고정은 자격 증명 모음 또는 항목에 바로 가기를 만드는 방법입니다. 바로 가기에서 일반적인 명령을 실행할 수도 있습니다.

>[AZURE.TIP] 여러 대시보드 및 블레이드를 창의 맨 아래에서 진한 파랑 슬라이더를 사용하여 Azure 대시보드에서 보기를 앞뒤로 슬라이드할 수 있습니다.

![슬라이더가 있는 전체 보기](./media/backup-azure-manage-vms/bottom-slider.png)

### 대시보드에서 복구 서비스 자격 증명 모음을 엽니다.

1. [Azure 포털](https://portal.azure.com/)에 로그인합니다.

2. 허브 메뉴에서 **찾아보기**를 클릭하고 리소스 목록에서 **복구 서비스**를 입력합니다. 입력을 시작하면 목록이 입력에 따라 필터링됩니다. **복구 서비스 자격 증명 모음**을 클릭합니다.

    ![복구 서비스 자격 증명 모음 만들기 1단계](./media/backup-azure-manage-vms/browse-to-rs-vaults.png) <br/>

    복구 서비스 자격 증명 모음의 목록이 표시됩니다.

    ![복구 서비스 자격 증명 모음 목록](./media/backup-azure-manage-vms/list-o-vaults.png) <br/>

    >[AZURE.TIP] Azure 대시보드에 자격 증명 모음을 고정하는 경우 Azure 포털을 열 경우 해당 자격 증명 모음에 즉시 액세스할 수 있습니다. 대시보드에 자격 증명 모음을 고정하려면 자격 증명 모음 목록에서 자격 증명 모음을 마우스 오른쪽 단추로 클릭하고 **대시보드에 고정**을 선택합니다.

3. 자격 증명 모음 목록에서 자격 증명 모음을 선택하여 대시보드를 엽니다. 자격 증명 모음을 선택할 경우 자격 증명 모음 대시보드 및 **설정** 블레이드를 엽니다. 다음 이미지에서 **Contoso-vault** 대시보드가 강조 표시됩니다.

    ![자격 증명 모음 대시보드 열기 및 설정 블레이드](./media/backup-azure-manage-vms/full-view-rs-vault.png)

### 자격 증명 모음 항목 대시보드 열기

이전 절차에서 자격 증명 모음 대시보드를 열었습니다. 자격 증명 모음 항목 대시보드를 열려면:

1. 자격 증명 모음 대시보드의 **백업 항목** 타일에서 **Azure 가상 컴퓨터**를 클릭합니다.

    ![백업 항목 열기 타일](./media/backup-azure-manage-vms/contoso-vault.png)

    **백업 항목** 블레이드에서 각 항목에 대한 마지막 백업 작업을 나열합니다. 이 예제에는 자격 증명이 모음에 의해 보호되는 하나의 가상 컴퓨터인 demovm-markgal이 있습니다.

    ![백업 항목 타일](./media/backup-azure-manage-vms/backup-items-blade.png)

    >[AZURE.TIP] 쉽게 액세스하기 위해 자격 증명 모음 항목을 Azure 대시보드에 고정할 수 있습니다. 자격 증명 모음을 고정하려면 자격 증명 모음 항목 목록에서 항목을 마우스 오른쪽 단추로 클릭하고 **대시보드에 고정**을 선택합니다.

2. **백업 항목** 블레이드에서 항목을 클릭하여 자격 증명 모음 항목 대시보드를 엽니다.

    ![백업 항목 타일](./media/backup-azure-manage-vms/backup-items-blade-select-item.png)

    자격 증명 모음 항목 대시보드 및 해당 **설정** 블레이드가 열립니다.

    ![설정 블레이드에서 백업 항목 대시보드](./media/backup-azure-manage-vms/item-dashboard-settings.png)

    자격 증명 모음 항목 대시보드에서 다음과 같은 다양한 키 관리 작업을 완료할 수 있습니다.

    - 정책 변경 또는 새 백업 정책 만들기 <br>
	- 복원 지점 확인 및 해당 일관성 상태 확인 <br>
	- 가상 컴퓨터의 주문형 백업 <br>
	- 가상 컴퓨터 보호 중지 <br>
	- 가상 컴퓨터 보호 재개 <br>
	- 백업 데이터(또는 복구 지점) 삭제 <br>
	- [백업(또는 복구 지점) 복원](./backup-azure-arm-restore-vms.md#restore-a-recovery-point) <br>

다음 절차의 경우 시작점은 자격 증명 모음 항목 대시보드입니다.

## 정책 변경 또는 새 백업 정책 만들기

1. [자격 증명 모음 항목 대시보드](backup-azure-manage-vms.md#open-a-vault-item-dashboard)에서 **모든 설정**을 클릭하여 **설정** 블레이드를 엽니다.

    ![백업 정책 블레이드](./media/backup-azure-manage-vms/all-settings-button.png)

2. **설정** 블레이드에서 **백업 정책**을 클릭하여 해당 블레이드를 엽니다.

    블레이드에서 백업 빈도 및 보존 범위 세부 정보가 표시됩니다.

    ![백업 정책 블레이드](./media/backup-azure-manage-vms/backup-policy-blade.png)

3. **백업 정책 선택** 메뉴에서:
    - 정책을 변경하려면 다른 정책을 선택하고 **저장**을 클릭합니다. 자격 증명 모음에 새 정책이 즉시 적용됩니다. <br>
    - 새 정책을 만들려는 경우 **새로 만들기**를 선택합니다.

    ![가상 컴퓨터 백업](./media/backup-azure-manage-vms/backup-policy-create-new.png)

    백업 정책을 만드는 지침은 [백업 정책 정의](backup-azure-manage-vms.md#defining-a-backup-policy)를 참조하세요.


## 가상 컴퓨터의 주문형 백업
보호를 위해 구성한 후에는 가상 컴퓨터의 주문형 백업을 수행할 수 있습니다. 가상 컴퓨터에 대한 초기 백업이 보류 중인 경우 주문형 백업은 복구 서비스 자격 증명 모음에 가상 컴퓨터의 전체 복사본을 만듭니다. 초기 백업이 완료되면 주문형 백업은 이전 스냅숏에서 복구 서비스 자격 증명 모음에 변경만을 보내게 됩니다. 즉, 항상 증분입니다.

>[AZURE.NOTE] 주문형 백업에 대한 보존 범위는 정책에서 매일 백업 지점에 대해 지정된 보존 값입니다. 매일 백업 시점을 선택하지 않은 경우 주간 백업 지점을 사용합니다.

가상 컴퓨터의 주문형 백업을 트리거하려면:

1. [자격 증명 모음 항목 대시보드](backup-azure-manage-vms.md#open-a-vault-item-dashboard)에서 **지금 백업**을 클릭합니다.

    ![지금 백업 아이콘](./media/backup-azure-manage-vms/backup-now-button.png)

    포털에서는 주문형 백업 작업을 시작하려는지 확인합니다. **예**를 클릭하여 백업 작업을 시작합니다.

    ![지금 백업 아이콘](./media/backup-azure-manage-vms/backup-now-check.png)

    백업 작업은 새 복구 지점을 만듭니다. 만든 복구 지점의 보존 범위는 가상 컴퓨터와 연결된 정책에 지정된 범위와 같습니다. 작업에 대한 진행률을 추적하려면 자격 증명 모음 대시보드에서 **백업 작업** 타일을 클릭합니다.


## 가상 컴퓨터 보호 중지
가상 컴퓨터에 대한 보호를 중지하려는 경우 복구 지점을 보존할지를 묻는 메시지가 표시됩니다. 두 가지 방법으로 가상 컴퓨터에 대한 보호를 중지할 수 있습니다. 이후의 모든 백업 작업을 중지하고 모든 복구 지점을 삭제하거나 이후의 모든 백업 작업을 중지하지만 복구 지점을 유지합니다. 저장소에서 복구 지점을 유지하는 데 관련된 비용이 듭니다. 그러나 복구 지점을 유지하는 장점은 나중에 원하는 경우 가상 컴퓨터를 복원할 수 있다는 것입니다. 이러한 가상 컴퓨터의 가격 정보를 보려면 [여기](https://azure.microsoft.com/pricing/details/backup/)를 클릭하세요. 모든 복구 지점을 삭제하려는 경우 가상 컴퓨터를 복원하는 옵션이 없어집니다.

가상 컴퓨터에 대한 보호를 중지하려면:

1. [자격 증명 모음 항목 대시보드](backup-azure-manage-vms.md#open-a-vault-item-dashboard)에서 **백업 중지**를 클릭합니다.

    ![백업 중지 단추](./media/backup-azure-manage-vms/stop-backup-button.png)

    백업 중지 블레이드가 열립니다.

    ![백업 중지 블레이드](./media/backup-azure-manage-vms/stop-backup-blade.png)

2. **백업 중지** 블레이드에서 백업 데이터를 유지할지 혹은 삭제할지 여부를 선택합니다. 정보 상자에서는 선택한 항목에 대한 세부 정보를 제공합니다.

    ![보호 중지](./media/backup-azure-manage-vms/retain-or-delete-option.png)

3. 백업 데이터를 유지하기로 선택한 경우 4단계로 건너뜁니다. 백업 데이터 삭제를 삭제하기로 선택하고 백업 작업을 중지하고 복구 지점을 삭제하려는 경우 항목의 이름을 입력합니다.

    ![백업 확인](./media/backup-azure-manage-vms/item-verification-box.png)

    항목 이름을 모르는 경우 이름을 보려면 느낌표 위로 마우스를 가져갑니다. 또한 항목의 이름은 블레이드 맨 위에 있는 **백업 중지**에 있습니다.

4. 필요에 따라 **이유** 또는 **주석**을 제공합니다.

5. 현재 항목에 대한 백업 작업을 중지하려면 ![백업 중지 단추](./media/backup-azure-manage-vms/stop-backup-button-blue.png)을 클릭합니다.

    알림 메시지를 통해 백업 작업이 중지되었음을 알 수 있습니다.

    ![보호 중지 확인](./media/backup-azure-manage-vms/stop-message.png)


## 가상 컴퓨터 보호 재개
가상 컴퓨터에 대한 보호가 중지되었을 때 **백업 데이터 보관** 옵션을 선택한 경우 보호를 다시 시작할 수 있습니다. **백업 데이터 삭제** 옵션을 선택한 경우 가상 컴퓨터에 대한 보호를 다시 시작할 수 없습니다.

가상 컴퓨터에 대한 보호를 다시 시작하려면

1. [자격 증명 모음 항목 대시보드](backup-azure-manage-vms.md#open-a-vault-item-dashboard)에서 **백업 다시 시작**을 클릭합니다.

    ![보호 다시 시작](./media/backup-azure-manage-vms/resume-backup-button.png)

    백업 정책 블레이드가 열립니다.

    >[AZURE.NOTE] 가상 컴퓨터를 다시 보호하는 경우 가상 컴퓨터가 처음에 보호된 정책과 다른 정책을 선택할 수 있습니다.

2. [정책 변경 또는 새 백업 정책 만들기](backup-azure-manage-vms.md#change-policies-or-create-a-new-backup-policy)의 단계에 따라 가상 컴퓨터에 대한 정책을 할당합니다.

    백업 정책이 가상 컴퓨터에 적용되면 다음과 같은 메시지가 표시됩니다.

    ![성공적으로 보호된 VM](./media/backup-azure-manage-vms/success-message.png)

## 백업 데이터 삭제
**중지 백업** 작업 중에 또는 백업이 중지되면 언제든지 가상 컴퓨터와 연결된 백업 데이터를 삭제할 수 있습니다. 가상 컴퓨터에 대한 백업 작업을 중지하고 복구 지점을 삭제할 것인지를 결정하기 전에 며칠 또는 몇 주 동안 대기하도록 허용하는 것이 좋다면 가능합니다. 백업 데이터를 삭제하는 경우 복구 지점을 복원하는 작업과 달리 특정 복구 지점을 삭제하도록 선택할 수 없습니다. 삭제하려는 경우 항목에 연결된 모든 복구 지점을 삭제합니다.

다음 절차에서는 가상 컴퓨터에 대한 백업 작업이 중지 또는 비활성화되었다고 가정합니다. 백업 작업을 사용하지 않는 경우에만 **백업 다시 시작** 및 **삭제 백업** 옵션을 자격 증명 모음 항목 대시보드에서 사용할 수 있습니다.

![다시 시작 및 삭제 단추](./media/backup-azure-manage-vms/resume-delete-buttons.png)

*백업을 사용하지 않는* 가상 컴퓨터의 백업 데이터를 삭제하려면:

1. [자격 증명 모음 항목 대시보드](backup-azure-manage-vms.md#open-a-vault-item-dashboard)에서 **백업 삭제**를 클릭합니다.

    ![VM 유형](./media/backup-azure-manage-vms/delete-backup-buttom.png)

    **백업 데이터 삭제** 블레이드가 열립니다.

    ![VM 유형](./media/backup-azure-manage-vms/delete-backup-blade.png)

2. 항목의 이름을 입력하여 복구 지점을 삭제한다고 확인해야 합니다.

    ![백업 확인](./media/backup-azure-manage-vms/item-verification-box.png)

    항목 이름을 모르는 경우 이름을 보려면 느낌표 위로 마우스를 가져갑니다. 또한 항목의 이름은 블레이드 맨 위에 있는 **백업 데이터 삭제**에 있습니다.

3. 필요에 따라 **이유** 또는 **주석**을 제공합니다.

4. 현재 항목에 대한 백업 데이터를 삭제하려면 ![백업 중지 단추](./media/backup-azure-manage-vms/delete-button.png)을 클릭합니다.

    알림 메시지를 통해 백업 데이터가 삭제되었음을 알 수 있습니다.

## 작업 감사
작업 및 이벤트 로그를 검토하여 복구 서비스 자격 증명 모음에서 수행되는 관리 작업을 확인할 수 있습니다. 작업 로그를 사용하면 백업 작업에 대한 post-mortem 및 감사 지원이 가능합니다. 감사 로그 기능을 사용하여 *구독에서* 모든 작업에 대한 로그를 볼 수 있습니다. 이벤트, 작업 및 감사 로그에 대한 자세한 내용은 [이벤트 및 감사 로그 보기](../azure-portal/insights-debugging-with-events.md)를 참조하세요. **감사 로그** 설정을 사용하여 특정 복구 서비스 자격 증명 모음 또는 자격 증명 모음 항목에 대한 이벤트 및 작업 로그를 봅니다.

다음 작업은 감사 로그에 기록됩니다.

- 등록
- 등록 취소
- 보호 구성
- 백업(예약된 백업 및 주문형 백업 모두)
- 복원
- 보호 중지
- 백업 데이터 삭제
- 정책 추가
- 정책 삭제
- 정책 업데이트
- 작업 취소

복구 서비스 자격 증명 모음에 대한 이벤트 로그를 보려면:

1. [자격 증명 모음 대시보드](backup-azure-manage-vms.md#open-a-recovery-services-vault-in-the-dashboard)에서 **감사 로그**를 찾아 클릭하여 **이벤트** 블레이드를 엽니다.

    ![감사 로그](./media/backup-azure-manage-vms/audit-logs.png)

    이벤트 블레이드는 현재 자격 증명 모음에 대해서만 필터링되는 작동 이벤트에 열립니다. 블레이드에서는 지난 주에 발생한 중요, 오류, 경고 및 정보 이벤트의 목록을 보여 줍니다. 시간 범위는 **필터** 설정에 설정된 기본값입니다. **이벤트** 블레이드에서는 이벤트가 발생했을 때 추적하는 가로 막대형 차트도 보여 줍니다. 가로 막대형 차트를 표시하지 않으려는 경우 **이벤트** 메뉴에서 **차트 표시**를 클릭하여 차트를 해제합니다.

    ![감사 로그 필터](./media/backup-azure-manage-vms/audit-logs-filter.png)

2. 특정 작업에 대한 자세한 내용을 보려면 작업의 목록에서 해당 작업을 클릭하여 해당 블레이드를 엽니다. 블레이드에는 작업 및 시간 범위에서 발생하는 이벤트의 목록에 대한 자세한 정보가 포함됩니다.

    ![작업 세부 정보](./media/backup-azure-manage-vms/audit-logs-details-window.png)

3. 이벤트의 목록에서 특정 이벤트에 대한 자세한 정보를 보려면 작업을 클릭하여 해당 세부 정보 블레이드를 엽니다.

    ![이벤트 세부 정보](./media/backup-azure-manage-vms/audit-logs-details-window-deep.png)

    이벤트 수준 정보는 정보에서 가져오는 만큼 자세합니다. 이 절차의 나머지 부분에서는 사용 가능한 정보를 편집하거나 변경하는 방법을 설명합니다.

4. 사용 가능한 필터 목록을 편집하려면 **이벤트** 메뉴에서 **필터**를 클릭하여 해당 블레이드를 엽니다.

    ![필터 블레이드 열기](./media/backup-azure-manage-vms/audi-logs-filter-button.png)

5. **필터** 블레이드에서 **수준**, **시간 범위** 및 **호출자** 필터를 조정합니다. 복구 서비스 자격 증명 모음에 대한 현재 정보를 제공하도록 설정되었기 때문에 다른 필터를 사용할 수 없습니다.

    ![감사 로그 쿼리 세부 정보](./media/backup-azure-manage-vms/filter-blade.png)

    이벤트의 **수준**을 중요, 오류, 경고 또는 정보로 지정할 수 있습니다. 이벤트 수준의 조합을 선택할 수 있지만 하나 이상의 수준을 선택해야 합니다. 수준을 설정하거나 해제합니다. **시간 범위** 필터를 사용하면 이벤트 캡처에 대한 시간의 길이를 지정할 수 있습니다. 사용자 지정 시간 범위를 사용하는 경우 시작 및 종료 시간을 설정할 수 있습니다.

6. 필터를 사용하여 작업 로그를 쿼리할 준비가 되면 **업데이트**를 클릭합니다. **이벤트** 블레이드에 결과가 표시됩니다.

    ![작업 세부 정보](./media/backup-azure-manage-vms/edited-list-of-events.png)


## 경고 알림
포털에서 작업에 대한 사용자 지정 경고 알림을 받을 수 있습니다. 다음 작업을 가져오려면 작업 로그 이벤트에서 PowerShell 기반 경고 규칙을 정의합니다. *Azure PowerShell 버전 1.3.0 이상*을 사용합니다.

사용자 지정 알림을 정의하여 백업 실패에 대해 경고하려면 다음과 같은 명령을 사용합니다.

```
PS C:\> $actionEmail = New-AzureRmAlertRuleEmail -CustomEmail contoso@microsoft.com
PS C:\> Add-AzureRmLogAlertRule -Name backupFailedAlert -Location "East US" -ResourceGroup RecoveryServices-DP2RCXUGWS3MLJF4LKPI3A3OMJ2DI4SRJK6HIJH22HFIHZVVELRQ-East-US -OperationName Microsoft.Backup/RecoveryServicesVault/Backup -Status Failed -TargetResourceId /subscriptions/86eeac34-eth9a-4de3-84db-7a27d121967e/resourceGroups/RecoveryServices-DP2RCXUGWS3MLJF4LKPI3A3OMJ2DI4SRJK6HIJH22HFIHZVVELRQ-East-US/providers/microsoft.backupbvtd2/RecoveryServicesVault/trinadhVault -Actions $actionEmail
```

**ResourceId** : 감사 로그에서 이 정보를 얻을 수 있습니다. ResourceId는 작업 로그의 리소스 열에 제공됩니다.

**OperationName** : "Microsoft.RecoveryServices/recoveryServicesVault/<EventName>" 형식이며, 여기서 EventName은 Register, Unregister, ConfigureProtection, Backup, Restore, StopProtection, DeleteBackupData, CreateProtectionPolicy, DeleteProtectionPolicy, UpdateProtectionPolicy 중 하나입니다.

**상태** : 지원되는 값은 시작함, 성공함 및 실패함입니다.

**ResourceGroup** : 리소스가 속한 리소스 그룹입니다. 생성된 로그에 리소스 그룹 열을 추가할 수 있습니다. 리소스 그룹은 사용할 수 있는 이벤트 정보의 종류 중 하나입니다.

**이름** : 경고 규칙의 이름입니다.

**CustomEmail** : 경고 알림을 보낼 사용자 지정 메일 주소를 지정합니다.

**SendToServiceOwners** : 이 옵션은 구독의 모든 관리자 및 공동 관리자에게 경고 알림을 보냅니다. **New-AzureRmAlertRuleEmail** cmdlet에 사용될 수 있습니다.

### 경고에 대한 제한
이벤트 기반 경고는 다음과 같은 제한 사항에 종속됩니다.

1. 복구 서비스 자격 증명 모음의 모든 가상 컴퓨터에서 경고를 유발합니다. 복구 서비스 자격 증명 모음에서 가상 컴퓨터의 특정 집합에 대한 경고를 사용자 지정할 수 없습니다.
2. 이 기능은 미리 보기 상태입니다. [자세히 알아보기](../azure-portal/insights-powershell-samples.md#create-alert-rules)
3. "alerts-noreply@mail.windowsazure.com"으로부터 경고가 전송됩니다. 현재는 전자 메일 보낸 사람을 수정할 수 없습니다.

[AZURE.INCLUDE [backup-create-backup-policy-for-vm](../../includes/backup-create-backup-policy-for-vm.md)]

## 다음 단계

복구 지점에서 가상 컴퓨터를 다시 만드는 방법에 대한 내용은 [Azure VM 복원](backup-azure-restore-vms.md)을 확인합니다. 가상 컴퓨터를 보호하는 방법에 대한 정보가 필요한 경우 [먼저 보기: 복구 서비스 자격 증명 모음에 VM 백업](backup-azure-vms-first-look-arm.md)을 참조하세요.

<!---HONumber=AcomDC_0608_2016-->