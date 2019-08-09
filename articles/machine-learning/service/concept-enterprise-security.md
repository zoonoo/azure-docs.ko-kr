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
ms.openlocfilehash: d1ad89943f6acfec6e42199ef399643be12e2b8b
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/08/2019
ms.locfileid: "68856233"
---
# <a name="enterprise-security-for-azure-machine-learning-service"></a>Azure Machine Learning 서비스에 대 한 엔터프라이즈 보안

이 문서에서는 Azure Machine learning 서비스에서 사용할 수 있는 보안 기능에 대해 알아봅니다.

클라우드 서비스를 사용 하는 경우이를 필요로 하는 사용자 에게만 액세스를 제한 하는 것이 좋습니다. 이렇게 하려면 먼저 서비스에서 사용 하는 인증 및 권한 부여 모델을 이해 해야 합니다. 네트워크 액세스를 제한 하거나 온-프레미스 네트워크의 리소스를 클라우드의 리소스와 안전 하 게 가입 시킬 수도 있습니다. 데이터 암호화 역시 휴지 상태이 고 데이터를 서비스 간에 이동 하는 동안 중요 합니다. 마지막으로 서비스를 모니터링 하 고 모든 작업의 감사 로그를 생성할 수 있어야 합니다.

## <a name="authentication"></a>인증

Multi-factor authentication은 Azure Active Directory (Azure AD)가 동일한에 대해 구성 된 경우에만 지원 됩니다.

* 클라이언트는 Azure AD에 로그인 하 고 Azure Resource Manager 토큰을 가져옵니다.  사용자 및 서비스 주체는 완전히 지원 됩니다.
* 클라이언트는 모든 Azure Machine Learning 서비스 & Azure Resource Manager 토큰을 제공 합니다.
* Azure Machine Learning 서비스는 사용자 계산에 대 한 Azure Machine Learning 토큰을 제공 합니다. 예를 들어 Machine Learning 컴퓨팅 합니다. 이 Azure Machine Learning 토큰은 실행이 완료 된 후 사용자 계산에서 Azure Machine Learning 서비스 (작업 영역으로 범위 제한)로 다시 호출 하는 데 사용 됩니다.

![Azure Machine Learning 서비스에서 인증이 작동 하는 방식을 보여 주는 스크린샷](./media/enterprise-readiness/authentication.png)

### <a name="authentication-for-web-service-deployment"></a>웹 서비스 배포에 대 한 인증

Azure Machine Learning는 웹 서비스, 키 및 토큰에 대 한 두 가지 형태의 인증을 지원 합니다. 각 웹 서비스는 한 번에 한 가지 형식의 인증만 사용할 수 있습니다.

|인증 방법|ACI|AKS|
|---|---|---|
|Key|기본적으로 사용 안 함| 기본적으로 사용|
|토큰| 사용할 수 없음| 기본적으로 사용 안 함 |

#### <a name="authentication-with-keys"></a>키를 사용 하 여 인증

배포에 대 한 키 인증을 사용 하도록 설정 하면 인증 키가 자동으로 생성 됩니다.

* Azure Kubernetes Service에 배포할 때 인증은 기본적으로 활성화되어 있습니다.
* Azure 컨테이너 인스턴스에 배포할 때 인증은 기본적으로 비활성화되어 있습니다.

키 인증을 사용 하려면 배포를 `auth_enabled` 만들거나 업데이트할 때 매개 변수를 사용 합니다.

키 인증을 사용 하는 경우 `get_keys` 메서드를 사용 하 여 기본 및 보조 인증 키를 검색할 수 있습니다.

```python
primary, secondary = service.get_keys()
print(primary)
```

> [!IMPORTANT]
> 키를 다시 생성 해야 하는 경우 다음을 사용 합니다.[`service.regen_key`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py)

#### <a name="authentication-with-tokens"></a>토큰을 사용한 인증

웹 서비스에 대 한 토큰 인증을 사용 하도록 설정 하는 경우 사용자는 웹 서비스에 대 한 Azure Machine Learning JSON Web Token를 제공 하 여 액세스 해야 합니다.

* 토큰 인증은 Azure Kubernetes Service에 배포할 때 기본적으로 사용 하지 않도록 설정 됩니다.
* Azure Container Instances에 배포 하는 경우에는 토큰 인증이 지원 되지 않습니다.

토큰 인증을 제어 하려면 배포를 `token_auth_enabled` 만들거나 업데이트할 때 매개 변수를 사용 합니다.

토큰 인증을 사용 하는 경우 `get_token` 메서드를 사용 하 여 JWT 토큰 및 해당 토큰의 만료 시간을 검색할 수 있습니다.

```python
token, refresh_by = service.get_token()
print(token)
```

> [!IMPORTANT]
> 토큰의 `refresh_by` 시간 이후에 새 토큰을 요청 해야 합니다.
>
> Azure Kubernetes Service 클러스터와 동일한 지역에 Azure Machine Learning 작업 영역을 만드는 것이 좋습니다. 토큰을 사용 하 여 인증 하기 위해 웹 서비스는 Azure Machine Learning 작업 영역이 생성 되는 영역에 대 한 호출을 수행 합니다. 작업 영역을 사용할 수 없는 경우 클러스터가 작업 영역과 다른 지역에 있는 경우에도 웹 서비스에 대 한 토큰을 가져올 수 없습니다. 이로 인해 작업 영역의 영역을 다시 사용할 수 있을 때까지 Azure AD 인증 사용할 수 없습니다. 또한 클러스터의 지역과 작업 영역 영역 간의 거리가 클수록 토큰을 인출 하는 데 시간이 오래 걸립니다.

## <a name="authorization"></a>Authorization

여러 작업 영역을 만들 수 있고 각 작업 영역을 여러 사용자와 공유할 수 있습니다. 작업 영역을 공유하는 경우 사용자에게 다음 역할을 할당하여 작업 영역에 대한 액세스를 제어할 수 있습니다.

* 소유자
* 참가자
* 독자

다음 표에서는 몇 가지 주요 Azure Machine Learning 서비스 작업 및 이러한 작업을 수행할 수 있는 역할을 보여 줍니다.

| Azure Machine Learning 서비스 작업 | 소유자 | 참가자 | 독자 |
| ---- |:----:|:----:|:----:|
| 작업 영역 만들기 | ✓ | ✓ | |
| 작업 영역 공유 | ✓ | |  |
| 계산 만들기 | ✓ | ✓ | |
| 계산 연결 | ✓ | ✓ | |
| Datastores 연결 | ✓ | ✓ | |
| 실험 실행 | ✓ | ✓ | |
| 실행/메트릭 보기 | ✓ | ✓ | ✓ |
| 모델 등록 | ✓ | ✓ | |
| 이미지 만들기 | ✓ | ✓ | |
| 웹 서비스 배포 | ✓ | ✓ | |
| 모델/이미지 보기 | ✓ | ✓ | ✓ |
| 웹 서비스 호출 | ✓ | ✓ | ✓ |

기본 제공 역할이 사용자 요구에 충분 하지 않은 경우 사용자 지정 역할을 만들 수도 있습니다. 작업 영역 및 Machine Learning 컴퓨팅에 대 한 작업을 지 원하는 사용자 지정 역할은 유일 하 게 지원 됩니다. 사용자 지정 역할에는 작업 영역에 대 한 읽기, 쓰기 또는 삭제 권한과 해당 작업 영역의 계산 리소스가 있을 수 있습니다. 특정 작업 영역 수준, 특정 리소스 그룹 수준 또는 특정 구독 수준에서 역할을 사용 하도록 설정할 수 있습니다. 자세한 내용은 [Azure Machine Learning 작업 영역에서 사용자 및 역할 관리](how-to-assign-roles.md) 를 참조 하세요.

### <a name="securing-compute-and-data"></a>계산 및 데이터 보안

소유자 및 참가자는 작업 영역에 연결 된 모든 계산 대상과 데이터 저장소를 사용할 수 있습니다.  
각 작업 영역에는 작업 영역에서 사용 되는 연결 된 리소스에 대 한 다음과 같은 사용 권한이 있는 시스템 할당 관리 Id (작업 영역과 동일한 이름)가 있습니다.

관리 id에 대 한 자세한 내용은 [Azure 리소스에 대 한 관리 되는 id](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) 를 참조 하세요.

| 리소스 | 사용 권한 |
| ----- | ----- |
| 작업 영역 | 기여자 |
| 스토리지 계정 | Storage Blob 데이터 Contributor |
| Key Vault | 모든 키, 암호, 인증서에 대 한 액세스 |
| Azure 컨테이너 레지스트리 | 기여자 |
| 작업 영역을 포함 하는 리소스 그룹 | 기여자 |
| Key Vault를 포함 하는 리소스 그룹 (작업 영역을 포함 하는 것과 다른 경우) | 기여자 |

관리자는 위에서 언급 한 리소스에 대 한 관리 되는 id의 액세스를 취소 하지 않는 것이 좋습니다. 키 다시 동기화 작업을 사용 하 여 액세스를 복원할 수 있습니다.

Azure Machine Learning 서비스는 모든 작업 영역 영역에 대 한 `aml-`구독에서 참가자 수준 액세스 권한이 있는 추가 응용 프로그램 (이름으로 시작)을 만듭니다. 예: 미국 동부에 작업 영역이 있고 동일한 구독의 북아메리카에 다른 작업 영역이 있는 경우 두 가지 응용 프로그램을 볼 수 있습니다. 이는 Azure Machine Learning 서비스에서 계산 리소스를 관리할 수 있도록 하는 데 필요 합니다.

## <a name="network-security"></a>네트워크 보안

Azure Machine Learning Service는 다른 Azure 서비스를 통해 컴퓨팅 리소스를 얻습니다. 컴퓨팅 리소스(컴퓨팅 대상)는 모델을 학습 및 배포하는 데 사용합니다. 이러한 컴퓨팅 대상을 가상 네트워크 내에서 만들 수 있습니다. 예를 들어 Microsoft Data Science Virtual Machine을 사용하여 모델을 학습시킨 다음, AKS(Azure Kubernetes Service)에 모델을 배포할 수 있습니다.  

자세한 내용은 [가상 네트워크에서 실험 및 유추를 실행 하는 방법](how-to-enable-virtual-network.md)을 참조 하세요.

## <a name="data-encryption"></a>데이터 암호화

### <a name="encryption-at-rest"></a>휴지 상태의 암호화

#### <a name="azure-blob-storage"></a>Azure Blob Storage

Azure Machine Learning 서비스는 Azure Machine Learning 서비스 작업 영역에 연결 되 고 사용자의 구독에 상주 하는 Azure Blob Storage 계정에 스냅숏, 출력 및 로그를 저장 합니다. Azure Blob Storage에 저장 된 모든 데이터는 Microsoft에서 관리 하는 키를 사용 하 여 미사용으로 암호화 됩니다.

Azure Blob Storage에 저장 된 데이터에 대 한 고유 키를 가져오는 방법에 대 한 자세한 내용은 [Azure Key Vault에서 고객이 관리 하는 키 사용 저장소 서비스 암호화](https://docs.microsoft.com/azure/storage/common/storage-service-encryption-customer-managed-keys)을 참조 하세요.

학습 데이터는 일반적으로 Azure Blob storage에 저장 되므로 학습 계산에 액세스할 수 있습니다. 이 저장소는 Azure Machine Learning에서 관리 되지 않지만 계산에 원격 파일 시스템으로 탑재 됩니다.

작업 영역에서 사용 되는 Azure storage 계정에 대 한 액세스 키를 다시 생성 하는 방법에 대 한 자세한 내용은 [저장소 액세스 키 다시 생성](how-to-change-storage-access-key.md) 문서를 참조 하세요.

#### <a name="cosmos-db"></a>Cosmos DB

Azure Machine Learning 서비스는 Azure Machine Learning 서비스에서 관리 하는 Microsoft 구독에 상주 하는 Cosmos DB에 메트릭과 메타 데이터를 저장 합니다. Cosmos DB에 저장 된 모든 데이터는 Microsoft 관리 키를 사용 하 여 미사용으로 암호화 됩니다.

#### <a name="azure-container-registry-acr"></a>ACR (Azure Container Registry)

레지스트리의 모든 컨테이너 이미지 (ACR)는 미사용 시 암호화 됩니다. Azure는 이미지를 저장 하기 전에 이미지를 자동으로 암호화 하 고 Azure Machine Learning 서비스에서 이미지를 끌어올 때 즉석에서 암호를 해독 합니다.

#### <a name="machine-learning-compute"></a>Machine Learning 컴퓨팅

각 계산 노드에 대 한 OS 디스크는 Azure Machine Learning service Storage 계정에서 Microsoft 관리 키를 사용 하 여 암호화 Azure Storage에 저장 됩니다. 이 계산은 임시 이며 대기 중인 실행이 없는 경우 일반적으로 클러스터를 축소 합니다. 기본 가상 머신은 프로 비전 해제 되 고 OS 디스크가 삭제 됩니다. OS 디스크에 대 한 Azure disk encryption은 지원 되지 않습니다.
또한 각 가상 컴퓨터에는 OS 작업을 위한 로컬 임시 디스크가 있습니다. 이 디스크를 선택적으로 사용 하 여 학습 데이터를 준비할 수도 있습니다. 이 디스크는 암호화 되지 않습니다.
Azure에서 미사용 암호화가 작동 하는 방식에 대 한 자세한 내용은 [Azure 미사용 데이터 암호화](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)를 참조 하세요.

### <a name="encryption-in-transit"></a>전송 중 암호화

다양 한 Azure Machine Learning 마이크로 서비스와 점수 매기기 끝점을 호출 하는 외부 통신 간의 내부 통신은 모두 SSL을 사용 하 여 지원 됩니다. 모든 Azure Storage 액세스도 보안 채널을 통해 사용 됩니다.
자세한 내용은 [SSL을 사용 하 여 웹 서비스 보안 Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/how-to-secure-web-service)을 참조 하세요.

### <a name="using-azure-key-vault"></a>Azure Key Vault 사용

작업 영역과 연결 된 Key Vault 인스턴스는 Azure Machine Learning 서비스에서 다양 한 종류의 자격 증명을 저장 하는 데 사용 됩니다.

* 연결 된 저장소 계정 연결 문자열
* Azure 컨테이너 리포지토리 인스턴스에 대 한 암호
* 데이터 저장소에 대 한 연결 문자열입니다.

HDI HDInsight 및 VM과 같은 대상 계산에 대 한 SSH 암호 및 키는 Microsoft 구독과 연결 된 별도의 Key Vault에 저장 됩니다. Azure Machine Learning 서비스는 사용자가 제공 하는 암호나 키를 저장 하지 않고, 사용자가 제공 하는 사용자가 직접 SSH 키를 생성, 권한 부여 및 저장 하 여 실험을 실행 하기 위해 VM/HDInsight에 연결 합니다.
각 작업 영역에는 Key Vault의 모든 키, 암호 및 인증서에 대 한 액세스 권한이 있는 연결 된 시스템 할당 관리 Id (작업 영역과 동일한 이름)가 있습니다.

## <a name="monitoring"></a>모니터링

### <a name="metrics"></a>metrics

Azure Monitor 메트릭은 Azure Machine Learning 서비스 작업 영역에 대 한 메트릭을 확인 하 고 모니터링 하는 데 사용할 수 있습니다. [Azure Portal](https://portal.azure.com)에서 작업 영역을 선택한 다음 __메트릭__ 링크를 사용 합니다.

![작업 영역에 대 한 예제 메트릭을 보여 주는 스크린샷](./media/enterprise-readiness/workspace-metrics.png)

메트릭은 실행, 배포 및 등록에 대 한 정보를 포함 합니다.

자세한 내용은 [Azure Monitor의 메트릭](/azure/azure-monitor/platform/data-platform-metrics)을 참조 하세요.

### <a name="activity-log"></a>활동 로그

작업 영역에서 활동 로그를 확인 하 여 작업 영역에서 수행 되는 다양 한 작업을 확인 하 고 작업 이름, 이벤트 시작 시간, 타임 스탬프 등의 기본 정보를 가져올 수 있습니다.

다음 스크린샷은 작업 영역에 대 한 활동 로그를 보여 줍니다.

![작업 영역에서 활동 로그를 보여 주는 스크린샷](./media/enterprise-readiness/workspace-activity-log.png)

점수 매기기 요청 세부 정보는 작업 영역을 만드는 동안 사용자의 구독에서 생성 되는 AppInsights에 저장 됩니다. 여기에는 HTTPMethod, UserAgent, Etype, RequestUrl, StatusCode, RequestId, Duration 등의 필드가 포함 됩니다.

## <a name="data-flow-diagram"></a>데이터 흐름 다이어그램

### <a name="create-workspace"></a>작업 영역 만들기

다음 다이어그램에서는 작업 영역 만들기 워크플로를 보여 줍니다.
사용자는 지원 되는 Azure Machine Learning 서비스 클라이언트 (CLI, Python SDK, Azure Portal) 중 하나에서 Azure AD에 로그인 하 고 적절 한 Azure Resource Manager 토큰을 요청 합니다. 그런 다음 사용자가 Azure Resource Manager를 호출 하 여 작업 영역을 만듭니다.  Azure Resource Manager은 Azure Machine Learning 서비스 리소스 공급자에 게 작업 영역을 프로 비전 하는 데 연결 합니다.  작업 영역을 만드는 동안 고객의 구독에서 추가 리소스가 생성 됩니다.

* KeyVault (암호 저장)
* Azure Storage 계정 (Blob & 파일 공유 포함)
* Azure Container Registry (유추/점수 매기기 및 실험을 위한 docker 이미지를 저장 하려면)
* Application Insights (원격 분석을 저장 하려면)

작업 영역에 연결 된 다른 계산 (Azure Kubernetes Service, VM 등)은 필요에 따라 고객이 프로 비전 할 수도 있습니다.

![작업 영역 만들기 워크플로를 보여 주는 스크린샷](./media/enterprise-readiness/create-workspace.png)

### <a name="save-source-code-training-scripts"></a>소스 코드 저장 (학습 스크립트)

다음 다이어그램에서는 코드 스냅숏 워크플로를 보여 줍니다.
Azure Machine Learning 서비스 작업 영역과 연결 된 디렉터리 (실험)는 소스 코드 (학습 스크립트)를 포함 합니다.  이러한 기능은 고객의 로컬 컴퓨터와 클라우드에 저장 됩니다 (고객의 구독에서 Azure Blob Storage). 이러한 코드 스냅숏은 기록 감사에 대 한 실행 또는 검사에 사용 됩니다.

![작업 영역 만들기 워크플로를 보여 주는 스크린샷](./media/enterprise-readiness/code-snapshot.png)

### <a name="training"></a>학습

다음 다이어그램에서는 학습 워크플로를 보여 줍니다.

* Azure Machine Learning 서비스는 위에 저장 된 코드 스냅숏의 스냅숏 ID를 사용 하 여 호출 됩니다.
* Azure Machine Learning 서비스는 Machine Learning 컴퓨팅/VM과 같은 계산 대상에서 나중에 사용 하 여 Azure Machine Learning 서비스에 다시 통신할 수 있도록 실행 ID (선택 사항) & Azure Machine Learning 서비스 토큰을 만듭니다.
* 관리 되는 계산 (예: Machine Learning 컴퓨팅) 또는 관리 되지 않는 계산 (예: VM)에서 학습 작업을 실행할 수 있습니다. 데이터 흐름은 아래의 두 시나리오에 대해 설명 합니다.
* (V m/HDInsight – Microsoft 구독의 Key Vault에서 SSH 자격 증명을 사용 하 여 액세스) Azure Machine Learning 서비스는 계산 대상에서 다음을 수행 하는 관리 코드를 실행 합니다.

   1. 환경을 준비 합니다. Docker는 VM 및 로컬 에서도 사용할 수 있는 옵션입니다. Docker 컨테이너에서 실험을 실행 하는 방법을 이해 하려면 Machine Learning 컴퓨팅 다음 단계를 참조 하세요.)
   1. 코드를 다운로드 합니다.
   1. 환경 변수 및 configs을 설정 합니다.
   1. 사용자 스크립트 (위에서 언급 한 코드 스냅숏)를 실행 합니다.

* (Machine Learning 컴퓨팅 – 작업 영역 관리 id를 사용 하 여 액세스 됨) Machine Learning 컴퓨팅는 관리 되는 계산 이므로 microsoft에서 관리 하며, 그 결과 microsoft 구독에서 실행 됩니다.

   1. 필요한 경우 원격 Docker 생성이 시작 됩니다.
   1. 사용자 Azure 파일 공유에 관리 코드를 기록 합니다.
   1. 이전 단계에서 설명한 대로 초기 명령, 즉 관리 코드를 사용 하 여 컨테이너를 시작 합니다.

#### <a name="querying-runs-and-metrics"></a>실행 및 메트릭 쿼리

이 단계는 학습 계산이 Cosmos DB에 저장 된 위치에서 Azure Machine Learning 서비스로 *실행 메트릭을* 다시 쓰는 흐름에 표시 됩니다. 클라이언트는 Cosmos DB에서 메트릭을 가져와서 클라이언트에 다시 반환 하는 Azure Machine Learning 서비스를 호출할 수 있습니다.

![작업 영역 만들기 워크플로를 보여 주는 스크린샷](./media/enterprise-readiness/training-and-metrics.png)

### <a name="creating-web-services"></a>웹 서비스 만들기

다음 다이어그램에서는 유추 워크플로를 보여 줍니다. 유추 또는 모델 점수 매기기는 배포 된 모델이 예측에 사용 되는 단계 이며 가장 일반적으로 프로덕션 데이터에 사용 됩니다.
아래 세부 정보를 참조 하세요.

* 사용자가 Azure ML SDK와 같은 클라이언트를 사용 하 여 모델 등록
* 사용자가 모델, 점수 파일 및 기타 모델 종속성을 사용 하 여 이미지를 만듭니다.
* Docker 이미지가 만들어지고 ACR에 저장 됩니다.
* 웹 서비스는 위에서 만든 이미지를 사용 하 여 계산 대상 (ACI/AKS)에 배포 됩니다.
* 점수 매기기 요청 세부 정보는 사용자의 구독에 있는 AppInsights에 저장 됩니다.
* Microsoft/Azure 구독에도 원격 분석이 푸시 됨

![작업 영역 만들기 워크플로를 보여 주는 스크린샷](./media/enterprise-readiness/inferencing.png)

## <a name="next-steps"></a>다음 단계

* [SSL을 사용하여 Azure Machine Learning 웹 서비스 보호](how-to-secure-web-service.md)
* [웹 서비스로 배포된 ML 모델 사용](how-to-consume-web-service.md)
* [일괄 처리 예측 실행 방법](how-to-run-batch-predictions.md)
* [Application Insights를 사용하여 Azure Machine Learning 모델 모니터링](how-to-enable-app-insights.md)
* [프로덕션 환경에서 모델용 데이터 수집](how-to-enable-data-collection.md)
* [Azure Machine Learning Service SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
* [Azure Virtual Networks에서 Azure Machine Learning Service 사용](how-to-enable-virtual-network.md)
* [추천 시스템 빌드 모범 사례](https://github.com/Microsoft/Recommenders)
* [Azure에서 실시간 추천 API 빌드](https://docs.microsoft.com/azure/architecture/reference-architectures/ai/real-time-recommendation)
