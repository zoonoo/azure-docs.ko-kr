
Windows 기반의 Azure 가상 컴퓨터에 대한 RDP(원격 데스크톱) 연결 문제 또는 Linux 기반의 Azure 가상 컴퓨터에 대한 SSH 연결 문제를 해결하기 어려운 경우 이 문서를 참조하면 지원을 요청하거나 가상 컴퓨터의 크기를 조정하지 않고 스스로 문제를 해결할 수 있습니다. Azure PowerShell을 통해 다시 배포 작업을 호출하는 경우 Microsoft Azure에서 가상 컴퓨터를 다시 배포합니다.

이 작업이 완료되면 임시 디스크 데이터가 손실되고 가상 컴퓨터와 연결된 동적 IP 주소가 업데이트됩니다.


## Azure PowerShell 사용

최신 Azure PowerShell 1.x를 컴퓨터에 설치했는지 확인합니다. 자세한 내용은 [Azure PowerShell 설치 및 구성 방법](../articles/powershell-install-configure.md)을 참조하세요.

가상 컴퓨터를 다시 배포하려면 이 Azure PowerShell 명령을 사용합니다.

	Set-AzureRmVM -Redeploy -ResourceGroupName $rgname -Name $vmname 


이 명령을 실행하는 동안 [Azure 포털](https://portal.azure.com)에서 가상 컴퓨터를 확인합니다. VM의 **상태**가 다음과 같이 변경됩니다.

1. 초기 **상태**는 *실행 중*입니다.

	![다시 배포 초기 상태](./media/virtual-machines-common-redeploy-to-new-node/statusrunning1.png)

2. **상태**가 *업데이트 중*으로 변경됩니다.

	![다시 배포 업데이트 중](./media/virtual-machines-common-redeploy-to-new-node/statusupdating.png)

3. **상태**가 *시작하는 중*으로 변경됩니다.

	![다시 배포 시작하는 중](./media/virtual-machines-common-redeploy-to-new-node/statusstarting.png)

4. **상태**가 다시 *실행 중*으로 변경됩니다.

	![다시 배포 최종 상태](./media/virtual-machines-common-redeploy-to-new-node/statusrunning2.png)

**상태**가 다시 *실행 중*으로 변경되면 VM이 성공적으로 다시 배포된 것입니다.

<!---HONumber=AcomDC_0309_2016-->