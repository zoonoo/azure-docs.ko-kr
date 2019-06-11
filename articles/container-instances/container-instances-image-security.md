---
title: Azure Container Instances에 대 한 보안 고려 사항
description: Azure Container Instances 및 컨테이너 플랫폼에 대 한 일반적인 보안 고려 사항에 대 한 이미지 및 비밀을 보호 하는 권장 사항
services: container-instances
author: dlepow
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 04/29/2019
ms.author: danlep
ms.custom: ''
ms.openlocfilehash: dc516277d79e37500e73e1aee6b88c908acb9b1c
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64943998"
---
# <a name="security-considerations-for-azure-container-instances"></a>Azure Container Instances에 대 한 보안 고려 사항

이 문서에서는 Azure Container Instances를 사용 하 여 컨테이너 앱을 실행 하는 것에 대 한 보안 고려 사항을 소개 합니다. 다룰 주제는 다음과 같습니다.

> [!div class="checklist"]
> * **보안 권장 사항** Azure Container Instances에 대 한 이미지 및 비밀을 관리 하기 위한
> * **컨테이너 에코 시스템에 대 한 고려 사항** 모든 컨테이너 플랫폼에 대 한 컨테이너 수명

## <a name="security-recommendations-for-azure-container-instances"></a>Azure Container Instances에 대 한 보안 권장 사항

### <a name="use-a-private-registry"></a>개인 레지스트리를 사용 합니다.

컨테이너는 하나 이상의 리포지토리에 저장된 이미지에서 작성됩니다. 이러한 리포지토리는 공개 레지스트리 같은 속할 수 있습니다 [Docker 허브](https://hub.docker.com), 또는 개인 레지스트리입니다. 프라이빗 레지스트리의 예는 [Docker Trusted Registry](https://docs.docker.com/datacenter/dtr/2.0/)이며 온-프레미스 또는 가상 프라이빗 클라우드에 설치할 수 있습니다. 클라우드 기반 개인 컨테이너 레지스트리 서비스를 포함 하 여 사용할 수도 있습니다 [Azure Container Registry](../container-registry/container-registry-intro.md)합니다. 

공개적으로 사용 가능한 컨테이너 이미지는 보안을 보장 하지 않습니다. 컨테이너 이미지 여러 소프트웨어 계층으로 구성 되며 각 소프트웨어 계층 취약점으로 인 한 있을 수 있습니다. 공격 위협을 완화 하, 저장소 및 Azure Container Registry 또는 Docker Trusted Registry 같은 개인 레지스트리에서 이미지를 검색 됩니다. Azure Container Registry는 관리 되는 개인 레지스트리를 제공 하는 것 외에도 다음을 지원 합니다. [서비스 보안 주체 기반 인증](../container-registry/container-registry-authentication.md) 기본 인증 흐름에 대 한 Azure Active Directory를 통해. 이 인증 (끌어오기) 읽기 전용, 쓰기 (밀어넣기) 및 소유자 권한에 대 한 역할 기반 액세스를 포함합니다.

### <a name="monitor-and-scan-container-images"></a>모니터링 하 고 컨테이너 이미지를 검색 합니다.

보안 모니터링 및 검색 솔루션와 같은 [Twistlock](https://azuremarketplace.microsoft.com/marketplace/apps/twistlock.twistlock?tab=Overview) 하 고 [Aqua Security](https://azuremarketplace.microsoft.com/marketplace/apps/aqua-security.aqua-security?tab=Overview) Azure Marketplace를 통해 사용할 수 있습니다. 개인 레지스트리의 컨테이너 이미지를 검색 하 여 잠재적인 취약점을 식별 하 고 사용할 수 있습니다. 다양 한 솔루션을 제공 하는 검색의 깊이 이해 하는 것이 반드시 합니다. 

### <a name="protect-credentials"></a>자격 증명 보호

컨테이너는 여러 클러스터 및 Azure 지역에 걸쳐 분산 시킬 수 있습니다. 따라서 로그인 또는 암호 또는 토큰과 같은 API 액세스에 필요한 자격 증명을 보호 해야 합니다. 권한 있는 사용자만 전송 중 및 미사용 시 해당 컨테이너를 액세스할 수 있는지 확인 합니다. 모든 자격 증명 비밀을 인벤토리에 포함 하 고 개발자가 컨테이너 플랫폼에 대 한 설계 된 새로운 비밀 관리 도구를 사용 하도록 해야 합니다.  암호화 된 데이터베이스를 전송 중에 최소 권한 비밀 데이터에 대 한 TLS 암호화 솔루션에 포함 되는지 확인 하십시오 [역할 기반 액세스 제어](../role-based-access-control/overview.md)입니다. [Azure Key Vault](../key-vault/key-vault-secure-your-key-vault.md) 암호화 키 및 비밀 (예: 인증서, 연결 문자열 및 암호)을 보호 하는 클라우드 서비스를 컨테이너 화 된 응용 프로그램입니다. 이 데이터는 민감하고 업무상 중요 이기 때문에 승인 된 응용 프로그램 및 사용자만 액세스할 수 있도록 자격 증명 모음 키에 대 한 보안 액세스 합니다.

## <a name="considerations-for-the-container-ecosystem"></a>컨테이너 에코 시스템에 대 한 고려 사항

다음 보안 조치를 제대로 구현 하 고 효과적으로 관리 되는 사용자 컨테이너 에코 시스템을 보호할 수 있습니다. 이러한 측정값 범위 컨테이너 오 케 스트레이 터, 호스트 및 플랫폼의 프로덕션 배포를 통해 개발에서 컨테이너 수명 주기 전체에 적용 됩니다. 

### <a name="use-vulnerability-management-as-part-of-your-container-development-lifecycle"></a>컨테이너 개발 수명 주기의 일부로 사용 하 여 취약성 관리 

컨테이너 개발 주기 전반에 걸쳐 효과적인 취약점으로 인 한 관리를 사용 하 여 확인 하 고 보다 심각한 문제가 되기 전에 보안 문제를 해결 하는 경우가 더 많기는 개선할 수 있습니다. 

### <a name="scan-for-vulnerabilities"></a>취약성 검색 

새 취약성 검색 및 취약성을 식별 하는 지속적인 프로세스 이므로 항상 검색 됩니다. 컨테이너 수명 주기 내내 검색 하는 취약점으로 인 한 통합:

* 개발 파이프라인에서 최종 검사, 공용 또는 개인 레지스트리로 이미지를 푸시하기 전에 컨테이너에 대 한 취약성 검사를 수행할 해야 있습니다. 
* 계속 개발 하는 동안 어떤 이유로 든 않은 결함을 식별 하 고 컨테이너 이미지에 사용 되는 코드에 존재 하는 모든 새로 발견 된 취약점을 해결 하기 위해 레지스트리에서 컨테이너 이미지를 검색 합니다.  

### <a name="map-image-vulnerabilities-to-running-containers"></a>실행 중인 컨테이너를 맵 이미지 취약점 

매핑 취약점으로 인 한 보안 문제 완화 또는 해결 될 수 있으므로 컨테이너를 실행 하려면 컨테이너 이미지에서 식별 하는 수단을 해야 합니다.  

### <a name="ensure-that-only-approved-images-are-used-in-your-environment"></a>사용자 환경에만 승인 된 이미지를 사용 하는지 확인 

충분 한 변경 및 알 수 없는 컨테이너도 허용 하지 않고 컨테이너 생태계의 변동성 있습니다. 승인 된 유일한 컨테이너 이미지를 허용 합니다. 도구 및 프로세스에 대 한 모니터링 및 승인 되지 않은 컨테이너 이미지의 사용을 방지 하는 위치에 있습니다. 

공격 노출 영역을 줄이고 중요 한 보안 실수 개발자 방지 효과적으로 개발 환경에 컨테이너 이미지의 흐름을 제어 하는 것입니다. 오히려 린 (lean)는 기본 이미지로 단일 Linux 배포를 사용 권한 부여 될 수 있습니다 예를 들어 (Alpine CoreOS 대신 또는 Ubuntu)를 잠재적인 공격에 대 한 영역을 최소화 합니다. 

서명 또는 지문 인식을 이미지는 컨테이너의 무결성을 확인할 수 있도록 관리 권의 체인을 제공할 수 있습니다. Azure Container Registry는 Docker의를 지원 하는 예를 들어 [트러스트 콘텐츠](https://docs.docker.com/engine/security/trust/content_trust) 이미지 게시자 이미지를 레지스트리에 푸시되는 로그온을 허용 하는 모델 및 서명 된 이미지를 이미지 소비자입니다.

### <a name="permit-only-approved-registries"></a>레지스트리를 승인 허용 

환경만 승인 된 이미지는 확인의 확장인은 승인 된 컨테이너 레지스트리의 사용만을 허용 하는 합니다. 승인 된 컨테이너 레지스트리를 사용 하지 알 수 없는 보안 문제 또는 보안 문제가 미치는 가능성을 제한 하 여 위험에 대 한 노출을 줄입니다. 

### <a name="ensure-the-integrity-of-images-throughout-the-lifecycle"></a>수명 주기 내내 이미지의 무결성을 보장 

컨테이너 수명 주기 전체에서 보안 관리의 일부 레지스트리에서 컨테이너 이미지의 무결성을 보장 하는 것을 변경 하거나 프로덕션 환경에 배포 하 고 있습니다. 

* 취약성으로 라도 사용 하 여 이미지는 프로덕션 환경에서 실행할 수 없습니다. 이상적으로 프로덕션에 배포 된 모든 이미지는 몇 가지에 액세스할 수 있는 개인 레지스트리에 저장 수 있어야 합니다. 프로덕션 이미지 수를 효과적으로 관리할 수는 적게 유지 합니다.

* 공개적으로 사용 가능한 컨테이너 이미지를 소프트웨어의 출처를 파악 하기가 이기 때문에 계층의 출처를 확인 하려면 원본에서 이미지를 빌드하십시오. 자체 구축된 컨테이너 이미지에 취약성이 있을 경우 고객은 보다 신속하게 해결 방법을 찾을 수 있습니다. 공용 이미지를 사용 하 여 수정 하거나 게시자에서 다른 보안 이미지를 가져올 공용 이미지의 루트를 찾기 위해 고객에 게 해야 합니다. 

* 프로덕션에 배포 된 이미지를 철저 하 게 검색된 응용 프로그램의 수명에 대 한 최신 상태인 것으로 보장 되지 않습니다. 보안 취약성은 이전에 알려지지 않았거나 프로덕션 배포 후에 도입 된 이미지 계층에 대해 보고될 수 있습니다. 

  한 동안 업데이트 되지 않은 되거나 만료 되는 이미지를 식별 하려면 프로덕션에 배포 된 이미지를 정기적으로 감사 합니다. 가동 중지 시간 없이 컨테이너 이미지를 업데이트할 청록색 배포 방법론과 롤링 업그레이드 메커니즘을 사용할 수 있습니다. 이전 섹션에 설명 된 도구를 사용 하 여 이미지를 검색할 수 있습니다. 

* 보안 이미지를 빌드하고 개인 레지스트리에 푸시를 검색 하는 통합된 보안을 사용 하 여 연속 통합 (CI) 파이프라인을 사용 합니다. CI 솔루션에 기본 제공된 취약성 검색은 모든 테스트를 통과한 이미지가 프로덕션 워크로드가 배포된 프라이빗 레지스트리에 푸시되는지 확인합니다. 

  CI 파이프라인 오류는 취약 한 이미지가 프로덕션 워크 로드 배포에 사용 되는 개인 레지스트리에 푸시되 지 않은 확인 합니다. 또한 이미지 수가 많은 경우를 검색 하는 이미지 보안을 자동화 합니다. 그렇지 않고 이미지에서 보안 취약성을 수동으로 감사하는 것은 매우 번거롭고 오류가 발생하기 쉽습니다. 

### <a name="enforce-least-privileges-in-runtime"></a>런타임의 최소 권한 적용 

최소 권한 개념은 컨테이너에도 적용 되는 기본 보안 모범 사례. 취약점을 악용 하는 경우 일반적으로 공격자가 액세스를 제공 하 고 손상 된 응용 프로그램 또는 프로세스의 같은 권한. 컨테이너는 가장 낮은 권한으로 작동 하 고 작업을 수행 하는 데 필요한 액세스 위험에 대 한 노출을 줄일 수 있도록 보장 합니다. 

### <a name="reduce-the-container-attack-surface-by-removing-unneeded-privileges"></a>불필요 한 권한을 제거 하 여 컨테이너 공격 표면을 줄이기 

또한 컨테이너 런타임을에서 사용 되지 않거나 불필요 한 프로세스 또는 권한을 제거 하 여 잠재적인 공격 노출 영역을 최소화할 수 있습니다. 권한 있는 컨테이너 루트로 실행합니다. 권한 있는 컨테이너에서 악의적인 사용자 또는 워크 로드를 이스케이프 하는 경우 컨테이너에서 실행 됩니다 루트로 해당 시스템입니다.

### <a name="whitelist-files-and-executables-that-the-container-is-allowed-to-access-or-run"></a>허용 목록에 추가 파일 및 컨테이너에 액세스 하거나 실행 허용 되는 실행 파일 

변수 또는 알 수를 줄이면 통해 안정적이 고 신뢰할 수 있는 환경을 유지 관리할 수 있습니다. 컨테이너에 액세스할 수 있도록 또는 사전 승인만 실행 또는 허용 목록에 파일 및 실행 파일 제한는 위험에 대 한 노출을 제한 하는 입증 된 메서드입니다.  

많은 시작 부분에서 구현 되는 허용 목록을 관리 하는 것이 쉽습니다. 허용 목록을 배우게, 파일 및 실행 파일은 제대로 작동 하려면 응용 프로그램에 필요한 제어 및 관리 효율성의 측정값을 제공 합니다. 

허용 목록 뿐 아니라 공격 취약점 감소 하지만 변칙에 대 한 기준을 제공 하 고 수도 "시끄러운 이웃" 및 컨테이너 혁신 시나리오의 사용 사례를 방지. 

### <a name="enforce-network-segmentation-on-running-containers"></a>실행 중인 컨테이너에 네트워크 조각화를 적용 합니다.  

유지 관리 네트워크 조각화 (또는 nano 구분) 컨테이너 하나의 서브넷에서 다른 서브넷에 보안 위험 으로부터 보호 하기 위해, 또는 실행 중인 컨테이너 간에 분리 합니다. 유지 관리 네트워크 조각화는 컨테이너를 사용 하 여 규정 준수 의무를 충족 하는 데 필요한 업계에 하는 데 필요한 수도 있습니다.  

예를 들어 파트너 도구로 [Aqua](https://azuremarketplace.microsoft.com/marketplace/apps/aqua-security.aqua-security?tab=Overview) nano 구분에 대 한 자동화 된 접근 방식을 제공 합니다. 바다색 런타임에서 컨테이너 네트워크 활동을 모니터링합니다. 다른 컨테이너, 서비스, IP 주소 및 공용 인터넷의 모든 인바운드 및 아웃 바운드 네트워크 연결을 식별합니다. Nano 구분은 모니터링 되는 트래픽에서에 따라 자동으로 생성 됩니다. 

### <a name="monitor-container-activity-and-user-access"></a>컨테이너 작업 및 사용자 액세스를 모니터링 합니다. 

모든 IT 환경에서와 마찬가지로 모든 악성 또는 의심 스러운 활동을 신속 하 게 식별 하 여 컨테이너 에코 시스템에 활동 및 사용자 액세스를 지속적으로 모니터링 해야 합니다. Azure 컨테이너를 모니터링을 비롯 한 솔루션을 제공 합니다.

* [컨테이너에 대 한 azure Monitor](../azure-monitor/insights/container-insights-overview.md) Azure Kubernetes Service (AKS)에서 호스트 된 Kubernetes 환경에 배포 된 워크 로드의 성능을 모니터링할 수 있습니다. 컨테이너용 Azure Monitor는 Metrics API를 통해 Kubernetes에서 사용할 수 있는 컨트롤러, 노드 및 컨테이너의 메모리 및 프로세서 메트릭을 수집하여 성능을 시각적으로 표시합니다. 

* 합니다 [Azure 컨테이너 모니터링 솔루션](../azure-monitor/insights/containers.md) 확인 하 고 다른 Docker 및 Windows 관리를 한 곳에서 컨테이너 호스트입니다. 예를 들면 다음과 같습니다.

  * 컨테이너를 사용 하는 명령을 보여 주는 자세한 감사 정보를 봅니다. 
  * 원격 Docker 또는 Windows 호스트를 확인 하지 않고도 중앙 집중식된 로그를 검색 하 여 컨테이너를 해결 합니다.  
  * 호스트에서 성가 시 고 소비 과도 한 리소스 수 있는 컨테이너를 찾습니다.
  * 중앙화 된 CPU, 메모리, 저장소 및 컨테이너에 대 한 네트워크 사용량 및 성능 정보를 봅니다.  

  이 솔루션은 Docker Swarm, DC/OS, 관리 되지 않는 Kubernetes, Service Fabric 및 Red Hat OpenShift를 비롯 한 컨테이너 오 케 스트레이 터를 지원 합니다. 

### <a name="monitor-container-resource-activity"></a>컨테이너 리소스 작업 모니터링 

파일, 네트워크 및 컨테이너에 액세스 하는 다른 리소스와 같은 리소스 작업을 모니터링 합니다. 리소스 작업 및 소비를 모니터링 하는 것은 보안 조치로 및 성능 모니터링에 유용 합니다. 

[Azure Monitor](../azure-monitor/overview.md)를 사용하면 메트릭, 활동 로그 및 진단 로그를 수집하도록 허용하여 Azure 서비스에 대한 핵심 모니터링을 수행할 수 있습니다. 예를 들어, 활동 로그는 새 리소스가 만들어지거나 수정되는 시기를 알려줍니다. 

다양한 리소스와 가상 머신 내부의 운영 체제에 대한 성능 통계를 제공하는 메트릭을 사용할 수 있습니다. Azure Portal에서 탐색기 중 하나를 사용하여 이 데이터를 보고 이러한 메트릭을 기반으로 경고를 만들 수 있습니다. Azure Monitor는 시간이 중요 한 경고 및 알림에 대 한 사용 해야 하므로 가장 빠른 메트릭 파이프라인 (1 분까지 5 분)을 제공 합니다. 

### <a name="log-all-container-administrative-user-access-for-auditing"></a>감사에 대 한 모든 컨테이너 관리자 액세스 기록 

컨테이너 에코 시스템, 컨테이너 레지스트리 및 컨테이너 이미지에 대 한 관리 액세스를 정확 하 게 감사 내역을 유지 합니다. 이러한 로그 감사를 위해 해야 할 수도 있습니다 및 보안 문제는 후 법정 증거로 도움이 됩니다. 사용할 수는 [Azure 컨테이너 모니터링 솔루션](../azure-monitor/insights/containers.md) 이 목적을 달성 하기 위해. 

## <a name="next-steps"></a>다음 단계

* 솔루션을 사용 하 여 컨테이너 취약성 관리에 대해 자세히 알아보려면 [Twistlock](https://www.twistlock.com/solutions/microsoft-azure-container-security/) 하 고 [Aqua Security](https://www.aquasec.com/solutions/azure-container-security/)합니다.

* 에 대해 자세히 알아보세요 [Azure의 컨테이너 보안](https://azure.microsoft.com/resources/container-security-in-microsoft-azure/)합니다.