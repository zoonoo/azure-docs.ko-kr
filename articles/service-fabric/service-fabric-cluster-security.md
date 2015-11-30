<properties
   pageTitle="서비스 패브릭 클러스터를 안전하게 보호하는 방법 | Microsoft Azure"
   description="서비스 패브릭 클러스터를 안전하게 보호하는 방법. 옵션은 무엇입니까?"
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

# 인증서를 사용하여 서비스 패브릭 클러스터를 보호하는 방법.

보안 서비스 패브릭 클러스터를 설정하려면 하나 이상의 서버/ x509 인증서가 있어야 합니다. 그런 다음 azure 키 자격 증명 모음에 업로드하고 [서비스 패브릭 클러스터 만들기 프로세스](service-fabric-cluster-creation-via-portal.md)에 설명된 클러스터 만들기 프로세스에서 사용합니다.

세 가지 고유한 단계

1. x509 인증서 획득
2. Azure 키 자격 증명 모음에 인증서를 업로드합니다.
3. 인증서의 위치 및 세부 정보를 서비스 패브릭 클러스터 만들기 프로세스에 제공합니다.

자세히 살펴보기 전에 시나리오의 일부 기본 사항을 알려 주세요.

##  어떤 시나리오를 다룹니까?

서비스 패브릭에서는 다음과 같은 시나리오에 대한 보안을 제공합니다.

1. 노드에 대한 클러스터를 노드 통신으로 보안 설정.
2. 클러스터의 특정 노드와 통신하는 패브릭 클라이언트 보안
3. 역할 기반 액세스 제어(RBAC) - 클러스터의 읽기 전용 작업에서 인증서 집합으로 관리 작업을 제한할 수 있습니다.   

서비스 패브릭은 클러스터를 만들 때 노드 유형 구성의 일부로 지정하는 X509 서버 인증서를 사용합니다. 인증서 정보 및 인증서 획득/생성 방법에 대한 빠른 개요는 이 페이지의 아래로 스크롤하세요.

 
## x509 인증서 획득

1. 프로덕션 작업을 실행하는 클러스터의 경우 클러스터 보호를 위해 [CA(인증 기관)](https://en.wikipedia.org/wiki/Certificate_authority)로 서명된 x509 인증서를 사용해야 합니다. 이러한 인증서를 얻는 방법에 대한 세부 정보는 [http://msdn.microsoft.com/library/aa702761.aspx](http://msdn.microsoft.com/library/aa702761.aspx)로 이동합니다.
2. 테스트 목적으로만 사용하는 클러스터의 경우 자체 서명된 인증서를 사용하도록 선택할 수 있습니다.


## 테스트 목적으로 자체 서명된 인증서 만들기

자체 서명된 인증서를 만드는 방법에 대한 세부 정보는 [https://technet.microsoft.com/library/hh848633.aspx](https://technet.microsoft.com/library/hh848633.aspx)에 있습니다.
    
다음은 PS입니다. 테스트 인증서 만들기에 대해 사용하지만 사용자의 요구를 충족하는지 확인하려면 위의 문서를 읽어야 합니다. ```
$password = Read-Host -AsSecureString 
``` ```
New-SelfSignedCertificate -CertStoreLocation Cert:\CurrentUser\My -DnsName ChackdanTestCertificate | Export-PfxCertificate -FilePath E:\MyCertificates\ChackdanTestCertificate.pfx -Password $password
```

**참고** DnsName <문자열> 복사할 인증서가 CloneCert 매개 변수를 통해 지정되지 않은 경우 인증서의 주체 대체 이름 확장명에 넣을 하나 이상의 DNS 이름을 지정합니다. 첫 번째 DNS 이름은 주체 이름으로도 저장됩니다. 서명 인증서가 지정되지 않은 경우 첫 번째 DNS 이름이 발급자 이름으로도 저장됩니다.

## 키 자격 증명 모음에 x509 인증서 업로드

인증서를 키 자격 증명 모음에 업로드하는 방법에 대한 지침은 여기 [키 자격 증명 모음 설명서 링크](https://azure.microsoft.com/documentation/articles/key-vault-get-started/)에 있습니다.

소스 자격 증명 모음 URL, 인증서 URL 인증서 및 인증서 지문을 기록해둡니다. 보안 서비스 패브릭 클러스터 설정 시 필요합니다. 필요한 데이터는 다음과 같이 보입니다.



1. **키 자격 증명 모음/소스 자격 증명 모음 URL의 리소스 ID** : /subscriptions/6c653126-e4ba-42cd-a1dd-f7bf96af7a47/resourceGroups/chackdan-keyvault/providers/Microsoft.KeyVault/vaults/chackdan-kmstest
2. **키 자격 증명 모음의 인증서 위치에 대한 URL** : https://chackdan-kmstest.vault.azure.net:443/secrets/MyCert/dcf17bdbb86b42ad864e8e827c268431 
3. **인증서 지문** : 2118C3BCE6541A54A0236E14ED2CCDD77EA4567A



##보안 클러스터 설정 

사용해야 할 인증서는 보안 구성 아래의 NodeType 수준에서 지정됩니다. 클러스터에 있는 모든 NodeType에 대해 이를 지정해야 합니다. 이 문서에서는 포털을 사용하여 수행하는 방법을 안내하지만 ARM 템플릿을 사용하여 동일하게 수행할 수 있습니다.

![SecurityConfigurations\_01][SecurityConfigurations_01]

필수 매개 변수

- **보안 모드** 'x509 인증서'를 선택해야 합니다. 보안 클러스터를 설정하려는 서비스 패브릭을 나타냅니다. 
- 여기에서는 3개의 값 - EncryptAndSign, Sign, 없음을 허용하지만 **클러스터 보호 수준**은 해당 값의 의미를 이해하기 위한 이 [보호 수준 문서](https://msdn.microsoft.com/library/aa347692.aspx)를 의미합니다. 현재 작업을 알 수 없는 경우 "EncryptAndSign"의 기본을 유지하는 것이 좋습니다.
- **소스 자격 증명 모음**은 키 자격 증명 모음의 리소스 ID를 의미하고 ```
/subscriptions/<Sub ID>/resourceGroups/<Resource group name>/providers/Microsoft.KeyVault/vaults/<vault name>
```의 형식이어야 합니다

- **인증서 URL**은 인증서가 업로드된 키 자격 증명 모음의 위치 URL을 의미하고 ```
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

서비스 패브릭을 통해 주 및 보조의 두 인증서를 지정할 수 있습니다. 만들 때 지정한 인증서가 기본적으로 주입니다. 다른 인증서를 추가하려면 해당 인증서를 클러스터의 VM에 배포해야 합니다. 방법에 대해서 [고객 관리 키 자격 증명 모음에서 VM에 인증서 배포](http://blogs.technet.com/b/kv/archive/2015/07/14/vm_2d00_certificates.aspx) 문서를 참조하세요.

해당 작업이 성공적으로 완료되면 포털 또는 ARM을 통해 이동하고 사용할 수도 있는 보조 인증서가 있음을 서비스 패브릭에 알립니다. 지문이 필요합니다.

포털에서 이 인증서를 추가하려는 클러스터 리소스에 대해 찾아보고 인증서 설정을 클릭하고 보조 인증서 지문을 입력하고 저장 단추를 누릅니다. 배포는 해당 배포의 시작 및 성공적 완료를 받고 주 또는 보조 인증서를 사용하여 클러스터에서 관리 작업을 수행할 수 있습니다.

![SecurityConfigurations\_02][SecurityConfigurations_02]

이제 원하는 인증서 중 하나를 제거하려는 경우 이를 수행할 수 있습니다. 제거한 후 저장 단추를 눌러 새 배포가 시작되도록 해야 합니다. 해당 배포가 완료되면 제거한 인증서를 더 이상 클러스터에 연결하는 데 사용할 수 없습니다. 보안 클러스터의 경우 항상 적어도 하나의 유효한(취소되지 않거나 만료된) 인증서 배포가 필요하며 그렇지 않으면 클러스터에 액세스할 수 없습니다.

만료가 가까운 인증서가 있는 경우를 알 수 있는 진단 이벤트가 있습니다.



## X509 인증서란?

X509 디지털 인증서는 클라이언트 및 서버를 인증하고 암호화하고 디지털로 메시지를 서명하는 데 일반적으로 사용됩니다. 이러한 인증서에 대한 세부 정보는 [http://msdn.microsoft.com/library/ms731899.aspx](http://msdn.microsoft.com/library/ms731899.aspx)로 이동하세요.

**참고**

1. 프로덕션 작업을 실행하는 클러스터에 사용되는 인증서는 올바르게 구성된 Windows 서버 인증서 서비스를 사용하여 만들어지거나 승인된 [CA(인증 기관)](https://en.wikipedia.org/wiki/Certificate_authority)를 통해 획득해야 합니다.
2. 프로덕션 환경에서 MakeCert.exe와 같은 도구를 사용하여 만든 임시 또는 테스트 인증서를 사용하지 마세요.
3. 테스트 목적으로만 사용하는 클러스터의 경우 자체 서명된 인증서를 사용하도록 선택할 수 있습니다. 


##서버 인증서 및 클라이언트 인증서란?

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

<!---HONumber=Nov15_HO4-->