<properties
	pageTitle="Azure 가상 컴퓨터 백업 - 백업"
	description="등록 후 Azure 가상 컴퓨터를 백업하는 방법 배우기"
	services="backup"
	documentationCenter=""
	authors="aashishr"
	manager="shreeshd"
	editor=""/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/30/2015"
	ms.author="aashishr"/>


# Azure 가상 컴퓨터 백업

이 문서는 Azure 가상 컴퓨터 백업에 대한 필수 가이드입니다. 진행하기 전에 모든 [필수 조건][prereq]이 충족되었는지 확인해야 합니다.

Azure 가상 컴퓨터 백업에는 3가지 주요 단계가 포함됩니다.

![Azure 가상 컴퓨터를 백업하는 세 단계][three-steps]

## Azure 가상 컴퓨터 검색
검색 프로세스는 클라우드 서비스 이름 및 지역과 같은 추가 정보와 함께 구독에서 가상 컴퓨터 목록에 대해 Azure에 쿼리합니다.

> [AZURE.NOTE]검색 프로세스는 항상 첫 번째 단계로 실행해야 합니다. 이는 구독에 추가된 새 가상 컴퓨터와 식별하기 위한 것입니다.

검색 프로세스를 트리거하려면 다음 단계를 수행합니다.

1. Azure 포털의 **복구 서비스**에서 찾을 수 있는 백업 저장소로 이동하여 **등록된 항목** 탭을 클릭합니다.

2. 드롭다운 메뉴의 작업 유형을 **Azure 가상 컴퓨터**로 선택하고 **선택** 단추를 클릭합니다.
  	![select workload][select-workload]

3. 페이지 아래쪽에서 **검색** 단추를 클릭합니다.
  	![discover button][discover-button]

4. 검색 프로세스는 가상 컴퓨터를 표로 정리하는 동안 몇 분 동안 실행할 수 있습니다. 검색 프로세스가 실행되는 동안 화면 아래쪽에 알림 메시지가 표시됩니다.
  	![discover vms][discover-vms]

5. 검색 프로세스가 완료되면 알림 메시지가 표시됩니다.
  	![discover-done][discover-done]

<br><br>
## Azure 가상 컴퓨터 등록
가상 컴퓨터를 보호하려면 Azure 백업 서비스에 등록해야 합니다. 등록 프로세스에는 두 가지 기본 목표가 있습니다.

1. 가상 컴퓨터의 VM 에이전트에 백업 확장 연결

2. Azure 백업 서비스와 가상 컴퓨터 연결

등록은 일반적으로 일회성 작업입니다. Azure 백업 서비스가 매끄럽게 업그레이드를 처리하고 다루기 힘든 사용자 간섭 없이 백업 확장을 패치합니다. 이는 일반적으로 백업 제품과 관련된 “에이전트 관리 오버헤드”의 사용자를 안심시킵니다.

### 가상 컴퓨터를 등록하려면

1. Azure 포털의 **복구 서비스**에서 찾을 수 있는 백업 저장소로 이동하여 **등록된 항목** 탭을 클릭합니다.

2. 드롭다운 메뉴의 작업 유형을 **Azure 가상 컴퓨터**로 선택하고 선택 단추를 클릭합니다.
  	![select workload][select-workload]

3. 페이지 아래쪽에서 **등록** 단추를 클릭합니다.
  	![register button][register-button]

4. **등록 항목** 팝업에서 등록하려는 가상 컴퓨터를 선택합니다. 동일한 이름으로 둘 이상의 가상 컴퓨터가 있는 경우 가상 컴퓨터 사이에서 구별하기 위해 클라우드 서비스를 사용합니다.

  	**등록** 작업은 대규모로 수행될 수 있으며 이는 여러 가상 컴퓨터를 한 번에 선택하여 등록할 수 있다는 뜻입니다. 백업을 위해 가상 컴퓨터를 준비하는 데 들이는 한 번의 노력을 크게 줄여 줍니다.

  	> [AZURE.NOTE]등록되지 않고 백업 저장소와 동일한 지역에 있는 가상 컴퓨터만 여기에 나타납니다.

  	![List of VMs to be registered][register-vms]

5. 등록해야 하는 각 가상 컴퓨터에 대한 작업이 만들어집니다. 알림 메시지는 이 활동의 상태를 보여줍니다. **작업 보기**를 클릭하여 **작업** 페이지로 이동합니다.
   	![register job][register-job]

6. 가상 컴퓨터가 등록된 항목 목록에도 나타나며 등록 작업의 상태가 표시됩니다.
  	![Registering status 1][register-status1]

7. 작업이 완료되면 포털의 상태가 등록된 상태를 반영하도록 변경됩니다.
  	![Registration status 2][register-status2]

<br><br>
## Azure 가상 컴퓨터 백업
이 단계에는 가상 컴퓨터의 백업 및 보존 정책 설정이 포함됩니다. 가상 컴퓨터를 보호하려면 다음 단계를 수행합니다.

1. Azure 포털의 **복구 서비스**에서 찾을 수 있는 백업 저장소로 이동하여 **등록된 항목** 탭을 클릭합니다.

2. 드롭다운 메뉴의 작업 유형을 **Azure 가상 컴퓨터**로 선택하고 선택 단추를 클릭합니다.
  	![Select workload in portal][select-workload]

3. 페이지 아래쪽에서 **보호** 단추를 클릭합니다.

4. 이렇게 하면 보호할 가상 컴퓨터를 선택할 수 있는 **항목 보호** 마법사를 불러 옵니다. 동일한 이름으로 둘 이상의 가상 컴퓨터가 있는 경우 가상 컴퓨터 사이에서 구별하기 위해 클라우드 서비스를 사용합니다. **보호** 작업은 대규모로 수행될 수 있으며 이는 여러 가상 컴퓨터를 한 번에 선택하여 등록할 수 있다는 뜻입니다. 가상 컴퓨터를 준비하는 데 들이는 노력을 크게 줄여 줍니다.

	> [AZURE.NOTE]Azure 백업 서비스에 제대로 등록되었고 백업 저장소와 동일한 지역에 있는 가상 컴퓨터만 여기에 나타납니다.

	![보호할 항목 선택][protect-wizard1]

5. **보호 항목** 마법사의 두 번째 화면에서 백업 및 보존 정책을 선택하여 선택한 가상 컴퓨터를 백업합니다. 정책의 기존 집합에서 선택하거나 새로 정의합니다.

	> [AZURE.NOTE]미리 보기는 최대 30일 보존 및 최대 하루에 한 번 백업이 지원됩니다.

	![보호 정책 선택][protect-wizard2]

	각 백업 저장소에서 여러 백업 정책을 사용할 수 있습니다. 정책은 백업을 예약 및 보존하는 방법에 대한 세부 정보를 반영합니다. 예를들어 다른 백업 정책이 매주 오전 6시 백업인 반면, 한 백업 정책은 매일 밤 10시 백업일 수 있습니다. 여러 백업 정책을 통해 가상 컴퓨터 인프라에 대한 백업 예약을 유연하게 할 수 있습니다. 각 백업 정책 정책에는 해당 정책과 연관된 여러 가상 컴퓨터가 있을 수 있습니다. 가상 컴퓨터는 특정 시간에 한 정책과만 연관될 수 있습니다.

6. 각 가상 컴퓨터에 대한 작업을 만들어 보호 정책을 구성하고 가상 컴퓨터를 정책과 연결합니다. **작업** 탭을 클릭하고 적합한 필터를 선택하여 **보호 구성** 작업 목록을 봅니다.
 ![Configure protection job][protect-configure]

7. 완료되면 가상 컴퓨터가 정책으로 보호되며 초기 백업에 대해 예약된 백업 시간이 완료될 때까지 기다려야 합니다. 이제 가상 컴퓨터가 **보호된 항목** 탭 아래에 표시되며 *보호됨*의 보호된 상태가 됩니다(초기 백업 보류 중).

	> [AZURE.NOTE]보호 구성 후 곧바로 초기 백업을 시작하면 오늘 옵션으로 사용할 수 없습니다.

8. 예약된 시간에 Azure 백업 서비스는 백업이 필요한 각 가상 컴퓨터에 대한 백업 작업을 만듭니다. **작업** 탭을 클릭하여 **백업** 박업 목록을 봅니다. 백업 작업의 일부로 Azure 백업 서비스는 각 가상 컴퓨터에서 백업 확장에 대한 명령을 발행하여 모든 쓰기를 플러시하고 일관된 스냅숏을 찍습니다.
 ![Backup in progress][protect-inprogress]

9. 완료되면 **보호된 항목** 탭에서 가상 컴퓨터의 보호 상태가 *보호됨*으로 표시됩니다.
 ![Virtual machine is backed up with recovery point][protect-backedup]


## 백업 상태 및 세부 정보 보기
보호되면 가상 컴퓨터 수가 **대시보드** 페이지 요약에서도 증가합니다. 또한 대시보드 페이지에서 지난 24시간 내의 성공 및 실패한 작업 수 및 아직 진행 중인 작업 수를 보여줍니다. 한 범주를 클릭하면 **작업** 페이지에서 해당 범주를 자세히 다루게 됩니다.
  	![Status of backup in Dashboard page][dashboard]


## 문제 해결
다음 정보를 사용하여 검색 및 등록 문제를 해결할 수 있습니다.

| 백업 작업 | 오류 세부 정보 | 해결 방법 |
| -------- | -------- | -------|
| 검색 | 새 항목 검색에 실패했습니다. Microsoft Azure 백업에 내부 오류가 발생했습니다. 몇 분간 기다린 다음 작업을 다시 시도하세요. | 15분 후 검색 프로세스를 다시 시도합니다.
| 검색 | 새 항목 검색에 실패했습니다. 다른 검색 작업이 이미 진행 중입니다. 현재 검색 작업을 완료할 때까지 잠시 기다려 주세요. | 기존 검색 작업이 완료될 때까지 기다려 주세요. |
| 등록 | Azure VM 역할이 확장 설치 상태가 아닙니다. VM이 실행 상태에 있는지 확인하세요. Azure 복구 서비스 확장에 VM 실행이 필요합니다. | 가상 컴퓨터를 시작하고 실행 상태에 있을 때 등록 작업을 다시 시도하세요.|


## 복구 지점의 일관성
백업 데이터를 처리할 때 고객은 복원된 후 VM의 동작에 대해 우려합니다. 고객이 문의하는 일반적인 질문은 다음과 같습니다.

+ 가상 컴퓨터가 부팅되나요?

+ 해당 데이터를 디스크에서 사용할 수 있나요? (또는) 데이터 손실이 있나요?

+ 응용 프로그램이 데이터를 읽을 수 있나요? (또는) 데이터 손상이 있나요?

+ 응용 프로그램에서 데이터를 이해할 수 있나요? (또는) 응용 프로그램이 데이터를 읽을 때 일관성이 있나요?

다음 표에서 Azure VM 백업 및 복원 중에 발생하는 일관성 유형을 설명합니다.

| 일관성 | VSS 기반 | 설명 및 세부 정보 |
|-------------|-----------|---------|
| 응용 프로그램 일관성 | 예 | 이는 Microsoft 작업에 대해 다음 사항을 확인하는 데 이상적인 장소입니다.<ol><li> VM은 *부팅*됨. <li>*손상 없음*, <li>*데이터 손실* 없음, <li>VSS를 사용하여 백업 시 응용 프로그램을 포함함으로써 데이터를 사용하는 응용 프로그램에 해당 데이터가 일관됨.</ol> VSS(볼륨 스냅숏 서비스)를 통해 데이터가 저장소에 제대로 작성되었음을 확인할 수 있습니다. 대부분의 Microsoft 작업에는 데이터 일관성과 관련된 작업 관련 동작을 수행하는 VSS 작성자가 있습니다. 예를들어 Microsoft SQL Server에는 트랜잭션 로그 파일에 대한 쓰기를 확인하고 데이터베이스가 제대로 수행되었는지 확인하는 VSS 작성자가 있습니다.<br><br> Azure VM 백업의 경우, 응용 프로그램에 일관된 복구 지점이 있다는 것은 백업 확장이 VSS 워크플로를 적용하고 VM 스냅숏을 촬영하기 전에 *제대로* 완료할 수 있었음을 의미합니다. 기본적으로 Azure VM에서 모든 응용 프로그램의 VSS 작성자도 적용되었음을 의미합니다.<br><br>[어떻게 동작하는지](https://technet.microsoft.com/ko-kr/library/cc785914%28v=ws.10%29.aspx)에 대해 자세히 다루어 [VSS의 기본 사항](http://blogs.technet.com/b/josebda/archive/2007/10/10/the-basics-of-the-volume-shadow-copy-service-vss.aspx)에 대해 알아봅니다. |
| 파일 시스템 일관성 | 예 - Windows 컴퓨터의 경우 | 파일 시스템이 일관될 수 있는 복구 지점이 있는 두 가지 시나리오가 있습니다.<ul><li>Azure에서 Linux VM 백업, Linux에는 VSS에 해당하는 플랫폼이 없기 때문.<li>Azure에서 Windows VM 백업 중에 VSS 오류.</li></ul> 두 경우 모두 최선의 방법은 다음을 확인하는 것입니다. <ol><li>VM *부팅* <li>*손상 없음*, <li>*데이터 손실* 없음.</ol> 응용 프로그램은 복원된 데이터에 대해 고유한 "수정" 메커니즘을 구현해야 합니다.|
| 충돌 일관성 | 아니요 | 이 상황은 "충돌"을 경험하는 컴퓨터와 같습니다(소프트 또는 하드 재설정 모두). 저장소 매체에 있는 데이터의 일관성을 보장할 수 없습니다. 백업 시 디스크에 이미 존재 하는 데이터만이 캡처 및 백업된 내용입니다. <ol><li>보장은 없지만 대부분의 경우에 OS는 부팅됩니다.<li>이는 일반적으로 손상 오류를 수정하기 위한 chkdsk와 같은 디스크 검사 과정 이후에 발생합니다.<li> 메모리 내 데이터 또는 완전히 플러시되지 않은 쓰기에서 디스크가 손실됩니다.<li> 일반적으로 응용 프로그램은 데이터 롤백 작업을 수행해야 하는 경우에 고유한 확인 매커니즘을 수행합니다. </ol>Azure VM 백업의 경우, 일관된 복구 지점에 충돌이 있다는 것은 Azure 백업이 저장소의 데이터 일관성을 보장해 주지 않는다는 뜻입니다(OS 관점 또는 응용 프로그램의 관점에서). 일반적으로 백업 시 Azure VM이 종료되는 경우 발생합니다.<br><br>예를들어 트랜잭션 로그에 데이터베이스에 없는 항목이 있는 경우 데이터베이스 소프트웨어는 데이터가 일관성을 찾을 때가지 롤백합니다. 여러 가상 디스크(예: 스팬 볼륨)에 분산된 데이터를 다룰 때 충돌-일관성 복구 지점은 데이터 정확성에 대해 보장하지 않습니다.|



## 다음 단계
Azure 백업 시작하기에 대해 자세히 알아보려면 다음을 참조하세요.

- [가상 컴퓨터 복원](backup-azure-restore-vms.md)

[three-steps]: ./media/backup-azure-vms/3-steps-for-backup.png
[select-workload]: ./media/backup-azure-vms/discovery-select-workload.png
[discover-button]: ./media/backup-azure-vms/discover-button.png
[discover-vms]: ./media/backup-azure-vms/discovering-vms.png
[discover-done]: ./media/backup-azure-vms/discovery-complete.png
[register-button]: ./media/backup-azure-vms/register-button.png
[register-job]: ./media/backup-azure-vms/register-create-job.png
[register-vms]: ./media/backup-azure-vms/register-popup.png
[register-status1]: ./media/backup-azure-vms/register-status01.png
[register-status2]: ./media/backup-azure-vms/register-status02.png
[select-workload]: ./media/backup-azure-vms/select-workload.png
[protect-wizard1]: ./media/backup-azure-vms/protect-wizard1.png
[protect-wizard2]: ./media/backup-azure-vms/protect-wizard2.png
[protect-configure]: ./media/backup-azure-vms/protect-configureprotection.png
[protect-inprogress]: ./media/backup-azure-vms/protect-inprogress.png
[protect-backedup]: ./media/backup-azure-vms/protect-backedupvm.png
[dashboard]: ./media/backup-azure-vms/dashboard-protectedvms.png
[prereq]: http://azure.microsoft.com/documentation/articles/backup-azure-vms-introduction/#prerequisites

<!--HONumber=54-->