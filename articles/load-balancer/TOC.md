# [Load Balancer 설명서](index.md)

# 개요
## [부하 분산 장치란?](load-balancer-overview.md)
## [Load Balancer Standard란?](load-balancer-standard-overview.md)
## [Load Balancer 프로브](load-balancer-custom-probe-overview.md)
## [고가용성 포트](load-balancer-ha-ports-overview.md)
## [IPv6 지원](load-balancer-ipv6-overview.md)
## [여러 프런트 엔드](load-balancer-multivip-overview.md)
## [아웃바운드 연결](load-balancer-outbound-connections.md)
## [표준 부하 분산 장치 및 가용성 영역](load-balancer-standard-availability-zones.md)
## [표준 Load Balancer 메트릭 및 진단](load-balancer-standard-diagnostics.md)

# 시작
## [기본 Load Balancer 만들기](load-balancer-get-started-internet-portal.md)
### [기본 Load Balancer 만들기(CLI)](load-balancer-get-started-internet-arm-cli.md)
### [기본 Load Balancer 만들기(PowerShell)](load-balancer-get-started-internet-arm-ps.md)
## [표준 Load Balancer 만들기](load-balancer-standard-public-portal.md)
### [표준 Load Balancer 만들기(CLI)](load-balancer-standard-public-cli.md)

# 방법

## [영역 중복 공용 표준 부하 분산 장치 만들기](load-balancer-get-started-internet-az-portal.md)
### [영역 중복 공용 표준 부하 분산 장치 만들기(PowerShell)](load-balancer-get-started-internet-az-powershell.md)
### [영역 중복 공용 표준 부하 분산 장치 만들기(CLI)](load-balancer-get-started-internet-az-cli.md)
## [영역 공용 표준 부하 분산 장치 만들기](load-balancer-get-started-internet-availability-zones-zonal-portal.md)
### [영역 공용 표준 부하 분산 장치 만들기(PowerShell)](load-balancer-get-started-internet-availability-zones-zonal-powershell.md)
### [영역 중복 공용 표준 부하 분산 장치 만들기(CLI)](load-balancer-get-started-internet-availability-zones-zonal-cli.md)
## [가용성 영역에 VM 부하 분산](load-balancer-standard-public-availability-zones-portal.md)
###  [가용성 영역에 VM 부하 분산(CLI)](load-balancer-standard-public-zone-redundant-cli.md)
##  [영역 내에 VM 부하 분산(CLI)](load-balancer-standard-public-zonal-cli.md)   
## [기본 Load Balancer 만들기(템플릿)](load-balancer-get-started-internet-arm-template.md)
## [IPv6를 사용하는 공용 부하 분산 장치 만들기](load-balancer-ipv6-internet-ps.md)
### [IPv6를 사용하는 공용 부하 분산 장치 만들기(CLI)](load-balancer-ipv6-internet-cli.md)
### [IPv6를 사용하는 공용 부하 분산 장치 만들기(템플릿)](load-balancer-ipv6-internet-template.md)
## [내부 부하 분산 장치 구성](load-balancer-get-started-ilb-arm-portal.md)
### [내부 부하 분산 장치 구성(PowerShell)](load-balancer-get-started-ilb-arm-ps.md)
### [내부 부하 분산 장치 구성(CLI)](load-balancer-get-started-ilb-arm-cli.md)
### [내부 부하 분산 장치 구성(템플릿)](load-balancer-get-started-ilb-arm-template.md)
## [부하 분산 장치의 TCP 유휴 시간 제한 구성](load-balancer-tcp-idle-timeout.md)
## [Load Balancer의 배포 모드 구성](load-balancer-distribution-mode.md)
## [부하 분산 서비스 조합](../traffic-manager/traffic-manager-load-balancing-azure.md?toc=%2fazure%2fload-balancer%2ftoc.json)
## [여러 IP 구성 사용](load-balancer-multiple-ip.md)
### [여러 IP 구성 사용(CLI)](load-balancer-multiple-ip-cli.md)
### [여러 IP 구성 사용(PowerShell)](load-balancer-multiple-ip-powershell.md)
## [Azure Load Balancer에 대한 Log analytics](load-balancer-monitor-log.md)
## [내부 부하 분산 장치에 대해 고가용성 포트 구성](load-balancer-configure-ha-ports.md)

## 문제 해결
### [Azure Load Balancer 문제 해결](load-balancer-troubleshoot.md)

## 클래식 배포 모델 문서
### [아웃바운드 연결(클래식)](load-balancer-outbound-connections-classic.md)
## [클라우드 서비스의 여러 VIP 구성](load-balancer-multivip.md)
### [Cloud Services에 대한 내부 부하 분산 장치 구성](load-balancer-get-started-ilb-classic-cloud.md)
#### [Cloud Services에 대한 내부 부하 분산 장치 구성(PowerShell)](load-balancer-get-started-ilb-classic-ps.md)
#### [Cloud Services에 대한 내부 부하 분산 장치 구성(CLI)](load-balancer-get-started-ilb-classic-cli.md)
### [공용 부하 분산 장치 구성(클래식 PowerShell)](load-balancer-get-started-internet-classic-ps.md)
#### [공용 부하 분산 장치 구성(클래식 클라우드)](load-balancer-get-started-internet-classic-cloud.md)
#### [공용 부하 분산 장치 구성(클래식 CLI)](load-balancer-get-started-internet-classic-cli.md)

# 참고 자료
## [코드 샘플](https://azure.microsoft.com/en-us/resources/samples/?service=load-balancer)
## [Azure PowerShell](/powershell/module/azurerm.network)
## [Azure Azure CLI](/cli/azure/network/lb)
## [.NET](/dotnet/api/microsoft.azure.management.network.models)
## [Java](/java/api/com.microsoft.azure.management.network)
## [Node.js](http://azure.github.io/azure-sdk-for-node/azure-arm-network/latest/LoadBalancers.html)
## [Ruby](http://www.rubydoc.info/gems/azure_mgmt_network/Azure/ARM/Network/LoadBalancers)
## [Python](http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.mgmt.network.operations.html#azure.mgmt.network.operations.LoadBalancersOperations)
## [REST](https://msdn.microsoft.com/library/azure/mt163651.aspx)

# 관련 항목
## [Application Gateway](/azure/application-gateway/)
## [Express Route](/azure/expressroute/)
## [Virtual Network](/azure/virtual-network/)
## [VPN Gateway](/azure/vpn-gateway/)
## [Virtual Machine](/azure/virtual-machines/)
## [Traffic Manager](/azure/traffic-manager/)
## [DNS](/azure/dns/)

# 리소스
## [Azure 로드맵](https://azure.microsoft.com/roadmap/?category=networking)
## [가격](https://azure.microsoft.com/pricing/details/load-balancer/)
## [요금 계산기](https://azure.microsoft.com/pricing/calculator/)
## [서비스 업데이트](https://azure.microsoft.com/updates/?product=load-balancer)
