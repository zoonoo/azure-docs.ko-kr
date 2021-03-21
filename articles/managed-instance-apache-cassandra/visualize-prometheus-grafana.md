---
title: Apache Cassandra에 대해 Azure Managed Instance에서 내보낸 메트릭을 시각화 하도록 Grafana 구성
description: Azure Managed Instance Apache Cassandra 클러스터에서 내보낸 메트릭을 시각화 하기 위해 VM에 Grafana를 설치 및 구성 하는 방법에 대해 알아봅니다.
author: TheovanKraay
ms.service: managed-instance-apache-cassandra
ms.topic: how-to
ms.date: 03/02/2021
ms.author: thvankra
ms.openlocfilehash: ed0ff343595429a4cb81fef280203f1180eeb098
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101745589"
---
# <a name="configure-grafana-to-visualize-metrics-emitted-from-the-managed-instance-cluster"></a>관리 되는 인스턴스 클러스터에서 내보낸 메트릭을 시각화 하도록 Grafana 구성

> [!IMPORTANT]
> Apache Cassandra용 Azure Managed Instance는 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

Apache Cassandra 클러스터에 대 한 Azure Managed Instance를 배포 하는 경우 서비스는 다양 한 클라이언트 도구에서 사용 될 수 있는 [프로메테우스](https://prometheus.io/) 를 호스트 하는 서버를 프로 비전 합니다. 프로메테우스는 오픈 소스 모니터링 솔루션입니다. 관리 되는 인스턴스는 메트릭을 내보내고 10 분 또는 10gb의 데이터 (가장 먼저 임계값에 도달 하는 것)를 유지 합니다. 이 문서에서는 관리 되는 인스턴스 클러스터에서 내보낸 메트릭을 시각화 하도록 Grafana을 구성 하는 방법을 설명 합니다. 다음 작업은 메트릭을 시각화 하는 데 필요 합니다.

* 관리 되는 인스턴스가 있는 Azure Virtual Network 내에 Ubuntu 가상 컴퓨터를 배포 합니다.
* 오픈 소스 [Grafana 도구](https://grafana.com/grafana/) 를 설치 하 여 대시보드를 빌드하고 프로메테우스에서 내보낸 메트릭을 시각화 합니다.

## <a name="deploy-a-ubuntu-server"></a>Ubuntu 서버 배포

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. 관리 되는 인스턴스 클러스터가 있는 리소스 그룹으로 이동 합니다. **추가** 를 선택 하 고 **Ubuntu Server 18.04 lts** 이미지를 검색 합니다.

   :::image type="content" source="./media/visualize-prometheus-grafana/select-ubuntu-image.png" alt-text="Azure Portal에서 Ubuntu 서버 이미지를 찾습니다." border="true":::

1. 이미지를 선택 하 고 **만들기** 를 선택 합니다.

1. **가상 머신 만들기** 블레이드에서 다음 필드에 대 한 값을 입력 합니다. 다른 필드에 대 한 기본값을 그대로 둘 수 있습니다.

   * **가상 컴퓨터 이름** -VM의 이름을 입력 합니다.
   * **지역** -Virtual Network 배포 된 동일한 지역을 선택 합니다.

   :::image type="content" source="./media/visualize-prometheus-grafana/create-vm-ubuntu.png" alt-text="양식을 작성 하 여 Ubuntu 서버 이미지를 사용 하 여 VM을 만듭니다." border="true":::

1. **네트워킹** 탭에서 관리 되는 인스턴스가 배포 된 Virtual Network를 선택 합니다.

   :::image type="content" source="./media/visualize-prometheus-grafana/configure-networking-details.png" alt-text="Ubuntu 서버의 네트워크 설정을 구성 합니다." border="true":::

1. 마지막으로 **검토 + 만들기** 를 선택 하 여 Grafana 서버를 만듭니다.

## <a name="install-grafana"></a>Grafana 설치

1. Azure Portal에서 관리 되는 인스턴스 및 Grafana 서버를 배포한 Virtual Network를 엽니다. **Cassandra (인스턴스 0)** 라는 가상 머신 확장 집합 인스턴스가 표시 되어야 합니다. 이 프로메테우스 메트릭은이 가상 머신 확장 집합에서 호스팅됩니다. 이 인스턴스의 IP 주소를 적어둡니다.

   :::image type="content" source="./media/visualize-prometheus-grafana/prometheus-instance-address.png" alt-text="프로메테우스 인스턴스의 IP 주소를 가져옵니다." border="true":::

1. [Azure CLI](../virtual-machines/linux/ssh-from-windows.md#ssh-clients) 또는 선호 하는 클라이언트 도구를 사용 하 여 새로 만든 Ubuntu 서버에 연결 하 여 SSH를 통해 연결 합니다.

1. VM에 연결한 후에는 Grafana를 설치 하 고 구성 하 여 메트릭이 호스트 되는 가상 머신 확장 집합에 연결 해야 합니다. 명령 프롬프트를 열고 `nano` 명령을 입력 하 여 Nano 텍스트 편집기를 엽니다. 다음 스크립트를 텍스트 편집기에 붙여넣고를 `<prometheus IP address>` 이전 단계에서 기록한 IP 주소로 바꿉니다.

   ```bash
   #!/bin/bash
   
   echo "Installing Grafana..."
   
   if ! $SSH dpkg -s grafana prometheus > /dev/null; then
       echo "Installing packages."
       echo 'deb https://packages.grafana.com/oss/deb stable main' | $SSH sudo tee /etc/apt/sources.list.d/grafana.list > /dev/null
       curl https://packages.grafana.com/gpg.key | $SSH sudo apt-key add -
       $SSH sudo apt-get update
       $SSH sudo apt-get install -y grafana prometheus
   else
       echo "Skipping package installation"
   fi
   
   echo "Configuring grafana"
   cat <<EOF | $SSH sudo tee /etc/grafana/provisioning/datasources/prometheus.yml
   apiVersion: 1
   datasources:
     - name: Prometheus
       type: prometheus
       url: https://<prometheus IP address>:9443
       jsonData:
         tlsSkipVerify: true
   EOF
   
   echo "Restarting Grafana"
   $SSH sudo systemctl enable grafana-server
   $SSH sudo systemctl restart grafana-server
   
   echo "Installing Grafana plugins"
   $SSH sudo grafana-cli plugins install natel-discrete-panel
   $SSH sudo grafana-cli plugins install grafana-polystat-panel
   $SSH sudo systemctl restart grafana-server
   ```

1. `ctrl + X`을 입력 하 여 파일을 저장 합니다. 파일의 이름을 지정할 수 있습니다 `grafana.sh` .

1. `./grafana.sh`명령 프롬프트에 명령을 입력 하 여 Grafana를 설치 합니다.

1. 설치가 완료 되 면 다음 스크린샷에 표시 된 것 처럼 서버의 IP 주소에서 **포트 3000** 에서 Grafana를 사용할 수 있습니다.

   :::image type="content" source="./media/visualize-prometheus-grafana/open-grafana-port.png" alt-text="포트 3000에서 Grafana를 실행 합니다." border="true":::

1. Grafana에서 Apache Cassandra에 대해 만든 오픈 소스 대시보드 (예: [클러스터 개요](https://github.com/TheovanKraay/cassandra-exporter/blob/master/grafana/instaclustr/cluster-overview.json) JSON 파일)에서 선택할 수 있습니다. 대시보드의 JSON 정의를 다운로드 하 고 Grafana로 가져옵니다.

   :::image type="content" source="./media/visualize-prometheus-grafana/grafana-import.png" alt-text="Grafana JSON 정의를 가져옵니다." border="true":::

   :::image type="content" source="./media/visualize-prometheus-grafana/grafana-upload-json.png" alt-text="Grafana JSON 정의를 업로드 합니다." border="true":::

1. 그런 다음 선택한 대시보드를 사용 하 여 cassandra 관리 되는 인스턴스 클러스터를 모니터링할 수 있습니다.

   :::image type="content" source="./media/visualize-prometheus-grafana/monitor-cassandra-metrics.gif" alt-text="대시보드에서 Cassandra 관리 되는 인스턴스 메트릭을 확인 합니다." border="true":::

## <a name="next-steps"></a>다음 단계

이 문서에서는 Grafana를 사용 하 여 프로메테우스에서 메트릭을 시각화 하도록 대시보드를 구성 하는 방법을 배웠습니다. 다음 문서를 사용 하 여 Apache Cassandra에 대 한 Azure Managed Instance에 대해 자세히 알아보세요.

* [Apache Cassandra에 대 한 Azure Managed Instance 개요](introduction.md)
* [Azure Databricks (미리 보기)를 사용 하 여 관리 되는 Apache Spark 클러스터 배포](deploy-cluster-databricks.md)
