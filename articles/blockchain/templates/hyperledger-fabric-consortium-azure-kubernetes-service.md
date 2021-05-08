---
title: Azure Kubernetes Service에 Hyperledger Fabric 컨소시엄 배포
description: Azure Kubernetes Service에 Hyperledger Fabric 컨소시엄 네트워크를 배포하고 구성하는 방법
ms.date: 03/01/2021
ms.topic: how-to
ms.reviewer: ravastra
ms.custom: contperf-fy21q3, devx-track-azurecli
ms.openlocfilehash: 03f19d1922c011c1b5304b66488e9fa8de703bf9
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107478319"
---
# <a name="deploy-hyperledger-fabric-consortium-on-azure-kubernetes-service"></a>Azure Kubernetes Service에 Hyperledger Fabric 컨소시엄 배포

AKS(Azure Kubernetes Service) 템플릿의 Hyperledger Fabric을 사용하여 Azure에서 Hyperledger Fabric 컨소시엄 네트워크를 배포하고 구성할 수 있습니다.

이 문서를 읽고 나면:

- Hyperledger Fabric 블록체인 네트워크의 블록을 구성하는 Hyperledger Fabric과 구성 요소에 대한 실무 지식을 얻습니다.
- 프로덕션 시나리오를 위한 Azure Kubernetes Service에 Hyperledger Fabric 컨소시엄 네트워크를 배포하고 구성하는 방법을 배웁니다.

[!INCLUDE [Preview note](./includes/preview.md)]

## <a name="choose-an-azure-blockchain-solution"></a>Azure Blockchain 솔루션을 선택합니다.

솔루션 템플릿을 사용하도록 선택하기 전에 시나리오를 사용 가능한 Azure Blockchain 옵션의 일반적인 사용 사례와 비교합니다.

옵션 | 서비스 모델 | 일반적인 사용 사례
-------|---------------|-----------------
솔루션 템플릿 | IaaS | 솔루션 템플릿은 완전히 구성된 블록체인 네트워크 토폴로지를 준비하는 데 사용할 수 있는 Azure Resource Manager 템플릿입니다. 이 템플릿은 블록체인 네트워크 유형에 대해 Microsoft Azure 컴퓨팅, 네트워킹 및 저장소 서비스를 배포하고 구성합니다. 솔루션 템플릿은 서비스 수준 계약 없이 제공됩니다. 지원을 받으려면 [Microsoft Q&A 페이지](/answers/topics/azure-blockchain-workbench.html)를 이용하세요.
[Azure Blockchain Service](../service/overview.md) | PaaS | Azure Blockchain Service Preview는 컨소시엄 블록체인 네트워크의 포맷, 관리 및 거버넌스를 간소화합니다. PaaS, 컨소시엄 관리 또는 계약 및 트랜잭션 개인 정보를 요구하는 솔루션을 위해 Azure Blockchain Service를 사용합니다.
[Azure Blockchain Workbench](../workbench/overview.md) | IaaS 및 PaaS | Azure Blockchain Workbench Preview는 비즈니스 프로세스와 데이터를 다른 조직과 공유하는 블록체인 애플리케이션을 만들고 배포할 수 있도록 설계된 Azure 서비스 및 기능 컬렉션입니다. 블록체인 솔루션을 프로토타이핑하거나 블록체인 애플리케이션에 대한 개념 증명을 위해 Azure Blockchain Workbench를 사용합니다. Azure Blockchain Workbench는 서비스 수준 규약 없이 제공됩니다. 지원을 받으려면 [Microsoft Q&A 페이지](/answers/topics/azure-blockchain-workbench.html)를 이용하세요.

## <a name="deploy-the-orderer-and-peer-organization"></a>orderer와 피어 조직 배포

시작하려면 여러 가상 머신과 표준 스토리지 계정의 배포를 지원할 수 있는 Azure 구독이 필요합니다. Azure 구독이 없는 경우 [Azure 계정을 무료로 만들 수 있습니다](https://azure.microsoft.com/free/).

Hyperledger Fabric 네트워크 구성 요소의 배포를 시작하려면 [Azure Portal](https://portal.azure.com)로 이동 합니다.

1. **리소스 만들기** > **블록체인** 을 선택한 다음 **Azure Kubernetes Service의 Hyperledger Fabric(미리 보기)** 를 검색합니다.

2. **기본 사항** 탭에서 프로젝트 세부 정보를 입력합니다.

    ![기본 사항 탭을 보여주는 스크린샷](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-basics.png)

3. 다음 세부 정보를 입력합니다.
    - **구독**: Hyperledger Fabric 네트워크 구성 요소를 배포할 구독 이름을 선택합니다.
    - **리소스 그룹**: 새 리소스 그룹을 만들거나 비어 있는 기존 리소스 그룹을 선택합니다. 리소스 그룹은 템플릿의 일부로 배포된 모든 리소스를 포함합니다.
    - **지역**: Hyperledger Fabric 구성 요소에 대한 Azure Kubernetes Service 클러스터를 배포하려는 Azure 지역을 선택합니다. 이 템플릿은 AKS를 이용할 수 있는 모든 지역에서 사용할 수 있습니다. 구독이 VM(가상 머신) 할당량 제한에 도달하지 않은 지역을 선택합니다.
    - **리소스 접두사**: 배포되는 리소스 이름에 접두사를 입력합니다. 6자 미만의 숫자와 문자를 모두 포함한 조합이어야 합니다.
4. **패브릭 설정** 탭을 선택하여 배포 될 Hyperledger Fabric 네트워크 구성 요소를 정의합니다.

    ![패브릭 설정 탭을 보여주는 스크린샷](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-settings.png)

5. 다음 세부 정보를 입력합니다.
    - **조직 이름**: 다양한 데이터 평면 작업에 필요한 Hyperledger Fabric 조직의 이름을 입력합니다. 조직 이름은 배포별로 고유해야 합니다.
    - **패브릭 네트워크 구성 요소**: 설정하려는 블록체인 네트워크 구성 요소를 기준으로 **정렬 서비스** 또는 **피어 노드** 를 선택합니다.
    - **노드 수**: 다음은 두 가지 유형의 노드입니다.
        - **주문 서비스**: 원장의 트랜잭션 순서를 담당하는 노드입니다. 네트워크에 내결함성을 제공할 노드 수를 선택합니다. 지원되는 주문 노드 수는 3, 5, 7입니다.
        - **피어 노드**: 원장 및 스마트 계약을 호스트하는 노드입니다. 요구 사항에 따라 1~10개의 노드를 선택할 수 있습니다.
    - **피어 노드 월드 스테이트 데이터베이스**: 피어 노드에 대한 월드 스테이트 데이터베이스입니다. LevelDB는 피어 노드에 포함된 기본 상태 데이터베이스입니다. chaincode 데이터를 단순 키/값 쌍으로 저장하고 키, 키 범위 및 복합 키 쿼리만 지원 합니다. CouchDB는 chaincode 데이터 값이 JSON으로 모델링될 때 다양한 쿼리를 지원하는 선택적 대체 상태 데이터베이스입니다. **패브릭 네트워크 구성 요소** 드롭다운 목록에서 **피어 노드** 를 선택하면 이 필드가 표시됩니다.
    - **패브릭 CA 사용자 이름**: 패브릭 인증 기관에서 인증 기관을 호스트하는 서버 프로세스를 초기화하고 시작할 수 있습니다. ID 및 인증서를 관리할 수 있습니다. 템플릿의 일부로 배포된 각 AKS 클러스터에는 기본적으로 패브릭 CA Pod가 있습니다. 패브릭 CA 인증에 사용되는 사용자 이름을 입력합니다.
    - **패브릭 CA 암호**: 패브릭 CA 인증을 위한 암호를 입력 합니다.
    - **암호 확인**: 패브릭 CA 암호를 확인합니다.
    - **인증서**: 자체 루트 인증서를 사용하여 패브릭 CA를 초기화 하려면 **패브릭 CA에 루트 인증서 업로드** 옵션을 선택합니다. 그렇지 않으면 패브릭 CA가 기본적으로 자체 서명된 인증서를 만듭니다.
    - **루트 인증서**: 패브릭 CA를 초기화해야 하는 루트 인증서(공개 키)를 업로드합니다. .pem 형식 인증서가 지원됩니다. 인증서가 유효하고 UTC 표준 시간대에 있어야 합니다.
    - **루트 인증서 프라이빗 키**: 루트 인증서의 프라이빗 키를 업로드합니다. 공개 키와 프라이빗 키를 결합한 .pem 인증서가 있는 경우 여기에도 업로드합니다.


6. **AKS 클러스터 설정** 탭을 선택하여 Azure Kubernetes Service 클러스터 구성을 정의합니다. AKS 클러스터에는 Hyperledger Fabric 네트워크 구성 요소를 실행하기 위해 구성된 다양한 pod이 있습니다. 배포된 Azure 리소스는 다음과 같습니다.

    - **패브릭 도구**: Hyperledger Fabric 구성 요소를 구성하는 도구입니다.
    - **Orderer/피어 Pod**: Hyperledger Fabric 네트워크의 노드입니다.
    - **프록시**: 클라이언트 애플리케이션에서 AKS 클러스터와 통신할 수 있는 NGNIX 프록시 Pod입니다.
    - **패브릭 CA**: 패브릭 CA를 실행하는 Pod입니다.
    - **PostgreSQL**: 패브릭 CA ID를 유지 관리하는 데이터베이스 인스턴스입니다.
    - **키 자격 증명 모음**: 패브릭 CA 자격 증명과 고객이 제공한 루트 인증서를 저장하기 위해 배포된 Azure Key Vault 서비스의 인스턴스입니다. 템플릿 배포를 다시 시도하는 경우 템플릿 메커니즘을 처리하기 위해 자격 증명 모음을 사용합니다.
    - **관리 디스크**: 원장 및 피어 노드의 월드 스테이트 데이터베이스에 대한 영구 저장소를 제공하는 Azure Managed Disks 서비스의 인스턴스입니다.
    - **공용 IP**: 클러스터와의 통신을 위해 배포된 AKS 클러스터의 엔드포인트입니다.

    다음 세부 정보를 입력합니다. 

    ![AKS 클러스터 설정 탭을 보여주는 스크린샷](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-aks-cluster-settings-1.png)

    - **Kubernetes 클러스터 이름**: 필요한 경우 AKS 클러스터의 이름을 변경합니다. 이 필드는 제공된 리소스 접두사에 따라 미리 채워져 있습니다.
    - **Kubernetes 버전**: 클러스터에 배포할 Kubernetes의 버전을 선택합니다. **기본** 탭에서 선택한 지역에 따라 지원되는 버전을 변경할 수 있습니다.
    - **DNS 접두사**: AKS 클러스터에 대한 DNS(Domain name System) 이름 접두사를 입력합니다. 클러스터를 만든 후 컨테이너를 관리할 때 DNS를 사용하여 Kubernetes API에 연결합니다.
    - **노드 크기**: Kubernetes 노드의 크기를 Azure에서 사용할 수 있는 VM SKU(Stock Keeping Unit) 목록에서 선택할 수 있습니다. 성능을 최적화하려면 Standard DS3 v2를 권장합니다.
    - **노드 수**: 클러스터에 배포할 Kubernetes 노드 수를 입력합니다. 이 노드 수를 **패브릭 설정** 탭에서 지정된 Hyperledger Fabric 노드 수와 동일하게 유지할 것을 권장합니다.
    - **서비스 사용자 클라이언트 ID**: 기존 서비스 주체 클라이언트 ID를 입력하거나 새 서비스 주체의 클라이언트 ID를 만듭니다. AKS 인증에는 서비스 주체가 필요합니다. [서비스 주체 생성하기](/powershell/azure/create-azure-service-principal-azureps#create-a-service-principal)를 참고하세요.
    - **서비스 사용자 클라이언트 암호**: 서비스 사용자의 클라이언트 ID에 제공된 서비스 주체의 클라이언트 암호를 입력합니다.
    - **클라이언트 암호 확인**: 서비스 주체에 대한 클라이언트 암호를 확인합니다.
    - **컨테이너 모니터링 사용**: AKS 모니터링을 사용하도록 선택하여 AKS 로그를 지정된 Log Analytics 작업 영역에 푸시할 수 있습니다.
    - **Log Analytics 작업 영역**: 모니터링을 사용하는 경우 Log Analytics 작업 영역이 생성된 기본 작업 영역으로 채워집니다.

8. **검토 및 만들기** 탭을 선택합니다. 이 단계에서는 사용자가 제공한 값에 대한 유효성 검사가 트리거됩니다.
9. 유효성 검사를 통과하면 **만들기** 를 선택합니다.

    배포는 일반적으로 10~12분이 소요됩니다. 시간은 지정된 AKS 노드의 크기와 수에 따라 달라질 수 있습니다.
10. 성공적으로 배포되면 오른쪽 위에 있는 Azure 알림을 통해 알림을 받게 됩니다.
11. **리소스 그룹으로 이동** 을 선택하여 템플릿 배포의 일부로 생성된 모든 리소스를 확인합니다. 모든 리소스 이름은 **기본** 탭에 제공된 접두사로 시작됩니다.

## <a name="build-the-consortium"></a>컨소시엄 빌드

주문 서비스 및 피어 노드를 배포한 후 블록체인 컨소시엄을 빌드하려면 다음 단계를 순서대로 수행합니다. Azure Hyperledger Fabric 스크립트(azhlf)를 사용하면 컨소시엄을 설정하고, 채널을 만들고, chaincode 작업을 수행할 수 있습니다.

> [!NOTE]
> Azure Hyperledger Fabric(azhlf) 스크립트가 추가 기능을 제공하도록 업데이트되었습니다. 이전 스크립트를 참조하려면 [GitHub의 추가 정보](https://github.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/blob/master/consortiumScripts/README.md)를 확인하세요. 이 스크립트는 Azure Kubernetes Service 템플릿 버전 2.0.0 이상에서 Hyperledger Fabric과 호환됩니다. 배포 버전을 확인하려면 [문제 해결](#troubleshoot)의 단계를 따르세요.

> [!NOTE]
> 이 스크립트는 데모, 개발 및 테스트 시나리오를 돕기 위해서만 제공됩니다. 이 스크립트에 생성되는 채널과 컨소시엄에는 데모, 개발 및 테스트 시나리오를 간소화하는 기본 Hyperledger Fabric 정책이 포함됩니다. 프로덕션 설정의 경우, 기본 Hyperledger Fabric API를 사용하여 조직의 규정 준수 요구 사항에 따라 채널/컨소시엄 Hyperledger Fabric 정책을 업데이트하는 것이 좋습니다.


Azure Hyperledger Fabric 스크립트를 실행하는 모든 명령은 Azure Bash CLI(명령줄 인터페이스)를 통해 실행할 수 있습니다. Azure Portal의 오른쪽 위에 있는 ![Azure Kubernetes Service 템플릿의 Hyperledger Fabric](./media/hyperledger-fabric-consortium-azure-kubernetes-service/arrow.png) 옵션을 통해 Azure Cloud Shell에 로그인할 수 있습니다. 명령 프롬프트에서 `bash`를 입력하고 Enter 키를 선택하여 Bash CLI로 전환하거나 Cloud Shell 도구 모음에서 **Bash** 를 선택합니다.

자세한 내용은 [Azure Cloud Shell](../../cloud-shell/overview.md)를 참조하세요.

![Azure Cloud Shell을 보여주는 스크린샷](./media/hyperledger-fabric-consortium-azure-kubernetes-service/hyperledger-powershell.png)


다음 이미지는 orderer 조직과 피어 조직 간에 컨소시엄을 빌드하는 단계별 프로세스를 보여줍니다. 다음 섹션에서는 이러한 단계를 완료하는 자세한 명령을 보여줍니다.

![컨소시엄을 빌드하는 프로세스의 다이어그램입니다.](./media/hyperledger-fabric-consortium-azure-kubernetes-service/process-to-build-consortium-flow-chart.png)

초기 설정을 완료한 후 클라이언트 응용 프로그램을 사용하여 다음 작업을 수행합니다.

- 채널 관리
- 컨소시엄 관리
- chaincode 관리

### <a name="download-client-application-files"></a>클라이언트 응용 프로그램 파일 다운로드

첫 번째 설정은 클라이언트 응용 프로그램 파일을 다운로드하는 것입니다. 다음 명령을 실행하여 필요한 모든 파일 및 패키지를 다운로드합니다.

```bash-interactive
curl https://raw.githubusercontent.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/master/azhlfToolSetup.sh | bash
cd azhlfTool
npm install
npm run setup
```

이러한 명령은 공용 GitHub 리포지토리에서 Azure Hyperledger Fabric 클라이언트 애플리케이션 코드를 복제한 다음 모든 종속 npm 패키지를 로드합니다. 명령을 성공적으로 실행한 후에 현재 디렉터리에서 node_modules 폴더를 볼 수 있습니다. 필요한 모든 패키지가 node_modules 폴더에 로드됩니다.

### <a name="set-up-environment-variables"></a>환경 변수 설정

모든 환경 변수는 Azure 리소스 명명 규칙을 따릅니다.

#### <a name="set-environment-variables-for-the-orderer-organizations-client"></a>orderer 조직의 클라이언트에 대한 환경 변수 설정

```bash
ORDERER_ORG_SUBSCRIPTION=<ordererOrgSubscription>
ORDERER_ORG_RESOURCE_GROUP=<ordererOrgResourceGroup>
ORDERER_ORG_NAME=<ordererOrgName>
ORDERER_ADMIN_IDENTITY="admin.$ORDERER_ORG_NAME"
CHANNEL_NAME=<channelName>
```

#### <a name="set-environment-variables-for-the-peer-organizations-client"></a>피어 조직의 클라이언트에 대한 환경 변수 설정

```bash
PEER_ORG_SUBSCRIPTION=<peerOrgSubscritpion>
PEER_ORG_RESOURCE_GROUP=<peerOrgResourceGroup>
PEER_ORG_NAME=<peerOrgName>
PEER_ADMIN_IDENTITY="admin.$PEER_ORG_NAME"
CHANNEL_NAME=<channelName>
```

컨소시엄의 피어 조직 수에 따라 피어 명령을 반복하고 올바른 환경 변수를 설정해야 할 수 있습니다.

#### <a name="set-environment-variables-for-an-azure-storage-account"></a>Azure Storage 계정 환경 변수 설정

```bash
STORAGE_SUBSCRIPTION=<subscriptionId>
STORAGE_RESOURCE_GROUP=<azureFileShareResourceGroup>
STORAGE_ACCOUNT=<azureStorageAccountName>
STORAGE_LOCATION=<azureStorageAccountLocation>
STORAGE_FILE_SHARE=<azureFileShareName>
```

다음 명령을 실행하여 Azure Storage 계정을 만듭니다. 이미 Azure storage 계정이 있는 경우 이 단계를 건너뜁니다.

```bash
az account set --subscription $STORAGE_SUBSCRIPTION
az group create -l $STORAGE_LOCATION -n $STORAGE_RESOURCE_GROUP
az storage account create -n $STORAGE_ACCOUNT -g  $STORAGE_RESOURCE_GROUP -l $STORAGE_LOCATION --sku Standard_LRS
```

다음 명령을 사용하여 Azure storage 계정에서 파일 공유를 만듭니다. 파일 공유가 이미 있는 경우 이 단계를 건너뜁니다.

```bash
STORAGE_KEY=$(az storage account keys list --resource-group $STORAGE_RESOURCE_GROUP  --account-name $STORAGE_ACCOUNT --query "[0].value" | tr -d '"')
az storage share create  --account-name $STORAGE_ACCOUNT  --account-key $STORAGE_KEY  --name $STORAGE_FILE_SHARE
```

다음 명령을 사용하여 Azure 파일 공유에 대한 연결 문자열을 생성합니다.

```bash
STORAGE_KEY=$(az storage account keys list --resource-group $STORAGE_RESOURCE_GROUP  --account-name $STORAGE_ACCOUNT --query "[0].value" | tr -d '"')
SAS_TOKEN=$(az storage account generate-sas --account-key $STORAGE_KEY --account-name $STORAGE_ACCOUNT --expiry `date -u -d "1 day" '+%Y-%m-%dT%H:%MZ'` --https-only --permissions lruwd --resource-types sco --services f | tr -d '"')
AZURE_FILE_CONNECTION_STRING=https://$STORAGE_ACCOUNT.file.core.windows.net/$STORAGE_FILE_SHARE?$SAS_TOKEN

```

### <a name="import-an-organization-connection-profile-admin-user-identity-and-msp"></a>조직 연결 프로필, 관리 사용자 ID 및 MSP 가져오기

다음 명령을 사용하여 Azure Kubernetes Service 클러스터에서 조직의 연결 프로필, 관리자 사용자 ID 및 MSP(관리 서비스 공급자)를 가져오고 이러한 ID를 클라이언트 애플리케이션의 로컬 저장소에 저장합니다. 로컬 저장소의 예로는 *azhlfTool/stores* 디렉터리가 있습니다.

orderer 조직의 경우:

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

### <a name="create-a-channel"></a>채널 만들기

orderer 조직의 클라이언트에서 다음 명령을 사용하여 orderer 조직을 포함하는 채널을 만듭니다.  

```bash
./azhlf channel create -c $CHANNEL_NAME -u $ORDERER_ADMIN_IDENTITY -o $ORDERER_ORG_NAME
```

### <a name="add-a-peer-organization-for-consortium-management"></a>컨소시엄 관리를 위한 피어 조직 추가

>[!NOTE]
> 컨소시엄 작업을 시작하기 전에 클라이언트 애플리케이션의 초기 설정을 완료했는지 확인합니다.  

채널 및 컨소시엄에서 피어 조직을 추가하려면 지정된 순서로 다음 명령을 실행합니다. 

1.  피어 조직의 클라이언트에서 Azure Storage에 대한 피어 조직의 MSP를 업로드합니다.

      ```bash
      ./azhlf msp export toAzureStorage -f  $AZURE_FILE_CONNECTION_STRING -o $PEER_ORG_NAME
      ```
2.  orderer 조직의 클라이언트에서 Azure Storage에 대한 피어 조직의 MSP를 업로드합니다. 그런 다음 명령을 실행하여 채널 및 컨소시엄에서 피어 조직을 추가합니다.

      ```bash
      ./azhlf msp import fromAzureStorage -o $PEER_ORG_NAME -f $AZURE_FILE_CONNECTION_STRING
      ./azhlf channel join -c  $CHANNEL_NAME -o $ORDERER_ORG_NAME  -u $ORDERER_ADMIN_IDENTITY -p $PEER_ORG_NAME
      ./azhlf consortium join -o $ORDERER_ORG_NAME  -u $ORDERER_ADMIN_IDENTITY -p $PEER_ORG_NAME
      ```

3.  orderer 조직의 클라이언트에서 이 연결 프로필을 사용하여 피어 조직이 orderer 노드에 연결할 수 있도록 Azure Storage에 orderer의 연결 프로필을 업로드합니다.

      ```bash
      ./azhlf connectionProfile  export toAzureStorage -o $ORDERER_ORG_NAME -f $AZURE_FILE_CONNECTION_STRING
      ```

4.  피어 조직의 클라이언트에서 Azure Storage의 orderer 연결 프로필을 다운로드합니다. 그런 다음 명령을 실행하여 채널에서 피어 노드를 추가합니다.

      ```bash
      ./azhlf connectionProfile  import fromAzureStorage -o $ORDERER_ORG_NAME -f $AZURE_FILE_CONNECTION_STRING
      ./azhlf channel joinPeerNodes -o $PEER_ORG_NAME  -u $PEER_ADMIN_IDENTITY -c $CHANNEL_NAME --ordererOrg $ORDERER_ORG_NAME
      ```

마찬가지로, 채널에 피어 조직을 더 추가하려면 필요한 피어 조직에 따라 피어 환경 변수를 업데이트하고 1~4단계를 다시 실행합니다.

### <a name="set-anchor-peers"></a>앵커 피어 설정

피어 조직의 클라이언트에서 명령을 실행하여 지정된 채널의 피어 조직에 대한 앵커 피어를 설정합니다.

>[!NOTE]
> 이 명령을 실행하기 전에 컨소시엄 관리 명령을 사용하여 채널에 피어 조직을 추가해야 합니다.

```bash
./azhlf channel setAnchorPeers -c $CHANNEL_NAME -p <anchorPeersList> -o $PEER_ORG_NAME -u $PEER_ADMIN_IDENTITY --ordererOrg $ORDERER_ORG_NAME
```

`<anchorPeersList>`는 앵커 피어로 설정할 피어 노드의 공백으로 구분된 목록입니다. 예를 들면 다음과 같습니다.

  - peer1 노드만 앵커 피어로 설정하려는 경우 `<anchorPeersList>`를 `"peer1"`으로 설정합니다.
  - peer1과 peer3 노드 모두를 앵커 피어로 설정하려는 경우 `<anchorPeersList>`를 `"peer1" "peer3"`로 설정합니다.

## <a name="chaincode-management-commands"></a>chaincode 관리 명령

>[!NOTE]
> chaincode 작업을 시작하기 전에 클라이언트 응용 프로그램의 초기 설정을 완료했는지 확인합니다.  

### <a name="set-the-chaincode-specific-environment-variables"></a>chaincode 환경 변수 설정

```bash
# Peer organization name where the chaincode operation will be performed
ORGNAME=<PeerOrgName>
USER_IDENTITY="admin.$ORGNAME"  
# If you are using chaincode_example02 then set CC_NAME=â€œchaincode_example02â€
CC_NAME=<chaincodeName>  
# If you are using chaincode_example02 then set CC_VERSION=â€œ1â€ for validation
CC_VERSION=<chaincodeVersion>
# Language in which chaincode is written. Supported languages are 'node', 'golang', and 'java'  
# Default value is 'golang'  
CC_LANG=<chaincodeLanguage>  
# CC_PATH contains the path where your chaincode is placed. This is the absolute path to the chaincode project root directory.
# If you are using chaincode_example02 to validate then CC_PATH=â€œ/home/<username>/azhlfTool/samples/chaincode/src/chaincode_example02/goâ€
CC_PATH=<chaincodePath>  
# Channel on which chaincode will be instantiated/invoked/queried  
CHANNEL_NAME=<channelName>  
```

### <a name="install-chaincode"></a>chaincode 설치  

다음 명령을 실행하여 피어 조직에 chaincode를 설치합니다.  

```bash
./azhlf chaincode install -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -p $CC_PATH -l $CC_LANG -v $CC_VERSION  

```
이 명령은 `ORGNAME` 환경 변수에 설정된 피어 조직의 모든 피어 노드에 chaincode를 설치합니다. 채널에 두 개 이상의 피어 조직이 있고 모든 조직에 chaincode를 설치하려면 각 피어 조직에 대해 이 명령을 별도로 실행합니다.  

다음 단계를 수행합니다.  

1.  `ORGNAME`과 `USER_IDENTITY`를 `peerOrg1`에 따라 설정하고 `./azhlf chaincode install` 명령을 실행합니다.  
2.  `ORGNAME`과 `USER_IDENTITY`를 `peerOrg2`에 따라 설정하고 `./azhlf chaincode install` 명령을 실행합니다.  

### <a name="instantiate-chaincode"></a>chaincode 인스턴스화  

피어 클라이언트 애플리케이션에서 다음 명령을 실행하여 채널에서 chaincode를 인스턴스화합니다.  

```bash
./azhlf chaincode instantiate -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -v $CC_VERSION -c $CHANNEL_NAME -f <instantiateFunc> --args <instantiateFuncArgs>
```

각각 `<instantiateFunc>`과 `<instantiateFuncArgs>`에서 인스턴스화 함수 이름과 공백으로 구분된 인수 목록을 전달합니다. 예를 들어 chaincode_example02.go chaincode를 인스턴스화하려면 `<instantiateFunc>`를 `init`으로, `<instantiateFuncArgs>`를 `"a" "2000" "b" "1000"`으로 설정합니다.

`--collections-config` 플래그를 사용하여 컬렉션의 구성 JSON 파일을 전달할 수도 있습니다. 또는 전용 트랜잭션에 사용되는 chaincode를 인스턴스화하는 동안 `-t` 플래그를 사용하여 임시 인수를 설정합니다.

예를 들면 다음과 같습니다.

```bash
./azhlf chaincode instantiate -c $CHANNEL_NAME -n $CC_NAME -v $CC_VERSION -o $ORGNAME -u $USER_IDENTITY --collections-config <collectionsConfigJSONFilePath>
./azhlf chaincode instantiate -c $CHANNEL_NAME -n $CC_NAME -v $CC_VERSION -o $ORGNAME -u $USER_IDENTITY --collections-config <collectionsConfigJSONFilePath> -t <transientArgs>
```

`<collectionConfigJSONFilePath>`파트는 프라이빗 데이터 chaincode의 인스턴스화에 대해 정의된 컬렉션을 포함하는 JSON 파일 경로입니다. 다음 경로에서 *azhlfTool* 디렉터리에 관한 샘플 컬렉션의 구성 JSON 파일을 찾을 수 있습니다. `./samples/chaincode/src/private_marbles/collections_config.json`
문자열 형식에서 유효한 JSON 파일로 `<transientArgs>`를 전달합니다. 특수 문자를 피합니다. `'{\\\"asset\":{\\\"name\\\":\\\"asset1\\\",\\\"price\\\":99}}'`

> [!NOTE]
> 채널의 아무 피어 조직에서 명령을 한 번 실행합니다. 트랜잭션이 성공적으로 orderer에 전송된 후 orderer는 이 트랜잭션을 채널의 모든 피어 조직에 배포합니다. 그런 다음 chaincode는 채널에 있는 모든 피어 조직의 모든 피어 노드에서 인스턴스화됩니다.  

### <a name="invoke-chaincode"></a>chaincode 호출  

피어 조직의 클라이언트에서 다음 명령을 실행하여 chaincode 함수를 호출합니다.  

```bash
./azhlf chaincode invoke -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -c $CHANNEL_NAME -f <invokeFunc> -a <invokeFuncArgs>  
```

각각 `<invokeFunction>`과 `<invokeFuncArgs>`에서 호출 함수 이름과 공백으로 구분된 인수 목록을 전달합니다. 호출 작업을 수행하는 Chaincode_example02.go Chaincode 예제를 계속 진행하여, `<invokeFunction>`을 `invoke`로, `<invokeFuncArgs>`를 `"a" "b" "10"`로 설정합니다.  

>[!NOTE]
> 채널의 아무 피어 조직에서 명령을 한 번 실행합니다. 트랜잭션이 성공적으로 orderer에 전송된 후 orderer는 이 트랜잭션을 채널의 모든 피어 조직에 배포합니다. 그러면 월드 스테이트가 채널에 있는 모든 피어 조직의 모든 피어 노드에서 업데이트됩니다.  


### <a name="query-chaincode"></a>chaincode 쿼리  

다음 명령을 실행하여 chaincode를 쿼리합니다.  

```bash
./azhlf chaincode query -o $ORGNAME -p <endorsingPeers> -u $USER_IDENTITY -n $CC_NAME -c $CHANNEL_NAME -f <queryFunction> -a <queryFuncArgs> 
```
보증 피어는 chaincode가 설치되고 트랜잭션을 실행하기 위해 호출되는 피어입니다. 현재 피어 조직에서 피어 노드 이름을 포함하도록 `<endorsingPeers>`를 설정해야 합니다. 지정된 chaincode 및 채널 조합에 대한 보증 피어를 공백으로 구분하여 나열합니다. 예를 들면 `-p "peer1" "peer3"`와 같습니다.

*AzhlfTool* 를 사용하여 chaincode를 설치하는 경우 피어 노드 이름을 보증 피어 인수에 값으로 전달합니다. chaincode는 해당 조직의 모든 피어 노드에 설치됩니다. 

각각 `<queryFunction>`과 `<queryFuncArgs>`에서 쿼리 함수 이름과 공백으로 구분된 인수 목록을 전달합니다. chaincode_example02.go chaincode를 참조하여 월드 스테이트의 “a”값을 쿼리하기 위해 `<queryFunction>`를 `query`로, `<queryArgs>`를 `"a"`로 설정합니다.  

## <a name="troubleshoot"></a>문제 해결

### <a name="find-deployed-version"></a>배포된 버전 찾기

다음 명령을 실행하여 템플릿 배포 버전을 찾습니다. 템플릿이 배포된 리소스 그룹에 따라 환경 변수를 설정합니다.

```bash
SWITCH_TO_AKS_CLUSTER $AKS_CLUSTER_RESOURCE_GROUP $AKS_CLUSTER_NAME $AKS_CLUSTER_SUBSCRIPTION
kubectl describe pod fabric-tools -n tools | grep "Image:" | cut -d ":" -f 3
```

### <a name="patch-previous-version"></a>이전 버전 패치

V 3.0.0 이전 템플릿 버전 배포에서 chaincode를 실행하는 데 문제가 있는 경우 아래 단계에 따라 수정하여 피어 노드를 패치합니다.

피어 배포 스크립트 다운로드

```bash
curl https://raw.githubusercontent.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/master/scripts/patchPeerDeployment.sh -o patchPeerDeployment.sh; chmod 777 patchPeerDeployment.sh
```

매개 변수를 대체하는 피어의 다음 명령을 사용하여 스크립트를 실행합니다.

```bash
source patchPeerDeployment.sh <peerOrgSubscription> <peerOrgResourceGroup> <peerOrgAKSClusterName>
```

모든 피어 노드가 패치될 때까지 기다립니다. 다음 명령을 사용하여 셸의 다른 인스턴스에서 항상 피어 노드의 상태를 확인할 수 있습니다.

```bash
kubectl get pods -n hlf
```

## <a name="support-and-feedback"></a>지원 및 피드백

Azure Blockchain 뉴스의 경우 [Azure Blockchain 블로그](https://azure.microsoft.com/blog/topics/blockchain/)를 방문하여 Azure Blockchain 엔지니어링 팀의 블록체인 서비스 제공 및 정보를 최신 상태로 유지하세요.

제품 피드백을 제공하거나 새로운 기능을 요청하려면 [블록체인에 대한 Azure 피드백 포럼](https://aka.ms/blockchainuservoice)을 통해 아이디어를 게시하거나 투표하세요.

### <a name="community-support"></a>커뮤니티 지원

Microsoft 엔지니어 및 Azure Blockchain 커뮤니티 전문가와 소통하세요.

- [Microsoft Q&A 페이지](/answers/topics/azure-blockchain-workbench.html) 
   
  Azure Blockchain Workbench에 대한 엔지니어링 지원은 배포 문제로 제한됩니다.
- [Microsoft 기술 커뮤니티](https://techcommunity.microsoft.com/t5/Blockchain/bd-p/AzureBlockchain)
- [스택 오버플로](https://stackoverflow.com/questions/tagged/azure-blockchain-workbench)
