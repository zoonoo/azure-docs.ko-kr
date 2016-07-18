## Azure 포털 사용

1. 다시 배포하려는 VM을 선택하고 '설정' 블레이드에서 '다시 배포' 단추를 클릭합니다.

	![Azure VM 블레이드](./media/virtual-machines-common-redeploy-to-new-node/vmoverview.png)

2. '다시 배포' 단추를 클릭하여 작업을 확인합니다.

	![VM 다시 배포 블레이드](./media/virtual-machines-common-redeploy-to-new-node/redeployvm.png)

3. VM 다시 배포 준비가 완료되면 VM의 **상태**가 *업데이트 중*으로 변경됩니다.

	![VM 업데이트 중](./media/virtual-machines-common-redeploy-to-new-node/vmupdating.png)

4. 그런 후 새 Azure 호스트에서 VM이 부팅되면 **상태**가 *시작 중*으로 변경됩니다.

	![VM 시작 중](./media/virtual-machines-common-redeploy-to-new-node/vmstarting.png)

5. VM이 부팅 프로세스를 완료하면 **상태**가 *실행 중*으로 돌아가 VM이 정상적으로 다시 배포되었음을 나타냅니다.

	![VM 실행 중](./media/virtual-machines-common-redeploy-to-new-node/vmrunning.png)

<!---HONumber=AcomDC_0706_2016-->