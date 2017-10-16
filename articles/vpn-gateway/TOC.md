# 개요
## [VPN Gateway 정보](vpn-gateway-about-vpngateways.md)
## [VPN 게이트웨이 FAQ](vpn-gateway-vpn-faq.md)
## [구독 및 서비스 한도](../azure-subscription-service-limits.md?toc=%2fazure%2fvpn-gateway%2ftoc.json)

# 시작
## [VPN Gateway 계획 및 설계](vpn-gateway-plan-design.md)
## [VPN Gateway 설정 정보](vpn-gateway-about-vpn-gateway-settings.md)
## [VPN 장치 정보](vpn-gateway-about-vpn-devices.md)
## [암호화 요구 사항 정보](vpn-gateway-about-compliance-crypto.md)
## [BGP 및 VPN Gateway 정보](vpn-gateway-bgp-overview.md)
## [항상 사용 가능한 연결 정보](vpn-gateway-highlyavailable.md)
## [지점 및 사이트 간 연결 정보](point-to-site-about.md)

# 방법
## 사이트 간 연결 구성
### [Azure 포털](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
### [Azure PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
### [Azure CLI](vpn-gateway-howto-site-to-site-resource-manager-cli.md)
### [Azure Portal(클래식)](vpn-gateway-howto-site-to-site-classic-portal.md)

## 지점 및 사이트 간 연결 구성 - 네이티브 Azure 인증서 인증
### P2S VPN 구성
#### [Azure 포털](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
#### [Azure PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
#### [Azure Portal(클래식)](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
### 자체 서명된 인증서 생성
#### [Azure PowerShell](vpn-gateway-certificates-point-to-site.md)
#### [Makecert](vpn-gateway-certificates-point-to-site-makecert.md)
### [VPN 클라이언트 구성 파일 생성 및 설치](point-to-site-vpn-client-configuration-azure-cert.md)
### [클라이언트 인증서 설치](point-to-site-how-to-vpn-client-install-azure-cert.md)

## 지점 및 사이트 간 연결 구성 - RADIUS 인증
### P2S VPN 구성
#### [Azure PowerShell](point-to-site-how-to-radius-ps.md)
### [VPN 클라이언트 구성 파일 생성 및 설치](point-to-site-vpn-client-configuration-radius.md)

## VNet 간 연결 구성
### [Azure 포털](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
### [Azure PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
### [Azure CLI](vpn-gateway-howto-vnet-vnet-cli.md)
### [Azure 포털(클래식)](vpn-gateway-howto-vnet-vnet-portal-classic.md)
## 배포 모델 간 VNet-VNet 연결 구성
### [Azure 포털](vpn-gateway-connect-different-deployment-models-portal.md)
### [Azure PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
## 사이트 간 및 ExpressRoute 공존 연결 구성
### [Azure PowerShell](../expressroute/expressroute-howto-coexist-resource-manager.md?toc=%2fazure%2fvpn-gateway%2ftoc.json)
## 여러 사이트 간 연결 구성
### [Azure 포털](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
### [Azure PowerShell(클래식)](vpn-gateway-multi-site.md)
## 여러 정책 기반 VPN 장치 연결
### [Azure PowerShell](vpn-gateway-connect-multiple-policybased-rm-ps.md)
## 연결에서 IPsec/IKE 정책 구성
### [Azure PowerShell](vpn-gateway-ipsecikepolicy-rm-powershell.md)
## 항상 사용 가능한 활성-활성 연결 구성
### [Azure PowerShell](vpn-gateway-activeactive-rm-powershell.md)
## VPN Gateway에 BGP 구성
### [Azure PowerShell](vpn-gateway-bgp-resource-manager-ps.md)
### [Azure CLI](bgp-how-to-cli.md)
## 강제 터널링 구성
### [Azure PowerShell](vpn-gateway-forced-tunneling-rm.md)
### [Azure PowerShell(클래식)](vpn-gateway-about-forced-tunneling.md)
## 로컬 네트워크 게이트웨이 설정 수정
### [Azure 포털](vpn-gateway-modify-local-network-gateway-portal.md)
### [Azure PowerShell](vpn-gateway-modify-local-network-gateway.md)
### [Azure CLI](vpn-gateway-modify-local-network-gateway-cli.md)
## [VPN 게이트웨이 연결 확인](vpn-gateway-verify-connection-resource-manager.md)
## [VPN 게이트웨이 다시 설정](vpn-gateway-resetgw-classic.md)
## VPN Gateway 삭제
### [Azure 포털](vpn-gateway-delete-vnet-gateway-portal.md)
### [Azure PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md)
### [Azure PowerShell(클래식)](vpn-gateway-delete-vnet-gateway-classic-powershell.md)
## [VPN Gateway(클래식) 구성](vpn-gateway-configure-vpn-gateway-mp.md)
## [게이트웨이 SKU(기존)](vpn-gateway-about-skus-legacy.md)
## 타사 VPN 장치 구성
### [개요 및 Azure 구성](vpn-gateway-3rdparty-device-config-overview.md)
### [샘플: Cisco ASA 장치(IKEv2/BGP 아님)](vpn-gateway-3rdparty-device-config-cisco-asa.md)
## 문제 해결
### [VNet에 대한 VPN 처리량의 유효성 검사](vpn-gateway-validate-throughput-to-vnet.md)
### [커뮤니티에서 제안한 VPN 또는 방화벽 장치 설정](vpn-gateway-third-party-settings.md)
### [지점 및 사이트 간 연결 문제](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)
### [사이트 간 연결이 일시적으로 끊김](vpn-gateway-troubleshoot-site-to-site-disconnected-intermittently.md)
### [사이트 간 연결을 사용할 수 없음](vpn-gateway-troubleshoot-site-to-site-cannot-connect.md) 
### [Application Gateway 세션 선호도 문제](https://support.microsoft.com/help/4033827/troubleshooting-azure-application-gateway-session-affinity-issues)
### [VNet 또는 VPN 연결 구성 및 유효성 검사](https://support.microsoft.com/help/4032151/configuring-and-validating-vnet-or-vpn-connections)

# 참조
## [Azure PowerShell](/powershell/module/azurerm.network/?view=azurermps-4.0.0#vpn)
## [Azure PowerShell(클래식)](/powershell/module/azure/?view=azuresmps-3.7.0#networking)
## [REST (영문)](/rest/api/network/virtualnetworkgateways)
## [REST(클래식)](https://msdn.microsoft.com/library/jj154113)
## [Azure CLI](/cli/azure/network/vnet-gateway)

# 관련 항목
## [가상 네트워크](/azure/virtual-network/)
## [응용 프로그램 게이트웨이](/azure/application-gateway/)
## [Azure DNS](/azure/dns/)
## [트래픽 관리자](/azure/traffic-manager/)
## [부하 분산 장치](/azure/load-balancer/)
## [ExpressRoute](/azure/expressroute/)

# 리소스
## [Azure 로드맵](https://azure.microsoft.com/roadmap/?category=networking)
## [블로그](https://azure.microsoft.com/blog/topics/networking)
## [포럼](https://social.msdn.microsoft.com/Forums/azure/home?forum=WAVirtualMachinesVirtualNetwork)
## [가격](https://azure.microsoft.com/pricing/details/vpn-gateway)
## [요금 계산기](https://azure.microsoft.com/pricing/calculator/)
## [SLA](https://azure.microsoft.com/support/legal/sla)
## [비디오](https://azure.microsoft.com/documentation/videos/index/?services=vpn-gateway)
