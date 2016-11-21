# 개요
## [가상 네트워크](virtual-networks-overview.md)
## [사용자 정의 경로 및 IP 전달](virtual-networks-udr-overview.md)
## [가상 네트워크 피어링](virtual-network-peering-overview.md)
## [비즈니스 연속성](virtual-network-disaster-recovery-guidance.md)
## [FAQ](virtual-networks-faq.md)
## IP 주소
### [리소스 관리자](virtual-network-ip-addresses-overview-arm.md)
### [클래식](virtual-network-ip-addresses-overview-classic.md)
## 가상 컴퓨터
### [네트워크 인터페이스](virtual-network-network-interface-overview.md)
### [이름 확인](virtual-networks-name-resolution-for-vms-and-role-instances.md)

# 시작
## [가상 네트워크 만들기](virtual-networks-create-vnet-arm-pportal.md)
## [가상 네트워크에 VM 배포](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

# 방법
## 계획 및 디자인
### [가상 네트워크](virtual-network-vnet-plan-design-arm.md)
### [네트워크 보안 그룹](virtual-networks-nsg.md)

## 배포
### 가상 네트워크
#### [포털](virtual-networks-create-vnet-arm-pportal.md)
#### [PowerShell](virtual-networks-create-vnet-arm-ps.md)
#### [CLI](virtual-networks-create-vnet-arm-cli.md)
#### [템플릿](virtual-networks-create-vnet-arm-template-click.md)
#### [포털(클래식)](virtual-networks-create-vnet-classic-pportal.md)
#### [PowerShell(클래식)](virtual-networks-create-vnet-classic-netcfg-ps.md)
#### [CLI(클래식)](virtual-networks-create-vnet-classic-cli.md)

### 네트워크 보안 그룹
#### [포털](virtual-networks-create-nsg-arm-pportal.md)
#### [PowerShell](virtual-networks-create-nsg-arm-ps.md)
#### [CLI](virtual-networks-create-nsg-arm-cli.md)
#### [템플릿](virtual-networks-create-nsg-arm-template.md)
#### [PowerShell(클래식)](virtual-networks-create-nsg-classic-ps.md)
#### [CLI(클래식)](virtual-networks-create-nsg-classic-cli.md)

### 사용자 정의 경로
#### [PowerShell](virtual-network-create-udr-arm-ps.md)
#### [CLI](virtual-network-create-udr-arm-cli.md)
#### [템플릿](virtual-network-create-udr-arm-template.md)
#### [PowerShell(클래식)](virtual-network-create-udr-classic-ps.md)
#### [CLI(클래식)](virtual-network-create-udr-classic-cli.md)

### 가상 네트워크 피어링
#### [포털](virtual-networks-create-vnetpeering-arm-portal.md)
#### [PowerShell](virtual-networks-create-vnetpeering-arm-ps.md)
#### [템플릿](virtual-networks-create-vnetpeering-arm-template-click.md)

### 가상 컴퓨터

#### 고정 공용 IP 주소
##### [포털](virtual-network-deploy-static-pip-arm-portal.md)
##### [PowerShell](virtual-network-deploy-static-pip-arm-ps.md)
##### [CLI](virtual-network-deploy-static-pip-arm-cli.md)
##### [템플릿](virtual-network-deploy-static-pip-arm-template.md)
##### [PowerShell(클래식)](virtual-networks-reserved-public-ip.md)

#### 고정 개인 IP 주소
##### [포털](virtual-networks-static-private-ip-arm-pportal.md)
##### [PowerShell](virtual-networks-static-private-ip-arm-ps.md)
##### [CLI](virtual-networks-static-private-ip-arm-cli.md)
##### [포털(클래식)](virtual-networks-static-private-ip-classic-pportal.md)
##### [PowerShell(클래식)](virtual-networks-static-private-ip-classic-ps.md)
##### [CLI(클래식)](virtual-networks-static-private-ip-classic-cli.md)

#### 여러 네트워크 인터페이스
##### [PowerShell](virtual-network-deploy-multinic-arm-ps.md)
##### [CLI](virtual-network-deploy-multinic-arm-cli.md)
##### [템플릿](virtual-network-deploy-multinic-arm-template.md)
##### [PowerShell(클래식)](virtual-network-deploy-multinic-classic-ps.md)
##### [CLI(클래식)](virtual-network-deploy-multinic-classic-cli.md)

#### 여러 IP 주소
##### [Azure 포털](virtual-network-multiple-ip-addresses-portal.md)
##### [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)

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
### 가속된 네트워킹
#### [Azure 포털](virtual-network-accelerated-networking-portal.md)
#### [PowerShell](virtual-network-accelerated-networking-powershell.md)
### 액세스 제어 목록
#### [클래식 포털](virtual-networks-acl.md)
#### [PowerShell](virtual-networks-acl-powershell.md)

## 관리
### 네트워크 보안 그룹
#### [포털](virtual-network-manage-nsg-arm-portal.md)
#### [PowerShell](virtual-network-manage-nsg-arm-ps.md)
#### [CLI](virtual-network-manage-nsg-arm-cli.md)
#### [로그](virtual-network-nsg-manage-log.md)
#### 문제 해결
##### [포털](virtual-network-nsg-troubleshoot-portal.md)
##### [PowerShell](virtual-network-nsg-troubleshoot-powershell.md)
### 문제 해결 경로
#### [포털](virtual-network-routes-troubleshoot-portal.md)
#### [PowerShell](virtual-network-routes-troubleshoot-powershell.md)
### 가상 컴퓨터
#### [호스트 이름 보기 및 수정](virtual-networks-viewing-and-modifying-hostnames.md)
#### [다른 서브넷으로 VM 이동](virtual-networks-move-vm-role-to-subnet.md)

# 참조
## [PowerShell(리소스 관리자)](https://msdn.microsoft.com/library/mt163510(v=azure.300))
## [PowerShell(클래식)](https://msdn.microsoft.com/library/mt270335(v=azure.300))
## [Azure CLI](/cli/azure/)
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
## [네트워킹 블로그](http://azure.microsoft.com/blog/topics/networking)
## [네트워킹 포럼](https://social.msdn.microsoft.com/Forums/azure/home?forum=WAVirtualMachinesVirtualNetwork)
## [가격 책정](https://azure.microsoft.com/pricing/details/virtual-network)
## [스택 오버플로](http://stackoverflow.com/questions/tagged/azure-virtual-network)


<!--HONumber=Nov16_HO3-->


