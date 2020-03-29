---
title: 컨테이너 인스턴스에 대한 보안
description: Azure 컨테이너 인스턴스에 대한 이미지 및 보안 보안 및 컨테이너 플랫폼에 대한 일반적인 보안 고려 사항에 대한 권장 사항
ms.topic: article
ms.date: 01/10/2020
ms.custom: ''
ms.openlocfilehash: b5f2c4d9ca80318574e288110fd4ce7f490af00d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76260500"
---
# <a name="security-considerations-for-azure-container-instances"></a>Azure 컨테이너 인스턴스에 대한 보안 고려 사항

이 문서에서는 Azure 컨테이너 인스턴스를 사용하여 컨테이너 앱을 실행하기 위한 보안 고려 사항을 소개합니다. 다룰 주제는 다음과 같습니다.

> [!div class="checklist"]
> * Azure 컨테이너 인스턴스에 대한 이미지 및 보안 관리를 위한 **보안 권장 사항**
> * 컨테이너 수명 주기 전반에 걸친 **컨테이너 에코시스템(모든** 컨테이너 플랫폼)에 대한 고려 사항

## <a name="security-recommendations-for-azure-container-instances"></a>Azure 컨테이너 인스턴스에 대한 보안 권장 사항

### <a name="use-a-private-registry"></a>개인 레지스트리 사용

컨테이너는 하나 이상의 리포지토리에 저장된 이미지에서 작성됩니다. 이러한 리포지토리는 [Docker Hub와](https://hub.docker.com)같은 공용 레지스트리 또는 개인 레지스트리에 속할 수 있습니다. 프라이빗 레지스트리의 예는 [Docker Trusted Registry](https://docs.docker.com/datacenter/dtr/)이며 온-프레미스 또는 가상 프라이빗 클라우드에 설치할 수 있습니다. [Azure](../container-registry/container-registry-intro.md)컨테이너 레지스트리를 포함하여 클라우드 기반 개인 컨테이너 레지스트리 서비스를 사용할 수도 있습니다. 

공개적으로 사용할 수 있는 컨테이너 이미지는 보안을 보장하지 않습니다. 컨테이너 이미지는 여러 소프트웨어 계층으로 구성되며 각 소프트웨어 계층에 취약점이 있을 수 있습니다. 공격의 위협을 줄이려면 Azure 컨테이너 레지스트리 또는 Docker 신뢰할 수 있는 레지스트리와 같은 개인 레지스트리에서 이미지를 저장하고 검색해야 합니다. Azure 컨테이너 레지스트리는 관리되는 개인 레지스트리를 제공하는 것 외에도 기본 인증 흐름에 대해 Azure Active Directory를 통해 [서비스 주체 기반 인증을](../container-registry/container-registry-authentication.md) 지원합니다. 이 인증에는 읽기 전용(끌어오기), 쓰기(푸시) 및 기타 권한에 대한 역할 기반 액세스가 포함됩니다.

### <a name="monitor-and-scan-container-images"></a>컨테이너 이미지 모니터링 및 스캔

솔루션을 활용하여 개인 레지스트리에서 컨테이너 이미지를 스캔하고 잠재적인 취약점을 식별합니다. 다양한 솔루션이 제공하는 위협 탐지의 깊이를 이해하는 것이 중요합니다.

예를 들어 Azure 컨테이너 레지스트리는 선택적으로 [Azure 보안 센터와 통합되어](../security-center/azure-container-registry-integration.md) 레지스트리로 푸시된 모든 Linux 이미지를 자동으로 검색합니다. Azure Security Center의 통합 Qualys 스캐너는 이미지 취약점을 감지하고 분류하며 수정 지침을 제공합니다.

[Twistlock](https://azuremarketplace.microsoft.com/marketplace/apps/twistlock.twistlock?tab=Overview) 및 [Aqua 보안과](https://azuremarketplace.microsoft.com/marketplace/apps/aqua-security.aqua-security?tab=Overview) 같은 보안 모니터링 및 이미지 검색 솔루션도 Azure 마켓플레이스를 통해 사용할 수 있습니다.  

### <a name="protect-credentials"></a>자격 증명 보호

컨테이너는 여러 클러스터 및 Azure 지역에 분산될 수 있습니다. 따라서 암호 나 토큰과 같은 로그인 또는 API 액세스에 필요한 자격 증명을 보호해야합니다. 권한이 있는 사용자만 전송 중 및 미사용 컨테이너에 액세스할 수 있는지 확인합니다. 모든 자격 증명 비밀을 인벤토리한 다음 개발자가 컨테이너 플랫폼에 대해 설계된 새로운 비밀 관리 도구를 사용하도록 요구합니다.  솔루션에 암호화된 데이터베이스, 전송 중 비밀 데이터에 대한 TLS 암호화 및 최소 권한 [역할 기반 액세스 제어가](../role-based-access-control/overview.md)포함되어 있는지 확인합니다. [Azure Key Vault는](../key-vault/key-vault-secure-your-key-vault.md) 컨테이너화된 응용 프로그램에 대한 암호화 키 및 암호(예: 인증서, 연결 문자열 및 암호)를 보호하는 클라우드 서비스입니다. 이 데이터는 중요하고 업무상 중요하므로 권한이 부여된 응용 프로그램 및 사용자만 액세스할 수 있도록 키 자격 증명 모음에 대한 보안 액세스가 가능합니다.

## <a name="considerations-for-the-container-ecosystem"></a>컨테이너 생태계에 대한 고려 사항

잘 구현되고 효과적으로 관리되는 다음 보안 조치는 컨테이너 에코시스템을 보호하고 보호하는 데 도움이 될 수 있습니다. 이러한 측정값은 개발에서 프로덕션 배포에 이르는 컨테이너 수명 주기 전반에 걸쳐 적용되며 다양한 컨테이너 오케스트레이터, 호스트 및 플랫폼에 적용됩니다. 

### <a name="use-vulnerability-management-as-part-of-your-container-development-lifecycle"></a>컨테이너 개발 수명 주기의 일부로 취약점 관리 사용 

컨테이너 개발 수명 주기 전반에 걸쳐 효과적인 취약성 관리를 사용하면 보안 문제가 더 심각한 문제가 되기 전에 식별하고 해결할 가능성을 개선할 수 있습니다. 

### <a name="scan-for-vulnerabilities"></a>취약성 검사 

새로운 취약점이 항상 발견되므로 취약점을 검색하고 식별하는 것은 지속적인 프로세스입니다. 컨테이너 수명 주기 전체에 취약점 검색을 통합합니다.

* 개발 파이프라인의 최종 검사로 이미지를 공용 또는 개인 레지스트리로 푸시하기 전에 컨테이너에서 취약성 검사를 수행해야 합니다. 
* 레지스트리에서 컨테이너 이미지를 계속 검사하여 개발 중에 누락된 결함을 식별하고 컨테이너 이미지에 사용된 코드에 존재할 수 있는 새로 발견된 취약점을 해결합니다.  

### <a name="map-image-vulnerabilities-to-running-containers"></a>실행 중인 컨테이너에 이미지 취약성 매핑 

보안 문제를 완화하거나 해결할 수 있도록 컨테이너 이미지에서 식별된 취약점을 실행 중인 컨테이너에 매핑하는 수단이 있어야 합니다.  

### <a name="ensure-that-only-approved-images-are-used-in-your-environment"></a>사용자 환경에서 승인된 이미지만 사용되도록 합니다. 

알 수 없는 컨테이너를 허용하지 않고 컨테이너 생태계에 충분한 변화와 변동성이 있습니다. 승인된 컨테이너 이미지만 허용합니다. 승인되지 않은 컨테이너 이미지의 사용을 모니터링하고 방지할 수 있는 도구와 프로세스가 마련되어 있습니다. 

공격 표면을 줄이고 개발자가 중요한 보안 실수를 하지 못하도록 하는 효과적인 방법은 컨테이너 이미지가 개발 환경으로 유입되는 것을 제어하는 것입니다. 예를 들어, 기본 이미지로 단일 리눅스 배포판을 제재할 수 있습니다., 바람직하게는 마른 (알파인 또는 CoreOS 보다는 우분투), 잠재적인 공격에 대 한 표면을 최소화 하기 위해. 

이미지 서명 또는 지문은 컨테이너의 무결성을 확인할 수 있는 일련의 양육권을 제공할 수 있습니다. 예를 들어 Azure 컨테이너 레지스트리는 Docker의 [콘텐츠 신뢰](https://docs.docker.com/engine/security/trust/content_trust) 모델을 지원하므로 이미지 게시자가 레지스트리로 푸시된 이미지에 서명하고 이미지 소비자가 서명된 이미지만 가져올 수 있습니다.

### <a name="permit-only-approved-registries"></a>승인된 레지스트리만 허용 

사용자 환경에서 승인된 이미지만 사용하도록 보장하는 확장은 승인된 컨테이너 레지스트리만 사용하도록 허용하는 것입니다. 승인된 컨테이너 레지스트리를 사용하도록 요구하면 알 수 없는 취약점이나 보안 문제가 발생할 가능성을 제한하여 위험에 노출될 수 있습니다. 

### <a name="ensure-the-integrity-of-images-throughout-the-lifecycle"></a>수명 주기 전반에 걸쳐 이미지의 무결성 보장 

컨테이너 수명 주기 전반에 걸쳐 보안을 관리하는 일의 일부는 레지스트리에서 컨테이너 이미지가 프로덕션에 변경되거나 배포될 때 컨테이너 이미지의 무결성을 보장하는 것입니다. 

* 사소한 취약점이 있는 이미지는 프로덕션 환경에서 실행해서는 안 됩니다. 프로덕션 환경에서 배포된 모든 이미지는 소수의 사용자가 액세스할 수 있는 개인 레지스트리에 저장해야 하는 것이 좋습니다. 프로덕션 이미지 수를 작게 유지하여 효과적으로 관리할 수 있도록 합니다.

* 공개적으로 사용 가능한 컨테이너 이미지에서 소프트웨어의 출처를 정확히 파악하기어렵기 때문에 소스에서 이미지를 빌드하여 레이어의 출처에 대한 지식을 확보합니다. 자체 구축된 컨테이너 이미지에 취약성이 있을 경우 고객은 보다 신속하게 해결 방법을 찾을 수 있습니다. 공용 이미지를 사용하면 공개 이미지의 루트를 찾아 수정하거나 게시자로부터 다른 보안 이미지를 얻어야 합니다. 

* 프로덕션 환경에서 철저하게 스캔된 이미지는 응용 프로그램의 수명 동안 최신 상태로 유지되지 않습니다. 보안 취약성은 이전에 알려지지 않았거나 프로덕션 배포 후에 도입 된 이미지 계층에 대해 보고될 수 있습니다. 

  프로덕션 에 배포된 이미지를 주기적으로 감사하여 오래되었거나 한동안 업데이트되지 않은 이미지를 식별합니다. 청록색 배포 방법론및 롤링 업그레이드 메커니즘을 사용하여 가동 중지 시간 없이 컨테이너 이미지를 업데이트할 수 있습니다. 이전 섹션에 설명된 도구를 사용하여 이미지를 스캔할 수 있습니다. 

* 통합 된 보안 검색과 지속적인 통합 (CI) 파이프라인을 사용 하 여 보안 이미지를 구축 하 고 개인 레지스트리에 푸시. CI 솔루션에 기본 제공된 취약성 검색은 모든 테스트를 통과한 이미지가 프로덕션 워크로드가 배포된 프라이빗 레지스트리에 푸시되는지 확인합니다. 

  CI 파이프라인 오류는 취약한 이미지가 프로덕션 워크로드 배포에 사용되는 개인 레지스트리로 푸시되지 않도록 합니다. 또한 많은 수의 이미지가 있는 경우 이미지 보안 검색을 자동화합니다. 그렇지 않고 이미지에서 보안 취약성을 수동으로 감사하는 것은 매우 번거롭고 오류가 발생하기 쉽습니다. 

### <a name="enforce-least-privileges-in-runtime"></a>런타임에 최소 권한 적용 

최소 권한의 개념은 컨테이너에도 적용되는 기본 보안 모범 사례입니다. 취약점이 악용되면 일반적으로 공격자에게 손상된 응용 프로그램 이나 프로세스와 동일한 액세스 및 권한을 부여합니다. 컨테이너가 작업을 수행하는 데 필요한 가장 낮은 권한과 액세스 권한으로 작동하도록 하면 위험에 노출될 수 있습니다. 

### <a name="reduce-the-container-attack-surface-by-removing-unneeded-privileges"></a>불필요한 권한을 제거하여 컨테이너 공격 표면을 줄입니다. 

또한 사용하지 않거나 불필요한 프로세스 또는 권한을 컨테이너 런타임에서 제거하여 잠재적인 공격 노출 가능성을 최소화할 수 있습니다. 권한 있는 컨테이너는 루트로 실행됩니다. 악의적인 사용자 또는 워크로드가 권한 있는 컨테이너에서 이스케이프되면 컨테이너가 해당 시스템의 루트로 실행됩니다.

### <a name="preapprove-files-and-executables-that-the-container-is-allowed-to-access-or-run"></a>컨테이너가 액세스하거나 실행할 수 있는 파일 및 실행 파일 사전 승인 

변수 또는 알 수 없는 수를 줄이면 안정적이고 신뢰할 수 있는 환경을 유지하는 데 도움이 됩니다. 미리 승인된 파일과 실행 파일만 액세스하거나 실행할 수 있도록 컨테이너를 제한하는 것은 위험에 대한 노출을 제한하는 입증된 방법입니다.  

처음부터 구현될 때 보호 목록을 관리하는 것이 훨씬 쉽습니다. 사용 정보 목록은 응용 프로그램이 올바르게 작동하는 데 필요한 파일 및 실행 파일을 학습할 때 제어 및 관리 용이성을 측정합니다. 

안전 목록은 공격 표면을 줄일 뿐만 아니라 이상 에 대한 기준을 제공하고 "시끄러운 이웃" 및 컨테이너 브레이크아웃 시나리오의 사용 사례를 방지할 수 있습니다. 

### <a name="enforce-network-segmentation-on-running-containers"></a>실행 중인 컨테이너에 네트워크 세분화 적용  

한 서브넷의 컨테이너를 다른 서브넷의 보안 위험으로부터 보호하려면 네트워크 세분화(또는 나노 세분화) 또는 실행 중인 컨테이너 간의 분리를 유지관리합니다. 규정 준수 규정을 충족하는 데 필요한 산업에서 컨테이너를 사용하려면 네트워크 세분화를 유지 관리해야 할 수도 있습니다.  

예를 들어, 파트너 도구 [Aqua는](https://azuremarketplace.microsoft.com/marketplace/apps/aqua-security.aqua-security?tab=Overview) 나노 세분화에 대한 자동화된 접근 방식을 제공합니다. Aqua는 런타임에 컨테이너 네트워크 활동을 모니터링합니다. 다른 컨테이너, 서비스, IP 주소 및 공용 인터넷을 통해 모든 인바운드 및 아웃바운드 네트워크 연결을 식별합니다. 나노 세분화는 모니터링된 트래픽을 기반으로 자동으로 생성됩니다. 

### <a name="monitor-container-activity-and-user-access"></a>컨테이너 활동 및 사용자 액세스 모니터링 

다른 IT 환경과 마찬가지로 컨테이너 에코시스템에 대한 활동 및 사용자 액세스를 지속적으로 모니터링하여 의심스럽거나 악의적인 활동을 신속하게 식별해야 합니다. Azure는 다음을 포함한 컨테이너 모니터링 솔루션을 제공합니다.

* [컨테이너용 Azure 모니터는](../azure-monitor/insights/container-insights-overview.md) AKS(Azure Kubernetes Service)에서 호스팅되는 Kubernetes 환경에 배포된 워크로드의 성능을 모니터링합니다. 컨테이너용 Azure Monitor는 Metrics API를 통해 Kubernetes에서 사용할 수 있는 컨트롤러, 노드 및 컨테이너의 메모리 및 프로세서 메트릭을 수집하여 성능을 시각적으로 표시합니다. 

* [Azure 컨테이너 모니터링 솔루션을](../azure-monitor/insights/containers.md) 사용하면 단일 위치에서 다른 Docker 및 Windows 컨테이너 호스트를 보고 관리할 수 있습니다. 예를 들어:

  * 컨테이너와 함께 사용되는 명령을 보여 주는 자세한 감사 정보를 봅니다. 
  * Docker 또는 Windows 호스트를 원격으로 볼 필요 없이 중앙 집중식 로그를 보고 검색하여 컨테이너 문제를 해결합니다.  
  * 시끄러운 경우 호스트에서 과도한 리소스를 사용하는 컨테이너를 찾습니다.
  * 컨테이너에 대한 중앙 집중식 CPU, 메모리, 스토리지 및 네트워크 사용 및 성능 정보를 봅니다.  

  이 솔루션은 Docker Swarm, DC/OS, 관리되지 않는 Kubernetes, 서비스 패브릭 및 Red Hat OpenShift를 포함한 컨테이너 오케스트레이터를 지원합니다. 

### <a name="monitor-container-resource-activity"></a>컨테이너 리소스 활동 모니터링 

컨테이너가 액세스하는 파일, 네트워크 및 기타 리소스와 같은 리소스 활동을 모니터링합니다. 리소스 활동 및 소비를 모니터링하는 것은 성능 모니터링과 보안 측정에 모두 유용합니다. 

[Azure Monitor](../azure-monitor/overview.md)를 사용하면 메트릭, 활동 로그 및 진단 로그를 수집하도록 허용하여 Azure 서비스에 대한 핵심 모니터링을 수행할 수 있습니다. 예를 들어, 활동 로그는 새 리소스가 만들어지거나 수정되는 시기를 알려줍니다. 

  다양한 리소스와 가상 머신 내부의 운영 체제에 대한 성능 통계를 제공하는 메트릭을 사용할 수 있습니다. Azure Portal에서 탐색기 중 하나를 사용하여 이 데이터를 보고 이러한 메트릭을 기반으로 경고를 만들 수 있습니다. Azure Monitor는 가장 빠른 메트릭 파이프라인을 제공하므로(5분에서 1분으로) 시간이 중요한 경고 및 알림에 사용해야 합니다. 

### <a name="log-all-container-administrative-user-access-for-auditing"></a>감사를 위해 모든 컨테이너 관리 사용자 액세스 기록 

Kubernetes 클러스터, 컨테이너 레지스트리 및 컨테이너 이미지를 포함하여 컨테이너 에코시스템에 대한 관리 액세스에 대한 정확한 감사 추적을 유지합니다. 이러한 로그는 감사 목적으로 필요할 수 있으며 보안 사고 후 법의학 적 증거로 유용할 수 있습니다. Azure 솔루션에는 다음이 포함됩니다.

* [Azure Kubernetes 서비스를 Azure 보안 센터와 통합하여](../security-center/azure-kubernetes-service-integration.md) 클러스터 환경의 보안 구성을 모니터링하고 보안 권장 사항을 생성합니다.
* [Azure 컨테이너 모니터링 솔루션](../azure-monitor/insights/containers.md)
* Azure 컨테이너 [인스턴스](container-instances-log-analytics.md) 및 [Azure 컨테이너 레지스트리에](../container-registry/container-registry-diagnostics-audit-logs.md) 대한 리소스 로그

## <a name="next-steps"></a>다음 단계

* 컨테이너화된 환경에서 실시간 위협 탐지를 위해 [Azure 보안 센터를](../security-center/container-security.md) 사용하는 방법에 대해 자세히 알아봅니다.

* [Twistlock](https://www.twistlock.com/solutions/microsoft-azure-container-security/) 및 [Aqua Security의](https://www.aquasec.com/solutions/azure-container-security/)솔루션으로 컨테이너 취약성 관리에 대해 자세히 알아보십시오.