---
title: Azure Kubernetes 서비스(AKS)의 하이퍼레저 패브릭 컨소시엄
description: Azure Kubernetes 서비스에서 하이퍼레저 패브릭 컨소시엄 네트워크를 배포하고 구성하는 방법
ms.date: 01/08/2020
ms.topic: article
ms.reviewer: v-umha
ms.openlocfilehash: 2312c002e5c2e0b813f8acbdc3e3bff597f204d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79476443"
---
# <a name="hyperledger-fabric-consortium-on-azure-kubernetes-service-aks"></a>Azure Kubernetes 서비스(AKS)의 하이퍼레저 패브릭 컨소시엄

AKS(Azure Kubernetes Service) 템플릿의 하이퍼레저 패브릭(HLF)을 사용하여 Azure에서 하이퍼레저 패브릭 컨소시엄 네트워크를 배포하고 구성할 수 있습니다.

이 문서를 읽고 나면:

- 하이퍼레저 패브릭과 하이퍼레저 패브릭 블록체인 네트워크의 구성 요소를 구성하는 다양한 구성 요소에 대한 실무 지식을 얻으시면 됩니다.
- 프로덕션 시나리오에 맞게 Azure Kubernetes 서비스에 하이퍼레저 패브릭 컨소시엄을 배포하고 구성하는 방법을 알아봅니다.

## <a name="hyperledger-fabric-consortium-architecture"></a>하이퍼레저 패브릭 컨소시엄 아키텍처

Azure에서 Hyperledger Fabric 네트워크를 구축하려면 피어 노드를 사용하여 주문 서비스 및 조직을 배포해야 합니다. 템플릿 배포의 일부로 작성된 다양한 기본 구성 요소는 다음과 같습니다.

- **주문자 노드**: 원장의 트랜잭션 순서를 담당하는 노드입니다. 다른 노드와 함께 정렬된 노드는 하이퍼레저 패브릭 네트워크의 주문 서비스를 형성합니다.

- **피어 노드**: 주로 원장 및 스마트 계약을 호스팅하는 노드로 네트워크의 이러한 기본 요소입니다.

- **패브릭 CA**: 패브릭 CA는 하이퍼레저 패브릭에 대한 인증 기관(CA)입니다. Fabric CA를 사용하면 인증서 기관을 호스팅하는 서버 프로세스를 초기화하고 시작할 수 있습니다. ID 및 인증서를 관리할 수 있습니다. 템플릿의 일부로 배포된 각 AKS 클러스터에는 기본적으로 Fabric CA 포드가 있습니다.

- **CouchDB 또는 LevelDB**: 피어 노드에 대한 월드 스테이트 데이터베이스는 LevelDB 또는 CouchDB에 저장할 수 있습니다. LevelDB는 피어 노드에 포함된 기본 상태 데이터베이스이며 간단한 키-값 쌍으로 체인코드 데이터를 저장하며 키, 키 범위 및 복합 키 쿼리만 지원합니다. CouchDB는 체인 코드 데이터 값이 JSON으로 모델링될 때 풍부한 쿼리를 지원하는 선택적 대체 상태 데이터베이스입니다.

배포 템플릿은 구독에서 다양한 Azure 리소스를 스핀업합니다. 배포된 다양한 Azure 리소스는 다음과 같습니다.

- **AKS 클러스터**: 고객이 제공한 입력 매개 변수에 따라 구성된 Azure Kubernetes 클러스터입니다. AKS 클러스터에는 하이퍼레저 패브릭 네트워크 구성 요소를 실행하도록 구성된 다양한 포드가 있습니다. 생성된 다른 포드는 다음과 같습니다.

  - **패브릭 도구**: 패브릭 도구는 하이퍼 레저 패브릭 구성 요소를 구성할 책임이 있습니다.
  - **오더/피어 포드**: HLF 네트워크의 노드입니다.
  - **프록시**: 클라이언트 응용 프로그램이 AKS 클러스터와 인터페이스할 수 있는 NGNIX 프록시 포드입니다.
  - **패브릭 CA**: 패브릭 CA를 실행하는 포드입니다.
- **PostgreSQL**: 패브릭 CA ID를 유지하기 위해 PostgreSQL 의 인스턴스가 배포됩니다.

- **Azure 키 자격 증명 모음**: 패브릭 CA 자격 증명 및 고객이 제공하는 루트 인증서를 저장하기 위해 키 자격 증명 인스턴스가 배포되며 템플릿 배포 재시도의 경우 템플릿의 메커니즘을 처리하는 것입니다.
- **Azure 관리 디스크**: Azure Managed 디스크는 원장 및 피어 노드 월드 상태 데이터베이스에 대한 영구 저장소용입니다.
- **공용 IP**: 클러스터와 인터페이싱하기 위해 배포된 AKS 클러스터의 공용 IP 끝점입니다.

## <a name="hyperledger-fabric-blockchain-network-setup"></a>하이퍼레저 패브릭 블록체인 네트워크 설정

시작하려면 여러 가상 머신과 표준 스토리지 계정의 배포를 지원할 수 있는 Azure 구독이 필요합니다. Azure 구독이 없는 경우 [Azure 체험 계정](https://azure.microsoft.com/free/)을 만들 수 있습니다.

다음 단계를 사용하여 하이퍼레저 패브릭 블록 체인 네트워크 설정 :

- [주문자/피어 조직 배포](#deploy-the-ordererpeer-organization)
- [컨소시엄 구축](#build-the-consortium)
- [네이티브 HLF 작업 실행](#run-native-hlf-operations)

## <a name="deploy-the-ordererpeer-organization"></a>주문자/피어 조직 배포

HLF 네트워크 구성 요소 배포를 시작하려면 [Azure 포털로](https://portal.azure.com)이동합니다. **Azure Kubernetes 서비스에서 하이퍼레저 패브릭을** **검색하기 > > 리소스 만들기를** 선택합니다.

1. 템플릿 배포를 시작하려면 **만들기를** 선택합니다. **Azure Kubernetes 서비스** 에서 하이퍼레저 패브릭 만들기 표시합니다.

    ![Azure Kubernetes 서비스 템플릿의 하이퍼레저 패브릭](./media/hyperledger-fabric-consortium-azure-kubernetes-service/hyperledger-fabric-aks.png)

2. 기본 페이지에서 프로젝트 세부 정보를 **입력합니다.**

    ![Azure Kubernetes 서비스 템플릿의 하이퍼레저 패브릭](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-basics.png)

3. 다음 세부 정보를 입력합니다.
    - **구독**: HLF 네트워크 구성 요소를 배포할 구독 이름을 선택합니다.
    - **리소스 그룹**: 새 리소스 그룹을 만들거나 기존 빈 리소스 그룹을 선택하면 리소스 그룹은 템플릿의 일부로 배포된 모든 리소스를 보유합니다.
    - **지역**: HLF 구성 요소에 대해 Azure Kubernetes 클러스터를 배포할 Azure 지역을 선택합니다. 템플릿은 AKS를 사용할 수 있는 모든 지역에서 사용할 수 있으며 구독이 VM(가상 컴퓨터) 할당량 제한에 해당하지 않는 지역을 선택하도록 합니다.
    - **리소스 접두사**: 배포된 리소스의 이름을 지정하는 접두사입니다. 리소스 접두사는 길이가 6자 미만이어야 하며 문자 조합에는 숫자와 문자가 모두 포함되어야 합니다.
4. **배포할** HLF 네트워크 구성 요소를 정의하려면 패브릭 설정 탭을 선택합니다.

    ![Azure Kubernetes 서비스 템플릿의 하이퍼레저 패브릭](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-settings.png)

5. 다음 세부 정보를 입력합니다.
    - **조직 이름**: 다양한 데이터 평면 작업에 필요한 Fabric 조직의 이름입니다. 조직 이름은 배포당 고유해야 합니다. 
    - **패브릭 네트워크 구성 요소**: 설정하려는 블록 체인 네트워크 구성 요소를 기반으로 주문 서비스 또는 피어 노드중 하나를 선택합니다.
    - **노드 수** - 다음은 두 가지 유형의 노드입니다.
        - 주문 서비스 - 네트워크에 내결함성을 제공하는 노드 수를 선택합니다. 지원되는 순서 노드 수는 3,5및 7입니다.
        - 피어 노드 - 요구 사항에 따라 1-10개의 노드를 선택할 수 있습니다.
    - **피어 노드 월드 상태 데이터베이스**: LevelDB와 CoucbDB 중에서 선택합니다. 이 필드는 사용자가 Fabric 네트워크 구성 요소 드롭다운에서 피어 노드를 선택할 때 표시됩니다.
    - **패브릭 사용자 이름**: 패브릭 CA 인증에 사용되는 사용자 이름을 입력합니다.
    - **패브릭 CA 암호**: 패브릭 CA 인증에 대한 암호를 입력합니다.
    - **암호 확인**: 패브릭 CA 암호를 확인합니다.
    - **인증서**: 패브릭 CA를 초기화하기 위해 자신의 루트 인증서를 사용하려는 경우 패브릭 CA 옵션에 대한 루트 인증서 업로드를 선택하고 기본적으로 Fabric CA는 자체 서명 된 인증서를 만듭니다.
    - **루트 인증서**: 패브릭 CA를 초기화해야 하는 루트 인증서(공개 키)를 업로드합니다. .pem 형식의 인증서가 지원되며 인증서는 UTC 표준 시간대에서 유효해야 합니다.
    - **루트 인증서 개인 키**: 루트 인증서의 개인 키를 업로드합니다. 공개 키와 개인 키가 모두 결합된 .pem 인증서가 있는 경우 여기에 업로드할 수도 있습니다.


6. **AKS 클러스터 설정** 탭을 선택하여 패브릭 네트워크 구성 요소를 설정하는 기본 인프라인 Azure Kubernetes 클러스터 구성을 정의합니다.

    ![Azure Kubernetes 서비스 템플릿의 하이퍼레저 패브릭](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-aks-cluster-settings-1.png)

7. 다음 세부 정보를 입력합니다.
    - **Kubernetes 클러스터 이름**: 생성된 AKS 클러스터의 이름입니다. 이 필드는 제공된 리소스 접두사에 따라 미리 채워지므로 필요한 경우 변경할 수 있습니다.
    - **Kubernetes 버전**: 클러스터에 배포될 Kubernetes 버전입니다. **기본** 탭에서 선택한 지역에 따라 사용 가능한 지원되는 버전이 변경될 수 있습니다.
    - **DNS 접두사**: AKS 클러스터에 대한 도메인 이름 시스템(DNS) 이름 접두사입니다. 클러스터를 만든 후 컨테이너를 관리할 때 DNS를 사용하여 Kubernetes API에 연결합니다.
    - **노드 크기**: Kubernetes 노드의 크기로 Azure에서 사용할 수 있는 VM Stock 유지 장치(SCO) 목록에서 선택할 수 있습니다. 최적의 성능을 위해 표준 DS3 v2를 권장합니다.
    - **노드 수**: 클러스터에 배포할 Kubernetes 노드 수입니다. 이 노드 수를 Fabric 설정에 지정된 HLF 노드 수보다 적어도 같거나 더 많이 유지하는 것이 좋습니다.
    - **서비스 주체 클라이언트 ID**: 기존 서비스 주체의 클라이언트 ID를 입력하거나 AKS 인증에 필요한 새 ID를 만듭니다. 서비스 [주체를 만드는](https://docs.microsoft.com/powershell/azure/create-azure-service-principal-azureps?view=azps-3.2.0#create-a-service-principal)단계를 참조하십시오.
    - **서비스 주체 클라이언트 보안**: 서비스 주체 클라이언트 ID에 제공된 서비스 주체의 클라이언트 비밀을 입력합니다.
    - **클라이언트 보안 확인**: 서비스 주 서버 클라이언트 보안에 제공된 클라이언트 보안 을 확인합니다.
    - **컨테이너 모니터링 사용**: AKS 로그가 지정된 로그 분석 작업 영역으로 푸시할 수 있도록 AKS 모니터링을 사용하도록 선택합니다.
    - **로그 분석 작업 영역**: 로그 분석 작업 영역은 모니터링이 활성화된 경우 생성되는 기본 작업 영역으로 채워집니다.

8. 위의 모든 세부 정보를 제공한 후 **검토 및 만들기** 탭을 선택합니다. 검토 및 만들기는 제공한 값에 대한 유효성 검사를 트리거합니다.
9. 유효성 검사가 통과하면 **을 만들수**있습니다.
배포는 일반적으로 10-12분 정도 소요되며 지정된 AKS 노드의 크기와 수에 따라 달라질 수 있습니다.
10. 성공적으로 배포한 후에는 오른쪽 상단 모서리에 있는 Azure 알림을 통해 알림을 받게 됩니다.
11. 템플릿 배포의 일부로 만든 모든 리소스를 확인하려면 **리소스 그룹으로 이동을** 선택합니다. 모든 리소스 이름은 **기본** 설정에 제공된 접두사로 시작합니다.

## <a name="build-the-consortium"></a>컨소시엄 구축

주문 서비스 및 피어 노드를 배포하는 블록 체인 컨소시엄 포스트를 구축하려면 다음 단계를 순서대로 수행해야합니다. 컨소시엄 설정, 채널 생성 및 체인 코드 설치에 도움이 되는 네트워크 스크립트(byn.sh)를 **빌드합니다.**

> [!NOTE]
> 제공된 네트워크 구축(byn) 스크립트는 데모/개발 시나리오에 엄격하게 사용됩니다. 프로덕션 등급 설정의 경우 기본 HLF API를 사용하는 것이 좋습니다.

byn 스크립트를 실행하는 모든 명령은 AZURE Bash 명령줄 인터페이스(CLI)를 통해 실행할 수 있습니다. 을 통해 Azure 셸 웹 버전에 로그인할 수 있습니다. ![Azure Kubernetes 서비스 템플릿의 하이퍼레저 패브릭](./media/hyperledger-fabric-consortium-azure-kubernetes-service/arrow.png) Azure 포털의 오른쪽 상단 모서리에 있는 옵션입니다. 명령 프롬프트에서 bash를 입력하고 입력하여 BASH CLI로 전환합니다.

자세한 내용은 [Azure 셸을](https://docs.microsoft.com/azure/cloud-shell/overview) 참조하십시오.

![Azure Kubernetes 서비스 템플릿의 하이퍼레저 패브릭](./media/hyperledger-fabric-consortium-azure-kubernetes-service/hyperledger-powershell.png)


byn.sh 및 패브릭 admin.yaml 파일을 다운로드합니다.

```bash-interactive
curl https://raw.githubusercontent.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/master/consortiumScripts/byn.sh -o byn.sh; chmod 777 byn.sh
curl https://raw.githubusercontent.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/master/consortiumScripts/fabric-admin.yaml -o fabric-admin.yaml
```
**Azure CLI Bash 셸에서 환경 변수 아래에 설정:**

채널 정보 및 주문자 조직 정보 설정

```bash
SWITCH_TO_AKS_CLUSTER() { az aks get-credentials --resource-group $1 --name $2 --subscription $3; }
ORDERER_AKS_SUBSCRIPTION=<ordererAKSClusterSubscriptionID>
ORDERER_AKS_RESOURCE_GROUP=<ordererAKSClusterResourceGroup>
ORDERER_AKS_NAME=<ordererAKSClusterName>
ORDERER_DNS_ZONE=$(az aks show --resource-group $ORDERER_AKS_RESOURCE_GROUP --name $ORDERER_AKS_NAME --subscription $ORDERER_AKS_SUBSCRIPTION -o json | jq .addonProfiles.httpApplicationRouting.config.HTTPApplicationRoutingZoneName | tr -d '"')
ORDERER_END_POINT="orderer1.$ORDERER_DNS_ZONE:443"
CHANNEL_NAME=<channelName>
```
피어 조직 정보 설정

```bash
PEER_AKS_RESOURCE_GROUP=<peerAKSClusterResourceGroup>
PEER_AKS_NAME=<peerAKSClusterName>
PEER_AKS_SUBSCRIPTION=<peerAKSClusterSubscriptionID>
#Peer organization name is case-sensitive. Specify exactly the same name, which was provided while creating the Peer AKS Cluster.
PEER_ORG_NAME=<peerOrganizationName>
```

하나의 Azure File 공유를 만들어 피어 및 순서 자 조직 간에 다양한 공용 인증서를 공유합니다.

```bash
STORAGE_SUBSCRIPTION=<subscriptionId>
STORAGE_RESOURCE_GROUP=<azureFileShareResourceGroup>
STORAGE_ACCOUNT=<azureStorageAccountName>
STORAGE_LOCATION=<azureStorageAccountLocation>
STORAGE_FILE_SHARE=<azureFileShareName>

az account set --subscription $STORAGE_SUBSCRIPTION
az group create -l $STORAGE_LOCATION -n $STORAGE_RESOURCE_GROUP
az storage account create -n $STORAGE_ACCOUNT -g  $STORAGE_RESOURCE_GROUP -l $STORAGE_LOCATION --sku Standard_LRS
STORAGE_KEY=$(az storage account keys list --resource-group $STORAGE_RESOURCE_GROUP  --account-name $STORAGE_ACCOUNT --query "[0].value" | tr -d '"')
az storage share create  --account-name $STORAGE_ACCOUNT  --account-key $STORAGE_KEY  --name $STORAGE_FILE_SHARE
SAS_TOKEN=$(az storage account generate-sas --account-key $STORAGE_KEY --account-name $STORAGE_ACCOUNT --expiry `date -u -d "1 day" '+%Y-%m-%dT%H:%MZ'` --https-only --permissions lruwd --resource-types sco --services f | tr -d '"')
AZURE_FILE_CONNECTION_STRING="https://$STORAGE_ACCOUNT.file.core.windows.net/$STORAGE_FILE_SHARE?$SAS_TOKEN"
```
**채널 관리 명령**

주문자 조직 AKS 클러스터로 이동하여 명령을 내하여 새 채널을 만듭니다.

```bash
SWITCH_TO_AKS_CLUSTER $ORDERER_AKS_RESOURCE_GROUP $ORDERER_AKS_NAME $ORDERER_AKS_SUBSCRIPTION
./byn.sh createChannel "$CHANNEL_NAME"
```

**컨소시엄 관리 명령**

지정된 순서대로 아래 명령을 실행하여 채널 및 컨소시엄에 피어 조직을 추가합니다.

1. 피어 조직 AKS 클러스터로 이동하여 Azure 파일 저장소에 MSP(멤버 서비스 제공)를 업로드합니다.

    ```bash
    SWITCH_TO_AKS_CLUSTER $PEER_AKS_RESOURCE_GROUP $PEER_AKS_NAME $PEER_AKS_SUBSCRIPTION
    ./byn.sh uploadOrgMSP "$AZURE_FILE_CONNECTION_STRING"
    ```

2. 주문자 조직 AKS 클러스터로 이동하여 채널 및 컨소시엄에 피어 조직을 추가합니다.

    ```bash
    SWITCH_TO_AKS_CLUSTER $ORDERER_AKS_RESOURCE_GROUP $ORDERER_AKS_NAME $ORDERER_AKS_SUBSCRIPTION
    #add peer in consortium
    ./byn.sh addPeerInConsortium "$PEER_ORG_NAME" "$AZURE_FILE_CONNECTION_STRING"
    #add peer in channel
    ./byn.sh addPeerInChannel "$PEER_ORG_NAME" "$CHANNEL_NAME" "$AZURE_FILE_CONNECTION_STRING"
    ```

3. 피어 조직으로 돌아가서 명령을 내하여 채널에서 피어 노드를 조인합니다.

    ```bash
    SWITCH_TO_AKS_CLUSTER $PEER_AKS_RESOURCE_GROUP $PEER_AKS_NAME $PEER_AKS_SUBSCRIPTION
    ./byn.sh joinNodesInChannel "$CHANNEL_NAME" "$ORDERER_END_POINT" "$AZURE_FILE_CONNECTION_STRING"
    ```

마찬가지로 채널에 피어 조직을 더 추가하려면 필요한 피어 조직에 따라 피어 AKS 환경 변수를 업데이트하고 1~3단계를 실행합니다.

**체인코드 관리 명령**

아래 명령을 실행하여 체인코드 관련 작업을 수행합니다. 이러한 명령은 데모 체인 코드에서 모든 작업을 수행합니다. 이 데모 체인 코드에는 "a" 및 "b"라는 두 가지 변수가 있습니다. 체인 코드를 인스턴스화할 때 "a"는 1000으로 초기화되고 "b"는 2000으로 초기화됩니다. 체인 코드의 각 호출에서 10단위는 "a"에서 "b"로 전송됩니다. 체인 코드에 대한 쿼리 작업은 "a" 변수의 세계 상태를 표시합니다.

피어 조직 AKS 클러스터에서 실행된 다음 명령을 실행합니다.

```bash
# switch to peer organization AKS cluster. Skip this command if already connected to the required Peer AKS Cluster
SWITCH_TO_AKS_CLUSTER $PEER_AKS_RESOURCE_GROUP $PEER_AKS_NAME $PEER_AKS_SUBSCRIPTION
```
**체인 코드 작업 명령**

```bash
PEER_NODE_NAME="peer<peer#>"
./byn.sh installDemoChaincode "$PEER_NODE_NAME"
./byn.sh instantiateDemoChaincode "$PEER_NODE_NAME" "$CHANNEL_NAME" "$ORDERER_END_POINT" "$AZURE_FILE_CONNECTION_STRING"
./byn.sh invokeDemoChaincode "$PEER_NODE_NAME" "$CHANNEL_NAME" "$ORDERER_END_POINT" "$AZURE_FILE_CONNECTION_STRING"
./byn.sh queryDemoChaincode "$PEER_NODE_NAME" "$CHANNEL_NAME"
```

## <a name="run-native-hlf-operations"></a>네이티브 HLF 작업 실행

고객이 AKS의 HLF 네트워크에서 Hyperledger 네이티브 명령을 실행하도록 돕기 위해 샘플 응용 프로그램은 패브릭 NodeJS SDK를 사용하여 HLF 작업을 수행하는 데 제공됩니다. 명령은 새 사용자 ID를 만들고 사용자 고유의 체인 코드를 설치하기 위해 제공됩니다.

### <a name="before-you-begin"></a>시작하기 전에

응용 프로그램의 초기 설정에 대한 아래 명령을 따르십시오.

- 응용 프로그램 파일 다운로드
- 연결 프로필 및 관리자 프로필 생성
- 관리자 사용자 ID 가져오기

초기 설정을 완료한 후 SDK를 사용하여 다음 작업을 수행할 수 있습니다.

- 사용자 ID 생성
- 체인코드 작업

위에서 언급한 명령은 Azure Cloud Shell에서 실행할 수 있습니다.

### <a name="download-application-files"></a>응용 프로그램 파일 다운로드

응용 프로그램을 실행하기위한 첫 번째 설정은 폴더에있는 모든 응용 프로그램 파일을 다운로드하는 것입니다.

**응용 프로그램 폴더를 만들고 폴더에 입력**:

```bash
mkdir app
cd app
```
필요한 모든 파일및 패키지를 다운로드하려면 아래 명령을 실행하십시오.

```bash-interactive
curl https://raw.githubusercontent.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/master/application/setup.sh | bash
```
이 명령은 모든 패키지를 로드하는 데 시간이 걸립니다. 명령을 성공적으로 실행하면 현재 디렉터리에서 폴더를 `node_modules` 볼 수 있습니다. 필요한 모든 패키지가 폴더에 `node_modules` 로드됩니다.

### <a name="generate-connection-profile-and-admin-profile"></a>연결 프로필 및 관리자 프로필 생성

폴더 내부에 디렉터리 만들기 `profile` `app`

```bash
cd app
mkdir ./profile
```
Azure 클라우드 셸에서 이러한 환경 변수 설정

```bash
# Organization name whose connection profile is to be generated
ORGNAME=<orgname>
# Organization AKS cluster resource group
AKS_RESOURCE_GROUP=<resourceGroup>
```

아래 명령을 실행하여 조직의 연결 프로필 및 관리자 프로필을 생성합니다.

```bash
./getConnector.sh $AKS_RESOURCE_GROUP | sed -e "s/{action}/gateway/g"| xargs curl > ./profile/$ORGNAME-ccp.json
./getConnector.sh $AKS_RESOURCE_GROUP | sed -e "s/{action}/admin/g"| xargs curl > ./profile/$ORGNAME-admin.json
```

프로필 폴더에 이름과 `profile` `<orgname>-ccp.json` `<orgname>-admin.json` 각각 연결 프로필 및 조직의 관리자를 만듭니다.

마찬가지로 각 주문자 및 피어 조직에 대한 연결 프로필 및 관리자 프로필도 생성됩니다.


### <a name="import-admin-user-identity"></a>관리자 사용자 ID 가져오기

마지막 단계는 지갑에서 조직의 관리자 사용자 ID를 가져오는 것입니다.

```bash
npm run importAdmin -- -o <orgName>

```
위의 명령은 importAdmin.js를 실행하여 관리자 사용자 ID를 지갑으로 가져옵니다. 스크립트는 관리자 프로필에서 `<orgname>-admin.json` 관리자 ID를 읽고 HLF 작업을 실행하기 위해 지갑에서 가져오십니다.

스크립트는 파일 시스템 지갑을 사용하여 ID를 저장합니다. 연결 프로필의 ".wallet" 필드에 지정된 경로에 따라 지갑이 만들어집니다. 기본적으로 ".wallet" 필드는 ID를 `<orgname>`저장하기 위해 현재 `<orgname>` 디렉터리에서 명명된 폴더가 생성됨을 의미합니다. 다른 경로에서 지갑을 만들려면 등록 관리자 사용자 및 기타 HLF 작업을 실행하기 전에 연결 프로필에서 ".wallet" 필드를 수정합니다.

마찬가지로 각 조직에 대한 관리자 사용자 ID를 가져옵니다.

명령 도움말을 참조하여 명령에서 전달된 인수에 대한 자세한 내용을 참조합니다.

```bash
npm run importAdmin -- -h

```

### <a name="user-identity-generation"></a>사용자 ID 생성

주어진 순서대로 아래 명령을 실행하여 HLF 조직에 대한 새 사용자 ID를 생성합니다.

> [!NOTE]
> 사용자 ID 생성 단계를 시작하기 전에 응용 프로그램의 초기 설정이 수행되었는지 확인합니다.

Azure 클라우드 셸의 환경 변수 아래에 설정

```bash
# Organization name for which user identity is to be generated
ORGNAME=<orgname>
# Name of new user identity. Identity will be registered with the Fabric-CA using this name.
USER_IDENTITY=<username>

```

새 사용자 등록 및 등록

새 사용자를 등록하고 등록하려면 registerUser.js를 실행하는 아래 명령을 실행합니다. 생성된 사용자 ID를 지갑에 저장합니다.

```bash
npm run registerUser -- -o $ORGNAME -u $USER_IDENTITY

```

> [!NOTE]
> 관리자 사용자 ID는 새 사용자에 대한 레지스터 명령을 발급하는 데 사용됩니다. 따라서 이 명령을 실행하기 전에 관리자 사용자 ID를 지갑에 있어야 합니다. 그렇지 않으면 이 명령이 실패합니다.

명령에서 전달된 인수에 대한 자세한 내용은 명령 도움말을 참조합니다.

```bash
npm run registerUser -- -h

```

### <a name="chaincode-operations"></a>체인코드 작업


> [!NOTE]
> 체인 코드 작업을 시작하기 전에 응용 프로그램의 초기 설정이 완료되었는지 확인하십시오.

Azure Cloud 셸에서 체인코드 특정 환경 변수 아래에 설정합니다.

```bash
# peer organization name where chaincode is to be installed
ORGNAME=<orgName>
USER_IDENTITY="admin.$ORGNAME"
CC_NAME=<chaincodeName>
CC_VERSION=<chaincodeVersion>
# Language in which chaincode is written. Supported languages are 'node', 'golang' and 'java'
# Default value is 'golang'
CC_LANG=<chaincodeLanguage>
# CC_PATH contains the path where your chaincode is place. In case of go chaincode, this path is relative to 'GOPATH'.
# For example, if your chaincode is present at path '/opt/gopath/src/chaincode/chaincode.go'.
# Then, set GOPATH to '/opt/gopath' and CC_PATH to 'chaincode'
CC_PATH=<chaincodePath>
# 'GOPATH' environment variable. This needs to be set in case of go chaincode only.
export GOPATH=<goPath>
# Channel on which chaincode is to be instantiated/invoked/queried
CHANNEL=<channelName>

````

아래 체인코드 연산을 수행할 수 있습니다.

- 체인 코드 설치
- 체인 코드 인스턴스화
- 체인 코드 호출
- 쿼리 체인 코드

### <a name="install-chaincode"></a>체인 코드 설치

아래 명령을 실행하여 피어 조직에 체인 코드를 설치합니다.

```bash
npm run installCC -- -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -p $CC_PATH -l $CC_LANG -v $CC_VERSION

```
환경 변수에 설정된 조직의 모든 피어 노드에 `ORGNAME` 체인 코드를 설치합니다. 채널에 둘 이상의 피어 조직이 있고 모든 피어 코드에 체인 코드를 설치하려는 경우 각 피어 조직에 대해 명령을 별도로 실행합니다.

다음 단계를 따르십시오.

- 로 `ORGNAME` `<peerOrg1Name>` 설정하고 `installCC` 명령을 내보를 내보십습니다.
- 로 `ORGNAME` `<peerOrg2Name>` 설정하고 `installCC` 명령을 내보를 내보십습니다.

  각 피어 조직에 대해 실행합니다.

명령도움말을 참조하여 명령에서 전달된 인수에 대한 자세한 내용을 참조하십시오.

```bash
npm run installCC -- -h

```

### <a name="instantiate-chaincode"></a>체인 코드 인스턴스화

아래 명령을 실행하여 피어에서 체인 코드를 인스턴스화합니다.

```bash
npm run instantiateCC -- -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -p $CC_PATH -v $CC_VERSION -l $CC_LANG -c $CHANNEL -f <instantiateFunc> -a <instantiateFuncArgs>

```
인스턴스화 함수 이름과 쉼표가 분리된 인수 `<instantiateFunc>` 목록을 `<instantiateFuncArgs>` 각각 전달합니다. 예를 들어 [fabrcar 체인 코드에서](https://github.com/hyperledger/fabric-samples/blob/release/chaincode/fabcar/fabcar.go)문자열을 `<instantiateFunc>` `"Init"` 인스턴스화하고 `<instantiateFuncArgs>` 문자열을 `""`비우면 됩니다.

> [!NOTE]
> 채널의 한 피어 조직에서 한 번 명령을 실행합니다.
> 트랜잭션이 주문자에게 성공적으로 제출되면 주문자는 이 트랜잭션을 채널의 모든 피어 조직에 배포합니다. 따라서 체인 코드는 채널의 모든 피어 조직의 모든 피어 노드에서 인스턴스화됩니다.

명령에서 전달된 인수에 대한 자세한 내용은 명령 도움말을 참조합니다.

```bash
npm run instantiateCC -- -h

```

### <a name="invoke-chaincode"></a>체인 코드 호출

아래 명령을 실행하여 체인 코드 함수를 호출합니다.

```bash
npm run invokeCC -- -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -c $CHANNEL -f <invokeFunc> -a <invokeFuncArgs>

```
함수 이름과 쉼표가 분리된 인수 목록을 각각 `<invokeFunction>` `<invokeFuncArgs>` 전달합니다. fabcar 체인 코드 예제를 계속하여 initLedger 함수를 `"initLedger"` `<invokeFuncArgs>` 에 `""`및 로 설정하여 `<invokeFunction>` 호출합니다.

> [!NOTE]
> 채널의 한 피어 조직에서 한 번 명령을 실행합니다.
> 트랜잭션이 주문자에게 성공적으로 제출되면 주문자는 이 트랜잭션을 채널의 모든 피어 조직에 배포합니다. 따라서 채널의 모든 피어 조직의 모든 피어 노드에서 월드 상태가 업데이트됩니다.

명령에서 전달된 인수에 대한 자세한 내용은 명령 도움말을 참조합니다.

```bash
npm run invokeCC -- -h

```

### <a name="query-chaincode"></a>쿼리 체인 코드

아래 명령을 실행하여 체인 코드를 쿼리합니다.

```bash
npm run queryCC -- -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -c $CHANNEL -f <queryFunction> -a <queryFuncArgs>

```

쿼리 함수 이름과 쉼표가 분리된 인수 `<queryFunction>` `<queryFuncArgs>` 목록을 각각 전달합니다. 다시 말하지만, 체인 코드를 참조로 `fabcar` 사용하여 로 설정된 `<queryFunction>` `"queryAllCars"` 세계 `<queryArgs>` 상태의 `""`모든 차량을 쿼리합니다.

명령에서 전달된 인수에 대한 자세한 내용은 명령 도움말을 참조합니다.

```bash
npm run queryCC -- -h

```
