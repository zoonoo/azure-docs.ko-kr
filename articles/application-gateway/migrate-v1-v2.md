---
title: v1에서 v2로 마이그레이션 - Azure 응용 프로그램 게이트웨이
description: 이 문서에서는 Azure 응용 프로그램 게이트웨이 및 웹 응용 프로그램 방화벽을 v1에서 v2로 마이그레이션하는 방법을 보여 주며
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 03/31/2020
ms.author: victorh
ms.openlocfilehash: 2a6165cf2739482805d712ddffb5c6a9f5ebabf8
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81312043"
---
# <a name="migrate-azure-application-gateway-and-web-application-firewall-from-v1-to-v2"></a>Azure 응용 프로그램 게이트웨이 및 웹 응용 프로그램 방화벽을 v1에서 v2로 마이그레이션

[이제 WAF(Azure 응용 프로그램 게이트웨이 및 웹 응용 프로그램 방화벽) v2를](application-gateway-autoscaling-zone-redundant.md) 사용할 수 있으며 자동 크기 조정 및 가용성 영역 중복성과 같은 추가 기능을 제공합니다. 그러나 기존 v1 게이트웨이는 v2로 자동 업그레이드되지 않습니다. v1에서 v2로 마이그레이션하려면 이 문서의 단계를 따르십시오.

마이그레이션에는 두 단계가 있습니다.

1. 구성 마이그레이션
2. 클라이언트 트래픽 마이그레이션

이 문서에서는 구성 마이그레이션에 대해 다룹니다. 클라이언트 트래픽 마이그레이션은 특정 환경에 따라 다릅니다. 그러나 일부 고급 일반 권장 사항이 [제공됩니다.](#migrate-client-traffic)

## <a name="migration-overview"></a>마이그레이션 개요

Azure PowerShell 스크립트는 다음을 수행하는 데 사용할 수 있습니다.

* 지정한 가상 네트워크 서브넷에서 새 Standard_v2 또는 WAF_v2 게이트웨이를 만듭니다.
* v1 표준 또는 WAF 게이트웨이와 연결된 구성을 새로 생성된 Standard_V2 또는 WAF_V2 게이트웨이에 원활하게 복사합니다.

### <a name="caveatslimitations"></a>주의 사항\제한 사항

* 새 v2 게이트웨이에는 새 공용 및 개인 IP 주소가 있습니다. 기존 v1 게이트웨이와 연결된 IP 주소를 v2로 원활하게 이동할 수 없습니다. 그러나 기존(할당되지 않은) 공용 또는 개인 IP 주소를 새 v2 게이트웨이에 할당할 수 있습니다.
* v1 게이트웨이가 있는 가상 네트워크 내의 다른 서브넷에 대한 IP 주소 공간을 제공해야 합니다. 스크립트는 이미 v1 게이트웨이가 있는 기존 서브넷에서 v2 게이트웨이를 만들 수 없습니다. 그러나 기존 서브넷에 이미 v2 게이트웨이가 있는 경우 IP 주소 공간이 충분하면 여전히 작동할 수 있습니다.
* TLS/SSL 구성을 마이그레이션하려면 v1 게이트웨이에 사용되는 모든 TLS/SSL 인증서를 지정해야 합니다.
* V1 게이트웨이에 대해 FIPS 모드를 사용하도록 설정한 경우 새 v2 게이트웨이로 마이그레이션되지 않습니다. FIPS 모드는 v2에서 지원되지 않습니다.
* v2는 IPv6를 지원하지 않으므로 IPv6 사용 가능한 v1 게이트웨이는 마이그레이션되지 않습니다. 스크립트를 실행하면 완료되지 않을 수 있습니다.
* v1 게이트웨이에 개인 IP 주소만 있는 경우 스크립트는 새 v2 게이트웨이에 대한 공용 IP 주소와 개인 IP 주소를 만듭니다. v2 게이트웨이는 현재 개인 IP 주소만 지원하지 않습니다.
* 문자, 숫자, 하이픈 및 밑줄 이외의 이름이 포함된 헤더는 응용 프로그램에 전달되지 않습니다. 헤더 값이 아닌 헤더 이름에만 적용됩니다. 이것은 v1에서 주요 변경 되었습니다.

## <a name="download-the-script"></a>스크립트 다운로드

[PowerShell 갤러리에서](https://www.powershellgallery.com/packages/AzureAppGWMigration)마이그레이션 스크립트를 다운로드합니다.

## <a name="use-the-script"></a>스크립트 사용

로컬 PowerShell 환경 설정 및 기본 설정에 따라 두 가지 옵션이 있습니다.

* Azure Az 모듈이 설치되어 있지 않거나 Azure Az 모듈을 제거해도 괜찮다면 이 `Install-Script` 옵션을 사용하여 스크립트를 실행하는 것이 가장 좋습니다.
* Azure Az 모듈을 유지해야 하는 경우 스크립트를 다운로드하여 직접 실행하는 것이 가장 좋습니다.

Azure Az 모듈이 설치되어 있는지 확인하려면 을 실행합니다. `Get-InstalledModule -Name az` 설치된 Az 모듈이 표시되지 않으면 이 `Install-Script` 방법을 사용할 수 있습니다.

### <a name="install-using-the-install-script-method"></a>설치 스크립트 방법을 사용하여 설치

이 옵션을 사용하려면 컴퓨터에 Azure Az 모듈이 설치되어 있지 않아야 합니다. 설치된 경우 다음 명령에 오류가 표시됩니다. Azure Az 모듈을 제거하거나 다른 옵션을 사용하여 스크립트를 수동으로 다운로드하여 실행할 수 있습니다.
  
다음 명령을 사용하여 스크립트를 실행합니다.

`Install-Script -Name AzureAppGWMigration`

이 명령은 필요한 Az 모듈도 설치합니다.  

### <a name="install-using-the-script-directly"></a>스크립트를 사용하여 직접 설치

일부 Azure Az 모듈이 설치되어 있고 제거할 수 없거나 제거할 수 없는 경우 스크립트 다운로드 링크의 수동 **다운로드** 탭을 사용하여 스크립트를 수동으로 다운로드할 수 있습니다. 스크립트는 원시 nupkg 파일로 다운로드됩니다. 이 nupkg 파일에서 스크립트를 설치하려면 [수동 패키지 다운로드](/powershell/scripting/gallery/how-to/working-with-packages/manual-download)를 참조하십시오.

스크립트를 실행하려면

1. Azure에 연결하는 데 사용합니다. `Connect-AzAccount`

1. Az `Import-Module Az` 모듈을 가져오는 데 사용합니다.

1. 실행하여 `Get-Help AzureAppGWMigration.ps1` 필요한 매개 변수를 검사합니다.

   ```
   AzureAppGwMigration.ps1
    -resourceId <v1 application gateway Resource ID>
    -subnetAddressRange <subnet space you want to use>
    -appgwName <string to use to append>
    -sslCertificates <comma-separated SSLCert objects as above>
    -trustedRootCertificates <comma-separated Trusted Root Cert objects as above>
    -privateIpAddress <private IP string>
    -publicIpResourceId <public IP name string>
    -validateMigration -enableAutoScale
   ```

   스크립트에 대한 매개 변수:
   * **resourceId: [문자열]: 필수** - 기존 표준 v1 또는 WAF v1 게이트웨이에 대한 Azure 리소스 ID입니다. 이 문자열 값을 찾으려면 Azure 포털로 이동하여 응용 프로그램 게이트웨이 또는 WAF 리소스를 선택하고 게이트웨이에 대한 **속성** 링크를 클릭합니다. 리소스 ID는 해당 페이지에 있습니다.

     리소스 ID를 얻으려면 다음 Azure PowerShell 명령을 실행할 수도 있습니다.

     ```azurepowershell
     $appgw = Get-AzApplicationGateway -Name <v1 gateway name> -ResourceGroupName <resource group Name> 
     $appgw.Id
     ```

   * **subnetAddressRange: [문자열]: 필수** - 새 v2 게이트웨이가 포함된 새 서브넷에 할당(또는 할당하려는 IP 주소 공간)입니다. CIDR 표기법의 명시를 지정해야 합니다. 예: 10.0.0.0/24. 이 서브넷을 미리 만들 필요는 없습니다. 스크립트가 존재하지 않는 경우 스크립트를 만듭니다.
   * **appgwName: [문자열]: 선택 사항**. 새 Standard_v2 또는 WAF_v2 게이트웨이의 이름으로 사용하도록 지정한 문자열입니다. 이 매개 변수가 제공되지 않으면 기존 v1 게이트웨이의 이름이 추가된 접미사 *_v2* 함께 사용됩니다.
   * **ssl인증서: [PSApplication게이트웨이Ssl인증서]: 선택 사항**.  v1 게이트웨이에서 TLS/SSL 인증서인증서를 나타내기 위해 만든 PSApplicationGatewaySslCertificate 개체의 쉼표로 구분된 목록은 새 v2 게이트웨이에 업로드해야 합니다. 표준 v1 또는 WAF v1 게이트웨이에 대해 구성된 각 TLS/SSL 인증서에 대해 여기에 표시된 `New-AzApplicationGatewaySslCertificate` 명령을 통해 새 PSApplicationGatewaySslCertificate 개체를 만들 수 있습니다. TLS/SSL 인증서 파일과 암호에 대한 경로가 필요합니다.

     이 매개 변수는 v1 게이트웨이 또는 WAF에 대해 구성된 HTTPS 수신기가 없는 경우에만 선택 사항입니다. HTTPS 수신기 설정이 하나 이상 있는 경우 이 매개 변수를 지정해야 합니다.

      ```azurepowershell  
      $password = ConvertTo-SecureString <cert-password> -AsPlainText -Force
      $mySslCert1 = New-AzApplicationGatewaySslCertificate -Name "Cert01" `
        -CertificateFile <Cert-File-Path-1> `
        -Password $password 
      $mySslCert2 = New-AzApplicationGatewaySslCertificate -Name "Cert02" `
        -CertificateFile <Cert-File-Path-2> `
        -Password $password
      ```

     스크립트에서 이 `$mySslCert1, $mySslCert2` 매개 변수에 대한 값으로 이전 예제에서 (쉼표로 구분된) 전달할 수 있습니다.
   * **trusted루트인증서: [PSApplicationGatewayTrustedRootCertificate]: 선택적**. v2 게이트웨이에서 백 엔드 인스턴스 인증을 위해 [신뢰할 수 있는 루트 인증서를](ssl-overview.md) 나타내기 위해 만든 PSApplicationGatewayTrustedRootCertificate 개체의 쉼표로 구분된 목록입니다.
   
      ```azurepowershell
      $certFilePath = ".\rootCA.cer"
      $trustedCert = New-AzApplicationGatewayTrustedRootCertificate -Name "trustedCert1" -CertificateFile $certFilePath
      ```

      PSApplication게이트웨이Trusted루트인증서 개체 목록을 만들려면 [New-AzApplication게이트웨이TrustedRootCertificate](https://docs.microsoft.com/powershell/module/Az.Network/New-AzApplicationGatewayTrustedRootCertificate?view=azps-2.1.0&viewFallbackFrom=azps-2.0.0)을 참조하십시오.
   * **개인 IpAddress: [문자열]: 선택 사항**입니다. 새 v2 게이트웨이에 연결하려는 특정 개인 IP 주소입니다.  새 v2 게이트웨이에 할당된 VNet과 동일한 VNet에서 사용해야 합니다. 이 지정되지 않은 경우 스크립트는 v2 게이트웨이에 대한 개인 IP 주소를 할당합니다.
   * **publicIpResourceId: [문자열]: 선택 사항**. 새 v2 게이트웨이에 할당하려는 구독의 기존 공용 IP 주소(표준 SKU) 리소스의 resourceId입니다. 이 지정되지 않은 경우 스크립트는 동일한 리소스 그룹에 새 공용 IP를 할당합니다. 이름은 *-IP가* 추가된 v2 게이트웨이의 이름입니다.
   * **유효성 검사 마이그레이션: [스위치]: 선택 사항**입니다. 스크립트가 v2 게이트웨이 생성 및 구성 복사본 후에 몇 가지 기본 구성 비교 유효성 검사를 수행하도록 하려면 이 매개 변수를 사용합니다. 기본적으로 유효성 검사는 수행되지 않습니다.
   * **enable자동 규모: [스위치]: 옵션**. 스크립트가 생성된 후 새 v2 게이트웨이에서 자동 크기 조정을 사용하도록 설정하려면 이 매개 변수를 사용합니다. 기본적으로 자동 크기 조정은 사용할 수 없습니다. 새로 만든 v2 게이트웨이에서 나중에 수동으로 활성화할 수 있습니다.

1. 적절한 매개 변수를 사용하여 스크립트를 실행합니다. 완료하는 데 5~7분정도 걸릴 수 있습니다.

    **예제**

   ```azurepowershell
   AzureAppGWMigration.ps1 `
      -resourceId /subscriptions/8b1d0fea-8d57-4975-adfb-308f1f4d12aa/resourceGroups/MyResourceGroup/providers/Microsoft.Network/applicationGateways/myv1appgateway `
      -subnetAddressRange 10.0.0.0/24 `
      -appgwname "MynewV2gw" `
      -sslCertificates $mySslCert1,$mySslCert2 `
      -trustedRootCertificates $trustedCert `
      -privateIpAddress "10.0.0.1" `
      -publicIpResourceId "/subscriptions/8b1d0fea-8d57-4975-adfb-308f1f4d12aa/resourceGroups/MyResourceGroup/providers/Microsoft.Network/publicIPAddresses/MyPublicIP" `
      -validateMigration -enableAutoScale
   ```

## <a name="migrate-client-traffic"></a>클라이언트 트래픽 마이그레이션

먼저 스크립트가 v1 게이트웨이에서 마이그레이션된 정확한 구성으로 새 v2 게이트웨이를 성공적으로 만들수 있는지 확인합니다. Azure 포털에서 이를 확인할 수 있습니다.

또한 수동 테스트로 v2 게이트웨이를 통해 소량의 트래픽을 보냅니다.
  
다음은 현재 응용 프로그램 게이트웨이(표준)가 클라이언트 트래픽을 수신할 수 있는 몇 가지 시나리오와 각 응용 프로그램 게이트웨이에 대한 권장 사항입니다.

* **표준 v1 또는 WAF v1 게이트웨이와 연결된 프런트 엔드 IP 주소(A 레코드 사용)를 가리키는 사용자 지정 DNS 영역(예: contoso.com).**

    Standard_v2 응용 프로그램 게이트웨이와 연결된 프런트 엔드 IP 또는 DNS 레이블을 가리키도록 DNS 레코드를 업데이트할 수 있습니다. DNS 레코드에 구성된 TTL에 따라 모든 클라이언트 트래픽이 새 v2 게이트웨이로 마이그레이션하는 데 시간이 걸릴 수 있습니다.
* **v1 게이트웨이와 연결된 DNS 레이블(예: CNAME 레코드 사용 *myappgw.eastus.cloudapp.azure.com)을* 가리키는 사용자 지정 DNS 영역(예: contoso.com).**

   두 가지 선택 사항이 있습니다.

  * 응용 프로그램 게이트웨이에서 공용 IP 주소를 사용하는 경우 트래픽 관리자 프로필을 사용하여 제어된 세분화된 마이그레이션을 수행하여 트래픽(가중치가 적용된 트래픽 라우팅 방법)을 새 v2 게이트웨이로 점진적으로 라우팅할 수 있습니다.

    이를 위해 v1 및 v2 응용 프로그램 게이트웨이의 DNS 레이블을 [트래픽 관리자 프로필에](../traffic-manager/traffic-manager-routing-methods.md#weighted-traffic-routing-method)추가하고 사용자 지정 DNS 레코드(예: `www.contoso.com`contoso.trafficmanager.net)에 CNAME를 지정하면 됩니다.
  * 또는 새 v2 응용 프로그램 게이트웨이의 DNS 레이블을 가리키도록 사용자 지정 도메인 DNS 레코드를 업데이트할 수 있습니다. DNS 레코드에 구성된 TTL에 따라 모든 클라이언트 트래픽이 새 v2 게이트웨이로 마이그레이션하는 데 시간이 걸릴 수 있습니다.
* **클라이언트는 애플리케이션 게이트웨이의 프런트 엔드 IP 주소에 연결합니다.**

   새로 만든 v2 응용 프로그램 게이트웨이와 연결된 IP 주소(es)를 사용하도록 클라이언트를 업데이트합니다. IP 주소를 직접 사용하지 않는 것이 좋습니다. 응용 프로그램 게이트웨이와 연결된 DNS 이름 레이블(예: yourgateway.eastus.cloudapp.azure.com)을 사용하여 사용자 지정 DNS 영역(예: contoso.com)에 CNAME을 사용할 수 있습니다.

## <a name="common-questions"></a>일반적인 질문

### <a name="are-there-any-limitations-with-the-azure-powershell-script-to-migrate-the-configuration-from-v1-to-v2"></a>Azure PowerShell 스크립트에서 구성을 v1에서 v2로 마이그레이션하는 데 제한이 있습니까?

예. [주의 사항/제한 사항을](#caveatslimitations)참조하십시오.

### <a name="is-this-article-and-the-azure-powershell-script-applicable-for-application-gateway-waf-product-as-well"></a>이 문서와 Azure PowerShell 스크립트는 응용 프로그램 게이트웨이 WAF 제품에도 적용 가능한가? 

예.

### <a name="does-the-azure-powershell-script-also-switch-over-the-traffic-from-my-v1-gateway-to-the-newly-created-v2-gateway"></a>Azure PowerShell 스크립트도 내 v1 게이트웨이에서 새로 생성된 v2 게이트웨이로 트래픽을 전환합니까?

아니요. Azure PowerShell 스크립트는 구성만 마이그레이션합니다. 실제 트래픽 마이그레이션은 사용자의 책임이며 제어할 수 있습니다.

### <a name="is-the-new-v2-gateway-created-by-the-azure-powershell-script-sized-appropriately-to-handle-all-of-the-traffic-that-is-currently-served-by-my-v1-gateway"></a>Azure PowerShell 스크립트에서 만든 새 v2 게이트웨이가 현재 내 v1 게이트웨이에서 제공되는 모든 트래픽을 처리할 수 있도록 적절하게 크기가 조정됩니까?

Azure PowerShell 스크립트는 기존 v1 게이트웨이의 트래픽을 처리하기에 적절한 크기의 새 v2 게이트웨이를 만듭니다. 자동 크기 조정은 기본적으로 비활성화되어 있지만 스크립트를 실행할 때 자동 크기 조정을 사용하도록 설정할 수 있습니다.

### <a name="i-configured-my-v1-gateway--to-send-logs-to-azure-storage-does-the-script-replicate-this-configuration-for-v2-as-well"></a>Azure 저장소에 로그를 보내도록 v1 게이트웨이를 구성했습니다. 스크립트가 v2에 대해서도 이 구성을 복제합니까?

아니요. 스크립트는 v2에 대해 이 구성을 복제하지 않습니다. 마이그레이션된 v2 게이트웨이에 로그 구성을 별도로 추가해야 합니다.

### <a name="does-this-script-support-certificates-uploaded-to-azure-keyvault-"></a>이 스크립트 지원 인증서Azure KeyVault에 업로드됩니까?

아니요. 현재 스크립트는 KeyVault의 인증서를 지원하지 않습니다. 그러나 이 방법은 향후 버전에서 고려중입니다.

### <a name="i-ran-into-some-issues-with-using-this-script-how-can-i-get-help"></a>이 스크립트를 사용하는 데 몇 가지 문제가 있습니다. 어떻게 도움을 받을 수 있나요?
  
appgwmigrationsup@microsoft.com에 전자 메일을 보낼 수 있습니다 ., Azure 지원 으로 지원 사례를 열거나 둘 다 수행할 수 있습니다.

## <a name="next-steps"></a>다음 단계

[애플리케이션 게이트웨이 v2에 대해 자세히 알아보기](application-gateway-autoscaling-zone-redundant.md)
