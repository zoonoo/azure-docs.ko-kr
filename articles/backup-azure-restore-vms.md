<properties
	pageTitle="Azure 백업 - 가상 컴퓨터 복원"
	description="Azure 가상 컴퓨터 복원 방법 알아보기"
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
	ms.date="05/27/2015"
	ms.author="aashishr"/>

# 가상 컴퓨터 복원
복원 동작을 사용하여 Azure 백업 자격 증명에 저장된 백업을 통해 새 VM에 가상 컴퓨터를 복원할 수 있습니다.

## 복원할 항목 선택

1. **보호된 항목** 탭으로 이동하고 새 VM에 복원할 가상 컴퓨터를 선택합니다.

    ![보호된 항목](./media/backup-azure-restore-vms/protected-items.png)

    **보호된 항목** 페이지의 **복구 지점** 열에는 가상 컴퓨터의 복구 지점 수가 표시됩니다. **가장 새로운 복구 지점** 열은 가상 컴퓨터를 복원할 수 있는 가장 최근의 백업 시간을 나타냅니다.

2. **복원**을 클릭하여 **항목 복원** 마법사를 엽니다.

    ![항목 복원](./media/backup-azure-restore-vms/restore-item.png)

## 복구 지점 선택

1. **복구 지점 선택** 화면에서 최신 복구 지점 또는 이전 시점을 복원할 수 있습니다. 마법사가 열릴 때 선택된 기본 옵션은 가장 새로운 복구 지점입니다.

    ![복구 지점 선택](./media/backup-azure-restore-vms/select-recovery-point.png)

2. 이전 시점을 선택하려면 드롭다운에서 **날짜 선택** 옵션을 선택하고 달력 아이콘을 클릭하여 달력 컨트롤에서 날짜를 선택합니다. 컨트롤에서 복구 지점이 있는 모든 날짜는 밝은 회색 음영으로 채워져 있으며 사용자가 선택할 수 있습니다.

    ![날짜 선택](./media/backup-azure-restore-vms/select-date.png)

    달력 컨트롤에서 날짜를 클릭하면 아래의 복구 지점 표에 해당 날짜에서 사용 가능한 복구 지점이 나타납니다. **시간** 열은 스냅숏이 만들어진 시간을 나타냅니다.  **유형** 열이 표시는 [일관성](https://azure.microsoft.com/documentation/articles/backup-azure-vms/#consistency-of-recovery-points) 의 복구 지점. 테이블 머리글의 괄호 안에는 해당 날짜에 사용할 수 있는 복구 지점의 수가 표시됩니다.

    ![복구 지점](./media/backup-azure-restore-vms/recovery-points.png)

3. **복구 지점** 표에서 복구 지점을 선택하고 다음 화살표를 클릭하여 다음 화면으로 이동합니다.

## 대상 위치 지정

1. **복원 인스턴스 선택** 화면에서 가상 컴퓨터를 복원할 위치에 대한 세부 정보를 지정합니다.

  - 가상 컴퓨터 이름 지정: 가상 컴퓨터 이름은 주어진 클라우드 서비스 내에서 고유해야 합니다. 기존 VM을 같은 이름으로 대체하려는 경우 먼저 기존 VM 및 데이터 디스크를 삭제하고 Azure 백업에서 데이터를 복원합니다.
  - VM에 대한 클라우드 서비스 선택: VM을 만들기 위한 필수 항목입니다. 기존 클라우드 서비스를 사용하거나 새 클라우드 서비스 만들기를 선택할 수 있습니다.

        Whatever cloud service name is picked should be globally unique. Typically, the cloud service name gets associated with a public-facing URL in the form of [cloudservice].cloudapp.net. Azure will not allow you to create a new cloud service if the name has already been used. If you choose to create select create a new cloud service, it will be given the same name as the virtual machine – in which case the VM name picked should be unique enough to be applied to the associated cloud service.

        We only display cloud services and virtual networks that are not associated with any affinity groups in the restore instance details. [Learn More](https://msdn.microsoft.com/en-us/library/azure/jj156085.aspx).

2. VM에 대한 저장소 계정 선택: VM을 만들기 위한 필수 항목입니다. Azure 백업 자격 증명 모음과 동일한 지역에 있는 기존 저장소 계정 중에서 선택할 수 있습니다. 영역 중복 또는 프리미엄 저장소 형식의 저장소 계정은 지원되지 않습니다.

    지원되는 구성의 저장소 계정이 없는 경우 복원 작업을 시작하기 전에 지원되는 구성의 저장소 계정을 만드세요.

    ![가상 네트워크 선택](./media/backup-azure-restore-vms/restore-sa.png)

3. 가상 네트워크 선택: VM을 만들 때 가상 컴퓨터의 가상 네트워크(VNET)를 선택해야 합니다. 복원 UI는 이 구독 내에서 사용할 수 있는 모든 VNET을 보여줍니다. 복원된 VM의 VNET을 선택하는 것은 필수가 아닙니다. VNET을 적용하지 않더라도 인터넷을 통해 복원된 가상 컴퓨터에 연결할 수 있습니다.

    선택한 클라우드 서비스가 가상 네트워크와 연결 되면 가상 네트워크를 변경할 수 없습니다.

    ![가상 네트워크 선택](./media/backup-azure-restore-vms/restore-cs-vnet.png)

4. 서브넷 선택: VNET에 서브넷이 있는 경우 기본적으로 첫 번째 서브넷이 선택됩니다. 드롭다운 옵션 중에서 원하는 서브넷을 선택합니다. 서브넷 세부 정보에 대 한 네트워크 확장에서 이동 된 [포털 홈페이지](https://manage.windowsazure.com/), 가상 네트워크로 이동 하 고 가상 네트워크를 선택 하 고 서브넷 세부 정보를 보려면 구성으로 드릴 다운 합니다.

    ![서브넷 선택](./media/backup-azure-restore-vms/select-subnet.png)

5. 마법사에서 **제출** 아이콘을 클릭하여 세부 정보를 제출하고 복원 작업을 만듭니다.

## 복원 작업 추적
복원 마법사에 모든 정보를 입력하고 제출하면 Azure 백업이 복원 작업을 추적하는 작업을 만듭니다.

![복원 작업 생성 중](./media/backup-azure-restore-vms/create-restore-job.png)

작업 생성에 성공하면 작업이 생성되었음을 알리는 토스트 알림이 표시됩니다. **작업 보기** 단추를 클릭하면 **작업** 탭으로 이동하고 더 자세한 정보를 볼 수 있습니다.

![복원 작업이 생성됨](./media/backup-azure-restore-vms/restore-job-created.png)

복원 작업이 완료되면 **작업** 탭에 완료된 것으로 표시됩니다.

![복원 작업 완료](./media/backup-azure-restore-vms/restore-job-complete.png)

가상 컴퓨터 복원 작업 후 원래 VM에서 기존 확장 프로그램을 다시 설치 해야할 수 및 [끝점을 다시](virtual-machines-set-up-endpoints) Azure 포털에서 가상 컴퓨터에 대 한 합니다.

## 문제 해결
대부분의 오류에 대해 오류 정보에 제시된 권장 조치를 따를 수 있습니다. 문제 해결에 도움이 되는 몇 가지 추가 사항은 다음과 같습니다.

| 백업 작업 | 오류 세부 정보 | 해결 방법 |
| -------- | -------- | -------|
| 복원 | 클라우드 내부 오류로 인해 복원 실패 | <ol><li>복원하려는 클라우드 서비스가 DNS 설정을 사용하여 구성되었습니다. <br>$deployment = Get-AzureDeployment -ServiceName "ServiceName" -Slot "Production" Get-AzureDns -DnsSettings $deployment.DnsSettings<br>를 확인하면 알 수 있습니다. 구성된 주소가 있으면 DNS 설정이 구성 되었다는 의미입니다.<br> <li>복원하려는 클라우드 서비스가 ReservedIP를 사용하여 구성되어 있고 클라우드 서비스의 기존 VM이 중지된 상태입니다.<br>다음 powershell cmdlet을 사용하여 클라우드 서비스에 IP 예약어가 있는지 확인할 수 있습니다.<br>$deployment = Get-AzureDeployment -ServiceName "servicename" -Slot "Production" $dep.ReservedIPName</ol> |

## 다음 단계
- [가상 컴퓨터 관리](backup-azure-manage-vms)

<!---HONumber=GIT-SubDir-->