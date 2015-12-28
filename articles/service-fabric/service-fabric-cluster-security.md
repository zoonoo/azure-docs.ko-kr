<properties
   pageTitle="서비스 패브릭 클러스터를 보호하는 방법 | Microsoft Azure"
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
   ms.date="11/10/2015"
   ms.author="chackdan"/>

# 서비스 패브릭 클러스터 보안

서비스 패브릭 클러스터는 사용자가 소유하는 리소스이며 특히 실행 중인 프로덕션 워크로드가 있는 경우 리소스에 대한 무단 액세스를 방지하기 위해 보안을 설정해야 합니다. 이 문서에서는 방법에 대한 프로세스를 안내합니다.

##  고려해야 할 클러스터 보안 시나리오

서비스 패브릭에서는 다음과 같은 시나리오에 대한 보안을 제공합니다.

1. **노드-노드 보안** 또는 노드 간 통신을 위한 클러스터 보안. 클러스터의 VM/컴퓨터 간 통신을 보호합니다. 이렇게 하면 클러스터에 가입하도록 인증된 컴퓨터만 호스팅 응용 프로그램 및 클러스터의 서비스에 참여할 수 있습니다.

	![노드-노드][Node-to-Node]

2. **클라이언트-노드 보안** 또는 클러스터의 특정 노드와 통신하는 패브릭 클라이언트 보안. 클라이언트 통신을 인증 및 보호하여 인증된 사용자만 클러스터 및 Windows Fabric 클러스터에 배포된 응용 프로그램에 액세스할 수 있도록 합니다. 클라이언트는 Windows 보안 자격 증명이나 인증서 보안 자격 증명을 통해 고유하게 식별됩니다.

	![클라이언트-노드][Client-to-Node]

	두 유형의 통신 시나리오(노드-노드 또는 클라이언트-노드)에 대해, 서비스 패브릭은 [인증서 보안](https://msdn.microsoft.com/library/ff649801.aspx) 또는 [Windows 보안](https://msdn.microsoft.com/library/ff649396.aspx) 사용 지원을 제공합니다. 노드-노드 또는 클라이언트-노드 보안을 위한 옵션은 서로 독립적이며 서로 같거나 다를 수 있습니다.

	Azure에서 서비스 패브릭은 클러스터를 만들 때 노드 유형 구성의 일부로 지정하는 X509 서버 인증서를 사용합니다. 인증서 정보 및 인증서 획득/생성 방법에 대한 빠른 개요는 이 페이지의 아래로 스크롤하세요.

3. **역할 기반 액세스 제어(RBAC)**: 클러스터의 읽기 전용 작업에서 인증서 집합으로 관리 작업을 제한할 수 있습니다.

4. **서비스 계정 및 RunAs**: 서비스 패브릭 자체가 Windows 서비스 프로세스(Fabric.exe)로 실행되고 Fabric.exe 프로세스가 실행되는 보안 계정을 구성할 수 있습니다. 클러스터의 각 노드에서 Fabric.exe가 실행되는 프로세스 계정뿐만 아니라 각 서비스에 대해 활성화되는 서비스 호스트 프로세스도 보호할 수 있습니다. 자세한 내용은 [응용 프로그램 보안 및 Runas](service-fabric-application-runas-security.md) 문서를 참조하세요.
  

## 인증서를 사용하여 서비스 패브릭 클러스터를 보호하는 방법.

보안 서비스 패브릭 클러스터를 설정하려면 하나 이상의 서버/ x509 인증서가 있어야 합니다. 그런 다음 azure 주요 자격 증명 모음에 업로드하고 클러스터 만들기 프로세스에서 사용합니다.

세 가지 고유한 단계

1. x509 인증서 획득
2. Azure 키 자격 증명 모음에 인증서를 업로드합니다.
3. 인증서의 위치 및 세부 정보를 서비스 패브릭 클러스터 만들기 프로세스에 제공합니다.

 
## 1단계: x509 인증서 획득

1. 프로덕션 워크로드를 실행하는 클러스터의 경우 클러스터 보호를 위해 [CA(인증 기관)](https://en.wikipedia.org/wiki/Certificate_authority)로 서명된 x509 인증서를 사용해야 합니다. 이러한 인증서를 얻는 방법에 대한 세부 정보를 보려면 [http://msdn.microsoft.com/library/aa702761.aspx](http://msdn.microsoft.com/library/aa702761.aspx)로 이동합니다.
2. 테스트 목적으로만 사용하는 클러스터의 경우 자체 서명된 인증서를 사용하도록 선택할 수 있습니다. 2.5단계에서 이 방법에 대한 단계를 안내합니다.


## 2단계: 주요 자격 증명 모음에 x509 인증서 업로드

이 단계는 복잡한 프로세스이므로 사용자를 위해 Git Repo에 업로드된 powershell 모듈이 있습니다.

**2.1단계**: 이 폴더를 이 [Git 리포지토리](https://github.com/ChackDan/Service-Fabric/tree/master/Scripts/ServiceFabricRPHelpers)에서 컴퓨터로 복사합니다.

**2.2단계**: 컴퓨터에 Azure SDK 1.0 이상이 설치되어 있는지 확인합니다.

**2.3단계**: Powershell 창을 열고 ServiceFabricRPHelpers.psm을 가져옵니다.

```
Remove-Module ServiceFabricRPHelpers
```

다음 내용을 복사하고 경로를 컴퓨터의 해당 .psm1로 변경합니다. 다음은 예제입니다.

```
Import-Module "C:\Users\chackdan\Documents\GitHub\Service-Fabric\Scripts\ServiceFabricRPHelpers\ServiceFabricRPHelpers.psm1"
```
  

**2.4단계**: 이미 획득한 인증서를 사용 중인 경우 이 단계를 진행하고 그렇지 않은 경우 2.5단계로 건너뜁니다.


Azure 계정에 로그인

```
Login-AzureRmAccount
```

다음 스크립트는 새 리소스 그룹 및/또는 자격 증명 모음을 만듭니다(아직 없는 경우).

```
Invoke-AddCertToKeyVault -SubscriptionId <you subscription id> -ResourceGroupName <string> -Location <region> -VaultName <Name of the Vault> -CertificateName <Name of the Certificate> -Password <Certificate password> -UseExistingCertificate -ExistingPfxFilePath <Full path to the .pfx file> 
```
다음은 채워진 스크립트 예제입니다.

```
Invoke-AddCertToKeyVault -SubscriptionId 35389201-c0b3-405e-8a23-9f1450994307 -ResourceGroupName chackdankeyvault4doc -Location westus -VaultName chackdankeyvault4doc  -CertificateName chackdantestcertificate2 -Password abcd123 -UseExistingCertificate -ExistingPfxFilePath C:\MyCertificates\ChackdanTestCertificate.pfx 
```

스크립트가 성공적으로 완료되면 아래와 같은 출력을 얻게 되며 이 출력은 3단계에 필요합니다.

1. **인증서 지문** : 2118C3BCE6541A54A0236E14ED2CCDD77EA4567A
2. **SourceVault** /Resource ID of the KeyVault : /subscriptions/35389201-c0b3-405e-8a23-9f1450994307/resourceGroups/chackdankeyvault4doc/providers/Microsoft.KeyVault/vaults/chackdankeyvault4doc
3. **인증서 URL** /주요 자격 증명 모음의 인증서 위치에 대한 URL : https://chackdankeyvalut4doc.vault.azure.net:443/secrets/chackdantestcertificate3/ebc8df6300834326a95d05d90e0701ea 

보안 클러스터를 설정하는 데 필요한 정보를 얻었습니다. 3단계로 이동합니다.


**2.5단계**: 새 자체 서명된 인증서를 만들려는 경우 주요 자격 증명 모음에 업로드합니다.

Azure 계정에 로그인

```
Login-AzureRmAccount
```

다음 스크립트는 새 리소스 그룹 및/또는 자격 증명 모음을 만듭니다(아직 없는 경우).

```
Invoke-AddCertToKeyVault -SubscriptionId <you subscription id> -ResourceGroupName <string> -Location <region> -VaultName <Name of the Vault> -CertificateName <Name of the Certificate> -Password <Certificate password> -CreateSelfSignedCertificate -DnsName <string- see note below.> -OutputPath <Full path to the .pfx file> 
```
스크립트에 지정한 OutputPath에는 keyvault에 업로드한 새 자체 서명된 인증서가 포함됩니다.


**참고** DnsName <문자열> 복사할 인증서가 CloneCert 매개 변수를 통해 지정되지 않은 경우 인증서의 주체 대체 이름 확장명에 넣을 하나 이상의 DNS 이름을 지정합니다. 첫 번째 DNS 이름은 주체 이름으로도 저장됩니다. 서명 인증서가 지정되지 않은 경우 첫 번째 DNS 이름이 발급자 이름으로도 저장됩니다.

자체 서명된 인증서를 만드는 방법에 대한 일반적인 정보는 [https://technet.microsoft.com/library/hh848633.aspx](https://technet.microsoft.com/library/hh848633.aspx)에 있습니다.

다음은 채워진 스크립트 예제입니다. ```
Invoke-AddCertToKeyVault -SubscriptionId 35389201-c0b3-405e-8a23-9f1450994307 -ResourceGroupName chackdankeyvault4doc -Location westus -VaultName chackdankeyvault4doc  -CertificateName chackdantestcertificate3 -Password abcd123 -CreateSelfSignedCertificate -DnsName www.chackdan.westus.azure.com -OutputPath C:\MyCertificates
```

자체 서명된 인증서이므로 이 인증서를 보안 클러스터에 연결하는 데 사용하기 전에 사용자 컴퓨터의 "신뢰할 수 있는 사용자" 저장소로 가져와야 합니다. ```
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\TrustedPeople -FilePath C:C:\MyCertificates\ChackdanTestCertificate.pfx -Password (Read-Host -AsSecureString -Prompt "Enter Certificate Password ")
``` ```
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My -FilePath C:C:\MyCertificates\ChackdanTestCertificate.pfx -Password (Read-Host -AsSecureString -Prompt "Enter Certificate Password ")
```

스크립트가 성공적으로 완료되면 아래와 같은 출력을 얻게 되며 이 출력은 3단계에 필요합니다.

1. **인증서 지문** : 64881409F4D86498C88EEC3697310C15F8F1540F
2. **SourceVault** /Resource ID of the KeyVault : /subscriptions/35389201-c0b3-405e-8a23-9f1450994307/resourceGroups/chackdankeyvault4doc/providers/Microsoft.KeyVault/vaults/chackdankeyvault4doc
3. **인증서 URL** /주요 자격 증명 모음의 인증서 위치에 대한 URL : https://chackdankeyvalut4doc.vault.azure.net:443/secrets/chackdantestcertificate3/fvc8df6300834326a95d05d90e0720ea 

##3단계: 보안 클러스터 설정 

보안 구성이 표시될 때까지 [서비스 패브릭 클러스터 만들기 프로세스](service-fabric-cluster-creation-via-portal.md)에 설명된 단계를 진행합니다. 다음은 보안 구성을 설정하는 방법입니다.

사용해야 할 인증서는 보안 구성 아래의 NodeType 수준에서 지정됩니다. 클러스터에 있는 모든 NodeType에 대해 이를 지정해야 합니다. 이 문서에서는 포털을 사용하여 수행하는 방법을 안내하지만 ARM 템플릿을 사용하여 동일하게 수행할 수 있습니다.

![SecurityConfigurations\_01][SecurityConfigurations_01]

필수 매개 변수

- **보안 모드** 'x509 인증서'를 선택해야 합니다. 보안 클러스터를 설정하려는 서비스 패브릭을 나타냅니다. 
- 여기에서는 3개의 값(EncryptAndSign, Sign, 없음)을 허용하지만 **클러스터 보호 수준**은 각 값의 의미를 이해하기 위한 이 [보호 수준 문서](https://msdn.microsoft.com/library/aa347692.aspx)를 의미합니다. 현재 작업을 알 수 없는 경우 "EncryptAndSign"의 기본을 유지하는 것이 좋습니다.
- **소스 자격 증명 모음**은 주요 자격 증명 모음의 리소스 ID를 의미하고 ```
/subscriptions/<Sub ID>/resourceGroups/<Resource group name>/providers/Microsoft.KeyVault/vaults/<vault name>
```의 형식이어야 합니다.

- **인증서 URL**은 인증서가 업로드된 주요 자격 증명 모음의 위치 URL을 의미하고 ```
https://<name of the vault>.vault.azure.net:443/secrets/<exact location>
https://chackdan-kmstest-eastus.vault.azure.net:443/secrets/MyCert/6b5cc15a753644e6835cb3g3486b3812
```의 형식이어야 합니다
- **인증서 지문**은 이전에 지정한 URL에서 찾을 수 있는 인증서의 지문을 의미합니다.

선택적 매개 변수 - 클러스터의 작업을 수행하는 데 사용하는 클라이언트 컴퓨터에 추가 인증서를 필요에 따라 지정할 수 있습니다. 기본적으로 필수 매개 변수에서 지정한 지문이 클라이언트 작업당에 부여된 지문의 권한 목록에 추가됩니다.

관리 클라이언트 - 이 정보는 클러스터 관리에 연결된 클라이언트의 유효성을 검사하는 데 사용되며 끝점은 실제로 클러스터의 관리자 및 읽기 전용 작업을 수행하기 위한 올바른 자격 증명을 나타냅니다. 관리자 작업에 대해 권한을 부여하고자 하는 하나 이상의 인증서를 지정할 수 있습니다.


- **권한 부여자** - 주체 이름을 사용하거나 지문으로 이 인증서를 찾아야 하는 경우 서비스 패브릭에 나타냅니다. 권한을 부여하는 주체 이름 사용은 좋은 보안 방법이 아니지만 더욱 유연하게 합니다.


- 주체 이름으로 인증을 지정한 경우에만 **주체 이름**이 필요합니다.
- **발급자 지문**은 클라이언트가 서버에 해당 자격 증명을 제공하는 경우에 서버에서 수행할 수 있는 검사의 추가 수준을 허용합니다.

읽기 전용 클라이언트 - 이 정보는 클러스터 관리에 연결된 클라이언트의 유효성을 검사하는 데 사용되며 끝점은 실제로 클러스터의 읽기 전용 작업을 수행하기 위한 올바른 자격 증명을 나타냅니다. 읽기 전용 작업에 대해 권한을 부여하고자 하는 하나 이상의 인증서를 지정할 수 있습니다.


- **권한 부여자** - 주체 이름을 사용하거나 지문으로 이 인증서를 찾아야 하는 경우 서비스 패브릭에 나타냅니다. 권한을 부여하는 주체 이름 사용은 좋은 보안 방법이 아니지만 더욱 유연하게 합니다.

- 주체 이름으로 인증을 지정한 경우에만 **주체 이름**이 필요합니다.
- **발급자 지문**은 클라이언트가 서버에 해당 자격 증명을 제공하는 경우에 서버에서 수행할 수 있는 검사의 추가 수준을 허용합니다.


## 클러스터의 인증서를 업데이트하는 방법

서비스 패브릭을 통해 주 및 보조의 두 인증서를 지정할 수 있습니다. 만들 때 지정한 인증서가 기본적으로 주입니다. 다른 인증서를 추가하려면 해당 인증서를 클러스터의 VM에 배포해야 합니다. 위의 2단계에서는 새 인증서를 keyvalult에 업로드하는 방법을 간략히 살펴봅니다. 첫 번째 인증서로 수행한 것과 동일한 keyvault를 사용할 수 있습니다.

방법은 [고객 관리 주요 자격 증명 모음에서 VM에 인증서 배포](http://blogs.technet.com/b/kv/archive/2015/07/14/vm_2d00_certificates.aspx) 문서를 참조하세요.

해당 작업이 성공적으로 완료되면 포털 또는 ARM을 통해 이동하고 사용할 수도 있는 보조 인증서가 있음을 서비스 패브릭에 알립니다. 지문이 필요합니다.

프로세스는 다음과 같습니다. 포털에서 이 인증서를 추가하려는 클러스터 리소스에 대해 찾아보고 인증서 설정을 클릭하고 보조 인증서 지문을 입력하고 저장 단추를 누릅니다. 배포는 해당 배포의 시작 및 성공적 완료를 받고 주 또는 보조 인증서를 사용하여 클러스터에서 관리 작업을 수행할 수 있습니다.

![SecurityConfigurations\_02][SecurityConfigurations_02]

이제 원하는 인증서 중 하나를 제거하려는 경우 이를 수행할 수 있습니다. 제거한 후 저장 단추를 눌러 새 배포가 시작되도록 해야 합니다. 해당 배포가 완료되면 제거한 인증서를 더 이상 클러스터에 연결하는 데 사용할 수 없습니다. 보안 클러스터의 경우 항상 적어도 하나의 유효한(취소되지 않거나 만료된) 인증서 배포가 필요하며 그렇지 않으면 클러스터에 액세스할 수 없습니다.

만료가 가까운 인증서가 있는 경우를 알 수 있는 진단 이벤트가 있습니다.



## X509 인증서란?

X509 디지털 인증서는 클라이언트 및 서버를 인증하고 암호화하고 디지털로 메시지를 서명하는 데 일반적으로 사용됩니다. 이러한 인증서에 대한 세부 정보를 보려면 [http://msdn.microsoft.com/library/ms731899.aspx](http://msdn.microsoft.com/library/ms731899.aspx)로 이동하세요.

**참고**

1. 프로덕션 작업을 실행하는 클러스터에 사용되는 인증서는 올바르게 구성된 Windows 서버 인증서 서비스를 사용하여 만들어지거나 승인된 [CA(인증 기관)](https://en.wikipedia.org/wiki/Certificate_authority)를 통해 획득해야 합니다.
2. 프로덕션 환경에서 MakeCert.exe와 같은 도구를 사용하여 만든 임시 또는 테스트 인증서를 사용하지 마세요.
3. 테스트 목적으로만 사용하는 클러스터의 경우 자체 서명된 인증서를 사용하도록 선택할 수 있습니다. 


## 서버 인증서 및 클라이언트 인증서란?

**서버/X509 인증서**

서버 인증서에는 서버(노드)에서 클라이언트 또는 서버(노드)에서 서버(노드)로 인증의 주요 작업이 있습니다. 클라이언트 또는 노드가 노드를 인증하는 경우 초기 검사 중 하나는 구성된 허용된 일반 이름 목록에 있는지 확인하도록 주체 필드의 일반 이름의 값을 비교하는 것입니다. 이 일반 이름 또는 인증서 주체 대체 이름 중 하나는 허용된 일반 이름 목록에 있어야 합니다.

다음 문서는 주체 대체 이름(SAN)을 사용하여 인증서를 생성하는 방법에 대해 설명합니다. [http://support.microsoft.com/kb/931351](http://support.microsoft.com/kb/931351)
 
**참고:** 주체 필드에는 값을 나타내는 이니셜로 접두사가 붙은 여러 값이 포함될 수 있습니다. 가장 일반적으로 이니셜은 일반 이름에 대해 "CN"이며 예를 들어 "CN = www.contoso.com"입니다. 주체 필드를 비워둘 수도 있습니다. 선택적 주체 대체 이름 필드가 채워진 경우 인증서의 일반 이름과 주체 대체 이름당 하나의 항목을 모두 포함해야 합니다. 이러한 작업은 DNS 이름 값으로 입력됩니다.

또한 인증서의 용도 필드의 값은 "서버 인증" 또는 "클라이언트 인증"과 같은 적절한 값을 포함해야 합니다.

**클라이언트 인증서**

클라이언트 인증서는 일반적으로 타사 인증 기관에서 발급되지 않습니다. 대신 현재 사용자 위치의 개인 저장소는 일반적으로 "클라이언트 인증"의 목적으로 루트 인증 기관에서 넣은 인증서를 포함합니다. 상호 인증이 필요한 경우 클라이언트에서 이러한 인증서를 사용할 수 있습니다. 서비스 패브릭 클러스터의 모든 관리 작업은 서버 인증서가 필요합니다. 클라이언트 인증서는 사용할 수 없습니다.


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 다음 단계
- [서비스 패브릭 클러스터 업그레이드 프로세스 및 사용자 기대 수준](service-fabric-cluster-upgrade.md)
- [Visual Studio에서 서비스 패브릭 응용 프로그램 관리](service-fabric-manage-application-in-visual-studio.md).
- [서비스 패브릭 상태 모델 소개](service-fabric-health-introduction.md)

<!--Image references-->
[SecurityConfigurations_01]: ./media/service-fabric-cluster-security/SecurityConfigurations_01.png
[SecurityConfigurations_02]: ./media/service-fabric-cluster-security/SecurityConfigurations_02.png
[Node-to-Node]: ./media/service-fabric-cluster-security/node-to-node.png
[Client-to-Node]: ./media/service-fabric-cluster-security/client-to-node.png

<!---HONumber=AcomDC_1217_2015-->