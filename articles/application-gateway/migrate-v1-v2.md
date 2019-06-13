---
title: Azure 응용 프로그램 게이트웨이 마이그레이션 및 웹 v1에서 v2로 응용 프로그램 방화벽
description: 이 문서에서는 Azure Application Gateway 및 Web Application Firewall v1에서 v2로 마이그레이션하는 방법을 보여 줍니다.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 6/5/2019
ms.author: victorh
ms.openlocfilehash: 44d5ce3e194c873a564039934f518cb3a0e142e3
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66497172"
---
# <a name="migrate-azure-application-gateway-and-web-application-firewall-from-v1-to-v2"></a>Azure 응용 프로그램 게이트웨이 마이그레이션 및 웹 v1에서 v2로 응용 프로그램 방화벽

[Azure Application Gateway 및 웹 응용 프로그램 방화벽 (WAF) v2](application-gateway-autoscaling-zone-redundant.md) 자동 크기 조정 및 가용성 영역 중복 같은 추가 기능 제품 출시 되었습니다. 그러나 기존 v1 게이트웨이 v2로 자동 업그레이드 되지 않습니다. V1에서 v2로 마이그레이션하는 경우이 문서의 단계를 수행 합니다.

마이그레이션에는 두 단계가 있습니다.

1. 구성 마이그레이션
2. 클라이언트 트래픽을 마이그레이션합니다

이 문서에서는 구성 마이그레이션을 다룹니다. 클라이언트 트래픽 마이그레이션 특정 환경에 따라 달라 집니다. 그러나 일부 고급, 일반 권장 사항 [나와](#migrate-client-traffic)합니다.

## <a name="migration-overview"></a>마이그레이션 개요

Azure PowerShell 스크립트는 다음을 수행 하는:

* 지정 하는 가상 네트워크 서브넷에 새 Standard_v2 또는 WAF_v2 게이트웨이 만듭니다.
* 새로 만든 Standard_V2 또는 WAF_V2 게이트웨이에 v1 표준 또는 WAF 게이트웨이에 연결 된 구성 원활 하 게 복사 합니다.

### <a name="caveatslimitations"></a>Caveats\Limitations

* 새 v2 게이트웨이 새 공용 및 개인 IP 주소를 가집니다. V2로 원활 하 게 기존 v1 게이트웨이와 연결 된 IP 주소를 이동 하는 것이 불가능 합니다. 그러나 기존 (할당 되지 않은) 공용 또는 개인 IP 주소를 새 v2 게이트웨이 할당할 수 있습니다.
* V1 게이트웨이 위치한 가상 네트워크 내의 다른 서브넷에 대 한 IP 주소 공간을 제공 해야 합니다. 스크립트는 v1 게이트웨이가 이미 있는 기존 서브넷에서 v2 게이트웨이 만들 수 없습니다. 그러나 기존 서브넷이 이미 있는 경우 여전히 작동할 수 있습니다는 v2 게이트웨이, IP 주소 공간이 충분 한지 제공.
* SSL 구성으로 마이그레이션하려면 v1 게이트웨이에서 사용 되는 모든 SSL 인증서를 지정 해야 합니다.
* FIPS 모드 V1 게이트웨이에 대 한 설정에 있는 경우 새 v2 게이트웨이에 마이그레이션되지 않습니다. FIPS 모드는 v2에서 지원 되지 않습니다.
* v2는 IPv6 지원 하지 않으므로 IPv6 v1 게이트웨이 마이그레이션되지 않습니다. 스크립트를 실행 하는 경우 완료 되지 않을 수 있습니다.
* V1 게이트웨이 개인 IP 주소만 있으면 스크립트를 공용 IP 주소 및 새 v2 게이트웨이에 대 한 개인 IP 주소를 만듭니다. 현재 v2 게이트웨이 개인 IP 주소를 지원 하지 않습니다.

## <a name="download-the-script"></a>스크립트를 다운로드 합니다.

마이그레이션 스크립트를 다운로드 합니다 [PowerShell 갤러리](https://www.powershellgallery.com/packages/AzureAppGWMigration)합니다.

## <a name="use-the-script"></a>스크립트를 사용 합니다.

두 가지 옵션이 있습니다를 로컬 PowerShell 환경 설정 및 기본 설정에 따라:

* 가장 좋은 방법은 사용 하는 모듈을 설치 하는 Azure Az 없는 Azure Az 모듈을 제거 하는 데 괜찮은 경우의 `Install-Script` 스크립트를 실행 하는 옵션입니다.
* Az Azure 모듈을 유지 해야 하는 경우 최선의 스크립트를 다운로드 하 고 직접 실행할 것입니다.

Az Azure 모듈이 설치 된 경우를 확인 하려면 실행 `Get-InstalledModule -Name az`합니다. Az 모듈을 설치 된 후 사용할 수 표시 되지 않으면를 `Install-Script` 메서드.

### <a name="install-using-the-install-script-method"></a>Install-script 메서드를 사용 하 여 설치

이 옵션을 사용 하려면 하지 Azure Az 모듈이 컴퓨터에 설치 해야 합니다. 설치 하는 경우 명령을 오류를 표시 합니다. Az Azure 모듈을 제거 하거나 스크립트를 수동으로 다운로드 하 고 실행 하려면 다른 옵션을 사용 합니다.
  
다음 명령을 사용하여 스크립트를 실행합니다.

`Install-Script -Name AzureAppGWMigration`

또한이 명령은 필요한 Az 모듈을 설치합니다.  

### <a name="install-using-the-script-directly"></a>스크립트를 직접 사용 하 여 설치

일부 Azure Az 모듈을 설치 및 제거 없습니다 (없거나 제거 하지 않으려는)를 수동으로 다운로드 하거나 사용 하 여 스크립트를 **수동 다운로드** 스크립트 다운로드 링크를 탭 합니다. 스크립트는 원시 nupkg 파일로 다운로드 됩니다. 이 nupkg 파일에서 스크립트를 설치 하려면 [수동 패키지 다운로드](https://docs.microsoft.com/powershell/gallery/how-to/working-with-packages/manual-download)합니다.

스크립트를 실행하려면

1. 사용 하 여 `Connect-AzAccount` Azure에 연결 합니다.

1. 사용 하 여 `Import-Module Az` Az 모듈을 가져옵니다.

1. 실행 `Get-Help AzureAppGWMigration.ps1` 필수 매개 변수를 검사 하려면:

   `AzureAppGwMigration.ps1
    -resourceId <v1 application gateway Resource ID>
    -subnetAddressRange <subnet space you want to use>
    -appgwName <string to use to append>
    -sslCertificates <comma-separated SSLCert objects as above>
    -trustedRootCertificates <comma-separated Trusted Root Cert objects as above>
    -privateIpAddress <private IP string>
    -publicIpResourceName <public IP name string>
    -validateMigration -enableAutoScale`

   스크립트에 대 한 매개 변수:
   * **resourceId: [String]: 필요한** -기존 표준 v1 또는 WAF v1 게이트웨이 Azure 리소스 ID입니다. 이 문자열 값을 찾으려면 Azure portal로 이동, 응용 프로그램 게이트웨이 또는 WAF 리소스를 선택 하 고 클릭 합니다 **속성** 게이트웨이에 대 한 링크입니다. 리소스 ID는 해당 페이지에 있습니다.

     또한 리소스 ID를 가져오려면 다음 Azure PowerShell 명령을 실행할 수 있습니다.

     ```azurepowershell
     $appgw = Get-AzApplicationGateway -Name <v1 gateway name> -ResourceGroupName <resource group Name> 
     $appgw.Id
     ```

   * **subnetAddressRange: [String]:  필요한** -새 v2 게이트웨이 포함 하는 새 서브넷 할당 (하거나 할당할) IP 주소 공간입니다. 이 CIDR 표기법으로 지정 되어야 합니다. 예를 들면 다음과 같습니다. 10.0.0.0/24. 이 서브넷을 미리 만들 필요가 없습니다. 스크립트 존재 하지 않는 경우이를 만듭니다.
   * **appgwName: [String]: 선택적**합니다. 새 Standard_v2 또는 WAF_v2 게이트웨이에 대 한 이름으로 사용 하도록 지정 하는 문자열입니다. 이 매개 변수를 제공 하지 않으면, 기존 v1 게이트웨이의 이름을 접미사로 사용 됩니다 *_v2* 추가 합니다.
   * **sslCertificates: [PSApplicationGatewaySslCertificate]: 선택적**합니다.  쉼표로 구분 된 목록은 PSApplicationGatewaySslCertificate 만드는 개체는 v1 게이트웨이에서 SSL 인증서를 나타내는 새로운 v2 게이트웨이를 업로드 해야 합니다. 통해 새 PSApplicationGatewaySslCertificate 개체를 만들 수 있습니다 각 표준 v1 또는 WAF v1 게이트웨이 구성 하 여 SSL 인증서에는 `New-AzApplicationGatewaySslCertificate` 명령은 다음과 같습니다. SSL 인증서 파일을 암호 경로가 필요 합니다.

       이 매개 변수는 v1 게이트웨이 또는 WAF 구성 하는 HTTPS 수신기 없는 경우 선택적만 합니다. 하나 이상의 HTTPS 수신기 설정에 있는 경우이 매개 변수를 지정 해야 합니다.

      ```azurepowershell  
      $password = ConvertTo-SecureString <cert-password> -AsPlainText -Force
      $mySslCert1 = New-AzApplicationGatewaySslCertificate -Name "Cert01" `
        -CertificateFile <Cert-File-Path-1> `
        -Password $password 
      $mySslCert2 = New-AzApplicationGatewaySslCertificate -Name "Cert02" `
        -CertificateFile <Cert-File-Path-2> `
        -Password $password
      ```

      에 전달할 수 있습니다 `$mySslCert1, $mySslCert2` (쉼표로 구분) 스크립트에서이 매개 변수 값으로 이전 예제에서 합니다.
   * **trustedRootCertificates: [PSApplicationGatewayTrustedRootCertificate]: 선택적**합니다. 쉼표로 구분 된 목록을 나타내는 만든 PSApplicationGatewayTrustedRootCertificate 개체를 [신뢰할 수 있는 루트 인증서](ssl-overview.md) v2 게이트웨이에서 백 엔드 인스턴스의 인증에 대 한 합니다.  

      PSApplicationGatewayTrustedRootCertificate 개체의 목록을 참조 하세요 [새로 만들기-AzApplicationGatewayTrustedRootCertificate](https://docs.microsoft.com/powershell/module/Az.Network/New-AzApplicationGatewayTrustedRootCertificate?view=azps-2.1.0&viewFallbackFrom=azps-2.0.0)합니다.
   * **privateIpAddress: [String]: 선택적**합니다. 특정 개인 IP 주소를 새 v2 게이트웨이에 연결 하려는 합니다.  새 v2 게이트웨이에 대 한 할당 하는 동일한 VNet에서 이어야 합니다. 를 지정 하지 않으면이 스크립트는 v2 게이트웨이에 대 한 개인 IP 주소를 할당 합니다.
    * **publicIpResourceId: [String]: 선택적**합니다. 새로운 v2 게이트웨이를 할당 하려는 구독에 공용 IP 주소 리소스의 resourceId입니다. 지정 하지 않으면 스크립트는 동일한 리소스 그룹에 새 공용 IP를 할당 합니다. V2 게이트웨이의 이름을 사용 하 여 이름이 *-IP* 추가 합니다.
   * **validateMigration: [스위치]: 선택적**합니다. V2 게이트웨이 생성 후 구성 복사본 몇 가지 기본 구성을 비교 유효성 검사를 수행 하는 스크립트를 하려는 경우이 매개 변수를 사용 합니다. 기본적으로 유효성을 검사 하지 이루어집니다.
   * **enableAutoScale: [switch]: 선택적**합니다. 스크립트를 만든 후 새 v2 게이트웨이에서 자동 크기 조정을 사용 하도록 설정 하려는 경우이 매개 변수를 사용 합니다. 기본적으로 자동 크기 조정은 비활성화 됩니다. 사용할 수 있습니다 항상 수동으로 나중에 새로 만든된 v2 게이트웨이.

1. 적절 한 매개 변수를 사용 하는 스크립트를 실행 합니다.

    **예제**

   ```azurepowershell
   AzureAppGWMigration.ps1 `
      -resourceId /subscriptions/8b1d0fea-8d57-4975-adfb-308f1f4d12aa/resourceGroups/MyResourceGroup/providers/Microsoft.Network/applicationGateways/myv1appgateway `
      -subnetAddressRange 10.0.0.0/24 `
      -appgwname "MynewV2gw" `
      -sslCertificates $Certs `
      -trustedRootCertificates $trustedCert `
      -privateIpAddress "10.0.0.1" `
      -publicIpResourceName "MyPublicIP" `
      -validateMigration -enableAutoScale
   ```

## <a name="migrate-client-traffic"></a>클라이언트 트래픽을 마이그레이션하십시오

첫째, 다시 확인 정확한 구성을 사용 하 여 새 v2 게이트웨이 성공적으로 만든 스크립트 v1 게이트웨이에서 마이그레이션됩니다. Azure portal에서이 확인할 수 있습니다.

수동 테스트로 적은 양의 v2 게이트웨이 통해 트래픽을 보낼 수도 있습니다.
  
현재 application gateway (표준) 클라이언트 트래픽 및 권장 사항을 각각 나타날 수 있는 몇 가지 시나리오는 다음과 같습니다.

* **(A 레코드 사용) 프런트 엔드 IP 주소를 가리키도록 하는 사용자 지정 DNS 영역 (예: contoso.com) v1 표준 또는 WAF v1 게이트웨이 연결**합니다.

    Standard_v2 application gateway와 연결 된 프런트 엔드 IP 또는 DNS 레이블을를 가리키도록 DNS 레코드를 업데이트할 수 있습니다. DNS 레코드에 구성 된 TTL에 따라 새 v2 게이트웨이에 마이그레이션할 모든 클라이언트 트래픽 시간이 걸릴 수 있습니다.
* **DNS 레이블을 가리키는 사용자 지정 DNS 영역 (예: contoso.com) (예: *myappgw.eastus.cloudapp.azure.com* CNAME 레코드를 사용 하 여) v1 게이트웨이 사용 하 여 연결 된**합니다.

   두 가지 옵션이 있습니다.

  * 제어 하면 응용 프로그램 게이트웨이의 공용 IP 주소를 사용 하는 경우 Traffic Manager 프로필을 사용 하 여 증분 방식으로 새 v2 게이트웨이로 트래픽을 (가중치 적용된 트래픽 라우팅 메서드)를 라우팅하는 세부적인 마이그레이션.

    v1과 v2 모두 응용 프로그램 게이트웨이의 DNS 레이블을 추가 하 여 이렇게 합니다 [Traffic Manager 프로필](../traffic-manager/traffic-manager-routing-methods.md#weighted-traffic-routing-method), 및 CNAMEing Traffic Manager 도메인 (예를 들어 사용자 지정 DNS 레코드 (예: www.contoso.com) contoso.trafficmanager.net)입니다.
  * 또는 새 v2 application gateway의 DNS 레이블로 가리키도록 사용자 지정 도메인 DNS 레코드를 업데이트할 수 있습니다. DNS 레코드에 구성 된 TTL에 따라 새 v2 게이트웨이에 마이그레이션할 모든 클라이언트 트래픽 시간이 걸릴 수 있습니다.
* **Application gateway의 IP 주소 프런트 엔드에 클라이언트 연결**합니다.

   새로 만든된 v2 응용 프로그램 게이트웨이에 연결 된 IP 주소를 사용 하도록 클라이언트를 업데이트 합니다. IP 주소를 직접 사용 하지 않는 것이 좋습니다. 사용자 고유의 사용자 지정 DNS 영역 (예: contoso.com)에 CNAME 수 있는 application gateway와 연결 된 DNS 이름 레이블 (예를 들어 yourgateway.eastus.cloudapp.azure.com)를 사용 하는 것이 좋습니다.

## <a name="common-questions"></a>일반적인 질문

### <a name="are-there-any-limitations-with-the-azure-powershell-script-to-migrate-the-configuration-from-v1-to-v2"></a>V1에서 v2 구성으로 Azure PowerShell 스크립트를 사용 하 여 모든 제한 사항이 있습니까?

예. 참조 [주의/제한](#caveatslimitations)합니다.

### <a name="is-this-article-and-the-azure-powershell-script-applicable-for-application-gateway-waf-product-as-well"></a>이 문서 및 Azure PowerShell 스크립트 및 Application Gateway WAF 제품에 적용할 수는? 

예.

### <a name="does-the-azure-powershell-script-also-switch-over-the-traffic-from-my-v1-gateway-to-the-newly-created-v2-gateway"></a>Azure PowerShell 스크립트를 전환할 수도 새로 만든된 v2 게이트웨이에 내 v1 게이트웨이에서 트래픽을?

아니요. Azure PowerShell 스크립트는 구성만 마이그레이션합니다. 실제 트래픽 마이그레이션은 사용자의 책임에 컨트롤입니다.

### <a name="is-the-new-v2-gateway-created-by-the-azure-powershell-script-sized-appropriately-to-handle-all-of-the-traffic-that-is-currently-served-by-my-v1-gateway"></a>Azure PowerShell 스크립트에서 만든 새 v2 게이트웨이 크기가 적절 하 게 v1 게이트웨이 내에서 현재 제공 되는 트래픽을 모두 처리 하 시겠습니까?

Azure PowerShell 스크립트는 기존 V1 게이트웨이에서 트래픽을 처리 하기 위해 적절 한 크기를 사용 하 여 새 v2 게이트웨이 만듭니다. 자동 크기 조정을 기본적으로 비활성화 되어 있지만 스크립트를 실행 하면 자동 크기 조정을 설정할 수 있습니다.

### <a name="i-ran-into-some-issues-with-using-this-script-how-can-i-get-help"></a>이 스크립트를 사용 하 여 몇 가지 문제를 실행 합니다. 도움을 받으려면 어떻게 하나요?
  
전자 메일을 보낼 수 있습니다 appgwmigrationsup@microsoft.com, 서 Azure 지원을 사용 하 여 지원 사례를 열어야 하거나 둘 다 수행 합니다.

## <a name="next-steps"></a>다음 단계

[응용 프로그램에 알아봅니다 게이트웨이 v2](application-gateway-autoscaling-zone-redundant.md)
