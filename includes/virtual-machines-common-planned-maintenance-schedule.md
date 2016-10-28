

## 다중 및 단일 인스턴스 VM
Azure에서 VM을 실행하는 여러 고객의 경우 VM에 대한 계획된 유지 관리 시간을 예약할 수 있어야 합니다. 계획된 유지 관리가 진행되는 동안 15분 정도 가동이 중지되기 때문입니다. 가용성 집합을 활용하여 프로비전된 VM이 계획된 유지 관리를 받는 시간을 제어할 수 있습니다.

Azure에서 실행되는 VM에 대한 두 가지 구성이 가능합니다. VM은 다중 인스턴스 또는 단일 인스턴스로 구성됩니다. VM이 가용성 집합에 있으면 해당 VM은 다중 인스턴스로 구성된 것입니다. 단일 VM도 가용성 집합에 배포할 수 있으며 다중 인스턴스도 취급됩니다. VM이 가용성 집합에 없으면 해당 VM은 단일 인스턴스로 구성된 것입니다. 가용성 집합에 대한 자세한 내용은 [Windows 가상 컴퓨터의 가용성 관리](../articles/virtual-machines/virtual-machines-windows-manage-availability.md) 또는 [Linux 가상 컴퓨터의 가용성 관리](../articles/virtual-machines/virtual-machines-linux-manage-availability.md)를 참조하세요.

단일 인스턴스 및 다중 인스턴스 VM의 계획된 유지 관리는 따로 발생합니다. VM을 단일 인스턴스로 다시 구성하거나(다중 인스턴스인 경우) 다중 인스턴스로 다시 구성하여(단일 인스턴스인 경우) VM이 계획된 유지 관리를 받는 시간을 제어할 수 있습니다. Azure VM의 계획된 유지 관리에 대한 자세한 내용은 [Azure Linux 가상 컴퓨터에 대한 계획된 유지 관리](../articles/virtual-machines/virtual-machines-linux-planned-maintenance.md) 또는 [Microsoft Azure 가상 컴퓨터에 대한 계획된 유지 관리](../articles/virtual-machines/virtual-machines-windows-planned-maintenance.md)를 참조하세요.

## 다중 인스턴스 구성의 경우
가용성 집합에서 이러한 VM을 제거하여 계획된 유지 관리가 가용성 집합 구성에 배포된 VM에 영향을 미치는 시간을 선택할 수 있습니다.

1.	다중 인스턴스 구성의 VM에 대한 계획된 유지 관리가 실행되기 7일 전에 사용자에게 전자 메일이 전송됩니다. 업데이트되는 다중 인스턴스 VM의 구독 ID 및 이름이 전자 메일 본문에 포함됩니다.

2.	이 7일 동안 가용성 집합에서 해당 지역의 다중 인스턴스 VM을 제거하여 인스턴트가 업데이트되는 시간을 선택할 수 있습니다. 이렇게 구성을 변경하면 가상 컴퓨터가 다시 부팅됩니다. 유지 관리가 목표인 한 물리적 호스트에서 유지 관리가 목표가 아닌 다른 물리적 호스트로 가상 컴퓨터가 이동되기 때문입니다.

3.	클래식 포털의 가용성 집합에서 VM을 제거할 수 있습니다.
   
    1.	클래식 포털에서 VM을 클릭한 다음 "구성"을 선택합니다.

    2.	"설정"에서 VM이 있는 가용성 집합을 확인할 수 있습니다.

        ![가용성 집합 선택](./media/virtual-machines-planned-maintenance-schedule/availabilitysetselection.png)

    3.	가용성 집합 드롭다운 메뉴에서 "가용성 집합에서 제거"를 선택합니다.

        ![집합에서 제거](./media/virtual-machines-planned-maintenance-schedule/availabilitysetselectionconfiguration.png)

    4.	아래쪽에서 “저장”을 선택합니다. 이 작업이 VM을 다시 시작하도록 승인하려면 "예"를 선택합니다.

4.	이러한 VM은 단일 인스턴스 호스트로 이동되며 계획된 유지 관리가 수행되는 동안 가용성 집합 구성에 업데이트되지 않습니다.

5.	가용성 집합 VM 업데이트가 완료되면(원래 전자 메일에 나와 있는 일정에 따라) VM을 다시 해당 가용성 집합에 추가합니다. 그러면 다중 인스턴스 VM으로 다시 구성될 것입니다. VM을 단일 인스턴스에서 다중 인스턴스로 이동하면 다시 부팅됩니다. 일반적으로 전체 Azure 환경에서 모든 다중 인스턴스 업데이트가 완료된 후 단일 인스턴스 유지 관리가 완료됩니다.

다음과 같이 Azure PowerShell을 사용하는 방법도 있습니다.

```
Get-AzureVM -ServiceName "<VmCloudServiceName>" -Name "<VmName>" | Remove-AzureAvailabilitySet | Update-AzureVM
```

## 단일 인스턴스 구성의 경우
이러한 VM을 가용성 집합에 추가하여 계획된 유지 관리가 단일 인스턴스 구성의 VM에 영향을 미치는 시간을 선택할 수 있습니다.

단계별 과정

1.	단일 인스턴스 구성의 VM에 대한 계획된 유지 관리가 실행되기 7일 전에 사용자에게 전자 메일이 전송됩니다. 업데이트되는 단일 인스턴스 VM의 구독 ID 및 이름이 전자 메일 본문에 포함됩니다.

2.	이 7일 동안 VM을 같은 지역의 가용성 집합으로 이동하여 단일 인스턴스 VM을 이동하는 방법으로 인스턴스가 다시 부팅되는 시간을 선택할 수 있습니다. 이렇게 구성을 변경하면 가상 컴퓨터가 다시 부팅됩니다. 유지 관리가 목표인 한 물리적 호스트에서 유지 관리가 목표가 아닌 다른 물리적 호스트로 가상 컴퓨터가 이동되기 때문입니다.

3.	여기에 제공된 지침에 따라 클래식 포털 및 Azure PowerShell을 사용하여 가용성 집합에 기존 VM을 추가합니다(아래에 있는 참고의 Azure PowerShell 샘플 참조).

4.	이러한 VM이 다중 인스턴스로 다시 구성되면 단일 인스턴스 VM에 대한 계획된 유지 관리에서 제외됩니다.

5.	단일 인스턴스 VM 업데이트가 완료되면(원래 전자 메일에 나와 있는 일정에 따라) 해당 가용성 집합에서 VM을 제거합니다. 그러면 단일 인스턴스 VM으로 다시 구성될 것입니다.

다음과 같이 Azure PowerShell을 사용하는 방법도 있습니다.

    Get-AzureVM -ServiceName "<VmCloudServiceName>" -Name "<VmName>" | Set-AzureAvailabilitySet -AvailabilitySetName "<AvSetName>" | Update-AzureVM

<!--Anchors-->



<!--Link references-->
[Virtual Machines Manage Availability]: virtual-machines-windows-tutorial.md
[Understand planned versus unplanned maintenance]: virtual-machines-manage-availability.md#Understand-planned-versus-unplanned-maintenance/

<!---HONumber=AcomDC_0803_2016-->