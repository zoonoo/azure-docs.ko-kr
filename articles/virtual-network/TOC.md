# 개요
## [가상 네트워크](virtual-networks-overview.md)
## [사용자 정의 경로 및 IP 전달](virtual-networks-udr-overview.md)
## [가상 네트워크 피어링](virtual-network-peering-overview.md)
## [비즈니스 연속성](virtual-network-disaster-recovery-guidance.md)
## [FAQ](virtual-networks-faq.md)
## [IP 주소 지정](virtual-network-ip-addresses-overview-arm.md)
## 클래식
### [IP 주소 지정](virtual-network-ip-addresses-overview-classic.md)
### [액세스 제어 목록](virtual-networks-acl.md)

# 시작
## [첫 가상 네트워크 만들기](virtual-network-get-started-vnet-subnet.md)

# 방법
## 계획 및 디자인
### [가상 네트워크](virtual-network-vnet-plan-design-arm.md)
### [네트워크 보안 그룹](virtual-networks-nsg.md)

## 배포
### [가상 네트워크](virtual-networks-create-vnet-arm-pportal.md)
#### [PowerShell](virtual-networks-create-vnet-arm-ps.md)
#### [CLI](virtual-networks-create-vnet-arm-cli.md)
#### [템플릿](virtual-networks-create-vnet-arm-template-click.md)
#### 클래식
##### [포털](virtual-networks-create-vnet-classic-pportal.md)
##### [PowerShell](virtual-networks-create-vnet-classic-netcfg-ps.md)
##### [CLI](virtual-networks-create-vnet-classic-cli.md)

### 네트워크 보안 그룹
#### [포털](virtual-networks-create-nsg-arm-pportal.md)
#### [PowerShell](virtual-networks-create-nsg-arm-ps.md)
#### [CLI](virtual-networks-create-nsg-arm-cli.md)
#### [템플릿](virtual-networks-create-nsg-arm-template.md)
#### 클래식
##### [PowerShell](virtual-networks-create-nsg-classic-ps.md)
##### [CLI](virtual-networks-create-nsg-classic-cli.md)

### 사용자 정의 경로
#### [PowerShell](virtual-network-create-udr-arm-ps.md)
#### [CLI](virtual-network-create-udr-arm-cli.md)
#### [템플릿](virtual-network-create-udr-arm-template.md)
#### 클래식
##### [PowerShell](virtual-network-create-udr-classic-ps.md)
##### [CLI](virtual-network-create-udr-classic-cli.md)

### 가상 네트워크 피어링
#### [동일한 배포 모델 - 동일한 구독](virtual-network-create-peering.md)
#### [동일한 배포 모델 - 다른 구독](create-peering-different-subscriptions.md)
#### [다른 배포 모델 - 동일한 구독](create-peering-different-deployment-models.md)
#### [다른 배포 모델 - 다른 구독](create-peering-different-deployment-models-subscriptions.md)

### 가상 컴퓨터
#### 정적 공용 IP 주소를 사용하는 VM 만들기
##### [포털](virtual-network-deploy-static-pip-arm-portal.md)
##### [PowerShell](virtual-network-deploy-static-pip-arm-ps.md)
##### [CLI](virtual-network-deploy-static-pip-arm-cli.md)
##### [템플릿](virtual-network-deploy-static-pip-arm-template.md)
##### 클래식
###### [PowerShell](virtual-networks-reserved-public-ip.md)

#### 고정 개인 IP 주소를 사용하는 VM 만들기
##### [포털](virtual-networks-static-private-ip-arm-pportal.md)
##### [PowerShell](virtual-networks-static-private-ip-arm-ps.md)
##### [CLI](virtual-networks-static-private-ip-arm-cli.md)
##### 클래식
###### [포털](virtual-networks-static-private-ip-classic-pportal.md)
###### [PowerShell](virtual-networks-static-private-ip-classic-ps.md)
###### [CLI](virtual-networks-static-private-ip-classic-cli.md)

#### 여러 네트워크 인터페이스를 사용하는 VM 만들기
##### [PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
##### [CLI](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
##### 클래식
###### [PowerShell](virtual-network-deploy-multinic-classic-ps.md)
###### [CLI](virtual-network-deploy-multinic-classic-cli.md)

#### 여러 IP 주소를 사용하여 VM 만들기
##### [Azure 포털](virtual-network-multiple-ip-addresses-portal.md)
##### [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)
##### [CLI](virtual-network-multiple-ip-addresses-cli.md)
##### [템플릿](virtual-network-multiple-ip-addresses-template.md)

#### [가속 네트워킹을 사용하여 VM 만들기](virtual-network-create-vm-accelerated-networking.md)

### 연결 시나리오
#### [가상 네트워크(VNet)에서 VNet으로](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
#### [VNet(Resource Manager)에서 VNet(클래식)으로](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
#### [VNet에서 온-프레미스 네트워크(VPN)로](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
#### [VNet에서 온-프레미스 네트워크(ExpressRoute)로](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
#### [고가용성 하이브리드 네트워크 아키텍처](../guidance/guidance-hybrid-network-expressroute-vpn-failover.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

### 보안 시나리오
#### [가상 어플라이언스를 사용하여 네트워크 보안](virtual-network-scenario-udr-gw-nva.md)
#### [Azure와 인터넷 간의 DMZ](../guidance/guidance-iaas-ra-secure-vnet-dmz.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
#### [클라우드 서비스 및 네트워크 보안](../best-practices-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
##### [NSG와 간단한 DMZ](virtual-networks-dmz-nsg-asm.md)
##### [방화벽 및 NSG와 DMZ](virtual-networks-dmz-nsg-fw-asm.md)
##### [방화벽, UDR 및 NSG와 DMZ](virtual-networks-dmz-nsg-fw-udr-asm.md)
##### [샘플 응용 프로그램](virtual-networks-sample-app.md)

## 구성
### 가상 컴퓨터
#### [네트워크 인터페이스 추가 또는 제거](virtual-network-network-interface-vm.md)
#### [VM 및 클라우드 서비스의 이름 확인](virtual-networks-name-resolution-for-vms-and-role-instances.md)
#### [네트워크 처리량 최적화](virtual-network-optimize-network-bandwidth.md)
#### [호스트 이름 보기 및 수정](virtual-networks-viewing-and-modifying-hostnames.md)
### 클래식
#### 액세스 제어 목록
##### [포털](../virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
##### [PowerShell](virtual-networks-acl-powershell.md)

## 관리
### [가상 네트워크](virtual-network-manage-network.md)
#### [서브넷](virtual-network-manage-subnet.md)
#### [피어링](virtual-network-manage-peering.md)
#### 클래식
##### [네트워크 구성 파일](virtual-networks-using-network-configuration-file.md)
##### [선호도 그룹에서 지역으로 마이그레이션](virtual-networks-migrate-to-regional-vnet.md)
### 네트워크 보안 그룹
#### [포털](virtual-network-manage-nsg-arm-portal.md)
#### [PowerShell](virtual-network-manage-nsg-arm-ps.md)
#### [CLI](virtual-network-manage-nsg-arm-cli.md)
#### [로그](virtual-network-nsg-manage-log.md)
### NIC(네트워크 인터페이스)
#### [NIC 만들기, 변경 또는 삭제](virtual-network-network-interface.md)
#### [IP 주소 추가, 변경 또는 제거](virtual-network-network-interface-addresses.md)
### 가상 컴퓨터
#### [다른 서브넷으로 VM 이동](virtual-networks-move-vm-role-to-subnet.md)
### [공용 IP 주소](virtual-network-public-ip-address.md)

## 문제 해결
### 네트워크 보안 그룹
#### [포털](virtual-network-nsg-troubleshoot-portal.md)
#### [PowerShell](virtual-network-nsg-troubleshoot-powershell.md)
### 경로
#### [포털](virtual-network-routes-troubleshoot-portal.md)
#### [PowerShell](virtual-network-routes-troubleshoot-powershell.md)
### [처리량 테스트](virtual-network-bandwidth-testing.md)
### [가상 네트워크를 삭제할 수 없음](virtual-network-troubleshoot-cannot-delete-vnet.md)

# 참조
## [PowerShell(리소스 관리자)](/powershell/module/azurerm.network)
## [PowerShell(클래식)](/powershell/module/azure/)
## [Azure CLI](/cli/azure/network)
## [Java](/java/api/)
## [REST(리소스 관리자)](https://msdn.microsoft.com/library/mt163658.aspx)
## [REST(클래식)](https://msdn.microsoft.com/library/jj157182.aspx)


# 관련 항목
## [가상 컴퓨터](/azure/virtual-machines/)
## [응용 프로그램 게이트웨이](/azure/application-gateway/)
## [Azure DNS](/azure/dns/)
## [트래픽 관리자](/azure/traffic-manager/)
## [부하 분산 장치](/azure/load-balancer/)
## [VPN Gateway](/azure/vpn-gateway/)
## [ExpressRoute](/azure/expressroute/)

# 리소스
## [Azure 로드맵](https://azure.microsoft.com/roadmap/)
## [네트워킹 블로그](http://azure.microsoft.com/blog/topics/networking)
## [네트워킹 포럼](https://social.msdn.microsoft.com/Forums/azure/home?forum=WAVirtualMachinesVirtualNetwork)
## [가격 책정](https://azure.microsoft.com/pricing/details/virtual-network)
## [스택 오버플로](http://stackoverflow.com/questions/tagged/azure-virtual-network)
