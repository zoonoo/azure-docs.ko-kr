---
title: Azure DevTest 랩에서 원격 데스크톱 게이트웨이를 사용하도록 랩 구성
description: RdP 포트를 노출하지 않고도 랩 VM에 대한 보안 액세스를 보장하기 위해 원격 데스크톱 게이트웨이를 사용하여 Azure DevTest Labs에서 랩을 구성하는 방법을 알아봅니다.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 2cdafa9a36a5f906151ca6946e18ef82bc7f1e01
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80529415"
---
# <a name="configure-your-lab-in-azure-devtest-labs-to-use-a-remote-desktop-gateway"></a>원격 데스크톱 게이트웨이를 사용하도록 Azure DevTest Labs에서 랩 구성
Azure DevTest Labs에서 RDP 포트를 노출하지 않고도 랩 가상 컴퓨터(VM)에 대한 보안 액세스를 보장하도록 랩용 원격 데스크톱 게이트웨이를 구성할 수 있습니다. 랩은 랩 사용자가 액세스할 수 있는 모든 가상 컴퓨터를 보고 연결할 수 있는 중앙 위치를 제공합니다. **가상 컴퓨터** 페이지의 **연결** 단추는 컴퓨터에 연결하기 위해 열 수 있는 컴퓨터별 RDP 파일을 만듭니다. 랩을 원격 데스크톱 게이트웨이에 연결하여 RDP 연결을 추가로 사용자 지정하고 보호할 수 있습니다. 

랩 사용자가 게이트웨이 컴퓨터에 직접 인증하거나 도메인 에 가입한 게이트웨이 컴퓨터에서 회사 자격 증명을 사용하여 컴퓨터에 연결할 수 있기 때문에 이 방법은 더 안전합니다. 또한 이 랩은 사용자가 RDP 포트를 인터넷에 노출하지 않고도 랩 가상 컴퓨터에 연결할 수 있는 게이트웨이 시스템에 대한 토큰 인증을 지원합니다. 이 문서에서는 토큰 인증을 사용하여 랩 컴퓨터에 연결하는 랩을 설정하는 방법에 대한 예제를 설명합니다.

## <a name="architecture-of-the-solution"></a>솔루션아키텍처

![솔루션아키텍처](./media/configure-lab-remote-desktop-gateway/architecture.png)

1. **연결** 단추를 선택할 때 [RDP 파일 내용 얻기](/rest/api/dtl/virtualmachines/getrdpfilecontents) 작업이 호출됩니다. 
1. RDP 파일 내용 받기 작업은 `https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}` 인증 토큰을 요청하기 위해 호출됩니다.
    1. `{gateway-hostname}`은 Azure 포털의 **랩에** 대한 랩 설정 페이지에 지정된 게이트웨이 호스트 이름입니다. 
    1. `{lab-machine-name}`은 연결하려는 컴퓨터의 이름입니다.
    1. `{port-number}`은 연결을 만들어야 하는 포트입니다. 일반적으로 이 포트는 3389입니다. 랩 VM이 DevTest Labs에서 [공유 IP](devtest-lab-shared-ip.md) 기능을 사용하는 경우 포트는 다릅니다.
1. 원격 데스크톱 게이트웨이는 인증 `https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}` 토큰을 생성하기 위해 Azure 함수로의 호출을 연기합니다. DevTest Labs 서비스에는 요청 헤더에 함수 키가 자동으로 포함됩니다. 함수 키는 랩의 키 자격 증명 모음에 저장됩니다. 랩의 **랩 설정** 페이지에서 **게이트웨이 토큰 비밀로** 표시될 해당 비밀의 이름입니다.
1. Azure 함수는 게이트웨이 컴퓨터에 대해 인증서 기반 토큰 인증에 대한 토큰을 반환해야 합니다.  
1. 그런 다음 RDP 파일 내용 수집 작업은 인증 정보를 포함한 전체 RDP 파일을 반환합니다.
1. 원하는 RDP 연결 프로그램을 사용하여 RDP 파일을 엽니다. 모든 RDP 연결 프로그램이 토큰 인증을 지원하는 것은 아닙니다. 인증 토큰에는 함수 앱에서 설정한 만료 날짜가 있습니다. 토큰이 만료되기 전에 랩 VM에 연결합니다.
1. 원격 데스크톱 게이트웨이 컴퓨터가 RDP 파일에서 토큰을 인증하면 연결이 랩 컴퓨터로 전달됩니다.

### <a name="solution-requirements"></a>솔루션 요구 사항
DevTest Labs 토큰 인증 기능을 사용하려면 게이트웨이 컴퓨터, 도메인 이름 서비스(DNS) 및 기능에 대한 몇 가지 구성 요구 사항이 있습니다.

### <a name="requirements-for-remote-desktop-gateway-machines"></a>원격 데스크톱 게이트웨이 컴퓨터에 대한 요구 사항
- HTTPS 트래픽을 처리하려면 게이트웨이 컴퓨터에 TLS/SSL 인증서를 설치해야 합니다. 인증서는 게이트웨이 팜에 대한 로드 밸러터의 정규화된 도메인 이름(FQDN) 또는 컴퓨터가 하나뿐인 경우 컴퓨터 자체의 FQDN과 일치해야 합니다. 와일드카드 TLS/SSL 인증서가 작동하지 않습니다.  
- 게이트웨이 컴퓨터에 설치된 서명 인증서입니다. 서명 인증서 만들기 [인증서.ps1](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/tools/Create-SigningCertificate.ps1) 스크립트를 사용 하 여 서명 인증서를 만듭니다.
- 원격 데스크톱 게이트웨이에 대한 토큰 인증을 지원하는 [플러그 형 인증](https://code.msdn.microsoft.com/windowsdesktop/Remote-Desktop-Gateway-517d6273) 모듈을 설치합니다. 이러한 모듈의 한 `RDGatewayFedAuth.msi` 가지 예는 [시스템 센터 가상 머신 관리자(VMM) 이미지와](/system-center/vmm/install-console?view=sc-vmm-1807)함께 제공되는 것입니다. 시스템 센터에 대한 자세한 내용은 [시스템 센터 설명서](https://docs.microsoft.com/system-center/) 및 [가격 세부 정보를](https://www.microsoft.com/cloud-platform/system-center-pricing)참조하십시오.  
- 게이트웨이 서버는 에 대한 `https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}`요청을 처리할 수 있습니다.

    게이트웨이 호스트 이름은 게이트웨이 팜의 로드 밸러터의 FQDN 또는 컴퓨터 자체의 FQDN(컴퓨터 하나만 있는 경우)입니다. 연결하려는 `{lab-machine-name}` 랩 컴퓨터의 이름이며 연결이 이루어지는 `{port-number}` 포트입니다.  기본적으로 이 포트는 3389입니다.  그러나 가상 시스템이 DevTest Labs에서 [공유 IP](devtest-lab-shared-ip.md) 기능을 사용하는 경우 포트는 다릅니다.
- IIS(인터넷 정보 서버)에 대한 [응용 프로그램 라우팅](/iis/extensions/planning-for-arr/using-the-application-request-routing-module) 요청 `https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}` 모듈을 사용하여 인증을 위한 토큰을 가져오도록 요청을 처리하는 Azure 함수로 요청을 리디렉션할 수 있습니다.


## <a name="requirements-for-azure-function"></a>Azure 기능에 대한 요구 사항
Azure 함수는 요청을 형식으로 `https://{function-app-uri}/app/host/{lab-machine-name}/port/{port-number}` 처리하고 게이트웨이 컴퓨터에 설치된 동일한 서명 인증서를 기반으로 인증 토큰을 반환합니다. 는 `{function-app-uri}` 함수에 액세스하는 데 사용되는 uri입니다. 함수 키는 요청의 헤더에 자동으로 전달됩니다. 샘플 함수는 을 [https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/src/RDGatewayAPI/Functions/CreateToken.cs](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/src/RDGatewayAPI/Functions/CreateToken.cs)참조하십시오. 


## <a name="requirements-for-network"></a>네트워크에 대한 요구 사항

- 게이트웨이 시스템에 설치된 TLS/SSL 인증서와 연결된 FQDN용 DNS는 게이트웨이 컴퓨터 또는 게이트웨이 시스템 팜의 로드 밸레인저로 트래픽을 직접 보내야 합니다.
- 랩 머신에서 개인 IP를 사용하는 경우 동일한 가상 네트워크를 공유하거나 피어링된 가상 네트워크를 사용하여 게이트웨이 컴퓨터에서 랩 컴퓨터로의 네트워크 경로가 있어야 합니다.

## <a name="configure-the-lab-to-use-token-authentication"></a>토큰 인증을 사용하도록 랩 구성 
이 섹션에서는 토큰 인증을 지원하는 원격 데스크톱 게이트웨이 컴퓨터를 사용하도록 랩을 구성하는 방법을 보여 주며 있습니다. 이 섹션에서는 원격 데스크톱 게이트웨이 팜 자체를 설정하는 방법을 다루지 않습니다. 이 정보에 대 한 이 문서의 끝에 [원격 데스크톱 게이트웨이](#sample-to-create-a-remote-desktop-gateway) 섹션을 만들 수 샘플을 참조 하십시오. 

랩 설정을 업데이트하기 전에 함수를 성공적으로 실행하여 랩의 키 자격 증명 모음에 인증 토큰을 반환하는 데 필요한 키를 저장합니다. Azure 포털의 함수에 대한 **관리** 페이지에서 함수 키 값을 얻을 수 있습니다. 키 자격 증명 모음에 비밀을 저장하는 방법에 대한 자세한 내용은 [키 자격 증명 모음에 비밀 추가를](../key-vault/quick-create-portal.md#add-a-secret-to-key-vault)참조하십시오. 나중에 사용할 수 있는 비밀의 이름을 저장합니다.

랩의 키 자격 증명 모음의 ID를 찾으려면 다음 Azure CLI 명령을 실행합니다. 

```azurecli
az resource show --name {lab-name} --resource-type 'Microsoft.DevTestLab/labs' --resource-group {lab-resource-group-name} --query properties.vaultName
```

다음 단계를 사용하여 토큰 인증을 사용하도록 랩을 구성합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **모든 서비스**를 선택한 다음, 목록에서 **DevTest Labs**를 선택합니다.
1. 랩 목록에서 **랩을**선택합니다.
1. 랩 페이지에서 구성 및 **정책을**선택합니다.
1. 왼쪽 메뉴에서 **설정** 섹션에서 랩 **설정을**선택합니다.
1. 원격 **데스크톱** 섹션에서 **게이트웨이 호스트 이름** 필드에 대한 원격 데스크톱 서비스 게이트웨이 컴퓨터 또는 팜의 정규화된 도메인 이름(FQDN) 또는 IP 주소를 입력합니다. 이 값은 게이트웨이 컴퓨터에 사용되는 TLS/SSL 인증서의 FQDN과 일치해야 합니다.

    ![랩 설정의 원격 데스크톱 옵션](./media/configure-lab-remote-desktop-gateway/remote-desktop-options-in-lab-settings.png)
1. **게이트웨이 토큰** 보안의 경우 **원격 데스크톱** 섹션에서 이전에 만든 비밀의 이름을 입력합니다. 이 값은 함수 키 자체가 아니라 함수 키를 보유하는 랩의 키 자격 증명 모음에 있는 비밀의 이름입니다.

    ![랩 설정의 게이트웨이 토큰 비밀](./media/configure-lab-remote-desktop-gateway/gateway-token-secret.png)
1. **저장** 변경.

    > [!NOTE] 
    > **저장을**클릭하면 [원격 데스크톱 게이트웨이의 라이센스 조건에](https://www.microsoft.com/licensing/product-licensing/products)동의합니다. 원격 게이트웨이에 대한 자세한 내용은 [원격 데스크톱 서비스 에 대한 시작](https://aka.ms/rds) 및 원격 데스크톱 환경 [배포를](/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure)참조하십시오.


자동화를 통해 랩을 구성하는 것이 바람직한 경우 샘플 PowerShell 스크립트에 대한 [Set-DevTestLabGateway.ps1을](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/tools/Set-DevTestLabGateway.ps1) 참조하여 **게이트웨이 호스트 이름** 및 게이트웨이 토큰 **비밀** 설정을 설정합니다. [Azure DevTest Labs GitHub 리포지토리는](https://github.com/Azure/azure-devtestlab) **게이트웨이 호스트 이름** 및 **게이트웨이 토큰 보안** 설정을 통해 랩을 만들거나 업데이트하는 Azure 리소스 관리자 템플릿도 제공합니다.

## <a name="configure-network-security-group"></a>네트워크 보안 그룹 구성
랩을 더욱 안전하게 보호하기 위해 랩 가상 컴퓨터에서 사용하는 가상 네트워크에 NSG(네트워크 보안 그룹)를 추가할 수 있습니다. NSG를 설정하는 방법에 대한 지침은 [네트워크 보안 그룹 만들기, 변경 또는 삭제를](../virtual-network/manage-network-security-group.md)참조하십시오.

다음은 게이트웨이를 통과하는 트래픽만 랩 컴퓨터에 도달할 수 있도록 허용하는 NSG 예제입니다. 이 규칙의 소스는 단일 게이트웨이 시스템의 IP 주소 또는 게이트웨이 컴퓨터 앞에 있는 로드 밸러버의 IP 주소입니다.

![네트워크 보안 그룹 - 규칙](./media/configure-lab-remote-desktop-gateway/network-security-group-rules.png)

## <a name="sample-to-create-a-remote-desktop-gateway"></a>원격 데스크톱 게이트웨이를 만드는 샘플

> [!NOTE] 
> 샘플 템플릿을 사용하면 [원격 데스크톱 게이트웨이의 라이센스 조건에](https://www.microsoft.com/licensing/product-licensing/products)동의합니다. 원격 게이트웨이에 대한 자세한 내용은 [원격 데스크톱 서비스 에 대한 시작](https://aka.ms/rds) 및 원격 데스크톱 환경 [배포를](/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure)참조하십시오.

[Azure DevTest Labs GitHub 리포지토리는](https://github.com/Azure/azure-devtestlab) DevTest Labs에서 토큰 인증 및 원격 데스크톱 게이트웨이를 사용하는 데 필요한 리소스를 설정하는 데 도움이 되는 몇 가지 샘플을 제공합니다. 이러한 샘플에는 게이트웨이 컴퓨터, 랩 설정 및 함수 앱에 대한 Azure 리소스 관리자 템플릿이 포함됩니다.

다음 단계에 따라 원격 데스크톱 게이트웨이 팜에 대한 샘플 솔루션을 설정합니다.

1. 서명 인증서를 만듭니다.  실행 [만들기 서명인증서.ps1.](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/tools/Create-SigningCertificate.ps1) 생성된 인증서의 지문, 암호 및 Base64 인코딩을 저장합니다.
2. TLS/SSL 인증서를 받으십시오. TLS/SSL 인증서와 연결된 FQDN은 제어하는 도메인에 대한 것이어야 합니다. 이 인증서에 대한 지문, 암호 및 Base64 인코딩을 저장합니다. PowerShell을 사용하여 지문을 얻으려면 다음 명령을 사용합니다.

    ```powershell
    $cer = New-Object System.Security.Cryptography.X509Certificates.X509Certificate;
    $cer.Import(‘path-to-certificate’);
    $hash = $cer.GetCertHashString()
    ```

    PowerShell을 사용하여 Base64 인코딩을 얻으려면 다음 명령을 사용합니다.

    ```powershell
    [System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes(‘path-to-certificate’))
    ```
3. 에서 파일을 [https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway)다운로드합니다.

    템플릿은 동일한 기본 URI에서 몇 가지 다른 리소스 관리자 템플릿 및 관련 리소스에 액세스해야 합니다. 저장소 계정의 [https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/arm/gateway](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/arm/gateway) Blob 컨테이너에 RDGatewayFedAuth.msi의 모든 파일을 복사합니다.  
4. 에서 **azuredeploy.json을** [https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway)배포합니다. 템플릿은 다음과 같은 매개 변수를 사용합니다.
    - 관리자사용자 이름 - 필수입니다.  게이트웨이 컴퓨터의 관리자 사용자 이름입니다.
    - 관리자 암호 - 필수입니다. 게이트웨이 컴퓨터에 대한 관리자 계정의 암호입니다.
    - 인스턴스Count - 만들 게이트웨이 컴퓨터의 수입니다.  
    - alwaysOn - 생성된 Azure Functions 앱을 웜 상태로 유지할지 여부를 나타냅니다. Azure Functions 앱을 유지하면 사용자가 랩 VM에 처음 연결하려고 할 때 지연을 방지할 수 있지만 비용에 영향을 미칩니다.  
    - tokenLifetime - 생성된 토큰이 유효한 기간입니다. 형식은 HH: MM: SS입니다.
    - sslCertificate - 게이트웨이 시스템에 대한 TLS/SSL 인증서의 Base64 인코딩입니다.
    - sslCertificatePassword - 게이트웨이 컴퓨터에 대한 TLS / SSL 인증서의 암호입니다.
    - sslCertificateThumbprint - TLS/SSL 인증서의 로컬 인증서 저장소에서 식별하기 위한 인증서 지문입니다.
    - signCertificate - 게이트웨이 시스템에 대한 인증서 서명을 위한 Base64 인코딩입니다.
    - signCertificatePassword - 게이트웨이 컴퓨터에 대한 인증서 서명에 대한 암호입니다.
    - signCertificateThumbprint - 서명 인증서의 로컬 인증서 저장소에서 식별을 위한 인증서 지문입니다.
    - _artifactsLocation – 모든 지원 리소스를 찾을 수 있는 URI 위치입니다. 이 값은 상대 경로가 아닌 정규화된 UIR이어야 합니다.
    - _artifactsLocationSasToken 위치가 Azure 저장소 계정인 경우 지원 리소스에 액세스하는 데 사용되는 SAS(공유 액세스 서명) 토큰입니다.

    다음 명령을 사용하여 Azure CLI를 사용하여 템플릿을 배포할 수 있습니다.

    ```azurecli
    az group deployment create --resource-group {resource-group} --template-file azuredeploy.json --parameters @azuredeploy.parameters.json -–parameters _artifactsLocation="{storage-account-endpoint}/{container-name}" -–parameters _artifactsLocationSasToken = "?{sas-token}"
    ```

    매개 변수에 대한 설명은 다음과 같습니다.

    - {저장소-계정-끝점}을 실행 `az storage account show --name {storage-acct-name} --query primaryEndpoints.blob`하 여 가져올 수 있습니다.  {저장소-acct-name}은 업로드한 파일을 보관하는 저장소 계정의 이름입니다.  
    - {컨테이너 이름}은 업로드한 파일을 보유하는 {저장소-acct-name}의 컨테이너 이름입니다.  
    - {sas-token}을 실행하여 `az storage container generate-sas --name {container-name} --account-name {storage-acct-name} --https-only –permissions drlw –expiry {utc-expiration-date}`얻을 수 있습니다. 
        - {저장소-acct-name}은 업로드한 파일을 보관하는 저장소 계정의 이름입니다.  
        - {컨테이너 이름}은 업로드한 파일을 보유하는 {저장소-acct-name}의 컨테이너 이름입니다.  
        - {utc 만료 날짜}는 UTC에서 SAS 토큰이 만료되고 SAS 토큰을 더 이상 저장소 계정에 액세스하는 데 사용할 수 없는 날짜입니다.

    템플릿 배포 출력에서 게이트웨이FQDN 및 게이트웨이IP에 대한 값을 기록합니다. 또한 함수 [앱 설정](../azure-functions/functions-how-to-use-azure-function-app-settings.md) 탭에서 찾을 수 있는 새로 만든 함수에 대한 함수 키 값을 저장해야 합니다.
5. TLS/SSL 인증서의 FQDN이 이전 단계에서 게이트웨이IP의 IP 주소로 전달되도록 DNS를 구성합니다.

    원격 데스크톱 게이트웨이 팜을 만들고 적절한 DNS 업데이트를 완료하면 DevTest Labs의 랩에서 사용할 준비가 됩니다. **배포한 게이트웨이 컴퓨터를** 사용하도록 게이트웨이 호스트 이름 및 **게이트웨이 토큰 보안** 설정을 구성해야 합니다. 

    > [!NOTE]
    > 랩 머신에서 개인 IP를 사용하는 경우 동일한 가상 네트워크를 공유하거나 피어링된 가상 네트워크를 사용하여 게이트웨이 컴퓨터에서 랩 컴퓨터로의 네트워크 경로가 있어야 합니다.

    게이트웨이와 랩이 모두 구성되면 랩 사용자가 **Connect를** 클릭할 때 생성된 연결 파일에는 토큰 인증을 사용하여 연결하는 데 필요한 정보가 자동으로 포함됩니다.     

## <a name="next-steps"></a>다음 단계
원격 데스크톱 서비스: [원격 데스크톱 서비스 설명서에](/windows-server/remote/remote-desktop-services/Welcome-to-rds) 대해 자세히 알아보려면 다음 문서를 참조하세요.


