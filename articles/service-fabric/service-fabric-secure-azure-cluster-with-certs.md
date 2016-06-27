<properties
   pageTitle="인증서를 사용하여 서비스 패브릭 클러스터 보호 | Microsoft Azure"
   description="X.509 인증서를 사용하여 서비스 패브릭 클러스터를 보호하는 방법"
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/27/2016"
   ms.author="chackdan"/>

# 인증서를 사용하여 Azure에서 서비스 패브릭 클러스터 보호

Azure 서비스 패브릭 클러스터는 사용자가 소유하는 리소스입니다. 특히 실행 중인 프로덕션 워크로드가 있는 경우 리소스에 대한 무단 액세스를 방지하기 위해 보안을 설정해야 합니다. X.509 인증서를 사용하여 보안 서비스 패브릭 클러스터를 설정하려면 Azure 주요 자격 증명 모음에 업로드하고 클러스터 만들기 프로세스에서 사용하는 하나 이상의 서버 X.509 인증서가 필요합니다.

이 문서는 클러스터 생성 프로세스의 [3단계: 보안 구성](service-fabric-cluster-creation-via-portal.md#step-3--configure-security)에 해당합니다. 서비스 패브릭에서 X.509 인증서를 사용하는 방법에 대한 자세한 내용은 [클러스터 보안 시나리오](service-fabric-cluster-security.md)를 참조하세요.

세 가지 고유한 단계가 있습니다.

1. X.509 인증서를 획득합니다.
2. Azure 키 자격 증명 모음에 인증서를 업로드합니다.
3. 인증서의 위치 및 세부 정보를 서비스 패브릭 클러스터 만들기 프로세스에 제공합니다.

<a id="acquirecerts"></a>
## 1단계: X.509 인증서를 획득합니다.

프로덕션 워크로드를 실행하는 클러스터의 경우 클러스터 보호를 위해 [CA(인증 기관)](https://en.wikipedia.org/wiki/Certificate_authority)로 서명된 X.509 인증서를 사용해야 합니다. 이러한 인증서를 얻는 방법에 대한 자세한 내용은 [방법: 인증서 가져오기](http://msdn.microsoft.com/library/aa702761.aspx)를 참조하세요.

테스트 목적으로만 사용하는 클러스터의 경우 자체 서명된 인증서를 사용하도록 선택할 수 있습니다. 아래 2.5단계는 수행하는 방법을 설명합니다.

## 2단계: 키 자격 증명 모음에 X.509 인증서를 업로드합니다.

이 단계는 복잡한 프로세스이므로 사용자를 위해 Git 리포지토리에 업로드된 PowerShell 모듈이 있습니다.

### 2\.1단계
컴퓨터에 Azure PowerShell 1.0 이상이 설치되어 있는지 확인합니다. 이전에 수행한 적이 없는 경우 [Azure PowerShell 설치 및 구성 방법](../powershell-install-configure.md)에 요약된 단계에 따르세요.

### 2\.2단계
이 [Git 리포지토리](https://github.com/ChackDan/Service-Fabric/tree/master/Scripts/ServiceFabricRPHelpers)의 *ServiceFabricRPHelpers* 폴더를 컴퓨터로 복사합니다.

### 2\.3단계
PowerShell 창을 열고 모듈을 다운로드한 디렉터리로 이동합니다. 그런 후 다음 명령을 사용하여 모듈을 가져옵니다.

```powershell
Import-Module .\ServiceFabricRPHelpers.psm1
```

### 2\.4단계
이미 획득한 인증서를 사용 중인경우 이 단계의 절차를 수행합니다. 그렇지 않으면 자체 서명된 인증서를 만들어 주요 자격 증명 모음에 배포하는 방법을 설명하는 2.5단계로 건너뜁니다.

기존 리소스 그룹 및 주요 자격 증명 모음을 사용하여 인증서를 저장하거나, 기존 항목이 없는 경우 새 리소스 그룹 및/또는 주요 자격 증명 모음을 만들 수 있습니다. 이 스크립트를 사용하여 배포를 지원하도록 기존 주요 자격 증명 모음을 먼저 구성해야 합니다.

```powershell
Login-AzureRmAccount

Set-AzureRmKeyVaultAccessPolicy -VaultName <Name of the Vault> -ResourceGroupName <string> -EnabledForDeployment
```

리소스 그룹 및 주요 자격 증명 모음에 인증서를 업로드하려면 다음 스크립트를 실행합니다. 리소스 그룹 및 주요 자격 증명 모음이 아직 존재하지 않는 경우 만들어지게 됩니다.

```powershell
Login-AzureRmAccount
Invoke-AddCertToKeyVault -SubscriptionId <your subscription id> -ResourceGroupName <string> -Location <region> -VaultName <Name of the Vault> -CertificateName <Name of the Certificate> -Password <Certificate password> -UseExistingCertificate -ExistingPfxFilePath <Full path to the .pfx file>
```
다음은 채워진 스크립트 예제입니다.

```powershell
Login-AzureRmAccount
Invoke-AddCertToKeyVault -SubscriptionId 35389201-c0b3-405e-8a23-9f1450994307 -ResourceGroupName chackdankeyvault4doc -Location westus -VaultName chackdankeyvault4doc  -CertificateName chackdantestcertificate2 -Password abcd123 -UseExistingCertificate -ExistingPfxFilePath C:\MyCertificates\ChackdanTestCertificate.pfx
```

스크립트가 성공적으로 완료되면 아래와 같은 출력을 얻게 되며 이 출력은 3단계(보안 클러스터 설정)에 필요합니다.

```
Certificate Thumbprint: 2118C3BCE6541A54A0236E14ED2CCDD77EA4567A

SourceVault /Resource ID of the key vault :  /subscriptions/35389201-c0b3-405e-8a23-9f1450994307/resourceGroups/chackdankeyvault4doc/providers/Microsoft.KeyVault/vaults/chackdankeyvault4doc

Certificate URL /URL to the certificate location in the key vault : https://chackdankeyvalut4doc.vault.azure.net:443/secrets/chackdantestcertificate3/ebc8df6300834326a95d05d90e0701ea
```

보안 클러스터를 설정하는 데 필요한 정보를 얻었습니다. 3단계로 이동합니다.

### 2\.5단계
인증서가 *없고* 자체 서명된 새 인증서를 만들어 주요 자격 증명 모음에 업로드하려는 경우 다음 단계를 수행합니다. 자체 서명된 인증서는 제품 클러스터가 아닌 테스트 클러스터에서만 사용해야 합니다.

기존 리소스 그룹 및 주요 자격 증명 모음을 사용하여 인증서를 저장하거나, 기존 항목이 없는 경우 새 리소스 그룹 및/또는 주요 자격 증명 모음을 만들 수 있습니다. 이 스크립트를 사용하여 배포를 지원하도록 기존 주요 자격 증명 모음을 먼저 구성해야 합니다.

```powershell
Login-AzureRmAccount
Set-AzureRmKeyVaultAccessPolicy -VaultName <Name of the Vault> -ResourceGroupName <string> -EnabledForDeployment
```

다음 스크립트는 새 리소스 그룹 및/또는 주요 자격 증명 모음을 아직 없는 경우 새로 만들고, 자체 서명된 인증서를 만들어 주요 자격 증명 모음에 업로드하고, 새 인증서를 *OutputPath*에 출력합니다.

```powershell
Login-AzureRmAccount
Invoke-AddCertToKeyVault -SubscriptionId <you subscription id> -ResourceGroupName <string> -Location <region> -VaultName <Name of the Vault> -CertificateName <Name of the Certificate> -Password <Certificate password> -CreateSelfSignedCertificate -DnsName <string- see note below.> -OutputPath <Full path to the .pfx file>
```
*DnsName* 문자열은 복사할 인증서가 CloneCert 매개 변수에서 지정되지 않은 경우 인증서의 주체 대체 이름 확장명에 넣을 하나 이상의 DNS 이름을 지정합니다. 첫 번째 DNS 이름은 주체 이름으로도 저장됩니다. 서명 인증서가 지정되지 않은 경우 첫 번째 DNS 이름이 발급자 이름으로도 저장됩니다. *Invoke AddCertToKeyVault* cmdlet은 [New-SelfSignedCertificate cmdlet](https://technet.microsoft.com/library/hh848633.aspx)을 사용하여 자체 서명된 인증서를 만듭니다.

다음은 채워진 스크립트 예제입니다.

```powershell
Login-AzureRmAccount
Invoke-AddCertToKeyVault -SubscriptionId 35389201-c0b3-405e-8a23-9f1450994307 -ResourceGroupName chackdankeyvault4doc -Location westus -VaultName chackdankeyvault4doc  -CertificateName chackdantestcertificate3 -Password abcd123 -CreateSelfSignedCertificate -DnsName www.chackdan.westus.azure.com -OutputPath C:\MyCertificates
```

스크립트가 성공적으로 완료되면 아래와 같은 출력을 얻게 됩니다. 이는 3단계에 필요합니다.

```
Certificate Thumbprint: 64881409F4D86498C88EEC3697310C15F8F1540F

SourceVault /Resource ID of the key vault : /subscriptions/35389201-c0b3-405e-8a23-9f1450994307/resourceGroups/chackdankeyvault4doc/providers/Microsoft.KeyVault/vaults/chackdankeyvault4doc

Certificate URL /URL to the certificate location in the key vault: https://chackdankeyvalut4doc.vault.azure.net:443/secrets/chackdantestcertificate3/fvc8df6300834326a95d05d90e0720ea
```

## 3단계: 보안 클러스터 설정

사용자 인증서가 Azure 주요 자격 증명 모음에 업로드되면 해당 인증서로 보호되는 클러스터를 만들 수 있습니다. 이 단계는 클러스터 생성 프로세스의 [3단계: 보안 구성](service-fabric-cluster-creation-via-portal.md#step-3--configure-security)에 해당하며 보안 구성을 설정하는 방법을 보여 줍니다.

>[AZURE.NOTE]
필요한 인증서는 보안 구성 아래의 노드 유형 수준에서 지정됩니다. 클러스터에 있는 모든 노드 유형에 대해 이를 지정해야 합니다. 이 문서에서는 포털을 사용하여 수행하는 방법을 안내하지만 Azure 리소스 관리자 템플릿을 사용하여 동일하게 수행할 수 있습니다.

![Azure 포털의 보안 구성 스크린 샷][SecurityConfigurations_01]

### 필수 매개 변수

- **보안 모드** **X509 인증서**를 선택하여 X.509 인증서로 보호되는 클러스터를 설정합니다.
- **클러스터 보호 수준** 각 값의 의미를 이해하려면 이 [보호 수준 문서](https://msdn.microsoft.com/library/aa347692.aspx)를 참조하세요. 여기에서는 3개의 값(EncryptAndSign, Sign 및 없음)을 허용하지만 현재 작업을 알 수 없는 경우 EncryptAndSign의 기본을 유지하는 것이 좋습니다.
- **원본 자격 증명 모음** 주요 자격 증명 모음의 리소스 ID를 나타냅니다. 이 형식이어야 합니다.

    ```
    /subscriptions/<Sub ID>/resourceGroups/<Resource group name>/providers/Microsoft.KeyVault/vaults/<vault name>
    ```

- **인증서 URL** 인증서가 업로드된 주요 자격 증명 모음의 위치 URL을 의미합니다. 이 형식이어야 합니다.

    ```
    https://<name of the vault>.vault.azure.net:443/secrets/<exact location>
```
```
    https://chackdan-kmstest-eastus.vault.azure.net:443/secrets/MyCert/6b5cc15a753644e6835cb3g3486b3812
    ```

- **인증서 지문** 이전에 지정한 URL에서 찾을 수 있는 인증서의 지문을 의미합니다.

### 선택적 매개 변수

 클러스터의 작업을 수행하는 데 사용하는 클라이언트 컴퓨터에 추가 인증서를 필요에 따라 지정할 수 있습니다. 기본적으로 필수 매개 변수에서 지정한 지문이 클라이언트 작업을 수행할 수 있는 지문의 권한 목록에 추가됩니다.

**관리 클라이언트**: 이 정보는 클러스터 관리에 연결된 클라이언트의 유효성을 검사하는 데 사용되며 끝점은 클러스터의 관리자 및 읽기 전용 작업을 수행하기 위한 올바른 자격 증명을 나타냅니다. 관리자 작업에 대해 권한을 부여하고자 하는 하나 이상의 인증서를 지정할 수 있습니다.

- **권한 부여자** 주체 이름을 사용하거나 지문으로 이 인증서를 찾아야 하는 경우 서비스 패브릭에 나타냅니다. 권한을 부여하는 주체 이름 사용은 좋은 보안 방법이 아니지만 더욱 유연하게 합니다.
- **주체 이름** 주체 이름으로 인증을 지정한 경우에만 필요합니다.
- **발급자 지문** 클라이언트가 서버에 해당 자격 증명을 제공하는 경우에 서버에서 수행할 수 있는 검사의 추가 수준을 제공합니다.

**읽기 전용 클라이언트**: 이 정보는 클러스터 관리에 연결된 클라이언트의 유효성을 검사하는 데 사용되며 끝점은 클러스터의 읽기 전용 작업을 수행하기 위한 올바른 자격 증명을 나타냅니다. 읽기 전용 작업에 대해 권한을 부여하고자 하는 하나 이상의 인증서를 지정할 수 있습니다.

- **권한 부여자** 주체 이름을 사용하거나 지문으로 이 인증서를 찾아야 하는 경우 서비스 패브릭에 나타냅니다. 권한을 부여하는 주체 이름 사용은 좋은 보안 방법이 아니지만 더욱 유연하게 합니다.
- **주체 이름** 주체 이름으로 인증을 지정한 경우에만 필요합니다.
- **발급자 지문** 클라이언트가 서버에 해당 자격 증명을 제공하는 경우에 서버에서 수행할 수 있는 검사의 추가 수준을 제공합니다.

## 다음 단계
클러스터에서 인증서 보안을 구성한 후에 [4단계: 클러스터 만들기 완료](service-fabric-cluster-creation-via-portal.md#step-4--complete-the-cluster-creation)의 클러스터 만들기 프로세스를 다시 시작합니다.

인증서 보안을 사용해서 클러스터가 만들어진 경우 나중에 [인증서를 업데이트](service-fabric-cluster-security-update-certs-azure.md)할 수 있습니다.


<!--Image references-->
[SecurityConfigurations_01]: ./media/service-fabric-cluster-azure-secure-with-certs/SecurityConfigurations_01.png
[SecurityConfigurations_02]: ./media/service-fabric-cluster-azure-secure-with-certs/SecurityConfigurations_02.png

<!---HONumber=AcomDC_0615_2016-->