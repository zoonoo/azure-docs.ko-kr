# [VPN Gateway 설명서](index.md)

# 개요
## [VPN Gateway 정보](vpn-gateway-about-vpngateways.md)
## [VPN Gateway FAQ](vpn-gateway-vpn-faq.md)
## [구독 및 서비스 한도](../azure-subscription-service-limits.md?toc=%2fazure%2fvpn-gateway%2ftoc.json)

# 시작
## 경로 기반 VPN Gateway 만들기
### [Azure Portal](create-routebased-vpn-gateway-portal.md)
### [Azure PowerShell](create-routebased-vpn-gateway-powershell.md)
### [Azure CLI](create-routebased-vpn-gateway-cli.md)

# 개념
## [VPN Gateway 계획 및 설계](vpn-gateway-plan-design.md)
## [VPN Gateway 설정 정보](vpn-gateway-about-vpn-gateway-settings.md)
## [VPN 장치 정보](vpn-gateway-about-vpn-devices.md)
## [암호화 요구 사항 정보](vpn-gateway-about-compliance-crypto.md)
## [BGP 및 VPN Gateway 정보](vpn-gateway-bgp-overview.md)
## [항상 사용 가능한 연결 정보](vpn-gateway-highlyavailable.md)
## [지점 및 사이트 간 연결 정보](point-to-site-about.md)

# 방법
## 사이트 간 연결 구성
### [Azure Portal](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
### [Azure PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
### [Azure CLI](vpn-gateway-howto-site-to-site-resource-manager-cli.md)
## [VPN 장치 구성 스크립트 다운로드](vpn-gateway-download-vpndevicescript.md)
## 지점 및 사이트 간 연결 구성 - 네이티브 Azure 인증서 인증
### P2S VPN 구성
#### [Azure Portal](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
#### [Azure PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
### 자체 서명된 인증서 생성
#### [Azure PowerShell](vpn-gateway-certificates-point-to-site.md)
#### [Makecert](vpn-gateway-certificates-point-to-site-makecert.md)
### [VPN 클라이언트 구성 파일 생성 및 설치](point-to-site-vpn-client-configuration-azure-cert.md)
### [클라이언트 인증서 설치](point-to-site-how-to-vpn-client-install-azure-cert.md)
## 지점 및 사이트 간 연결 구성 - RADIUS 인증
### P2S VPN 구성
#### [Azure PowerShell](point-to-site-how-to-radius-ps.md)
### [VPN 클라이언트 구성 파일 생성 및 설치](point-to-site-vpn-client-configuration-radius.md)
### [NPS 서버와 P2S VPN RADIUS 인증 통합](vpn-gateway-radiuis-mfa-nsp.md)
## VNet 간 연결 구성
### [Azure Portal](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
### [Azure PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
### [Azure CLI](vpn-gateway-howto-vnet-vnet-cli.md)
## 배포 모델 간 VNet-VNet 연결 구성
### [Azure Portal](vpn-gateway-connect-different-deployment-models-portal.md)
### [Azure PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
## 사이트 간 및 ExpressRoute 공존 연결 구성
### [Azure PowerShell](../expressroute/expressroute-howto-coexist-resource-manager.md?toc=%2fazure%2fvpn-gateway%2ftoc.json)
## 여러 사이트 간 연결 구성
### [Azure Portal](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
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
## 로컬 네트워크 게이트웨이 설정 수정
### [Azure Portal](vpn-gateway-modify-local-network-gateway-portal.md)
### [Azure PowerShell](vpn-gateway-modify-local-network-gateway.md)
### [Azure CLI](vpn-gateway-modify-local-network-gateway-cli.md)
## [VPN 게이트웨이 연결 확인](vpn-gateway-verify-connection-resource-manager.md)
## [VPN 게이트웨이 다시 설정](vpn-gateway-resetgw-classic.md)
## VPN Gateway 삭제
### [Azure Portal](vpn-gateway-delete-vnet-gateway-portal.md)
### [Azure PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md)
## [게이트웨이 SKU(기존)](vpn-gateway-about-skus-legacy.md)
## 타사 VPN 장치 구성
### [개요 및 Azure 구성](vpn-gateway-3rdparty-device-config-overview.md)
### [샘플: Cisco ASA 장치(IKEv2/BGP 아님)](vpn-gateway-3rdparty-device-config-cisco-asa.md)
## [문제 해결](vpn-gateway-troubleshoot.md)
### [커뮤니티에서 제안한 VPN 또는 방화벽 장치 설정](vpn-gateway-third-party-settings.md)
### [VNet 또는 VPN 연결 구성 및 유효성 검사](https://support.microsoft.com/help/4032151/configuring-and-validating-vnet-or-vpn-connections)
### [VNet에 대한 VPN 처리량의 유효성 검사](vpn-gateway-validate-throughput-to-vnet.md)
### 지점 및 사이트 간 연결 문제
#### [지점 및 사이트 간 연결 문제](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)
#### [지점 및 사이트 간 연결 문제 - Mac OS X VPN 클라이언트](vpn-gateway-troubleshoot-point-to-site-osx-ikev2.md)
### 사이트 및 사이트 간 연결 문제
#### [사이트 간 연결 문제](vpn-gateway-troubleshoot-site-to-site-cannot-connect.md)
#### [사이트 간 연결이 일시적으로 끊김](vpn-gateway-troubleshoot-site-to-site-disconnected-intermittently.md)

## 클래식 배포 모델 문서
### [사이트 간 연결 구성](vpn-gateway-howto-site-to-site-classic-portal.md)
### [지점 및 사이트 간 연결 구성](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
### [VNet 간 연결 구성](vpn-gateway-howto-vnet-vnet-portal-classic.md)
### [강제 터널링 구성](vpn-gateway-about-forced-tunneling.md)
### [VPN 게이트웨이 삭제](vpn-gateway-delete-vnet-gateway-classic-powershell.md)
### [여러 S2S 연결 구성](vpn-gateway-multi-site.md)
### [VPN 게이트웨이 구성](vpn-gateway-configure-vpn-gateway-mp.md)
### [클래식에서 Resource Manager로 마이그레이션](vpn-gateway-classic-resource-manager-migration.md)

# 참고 자료
## [Azure PowerShell](/powershell/module/azurerm.network/?view=azurermps-4.0.0#vpn)
## [Azure PowerShell(클래식)](/powershell/module/azure/?view=azuresmps-3.7.0#networking)
## [REST](/rest/api/network/virtualnetworkgateways)
## [REST(클래식)](https://msdn.microsoft.com/library/jj154113)
## [Azure CLI](/cli/azure/network/vnet-gateway)

# 관련 항목
## [Virtual Network](/azure/virtual-network/)
## [Application Gateway](/azure/application-gateway/)
## [Azure DNS](/azure/dns/)
## [Traffic Manager](/azure/traffic-manager/)
## [Load Balancer](/azure/load-balancer/)
## [ExpressRoute](/azure/expressroute/)

# 리소스
## [Azure 로드맵](https://azure.microsoft.com/roadmap/?category=networking)
## [블로그](https://azure.microsoft.com/blog/topics/networking)
## [포럼](https://social.msdn.microsoft.com/Forums/azure/home?forum=WAVirtualMachinesVirtualNetwork)
## [가격](https://azure.microsoft.com/pricing/details/vpn-gateway)
## [요금 계산기](https://azure.microsoft.com/pricing/calculator/)
## [SLA](https://azure.microsoft.com/support/legal/sla)
## [비디오](https://azure.microsoft.com/documentation/videos/index/?services=vpn-gateway)
