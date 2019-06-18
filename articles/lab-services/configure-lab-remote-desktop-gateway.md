---
title: 에 원격 데스크톱 게이트웨이 사용 하 여 Azure DevTest Labs에서 랩 구성 | Microsoft Docs
description: RDP 포트를 노출 하지 않고도 랩 Vm에 대 한 보안 액세스를 확인 하려면 원격 데스크톱 게이트웨이 사용 하 여 Azure DevTest Labs에서 랩을 구성 하는 방법에 알아봅니다.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: spelluru
ms.openlocfilehash: 430734878c01d10a4e7dd385dc75d8d502a2d82c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079003"
---
# <a name="configure-your-lab-in-azure-devtest-labs-to-use-a-remote-desktop-gateway"></a>원격 데스크톱 게이트웨이 사용 하 여 Azure DevTest Labs에서 랩 구성
Azure DevTest Labs에서 랩 가상 컴퓨터 (Vm)에 대 한 보안 액세스를 위해 랩에 대 한 원격 데스크톱 게이트웨이 RDP 포트를 노출 하지 않고도 구성할 수 있습니다. 랩을 보고 액세스할 수 있는 모든 가상 컴퓨터에 연결할 랩 사용자에 대 한 중앙 위치를 제공 합니다. 합니다 **Connect** 단추를 **가상 머신** 페이지 컴퓨터에 연결 하 여 컴퓨터 전용 RDP 파일을 만듭니다. 추가 사용자 지정 하 고 랩 원격 데스크톱 게이트웨이를 연결 하 여 RDP 연결을 보호할 수 있습니다. 

이 방식은 랩 사용자는 게이트웨이 컴퓨터에 직접 인증 하거나 해당 컴퓨터에 연결할 게이트웨이 도메인에 가입 된 컴퓨터에서 회사 자격 증명을 사용할 수 있으므로 더 안전 합니다. 랩은 사용자가 인터넷에 노출 하는 RDP 포트가 필요 없이 해당 랩 가상 컴퓨터에 연결할 수 있도록 하는 게이트웨이 컴퓨터에 대 한 토큰 인증을 사용 하 여도 지원 합니다. 이 문서에서는 랩 컴퓨터에 연결할 토큰 인증을 사용 하는 랩을 설정 하는 방법은 예제를 안내 합니다.

## <a name="architecture-of-the-solution"></a>솔루션의 아키텍처

![솔루션의 아키텍처](./media/configure-lab-remote-desktop-gateway/architecture.png)

1. [가져오기 RDP 파일 콘텐츠](/rest/api/dtl/virtualmachines/getrdpfilecontents) 선택 하면 작업 이라고 합니다 **연결** button.1 합니다. 
1. 가져올 RDP 파일 콘텐츠 작업 호출 `https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}` 인증 토큰을 요청 합니다.
    1. `{gateway-hostname}` 게이트웨이 호스트 이름에 지정 합니다 **랩 설정** Azure portal에서 랩에 대 한 페이지입니다. 
    1. `{lab-machine-name}` 연결 하려는 컴퓨터의 이름이입니다.
    1. `{port-number}` 연결을 설정 해야 하는 포트가입니다. 일반적으로이 포트는 3389입니다. 랩 VM을 사용 하는 경우는 [공유 IP](devtest-lab-shared-ip.md) 기능 DevTest labs에서 포트는 다를 수 있습니다.
1. 식에서 호출을 지연 하는 원격 데스크톱 게이트웨이 `https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}` 인증 토큰을 생성 하는 Azure 함수입니다. DevTest Labs 서비스는 자동으로 요청 헤더에 함수 키를 포함합니다. 함수 키는 랩의 키 자격 증명 모음에 저장 합니다. 로 표시 하는 비밀에 대 한 이름을 **게이트웨이 토큰 비밀** 에 **랩 설정** 랩에 대 한 페이지.
1. Azure 함수는 게이트웨이 컴퓨터에 대 한 인증서 기반 토큰 인증에 대 한 토큰을 반환 해야 합니다.  
1. 가져오기 RDP 파일 콘텐츠를 작업 다음 인증 정보를 포함 하는 전체 RDP 파일을 반환 합니다.
1. 기본 RDP 연결 프로그램을 사용 하 여 RDP 파일을 열면 됩니다. 모든 RDP 연결 프로그램 토큰 인증을 지원 해야 합니다. 인증 토큰 만료 날짜가 없는, 함수 앱으로 설정 합니다. 토큰 만료 되기 전에 랩 VM에 대 한 연결을 확인 합니다.
1. RDP 파일에는 토큰을 인증 하는 원격 데스크톱 게이트웨이 컴퓨터에 일단 연결 실습실 시스템으로 전달 됩니다.

### <a name="solution-requirements"></a>솔루션 요구 사항
DevTest Labs 토큰 인증 기능을 사용 하려면 게이트웨이 컴퓨터, 도메인 이름 서비스 (DNS) 및 함수에 대 한 몇 가지 구성 요구 사항이 있습니다.

### <a name="requirements-for-remote-desktop-gateway-machines"></a>원격 데스크톱 게이트웨이 컴퓨터에 대 한 요구 사항
- SSL 인증서는 HTTPS 트래픽을 처리 하기 위해 게이트웨이 컴퓨터에 설치 되어야 합니다. 인증서가 컴퓨터 하나만 있으면 게이트웨이 팜 또는 자체 컴퓨터의 FQDN에 대 한 load balancer의 정규화 된 도메인 이름 (FQDN)가 일치 해야 합니다. 와일드 카드 SSL 인증서가 작동 하지 않습니다.  
- 게이트웨이 컴퓨터에 설치 된 서명 인증서. 서명 인증서를 사용 하 여 만들 [SigningCertificate.ps1 만들기](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/tools/Create-SigningCertificate.ps1) 스크립트입니다.
- 설치 합니다 [플러그형 인증](https://code.msdn.microsoft.com/windowsdesktop/Remote-Desktop-Gateway-517d6273) 원격 데스크톱 게이트웨이에 대 한 토큰 인증을 지 원하는 모듈입니다. 이러한 모듈의 예로 `RDGatewayFedAuth.msi` 와 함께 제공 되 [System Center Virtual Machine Manager (VMM) 이미지](/system-center/vmm/install-console?view=sc-vmm-1807)합니다. System Center에 대 한 자세한 내용은 참조 하세요. [System Center 설명서](https://docs.microsoft.com/system-center/) 하 고 [가격 책정 세부 정보](https://www.microsoft.com/cloud-platform/system-center-pricing)합니다.  
- 게이트웨이 서버에 대 한 요청을 처리할 수 `https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}`입니다.

    컴퓨터가 하나만 있으면 게이트웨이-호스트 이름은 게이트웨이 팜의 부하 분산 장치의 FQDN 또는 컴퓨터의 FQDN. 합니다 `{lab-machine-name}` 에 연결 하려는 랩 컴퓨터의 이름 및 `{port-number}` 는 연결을 설정할 수는 포트입니다.  기본적으로이 포트는 3389입니다.  그러나 가상 머신을 사용 하는 경우는 [공유 IP](devtest-lab-shared-ip.md) 기능 DevTest labs에서 포트는 다를 수 있습니다.
- 합니다 [응용 프로그램 요청 라우팅](/iis/extensions/planning-for-arr/using-the-application-request-routing-module) 리디렉션할 모듈에 대 한 인터넷 정보 서버 (IIS)를 사용할 수 있습니다 `https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}` 인증 토큰을 가져오기 위해 요청을 처리 하는 azure 함수를 요청 합니다.


## <a name="requirements-for-azure-function"></a>Azure 함수에 대 한 요구 사항
Azure 함수 핸들 요청과 형식의 `https://{function-app-uri}/app/host/{lab-machine-name}/port/{port-number}` 및 인증 토큰을 기반으로 동일한 반환 게이트웨이 컴퓨터에 설치 된 인증서를 서명 합니다. `{function-app-uri}` uri 함수에 액세스 하는 데 사용 됩니다. 기능 키는 요청 헤더에 자동으로 전달 됩니다. 샘플 함수를 참조 하세요 [ https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/src/RDGatewayAPI/Functions/CreateToken.cs ](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/src/RDGatewayAPI/Functions/CreateToken.cs)합니다. 


## <a name="requirements-for-network"></a>네트워크 요구 사항

- 게이트웨이 컴퓨터에 설치 된 SSL 인증서와 연결 된 FQDN에 DNS는 게이트웨이 컴퓨터에서 게이트웨이 컴퓨터 팜의 부하 분산 장치에 트래픽을 해야 합니다.
- 랩 컴퓨터에서 개인 Ip를 사용 하는 경우 동일한 가상 네트워크 공유 또는 피어 링 된 가상 네트워크를 사용 하 여을 통해 랩 컴퓨터에 게이트웨이 컴퓨터에서 네트워크 경로 여야 합니다.

## <a name="configure-the-lab-to-use-token-authentication"></a>토큰 인증을 사용 하도록 랩 구성 
이 섹션에는 토큰 인증을 지 원하는 원격 데스크톱 게이트웨이 컴퓨터를 사용 하는 환경을 구성 하는 방법을 보여 줍니다. 이 섹션에는 자체는 원격 데스크톱 게이트웨이 팜을 설정 하는 방법을 다루지 않습니다. 해당 정보에 대 한 참조를 [원격 데스크톱 게이트웨이 만드는 샘플](#sample-to-create-a-remote-desktop-gateway) 이 문서의 끝에 있는 섹션입니다. 

랩 설정을 업데이트 하기 전에 랩의 key vault에 인증 토큰을 반환 하는 함수를 성공적으로 실행 하는 데 필요한 키를 저장 합니다. 함수 키 값을 가져올 수는 **관리** Azure portal에서 함수에 대 한 페이지입니다. Key vault에서 비밀을 저장 하는 방법에 대 한 자세한 내용은 참조 하세요. [Key Vault에 비밀 추가](../key-vault/quick-create-portal.md#add-a-secret-to-key-vault)합니다. 나중에 사용할 암호의 이름을 저장 합니다.

랩의 key vault의 ID를 찾으려면 다음 Azure CLI 명령을 실행 합니다. 

```azurecli
az resource show --name {lab-name} --resource-type 'Microsoft.DevTestLab/labs' --resource-group {lab-resource-group-name} --query properties.vaultName
```

랩에 토큰 인증을 사용 하 여 이러한 단계를 사용 하 여 구성 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **모든 서비스**를 선택한 다음, 목록에서 **DevTest Labs**를 선택합니다.
1. 랩 목록에서 선택 하 **랩**합니다.
1. 랩의 페이지 선택 **구성 및 정책**합니다.
1. 왼쪽된 메뉴에서에 **설정을** 섹션에서 **랩 설정**합니다.
1. 에 **원격 데스크톱** 섹션에서 정규화 된 도메인 이름 (FQDN) 또는 원격 데스크톱 서비스 게이트웨이 컴퓨터의 IP 주소를 입력 하거나에 대 한 팜 합니다 **게이트웨이 호스트 이름** 필드입니다. 이 값에는 게이트웨이 컴퓨터에서 사용 된 SSL 인증서의 FQDN과 일치 해야 합니다.

    ![랩 설정에서 원격 데스크톱 옵션](./media/configure-lab-remote-desktop-gateway/remote-desktop-options-in-lab-settings.png)
1. 에 **원격 데스크톱** 섹션에 대 한 **게이트웨이 토큰이** 암호를 이전에 만든 암호의 이름을 입력 합니다. 이 값이 아닌 자체 함수 키가 있지만 함수 키를 보유 하는 랩의 key vault의 비밀의 이름입니다.

    ![랩 설정에서 게이트웨이 토큰 암호](./media/configure-lab-remote-desktop-gateway/gateway-token-secret.png)
1. **저장** 변경 합니다.

    > [!NOTE] 
    > 클릭 하 여 **저장**에 동의 하 게 [원격 데스크톱 게이트웨이 사용 약관](https://www.microsoft.com/licensing/product-licensing/products)합니다. 원격 게이트웨이 대 한 자세한 내용은 참조 하세요. [원격 데스크톱 서비스 시작](https://aka.ms/rds) 하 고 [원격 데스크톱 환경 배포](/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure)합니다.


자동화를 통해 랩 구성 기본 설정 된 경우 참조 [집합 DevTestLabGateway.ps1](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/tools/Set-DevTestLabGateway.ps1) 설정 하는 샘플 PowerShell 스크립트에 대 한 **게이트웨이 호스트 이름** 고 **게이트웨이 토큰 암호**설정 합니다. 합니다 [Azure DevTest Labs GitHub 리포지토리](https://github.com/Azure/azure-devtestlab) 만들거나 사용 하 여 랩을 업데이트 하는 Azure Resource Manager 템플릿을 제공 합니다 **게이트웨이 호스트 이름** 및 **게이트웨이 토큰 암호**설정 합니다.

## <a name="configure-network-security-group"></a>네트워크 보안 그룹 구성
랩을 추가로 보호 하려면 랩 가상 컴퓨터에서 사용 하는 가상 네트워크에 네트워크 보안 그룹 (NSG)을 추가할 수 있습니다. 자세한 내용은 NSG를 설정 하는 방법 [만들기, 변경 또는 네트워크 보안 그룹을 삭제](../virtual-network/manage-network-security-group.md)합니다.

다음은 예제 NSG는 실습실 시스템에 연결할 게이트웨이 통해 먼저 전달 되는 트래픽만 허용 합니다. 이 규칙에서 단일 게이트웨이 컴퓨터의 IP 주소 또는 게이트웨이 컴퓨터 앞에 load balancer의 IP 주소입니다.

![네트워크 보안 그룹-규칙](./media/configure-lab-remote-desktop-gateway/network-security-group-rules.png)

## <a name="sample-to-create-a-remote-desktop-gateway"></a>원격 데스크톱 게이트웨이 만드는 샘플

> [!NOTE] 
> 샘플 템플릿을 사용 하 여에 동의 [원격 데스크톱 게이트웨이 사용 약관](https://www.microsoft.com/licensing/product-licensing/products)합니다. 원격 게이트웨이 대 한 자세한 내용은 참조 하세요. [원격 데스크톱 서비스 시작](https://aka.ms/rds) 하 고 [원격 데스크톱 환경 배포](/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure)합니다.

합니다 [Azure DevTest Labs GitHub 리포지토리](https://github.com/Azure/azure-devtestlab) DevTest Labs를 사용 하 여 토큰 인증 및 원격 데스크톱 게이트웨이 사용 하는 데 필요한 리소스를 설치 하는 데는 몇 가지 샘플을 제공 합니다. 이러한 샘플에는 게이트웨이 컴퓨터, 랩 설정 및 함수 앱에 대 한 Azure Resource Manager 템플릿 포함 됩니다.

원격 데스크톱 게이트웨이 팜에 대 한 샘플 솔루션을 설정 하려면 다음이 단계를 따릅니다.

1. 서명 인증서를 만듭니다.  실행할 [만들 SigningCertificate.ps1](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/tools/Create-SigningCertificate.ps1)합니다. 지문, 암호 및 생성된 된 인증서의 Base64 인코딩을 저장 합니다.
2. SSL 인증서를 가져옵니다. 제어 되는 도메인에 대 한 SSL 인증서와 연결 된 FQDN 이어야 합니다. 지문, 암호 및이 인증서의 Base64 인코딩을 저장 합니다. PowerShell을 사용 하 여 지문을 가져오려면 다음 명령을 사용 합니다.

    ```powershell
    $cer = New-Object System.Security.Cryptography.X509Certificates.X509Certificate;
    $cer.Import(‘path-to-certificate’);
    $hash = $cer.GetCertHashString()
    ```

    PowerShell을 사용 하 여 Base64 인코딩를 가져오려면 다음 명령을 사용 합니다.

    ```powershell
    [System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes(‘path-to-certificate’))
    ```
3. 파일을 다운로드 [ https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway ](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway)합니다.

    서식 파일에는 몇 가지 다른 Resource Manager 템플릿과 같은 기본 URI에 관련 된 리소스 액세스를 해야합니다. 모든 파일을 복사 [ https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/arm/gateway ](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/arm/gateway) 및 저장소 계정에 blob 컨테이너에 RDGatewayFedAuth.msi입니다.  
4. 배포할 **azuredeploy.json** 에서 [ https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway ](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway)합니다. 다음 매개 변수를 사용 하는 템플릿:
    - adminUsername – 필요 합니다.  게이트웨이 컴퓨터에 대 한 관리자 사용자 이름입니다.
    - adminPassword – 필요 합니다. 게이트웨이 컴퓨터에 대 한 관리자 계정의 암호입니다.
    - instanceCount – 게이트웨이 컴퓨터를 만들 수 있습니다.  
    - alwaysOn-여부 웜 상태에서 만든된 Azure 함수 앱을 유지할지 여부를 나타냅니다. Azure Functions 앱을 유지 사용자는 먼저 랩 VM에 연결 하려고 하지만 비용 영향 않은 경우 지연을 방지 됩니다.  
    - tokenLifetime – 만든된 토큰은 유효 기간. 형식은 hh: mm:입니다.
    - sslCertificate – The Base64 게이트웨이 컴퓨터에 대 한 SSL 인증서의 인코딩입니다.
    - sslCertificatePassword – 게이트웨이 컴퓨터에 대 한 SSL 인증서의 암호입니다.
    - sslCertificateThumbprint-SSL 인증서의 로컬 인증서 저장소에서 id에 대 한 인증서 지문입니다.
    - signCertificate –의 Base64 인코딩을 게이트웨이 컴퓨터에 대 한 인증서를 서명 합니다.
    - signCertificatePassword – 게이트웨이 컴퓨터에 대 한 인증서를 서명 하는 것에 대 한 암호입니다.
    - signCertificateThumbprint-서명 인증서의 로컬 인증서 저장소에서 id에 대 한 인증서 지문입니다.
    - _artifactsLocation – 모든 지원 리소스를 찾을 수 있는 URI 위치입니다. 이 값을 정규화 UIR 상대 경로가 아닌 여야 합니다.
    - _artifactsLocationSasToken – Azure 저장소 계정 위치가 경우 지원 리소스에 액세스 하는 데 공유 액세스 서명 (SAS) 토큰입니다.

    Azure CLI를 사용 하 여 다음 명령을 사용 하 여 템플릿을 배포할 수 있습니다.

    ```azurecli
    az group deployment create --resource-group {resource-group} --template-file azuredeploy.json --parameters @azuredeploy.parameters.json -–parameters _artifactsLocation=”{storage-account-endpoint}/{container-name}” -–parameters _artifactsLocationSasToken = “?{sas-token}”
    ```

    매개 변수에 대 한 설명은 다음과 같습니다.

    - {0} 저장소 계정 끝점을 (를) 실행 하 여 얻을 수 있습니다 `az storage account show --name {storage-acct-name} --query primaryEndpoints.blob`합니다.  {저장소 acct name}에 업로드 한 파일을 포함 하는 저장소 계정의 이름입니다.  
    - {Container name}에 {저장소 계정 이름}에 업로드 한 파일을 보관 하는 컨테이너의 이름입니다.  
    - {Sas 토큰을 (를) 실행 하 여 얻을 수 있습니다 `az storage container generate-sas --name {container-name} --account-name {storage-acct-name} --https-only –permissions drlw –expiry {utc-expiration-date}`합니다. 
        - {저장소 acct name}에 업로드 한 파일을 포함 하는 저장소 계정의 이름입니다.  
        - {Container name}에 {저장소 계정 이름}에 업로드 한 파일을 보관 하는 컨테이너의 이름입니다.  
        - {Utc-만료-date}는 날짜가 고, utc는 SAS 토큰 만료 되 고 저장소 계정에 액세스 하려면 SAS 토큰을 더 이상 사용할 수 없습니다.

    GatewayFQDN 및 gatewayIP 템플릿 배포 출력에서 값을 기록 합니다. 에 있는 새로 만든된 함수에 대 한 함수 키의 값을 저장 해야 합니다 [함수 앱 설정](../azure-functions/functions-how-to-use-azure-function-app-settings.md) 탭 합니다.
5. 이전 단계에서 gatewayIP의 IP 주소를 해당 FQDN의 SSL 인증서를 전달 하도록 DNS를 구성 합니다.

    원격 데스크톱 게이트웨이 팜이 만들어진 후 적절 한 DNS 업데이트가 이루어집니다 DevTest Labs에서 랩에 사용할 준비가 완료 된 것입니다. 합니다 **게이트웨이 호스트 이름** 하 고 **게이트웨이 토큰 암호** 설정을 배포한 게이트웨이 컴퓨터를 사용 하도록 구성 해야 합니다. 

    > [!NOTE]
    > 랩 컴퓨터에서 개인 Ip를 사용 하는 경우 동일한 가상 네트워크 공유 또는 피어 링된 된 가상 네트워크를 사용 하 여을 통해 랩 컴퓨터에 게이트웨이 컴퓨터에서 네트워크 경로 여야 합니다.

    랩 사용자가 클릭할 때 연결 파일을 만들 랩 및 게이트웨이 구성 합니다 **Connect** 토큰 인증을 사용 하 여 연결 하는 데 필요한 정보를 자동으로 포함 됩니다.     

## <a name="next-steps"></a>다음 단계
원격 데스크톱 서비스에 자세히 알아보려면 다음 문서를 참조 하세요. [원격 데스크톱 서비스 설명서](/windows-server/remote/remote-desktop-services/Welcome-to-rds)


