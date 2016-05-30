<properties
   pageTitle="서비스 패브릭 클러스터 보안 | Microsoft Azure"
   description="서비스 패브릭 클러스터를 보호하는 방법입니다. 옵션은 무엇입니까?"
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
   ms.date="05/02/2016"
   ms.author="chackdan"/>

# 서비스 패브릭 클러스터 보안

Azure 서비스 패브릭 클러스터는 사용자가 소유하는 리소스입니다. 특히 실행 중인 프로덕션 워크로드가 있는 경우 리소스에 대한 무단 액세스를 방지하기 위해 보안을 설정해야 합니다. 이 문서에서는 서비스 패브릭 클러스터의 보안을 설정하는 과정을 안내합니다.

## 클러스터 보안 시나리오

서비스 패브릭에서는 다음과 같은 시나리오에 대한 보안을 제공합니다.

1. **노드-노드 보안:** 클러스터의 VM과 컴퓨터 간 통신을 보호합니다. 이렇게 하면 클러스터에 가입하도록 인증된 컴퓨터만 호스팅 응용 프로그램 및 클러스터의 서비스에 참여할 수 있습니다.

	![노드-노드 통신의 다이어그램][Node-to-Node]

2. **클라이언트-노드 보안:** 서비스 패브릭 클라이언트와 클러스터의 개별 노드 간 통신을 보호합니다. 이 보안 유형은 클라이언트 통신을 인증 및 보호하여 인증된 사용자만 클러스터 및 클러스터에 배포된 응용 프로그램에 액세스할 수 있도록 합니다. 클라이언트는 Windows 보안 자격 증명이나 인증서 보안 자격 증명을 통해 고유하게 식별됩니다.

	![클라이언트-노드 통신의 다이어그램][Client-to-Node]

	노드-노드 또는 클라이언트-노드 보안의 경우 [인증서 보안](https://msdn.microsoft.com/library/ff649801.aspx) 또는 [Windows 보안](https://msdn.microsoft.com/library/ff649396.aspx)을 사용할 수 있습니다 노드-노드 또는 클라이언트-노드 보안을 위한 옵션은 서로 독립적이며 서로 같거나 다를 수 있습니다.

	Azure 서비스 패브릭은 클러스터를 만들 때 노드 유형 구성의 일부로 지정하는 X.509 서버 인증서를 사용합니다. 인증서 정보 및 인증서 획득 또는 생성 방법에 대한 빠른 개요는 이 문서의 끝에 제공됩니다.

3. **RBAC(역할 기반 액세스 제어):** 클러스터의 관리 작업을 특정 인증서 집합으로 제한합니다.

## 인증서를 사용하여 서비스 패브릭 클러스터 보호.

보안 서비스 패브릭 클러스터를 설정하려면 Azure 키 자격 증명 모음에 업로드하고 클러스터 만들기 프로세스에서 사용하는 하나 이상의 서버 X.509 인증서가 필요합니다.

세 가지 고유한 단계가 있습니다.

1. X.509 인증서를 획득합니다.
2. Azure 키 자격 증명 모음에 인증서를 업로드합니다.
3. 인증서의 위치 및 세부 정보를 서비스 패브릭 클러스터 만들기 프로세스에 제공합니다.

### 1단계: X.509 인증서를 획득합니다.

1. 프로덕션 워크로드를 실행하는 클러스터의 경우 클러스터 보호를 위해 [CA(인증 기관)](https://en.wikipedia.org/wiki/Certificate_authority)로 서명된 X.509 인증서를 사용해야 합니다. 이러한 인증서를 얻는 방법에 대한 자세한 내용은 [방법: 인증서 가져오기](http://msdn.microsoft.com/library/aa702761.aspx)를 참조하세요.
2. 테스트 목적으로만 사용하는 클러스터의 경우 자체 서명된 인증서를 사용하도록 선택할 수 있습니다. 아래 2.5단계는 수행하는 방법을 설명합니다.

### 2단계: 키 자격 증명 모음에 X.509 인증서를 업로드합니다.

이 단계는 복잡한 프로세스이므로 사용자를 위해 Git 리포지토리에 업로드된 PowerShell 모듈이 있습니다.

**2.1단계**: 이 폴더를 이 [Git 리포지토리](https://github.com/ChackDan/Service-Fabric/tree/master/Scripts/ServiceFabricRPHelpers)에서 컴퓨터로 복사합니다.

**2.2단계**: 컴퓨터에 Azure PowerShell 1.0 이상이 설치되어 있는지 확인합니다. 이전에 수행한 적이 없는 경우 [Azure PowerShell 설치 및 구성 방법](../powershell-install-configure.md)에 요약된 단계에 따라 수행하는 것이 좋습니다.

**2.3단계**: PowerShell 창을 열고 ServiceFabricRPHelpers.psm을 가져옵니다. 이 모듈은 2.1단계에서 다운로드한 모듈입니다.

```
Remove-Module ServiceFabricRPHelpers
```

다음 예제를 복사하고 컴퓨터의 경로와 일치하도록 경로를 .psm1로 변경합니다.

```
Import-Module "C:\Users\chackdan\Documents\GitHub\Service-Fabric\Scripts\ServiceFabricRPHelpers\ServiceFabricRPHelpers.psm1"
```

**2.4단계**: 이미 획득한 인증서를 사용 중인경우 이 단계의 절차를 수행합니다. 그렇지 않으면 자체 서명된 인증서를 만들어 주요 자격 증명 모음에 배포하는 방법을 설명하는 2.5단계로 건너뜁니다.

Azure 계정에 로그인합니다. Powershell이 어떤 이유로 인해 실패하면 Azure PowerShell이 올바르게 설치되었는지 확인해야 합니다.

```
Login-AzureRmAccount
```

다음 스크립트는 새 리소스 그룹 및/또는 주요 자격 증명 모음이 아직 없는 경우 만듭니다. **참고: 기존 주요 자격 증명 모음을 사용하는 경우 이 스크립트를 사용하여 배포를 지원하도록 구성되어야 합니다.**

```
Set-AzureRmKeyVaultAccessPolicy -VaultName <Name of the Vault> -ResourceGroupName <string> -EnabledForDeployment
```

```
Invoke-AddCertToKeyVault -SubscriptionId <your subscription id> -ResourceGroupName <string> -Location <region> -VaultName <Name of the Vault> -CertificateName <Name of the Certificate> -Password <Certificate password> -UseExistingCertificate -ExistingPfxFilePath <Full path to the .pfx file>
```
다음은 채워진 스크립트 예제입니다.

```
Invoke-AddCertToKeyVault -SubscriptionId 35389201-c0b3-405e-8a23-9f1450994307 -ResourceGroupName chackdankeyvault4doc -Location westus -VaultName chackdankeyvault4doc  -CertificateName chackdantestcertificate2 -Password abcd123 -UseExistingCertificate -ExistingPfxFilePath C:\MyCertificates\ChackdanTestCertificate.pfx
```

스크립트가 성공적으로 완료되면 아래와 같은 출력을 얻게 되며 이 출력은 3단계(보안 클러스터 설정)에 필요합니다.

- **인증서 지문** : 2118C3BCE6541A54A0236E14ED2CCDD77EA4567A

- **SourceVault** /주요 자격 증명 모음의 리소스 ID : /subscriptions/35389201-c0b3-405e-8a23-9f1450994307/resourceGroups/chackdankeyvault4doc/providers/Microsoft.KeyVault/vaults/chackdankeyvault4doc

- **인증서 URL** /주요 자격 증명 모음의 인증서 위치에 대한 URL : https://chackdankeyvalut4doc.vault.azure.net:443/secrets/chackdantestcertificate3/ebc8df6300834326a95d05d90e0701ea

보안 클러스터를 설정하는 데 필요한 정보를 얻었습니다. 3단계로 이동합니다.

**2.5단계**: 인증서가 *없고* 자체 서명된 새 인증서를 만들어 주요 자격 증명 모음에 업로드하려는 경우 다음 단계를 수행합니다.

Azure 계정에 로그인 Powershell이 어떤 이유로 인해 실패하면 Azure PowerShell이 올바르게 설치되었는지 확인해야 합니다.

```
Login-AzureRmAccount
```

다음 스크립트는 새 리소스 그룹 및/또는 주요 자격 증명 모음이 아직 없는 경우 만듭니다.

```
Invoke-AddCertToKeyVault -SubscriptionId <you subscription id> -ResourceGroupName <string> -Location <region> -VaultName <Name of the Vault> -CertificateName <Name of the Certificate> -Password <Certificate password> -CreateSelfSignedCertificate -DnsName <string- see note below.> -OutputPath <Full path to the .pfx file>
```

스크립트에 지정한 OutputPath에는 스크립트를 통해 주요 자격 증명 모음에 업로드한 자체 서명된 새 인증서가 포함됩니다.

>[AZURE.NOTE] DnsName 문자열은 복사할 인증서가 CloneCert 매개 변수에서 지정되지 않은 경우 인증서의 주체 대체 이름 확장명에 넣을 하나 이상의 DNS 이름을 지정합니다. 첫 번째 DNS 이름은 주체 이름으로도 저장됩니다. 서명 인증서가 지정되지 않은 경우 첫 번째 DNS 이름이 발급자 이름으로도 저장됩니다.

자체 서명된 인증서를 만드는 방법에 대한 정보는 [https://technet.microsoft.com/library/hh848633.aspx](https://technet.microsoft.com/library/hh848633.aspx)에 있습니다.

다음은 채워진 스크립트 예제입니다.

```
Invoke-AddCertToKeyVault -SubscriptionId 35389201-c0b3-405e-8a23-9f1450994307 -ResourceGroupName chackdankeyvault4doc -Location westus -VaultName chackdankeyvault4doc  -CertificateName chackdantestcertificate3 -Password abcd123 -CreateSelfSignedCertificate -DnsName www.chackdan.westus.azure.com -OutputPath C:\MyCertificates
```

자체 서명된 인증서이므로 이 인증서를 보안 클러스터에 연결하는 데 사용하기 전에 사용자 컴퓨터의 "신뢰할 수 있는 사용자" 저장소로 가져와야 합니다.

```
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\TrustedPeople -FilePath C:\MyCertificates\ChackdanTestCertificate.pfx -Password (Read-Host -AsSecureString -Prompt "Enter Certificate Password ")
```

```
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My -FilePath C:\MyCertificates\ChackdanTestCertificate.pfx -Password (Read-Host -AsSecureString -Prompt "Enter Certificate Password ")
```

스크립트가 성공적으로 완료되면 아래와 같은 출력을 얻게 됩니다. 이는 3단계에 필요합니다.

- **인증서 지문** : 64881409F4D86498C88EEC3697310C15F8F1540F

- **SourceVault** /주요 자격 증명 모음의 리소스 ID : /subscriptions/35389201-c0b3-405e-8a23-9f1450994307/resourceGroups/chackdankeyvault4doc/providers/Microsoft.KeyVault/vaults/chackdankeyvault4doc

- **인증서 URL** /주요 자격 증명 모음의 인증서 위치에 대한 URL : https://chackdankeyvalut4doc.vault.azure.net:443/secrets/chackdantestcertificate3/fvc8df6300834326a95d05d90e0720ea

### 3단계: 보안 클러스터 설정

보안 구성 섹션이 표시될 때까지 [서비스 패브릭 클러스터 만들기 프로세스](service-fabric-cluster-creation-via-portal.md)에 설명된 단계를 진행합니다. 보안 구성을 설정하려면 여기에 표시된 지침으로 건너뜁니다.

>[AZURE.NOTE]
필요한 인증서는 보안 구성 아래의 노드 유형 수준에서 지정됩니다. 클러스터에 있는 모든 노드 유형에 대해 이를 지정해야 합니다. 이 문서에서는 포털을 사용하여 수행하는 방법을 안내하지만 Azure 리소스 관리자 템플릿을 사용하여 동일하게 수행할 수 있습니다.

![Azure 포털의 보안 구성 스크린 샷][SecurityConfigurations_01]

필수 매개 변수:

- **보안 모드.** **X509 인증서**를 선택합니다. 보안 클러스터를 설정하려는 서비스 패브릭을 나타냅니다.
- **클러스터 보호 수준.** 각 값의 의미를 이해하려면 이 [보호 수준 문서](https://msdn.microsoft.com/library/aa347692.aspx)를 참조하세요. 여기에서는 3개의 값(EncryptAndSign, Sign 및 없음)을 허용하지만 현재 작업을 알 수 없는 경우 EncryptAndSign의 기본을 유지하는 것이 좋습니다.
- **원본 자격 증명 모음.** 주요 자격 증명 모음의 리소스 ID를 나타냅니다. 이 형식이어야 합니다.

    ```
    /subscriptions/<Sub ID>/resourceGroups/<Resource group name>/providers/Microsoft.KeyVault/vaults/<vault name>
    ```

- **인증서 URL.** 인증서가 업로드된 주요 자격 증명 모음의 위치 URL을 의미합니다. 이 형식이어야 합니다.

    ```
    https://<name of the vault>.vault.azure.net:443/secrets/<exact location>
```
```
    https://chackdan-kmstest-eastus.vault.azure.net:443/secrets/MyCert/6b5cc15a753644e6835cb3g3486b3812
    ```

- **인증서 지문.** 이전에 지정한 URL에서 찾을 수 있는 인증서의 지문을 의미합니다.

선택적 매개 변수:

 - 클러스터의 작업을 수행하는 데 사용하는 클라이언트 컴퓨터에 추가 인증서를 필요에 따라 지정할 수 있습니다. 기본적으로 필수 매개 변수에서 지정한 지문이 클라이언트 작업을 수행할 수 있는 지문의 권한 목록에 추가됩니다.

관리 클라이언트: 이 정보는 클러스터 관리에 연결된 클라이언트의 유효성을 검사하는 데 사용되며 끝점은 클러스터의 관리자 및 읽기 전용 작업을 수행하기 위한 올바른 자격 증명을 나타냅니다. 관리자 작업에 대해 권한을 부여하고자 하는 하나 이상의 인증서를 지정할 수 있습니다.

- **권한 부여자.** 주체 이름을 사용하거나 지문으로 이 인증서를 찾아야 하는 경우 서비스 패브릭에 나타냅니다. 권한을 부여하는 주체 이름 사용은 좋은 보안 방법이 아니지만 더욱 유연하게 합니다.
- **주체 이름.** 주체 이름으로 인증을 지정한 경우에만 필요합니다.
- **발급자 지문** 클라이언트가 서버에 해당 자격 증명을 제공하는 경우에 서버에서 수행할 수 있는 검사의 추가 수준을 제공합니다.

읽기 전용 클라이언트: 이 정보는 클러스터 관리에 연결된 클라이언트의 유효성을 검사하는 데 사용되며 끝점은 클러스터의 읽기 전용 작업을 수행하기 위한 올바른 자격 증명을 나타냅니다. 읽기 전용 작업에 대해 권한을 부여하고자 하는 하나 이상의 인증서를 지정할 수 있습니다.

- **권한 부여자.** 주체 이름을 사용하거나 지문으로 이 인증서를 찾아야 하는 경우 서비스 패브릭에 나타냅니다. 권한을 부여하는 주체 이름 사용은 좋은 보안 방법이 아니지만 더욱 유연하게 합니다.
- **주체 이름.** 주체 이름으로 인증을 지정한 경우에만 필요합니다.
- **발급자 지문.** 클라이언트가 서버에 해당 자격 증명을 제공하는 경우에 서버에서 수행할 수 있는 검사의 추가 수준을 제공합니다.

## 클러스터의 인증서 업데이트

서비스 패브릭을 통해 주 및 보조의 두 인증서를 지정합니다. 기본적으로 생성 시 지정한 인증서가 주 인증서입니다. 다른 인증서를 추가하려면 클러스터의 VM에 배포해야 합니다. 위의 2단계에서는 주요 자격 증명 모음에 새 인증서를 업로드하는 방법을 설명합니다. 첫 번째 인증서에서와 마찬가지로 동일한 주요 자격 증명 모음을 사용할 수 있습니다. 자세한 내용은 [고객 관리 주요 자격 증명 모음에서 VM에 인증서 배포](http://blogs.technet.com/b/kv/archive/2015/07/14/vm_2d00_certificates.aspx)를 참조하세요.

해당 작업이 완료되면 포털 또는 ARM을 사용하여 사용할 수 있는 보조 인증서가 있음을 서비스 패브릭에 알립니다. 지문이 필요합니다.

보조 인증서를 추가하기 위한 프로세스는 다음과 같습니다.

1. 포털로 이동하고 이 인증서를 추가하려는 클러스터 리소스로 이동합니다.
2. **설정**아래에서 인증서 설정을 클릭하고 보조 인증서 지문을 입력합니다.
3. **Save**를 클릭합니다. 배포가 시작되고 해당 배포가 완료되면 주 또는 보조 인증서를 사용하여 클러스터에서 관리 작업을 수행할 수 있습니다.

![포털의 인증서 지문 스크린 샷][SecurityConfigurations_02]

다음은 클러스터에서 사용하지 않도록 오래된 인증서를 제거하는 프로세스입니다.

1. 포털로 이동하고 클러스터의 보안 설정으로 이동합니다.
2. 인증서 중 하나를 제거합니다.
3. **저장**을 클릭하여 새 배포를 시작합니다. 배포가 완료되면 제거한 인증서는 클러스터에 연결하는 데 더 이상 사용할 수 없습니다.

>[AZURE.NOTE] 보안 클러스터의 경우 항상 적어도 하나의 유효한(취소되지 않거나 만료되지 않은) 주 또는 보조 인증서 배포가 필요하며 그렇지 않으면 클러스터에 액세스할 수 없습니다.


## 
서비스 패브릭에서 사용하는 인증서 유형

### X.509 인증서

X.509 디지털 인증서는 클라이언트 및 서버를 인증하고 암호화하고 디지털로 메시지를 서명하는 데 일반적으로 사용됩니다. 이러한 인증서에 대한 자세한 내용은 MSDN 라이브러리의 [인증서 작업](http://msdn.microsoft.com/library/ms731899.aspx)으로 이동합니다.

>[AZURE.NOTE]
- 프로덕션 워크로드를 실행하는 클러스터에 사용되는 인증서는 올바르게 구성된 Windows Server 인증서 서비스를 사용하여 만들어지거나 승인된 [CA(인증 기관)](https://en.wikipedia.org/wiki/Certificate_authority)에서 획득해야 합니다.
- 프로덕션 환경에서 MakeCert.exe와 같은 도구를 사용하여 만든 임시 또는 테스트 인증서를 사용하지 마세요.
- 테스트 목적으로만 사용하는 클러스터의 경우 자체 서명된 인증서를 사용하도록 선택할 수 있습니다.

### 서버 인증서 및 클라이언트 인증서

#### 서버 X.509 인증서

서버 인증서에는 서버(노드)에서 클라이언트 또는 서버(노드)에서 서버(노드)로 인증의 주요 작업이 있습니다. 클라이언트 또는 노드가 노드를 인증할 때 초기 검사 중 하나는 제목 필드에 있는 일반 이름의 값을 확인하는 것입니다. 이 일반 이름 또는 인증서 주체 대체 이름 중 하나는 허용된 일반 이름 목록에 있어야 합니다.

다음 문서는 SAN(주체 대체 이름)을 사용하여 인증서를 생성하는 방법에 대해 설명합니다. [보안 LDAP 인증서에 주체 대체 이름을 추가하는 방법](http://support.microsoft.com/kb/931351)

>[AZURE.NOTE] 주체 필드에는 값 형식을 나타내는 이니셜로 접두사가 붙은 여러 값이 포함될 수 있습니다. 가장 일반적으로 이니셜은 일반 이름에 대해 "CN"이며 예를 들어 "CN = www.contoso.com"입니다. 주체 필드를 비워둘 수도 있습니다. 선택적 주체 대체 이름 필드가 채워진 경우 인증서의 일반 이름과 주체 대체 이름당 하나의 항목을 모두 포함해야 합니다. 이러한 작업은 DNS 이름 값으로 입력됩니다.

인증서의 용도 필드의 값은 "서버 인증" 또는 "클라이언트 인증"과 같은 적절한 값을 포함해야 합니다.

#### 클라이언트 인증서

클라이언트 인증서는 일반적으로 타사 인증 기관에서 발급되지 않습니다. 대신 현재 사용자 위치의 개인 저장소는 일반적으로 "클라이언트 인증"의 목적으로 루트 인증 기관에서 넣은 클라이언트 인증서를 포함합니다. 상호 인증이 필요한 경우 클라이언트에서 이러한 인증서를 사용할 수 있습니다.

>[AZURE.NOTE] 서비스 패브릭 클러스터의 모든 관리 작업은 서버 인증서가 필요합니다. 관리에 클라이언트 인증서를 사용할 수 없습니다.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->


### 보안 클러스터에 연결

1. "Connect-serviceFabricCluster" PowerShell 명령을 실행하는 데 사용할 컴퓨터에서 다음 명령을 실행하여 인증서를 설정합니다.

    ```powershell
    Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
            -FilePath C:\docDemo\certs\DocDemoClusterCert.pfx `
            -Password (ConvertTo-SecureString -String test -AsPlainText -Force)
    ```

2. 다음 PowerShell 명령을 실행하여 보안 클러스터에 연결합니다. 인증서 세부 정보는 클러스터를 설정할 때 제공한 것과 동일합니다.

    ```powershell
    Connect-serviceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `
              -KeepAliveIntervalInSec 10 `
              -X509Credential -ServerCertThumbprint <Certificate Thumbprint> `
              -FindType FindByThumbprint -FindValue <Certificate Thumbprint> `
              -StoreLocation CurrentUser -StoreName My
    ```

    예를 들어 위의 PowerShell 명령은 다음과 비슷합니다.

    ```powershell
    Connect-serviceFabricCluster -ConnectionEndpoint sfcluster4doc.westus.cloudapp.azure.com:19000 `
              -KeepAliveIntervalInSec 10 `
              -X509Credential -ServerCertThumbprint C179E609BBF0B227844342535142306F3913D6ED `
              -FindType FindByThumbprint -FindValue C179E609BBF0B227844342535142306F3913D6ED `
              -StoreLocation CurrentUser -StoreName My
    ```

## 다음 단계

- [서비스 패브릭 클러스터 업그레이드 프로세스 및 사용자 기대 수준](service-fabric-cluster-upgrade.md)
- [Visual Studio에서 서비스 패브릭 응용 프로그램 관리](service-fabric-manage-application-in-visual-studio.md).
- [서비스 패브릭 상태 모델 소개](service-fabric-health-introduction.md)
- [응용 프로그램 보안 및 RunAs](service-fabric-application-runas-security.md)

<!--Image references-->
[SecurityConfigurations_01]: ./media/service-fabric-cluster-security/SecurityConfigurations_01.png
[SecurityConfigurations_02]: ./media/service-fabric-cluster-security/SecurityConfigurations_02.png
[Node-to-Node]: ./media/service-fabric-cluster-security/node-to-node.png
[Client-to-Node]: ./media/service-fabric-cluster-security/client-to-node.png

<!---HONumber=AcomDC_0518_2016-->