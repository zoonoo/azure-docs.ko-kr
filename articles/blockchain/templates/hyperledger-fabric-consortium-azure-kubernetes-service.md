---
title: Azure Kubernetes 서비스 (AKS)의 hyperledger 패브릭 컨소시엄
description: Azure Kubernetes Service에서 Hyperledger Fabric consortium 네트워크를 배포 하 고 구성 하는 방법
ms.date: 07/27/2020
ms.topic: how-to
ms.reviewer: ravastra
ms.openlocfilehash: 4bc55090234a4ab33125ba43b8416de1eadb702f
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/03/2020
ms.locfileid: "87533430"
---
# <a name="hyperledger-fabric-consortium-on-azure-kubernetes-service-aks"></a>Azure Kubernetes 서비스 (AKS)의 hyperledger 패브릭 컨소시엄

AKS (Azure Kubernetes Service) 템플릿에서 HLF (Hyperledger Fabric)를 사용 하 여 Azure에서 하이퍼 원장 Fabric consortium 네트워크를 배포 하 고 구성할 수 있습니다.

이 문서를 읽고 나면:

- 하이퍼 원장 패브릭 및 하이퍼 원장 패브릭 blockchain 네트워크의 빌딩 블록을 구성 하는 다양 한 구성 요소에 대 한 실무 지식을 얻습니다.
- 프로덕션 시나리오를 위해 Azure Kubernetes Service에서 하이퍼 원장 Fabric 컨소시엄을 배포 하 고 구성 하는 방법에 대해 알아봅니다.

[!INCLUDE [Preview note](./includes/preview.md)]

## <a name="choose-an-azure-blockchain-solution"></a>Azure 블록 체인 솔루션 선택

솔루션 템플릿을 사용 하도록 선택 하기 전에 시나리오를 사용 가능한 Azure Blockchain 옵션의 일반적인 사용 사례와 비교 합니다.

옵션 | 서비스 모델 | 일반적인 사용 사례
-------|---------------|-----------------
솔루션 템플릿 | IaaS | 솔루션 템플릿은 완전히 구성 된 blockchain 네트워크 토폴로지를 프로 비전 하는 데 사용할 수 있는 Azure Resource Manager 템플릿입니다. 템플릿은 지정 된 blockchain 네트워크 유형에 대해 Microsoft Azure 계산, 네트워킹 및 저장소 서비스를 배포 하 고 구성 합니다. 솔루션 템플릿은 서비스 수준 계약 없이 제공 됩니다. 지원을 받으려면 [Microsoft Q&A 질문 페이지](/answers/topics/azure-blockchain-workbench.html)를 사용하세요.
[Azure Blockchain 서비스](../service/overview.md) | PaaS | Azure Blockchain 서비스 미리 보기는 컨소시엄 Blockchain 네트워크의 대형, 관리 및 관리를 간소화 합니다. PaaS, consortium 관리 또는 계약 및 트랜잭션 개인 정보를 요구 하는 솔루션에는 Azure Blockchain 서비스를 사용 합니다.
[Azure Blockchain Workbench](../workbench/overview.md) | IaaS 및 PaaS | Azure Blockchain Workbench 미리 보기는 비즈니스 프로세스 및 데이터를 다른 조직과 공유하기 위해 블록체인 애플리케이션을 만들고 배포할 수 있도록 설계된 Azure 서비스 및 기능 컬렉션입니다. 블록 체인 솔루션 프로토타입 또는 블록 체인 응용 프로그램 개념 증명을 위해 Azure Blockchain 워크 벤치를 사용 합니다. Azure Blockchain Workbench는 서비스 수준 규약 없이 제공됩니다. 지원을 받으려면 [Microsoft Q&A 질문 페이지](/answers/topics/azure-blockchain-workbench.html)를 사용하세요.

## <a name="hyperledger-fabric-consortium-architecture"></a>Hyperledger 패브릭 컨소시엄 아키텍처

Azure에서 Hyperledger 패브릭 네트워크를 빌드하려면 피어 노드를 사용 하 여 주문 서비스 및 조직을 배포 해야 합니다. Azure Kubernetes Service 솔루션 템플릿에서 Hyperledger 패브릭을 사용 하 여 주문 노드나 피어 노드를 만들 수 있습니다. 만들려는 각 노드에 대 한 템플릿을 배포 해야 합니다.

템플릿 배포의 일부로 생성 되는 다양 한 기본 구성 요소는 다음과 같습니다.

- **Orderer 노드**: 원장에서 트랜잭션 순서 지정을 담당 하는 노드입니다. 다른 노드와 함께, 정렬 된 노드는 Hyperledger 패브릭 네트워크의 주문 서비스를 형성 합니다.

- **피어 노드**: 주로 네트워크의 기본 요소인 원장 및 스마트 계약을 호스트 하는 노드입니다.

- **패브릭 ca**: 패브릭 Ca는 Hyperledger 패브릭에 대 한 Ca (인증 기관)입니다. 패브릭 CA를 사용 하 여 인증 기관을 호스트 하는 서버 프로세스를 초기화 하 고 시작할 수 있습니다. Id 및 인증서를 관리할 수 있습니다. 템플릿의 일부로 배포 된 각 AKS 클러스터에는 기본적으로 패브릭 CA pod가 있습니다.

- **쿠 chdb 또는 leveldb**: 피어 노드에 대 한 세계 상태 데이터베이스를 leveldb 또는 LevelDB는 피어 노드에 포함 된 기본 상태 데이터베이스 이며, chaincode 데이터를 단순 키-값 쌍으로 저장 하 고 키, 키 범위 및 복합 키 쿼리만 지원 합니다. Chaincode 데이터 값이 JSON으로 모델링 될 때 풍부한 쿼리를 지 원하는 대체 상태 데이터베이스 (선택 사항)입니다.

배포의 템플릿은 구독에서 다양 한 Azure 리소스를 회전 합니다. 배포 되는 다양 한 Azure 리소스는 다음과 같습니다.

- **AKS cluster**: 고객이 제공한 입력 매개 변수에 따라 구성 된 Azure Kubernetes cluster입니다. AKS 클러스터에는 하이퍼 원장 패브릭 네트워크 구성 요소를 실행 하기 위해 구성 된 다양 한 pod이 있습니다. 만든 다른 pod는 다음과 같습니다.

  - **패브릭 도구**: 패브릭 도구는 Hyperledger 패브릭 구성 요소를 구성 합니다.
  - **Orderer/피어 pod**: HLF 네트워크의 노드입니다.
  - **프록시**: 클라이언트 응용 프로그램이 AKS 클러스터와 인터페이스 할 수 있는 N워 ix 프록시 pod입니다.
  - **패브릭 ca**: 패브릭 ca를 실행 하는 pod입니다.
- **PostgreSQL**: 패브릭 CA id를 유지 하기 위해 PostgreSQL의 인스턴스가 배포 됩니다.

- **Azure 키 자격 증명 모음**: 키 자격 증명 모음 인스턴스는 패브릭 CA 자격 증명과 고객이 제공한 루트 인증서를 저장 하기 위해 배포 됩니다 .이 인증서는 템플릿 배포를 다시 시도 하는 경우 템플릿 메커니즘을 처리 하는 데 사용 됩니다.
- **Azure managed disk**: azure 관리 디스크는 원장 및 피어 노드 세계 상태 데이터베이스용 영구 저장소에 대 한 것입니다.
- **공용 ip**: 클러스터와의 상호 작용을 위해 배포 된 AKS 클러스터의 공용 ip 끝점입니다.

## <a name="deploy-the-ordererpeer-organization"></a>Orderer/피어 조직 배포

시작하려면 여러 가상 머신과 표준 스토리지 계정의 배포를 지원할 수 있는 Azure 구독이 필요합니다. Azure 구독이 없는 경우 [Azure 체험 계정](https://azure.microsoft.com/free/)을 만들 수 있습니다.

HLF 네트워크 구성 요소 배포를 시작 하려면 [Azure Portal](https://portal.azure.com)으로 이동 합니다.

1. **Azure Kubernetes Service (미리 보기)에서 하이퍼 원장 패브릭을**검색 > **리소스 만들기 > blockchain** 을 선택 합니다.

2. **기본 사항** 페이지에서 프로젝트 세부 정보를 입력 합니다.

    ![Azure Kubernetes의 hyperledger 패브릭 서비스 템플릿](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-basics.png)

3. 다음 세부 정보를 입력합니다.
    - **구독**: HLF 네트워크 구성 요소를 배포 하려는 구독 이름을 선택 합니다.
    - **리소스 그룹**: 새 리소스 그룹을 만들거나 기존 빈 리소스 그룹을 선택 하면 리소스 그룹에 템플릿의 일부로 배포 된 모든 리소스가 저장 됩니다.
    - **지역**: HLF 구성 요소에 대 한 azure Kubernetes 클러스터를 배포 하려는 azure 지역을 선택 합니다. 이 템플릿은 AKS을 사용할 수 있는 모든 지역에서 사용할 수 있습니다. 구독에서 VM (가상 머신) 할당량 한도에 도달 하지 않는 지역을 선택 해야 합니다.
    - **리소스 접두사**: 배포 되는 리소스의 이름에 대 한 접두사입니다. 리소스 접두사의 길이는 6 자 미만 이어야 하 고 문자 조합에는 숫자와 문자를 모두 포함 해야 합니다.
4. **패브릭 설정** 탭을 선택 하 여 배포 될 HLF 네트워크 구성 요소를 정의 합니다.

    ![Azure Kubernetes의 hyperledger 패브릭 서비스 템플릿](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-settings.png)

5. 다음 세부 정보를 입력합니다.
    - **조직 이름**: 다양 한 데이터 평면 작업에 필요한 패브릭 조직의 이름입니다. 조직 이름은 배포 마다 고유 해야 합니다.
    - **패브릭 네트워크 구성 요소**: 설정 하려는 blockchain 네트워크 구성 요소를 기준으로 정렬 서비스 또는 피어 노드를 선택 합니다.
    - **노드 수** -다음은 두 가지 유형의 노드입니다.
        - 서비스 순서 지정-네트워크에 내결함성을 제공한 노드 수를 선택 합니다. 지원 되는 가져오므로 노드 수는 3, 5, 7만 지원 됩니다.
        - 피어 노드-요구 사항에 따라 1-10 노드를 선택할 수 있습니다.
    - **피어 노드 전 세계 상태 데이터베이스**: leveldb와 사용자가 패브릭 네트워크 구성 요소 드롭다운에서 피어 노드를 선택 하면이 필드가 표시 됩니다.
    - **패브릭 사용자 이름**: 패브릭 CA 인증에 사용 되는 사용자 이름을 입력 합니다.
    - **패브릭 ca 암호**: 패브릭 ca 인증에 대 한 암호를 입력 합니다.
    - **암호 확인**: 패브릭 CA 암호를 확인 합니다.
    - **인증서**: 사용자 고유의 루트 인증서를 사용 하 여 패브릭 ca를 초기화 하려면 패브릭 ca에 루트 인증서 업로드 옵션을 선택 합니다. 그 외의 경우에는 기본적으로 패브릭 ca가 자체 서명 된 인증서를 만듭니다.
    - **루트 인증서**: 패브릭 CA를 초기화 해야 하는 루트 인증서 (공개 키)를 업로드 합니다. Pem 형식의 인증서가 지원 되며, 인증서는 UTC 표준 시간대로 유효 해야 합니다.
    - **루트 인증서 개인 키**: 루트 인증서의 개인 키를 업로드 합니다. 공개 키와 개인 키가 모두 결합 된 pem 인증서가 있는 경우 여기에도 업로드 합니다.


6. **AKS 클러스터 설정** 탭을 선택 하 여 패브릭 네트워크 구성 요소가 설정 되는 기본 인프라 인 Azure Kubernetes 클러스터 구성을 정의 합니다.

    ![Azure Kubernetes의 hyperledger 패브릭 서비스 템플릿](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-aks-cluster-settings-1.png)

7. 다음 세부 정보를 입력합니다.
    - **Kubernetes cluster name**: 생성 되는 AKS 클러스터의 이름입니다. 이 필드는 제공 된 리소스 접두사에 따라 미리 채워져 있으므로 필요한 경우 변경할 수 있습니다.
    - **Kubernetes version**: 클러스터에 배포할 Kubernetes의 버전입니다. **기본** 탭에서 선택한 지역에 따라 지원 되는 버전을 변경할 수 있습니다.
    - **Dns 접두사**: AKS 클러스터에 대 한 dns (Domain name System) 이름 접두사입니다. 클러스터를 만든 후 컨테이너를 관리할 때 DNS를 사용 하 여 Kubernetes API에 연결 합니다.
    - **노드 크기**: Kubernetes 노드의 크기 이며, Azure에서 사용할 수 있는 VM Sku (재고 보존 유닛) 목록에서 선택할 수 있습니다. 성능을 최적화 하려면 Standard DS3 v2를 권장 합니다.
    - **노드 수**: 클러스터에 배포할 Kubernetes 노드의 수입니다. 이 노드 수를 패브릭 설정에 지정 된 HLF 노드 수 이상 이하로 유지 하는 것이 좋습니다.
    - **서비스 사용자 클라이언트 id**: 기존 서비스 주체의 클라이언트 id를 입력 하거나 AKS 인증에 필요한 새를 만듭니다. [서비스 주체를 만드는](/powershell/azure/create-azure-service-principal-azureps?view=azps-3.2.0#create-a-service-principal)단계를 참조 하세요.
    - **서비스 사용자 클라이언트 암호**: 서비스 사용자 클라이언트 ID에 제공 된 서비스 주체의 클라이언트 암호를 입력 합니다.
    - **클라이언트 암호 확인**: 서비스 사용자 클라이언트 암호에 제공 된 클라이언트 암호를 확인 합니다.
    - **컨테이너 모니터링 사용**: AKS 모니터링을 사용 하도록 선택 하 여 AKS 로그를 지정 된 Log Analytics 작업 영역에 푸시할 수 있습니다.
    - **Log Analytics 작업 영역**: 모니터링을 사용 하는 경우 Log Analytics 작업 영역이 생성 된 기본 작업 영역으로 채워집니다.

8. 위의 세부 정보를 모두 제공한 후 **검토 및 만들기** 탭을 선택 합니다. 검토 및 만들기에서는 사용자가 제공한 값에 대 한 유효성 검사를 트리거합니다.
9. 유효성 검사가 통과 되 면 **만들기**를 선택할 수 있습니다.
배포는 일반적으로 10-12 분 정도 걸리며, 지정 된 AKS 노드의 크기와 수에 따라 달라질 수 있습니다.
10. 성공적으로 배포 된 후에는 오른쪽 위 모퉁이에 있는 Azure 알림을 통해 알림을 받습니다.
11. **리소스 그룹으로 이동** 을 선택 하 여 템플릿 배포의 일부로 생성 된 모든 리소스를 확인 합니다. 모든 리소스 이름은 **기본** 설정에 제공 된 접두사로 시작 됩니다.

## <a name="build-the-consortium"></a>컨소시엄 빌드

주문 서비스 및 피어 노드를 배포 하는 블록 체인 consortium 게시물을 빌드하려면 아래 단계를 순서 대로 수행 해야 합니다. Azhlf (Azure HLF script)를 사용 하 여 컨소시엄 설정, 채널 만들기 및 chaincode 작업에 도움이 됩니다.

> [!NOTE]
> 스크립트에 업데이트가 있습니다 .이 업데이트는 Azure HLF 스크립트를 사용 하 여 더 많은 기능을 제공 합니다. 이전 스크립트를 참조 하려면 [여기를 참조](https://github.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/blob/master/consortiumScripts/README.md)하세요. 이 스크립트는 Azure Kubernetes 서비스 템플릿 버전 2.0.0 이상에서 Hyperledger 패브릭과 호환 됩니다. 배포 버전을 확인 하려면 [문제 해결](#troubleshoot)의 단계를 따르세요.

> [!NOTE]
> 제공 되는 Azure HLF (azhlf) 스크립트는 데모/DevTest 시나리오에만 도움이 됩니다. 이 스크립트로 만든 채널 및 컨소시엄에는 데모/DevTest 시나리오를 간소화 하는 기본 HLF 정책이 있습니다. 프로덕션 설정의 경우 기본 HLF Api를 사용 하 여 조직의 규정 준수 요구 사항에 따라 channel/consortium HLF 정책을 업데이트 하는 것이 좋습니다.


Azure HLF 스크립트를 실행 하는 모든 명령은 Azure Bash 명령줄을 통해 실행할 수 있습니다. 인터페이스 (CLI).   ![ ](./media/hyperledger-fabric-consortium-azure-kubernetes-service/arrow.png) Azure Portal의 오른쪽 위 모퉁이에 있는 Azure Kubernetes 서비스 템플릿의 하이퍼 원장 패브릭을 통해 azure shell 웹 버전에 로그인 할 수 있습니다. 명령 프롬프트에서 bash를 입력 하 고를 입력 하 여 bash CLI로 전환 하거나 shell 도구 모음에서 *bash* 를 선택 합니다.

자세한 내용은 [Azure shell](../../cloud-shell/overview.md) 을 참조 하세요.

![Azure Kubernetes의 hyperledger 패브릭 서비스 템플릿](./media/hyperledger-fabric-consortium-azure-kubernetes-service/hyperledger-powershell.png)


다음 이미지는 가져오므로 조직과 피어 조직 간에 컨소시엄을 빌드하는 단계별 프로세스를 보여 줍니다. 이러한 단계를 실행 하는 자세한 명령은 다음 섹션에 설명 되어 있습니다.

![Azure Kubernetes의 hyperledger 패브릭 서비스 템플릿](./media/hyperledger-fabric-consortium-azure-kubernetes-service/process-to-build-consortium-flow-chart.png)

클라이언트 응용 프로그램의 초기 설치에 대 한 섹션을 완료 합니다. 

1. 클라이언트 응용 프로그램 파일 다운로드
1. 환경 변수 설정
1. 조직 연결 프로필, 관리 사용자 및 MSP 가져오기

초기 설정을 완료 한 후 클라이언트 응용 프로그램을 사용 하 여 다음 작업을 수행 합니다.  

- 채널 관리
- 컨소시엄 관리
- Chaincode 관리

### <a name="download-client-application-files"></a>클라이언트 응용 프로그램 파일 다운로드

첫 번째 설정은 클라이언트 응용 프로그램 파일을 다운로드 하는 것입니다. 다음 명령을 실행 하 여 필요한 모든 파일 및 패키지를 다운로드 합니다.

```bash-interactive
curl https://raw.githubusercontent.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/master/azhlfToolSetup.sh | bash
cd azhlfTool
npm install
npm run setup
```

이러한 명령은 공용 GitHub 리포지토리에서 Azure HLF client 응용 프로그램 코드를 복제 한 다음 모든 종속 npm 패키지를 로드 합니다. 명령을 성공적으로 실행 한 후에는 현재 디렉터리에서 node_modules 폴더를 볼 수 있습니다. 필요한 모든 패키지가 node_modules 폴더에 로드 됩니다.

### <a name="setup-environment-variables"></a>환경 변수 설정하기

> [!NOTE]
> 모든 환경 변수는 Azure 리소스 명명 규칙을 따릅니다.

#### <a name="set-environment-variables-for-orderer-organization-client"></a>가져오므로 조직 클라이언트에 대 한 환경 변수 설정

```bash
ORDERER_ORG_SUBSCRIPTION=<ordererOrgSubscription>
ORDERER_ORG_RESOURCE_GROUP=<ordererOrgResourceGroup>
ORDERER_ORG_NAME=<ordererOrgName>
ORDERER_ADMIN_IDENTITY="admin.$ORDERER_ORG_NAME"
CHANNEL_NAME=<channelName>
```

#### <a name="set-the-environment-variables-for-peer-organization-client"></a>피어 조직 클라이언트에 대 한 환경 변수 설정

```bash
PEER_ORG_SUBSCRIPTION=<peerOrgSubscritpion>
PEER_ORG_RESOURCE_GROUP=<peerOrgResourceGroup>
PEER_ORG_NAME=<peerOrgName>
PEER_ADMIN_IDENTITY="admin.$PEER_ORG_NAME"
CHANNEL_NAME=<channelName>
```

> [!NOTE]
> 컨소시엄의 피어 Orgs 수에 따라 피어 명령을 반복 하 고 환경 변수를 적절 하 게 설정 해야 할 수 있습니다.

#### <a name="set-the-environment-variables-for-setting-up-azure-storage-account"></a>Azure Storage 계정 설정에 대 한 환경 변수 설정

```bash
STORAGE_SUBSCRIPTION=<subscriptionId>
STORAGE_RESOURCE_GROUP=<azureFileShareResourceGroup>
STORAGE_ACCOUNT=<azureStorageAccountName>
STORAGE_LOCATION=<azureStorageAccountLocation>
STORAGE_FILE_SHARE=<azureFileShareName>
```

계정을 만드는 Azure Storage 다음 단계를 사용 합니다. 이미 Azure Storage 계정을 만든 경우에는 다음 단계를 건너뜁니다.

```bash
az account set --subscription $STORAGE_SUBSCRIPTION
az group create -l $STORAGE_LOCATION -n $STORAGE_RESOURCE_GROUP
az storage account create -n $STORAGE_ACCOUNT -g  $STORAGE_RESOURCE_GROUP -l $STORAGE_LOCATION --sku Standard_LRS
```

Azure Storage 계정에서 파일 공유를 만드는 데 다음 단계를 사용 합니다. 파일 공유를 이미 만든 경우에는 다음 단계를 건너뜁니다.

```bash
STORAGE_KEY=$(az storage account keys list --resource-group $STORAGE_RESOURCE_GROUP  --account-name $STORAGE_ACCOUNT --query "[0].value" | tr -d '"')
az storage share create  --account-name $STORAGE_ACCOUNT  --account-key $STORAGE_KEY  --name $STORAGE_FILE_SHARE
```

다음 단계를 사용 하 여 Azure 파일 공유 연결 문자열을 생성 합니다.

```bash
STORAGE_KEY=$(az storage account keys list --resource-group $STORAGE_RESOURCE_GROUP  --account-name $STORAGE_ACCOUNT --query "[0].value" | tr -d '"')
SAS_TOKEN=$(az storage account generate-sas --account-key $STORAGE_KEY --account-name $STORAGE_ACCOUNT --expiry `date -u -d "1 day" '+%Y-%m-%dT%H:%MZ'` --https-only --permissions lruwd --resource-types sco --services f | tr -d '"')
AZURE_FILE_CONNECTION_STRING=https://$STORAGE_ACCOUNT.file.core.windows.net/$STORAGE_FILE_SHARE?$SAS_TOKEN

```

### <a name="import-organization-connection-profile-admin-user-identity-and-msp"></a>조직 연결 프로필, 관리 사용자 id 및 MSP 가져오기

아래 명령을 실행 하 여 Azure Kubernetes 클러스터에서 조직의 연결 프로필, 관리자 사용자 id 및 MSP를 가져오고 이러한 id를 클라이언트 응용 프로그램 로컬 저장소 (예: "azhlfTool/stores" 디렉터리)에 저장 합니다.

가져오므로 조직의 경우:

```bash
./azhlf adminProfile import fromAzure -o $ORDERER_ORG_NAME -g $ORDERER_ORG_RESOURCE_GROUP -s $ORDERER_ORG_SUBSCRIPTION
./azhlf connectionProfile import fromAzure -g $ORDERER_ORG_RESOURCE_GROUP -s $ORDERER_ORG_SUBSCRIPTION -o $ORDERER_ORG_NAME   
./azhlf msp import fromAzure -g $ORDERER_ORG_RESOURCE_GROUP -s $ORDERER_ORG_SUBSCRIPTION -o $ORDERER_ORG_NAME
```

피어 조직의 경우:

```bash
./azhlf adminProfile import fromAzure -g $PEER_ORG_RESOURCE_GROUP -s $PEER_ORG_SUBSCRIPTION -o $PEER_ORG_NAME
./azhlf connectionProfile import fromAzure -g $PEER_ORG_RESOURCE_GROUP -s $PEER_ORG_SUBSCRIPTION -o $PEER_ORG_NAME
./azhlf msp import fromAzure -g $PEER_ORG_RESOURCE_GROUP -s $PEER_ORG_SUBSCRIPTION -o $PEER_ORG_NAME
```

### <a name="create-channel-command"></a>채널 만들기 명령

가져오므로 조직 클라이언트에서 명령을 실행 하 여 새 채널을 만듭니다. 이 명령은 가져오므로 조직에만 채널을 만듭니다.  

```bash
./azhlf channel create -c $CHANNEL_NAME -u $ORDERER_ADMIN_IDENTITY -o $ORDERER_ORG_NAME
```

### <a name="consortium-management-commands"></a>컨소시엄 관리 명령

>[!NOTE]
> 컨소시엄 작업을 시작 하기 전에 클라이언트 응용 프로그램의 초기 설정이 완료 되었는지 확인 합니다.  

채널 및 컨소시엄에서 피어 조직을 추가 하기 위해 지정 된 순서에서 아래 명령을 실행 합니다.
1.  피어 조직 클라이언트에서 Azure Storage에 피어 조직 MSP 업로드

      ```bash
      ./azhlf msp export toAzureStorage -f  $AZURE_FILE_CONNECTION_STRING -o $PEER_ORG_NAME
      ```
2.  가져오므로 조직 클라이언트에서 Azure Storage의 피어 조직 MSP를 다운로드 한 다음 명령을 실행 하 여 채널/컨소시엄에서 피어 조직을 추가 합니다.

      ```bash
      ./azhlf msp import fromAzureStorage -o $PEER_ORG_NAME -f $AZURE_FILE_CONNECTION_STRING
      ./azhlf channel join -c  $CHANNEL_NAME -o $ORDERER_ORG_NAME  -u $ORDERER_ADMIN_IDENTITY -p $PEER_ORG_NAME
      ./azhlf consortium join -o $ORDERER_ORG_NAME  -u $ORDERER_ADMIN_IDENTITY -p $PEER_ORG_NAME
      ```

3.  가져오므로 조직 클라이언트에서이 연결 프로필을 사용 하 여 피어 조직이 가져오므로 노드에 연결할 수 있도록 Azure Storage에 가져오므로 연결 프로필을 업로드 합니다.

      ```bash
      ./azhlf connectionProfile  export toAzureStorage -o $ORDERER_ORG_NAME -f $AZURE_FILE_CONNECTION_STRING
      ```

4.  피어 조직 클라이언트에서 가져오므로 연결 프로필을 Azure Storage에서 다운로드 한 다음 명령을 실행 하 여 채널에서 피어 노드를 추가 합니다.

      ```bash
      ./azhlf connectionProfile  import fromAzureStorage -o $ORDERER_ORG_NAME -f $AZURE_FILE_CONNECTION_STRING
      ./azhlf channel joinPeerNodes -o $PEER_ORG_NAME  -u $PEER_ADMIN_IDENTITY -c $CHANNEL_NAME --ordererOrg $ORDERER_ORG_NAME
      ```

마찬가지로, 채널에 피어 조직을 더 추가 하려면 필요한 피어 조직에 따라 피어 환경 변수를 업데이트 하 고 1 ~ 4 단계를 실행 합니다.

### <a name="set-anchor-peers-command"></a>앵커 피어 명령 설정

피어 조직 클라이언트에서 명령을 실행 하 여 지정 된 채널에서 피어 조직의 앵커 피어를 설정 합니다.

>[!NOTE]
> 이 명령을 실행 하기 전에 Consortium 관리 명령을 사용 하 여 채널에 피어 조직을 추가 해야 합니다.

```bash
./azhlf channel setAnchorPeers -c $CHANNEL_NAME -p <anchorPeersList> -o $PEER_ORG_NAME -u $PEER_ADMIN_IDENTITY --ordererOrg $ORDERER_ORG_NAME
```

`<anchorPeersList>`앵커 피어로 설정할 피어 노드의 공백으로 구분 된 목록입니다. 예를 들면 다음과 같습니다.

  - `<anchorPeersList>`Peer1 노드만 앵커 피어로 설정 하려면 "peer1"로 설정 합니다.
  - `<anchorPeersList>`Peer1 및 peer3 노드를 앵커 피어로 설정 하려면 "peer1" "peer3"로 설정 합니다.

## <a name="chaincode-management-commands"></a>Chaincode 관리 명령

>[!NOTE]
> Chaincode 작업을 시작 하기 전에 클라이언트 응용 프로그램의 초기 설정이 완료 되었는지 확인 합니다.  

### <a name="set-the-below-chaincode-specific-environment-variables"></a>아래 chaincode 특정 환경 변수 설정

```bash
# peer organization name where chaincode operation is to be performed
ORGNAME=<PeerOrgName>
USER_IDENTITY="admin.$ORGNAME"  
# If you are using chaincode_example02 then set CC_NAME=“chaincode_example02”
CC_NAME=<chaincodeName>  
# If you are using chaincode_example02 then set CC_VERSION=“1” for validation
CC_VERSION=<chaincodeVersion>
# Language in which chaincode is written. Supported languages are 'node', 'golang' and 'java'  
# Default value is 'golang'  
CC_LANG=<chaincodeLanguage>  
# CC_PATH contains the path where your chaincode is place.
# If you are using chaincode_example02 to validate then CC_PATH=“/home/<username>/azhlfTool/samples/chaincode/src/chaincode_example02/go”
CC_PATH=<chaincodePath>  
# Channel on which chaincode is to be instantiated/invoked/queried  
CHANNEL_NAME=<channelName>  
```

### <a name="install-chaincode"></a>Chaincode 설치  

아래 명령을 실행 하 여 피어 조직에 chaincode를 설치 합니다.  

```bash
./azhlf chaincode install -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -p $CC_PATH -l $CC_LANG -v $CC_VERSION  

```
ORGNAME 환경 변수에 설정 된 피어 조직의 모든 피어 노드에 chaincode를 설치 합니다. 두 개 이상의 피어 조직이 채널에 있고 chaincode를 모두 설치 하려면 각 피어 조직에 대해이 명령을 별도로 실행 합니다.  

다음 단계를 수행 합니다.  

1.  `ORGNAME` `USER_IDENTITY` PeerOrg1 및 issue 명령으로 설정 `./azhlf chaincode install` 합니다.  
2.  `ORGNAME` `USER_IDENTITY` PeerOrg2 및 issue 명령으로 설정 `./azhlf chaincode install` 합니다.  

### <a name="instantiate-chaincode"></a>Chaincode 인스턴스화  

피어 클라이언트 응용 프로그램에서 아래 명령을 실행 하 여 채널에서 chaincode를 인스턴스화합니다.  

```bash
./azhlf chaincode instantiate -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -v $CC_VERSION -c $CHANNEL_NAME -f <instantiateFunc> --args <instantiateFuncArgs>  
```

각각 및에서 인스턴스화 함수 이름 및 공백으로 구분 된 인수 목록을 전달 `<instantiateFunc>` `<instantiateFuncArgs>` 합니다. 예를 들어 chaincode_example02에서로 설정 된 chaincode을 `<instantiateFunc>` `init` `<instantiateFuncArgs>` "a" "2000" "b" "1000"로 설정 합니다.

> [!NOTE]
> 채널의 한 피어 조직에서 한 번 명령을 실행 합니다. 트랜잭션이 가져오므로에 성공적으로 제출 되 면 가져오므로는이 트랜잭션을 채널의 모든 피어 조직에 배포 합니다. 따라서 chaincode은 채널의 모든 피어 조직에 있는 모든 피어 노드에서 인스턴스화됩니다.  

### <a name="invoke-chaincode"></a>Chaincode 호출  

피어 조직 클라이언트에서 아래 명령을 실행 하 여 chaincode 함수를 호출 합니다.  

```bash
./azhlf chaincode invoke -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -c $CHANNEL_NAME -f <invokeFunc> -a <invokeFuncArgs>  
```

각각 및에서 호출 함수 이름 및 공백으로 구분 된 인수 목록을 전달  `<invokeFunction>`    `<invokeFuncArgs>`   합니다. Chaincode_example02를 계속 진행 하 여 호출 작업을  `<invokeFunction>`    `invoke`    `<invokeFuncArgs>`   "a" "b" "10"으로 설정 합니다.  

>[!NOTE]
> 채널의 한 피어 조직에서 한 번 명령을 실행 합니다. 트랜잭션이 가져오므로에 성공적으로 제출 되 면 가져오므로는이 트랜잭션을 채널의 모든 피어 조직에 배포 합니다. 따라서 세계 상태는 채널에 있는 모든 피어 조직의 모든 피어 노드에서 업데이트 됩니다.  


### <a name="query-chaincode"></a>Chaincode 쿼리  

아래 명령을 실행 하 여 chaincode를 쿼리 합니다.  

```bash
./azhlf chaincode query -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -c $CHANNEL_NAME -f <queryFunction> -a <queryFuncArgs>  
```
각각 및에서 쿼리 함수 이름 및 공백으로 구분 된 인수 목록을 전달  `<queryFunction>`    `<queryFuncArgs>`   합니다. 다시 chaincode_example02로 이동 chaincode을 참조로 가져와 세계 상태에서 "a"의 값을  `<queryFunction>`    `query`  `<queryArgs>` "a"로 설정 합니다.  

## <a name="troubleshoot"></a>문제 해결

**실행 중인 템플릿 버전을 확인 하려면**

아래 명령을 실행 하 여 템플릿 배포의 버전을 찾습니다.

템플릿이 배포 된 리소스 그룹에 따라 아래 환경 변수를 설정 합니다.

```bash

SWITCH_TO_AKS_CLUSTER() { az aks get-credentials --resource-group $1 --name $2 --subscription $3; }
AKS_CLUSTER_SUBSCRIPTION=<AKSClusterSubscriptionID>
AKS_CLUSTER_RESOURCE_GROUP=<AKSClusterResourceGroup>
AKS_CLUSTER_NAME=<AKSClusterName>
```
아래 명령을 실행 하 여 템플릿 버전을 인쇄 합니다.
```bash
SWITCH_TO_AKS_CLUSTER $AKS_CLUSTER_RESOURCE_GROUP $AKS_CLUSTER_NAME $AKS_CLUSTER_SUBSCRIPTION
kubectl describe pod fabric-tools -n tools | grep "Image:" | cut -d ":" -f 3

```

## <a name="support-and-feedback"></a>지원 및 피드백

Azure Blockchain 뉴스의 경우 [Azure Blockchain 블로그](https://azure.microsoft.com/blog/topics/blockchain/)를 방문하여 Azure Blockchain 엔지니어링 팀의 블록체인 서비스 제공 및 정보를 최신 상태로 유지하세요.

제품 피드백을 제공하거나 새로운 기능을 요청하려면 [블록체인에 대한 Azure 피드백 포럼](https://aka.ms/blockchainuservoice)을 통해 아이디어를 게시하거나 투표하세요.

### <a name="community-support"></a>커뮤니티 지원

Microsoft 엔지니어 및 Azure Blockchain 커뮤니티 전문가와 소통하세요.

- [Microsoft Q&질문 페이지](/answers/topics/azure-blockchain-workbench.html)입니다. 블록 체인 템플릿에 대 한 엔지니어링 지원은 배포 문제로 제한 됩니다.
- [Microsoft 기술 커뮤니티](https://techcommunity.microsoft.com/t5/Blockchain/bd-p/AzureBlockchain)
- [스택 오버플로](https://stackoverflow.com/questions/tagged/azure-blockchain-workbench)
