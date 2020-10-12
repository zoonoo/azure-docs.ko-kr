---
title: Azure DevTest Labs에서 원격 데스크톱 게이트웨이를 사용 하도록 랩 구성
description: RDP 포트를 노출 하지 않고도 랩 Vm에 안전 하 게 액세스할 수 있도록 원격 데스크톱 게이트웨이를 사용 하 여 Azure DevTest Labs에서 랩을 구성 하는 방법에 대해 알아봅니다.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: bc45a0c2953f8f84289fa01d4af72bf98544bd7f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87288075"
---
# <a name="configure-your-lab-in-azure-devtest-labs-to-use-a-remote-desktop-gateway"></a>Azure DevTest Labs에서 원격 데스크톱 게이트웨이를 사용 하도록 랩 구성
Azure DevTest Labs에서 랩에 대 한 원격 데스크톱 게이트웨이를 구성 하 여 RDP 포트를 노출 하지 않고도 랩 Vm (가상 머신)에 대 한 보안 액세스를 보장할 수 있습니다. 랩 사용자는 랩 사용자가 액세스 권한이 있는 모든 가상 컴퓨터를 보고 연결할 수 있는 중앙의 장소를 제공 합니다. **가상 컴퓨터** 페이지의 **연결** 단추를 클릭 하면 컴퓨터에 연결 하기 위해 열 수 있는 컴퓨터 관련 RDP 파일이 만들어집니다. 랩을 원격 데스크톱 게이트웨이에 연결 하 여 RDP 연결을 추가로 사용자 지정 하 고 보호할 수 있습니다. 

이 접근 방식은 랩 사용자가 게이트웨이 컴퓨터에 직접 인증 하거나 도메인에 가입 된 게이트웨이 컴퓨터에서 회사 자격 증명을 사용 하 여 컴퓨터에 연결할 수 있기 때문에 더 안전 합니다. 또한이 랩에서는 게이트웨이 컴퓨터에 대 한 토큰 인증을 사용 하 여 RDP 포트를 인터넷에 노출 하지 않고도 사용자가 랩 가상 컴퓨터에 연결할 수 있도록 지원 합니다. 이 문서에서는 랩 컴퓨터에 연결 하기 위해 토큰 인증을 사용 하는 랩을 설정 하는 방법에 대 한 예제를 안내 합니다.

## <a name="architecture-of-the-solution"></a>솔루션의 아키텍처

![솔루션의 아키텍처](./media/configure-lab-remote-desktop-gateway/architecture.png)

1. **연결** 단추를 선택 하면 [RDP 파일 콘텐츠 가져오기](/rest/api/dtl/virtualmachines/getrdpfilecontents) 작업을 호출 합니다. 1. 
1. RDP 파일 콘텐츠 가져오기 작업은 `https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}` 을 호출 하 여 인증 토큰을 요청 합니다.
    1. `{gateway-hostname}` Azure Portal 랩의 랩 **설정** 페이지에서 지정 된 게이트웨이 호스트 이름입니다. 
    1. `{lab-machine-name}` 연결 하려는 컴퓨터의 이름입니다.
    1. `{port-number}` 연결을 설정 해야 하는 포트입니다. 일반적으로이 포트는 3389입니다. 랩 VM이 DevTest Labs에서 [공유 IP](devtest-lab-shared-ip.md) 기능을 사용 하는 경우 포트는 달라 집니다.
1. 원격 데스크톱 게이트웨이는에서 Azure 함수로의 호출을 지연 `https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}` 하 여 인증 토큰을 생성 합니다. DevTest Labs 서비스는 요청 헤더에 함수 키를 자동으로 포함 합니다. 함수 키는 랩의 키 자격 증명 모음에 저장 됩니다. 랩에 대 한 **랩 설정** 페이지에서 **게이트웨이 토큰 비밀로** 표시할 비밀의 이름입니다.
1. Azure 함수는 게이트웨이 컴퓨터에 대해 인증서 기반 토큰 인증에 대 한 토큰을 반환 합니다.  
1. 그러면 RDP 파일 콘텐츠 가져오기 작업에서 인증 정보를 포함 하 여 전체 RDP 파일을 반환 합니다.
1. 원하는 RDP 연결 프로그램을 사용 하 여 RDP 파일을 엽니다. 모든 RDP 연결 프로그램에서 토큰 인증을 지 원하는 것은 아닙니다. 인증 토큰에는 함수 앱에 의해 설정 된 만료 날짜가 있습니다. 토큰이 만료 되기 전에 랩 VM에 대 한 연결을 설정 합니다.
1. 원격 데스크톱 게이트웨이 컴퓨터가 RDP 파일에서 토큰을 인증 하면 연결이 랩 컴퓨터에 전달 됩니다.

### <a name="solution-requirements"></a>솔루션 요구 사항
DevTest Labs 토큰 인증 기능을 사용 하려면 게이트웨이 컴퓨터, DNS (도메인 이름 서비스) 및 함수에 대 한 몇 가지 구성 요구 사항이 있습니다.

### <a name="requirements-for-remote-desktop-gateway-machines"></a>원격 데스크톱 게이트웨이 컴퓨터에 대 한 요구 사항
- HTTPS 트래픽을 처리 하려면 게이트웨이 컴퓨터에 TLS/SSL 인증서가 설치 되어 있어야 합니다. 인증서는 게이트웨이 팜에 대 한 부하 분산 장치의 FQDN (정규화 된 도메인 이름)과 일치 하거나 컴퓨터를 하나만 있는 경우 컴퓨터 자체의 FQDN과 일치 해야 합니다. 와일드 카드 TLS/SSL 인증서가 작동 하지 않습니다.  
- 게이트웨이 컴퓨터에 설치 된 서명 인증서입니다. [Create-SigningCertificate.ps1](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/tools/Create-SigningCertificate.ps1) 스크립트를 사용 하 여 서명 인증서를 만듭니다.
- 원격 데스크톱 게이트웨이에 대 한 토큰 인증을 지 원하는 [플러그형 인증](https://code.msdn.microsoft.com/windowsdesktop/Remote-Desktop-Gateway-517d6273) 모듈을 설치 합니다. 이러한 모듈의 한 가지 예는 `RDGatewayFedAuth.msi` [SYSTEM CENTER VIRTUAL MACHINE MANAGER (VMM) 이미지](/system-center/vmm/install-console?view=sc-vmm-1807)와 함께 제공 되는 것입니다. System Center에 대 한 자세한 내용은 [System center 설명서](/system-center/) 및 [가격 정보](https://www.microsoft.com/cloud-platform/system-center-pricing)를 참조 하세요.  
- 게이트웨이 서버는에 대 한 요청을 처리할 수 있습니다 `https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}` .

    게이트웨이-호스트 이름은 게이트웨이 팜의 부하 분산 장치 FQDN 또는 컴퓨터를 하나만 있는 경우 컴퓨터 자체의 fqdn입니다. 는 연결 하려는 `{lab-machine-name}` 랩 컴퓨터의 이름이 고,는 `{port-number}` 연결을 설정할 대상 포트입니다.  기본적으로이 포트는 3389입니다.  그러나 가상 컴퓨터가 DevTest Labs에서 [공유 IP](devtest-lab-shared-ip.md) 기능을 사용 하는 경우 포트는 달라 집니다.
- IIS (Internet Information Server) 용 [응용 프로그램 라우팅 요청](/iis/extensions/planning-for-arr/using-the-application-request-routing-module) 모듈을 사용 하 여 `https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}` 요청을 인증을 위한 토큰 가져오기 요청을 처리 하는 azure 함수로 리디렉션할 수 있습니다.


## <a name="requirements-for-azure-function"></a>Azure function에 대 한 요구 사항
Azure function은 형식이 인 요청 `https://{function-app-uri}/app/host/{lab-machine-name}/port/{port-number}` 을 처리 하 고 게이트웨이 컴퓨터에 설치 된 것과 동일한 서명 인증서를 기반으로 인증 토큰을 반환 합니다. 는 `{function-app-uri}` 함수에 액세스 하는 데 사용 되는 uri입니다. 함수 키는 요청 헤더에 자동으로 전달 됩니다. 샘플 함수를 보려면을 참조 하십시오 [https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/src/RDGatewayAPI/Functions/CreateToken.cs](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/src/RDGatewayAPI/Functions/CreateToken.cs) . 


## <a name="requirements-for-network"></a>네트워크 요구 사항

- 게이트웨이 컴퓨터에 설치 된 TLS/SSL 인증서와 연결 된 FQDN의 DNS는 게이트웨이 컴퓨터 또는 게이트웨이 컴퓨터 팜의 부하 분산 장치로 트래픽을 전송 해야 합니다.
- 랩 컴퓨터에서 개인 Ip를 사용 하는 경우 동일한 가상 네트워크를 공유 하거나 피어 링 가상 네트워크를 사용 하 여 게이트웨이 컴퓨터에서 랩 컴퓨터로의 네트워크 경로가 있어야 합니다.

## <a name="configure-the-lab-to-use-token-authentication"></a>토큰 인증을 사용 하도록 랩 구성 
이 섹션에서는 토큰 인증을 지 원하는 원격 데스크톱 게이트웨이 컴퓨터를 사용 하도록 랩을 구성 하는 방법을 보여 줍니다. 이 섹션에서는 원격 데스크톱 게이트웨이 팜 자체를 설정 하는 방법에 대해서는 설명 하지 않습니다. 해당 정보는이 문서의 끝에 있는 [원격 데스크톱 게이트웨이를 만드는 샘플](#sample-to-create-a-remote-desktop-gateway) 섹션을 참조 하세요. 

랩 설정을 업데이트 하기 전에 랩의 주요 자격 증명 모음에서 인증 토큰을 반환 하는 함수를 성공적으로 실행 하는 데 필요한 키를 저장 합니다. Azure Portal의 함수에 대 한 **관리** 페이지에서 함수 키 값을 가져올 수 있습니다. 키 자격 증명 모음에 암호를 저장 하는 방법에 대 한 자세한 내용은 [Key Vault에 암호 추가](../key-vault/secrets/quick-create-portal.md#add-a-secret-to-key-vault)를 참조 하세요. 나중에 사용할 암호의 이름을 저장 합니다.

랩의 키 자격 증명 모음 ID를 찾으려면 다음 Azure CLI 명령을 실행 합니다. 

```azurecli
az resource show --name {lab-name} --resource-type 'Microsoft.DevTestLab/labs' --resource-group {lab-resource-group-name} --query properties.vaultName
```

다음 단계를 사용 하 여 토큰 인증을 사용 하도록 랩을 구성 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **모든 서비스**를 선택한 다음, 목록에서 **DevTest Labs**를 선택합니다.
1. 랩 목록에서 **랩을**선택 합니다.
1. 랩의 페이지에서 **구성 및 정책**을 선택 합니다.
1. 왼쪽 메뉴의 **설정** 섹션에서 **랩 설정**을 선택 합니다.
1. **원격 데스크톱** 섹션에서 **게이트웨이 호스트 이름** 필드에 대 한 원격 데스크톱 서비스 게이트웨이 컴퓨터 또는 팜의 FQDN (정규화 된 도메인 이름) 또는 IP 주소를 입력 합니다. 이 값은 게이트웨이 컴퓨터에서 사용 되는 TLS/SSL 인증서의 FQDN과 일치 해야 합니다.

    ![랩 설정의 원격 데스크톱 옵션](./media/configure-lab-remote-desktop-gateway/remote-desktop-options-in-lab-settings.png)
1. **원격 데스크톱** 섹션에서 **게이트웨이 토큰** 비밀에 대해 이전에 만든 암호의 이름을 입력 합니다. 이 값은 함수 키 자체가 아니라, 랩의 키 자격 증명 모음에서 함수 키를 보유 하는 암호의 이름입니다.

    ![랩 설정의 게이트웨이 토큰 비밀](./media/configure-lab-remote-desktop-gateway/gateway-token-secret.png)
1. **저장** 변경.

    > [!NOTE] 
    > **저장**을 클릭 하면 [원격 데스크톱 게이트웨이의 사용 조건](https://www.microsoft.com/licensing/product-licensing/products)에 동의 하는 것입니다. 원격 게이트웨이에 대 한 자세한 내용은 [원격 데스크톱 서비스 시작](https://aka.ms/rds) 및 [원격 데스크톱 환경 배포](/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure)를 참조 하세요.


Automation을 통해 랩 구성이 선호 되는 경우 [Set-DevTestLabGateway.ps1](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/tools/Set-DevTestLabGateway.ps1) 샘플 PowerShell 스크립트를 참조 하 여 **게이트웨이 호스트 이름** 및 **게이트웨이 토큰 비밀** 설정을 설정 합니다. [Azure DevTest Labs GitHub 리포지토리](https://github.com/Azure/azure-devtestlab) 는 **게이트웨이 호스트 이름** 및 **게이트웨이 토큰 비밀** 설정을 사용 하 여 랩을 만들거나 업데이트 하는 Azure Resource Manager 템플릿도 제공 합니다.

## <a name="configure-network-security-group"></a>네트워크 보안 그룹 구성
랩의 보안을 강화 하기 위해 랩 가상 컴퓨터에서 사용 하는 가상 네트워크에 NSG (네트워크 보안 그룹)를 추가할 수 있습니다. NSG를 설정 하는 방법에 대 한 지침은 [네트워크 보안 그룹 만들기, 변경 또는 삭제](../virtual-network/manage-network-security-group.md)를 참조 하세요.

다음은 게이트웨이를 먼저 이동 하 여 랩 컴퓨터에 연결 하는 트래픽만 허용 하는 NSG 예제입니다. 이 규칙의 원본은 단일 게이트웨이 컴퓨터의 IP 주소 또는 게이트웨이 컴퓨터 앞에 있는 부하 분산 장치의 IP 주소입니다.

![네트워크 보안 그룹-규칙](./media/configure-lab-remote-desktop-gateway/network-security-group-rules.png)

## <a name="sample-to-create-a-remote-desktop-gateway"></a>원격 데스크톱 게이트웨이를 만드는 샘플

> [!NOTE] 
> 샘플 템플릿을 사용 하면 [원격 데스크톱 게이트웨이의 사용 조건](https://www.microsoft.com/licensing/product-licensing/products)에 동의 하는 것입니다. 원격 게이트웨이에 대 한 자세한 내용은 [원격 데스크톱 서비스 시작](https://aka.ms/rds) 및 [원격 데스크톱 환경 배포](/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure)를 참조 하세요.

[Azure DevTest Labs GitHub 리포지토리](https://github.com/Azure/azure-devtestlab) 는 DevTest Labs에서 토큰 인증 및 원격 데스크톱 게이트웨이를 사용 하는 데 필요한 리소스를 설정 하는 데 도움이 되는 몇 가지 샘플을 제공 합니다. 이러한 샘플에는 게이트웨이 컴퓨터, 랩 설정 및 함수 앱에 대 한 Azure Resource Manager 템플릿이 포함 되어 있습니다.

원격 데스크톱 게이트웨이 팜에 대 한 샘플 솔루션을 설정 하려면 다음 단계를 수행 합니다.

1. 서명 인증서를 만듭니다.  [Create-SigningCertificate.ps1](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/tools/Create-SigningCertificate.ps1)를 실행 합니다. 만든 인증서의 지문, 암호 및 Base64 인코딩을 저장 합니다.
2. TLS/SSL 인증서를 가져옵니다. TLS/SSL 인증서와 연결 된 FQDN은 사용자가 제어 하는 도메인에 대 한 인증서 여야 합니다. 이 인증서에 대 한 지문, 암호 및 Base64 인코딩을 저장 합니다. PowerShell을 사용 하 여 지문을 가져오려면 다음 명령을 사용 합니다.

    ```powershell
    $cer = New-Object System.Security.Cryptography.X509Certificates.X509Certificate;
    $cer.Import(‘path-to-certificate’);
    $hash = $cer.GetCertHashString()
    ```

    PowerShell을 사용 하 여 Base64 인코딩을 가져오려면 다음 명령을 사용 합니다.

    ```powershell
    [System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes(‘path-to-certificate’))
    ```
3. 에서 파일을 다운로드 [https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway) 합니다.

    템플릿은 동일한 기본 URI에서 몇 가지 다른 리소스 관리자 템플릿 및 관련 리소스에 액세스 해야 합니다. 및 RDGatewayFedAuth.msi의 모든 파일을 [https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/arm/gateway](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/arm/gateway) 저장소 계정의 blob 컨테이너에 복사 합니다.  
4. 에서 **azuredeploy.js를** 배포 [https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway) 합니다. 템플릿은 다음 매개 변수를 사용 합니다.
    - adminUsername – 필수 항목입니다.  게이트웨이 컴퓨터의 관리자 사용자 이름입니다.
    - adminPassword – 필수 항목입니다. 게이트웨이 컴퓨터의 관리자 계정에 대 한 암호입니다.
    - instanceCount – 만들 게이트웨이 컴퓨터의 수입니다.  
    - alwaysOn – 생성 된 Azure Functions 앱을 웜 상태로 유지할지 여부를 나타냅니다. Azure Functions 앱을 유지 하면 사용자가 먼저 랩 VM에 연결 하려고 할 때 지연이 발생 하지 않지만 비용에 영향을 미칩니다.  
    - tokenLifetime – 생성 된 토큰을 사용할 수 있는 시간입니다. 형식은 HH: MM: SS입니다.
    - sslCertificate – 게이트웨이 컴퓨터에 대 한 TLS/SSL 인증서의 Base64 인코딩입니다.
    - sslCertificatePassword – 게이트웨이 컴퓨터에 대 한 TLS/SSL 인증서의 암호입니다.
    - sslCertificateThumbprint-TLS/SSL 인증서의 로컬 인증서 저장소에서 id에 대 한 인증서 손 도장 (thumbprint)입니다.
    - signCertificate – 게이트웨이 컴퓨터의 서명 인증서에 대 한 Base64 인코딩입니다.
    - signCertificatePassword – 게이트웨이 컴퓨터의 서명 인증서에 대 한 암호입니다.
    - signCertificateThumbprint-서명 인증서의 로컬 인증서 저장소에서 id에 대 한 인증서 손 도장 (thumbprint)입니다.
    - _artifactsLocation – 모든 지원 리소스를 찾을 수 있는 URI 위치입니다. 이 값은 상대 경로가 아니라 정규화 된 UIR 이어야 합니다.
    - _artifactsLocationSasToken – 지원 리소스에 액세스 하는 데 사용 되는 SAS (공유 액세스 서명) 토큰입니다 (위치가 Azure storage 계정인 경우).

    다음 명령을 사용 하 여 Azure CLI를 사용 하 여 템플릿을 배포할 수 있습니다.

    ```azurecli
    az group deployment create --resource-group {resource-group} --template-file azuredeploy.json --parameters @azuredeploy.parameters.json -–parameters _artifactsLocation="{storage-account-endpoint}/{container-name}" -–parameters _artifactsLocationSasToken = "?{sas-token}"
    ```

    매개 변수에 대 한 설명은 다음과 같습니다.

    - {저장소-계정-엔드포인트}는를 실행 하 여 가져올 수 있습니다 `az storage account show --name {storage-acct-name} --query primaryEndpoints.blob` .  {저장소-계정-이름}은 업로드 한 파일을 보유 하는 저장소 계정의 이름입니다.  
    - {Container name}은 업로드 한 파일을 보유 하는 {저장소-계정 이름}의 컨테이너 이름입니다.  
    - {Sas 토큰}은를 실행 하 여 얻을 수 있습니다 `az storage container generate-sas --name {container-name} --account-name {storage-acct-name} --https-only –permissions drlw –expiry {utc-expiration-date}` . 
        - {저장소-계정-이름}은 업로드 한 파일을 보유 하는 저장소 계정의 이름입니다.  
        - {Container name}은 업로드 한 파일을 보유 하는 {저장소-계정 이름}의 컨테이너 이름입니다.  
        - {Utc-만료 날짜}는 SAS 토큰이 만료 되 고 SAS 토큰을 더 이상 사용 하 여 저장소 계정에 액세스할 수 없는 날짜 (UTC)입니다.

    템플릿 배포 출력에서 Gfqdn 및 Gip의 값을 기록 합니다. 또한 함수 [앱 설정](../azure-functions/functions-how-to-use-azure-function-app-settings.md) 탭에서 찾을 수 있는 새로 생성 된 함수에 대 한 함수 키 값을 저장 해야 합니다.
5. TLS/SSL 인증서의 FQDN이 이전 단계에서 서브넷 Ip의 IP 주소로 전달 되도록 DNS를 구성 합니다.

    원격 데스크톱 게이트웨이 팜이 만들어지고 적절 한 DNS 업데이트가 수행 된 후에는 DevTest Labs의 랩에서 사용할 준비가 된 것입니다. 배포한 게이트웨이 컴퓨터를 사용 하도록 **게이트웨이 호스트 이름** 및 **게이트웨이 토큰 비밀** 설정을 구성 해야 합니다. 

    > [!NOTE]
    > 랩 컴퓨터에서 개인 Ip를 사용 하는 경우 동일한 가상 네트워크를 공유 하거나 피어 링 가상 네트워크를 사용 하 여 게이트웨이 컴퓨터에서 랩 컴퓨터로의 네트워크 경로가 있어야 합니다.

    게이트웨이와 랩을 모두 구성 하면 랩 사용자가 **연결** 을 클릭할 때 생성 된 연결 파일에는 토큰 인증을 사용 하 여 연결 하는 데 필요한 정보가 자동으로 포함 됩니다.     

## <a name="next-steps"></a>다음 단계
원격 데스크톱 서비스에 대해 자세히 알아보려면 다음 문서를 참조 하세요. [원격 데스크톱 서비스 설명서](/windows-server/remote/remote-desktop-services/Welcome-to-rds)
