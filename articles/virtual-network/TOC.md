# [Virtual Network 설명서](index.md)

# 개요
## [가상 네트워크](virtual-networks-overview.md)
## [라우팅](virtual-networks-udr-overview.md)
## [가상 네트워크 피어링](virtual-network-peering-overview.md)
## [가상 네트워크 서비스 끝점](virtual-network-service-endpoints-overview.md)
## [Azure 서비스에 대한 가상 네트워크](virtual-network-for-azure-services.md)
## [보안](security-overview.md)
## [컨테이너 네트워킹](container-networking.md)
## [비즈니스 연속성](virtual-network-disaster-recovery-guidance.md)
## [IP 주소 지정](virtual-network-ip-addresses-overview-arm.md)
## [DDoS Protection](ddos-protection-overview.md)
## [FAQ](virtual-networks-faq.md)
## 클래식
### [IP 주소 지정](virtual-network-ip-addresses-overview-classic.md)
### [액세스 제어 목록](virtual-networks-acl.md)

# 시작
## [가상 네트워크 만들기 - 포털](quick-create-portal.md)
## [가상 네트워크 만들기 - PowerShell](quick-create-powershell.md)
## [가상 네트워크 만들기 - Azure CLI](quick-create-cli.md)

# 방법
## 계획 및 디자인
### [가상 네트워크](virtual-network-vnet-plan-design-arm.md)
### [네트워크 보안 그룹](virtual-networks-nsg.md)

## 배포

### 네트워크 보안 그룹
#### [Azure PowerShell](tutorial-filter-network-traffic.md)
#### [Azure CLI](tutorial-filter-network-traffic-cli.md)
#### 응용 프로그램 보안 그룹 없음
##### [Azure Portal](virtual-networks-create-nsg-arm-pportal.md)
##### [템플릿](virtual-networks-create-nsg-arm-template.md)
#### 클래식
##### [Azure PowerShell](virtual-networks-create-nsg-classic-ps.md)
##### [Azure CLI 1.0](virtual-networks-create-nsg-classic-cli.md)

### 경로 테이블
#### [Azure Portal](tutorial-create-route-table-portal.md)
#### [Azure PowerShell](tutorial-create-route-table-powershell.md)
#### [Azure CLI](tutorial-create-route-table-cli.md)
#### [템플릿](virtual-network-create-udr-arm-template.md)
#### 클래식
##### [Azure PowerShell](virtual-network-create-udr-classic-ps.md)
##### [Azure CLI](virtual-network-create-udr-classic-cli.md)

### 가상 네트워크 피어링
#### 동일한 배포 모델 - 동일한 구독
##### [Azure Portal](tutorial-connect-virtual-networks-portal.md)
##### [Azure PowerShell](tutorial-connect-virtual-networks-powershell.md)
##### [Azure CLI](tutorial-connect-virtual-networks-cli.md)
#### [동일한 배포 모델 - 다른 구독](create-peering-different-subscriptions.md)
#### [다른 배포 모델 - 동일한 구독](create-peering-different-deployment-models.md)
#### [다른 배포 모델 - 다른 구독](create-peering-different-deployment-models-subscriptions.md)

### 가상 네트워크 서비스 엔드포인트
#### [Azure Portal](tutorial-restrict-network-access-to-resources.md)
#### [Azure PowerShell](tutorial-restrict-network-access-to-resources-powershell.md)
#### [Azure CLI](tutorial-restrict-network-access-to-resources-cli.md)

### 가상 머신
#### [가상 머신 네트워크 처리량](virtual-machine-network-throughput.md)
#### 정적 공용 IP 주소를 사용하는 VM 만들기
##### [Azure Portal](virtual-network-deploy-static-pip-arm-portal.md)
##### [Azure PowerShell](virtual-network-deploy-static-pip-arm-ps.md)
##### [Azure CLI](virtual-network-deploy-static-pip-arm-cli.md)
##### [템플릿](virtual-network-deploy-static-pip-arm-template.md)
##### 클래식
###### [Azure PowerShell](virtual-networks-reserved-public-ip.md)

#### VM 만들기 - 고정 개인 IP 주소
##### [Azure Portal](virtual-networks-static-private-ip-arm-pportal.md)
##### [Azure PowerShell](virtual-networks-static-private-ip-arm-ps.md)
##### [Azure CLI](virtual-networks-static-private-ip-arm-cli.md)
##### 클래식
###### [Azure Portal](virtual-networks-static-private-ip-classic-pportal.md)
###### [Azure PowerShell](virtual-networks-static-private-ip-classic-ps.md)
###### [Azure CLI](virtual-networks-static-private-ip-classic-cli.md)

#### VM 만들기 - 여러 네트워크 인터페이스
##### [Azure PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
##### [Azure CLI](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
##### [템플릿](virtual-network-deploy-multinic-arm-template.md)

##### 클래식
###### [Azure PowerShell](virtual-network-deploy-multinic-classic-ps.md)
###### [Azure CLI](virtual-network-deploy-multinic-classic-cli.md)

#### VM 만들기 - 여러 IP 주소
##### [Azure Portal](virtual-network-multiple-ip-addresses-portal.md)
##### [Azure PowerShell](virtual-network-multiple-ip-addresses-powershell.md)
##### [Azure CLI](virtual-network-multiple-ip-addresses-cli.md)
##### [템플릿](virtual-network-multiple-ip-addresses-template.md)

#### VM 만들기 - 가속 네트워킹
##### [Azure PowerShell](create-vm-accelerated-networking-powershell.md)
##### [Azure CLI](create-vm-accelerated-networking-cli.md)

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
##### [NSG와 DMZ 만들기](virtual-networks-dmz-nsg.md)
##### [NSG와 DMZ 만들기(클래식)](virtual-networks-dmz-nsg-asm.md)
##### [방화벽 및 NSG와 DMZ 만들기(클래식)](virtual-networks-dmz-nsg-fw-asm.md)
##### [방화벽, UDR 및 NSG와 DMZ(클래식)](virtual-networks-dmz-nsg-fw-udr-asm.md)

##### [샘플 응용 프로그램](virtual-networks-sample-app.md)

### 클래식
#### [가상 네트워크](create-virtual-network-classic.md)
##### [Azure Portal](virtual-networks-create-vnet-classic-pportal.md)
##### [Azure PowerShell](virtual-networks-create-vnet-classic-netcfg-ps.md)
##### [Azure CLI](virtual-networks-create-vnet-classic-cli.md)
#### [가상 네트워크 구성 파일에서 DNS 설정 지정](virtual-networks-specifying-a-dns-settings-in-a-virtual-network-configuration-file.md)
#### [서비스 구성 파일에서 DNS 설정 지정](virtual-networks-specifying-dns-settings-in-a-service-configuration-file.md)

## 구성
### 가상 머신
#### [네트워크 인터페이스 추가 또는 제거](virtual-network-network-interface-vm.md)
#### [VM 및 클라우드 서비스의 이름 확인](virtual-networks-name-resolution-for-vms-and-role-instances.md)
#### [동적 DNS를 사용하여 자체 DNS 서버에 호스트 이름 등록](virtual-networks-name-resolution-ddns.md)
#### [네트워크 처리량 최적화](virtual-network-optimize-network-bandwidth.md)
#### [호스트 이름 보기 및 수정](virtual-networks-viewing-and-modifying-hostnames.md)
#### 클래식
##### 고정 IP 주소
###### [PowerShell](virtual-networks-reserved-private-ip.md)
###### [CLI](virtual-networks-static-private-ip-classic-cli.md)
##### [인스턴스 수준 공용 IP 주소](virtual-networks-instance-level-public-ip.md)

### 클래식
#### 액세스 제어 목록
##### [Azure Portal](../virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
##### [Azure PowerShell](virtual-networks-acl-powershell.md)

## 관리
### [가상 네트워크](manage-virtual-network.md)
#### [서브넷](virtual-network-manage-subnet.md)
#### [피어링](virtual-network-manage-peering.md)
#### 클래식
##### [네트워크 구성 파일](virtual-networks-using-network-configuration-file.md)
##### [선호도 그룹에서 지역으로 마이그레이션](virtual-networks-migrate-to-regional-vnet.md)
### 네트워크 보안 그룹
#### [Azure Portal](virtual-network-manage-nsg-arm-portal.md)
#### [Azure PowerShell](virtual-network-manage-nsg-arm-ps.md)
#### [Azure CLI](virtual-network-manage-nsg-arm-cli.md)

#### [로그](virtual-network-nsg-manage-log.md)
### [경로 테이블](manage-route-table.md)
### NIC(네트워크 인터페이스)
#### [NIC 만들기, 변경 또는 삭제](virtual-network-network-interface.md)
#### [IP 주소 추가, 변경 또는 제거](virtual-network-network-interface-addresses.md)
### 가상 머신
#### [다른 서브넷으로 VM 이동](virtual-networks-move-vm-role-to-subnet.md)
### [공용 IP 주소](virtual-network-public-ip-address.md)
### DDoS Protection
#### [Azure Portal](ddos-protection-manage-portal.md)
#### [Azure PowerShell](ddos-protection-manage-ps.md)

## 문제 해결
### 네트워크 보안 그룹
#### [Azure Portal](virtual-network-nsg-troubleshoot-portal.md)
#### [Azure PowerShell](virtual-network-nsg-troubleshoot-powershell.md)
### 경로
#### [Azure Portal](virtual-network-routes-troubleshoot-portal.md)
#### [Azure PowerShell](virtual-network-routes-troubleshoot-powershell.md)
### [처리량 테스트](virtual-network-bandwidth-testing.md)
### [가상 네트워크를 삭제할 수 없음](virtual-network-troubleshoot-cannot-delete-vnet.md)
### [VM 간 연결 문제](virtual-network-troubleshoot-connectivity-problem-between-vms.md)
### [SMTP 배너 검사를 위한 PTR 구성](create-ptr-for-smtp-service.md)

## 샘플 스크립트
### [Azure CLI](cli-samples.md)
### [Azure PowerShell](powershell-samples.md)

# 참고 자료
## [코드 샘플](https://azure.microsoft.com/resources/samples/?service=virtual-network)
## [Azure PowerShell(Resource Manager)](/powershell/module/azurerm.network)
## [Azure PowerShell(클래식)](/powershell/module/azure/)
## [Azure CLI](/cli/azure/network)
## [Java](/java/api/)
## [REST(리소스 관리자)](https://msdn.microsoft.com/library/mt163658.aspx)
## [REST(클래식)](https://msdn.microsoft.com/library/jj157182.aspx)


# 관련 항목
## [Virtual Machines](/azure/virtual-machines/)
## [Application Gateway](/azure/application-gateway/)
## [Azure DNS](/azure/dns/)
## [Traffic Manager](/azure/traffic-manager/)
## [Load Balancer](/azure/load-balancer/)
## [VPN Gateway](/azure/vpn-gateway/)
## [ExpressRoute](/azure/expressroute/)

# 리소스
## [Azure 로드맵](https://azure.microsoft.com/roadmap/?category=networking)
## [네트워킹 블로그](http://azure.microsoft.com/blog/topics/networking)
## [네트워킹 포럼](https://social.msdn.microsoft.com/Forums/azure/home?forum=WAVirtualMachinesVirtualNetwork)
## [가격 책정](https://azure.microsoft.com/pricing/details/virtual-network)
## [요금 계산기](https://azure.microsoft.com/pricing/calculator/)
## [스택 오버플로](http://stackoverflow.com/questions/tagged/azure-virtual-network)
## [네트워크 리소스 공급자](resource-groups-networking.md)
