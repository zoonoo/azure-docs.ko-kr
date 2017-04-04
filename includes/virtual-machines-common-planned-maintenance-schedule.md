

## <a name="multi-and-single-instance-vms"></a>다중 및 단일 인스턴스 VM
Azure에서 실행하는 많은 고객에게는 VM이 작동 중단 시간(약 15분)으로 인해 유지 관리하는 동안 발생하는 계획된 유지 관리를 수행하는 경우 예약할 수 있는지가 중요합니다. 가용성 집합을 사용하여 프로비전된 VM이 계획된 유지 관리를 받는 시기를 제어할 수 있습니다.

Azure에서 실행되는 VM에 대한 두 가지 구성이 가능합니다. VM은 다중 인스턴스 또는 단일 인스턴스로 구성됩니다. VM이 가용성 집합에 있으면 해당 VM은 다중 인스턴스로 구성된 것입니다. 단일 VM도 가용성 집합에 배포할 수 있으며 다중 인스턴스로 취급됩니다. VM이 가용성 집합에 없으면 해당 VM은 단일 인스턴스로 구성된 것입니다.  가용성 집합에 대한 자세한 내용은 [Windows Virtual Machines의 가용성 관리](../articles/virtual-machines/virtual-machines-windows-manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 또는 [Linux Virtual Machines의 가용성 관리](../articles/virtual-machines/virtual-machines-linux-manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)를 참조하세요.

단일 인스턴스 및 다중 인스턴스 VM의 계획된 유지 관리는 따로 발생합니다. VM을 단일 인스턴스로 다시 구성하거나(다중 인스턴스인 경우) 다중 인스턴스로 다시 구성하여(단일 인스턴스인 경우) VM이 계획된 유지 관리를 받는 시간을 제어할 수 있습니다. Azure VM의 계획된 유지 관리에 대한 자세한 내용은 [Azure Linux 가상 컴퓨터에 대한 계획된 유지 관리](../articles/virtual-machines/virtual-machines-linux-planned-maintenance.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 또는 [Windows Azure 가상 컴퓨터에 대한 계획된 유지 관리](../articles/virtual-machines/virtual-machines-windows-planned-maintenance.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 참조하세요.

## <a name="for-multi-instance-configuration"></a>다중 인스턴스 구성의 경우
가용성 집합에서 이러한 VM을 제거하여 계획된 유지 관리가 가용성 집합 구성에 배포된 VM에 영향을 미치는 시간을 선택할 수 있습니다.

1. 다중 인스턴스 구성의 VM에 대한 계획된 유지 관리가 실행되기 7일 전에 사용자에게 전자 메일이 전송됩니다. 영향을 받는 다중 인스턴스 VM의 구독 ID 및 이름이 전자 메일 본문에 포함됩니다.
2. 이 7일 동안 가용성 집합에서 해당 지역의 다중 인스턴스 VM을 제거하여 인스턴트가 업데이트되는 시간을 선택할 수 있습니다. 이렇게 구성을 변경하면 가상 컴퓨터가 다시 부팅됩니다. 유지 관리가 목표인 하나의 물리적 호스트에서 유지 관리가 목표가 아닌 다른 물리적 호스트로 가상 컴퓨터가 이동되기 때문입니다.
3. Azure Portal의 가용성 집합에서 VM을 제거할 수 있습니다.

   1. 포털의 가용성 집합에서 제거할 VM을 선택합니다.  

   2. **설정** 아래에서 **가용성 집합**을 클릭합니다.

      ![가용성 집합 선택](./media/virtual-machines-planned-maintenance-schedule/availabilitysetselection.png)

   3. 가용성 집합 드롭다운 메뉴에서 "가용성 집합의 일부가 아님"을 선택합니다.

      ![집합에서 제거](./media/virtual-machines-planned-maintenance-schedule/availabilitysetwarning.png)

   4. 위쪽에서 **저장**을 클릭합니다. **예**를 클릭하여 이 작업이 VM을 다시 시작하도록 승인합니다.

   >[!TIP]
   >나열된 가용성 집합 중 하나를 선택하여 나중에 다중 인스턴스에 VM을 재구성할 수 있습니다.

4. 가용성 집합에서 제거된 VM은 단일 인스턴스 호스트로 이동되며 계획된 유지 관리가 수행되는 동안 가용성 집합 구성으로 업데이트되지 않습니다.
5. 가용성 집합 VM 업데이트가 완료되면(원래 전자 메일에 나와 있는 일정에 따라) VM을 다시 해당 가용성 집합에 추가합니다. 가용성 집합의 일부가 되면 다중 인스턴스로 VM을 재구성하고 다시 부팅이 발생합니다. 일반적으로 전체 Azure 환경에서 모든 다중 인스턴스 업데이트가 완료된 후 단일 인스턴스 유지 관리가 완료됩니다.

Azure PowerShell을 사용하여 가용성 집합에서 VM을 제거할 수도 있습니다.

```
Get-AzureVM -ServiceName "<VmCloudServiceName>" -Name "<VmName>" | Remove-AzureAvailabilitySet | Update-AzureVM
```

## <a name="for-single-instance-configuration"></a>단일 인스턴스 구성의 경우
이러한 VM을 가용성 집합에 추가하여 계획된 유지 관리가 단일 인스턴스 구성의 VM에 영향을 미치는 시간을 선택할 수 있습니다.

단계별 과정

1. 단일 인스턴스 구성의 VM에 대한 계획된 유지 관리가 실행되기 7일 전에 사용자에게 전자 메일이 전송됩니다. 업데이트되는 단일 인스턴스 VM의 구독 ID 및 이름이 전자 메일 본문에 포함됩니다.
2. 이 7일 동안 단일 인스턴스 VM을 같은 지역의 가용성 집합에 추가하여 인스턴스가 다시 부팅되는 시간을 선택할 수 있습니다. 이렇게 구성을 변경하면 가상 컴퓨터가 다시 부팅됩니다. 유지 관리가 목표인 하나의 물리적 호스트에서 유지 관리가 목표가 아닌 다른 물리적 호스트로 가상 컴퓨터가 이동되기 때문입니다.
3. Azure Portal 및 Azure PowerShell을 사용하여 가용성 집합에 기존 VM을 추가하도록 여기에 있는 지침을 따릅니다. (이러한 단계를 수행하는 Azure PowerShell 샘플을 참조하세요.)
4. 이러한 VM이 다중 인스턴스로 다시 구성되면 단일 인스턴스 VM에 대한 계획된 유지 관리에서 제외됩니다.
5. 단일 인스턴스 VM 업데이트가 원래 전자 메일에서 일정에 따라 완료되면 해당 가용성 집합에서 VM을 제거하여 VM을 단일 인스턴스에 반환할 수 있습니다.

Azure PowerShell을 사용하여 가용성 집합에 VM을 추가할 수도 있습니다.

    Get-AzureVM -ServiceName "<VmCloudServiceName>" -Name "<VmName>" | Set-AzureAvailabilitySet -AvailabilitySetName "<AvSetName>" | Update-AzureVM

<!--Anchors-->



<!--Link references-->
[Virtual Machines Manage Availability]: virtual-machines-windows-tutorial.md
[Understand planned versus unplanned maintenance]: virtual-machines-manage-availability.md#Understand-planned-versus-unplanned-maintenance/
