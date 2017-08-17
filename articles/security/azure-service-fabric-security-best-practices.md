---
title: "Azure Service Fabric 보안 모범 사례 | Microsoft Docs"
description: "이 문서에서는 Azure Service Fabric 보안을 위한 여러 모범 사례를 제공합니다."
services: security
documentationcenter: na
author: unifycloud
manager: swadhwa
editor: tomsh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/04/2017
ms.author: tomsh
ms.translationtype: HT
ms.sourcegitcommit: 0aae2acfbf30a77f57ddfbaabdb17f51b6938fd6
ms.openlocfilehash: 3132e42aac72c0132c7526ac56d80bc5eec269e7
ms.contentlocale: ko-kr
ms.lasthandoff: 08/09/2017

---
# <a name="azure-service-fabric-security-best-practices"></a>Azure Service Fabric 보안 모범 사례
Azure에 응용 프로그램을 배포하는 것은 빠르고, 쉽고, 비용 효율적입니다. 프로덕션 환경에 클라우드 응용 프로그램을 배포하기 전에 필수 및 권장 모범 사례와 대조하여 응용 프로그램을 평가하는 데 도움이 되는 모범 사례가 있으면 유용합니다.

Azure Service Fabric은 손쉽게 패키지하고 배포하며 확장 가능하고 안정성이 뛰어난 마이크로 서비스를 관리하는 분산된 시스템 플랫폼입니다. 또한 서비스 패브릭은 클라우드 응용 프로그램 개발 및 관리에서 발생하는 중요한 과제를 해결합니다. 개발자와 관리자가 복잡한 인프라 문제를 피하고 업무 수행에 필수적인 까다로운 워크로드를 확장 가능하고 신뢰할 수 있으며 관리가 가능하도록 구현하는 데 집중할 수 있습니다. 

각 모범 사례에 대해 다음과 같이 설명합니다.

-   각 모범 사례
-   해당 모범 사례를 사용해야 하는 이유
-   해당 모범 사례를 사용하지 않을 경우에 발생할 수 있는 결과
-   해당 모범 사례를 사용하는 방법을 알아보는 방법

현재 다음과 같은 Azure Service Fabric 보안 모범 사례가 있습니다.

-   ARM(Azure Resource Manager) 템플릿 및 Service Fabric Azure PowerShell 모듈을 사용하여 보안 클러스터 만들기
-   X.509 인증서 사용
-   보안 정책 구성
-   Reliable Actors 보안 구성
-   Azure Service Fabric에 대해 SSL 구성
-   Azure Service Fabric을 통한 네트워크 격리/보안
-   보안을 위해 Key Vault 설정
-   역할에 사용자 할당


## <a name="best-practices-for-securing-your-cluster"></a>클러스터를 보호하기 위한 모범 사례

**큰 그림**

항상 보안 클러스터 사용
-   클러스터 보안 - 인증서 사용
-   클라이언트 액세스(관리자 및 읽기 전용) – AAD 사용

자동 배포 사용
-   스크립트를 사용하여 암호 생성, 배포 및 배치
-   암호를 KV에 유지, 모든 다른 클라이언트 액세스에 AD 사용.
-   인증 없이는 누구도 액세스할 수 없습니다.

또한 다음을 고려해 보세요.
-   네트워크 보안 그룹(NSG)을 사용하여 DMZ 만들기
-   점프 서버를 사용하여 클러스터 VM으로 RDP 또는 클러스터 관리

실행 중인 프로덕션 작업이 있을 때 권한 없는 사용자가 클러스터에 연결하지 못하도록 하여 클러스터의 보안을 유지해야 합니다. 보안이 되지 않은 클러스터를 만들 수 있지만, 이렇게 하면 공용 인터넷에 관리 끝점이 노출될 경우 익명 사용자가 클러스터에 연결할 수 있게 됩니다.

이러한 시나리오를 구현하는 데 사용되는 기술. [클러스터 보안 시나리오](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security):

-   노드 - 노드 보안: VM과 클러스터 내 컴퓨터 간의 통신을 보호합니다. 이렇게 하면 클러스터에 가입하도록 인증된 컴퓨터만 호스팅 응용 프로그램 및 클러스터의 서비스에 참여할 수 있습니다.
Azure에서 실행되는 클러스터 또는 Windows에서 실행되는 독립 실행형 클러스터는 Windows Server 컴퓨터에 대한 [인증서 보안](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-x509-security) 또는 [Windows 보안](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-windows-cluster-windows-security)을 사용할 수 있습니다.
-   클라이언트 - 노드 보안: 클라이언트를 인증하고 Service Fabric 클라이언트와 클러스터의 개별 노드 간 통신을 보호합니다.
-   역할 기반 액세스 제어(RBAC): 클러스터를 만들 때 각각에 대해 개별 ID(인증서, AAD 등)를 제공하여 관리자 및 사용자 클라이언트 역할을 지정합니다.
-   보안 권고: Azure 클러스터의 경우 AAD 보안을 사용하여 노드 간 보안에 대해 클라이언트 및 인증서를 인증하는 것이 좋습니다.

독립 실행형 Windows 클러스터를 구성하려면 [독립 실행형 Windows 클러스터에 대한 설정 구성](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-manifest)을 참조하세요.

Azure Resource Manager 템플릿 및 Service Fabric Azure PowerShell 모듈을 사용하여 보안 클러스터를 만듭니다.
Azure Resource Manager를 사용하여 Azure에 보안 Azure Service Fabric 클러스터를 설정하는 단계를 안내하는 단계별 가이드는 [여기](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm)서 제공합니다.

Azure Resource Manager 템플릿을 사용하여 클러스터 사용자 지정
-   VM VHD에 대한 설치 관리 저장소

Azure Resource Manager 템플릿을 사용하여 변경 사항을 리소스 그룹에 적용
-   간편한 구성 관리
-   감사

클러스터 구성을 코드로 처리
-   배포하도록 선택한 구성의 심층적 확인
-   리소스를 직접 조정하기 위해 암시적 명령을 사용하지 않음

[서비스 패브릭 응용 프로그램 수명 주기](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-lifecycle) 의 여러 측면을 자동화할 수 있습니다. [Service Fabric Azure PowerShell 모듈](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications#upload-the-application-package)은 Azure Service Fabric 응용 프로그램의 배포, 업그레이드, 제거 및 테스트를 위한 일반적인 작업을 자동화합니다. 앱 관리를 위한 관리되는 API 및 HTTP API도 사용 가능합니다.

## <a name="use-x509-certificates"></a>X.509 인증서 사용
항상 X.509 인증서 또는 Windows 보안을 사용하여 클러스터를 보호해야 합니다. 클러스터 생성 시에만 보안을 구성하므로 클러스터를 만든 후에 보안을 사용하도록 설정할 수 없습니다.

[클러스터 인증서](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-x509-security)를 지정하는 경우 ClusterCredentialType 값을 X509로 설정합니다. 외부 연결에 대한 서버 인증서를 지정하려면 ServerCredentialType을 X509로 설정합니다.

-   프로덕션 워크로드를 실행하는 클러스터에 사용되는 인증서는 올바르게 구성된 Windows Server 인증서 서비스를 사용하여 만들어지거나 승인된 CA(인증 기관)에서 획득해야 합니다.
-   프로덕션 환경에서 MakeCert.exe와 같은 도구를 사용하여 만든 임시 또는 테스트 인증서를 사용하지 마세요.
-   자체 서명된 인증서를 사용할 수 있지만 프로덕션이 아닌 테스트 클러스터에 대해서만 이러한 인증서를 사용해야 합니다.

클러스터는 안전하지 않은 경우 누구든지 익명으로 연결하고 관리 작업을 수행할 수 있으므로 프로덕션 클러스터가 항상 X.509 인증서 또는 Windows 보안을 사용하여 보호되어야 합니다.

Service Fabric 클러스터에서의 인증서 활성화 방법에 대한 자세한 내용은 [Service Fabric 클러스터에 대한 인증서 추가 또는 제거](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security-update-certs-azure)를 참조하세요.

## <a name="configure-security-policies"></a>보안 정책 구성
또한 Service Fabric으로 배포 시 파일, 디렉터리, 인증서 등과 같은 사용자 계정을 통해 응용 프로그램에서 사용하는 리소스도 보호합니다. 따라서 공유되는 호스티드 환경에서도 서로 더욱 안전하게 응용 프로그램을 실행할 수 있습니다.

-   Active Directory 도메인 그룹 또는 사용자사용: Active Directory 사용자 또는 그룹 계정에 대한 자격 증명에서 서비스를 실행할 수 있습니다. 도메인 내의 Active Directory 온-프레미스이며 Azure Active Directory(Azure AD)를 사용하지 않습니다. 도메인 사용자 또는 그룹을 사용하여 권한이 부여된 도메인의 다른 리소스(예: 파일 공유)에 액세스할 수 있습니다.

-   HTTP 및 HTTPS 끝점에 대해 보안 액세스 정책 할당: 서비스에 RunAs 정책을 적용하고 서비스 매니페스트에서 HTTP 프로토콜을 사용하여 끝점 리소스를 선언할 경우 이러한 끝점에 할당된 포트가 서비스가 실행되는 RunAs 사용자 계정에 대해 올바르게 액세스 제어 나열되도록 SecurityAccessPolicy를 지정해야 합니다. 그러지 않으면 http.sys가 해당 서비스에 액세스할 수 없고 클라이언트의 호출과 함께 오류가 발생합니다.
Service Fabric에서의 보안 정책 활성화에 대한 자세한 내용은 [응용 프로그램에 대한 보안 정책 구성](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-runas-security)을 참조하세요.

## <a name="reliable-actors-security-configuration"></a>Reliable Actors 보안 구성
서비스 패브릭 Reliable Actors는 행위자 디자인 패턴의 구현입니다. 소프트웨어 디자인 패턴처럼 특정 패턴을 사용하도록 결정하는 것은 소프트웨어 디자인 문제가 패턴에 맞는지 여부에 따라 이루어집니다.

일반적인 지침으로 문제 또는 시나리오를 모델링하기 위해 행위자 패턴을 고려합니다.
-   문제 영역은 1,000개 이상이라는 많은 수의 작고 독립적인 격리된 상태 및 논리 단위를 포함합니다.
-   일련의 행위자에 걸친 상태를 쿼리하는 등 외부 구성 요소에서 중요한 상호 작용을 필요로 하지 않는 단일 스레드 개체로 작업하려고 합니다.
-   행위자 인스턴스는 I/O 작업을 실행하여 예측할 수 없는 지연으로 호출자를 차단하지 않습니다.

서비스 패브릭에서 행위자는 Reliable Actors 프레임워크, 즉 [서비스 패브릭 Reliable Services](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-introduction)에 기반한 행위자 패턴 기반 응용 프로그램 프레임워크에서 구현됩니다. 작성한 Reliable Actor 서비스는 각각 실제로 분할된 상태 저장 Reliable Service입니다.
.NET 개체가 .NET 형식의 인스턴스인 것과 동일하게 모든 행위자는 행위자 형식의 인스턴스로 정의됩니다. 예를 들어, 계산기의 기능을 구현하는 행위자 형식이 있을 수 있으며 클러스터 전체에 걸쳐 다양한 노드에 배포되는 해당 형식의 여러 행위자가 있을 수 있습니다. 이러한 각 행위자는 행위자 ID에 의해 고유하게 식별됩니다.

[복제자 보안 구성](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-kvsactorstateprovider-configuration)은 복제하는 동안 사용되는 통신 채널을 보호하는 데 사용됩니다. 따라서 서비스는 서로의 복제 트래픽을 볼 수 없으므로 항상 사용 가능하게 설정한 데이터를 안전하게 보호할 수 있습니다. 기본적으로 빈 보안 구성 섹션에서는 복제 보안이 되지 않습니다.
복제자 구성은 행위자 상태 제공자의 상태를 매우 안정적으로 만드는 작업을 담당하는 복제자를 구성합니다.

## <a name="configure-ssl-for-azure-service-fabric"></a>Azure Service Fabric에 대해 SSL 구성

서버 인증: 관리 클라이언트에 클러스터 관리 끝점을 [인증하여](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) 관리 클라이언트기 실제 클러스터와 통신하는지 알 수 있도록 합니다. 이 인증은 HTTPS 관리 API 및 HTTPS를 통한 Service Fabric Explorer용 [SSL](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-cluster-creation-via-arm)도 제공합니다.
클러스터에 대한 사용자 지정 도메인 이름을 획득해야 합니다. CA에서 인증서를 요청하는 경우 인증서의 주체 이름이 클러스터에 사용되는 사용자 지정 도메인 이름과 일치해야 합니다.

응용 프로그램에 대해 SSL을 구성하려면 먼저 이 목적으로 인증서를 발급하는 신뢰할 수 있는 타사 CA(인증 기관)에서 서명한 SSL 인증서를 가져와야 합니다. 아직 없는 경우 SSL 인증서를 판매하는 회사에서 구입해야 합니다.

인증서는 Azure의 SSL 인증서에 대한 다음 요구 사항을 충족해야 합니다.
-   인증서에 개인 키가 포함되어 있어야 합니다.
-   개인 정보 교환(.pfx) 파일로 내보낼 수 있는 키 교환용 인증서를 만들어야 합니다.
-   인증서의 주체 이름은 클라우드 서비스 액세스에 사용되는 도메인과 일치해야 합니다. cloudapp.net 도메인에 사용되는 SSL 인증서는 CA(인증 기관)에서 얻을 수 없습니다. 서비스에 액세스할 때 사용할 사용자 지정 도메인 이름을 획득해야 합니다. CA에서 인증서를 요청하는 경우 인증서의 주체 이름이 응용 프로그램 액세스에 사용되는 사용자 지정 도메인 이름과 일치해야 합니다. 예를 들어 사용자 지정 도메인 이름이 contoso.com인 경우 CA에서 **.contoso.com** 또는 **www.contoso.com**에 대한 인증서를 요청합니다.
-   인증서는 최소한 2048비트 암호화를 사용해야 합니다.

HTTP는 웹 브라우저에서 웹 브라우저로 전송되거나 다른 끝점 간에 전송되는 데이터가 일반 텍스트로 전송되기 때문에 안전하지 않고 도청 공격의 대상이 됩니다. 즉 공격자가 신용카드 정보와 계정 로그인 같은 중요 데이터를 가로채 볼 수 있습니다. 데이터가 HTTPS를 사용하는 브라우저를 통해 전송 또는 게시될 경우 SSL이 그러한 정보를 암호화하여 가로채기로부터 보호합니다.

자세한 내용은 [Azure 응용 프로그램에 대한 SSL 구성](https://docs.microsoft.com/azure/cloud-services/cloud-services-configure-ssl-certificate)을 참조하세요.

## <a name="network-isolationsecurity-with-azure-service-fabric"></a>Azure Service Fabric을 통한 네트워크 격리/보안
[ARM(Azure Resource Manager) 템플릿](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)을 샘플로 사용하여 3개의 노드 유형 보안 클러스터를 설정하고 네트워크 보안 그룹을 사용하여 인바운드 및 아웃바운드 네트워크 트래픽을 제어합니다.

템플릿에는 VMSS로 들어오고 나가는 트래픽의 제어를 위해 각각의 가상 컴퓨터 확장 집합(VMSS)에 대한 네트워크 보안 그룹이 있습니다. 기본적으로 규칙은 템플릿에서 명시한 응용 프로그램 포트와 시스템 서비스에서 필요한 모든 트래픽을 허용하도록 설정됩니다. 이러한 규칙을 검토하고 응용 프로그램에 대한 새 규칙을 추가하는 등, 필요에 따라 변경합니다.

자세한 내용은 [Azure Service Fabric – 일반적인 네트워킹 시나리오](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking)를 참조하세요.

## <a name="set-up-a-key-vault-for-security"></a>보안을 위해 Key Vault 설정
인증서는 서비스 패브릭에서 클러스터 및 해당 응용 프로그램의 다양한 측면을 보호하기 위해 인증 및 암호화를 제공하는 데 사용됩니다.

서비스 패브릭은 클러스터에 보안 적용을 하고 응용 프로그램 보안 기능을 제공하기 위해 X.509 인증서를 사용합니다. Key Vault는 Azure에서 Service Fabric 클러스터에 대한 [인증서를 관리](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security-update-certs-azure)하는 데 사용됩니다. 클러스터를 Azure에 배포할 때 Service Fabric 클러스터 생성을 담당하는 Azure 리소스 공급자는 Key Vault에서 인증서를 가져와 클러스터 VM에 설치합니다.

클러스터를 만들 때 Key Vault에 저장되는 인증서를 사용하는 [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault), Service Fabric 클러스터 및 Azure 리소스 공급자 관의 관계.

**리소스 그룹 만들기** 첫 번째 단계는 Key Vault 전용 리소스 그룹을 만드는 것입니다. Key Vault를 자체 리소스 그룹에 배치하는 것이 좋습니다. 이렇게 하면 키 및 암호는 유실하지 않고 Service Fabric 클러스터가 있는 리소스 그룹과 같은 계산 및 저장소 리소스 그룹을 제거할 수 있습니다. 사용자 Key Vault를 포함하는 리소스 그룹은 해당 그룹을 사용하는 클러스터와 동일한 지역에 있어야 합니다.

**새 리소스 그룹에 Key Vault 만들기** 연산 리소스 공급자가 인증서를 가져와 가상 컴퓨터 인스턴스에 설치할 수 있도록 Key Vault를 배포에 대해 사용하도록 설정해야 합니다.
Azure Key Vault 설정 방법에 대한 자세한 내용은 [Azure Key Vault 시작](https://docs.microsoft.com/azure/key-vault/key-vault-get-started)을 참조하세요.

## <a name="assign-users-roles"></a>사용자 역할 할당
클러스터를 나타내는 응용 프로그램을 만들었으면 사용자를 Service Fabric에서 지원하는 역할(읽기 전용 및 관리자)에 할당합니다. Azure 클래식 포털을 사용하여 역할을 할당할 수 있습니다.

>[!Note]
> 서비스 패브릭의 역할에 대한 자세한 내용은 [서비스 패브릭 클라이언트의 역할 기반 액세스 제어](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security-roles)를 참조하세요.

Azure Service Fabric은 [Service Fabric 클러스터](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm)에 연결된 클라이언트 즉, 관리자 및 사용자에 대한 액세스 제어 형식을 지원합니다. 액세스 제어를 사용하면 클러스터 관리자가 사용자 그룹마다 특정 클러스터 작업에 대한 액세스를 제한하여 클러스터의 보안을 강화할 수 있습니다.

## <a name="next-steps"></a>다음 단계
- Service Fabric [개발 환경](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started) 설정
- [Service Fabric 지원 옵션](https://docs.microsoft.com/azure/service-fabric/service-fabric-support)에 대해 알아봅니다.


