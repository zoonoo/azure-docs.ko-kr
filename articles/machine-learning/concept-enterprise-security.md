---
title: 엔터프라이즈 보안 및 거버넌스
titleSuffix: Azure Machine Learning
description: 'Azure Machine Learning: 인증, 권한 부여, 네트워크 보안, 데이터 암호화 및 모니터링을 안전하게 사용합니다.'
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 09/09/2020
ms.openlocfilehash: a9ad018980784a1f809ad28a77dacf9f0328fffa
ms.sourcegitcommit: 642988f1ac17cfd7a72ad38ce38ed7a5c2926b6c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2020
ms.locfileid: "94873899"
---
# <a name="enterprise-security-and-governance-for-azure-machine-learning"></a>Azure Machine Learning에 대 한 엔터프라이즈 보안 및 거 버 넌 스

이 문서에서는 Azure Machine Learning에 사용할 수 있는 보안 기능에 대해 알아봅니다.

클라우드 서비스를 사용하는 경우에는 필요한 사용자에게만 액세스를 제한하는 것이 가장 좋습니다. 먼저, 서비스에 사용되는 인증 및 권한 부여 모델을 파악합니다. 클라우드를 통해 온-프레미스 네트워크의 리소스에 안전하게 조인하거나 네트워크 액세스를 제한할 수도 있습니다. 데이터 암호화도 중요하며, 미사용 데이터 및 서비스간에 이동하는 데이터 모두에 중요합니다. 비규격 구성이 생성 될 때 특정 구성 또는 로그를 적용 하는 정책을 만들 수도 있습니다. 마지막으로 서비스를 모니터링하고 모든 활동에 대한 감사 로그를 생성할 수 있어야 합니다.

> [!NOTE]
> 이 문서의 정보는 Azure Machine Learning Python SDK 버전 1.0.83.1 이상에서 작동합니다.

## <a name="authentication--authorization"></a>인증 & 권한 부여

Azure Machine Learning 리소스에 대 한 대부분의 인증에서는 인증을 위해 Azure AD (Azure Active Directory)를 사용 하 고 권한 부여에 대 한 Azure RBAC (역할 기반 액세스 제어)를 사용 합니다. 이에 대 한 예외는 다음과 같습니다.

* __Ssh__: Azure Machine Learning 계산 인스턴스와 같은 일부 계산 리소스에 대 한 SSH 액세스를 사용 하도록 설정할 수 있습니다. SSH 액세스는 키 기반 인증을 사용 합니다. SSH 키를 만드는 방법에 대 한 자세한 내용은 [ssh 키 만들기 및 관리](../virtual-machines/linux/create-ssh-keys-detailed.md)를 참조 하세요. SSH 액세스를 사용 하도록 설정 하는 방법에 대 한 자세한 내용은 [Azure Machine Learning 계산 인스턴스 만들기 및 관리](how-to-create-manage-compute-instance.md)를 참조 하세요.
* __웹 서비스로 배포 된 모델__: 웹 서비스 배포는 __키__ 또는 __토큰__ 기반 액세스 제어를 사용할 수 있습니다. 키는 정적 문자열입니다. 토큰은 Azure AD 계정을 사용 하 여 검색 됩니다. 자세한 내용은 [웹 서비스로 배포 된 모델에 대 한 인증 구성](how-to-authenticate-web-service.md)을 참조 하세요.

Azure 데이터 저장소 서비스와 같이 Azure Machine Learning에 의존 하는 특정 서비스에는 자체 인증 및 권한 부여 방법이 있습니다. 저장소 서비스 인증에 대 한 자세한 내용은 [저장소 서비스에 연결](how-to-access-data.md)을 참조 하세요.

### <a name="azure-ad-authentication"></a>Azure AD 인증

다단계 인증은 Azure AD(Azure Active Directory)에서 사용하도록 구성된 경우 지원됩니다. 인증 프로세스는 다음과 같습니다.

1. 클라이언트가 Azure AD에 로그인하여 Azure Resource Manager 토큰을 얻습니다.  사용자 및 서비스 주체가 완전히 지원됩니다.
1. 클라이언트가 Azure Resource Manager와 모든 Azure Machine Learning에 토큰을 제공합니다.
1. Machine Learning Service가 사용자 컴퓨팅 대상(예: Machine Learning 컴퓨팅)에 Machine Learning Service 토큰을 제공합니다. 이 토큰은 실행이 완료된 후 사용자 컴퓨팅 대상이 Machine Learning Service로 콜백되는 데 사용됩니다. 범위는 작업 영역으로 제한됩니다.

[![Azure Machine Learning 인증](media/concept-enterprise-security/authentication.png)](media/concept-enterprise-security/authentication.png#lightbox)

자세한 내용은 [Azure Machine Learning 작업 영역에 대 한 인증](how-to-setup-authentication.md)을 참조 하세요.

### <a name="azure-rbac"></a>Azure RBAC

여러 작업 영역을 만들 수 있고 각 작업 영역을 여러 사용자와 공유할 수 있습니다. Azure AD 계정을 Azure RBAC 역할에 할당 하 여 작업 영역의 사용자가 액세스할 수 있는 기능 또는 작업을 제어할 수 있습니다. 기본 제공 역할은 다음과 같습니다.

* 소유자
* 참가자
* 판독기

소유자와 기여자는 작업 영역에 연결된 모든 컴퓨팅 대상과 데이터 저장소를 사용할 수 있습니다.  

다음 표에는 몇 가지 주요 Azure Machine Learning 작업과 이를 수행할 수 있는 역할이 나와 있습니다.

| Azure Machine Learning 작업 | 소유자 | 참가자 | 판독기 |
| ---- |:----:|:----:|:----:|
| 작업 영역 만들기 | ✓ | ✓ | |
| 작업 영역 공유 | ✓ | |  |
| 컴퓨팅 대상 만들기 | ✓ | ✓ | |
| 컴퓨팅 대상 연결 | ✓ | ✓ | |
| 데이터 저장소 연결 | ✓ | ✓ | |
| 실험 실행 | ✓ | ✓ | |
| 실행/메트릭 보기 | ✓ | ✓ | ✓ |
| 모델 등록 | ✓ | ✓ | |
| 이미지 만들기 | ✓ | ✓ | |
| 웹 서비스 배포 | ✓ | ✓ | |
| 모델/이미지 보기 | ✓ | ✓ | ✓ |
| 웹 서비스 호출 | ✓ | ✓ | ✓ |

기본 제공 역할이 요구에 맞지 않으면 사용자 지정 역할을 만들 수 있습니다. 사용자 지정 역할은 계산 만들기, 실행 제출, 데이터 저장소 등록 또는 모델 배포와 같은 작업 영역 내의 모든 작업을 제어 합니다. 사용자 지정 역할에는 클러스터, 데이터 저장소, 모델 및 끝점과 같은 작업 영역의 다양 한 리소스에 대 한 읽기, 쓰기 또는 삭제 권한이 있을 수 있습니다. 특정 작업 영역 수준, 특정 리소스 그룹 수준 또는 특정 구독 수준에서 역할을 사용하도록 설정할 수 있습니다. 자세한 내용은 [Azure Machine Learning 작업 영역에서 사용자 및 역할 관리](how-to-assign-roles.md)를 참조하세요.

Kubernetes에서 RBAC를 사용 하는 방법에 대 한 자세한 내용은 [Kubernetes 권한 부여에 대 한 Azure Role-Based Access Control](../aks/manage-azure-rbac.md)를 참조 하세요.

> [!IMPORTANT]
> Azure Machine Learning Azure Blob Storage 및 Azure Kubernetes 서비스와 같은 다른 Azure 서비스에 따라 달라 집니다. 각 Azure 서비스에는 자체 Azure RBAC 구성이 있습니다. 원하는 액세스 제어 수준을 얻으려면 Azure Machine Learning에 대 한 Azure RBAC 구성과 Azure Machine Learning에 사용 되는 서비스에 대 한 구성을 모두 적용 해야 할 수 있습니다.

> [!WARNING]
> Azure Machine Learning은 Azure Active Directory B2B(Business-to-Business) 협업에서 지원되지만 현재 Azure Active Directory B2C(Business-to-Consumer) 협업에서는 지원되지 않습니다.

### <a name="managed-identities"></a>관리 ID

각 작업 영역에는 작업 영역과 이름이 동일한 연결 된 시스템 할당 [관리 id](../active-directory/managed-identities-azure-resources/overview.md) 가 있습니다. 관리 id는 작업 영역에서 사용 하는 리소스에 안전 하 게 액세스 하는 데 사용 됩니다. 연결 된 리소스에 대 한 다음 권한이 있습니다.

| 리소스 | 사용 권한 |
| ----- | ----- |
| 작업 영역 | 참가자 |
| 스토리지 계정 | Storage Blob 데이터 기여자 |
| 주요 자격 증명 모음 | 모든 키, 비밀, 인증서에 대한 액세스 |
| Azure Container Registry | 참가자 |
| 작업 영역을 포함하는 리소스 그룹 | 참가자 |
| 키 자격 증명 모음이 포함된 리소스 그룹(작업 영역이 포함된 것과 다른 경우) | 참가자 |

이전 표에 언급된 리소스의 관리 ID에 대한 액세스를 관리자가 취소하지 않는 것이 좋습니다. 키 다시 동기화를 사용하여 액세스를 복원할 수 있습니다.

Azure Machine Learning은 모든 작업 영역 지역에 대한 구독에서 기여자 수준 액세스 권한으로 추가 애플리케이션(이름이 `aml-` 또는 `Microsoft-AzureML-Support-App-`으로 시작)을 만듭니다. 예를 들어, 미국 동부에 작업 영역이 하나 있고 동일한 구독의 북유럽에 하나가 있으면 해당 애플리케이션은 두 개가 보입니다. 이러한 애플리케이션을 통해 Azure Machine Learning에서 컴퓨팅 리소스를 관리할 수 있습니다.

필요에 따라 Azure Virtual Machines 및 Azure Machine Learning 계산 클러스터에서 사용할 관리 되는 id를 구성할 수 있습니다. VM을 사용 하면 관리 되는 id를 사용 하 여 개별 사용자의 Azure AD 계정 대신 SDK에서 작업 영역에 액세스할 수 있습니다. 계산 클러스터를 사용 하면 관리 되는 id를 사용 하 여 학습 작업을 실행 하는 사용자가 액세스할 수 없는 보안 된 데이터 저장소와 같은 리소스에 액세스 합니다. 자세한 내용은 [Azure Machine Learning 작업 영역에 대 한 인증](how-to-setup-authentication.md)을 참조 하세요.

## <a name="network-security-and-isolation"></a>네트워크 보안 및 격리

Azure Machine Learning 리소스에 대 한 물리적 액세스를 제한 하기 위해 Azure Virtual Network (VNet)를 사용할 수 있습니다. Vnet를 사용 하면 공용 인터넷에서 부분적으로 또는 완전히 격리 된 네트워크 환경을 만들 수 있습니다. 이렇게 하면 솔루션에 대 한 공격 노출 영역 뿐만 아니라 데이터 exfiltration의 기회가 줄어듭니다.

자세한 내용은 다음 문서를 참조하세요.

* [가상 네트워크 격리 및 개인 정보 개요](how-to-network-security-overview.md)
* [보안 작업 영역](how-to-secure-workspace-vnet.md)
* [보안 교육 환경](how-to-secure-training-vnet.md)
* [보안 유추 환경](how-to-secure-inferencing-vnet.md)
* [보안 가상 네트워크에서 studio 사용](how-to-enable-studio-virtual-network.md)

<a id="encryption-at-rest"></a><a id="azure-blob-storage"></a>

## <a name="data-encryption"></a>데이터 암호화.

Azure Machine Learning은 다양 한 계산 리소스 및 데이터 저장소를 사용 합니다. 이러한 각각은 미사용 및 전송 중에 데이터 암호화를 지 원하는 방법에 대해 자세히 알아보려면 [Azure Machine Learning로 데이터 암호화](concept-data-encryption.md)를 참조 하세요.

### <a name="microsoft-generated-data"></a>Microsoft에서 생성된 데이터

자동화된 Machine Learning과 같은 서비스를 사용하는 경우 Microsoft는 여러 모델을 학습시키기 위해 전처리된 일시적 데이터를 생성할 수 있습니다. 이런 데이터는 사용자 작업 영역의 데이터 저장소에 저장되므로 액세스 제어 및 암호화를 적절하게 적용할 수 있습니다.

[배포된 엔드포인트에서 기록된 진단 정보](how-to-enable-app-insights.md)를 Azure Application Insights 인스턴스로 암호화할 수도 있습니다.

## <a name="monitoring"></a>모니터링

역할 및 모니터링 대상에 따라 Azure Machine Learning를 사용 하는 몇 가지 모니터링 시나리오가 있습니다.

| 역할 | 사용할 모니터링 | Description |
| ---- | ----- | ----- |
| 관리자, DevOps, MLOps | [Azure Monitor 메트릭](#azure-monitor), [활동 로그](#activity-log), [취약성 검색](#vulnerability-scanning) | 서비스 수준 정보 |
| 데이터 과학자, MLOps | [실행 모니터링](#monitor-runs) | 학습 실행 중에 기록 되는 정보 |
| MLOps | [모델 데이터 수집](how-to-enable-data-collection.md), [Application Insights로 모니터링](how-to-enable-app-insights.md) | 웹 서비스로 배포 된 모델 또는 IoT Edge 모듈에 의해 기록 된 정보|

### <a name="monitor-runs"></a>실행 모니터링

학습 스크립트 내의 로깅 정보를 포함 하 여 Azure Machine Learning 실험 실행을 모니터링할 수 있습니다. 이 정보는 SDK, Azure CLI 및 studio를 통해 볼 수 있습니다. 자세한 내용은 다음 아티클을 참조하세요.

* [학습 실행 시작, 모니터링 및 취소](how-to-manage-runs.md)
* [로그 사용 설정](how-to-track-experiments.md)
* [로그 보기](how-to-monitor-view-training-logs.md)
* [TensorBoard로 시각화 실행](how-to-monitor-tensorboard.md)

### <a name="azure-monitor"></a>Azure Monitor

Azure Monitor 메트릭을 사용하여 Azure Machine Learning 작업 영역에 대한 메트릭을 보고 모니터링할 수 있습니다. [Azure Portal](https://portal.azure.com)에서 작업 영역을 선택한 다음, **메트릭** 을 선택합니다.

[![작업 영역에 대한 예제 메트릭을 보여주는 스크린샷](media/concept-enterprise-security/workspace-metrics.png)](media/concept-enterprise-security/workspace-metrics-expanded.png#lightbox)

메트릭에는 실행, 배포 및 등록에 대한 정보가 포함됩니다.

자세한 내용은 [Azure Monitor의 메트릭](../azure-monitor/platform/data-platform-metrics.md)을 참조하세요.

### <a name="activity-log"></a>활동 로그

작업 영역의 활동 로그를 보면 작업 영역에서 수행되는 다양한 작업을 확인할 수 있습니다. 로그에는 작업 이름, 이벤트 초기자 및 타임스탬프와 같은 기본 정보가 포함됩니다.

이 스크린샷은 작업 영역에 대한 활동 로그를 보여줍니다.

[![작업 영역에 대한 활동 로그를 보여주는 스크린샷](media/concept-enterprise-security/workspace-activity-log.png)](media/concept-enterprise-security/workspace-activity-log-expanded.png#lightbox)

채점 요청 세부 정보는 Application Insights에 저장됩니다. Application Insights는 작업 영역을 만들 때 구독에 생성됩니다. 기록된 정보에는 다음과 같은 필드가 포함됩니다.

* HTTPMethod
* UserAgent
* ComputeType
* RequestUrl
* StatusCode
* RequestId
* Duration

> [!IMPORTANT]
> Azure Machine Learning 작업 영역의 일부 작업은 활동 로그에 정보를 기록하지 않습니다. 예를 들어, 학습 실행 시작과 모델 등록은 기록되지 않습니다.
>
> 이러한 작업 중 일부는 작업 영역의 **작업** 영역에 표시되지만 이 알림은 작업을 시작한 사용자를 나타내지 않습니다.

### <a name="vulnerability-scanning"></a>취약성 검색

Azure Security Center는 하이브리드 클라우드 워크로드에 통합 보안 관리 및 고급 위협 방지를 제공합니다. Azure machine learning의 경우 Azure Container Registry 리소스 및 Azure Kubernetes Service 리소스에 대 한 검사를 사용 하도록 설정 해야 합니다. [Azure Container Registry 이미지 검색 Security Center](../security-center/defender-for-container-registries-introduction.md) 및 [Security Center와 Azure Kubernetes Services 통합](../security-center/defender-for-kubernetes-introduction.md)을 참조 하세요.

## <a name="audit-and-manage-compliance"></a>규정 준수 감사 및 관리

[Azure Policy](../governance/policy/index.yml) 은 Azure 리소스가 정책을 준수 하는지 확인할 수 있도록 하는 거 버 넌 스 도구입니다. Azure Machine Learning를 사용 하 여 다음 정책을 할당할 수 있습니다.

* **고객 관리 키**: 작업 영역에서 고객이 관리 하는 키를 사용 해야 하는지 여부를 감사 하거나 적용 합니다.
* **개인 링크**: 작업 영역에서 개인 끝점을 사용 하 여 가상 네트워크와 통신 하는지 여부를 감사 합니다.

Azure Policy에 대 한 자세한 내용은 [Azure Policy 설명서](../governance/policy/overview.md)를 참조 하세요.

Azure Machine Learning 관련 된 정책에 대 한 자세한 내용은 [Azure Policy 준수 감사 및 관리](how-to-integrate-azure-policy.md)를 참조 하세요.

## <a name="resource-locks"></a>리소스 잠금

[!INCLUDE [resource locks](../../includes/machine-learning-resource-lock.md)]

## <a name="next-steps"></a>다음 단계

* [TLS를 사용하여 Azure Machine Learning 웹 서비스 보호](how-to-secure-web-service.md)
* [웹 서비스로 배포된 Machine Learning 모델 사용](how-to-consume-web-service.md)
* [Azure Firewall과 함께 Azure Machine Learning 사용](how-to-access-azureml-behind-firewall.md)
* [Azure Virtual Network에서 Azure Machine Learning 사용](how-to-network-security-overview.md)
* [미사용 및 전송 중인 데이터 암호화](concept-data-encryption.md)
* [Azure에서 실시간 추천 API 빌드](/azure/architecture/reference-architectures/ai/real-time-recommendation)
