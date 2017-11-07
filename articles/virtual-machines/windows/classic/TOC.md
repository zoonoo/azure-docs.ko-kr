# 개요
## [가상 컴퓨터 정보](../../virtual-machines-windows-about.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
## [디스크 및 VHD](../about-disks-and-vhds.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
## [가상 네트워크](../../../virtual-network/virtual-networks-overview.md)
## [FAQ](faq.md)
## [VM, 웹 사이트 및 클라우드 서비스 비교](../../../app-service/choose-web-site-cloud-service-vm.md)
## [컨테이너](../../virtual-machines-windows-containers.md)

# 시작
## [Azure Portal을 사용하여 VM 만들기](tutorial.md)
## [VM에 로그온](connect-logon.md)
## [Azure PowerShell 설치](/powershell/azure/overview)
## [Azure CLI 설치](../../../cli-install-nodejs.md)

# 방법

## 저장소 사용
### [데이터 디스크 연결](attach-disk.md)
### [데이터 디스크 분리](detach-disk.md)
### [D:를 데이터 디스크로 사용](../../virtual-machines-windows-change-drive-letter.md)

## 네트워크
### [끝점을 설정하는 방법](setup-endpoints.md)
### [VM을 VNet 또는 클라우드 서비스와 연결](connect-vms.md)
### [리소스 관리자 VNet에 클래식 VNet 연결](../../../vpn-gateway/vpn-gateway-connect-different-deployment-models-powershell.md)
### [부하 분산 장치 만들기](../../../load-balancer/load-balancer-get-started-internet-classic-portal.md)
### [Azure PowerShell을 사용하여 NSG 관리](../../../virtual-network/virtual-networks-create-nsg-classic-ps.md)

## 배포
### [사용자 지정 VM 만들기](createportal.md)
### [Azure PowerShell을 사용하여 VM 만들기 및 구성](create-powershell.md)
### [Windows VM 캡처](capture-image.md)
### [PowerShell을 사용하여 VHD 만들기 및 업로드](createupload-vhd.md)
### [Chef를 사용하여 Azure VM 배포 자동화](../../virtual-machines-windows-chef-automation.md)
### [Visual Studio에서 VM 만들기 및 관리](manage-visual-studio.md)
### [Visual Studio를 사용하여 웹앱에 대한 VM 만들기](web-app-visual-studio.md)
### [Java로 계산 집약적인 작업 실행](java-run-compute-intensive-task.md)
### [Django Hello World 웹 응용 프로그램](python-django-web-app.md)

## 구성
### [암호 또는 원격 데스크톱 서비스 다시 설정](../../virtual-machines-windows-reset-rdp.md)
### [Symantec Endpoint Protection 설치 및 구성](install-symantec.md)
### [Trend Micro Deep Security를 서비스로 설치 및 구성](install-trend.md)
### [가용성 집합 구성](configure-availability.md)
### [클래식 배포 모델에서 만든 Windows VM 크기 조정](resize-vm.md)
### [유지 관리](planned-maintenance-schedule.md)

## 관리
### [클래식에서 Resource Manager로 마이그레이션](../../virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
### [Azure PowerShell을 사용하여 VM 관리](manage-psh.md)
### [VM 에이전트 및 확장 정보](agents-and-extensions.md)
### [VM 확장 관리](manage-extensions.md)
### [VM용 사용자 지정 스크립트 확장](extensions-customscript.md)
### [Azure VM에 사용자 지정 데이터 삽입](inject-custom-data.md)

## 계획
### [이미지 정보](about-images.md)
### [VM의 크기](../../virtual-machines-windows-sizes.md)
### [Azure VM에 대한 계획된 유지 관리](../../virtual-machines-windows-planned-maintenance.md)
### [Azure 인프라 서비스 구현 지침](../../virtual-machines-windows-infrastructure-subscription-accounts-guidelines.md)

## 워크로드 관리
### [HPC(고성능 컴퓨팅)](../../virtual-machines-windows-hpcpack-cluster-options.md)
#### [리소스 자동 확장](hpcpack-cluster-node-autogrowshrink.md)
#### [계산 노드 관리](hpcpack-cluster-node-manage.md)
#### [클러스터 만들기](hpcpack-cluster-powershell-script.md)
#### [MPI 응용 프로그램을 실행하도록 클러스터 설정](hpcpack-rdma-cluster.md)
#### [Excel 및 SOA 작업 실행](../../virtual-machines-windows-excel-cluster-hpcpack.md)
#### [마켓플레이스 이미지로 헤드 노드 만들기](../../virtual-machines-windows-hpcpack-cluster-headnode.md)
#### [온-프레미스에서 Azure로 작업 제출](../../virtual-machines-windows-hpcpack-cluster-submit-jobs.md)
### [MongoDB](install-mongodb.md)
### [MySQL](mysql-2008r2.md)
### [Oracle](../../workloads/oracle/oracle-considerations.md)
### [SAP](sap-get-started.md)
### [SQL Server](../sql/virtual-machines-windows-sql-server-iaas-overview.md)
### [Tomcat](java-run-tomcat-app-server.md)

## 문제 해결
### [원격 데스크톱 연결](../../virtual-machines-windows-troubleshoot-rdp-connection.md)
####[원격 데스크톱 연결 문제의 자세한 문제 해결 단계](../../virtual-machines-windows-detailed-troubleshoot-rdp.md)
### [응용 프로그램에 액세스](../../virtual-machines-windows-troubleshoot-app-connection.md)
### [새 VM 생성 관련 클래식 배포 문제](troubleshoot-deployment-new-vm.md)
### [기존 VM 재시작 또는 크기 조정 관련 클래식 배포 문제](virtual-machines-windows-classic-restart-resize-error-troubleshooting.md)
### [RDP 암호 재설정](reset-rdp.md)
### [VM 문제 해결에 가상 하드 디스크 연결](troubleshoot-recovery-disks-portal.md)

# 참조
## [Azure PowerShell](/powershell/azure/overview)
## [Azure CLI](/cli/azure/vm)
## [Java](/java/api)
## [.NET](/dotnet/api/microsoft.azure.management.compute)
## [Resource Manager 템플릿 작성](../../../resource-group-authoring-templates.md)
## [커뮤니티 템플릿](https://azure.microsoft.com/documentation/templates)
## [계산 REST](https://msdn.microsoft.com/library/jj157206.aspx)
## [네트워크 REST](https://msdn.microsoft.com/library/jj157182.aspx)
## [저장소 REST](https://msdn.microsoft.com/library/ee460790.aspx)

# 리소스
## [Azure 로드맵](https://azure.microsoft.com/roadmap/?category=compute)
## [가격](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows)
## [요금 계산기](https://azure.microsoft.com/pricing/calculator/)
## [국가별 가용성](https://azure.microsoft.com/regions/services/)
## [스택 오버플로](http://stackoverflow.com/questions/tagged/azure-virtual-machine)
## [비디오](https://azure.microsoft.com/documentation/videos/index/?services=virtual-machines)
