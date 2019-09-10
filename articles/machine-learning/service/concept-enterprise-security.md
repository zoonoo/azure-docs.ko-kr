---
title: 엔터프라이즈 보안
titleSuffix: Azure Machine Learning service
description: Azure Machine Learning 서비스 (인증, 권한 부여, 네트워크 보안, 데이터 암호화 및 모니터링)를 안전 하 게 사용 합니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 08/07/2019
ms.openlocfilehash: e1029ad34a05d342e5aed5bb30407dee7c914f3c
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/10/2019
ms.locfileid: "70873569"
---
# <a name="enterprise-security-for-the-azure-machine-learning-service"></a>Azure Machine Learning 서비스에 대 한 엔터프라이즈 보안

이 문서에서는 Azure Machine Learning 서비스에 사용할 수 있는 보안 기능에 대해 알아봅니다.

클라우드 서비스를 사용 하는 경우 필요한 사용자 에게만 액세스를 제한 하는 것이 가장 좋습니다. 먼저 서비스에서 사용 하는 인증 및 권한 부여 모델을 이해 합니다. 클라우드를 사용 하 여 온-프레미스 네트워크에서 네트워크 액세스를 제한 하거나 안전 하 게 리소스를 연결할 수도 있습니다. 데이터 암호화도 휴지 상태 이며 서비스 간에 데이터를 이동 하는 동안에도 중요 합니다. 마지막으로 서비스를 모니터링 하 고 모든 작업의 감사 로그를 생성할 수 있어야 합니다.

## <a name="authentication"></a>인증

Multi-factor authentication은 Azure AD (Azure Active Directory)를 사용 하도록 구성 된 경우에 지원 됩니다. 인증 프로세스는 다음과 같습니다.

1. 클라이언트는 Azure AD에 로그인 하 고 Azure Resource Manager 토큰을 가져옵니다.  사용자 및 서비스 주체는 완전히 지원 됩니다.
1. 클라이언트는 Azure Resource Manager 및 모든 Azure Machine Learning 서비스에 대 한 토큰을 제공 합니다.
1. Machine Learning 서비스는 사용자 계산 대상에 Machine Learning 서비스 토큰을 제공 합니다 (예: Machine Learning 컴퓨팅). 이 토큰은 실행이 완료 된 후 사용자 계산 대상에서 Machine Learning 서비스로 다시 호출 하는 데 사용 됩니다. 범위는 작업 영역으로 제한 됩니다.

[![Azure Machine Learning 서비스의 인증](./media/enterprise-readiness/authentication.png)](./media/enterprise-readiness/authentication-expanded.png)

### <a name="authentication-for-web-service-deployment"></a>웹 서비스 배포에 대 한 인증

Azure Machine Learning는 웹 서비스에 대 한 두 가지 형태의 인증 인 키와 토큰을 지원 합니다. 각 웹 서비스는 한 번에 한 가지 형태의 인증을 사용 하도록 설정할 수 있습니다.

|인증 방법|Azure Container Instances|AKS|
|---|---|---|
|Key|기본적으로 사용 안 함| 기본적으로 사용|
|토큰| 사용할 수 없음| 기본적으로 사용 안 함 |

#### <a name="authentication-with-keys"></a>키를 사용 하 여 인증

배포에 대 한 키 인증을 사용 하도록 설정 하면 인증 키가 자동으로 생성 됩니다.

* Azure Kubernetes Service (AKS)에 배포 하는 경우 기본적으로 인증이 사용 됩니다.
* Azure Container Instances에 배포 하는 경우 인증은 기본적으로 사용 되지 않습니다.

키 인증을 사용 하려면 배포를 `auth_enabled` 만들거나 업데이트할 때 매개 변수를 사용 합니다.

키 인증을 사용 하는 경우 `get_keys` 메서드를 사용 하 여 기본 및 보조 인증 키를 검색할 수 있습니다.

```python
primary, secondary = service.get_keys()
print(primary)
```

> [!IMPORTANT]
> 키를 다시 생성해야 하는 경우 [`service.regen_key`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py)를 사용합니다.

#### <a name="authentication-with-tokens"></a>토큰을 사용한 인증

웹 서비스에 대 한 토큰 인증을 사용 하도록 설정 하는 경우 사용자는 웹 서비스에 액세스 하는 Azure Machine Learning JSON Web Token를 제공 해야 합니다.

* 토큰 인증은 Azure Kubernetes Service에 배포할 때 기본적으로 사용 하지 않도록 설정 됩니다.
* Azure Container Instances에 배포 하는 경우에는 토큰 인증이 지원 되지 않습니다.

토큰 인증을 제어 하려면 배포를 `token_auth_enabled` 만들거나 업데이트할 때 매개 변수를 사용 합니다.

토큰 인증을 사용 하는 경우 `get_token` 메서드를 사용 하 여 JWT (JSON Web Token)와 해당 토큰의 만료 시간을 검색할 수 있습니다.

```python
token, refresh_by = service.get_token()
print(token)
```

> [!IMPORTANT]
> 토큰의 `refresh_by` 시간 이후에 새 토큰을 요청 해야 합니다.
>
> Azure Kubernetes Service 클러스터와 동일한 지역에 Azure Machine Learning 작업 영역을 만드는 것이 좋습니다. 
>
> 토큰을 사용 하 여 인증 하기 위해 웹 서비스는 Azure Machine Learning 작업 영역이 생성 되는 영역에 대 한 호출을 수행 합니다. 작업 영역을 사용할 수 없는 경우에는 클러스터가 작업 영역에서 다른 지역에 있는 경우에도 웹 서비스에 대 한 토큰을 가져올 수 없습니다. 결과적으로 작업 영역을 다시 사용할 수 있을 때까지 Azure AD 인증를 사용할 수 없습니다. 
>
> 또한 클러스터 지역과 작업 영역 영역 간의 거리가 클수록 토큰을 인출 하는 데 시간이 오래 걸립니다.

## <a name="authorization"></a>Authorization

여러 작업 영역을 만들 수 있고 각 작업 영역을 여러 사용자와 공유할 수 있습니다. 작업 영역을 공유 하는 경우 사용자에 게 다음 역할을 할당 하 여 해당 작업 영역에 대 한 액세스를 제어할 수 있습니다.

* 소유자
* 참가자
* 독자

다음 표에서는 몇 가지 주요 Azure Machine Learning 서비스 작업 및 이러한 작업을 수행할 수 있는 역할을 보여 줍니다.

| Azure Machine Learning 서비스 작업 | 소유자 | 참가자 | 독자 |
| ---- |:----:|:----:|:----:|
| 작업 영역 만들기 | ✓ | ✓ | |
| 작업 영역 공유 | ✓ | |  |
| 계산 대상 만들기 | ✓ | ✓ | |
| 계산 대상 연결 | ✓ | ✓ | |
| 데이터 저장소 연결 | ✓ | ✓ | |
| 실험 실행 | ✓ | ✓ | |
| 실행/메트릭 보기 | ✓ | ✓ | ✓ |
| 모델 등록 | ✓ | ✓ | |
| 이미지 만들기 | ✓ | ✓ | |
| 웹 서비스 배포 | ✓ | ✓ | |
| 모델/이미지 보기 | ✓ | ✓ | ✓ |
| 웹 서비스 호출 | ✓ | ✓ | ✓ |

기본 제공 역할이 요구 사항에 맞지 않는 경우 사용자 지정 역할을 만들 수 있습니다. 사용자 지정 역할은 작업 영역 및 Machine Learning 컴퓨팅에 대 한 작업에 대해서만 지원 됩니다. 사용자 지정 역할은 작업 영역 및 해당 작업 영역의 계산 리소스에 대 한 읽기, 쓰기 또는 삭제 권한을 가질 수 있습니다. 특정 작업 영역 수준, 특정 리소스 그룹 수준 또는 특정 구독 수준에서 역할을 사용 하도록 설정할 수 있습니다. 자세한 내용은 [Azure Machine Learning 작업 영역에서 사용자 및 역할 관리](how-to-assign-roles.md)를 참조 하세요.

### <a name="securing-compute-targets-and-data"></a>계산 대상 및 데이터 보안

소유자 및 참가자는 작업 영역에 연결 된 모든 계산 대상과 데이터 저장소를 사용할 수 있습니다.  

각 작업 영역에는 작업 영역과 이름이 동일한 연결 된 시스템 할당 관리 id가 있습니다. 관리 id에는 작업 영역에서 사용 되는 연결 된 리소스에 대 한 다음과 같은 사용 권한이 있습니다.

관리 id에 대 한 자세한 내용은 [Azure 리소스에 대 한 관리 되는 id](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)를 참조 하세요.

| 리소스 | 사용 권한 |
| ----- | ----- |
| 작업 영역 | 기여자 |
| Storage 계정 | Storage Blob 데이터 Contributor |
| 키 자격 증명 모음 | 모든 키, 암호, 인증서에 대 한 액세스 |
| Azure 컨테이너 레지스트리 | 기여자 |
| 작업 영역을 포함 하는 리소스 그룹 | 기여자 |
| 키 자격 증명 모음을 포함 하는 리소스 그룹 (작업 영역을 포함 하는 자격 증명 모음과 다른 경우) | 기여자 |

관리자가 이전 표에 언급 된 리소스에 대 한 관리 되는 id의 액세스를 취소 하는 것은 좋지 않습니다. 키 다시 동기화 작업을 사용 하 여 액세스를 복원할 수 있습니다.

Azure Machine Learning 서비스는 모든 작업 영역 영역에 대 한 구독에서 `aml-` 참가자 `Microsoft-AzureML-Support-App-`수준 액세스를 사용 하 여 추가 응용 프로그램 (또는로 시작 하는 이름)을 만듭니다. 예를 들어 미국 동부에 한 작업 영역이 있고 동일한 구독의 북아메리카에 다른 작업 영역이 있는 경우 이러한 응용 프로그램 중 두 개를 볼 수 있습니다. 이러한 응용 프로그램을 통해 Azure Machine Learning 서비스를 통해 계산 리소스를 관리할 수 있습니다.

## <a name="network-security"></a>네트워크 보안

Azure Machine Learning Service는 다른 Azure 서비스를 통해 컴퓨팅 리소스를 얻습니다. 컴퓨팅 리소스(컴퓨팅 대상)는 모델을 학습 및 배포하는 데 사용합니다. 가상 네트워크에서 이러한 계산 대상을 만들 수 있습니다. 예를 들어 Azure Data Science Virtual Machine를 사용 하 여 모델을 학습 한 다음 모델을 AKS에 배포할 수 있습니다.  

자세한 내용은 [가상 네트워크에서 실험 및 유추를 실행 하는 방법](how-to-enable-virtual-network.md)을 참조 하세요.

## <a name="data-encryption"></a>데이터 암호화

### <a name="encryption-at-rest"></a>휴지 상태의 암호화

#### <a name="azure-blob-storage"></a>Azure Blob 스토리지

Azure Machine Learning 서비스는 Azure Machine Learning 서비스 작업 영역 및 구독과 연결 된 Azure Blob storage 계정에 스냅숏, 출력 및 로그를 저장 합니다. Azure Blob storage에 저장 된 모든 데이터는 Microsoft에서 관리 하는 키를 사용 하 여 미사용에 암호화 됩니다.

Azure Blob storage에 저장 된 데이터에 대 한 고유 키를 사용 하는 방법에 대 한 자세한 내용은 [Azure Key Vault에서 고객이 관리 하는 키를 사용 하 여 암호화 Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-service-encryption-customer-managed-keys)를 참조 하세요.

일반적으로 학습 데이터는 Azure Blob storage에 저장 되므로 계산 대상 학습에 액세스할 수 있습니다. 이 저장소는 Azure Machine Learning에서 관리 되지 않고 원격 파일 시스템으로 계산 대상에 탑재 됩니다.

작업 영역에서 사용 되는 Azure storage 계정에 대 한 액세스 키를 다시 생성 하는 방법에 대 한 자세한 내용은 [저장소 액세스 키 다시 생성](how-to-change-storage-access-key.md)을 참조 하세요.

#### <a name="azure-cosmos-db"></a>Azure Cosmos DB

Azure Machine Learning 서비스는 Azure Machine Learning 서비스에서 관리 하는 Microsoft 구독과 연결 된 Azure Cosmos DB 인스턴스에 메트릭과 메타 데이터를 저장 합니다. Azure Cosmos DB에 저장 된 모든 데이터는 Microsoft에서 관리 하는 키를 사용 하 여 미사용으로 암호화 됩니다.

#### <a name="azure-container-registry"></a>Azure 컨테이너 레지스트리

레지스트리의 모든 컨테이너 이미지 (Azure Container Registry)는 미사용 시 암호화 됩니다. Azure는 이미지를 저장 하기 전에 이미지를 자동으로 암호화 하 고 Azure Machine Learning 서비스가 이미지를 끌어올 때 즉석에서 암호를 해독 합니다.

#### <a name="machine-learning-compute"></a>Machine Learning 컴퓨팅

Azure Storage에 저장 된 각 계산 노드에 대 한 OS 디스크는 Azure Machine Learning service Storage 계정에서 Microsoft 관리 키를 사용 하 여 암호화 됩니다. 이 계산 대상은 임시 이며, 대기 중인 실행이 없는 경우 일반적으로 클러스터는 축소 됩니다. 기본 가상 머신이 프로 비전 해제 되 고 OS 디스크가 삭제 됩니다. Azure Disk Encryption은 OS 디스크에 대해 지원 되지 않습니다.

또한 각 가상 컴퓨터에는 OS 작업을 위한 로컬 임시 디스크가 있습니다. 원할 경우 디스크를 사용 하 여 학습 데이터를 준비할 수 있습니다. 디스크가 암호화 되지 않았습니다.
Azure에서 미사용 암호화가 작동 하는 방식에 대 한 자세한 내용은 [미사용 azure 데이터 암호화](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)를 참조 하세요.

### <a name="encryption-in-transit"></a>전송 중 암호화

SSL을 사용 하 여 Azure Machine Learning 마이크로 서비스 간의 내부 통신을 보호 하 고 점수 매기기 끝점에 대 한 외부 호출의 보안을 유지할 수 있습니다. 모든 Azure Storage 액세스는 보안 채널을 통해 수행 됩니다.

자세한 내용은 [Azure Machine Learning를 통해 SSL을 사용 하 여 웹 서비스 보호](https://docs.microsoft.com/azure/machine-learning/service/how-to-secure-web-service)를 참조 하세요.

### <a name="using-azure-key-vault"></a>Azure Key Vault 사용

Azure Machine Learning 서비스는 작업 영역과 연결 된 Azure Key Vault 인스턴스를 사용 하 여 다양 한 종류의 자격 증명을 저장 합니다.

* 연결 된 저장소 계정 연결 문자열
* Azure 컨테이너 리포지토리 인스턴스에 대 한 암호
* 데이터 저장소에 대 한 연결 문자열

Azure HDInsight 및 Vm과 같은 대상을 계산 하기 위한 SSH 암호 및 키는 Microsoft 구독과 연결 된 별도의 key vault에 저장 됩니다. Azure Machine Learning 서비스는 사용자가 제공한 암호나 키를 저장 하지 않습니다. 대신 Vm 및 HDInsight에 연결 하 여 실험을 실행 하기 위해 자체 SSH 키를 생성, 권한 부여 및 저장 합니다.

각 작업 영역에는 작업 영역과 동일한 이름을 가진 시스템 할당 관리 id가 연결 되어 있습니다. 이 관리 되는 id는 key vault의 모든 키, 암호 및 인증서에 액세스할 수 있습니다.

## <a name="monitoring"></a>모니터링

### <a name="metrics"></a>metrics

Azure Monitor 메트릭을 사용 하 여 Azure Machine Learning 서비스 작업 영역에 대 한 메트릭을 보고 모니터링할 수 있습니다. [Azure Portal](https://portal.azure.com)에서 작업 영역을 선택 하 고 **메트릭**을 선택 합니다.

[![작업 영역에 대 한 예제 메트릭을 보여 주는 스크린샷](./media/enterprise-readiness/workspace-metrics.png)](./media/enterprise-readiness/workspace-metrics-expanded.png)

메트릭은 실행, 배포 및 등록에 대 한 정보를 포함 합니다.

자세한 내용은 [Azure Monitor의 메트릭](/azure/azure-monitor/platform/data-platform-metrics)을 참조 하세요.

### <a name="activity-log"></a>활동 로그

작업 영역에 대 한 작업 로그를 확인 하 여 작업 영역에서 수행 되는 다양 한 작업을 볼 수 있습니다. 로그에는 작업 이름, 이벤트 시작자, 타임 스탬프 등의 기본 정보가 포함 됩니다.

이 스크린샷은 작업 영역에 대 한 활동 로그를 보여 줍니다.

[![작업 영역의 활동 로그를 보여 주는 스크린샷](./media/enterprise-readiness/workspace-activity-log.png)](./media/enterprise-readiness/workspace-activity-log-expanded.png)

점수 매기기 요청 세부 정보는 Application Insights에 저장 됩니다. 작업 영역을 만들 때 구독에 Application Insights 만들어집니다. 기록 되는 정보에는 HTTPMethod, UserAgent,, RequestUrl, StatusCode, RequestId 및 Duration과 같은 필드가 포함 됩니다.

> [!IMPORTANT]
> Azure Machine Learning 작업 영역의 일부 작업은 작업 로그에 정보를 기록 하지 않습니다. 예를 들어 학습 실행의 시작과 모델 등록은 기록 되지 않습니다.
>
> 이러한 작업 중 일부는 작업 영역의 **작업** 영역에 표시 되지만 이러한 알림은 작업을 시작한 사용자를 나타내지 않습니다.

## <a name="data-flow-diagrams"></a>데이터 흐름 다이어그램

### <a name="create-workspace"></a>작업 영역 만들기

다음 다이어그램에서는 작업 영역 만들기 워크플로를 보여 줍니다.

* 사용자는 지원 되는 Azure Machine Learning 서비스 클라이언트 (Azure CLI, Python SDK, Azure Portal) 중 하나에서 Azure AD에 로그인 하 고 적절 한 Azure Resource Manager 토큰을 요청 합니다.
* 사용자는 Azure Resource Manager를 호출 하 여 작업 영역을 만듭니다. 
* Azure Resource Manager은 Azure Machine Learning 서비스 리소스 공급자에 게 작업 영역을 프로 비전 하는 데 연결 합니다.

작업 영역을 만드는 동안 사용자의 구독에서 추가 리소스가 생성 됩니다.

* Key Vault (암호를 저장 하려면)
* Azure 저장소 계정 (blob 및 파일 공유 포함)
* Azure Container Registry (유추/점수 매기기 및 실험을 위한 Docker 이미지를 저장 하려면)
* Application Insights (원격 분석을 저장 하려면)

사용자는 필요에 따라 작업 영역 (예: Azure Kubernetes Service 또는 Vm)에 연결 된 다른 계산 대상을 프로 비전 할 수도 있습니다.

[![작업 영역 워크플로 만들기](./media/enterprise-readiness/create-workspace.png)](./media/enterprise-readiness/create-workspace-expanded.png)

### <a name="save-source-code-training-scripts"></a>소스 코드 저장 (학습 스크립트)

다음 다이어그램에서는 코드 스냅숏 워크플로를 보여 줍니다.

Azure Machine Learning 서비스 작업 영역과 연결 된 디렉터리 (실험)는 소스 코드 (학습 스크립트)를 포함 합니다. 이러한 스크립트는 로컬 컴퓨터와 클라우드에 저장 됩니다 (구독의 Azure Blob storage에 저장 됨). 코드 스냅숏은 기록 감사에 대 한 실행 또는 검사에 사용 됩니다.

[![코드 스냅숏 워크플로](./media/enterprise-readiness/code-snapshot.png)](./media/enterprise-readiness/code-snapshot-expanded.png)

### <a name="training"></a>학습

다음 다이어그램에서는 학습 워크플로를 보여 줍니다.

* Azure Machine Learning 서비스는 이전 섹션에서 저장 한 코드 스냅숏의 스냅숏 ID를 사용 하 여 호출 됩니다.
* Azure Machine Learning 서비스는 실행 ID (선택 사항) 및 Machine Learning 서비스 토큰을 만듭니다 .이 토큰은 나중에 Machine Learning 컴퓨팅/Vm과 같은 계산 대상에서 Machine Learning 서비스와 통신 하는 데 사용 됩니다.
* 관리 되는 계산 대상 (예: Machine Learning 컴퓨팅) 또는 관리 되지 않는 계산 대상 (예: Vm) 중 하나를 선택 하 여 학습 작업을 실행할 수 있습니다. 두 시나리오에 대 한 데이터 흐름은 다음과 같습니다.
   * Vm/HDInsight, Microsoft 구독의 key vault에서 SSH 자격 증명을 통해 액세스 합니다. Azure Machine Learning 서비스는 계산 대상에서 다음을 수행 하는 관리 코드를 실행 합니다.

   1. 환경을 준비 합니다. Docker는 Vm 및 로컬 컴퓨터에 대 한 옵션입니다. Docker 컨테이너에서 실행 되는 실험을 이해 하려면 Machine Learning 컴퓨팅 다음 단계를 참조 하세요.)
   1. 코드를 다운로드 합니다.
   1. 환경 변수 및 구성을 설정 합니다.
   1. 사용자 스크립트 (이전 섹션에 언급 된 코드 스냅숏)를 실행 합니다.

   * Machine Learning 컴퓨팅 작업 영역 관리 id를 통해 액세스 됩니다.
Machine Learning 컴퓨팅는 관리 되는 계산 대상 이므로 microsoft에서 관리 하므로 Microsoft 구독에서 실행 됩니다.

   1. 필요한 경우 원격 Docker 생성이 시작 됩니다.
   1. 관리 코드는 사용자의 Azure Files 공유에 기록 됩니다.
   1. 컨테이너는 초기 명령으로 시작 됩니다. 즉, 이전 단계에서 설명한 관리 코드입니다.

#### <a name="querying-runs-and-metrics"></a>실행 및 메트릭 쿼리

아래 흐름 다이어그램에서이 단계는 학습 계산 대상이 Cosmos DB 데이터베이스의 저장소에서 실행 메트릭을 Azure Machine Learning 서비스에 다시 쓸 때 발생 합니다. 클라이언트는 Azure Machine Learning 서비스를 호출할 수 있습니다. Machine Learning Cosmos DB 데이터베이스에서 메트릭을 가져와서 클라이언트에 다시 반환 합니다.

[![학습 워크플로](./media/enterprise-readiness/training-and-metrics.png)](./media/enterprise-readiness/training-and-metrics-expanded.png)

### <a name="creating-web-services"></a>웹 서비스 만들기

다음 다이어그램에서는 유추 워크플로를 보여 줍니다. 유추 또는 모델 점수 매기기는 배포 된 모델이 예측에 사용 되는 단계 이며 가장 일반적으로 프로덕션 데이터에 사용 됩니다.

세부 정보는 다음과 같습니다.

* 사용자는 Azure Machine Learning SDK와 같은 클라이언트를 사용 하 여 모델을 등록 합니다.
* 사용자는 모델, 점수 파일 및 기타 모델 종속성을 사용 하 여 이미지를 만듭니다.
* Docker 이미지는 Azure Container Registry에 생성 되 고 저장 됩니다.
* 웹 서비스는 이전 단계에서 만든 이미지를 사용 하 여 계산 대상 (Container Instances/AKS)에 배포 됩니다.
* 점수 매기기 요청 정보는 사용자의 구독에 있는 Application Insights에 저장 됩니다.
* 원격 분석도 Microsoft/Azure 구독에 푸시됩니다.

[![유추 워크플로](./media/enterprise-readiness/inferencing.png)](./media/enterprise-readiness/inferencing-expanded.png)

## <a name="next-steps"></a>다음 단계

* [SSL을 사용하여 Azure Machine Learning 웹 서비스 보호](how-to-secure-web-service.md)
* [웹 서비스로 배포 된 Machine Learning 모델 사용](how-to-consume-web-service.md)
* [일괄 처리 예측 실행 방법](how-to-run-batch-predictions.md)
* [Application Insights를 사용하여 Azure Machine Learning 모델 모니터링](how-to-enable-app-insights.md)
* [프로덕션 환경에서 모델용 데이터 수집](how-to-enable-data-collection.md)
* [Azure Machine Learning Service SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
* [Azure Virtual Network에서 Azure Machine Learning 서비스 사용](how-to-enable-virtual-network.md)
* [추천 시스템 빌드 모범 사례](https://github.com/Microsoft/Recommenders)
* [Azure에서 실시간 추천 API 빌드](https://docs.microsoft.com/azure/architecture/reference-architectures/ai/real-time-recommendation)
