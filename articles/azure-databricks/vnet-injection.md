---
title: 가상 네트워크에 Azure Databricks 배포
description: 이 문서에서는 VNet 주입이 라고도 하는 가상 네트워크에 Azure Databricks를 배포 하는 방법을 설명 합니다.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: conceptual
ms.date: 10/10/2019
ms.openlocfilehash: 0bb3221c201e6dd4dd17cca8ef7e3ed3331de228
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72432669"
---
# <a name="deploy-azure-databricks-in-your-virtual-network"></a>가상 네트워크에 Azure Databricks 배포

Azure Databricks의 기본 배포는 Azure에서 완전히 관리 되는 서비스입니다. 가상 네트워크 (VNet)를 비롯 한 모든 데이터 평면 리소스는 잠긴 리소스 그룹에 배포 됩니다. 그러나 네트워크 사용자 지정이 필요한 경우 사용자가 다음을 수행할 수 있도록 사용자의 가상 네트워크 (VNet 삽입이 라고도 함)에 Azure Databricks 리소스를 배포할 수 있습니다.

* 서비스 끝점을 사용 하 여 보다 안전한 방식으로 다른 Azure 서비스 (예: Azure Storage)에 Azure Databricks을 연결 합니다.
* Azure Databricks와 함께 사용 하기 위해 온-프레미스 데이터 원본에 연결 하 여 사용자 정의 경로를 활용 합니다.
* 네트워크 가상 어플라이언스에 Azure Databricks 연결 하 여 모든 아웃 바운드 트래픽을 검사 하 고 허용 및 거부 규칙에 따라 조치를 취합니다.
* 사용자 지정 DNS를 사용 하도록 Azure Databricks를 구성 합니다.
* 네트워크 보안 그룹 (NSG) 규칙을 구성 하 여 송신 트래픽 제한을 지정 합니다.
* 기존 가상 네트워크에 Azure Databricks 클러스터를 배포 합니다.

사용자 고유의 가상 네트워크에 Azure Databricks 리소스를 배포 하면 유연한 CIDR 범위 (가상 네트워크의 경우/16-/24 사이에 있고 서브넷의 경우/18-/26 사이)를 활용할 수도 있습니다.

  > [!NOTE]
  > 기존 작업 영역에 대 한 가상 네트워크를 대체할 수 없습니다. 현재 작업 영역에서 필요한 수의 활성 클러스터 노드를 수용할 수 없는 경우 더 큰 가상 네트워크에 다른 작업 영역을 만듭니다. 이전에서 새 작업 영역으로 리소스 (노트북, 클러스터 구성, 작업)를 복사 하려면 [이 자세한 마이그레이션 단계](howto-regional-disaster-recovery.md#detailed-migration-steps) 를 따르세요.

## <a name="virtual-network-requirements"></a>가상 네트워크 요구 사항

Azure Portal에서 Azure Databricks 작업 영역 배포 인터페이스를 사용 하 여 필수 서브넷, 네트워크 보안 그룹 및 허용 목록 설정으로 기존 가상 네트워크를 자동으로 구성 하거나를 사용할 수 있습니다 Azure Resource Manager 템플릿을 구성 하 여 가상 네트워크를 구성 하 고 작업 영역을 배포 합니다.

Azure Databricks 작업 영역을 배포 하는 가상 네트워크는 다음 요구 사항을 충족 해야 합니다.

### <a name="location"></a>위치

가상 네트워크는 Azure Databricks 작업 영역과 동일한 위치에 있어야 합니다.

### <a name="subnets"></a>서브넷

가상 네트워크는 Azure Databricks 전용 두 개의 서브넷을 포함 해야 합니다.

   1. 클러스터 내부 통신을 허용 하는 구성 된 네트워크 보안 그룹을 포함 하는 개인 서브넷

   2. Azure Databricks 제어 평면과 통신할 수 있도록 구성 된 네트워크 보안 그룹을 사용 하는 공용 서브넷

### <a name="address-space"></a>주소 공간

가상 네트워크에 대 한/16-/24 사이의 CIDR 블록과 개인 및 공용 서브넷의 CIDR 블록 (/18-/26)

### <a name="whitelisting"></a>허용 목록

서브넷과 Azure Databricks 제어 평면 간의 모든 아웃 바운드 및 인바운드 트래픽은 허용 목록 여야 합니다.

## <a name="create-an-azure-databricks-workspace"></a>Azure Databricks 작업 영역 만들기

이 섹션에서는 Azure Portal에서 Azure Databricks 작업 영역을 만들고 자신의 기존 가상 네트워크에 배포 하는 방법을 설명 합니다. Azure Databricks은 사용자가 제공한 CIDR 범위를 사용 하 여 두 개의 새 서브넷 및 네트워크 보안 그룹으로 가상 네트워크를 업데이트 하 고, 인바운드 및 아웃 바운드 서브넷 트래픽을 허용 목록 하 고, 업데이트 된 가상 네트워크에 작업 영역을 배포 합니다.

## <a name="prerequisites"></a>전제 조건

Azure Databricks 작업 영역을 배포할 가상 네트워크가 있어야 합니다. 기존 가상 네트워크를 사용 하거나 새 가상 네트워크를 만들 수 있지만 가상 네트워크는 만들려는 Azure Databricks 작업 영역과 동일한 지역에 있어야 합니다. 가상 네트워크에는/16-/24 사이의 CIDR 범위가 필요 합니다.

  > [!Warning]
  > 가상 네트워크가 더 작은 작업 영역 (CIDR 범위 더 낮음)은 더 큰 가상 네트워크를 사용 하는 작업 영역 보다 더 빠르게 IP 주소 (네트워크 공간)를 사용할 수 있습니다. 예를 들어/24 가상 네트워크 및/26 서브넷을 포함 하는 작업 영역에는 한 번에 최대 64 노드가 활성화 될 수 있지만,/20 개의 가상 네트워크 및/22 서브넷이 있는 작업 영역에는 최대 1024 노드가 포함 될 수 있습니다.

  작업 영역을 구성할 때 서브넷이 자동으로 생성 되 고 구성 중에 서브넷에 대 한 CIDR 범위를 제공할 기회가 생깁니다.

## <a name="configure-the-virtual-network"></a>가상 네트워크 구성

1. Azure Portal에서 **+ 리소스 만들기 > 분석 > Azure Databricks** 을 선택 하 여 Azure Databricks 서비스 대화 상자를 엽니다.

2. 시작 가이드의 2 단계: Azure Databricks 작업 영역 만들기에 설명 된 구성 단계를 따르고 Virtual Network 옵션에서 Azure Databricks 작업 영역 배포를 선택 합니다.

   ![Azure Databricks 서비스 만들기](./media/vnet-injection/create-databricks-service.png)

3. 사용 하려는 가상 네트워크를 선택 합니다.

   ![가상 네트워크 옵션](./media/vnet-injection/select-vnet.png)

4. Azure Databricks 전용 두 서브넷에 대해/18-/26 사이의 블록에 CIDR 범위를 제공 합니다.

   * 공용 서브넷은 Azure Databricks 제어 평면과 통신할 수 있도록 하는 연결 된 네트워크 보안 그룹을 사용 하 여 생성 됩니다.
   * 클러스터 내부 통신을 허용 하는 연결 된 네트워크 보안 그룹을 사용 하 여 개인 서브넷을 만듭니다.

5. **만들기** 를 클릭 하 여 가상 네트워크에 Azure Databricks 작업 영역을 배포 합니다.

## <a name="advanced-resource-manager-configurations"></a>고급 리소스 관리자 구성

가상 네트워크의 구성에 대 한 제어를 강화 하려는 경우, 예를 들어 기존 서브넷을 사용 하거나, 기존 네트워크 보안 그룹을 사용 하거나, 사용자 고유의 보안 규칙을 만들려면 다음과 같은 Azure Resource Manager 템플릿을 사용할 수 있습니다. 포털 가상 네트워크 구성 및 작업 영역 배포.

### <a name="all-in-one"></a>모든 항목

가상 네트워크, 네트워크 보안 그룹 및 Azure Databricks 작업 영역을 모두 만들려면 [Databricks VNet에 삽입 된 작업 영역에 대해 모두 단일 템플릿을](https://azure.microsoft.com/resources/templates/101-databricks-all-in-one-template-for-vnet-injection/)사용 합니다.

이 템플릿을 사용 하는 경우에는 서브넷 트래픽 수동 허용 목록을 수행할 필요가 없습니다.

### <a name="network-security-groups"></a>네트워크 보안 그룹

기존 가상 네트워크에 필요한 규칙을 사용 하 여 네트워크 보안 그룹을 만들려면 [Databricks VNet 주입을 위한 네트워크 보안 그룹 템플릿을](https://azure.microsoft.com/resources/templates/101-databricks-all-in-one-template-for-vnet-injection/)사용 합니다.

이 템플릿을 사용 하는 경우에는 서브넷 트래픽 수동 허용 목록을 수행할 필요가 없습니다.

### <a name="virtual-network"></a>가상 네트워크

적절 한 공용 및 개인 서브넷을 사용 하 여 가상 네트워크를 만들려면 [Databricks VNet 삽입에 대 한 Virtual Network 템플릿을](https://azure.microsoft.com/resources/templates/101-databricks-vnet-for-vnet-injection)사용 합니다.

또한 네트워크 보안 그룹 템플릿을 사용 하지 않고이 템플릿을 사용 하는 경우 가상 네트워크에서 사용 하는 네트워크 보안 그룹에 허용 목록 규칙을 수동으로 추가 해야 합니다.

### <a name="azure-databricks-workspace"></a>Azure Databricks 작업 영역

공용 및 개인 서브넷이 있고 올바르게 구성 된 네트워크 보안 그룹이 이미 구성 된 기존 가상 네트워크에 Azure Databricks 작업 영역을 배포 하려면 [Databricks VNet 주입 용 작업 영역 템플릿을](https://azure.microsoft.com/resources/templates/101-databricks-workspace-with-vnet-injection)사용 합니다.

또한 네트워크 보안 그룹 템플릿을 사용 하지 않고이 템플릿을 사용 하는 경우 가상 네트워크에서 사용 하는 네트워크 보안 그룹에 허용 목록 규칙을 수동으로 추가 해야 합니다.

## <a name="whitelisting-subnet-traffic"></a>서브넷 트래픽 허용 목록

[Azure Portal](https://docs.azuredatabricks.net/administration-guide/cloud-configurations/azure/vnet-inject.html#vnet-inject-portal) 또는 [Azure Resource Manager 템플릿을](https://docs.azuredatabricks.net/administration-guide/cloud-configurations/azure/vnet-inject.html#vnet-inject-advanced) 사용 하 여 네트워크 보안 그룹을 만들지 않은 경우에는 서브넷에서 다음 트래픽을 수동으로 허용 목록 합니다.

|방향|프로토콜|원본|원본 포트|대상|대상 포트|
|---------|--------|------|-----------|-----------|----------------|
|인바운드|\*|VirtualNetwork|\*|\*|\*|
|인바운드|\*|제어 평면 NAT IP|\*|\*|22|
|인바운드|\*|제어 평면 NAT IP|\*|\*|5557|
|아웃바운드|\*|\*|\*|Webapp IP|\*|
|아웃바운드|\*|\*|\*|SQL (서비스 태그)|\*|
|아웃바운드|\*|\*|\*|저장소 (서비스 태그)|\*|
|아웃바운드|\*|\*|\*|VirtualNetwork|\*|

다음 IP 주소를 사용 하 여 서브넷 트래픽을 허용 목록. SQL (metastore) 및 저장소 (아티팩트 및 로그 저장소)의 경우 Sql 및 저장소 [서비스 태그](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags)를 사용 해야 합니다.

|Azure Databricks 지역|서비스|공용 IP|
|-----------------------|-------|---------|
|미국 동부|제어 평면 NAT </br></br>Webapp|23.101.152.95/32 </br></br>40.70.58.221/32|
|미국 동부 2|제어 평면 NAT </br></br>Webapp|23.101.152.95/32 </br></br>40.70.58.221/32|
|미국 중북부|제어 평면 NAT </br></br>Webapp|23.101.152.95/32 </br></br>40.70.58.221/32|
|미국 중부|제어 평면 NAT </br></br>Webapp|23.101.152.95/32 </br></br>40.70.58.221/32|
|미국 중남부|제어 평면 NAT </br></br>Webapp|40.83.178.242/32 </br></br>40.118.174.12/32|
|미국 서부|제어 평면 NAT </br></br>Webapp|40.83.178.242/32 </br></br>40.118.174.12/32|
|미국 서부 2|제어 평면 NAT </br></br>Webapp|40.83.178.242/32 </br></br>40.118.174.12/32|
|캐나다 중부|제어 평면 NAT </br></br>Webapp|40.85.223.25/32 </br></br>13.71.184.74/32|
|캐나다 동부|제어 평면 NAT </br></br>Webapp|40.85.223.25/32 </br></br>13.71.184.74/32|
|영국 서부|제어 평면 NAT </br></br>Webapp|51.140.203.27/32 </br></br>51.140.204.4/32|
|영국 남부|제어 평면 NAT </br></br>Webapp|51.140.203.27/32 </br></br>51.140.204.4/32|
|서유럽|제어 평면 NAT </br></br>Webapp|23.100.0.135/32 </br></br>52.232.19.246/32|
|북유럽|제어 평면 NAT </br></br>Webapp|23.100.0.135/32 </br></br>52.232.19.246/32|
|인도 중부|제어 평면 NAT </br></br>Webapp|104.211.89.81/32 </br></br>104.211.101.14/32|
|인도 남부|제어 평면 NAT </br></br>Webapp|104.211.89.81/32 </br></br>104.211.101.14/32|
|인도 서부|제어 평면 NAT </br></br>Webapp|104.211.89.81/32 </br></br>104.211.101.14/32|
|동남아시아|제어 평면 NAT </br></br>Webapp|52.187.0.85/32 </br></br>52.187.145.107/32|
|동아시아|제어 평면 NAT </br></br>Webapp|52.187.0.85/32 </br></br>52.187.145.107/32|
|오스트레일리아 동부|제어 평면 NAT </br></br>Webapp|13.70.105.50/32 </br></br>13.75.218.172/32|
|오스트레일리아 남동부|제어 평면 NAT </br></br>Webapp|13.70.105.50/32 </br></br>13.75.218.172/32|
|오스트레일리아 중부|제어 평면 NAT </br></br>Webapp|13.70.105.50/32 </br></br>13.75.218.172/32|
|오스트레일리아 중부 2|제어 평면 NAT </br></br>Webapp|13.70.105.50/32 </br></br>13.75.218.172/32|
|일본 동부|제어 평면 NAT </br></br>Webapp|13.78.19.235/32 </br></br>52.246.160.72/32|
|일본 서부|제어 평면 NAT </br></br>Webapp|13.78.19.235/32 </br></br>52.246.160.72/32|

## <a name="troubleshooting"></a>문제 해결

### <a name="workspace-launch-errors"></a>작업 영역 시작 오류

**"작업 영역을 만드는 동안 오류가 발생 했습니다." 라는 오류와 함께 사용자 지정 가상 네트워크에서 작업 영역을 시작 하면 Azure Databricks 로그인 화면에서 실패 합니다. 사용자 지정 네트워크 구성이 올바른지 확인 하 고 다시 시도 하십시오. "**

이 오류는 네트워크 구성에서 요구 사항을 충족 하지 않는 경우에 발생 합니다. 작업 영역을 만들 때이 항목의 지침을 준수 하는지 확인 합니다.

### <a name="cluster-creation-errors"></a>클러스터 생성 오류

**연결할 수 없는 인스턴스: SSH를 통해 리소스에 연결할 수 없습니다.**

가능한 원인: 제어 평면에서 작업자로의 트래픽이 차단 됩니다. 인바운드 보안 규칙이 요구 사항을 충족 하는지 확인 하 여 수정 합니다. 온-프레미스 네트워크에 연결 된 기존 가상 네트워크에 배포 하는 경우 Azure Databricks 작업 영역을 온-프레미스 네트워크에 연결 하는 데 제공 된 정보를 사용 하 여 설정을 검토 합니다.

**예기치 않은 시작 오류: 클러스터를 설정 하는 동안 예기치 않은 오류가 발생 했습니다. 문제가 지속 되 면 다시 시도 하 고 Azure Databricks에 문의 하세요. 내부 오류 메시지: 노드를 배치 하는 동안 시간이 초과 되었습니다.**

가능한 원인: 작업자에서 Azure Storage 끝점으로의 트래픽이 차단 됩니다. 아웃 바운드 보안 규칙이 요구 사항을 충족 하는지 확인 하 여 수정 합니다. 사용자 지정 DNS 서버를 사용 하는 경우에는 가상 네트워크의 DNS 서버 상태도 확인 해야 합니다.

**클라우드 공급자 시작 실패: 클러스터를 설정 하는 동안 클라우드 공급자 오류가 발생 했습니다. 자세한 내용은 Azure Databricks 가이드를 참조 하세요. Azure 오류 코드: AuthorizationFailed/InvalidResourceReference.**

가능한 원인: 가상 네트워크 또는 서브넷이 더 이상 존재 하지 않습니다. 가상 네트워크 및 서브넷이 존재 하는지 확인 합니다.

**클러스터가 종료 되었습니다. 원인: Spark 시작 실패: Spark를 시간 내에 시작할 수 없습니다. 이 문제는 Hive metastore 오작동, 잘못 된 Spark 구성 또는 init 스크립트의 오작동으로 인해 발생할 수 있습니다. 이 문제를 해결 하려면 Spark 드라이버 로그를 참조 하 고 문제가 지속 되 면 Databricks에 문의 하세요. 내부 오류 메시지: Spark를 시작 하지 못했습니다. 드라이버가 시간 내에 시작 하지 못했습니다.**

가능한 원인: 컨테이너가 호스팅 인스턴스 또는 DBFS 저장소 계정과 통신할 수 없습니다. 다음 홉이 인터넷 인 DBFS 저장소 계정의 서브넷에 사용자 지정 경로를 추가 하 여 수정 합니다.

### <a name="notebook-command-errors"></a>노트북 명령 오류

**명령이 응답 하지 않습니다.**

가능한 원인: 작업자 간 통신이 차단 됩니다. 인바운드 보안 규칙이 요구 사항을 충족 하는지 확인 하 여 수정 합니다.

**노트북 워크플로는 다음과 같은 예외를 제외 하 고 실패 합니다. databricks: org.**

가능한 원인: 작업자에서 Azure Databricks Webapp의 트래픽이 차단 됩니다. 아웃 바운드 보안 규칙이 요구 사항을 충족 하는지 확인 하 여 수정 합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure Databricks를 사용하여 데이터 추출, 변환 및 로드](databricks-extract-load-sql-data-warehouse.md)
