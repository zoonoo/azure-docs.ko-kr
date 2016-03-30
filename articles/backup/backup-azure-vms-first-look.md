<properties
	pageTitle="Azure 백업으로 Azure에서 VM 보호 | Microsoft Azure"
	description="Azure 백업 서비스로 Azure VM을 보호합니다. 자습서에서는 Azure의 자격 증명 모음 만들기, VM 등록, 정책 만들기, VM 보호를 설명합니다."
	services="backup"
	documentationCenter=""
	authors="markgalioto"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="03/14/2016"
	ms.author="markgal; jimpark"/>


# 소개: Azure 가상 컴퓨터 백업

이 문서는 Azure VM(가상 컴퓨터)을 백업하도록 Azure 환경을 준비하기 위한 일련의 단계를 안내하는 자습서입니다. 이 자습서에서는 Azure 구독에 이미 VM이 있으며 VM에 액세스하는 백업 서비스를 허용하도록 조치를 취했다고 가정합니다. 높은 수준에서 볼 때 다음 단계를 완료해야 합니다.

![VM 백업 프로세스의 상위 수준 보기](./media/backup-azure-vms-first-look/BackupAzureVM.png)


1. Azure 구독을 만들거나 로그인합니다.
2. *VM과 동일한 지역*에서 백업 자격 증명 모음을 만들거나 기존의 백업 자격 증명 모음을 식별합니다.
3. Azure 포털을 사용하여 구독에서 가상 컴퓨터를 검색하고 등록합니다.
4. 가상 컴퓨터에 VM 에이전트를 설치합니다(Azure 갤러리에서 VM을 사용하는 경우 VM 에이전트가 이미 있음).
5. 가상 컴퓨터를 보호하는 정책을 만듭니다.
6. 백업을 실행합니다.

>[AZURE.NOTE] Azure에는 리소스를 만들고 작업하기 위한 두 가지 배포 모델인 [리소스 관리자와 클래식](../resource-manager-deployment-model.md) 모델이 있습니다. 현재, Azure 백업 서비스는 IaaS V2 가상 컴퓨터라고 하는 ARM(Azure Resource Manager)를 지원하지 않습니다. IaaS V2 VM은 새 Azure 포털이 릴리스될 때 함께 제공되기 때문에 이 자습서는 Azure 클래식 포털에서 만들어질 수 있는 형식의 VM으로 사용하도록 디자인됩니다.


## 1단계 - VM에 대한 백업 자격 증명 모음 만들기

백업 자격 증명 모음은 모든 백업과 시간에 따라 생성된 복구 지점을 저장하는 엔터티입니다. 백업 자격 증명 모음에는 백업 중인 가상 컴퓨터에 적용할 백업 정책도 포함됩니다.

이 그림은 여러 Azure 백업 엔터티 간의 관계를 보여 줍니다. ![Azure 백업 엔터티 및 관계](./media/backup-azure-vms-prepare/vault-policy-vm.png)

백업 자격 증명 모음을 만들려면:

1. [Azure 포털](http://manage.windowsazure.com/)에 로그인합니다.

2. Azure 포털에서 **새로 만들기** > **데이터 서비스** > **복구 서비스** > **백업 자격 증명 모음** > **빨리 만들기**(아래 이미지 참조)를 클릭합니다.

    ![백업 자격 증명 모음 만들기](./media/backup-azure-vms-first-look/backup-vaultcreate.png)

3. **이름**에는 자격 증명 모음을 식별하기 위한 이름을 입력합니다. 이름은 Azure 구독에 대해 고유해야 합니다. 이름을 2~50자 사이로 입력합니다. 문자로 시작해야 하며, 문자, 숫자, 하이픈만 사용할 수 있습니다.

4. **지역**에서 자격 증명 모음에 대한 지리적 지역을 선택합니다. 자격 증명 모음은 보호하려는 가상 컴퓨터와 동일한 지역에 **있어야** 합니다.

    VM이 있는 지역을 정확히 알지 못하는 경우 자격 증명 모음 만들기 대화 상자를 닫고 포털에서 가상 컴퓨터의 목록으로 이동합니다. 여러 지역에 가상 컴퓨터가 있는 경우 각 지역의 백업 자격 증명 모음을 만들어야 하지만 다음 지역으로 이동하기 전에 첫 번째 지역에 자격 증명 모음 만들기를 완료합니다. 백업 데이터를 저장하기 위해 저장소 계정을 지정할 필요는 없습니다. 백업 자격 증명 모음 및 Azure 백업 서비스가 자동으로 처리합니다.

5. **구독**에서 백업 자격 증명 모음과 연결하려는 구독을 선택합니다. 조직 계정이 여러 Azure 구독과 연결된 경우에만 여러 항목을 선택할 수 있습니다.

6. **자격 증명 모음 만들기**를 클릭합니다. 백업 자격 증명 모음을 만드는 데 시간이 걸릴 수 있습니다. 포털의 맨 아래에서 상태 알림을 모니터링합니다.

    ![자격 증명 모음 알림 메시지 만들기](./media/backup-azure-vms-first-look/create-vault-demo.png)

    자격 증명 모음이 성공적으로 만들어졌다는 메시지가 표시됩니다. **복구 서비스** 페이지에서 **활성**으로 표시됩니다.

    ![자격 증명 모음 알림 메시지 만들기](./media/backup-azure-vms-first-look/create-vault-demo-success.png)

7. **복구 서비스** 페이지의 자격 증명 모음 목록에서 만든 자격 증명 모음을 클릭하여 **빠른 시작** 페이지를 시작합니다.

    ![백업 자격 증명 모음 목록](./media/backup-azure-vms-first-look/active-vault-demo.png)

8. **빠른 시작** 페이지에서 **구성**을 클릭하여 저장소 복제 옵션을 엽니다. ![백업 자격 증명 모음 목록](./media/backup-azure-vms-first-look/configure-storage.png)

9. **저장소 복제** 옵션에서 사용자 자격 증명 모음에 대한 복제 옵션을 선택합니다.

    ![백업 자격 증명 모음 목록](./media/backup-azure-vms-first-look/backup-vault-storage-options-border.png)

    기본적으로 사용자 자격 증명 모음에는 지역 중복 저장소가 있습니다. Azure를 기본 백업 저장소 끝점으로 사용하는 경우 지역 중복 저장소를 사용하여 계속하는 것이 좋습니다. Azure를 기본이 아닌 백업 저장소 끝점으로 사용하는 경우 Azure에서 데이터를 저장하는 비용을 절약하는 로컬 중복 저장소를 선택하도록 고려할 수 있습니다. 이 [개요](../storage/storage-redundancy.md)에서 [지역 중복](../storage/storage-redundancy.md#geo-redundant-storage) 및 [로컬 중복](../storage/storage-redundancy.md#locally-redundant-storage) 저장소 옵션에 대해 자세히 알아봅니다.

자격 증명 모음에 대한 저장소 옵션을 선택하면 자격 증명 모음이 있는 VM에 연결할 준비가 됩니다. 연결을 시작하려면 Azure 가상 컴퓨터를 검색하고 등록해야 합니다.

## 2단계 - Azure 가상 컴퓨터 검색 및 등록
자격 증명 모음으로 VM을 등록하기 전에 검색 프로세스를 실행하여 구독에 추가된 새 가상 컴퓨터를 식별하도록 합니다. 프로세스는 클라우드 서비스 이름 및 지역과 같은 추가 정보와 함께 구독의 가상 컴퓨터 목록을 Azure에 쿼리합니다.

1. [Azure 포털](http://manage.windowsazure.com/)에 로그인합니다.

2. Azure 클래식 포털에서 **복구 서비스**를 클릭하여 복구 서비스 자격 증명 모음 목록을 엽니다. ![워크로드 선택](./media/backup-azure-vms-first-look/recovery-services-icon.png)

3. **복구 서비스** 자격 증명 모음 목록에서 VM을 백업하는 데 사용할 자격 증명 모음을 선택합니다.

    자격 증명 모음을 선택하면 **빠른 시작** 페이지에 열립니다.

4. 자격 증명 모음 메뉴(페이지 맨 위에 있음)에서 **등록된 항목**을 클릭합니다.

5. **형식** 메뉴에서 **Azure 가상 컴퓨터**를 선택합니다.

    ![워크로드 선택](./media/backup-azure-vms/discovery-select-workload.png)

6. 페이지 맨 아래에서 **검색**을 클릭합니다. ![검색 단추](./media/backup-azure-vms/discover-button-only.png)

    검색 프로세스는 가상 컴퓨터를 표로 정리하는 동안 몇 분이 걸릴 수 있습니다. 화면 맨 아래에 있는 알림은 프로세스가 실행되고 있다는 것을 알려줍니다.

    ![VM 검색](./media/backup-azure-vms/discovering-vms.png)

    프로세스가 완료되면 알림이 변경됩니다.

    ![검색 완료](./media/backup-azure-vms-first-look/discovery-complete.png)

7. 페이지의 맨 아래에서 **등록**을 클릭합니다. ![등록 단추](./media/backup-azure-vms-first-look/register-icon.png)

8. **등록 항목** 바로 가기 메뉴에서 등록하려는 가상 컴퓨터를 선택합니다. 동일한 이름을 가진 가상 컴퓨터가 두 개 이상 있는 경우 클라우드 서비스를 사용하여 가상 컴퓨터를 구분합니다.

    >[AZURE.TIP] 한 번에 여러 가상 컴퓨터를 등록할 수 있습니다.

    선택한 각 가상 컴퓨터에 대한 작업이 만들어집니다.

9. 알림에서 **작업 보기**를 클릭하여 **작업** 페이지로 이동합니다.

    ![작업 등록](./media/backup-azure-vms/register-create-job.png)

    또한 등록 작업의 상태와 함께 가상 컴퓨터가 등록된 항목 목록에 나타납니다.

    ![등록 상태 1](./media/backup-azure-vms/register-status01.png)

    작업이 완료되면 상태가 *등록된* 상태를 반영하도록 변경됩니다.

    ![등록 상태 2](./media/backup-azure-vms/register-status02.png)

## 3단계 - 가상 컴퓨터에 VM 에이전트 설치

Azure VM 에이전트는 작업할 백업 확장을 위한 Azure 가상 컴퓨터에 설치되어야 합니다. Azure 갤러리에서 VM을 만든 경우 VM 에이전트는 이미 가상 컴퓨터에 있습니다. 그러나 온-프레미스 데이터 센터에서 마이그레이션한 VM에는 VM 에이전트가 설치되어 있지 않습니다. 이러한 경우에 VM 에이전트를 명시적으로 설치해야 합니다. Azure VM을 백업하려고 시도하기 전에 Azure VM 에이전트가 가상 컴퓨터에 올바르게 설치되었는지 확인합니다(아래 테이블 참조). 사용자 지정 VM을 만드는 경우 가상 컴퓨터를 프로비전하기 전에 [**VM 에이전트 설치** 확인란을 선택해야 합니다](../virtual-machines/virtual-machines-windows-classic-agents-and-extensions.md).

[VM 에이전트](https://go.microsoft.com/fwLink/?LinkID=390493&clcid=0x409) 및 [설치 방법](../virtual-machines/virtual-machines-windows-classic-manage-extensions.md)에 대해 알아보세요.

다음 테이블에서는 Windows 및 Linux VM용 VM 에이전트에 대한 추가 정보를 제공합니다.

| **작업** | **Windows** | **Linux** |
| --- | --- | --- |
| VM 에이전트 설치 | <li>[에이전트 MSI](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)를 다운로드하여 설치합니다. 설치를 완료하려면 관리자 권한이 있어야 합니다. <li>[VM 속성을 업데이트](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx)하여 에이전트가 설치되었다고 표시합니다. | <li> GitHub에서 최신 [Linux 에이전트](https://github.com/Azure/WALinuxAgent)를 설치합니다. 설치를 완료하려면 관리자 권한이 있어야 합니다. <li> [VM 속성을 업데이트](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx)하여 에이전트가 설치되었다고 표시합니다. |
| VM 에이전트 업데이트 | VM 에이전트 업데이트는 [VM 에이전트 이진](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)을 다시 설치하면 되는 간단한 작업입니다. <br>VM 에이전트를 업데이트하는 동안 실행 중인 백업 작업이 없도록 합니다. | [Linux VM 에이전트 업데이트](../virtual-machines-linux-update-agent.md)의 지침을 수행합니다. <br>VM 에이전트를 업데이트하는 동안 실행 중인 백업 작업이 없도록 합니다. |
| VM 에이전트 설치 유효성 검사 | <li>Azure VM에서 *C:\\WindowsAzure\\Packages* 폴더로 이동합니다. <li>WaAppAgent.exe 파일을 찾습니다.<li> 파일을 마우스 오른쪽 단추로 클릭하고 **속성**으로 이동한 다음 **세부 정보** 탭을 선택합니다. 제품 버전 필드가 2.6.1198.718 이상이어야 합니다. | 해당 없음 |


### 백업 확장

가상 컴퓨터에 VM 에이전트를 설치하면 Azure 백업 서비스는 VM 에이전트에 대한 백업 확장을 설치합니다. Azure 백업 서비스는 추가 사용자 개입 없이 원활하게 백업 확장을 업그레이드 및 패치합니다.

백업 확장은 VM의 실행 여부와 상관없이 백업 서비스에 의해 설치됩니다. 실행 중인 VM은 응용 프로그램 일치 복구 지점을 확보할 수 있는 큰 기회를 제공합니다. 그러나 Azure 백업 서비스는 VM이 꺼져 확장을 설치할 수 없더라도 VM을 계속 백업합니다. 오프라인 VM이라고 합니다. 이 경우에 복구 지점은 *크래시 일관성 상태*가 됩니다.


## 4단계 - Azure 가상 컴퓨터 보호
이제 가상 컴퓨터에 대한 백업 및 보존 정책을 설정할 수 있습니다. 단일 보호 작업을 사용하여 여러 가상 컴퓨터를 보호할 수 있습니다. 2015년 5월 이후에 만든 Azure 백업 자격 증명 모음은 자격 증명 모음에 기본 제공되는 기본 정책을 사용합니다. 이 기본 정책을 30일의 기본 보존 기간 및 하루 한 번 백업 일정과 함께 제공됩니다.

1. Azure 포털의 **복구 서비스**에 있는 백업 저장소로 이동한 다음, **등록된 항목**을 클릭합니다.
2. 드롭다운 메뉴에서 **Azure 가상 컴퓨터**를 선택합니다.

    ![포털에서 워크로드 선택](./media/backup-azure-vms/select-workload.png)

3. 페이지 맨 아래에 있는 **보호**를 클릭합니다. ![보호 클릭](./media/backup-azure-vms-first-look/protect-icon.png)

    **항목 마법사 보호**가 나타나고 등록되었지만 보호되지 않은 가상 컴퓨터*만* 나열됩니다.

    ![규모로 보호 구성](./media/backup-azure-vms/protect-at-scale.png)

4. 보호하려는 가상 컴퓨터를 선택합니다.

    동일한 이름으로 둘 이상의 가상 컴퓨터가 있는 경우 가상 컴퓨터 사이에서 구별하기 위해 클라우드 서비스를 사용합니다.

5. **보호 구성**에서 기존 정책을 선택하거나 새 정책을 만들어서 식별할 가상 컴퓨터를 보호합니다.

    각 백업 정책 정책에는 해당 정책과 연관된 여러 가상 컴퓨터가 있을 수 있습니다. 그러나 가상 컴퓨터는 특정 시간에 한 정책에만 연관될 수 있습니다.

    ![새 정책으로 보호](./media/backup-azure-vms/policy-schedule.png)

    >[AZURE.NOTE] 백업 정책은 예약된 백업의 보존 체계를 포함합니다. 기존 백업 정책을 선택하면 다음 단계에서 보존 옵션을 수정할 수 없습니다.

6. **보존 범위**에서 특정 백업 지점에 대한 매일, 매주, 매월 및 매년 범위를 정의합니다.

    ![유연한 보존으로 보호](./media/backup-azure-vms/policy-retention.png)

    보존 정책은 백업을 저장하는 시간을 지정합니다. 백업이 수행되는 시기에 따라 다른 보존 정책을 지정할 수 있습니다. 예를 들어 각 분기 말에 수행된 백업 지점은 감사를 위해 더 오랜 기간 동안 유지되어야 하는 반면, 매일 수행된 백업 지점(작업 복구 지점으로 사용됨)은 90일 동안 유지되어야 합니다.

    ![가상 컴퓨터는 복구 지점으로 백업됨](./media/backup-azure-vms/long-term-retention.png)

    이 예제 이미지에서

    - **일 단위 보존 정책**: 매일 수행된 백업이 30일 동안 저장됩니다.
    - **주 단위 보존 정책**: 매주 일요일에 수행된 백업이 104주 동안 유지됩니다.
    - **월 단위 보존 정책**: 매달 마지막 주 일요일에 수행된 백업이 120개월 동안 유지됩니다.
    - **월 단위 보존 정책**: 매년 1월 첫째 주 일요일에 수행된 백업이 99년 동안 유지됩니다.

    작업은 선택한 가상 컴퓨터 각각에 대해 보호 정책을 구성하고 가상 컴퓨터를 해당 정책과 연결합니다.

6. **작업**을 클릭하고 적합한 필터를 선택하여 **보호 구성** 작업 목록을 봅니다.

    ![보호 작업 구성](./media/backup-azure-vms/protect-configureprotection.png)


## 5단계 - 초기 백업

가상 컴퓨터가 정책으로 보호되면 **보호된 항목** 탭에서 해당 관계를 볼 수 있습니다. VM에 대한 초기 백업이 발생할 때까지 **보호 상태**는 **보호됨 - (초기 백업 보류 중)**으로 표시됩니다. 기본적으로 첫 번째 예약된 백업은 *초기 백업*입니다.

![보류 중인 백업](./media/backup-azure-vms-first-look/protection-pending-border.png)

보호를 구성한 후에 즉시 초기 백업을 트리거하려면.

1. **보호된 항목** 페이지의 아래쪽에서 **지금 백업**을 클릭합니다. ![지금 백업 아이콘](./media/backup-azure-vms-first-look/backup-now-icon.png)

    Azure 백업 서비스는 초기 백업 작업에 대한 백업 작업을 만듭니다.

2. **작업** 탭을 클릭하여 작업 목록을 봅니다.

    ![진행 중인 백업](./media/backup-azure-vms-first-look/protect-inprogress.png)

    >[AZURE.NOTE] 백업 작업의 일부로 Azure 백업 서비스는 각 가상 컴퓨터에서 백업 확장에 대한 명령을 발행하여 모든 쓰기를 플러시하고 일관된 스냅숏을 찍습니다.

    초기 백업이 완료되면 **보호된 항목** 탭에서 가상 컴퓨터의 상태가 *보호됨*으로 표시됩니다.

    ![가상 컴퓨터는 복구 지점으로 백업됨](./media/backup-azure-vms/protect-backedupvm.png)

    >[AZURE.NOTE] 가상 컴퓨터 백업은 로컬 프로세스입니다. 한 지역에서 다른 지역의 백업 자격 증명 모음에 가상 컴퓨터를 백업할 수 없습니다. 따라서 백업해야 하는 VM이 있는 모든 Azure 지역의 경우, 해당 지역에 1개 이상의 백업 저장소가 만들어져야 합니다.

## 다음 단계
이제 VM을 성공적으로 백업하면 도움이 될 수 있는 다음 단계가 있습니다. VM에 데이터를 복원하는 작업을 익히는 것이 합리적이지만 데이터를 안전하게 보관하고 비용을 절감하는 방법을 이해하도록 돕는 관리 태스크입니다.

- [가상 컴퓨터 관리 및 모니터링](backup-azure-manage-vms.md)
- [가상 컴퓨터 복원](backup-azure-restore-vms.md)
- [문제 해결 지침](backup-azure-vms-troubleshoot.md)


## 질문이 있으십니까?
질문이 있거나 포함되었으면 하는 기능이 있는 경우 [의견을 보내 주세요](http://aka.ms/azurebackup_feedback).

<!---HONumber=AcomDC_0323_2016-->