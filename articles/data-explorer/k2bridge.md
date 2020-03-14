---
title: Kibana를 사용 하 여 Azure 데이터 탐색기에서 데이터 시각화
description: 이 문서에서는 Kibana에 대 한 데이터 원본으로 Azure 데이터 탐색기를 설정 하는 방법에 대해 알아봅니다.
author: orspod
ms.author: orspodek
ms.reviewer: guregini
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: 30d74f36c6462d1fba039595d2ed6fe722b742e8
ms.sourcegitcommit: d322d0a9d9479dbd473eae239c43707ac2c77a77
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/12/2020
ms.locfileid: "79164814"
---
# <a name="visualize-data-from-azure-data-explorer-in-kibana-with-the-k2bridge-open-source-connector"></a>K2Bridge 오픈 소스 커넥터를 사용 하 여 Kibana의 Azure 데이터 탐색기에서 데이터 시각화

K2Bridge (Kibana-Kusto Bridge)를 사용 하면 Azure 데이터 탐색기를 데이터 원본으로 사용 하 고 Kibana에서 해당 데이터를 시각화할 수 있습니다. K2Bridge은 Kibana 인스턴스와 Azure 데이터 탐색기 클러스터 간의 프록시 역할을 하는 [오픈 소스](https://github.com/microsoft/K2Bridge) 컨테이너 화 된 응용 프로그램입니다. 이 문서에서는 K2Bridge를 사용 하 여 해당 연결을 만드는 방법을 설명 합니다.

K2Bridge는 Kibana 쿼리를 KQL (Kusto Query Language)로 변환 하 고 Azure 데이터 탐색기 결과를 다시 Kibana로 보냅니다. 

   ![차트](media/k2bridge/k2bridge-chart.png)

K2Bridge는 Kibana의 검색 탭을 지원 합니다.
* 데이터 검색 및 탐색
* 결과 필터링
* 결과 표에서 필드 추가 또는 제거
* 레코드 내용 보기
* 검색 저장 및 공유

아래 이미지는 K2Bridge에서 Azure 데이터 탐색기에 바인딩된 Kibana 인스턴스를 보여줍니다. Kibana의 사용자 환경은 변경 되지 않습니다.

   [![Kibana 페이지](media/k2bridge/k2bridge-kibana-page.png)](media/k2bridge/k2bridge-kibana-page.png#lightbox)

## <a name="prerequisites"></a>사전 요구 사항

Kibana의 Azure 데이터 탐색기에서 데이터를 시각화 하려면 다음을 준비 해야 합니다.

* Kubernetes 패키지 관리자 인 [투구 V3](https://github.com/helm/helm#install)
* AKS (Azure Kubernetes Service) 클러스터 또는 다른 Kubernetes 클러스터 (버전 1.14에서 버전 1.16로 테스트 및 확인 됨). AKS 클러스터가 필요한 경우 [Azure CLI를 사용 하 여](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough) AKS 클러스터 배포 또는 [Azure Portal를 사용](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough-portal) 하 여 배포를 참조 하세요.
* 다음을 포함 한 [Azure 데이터 탐색기 클러스터](create-cluster-database-portal.md)
    * Azure 데이터 탐색기 클러스터의 URL 
    * 데이터베이스 이름입니다.
    
* 다음을 포함 하 여 Azure 데이터 탐색기에서 데이터를 볼 수 있는 Azure AD 서비스 주체
    * 클라이언트 ID 
    * 클라이언트 암호입니다.

    ' Viewer ' 권한이 있는 서비스 주체를 사용 하는 것이 좋습니다. 더 높은 권한을 사용 하지 않는 것이 좋습니다.

    * [AZURE AD 서비스 주체에 대 한 클러스터의 보기 권한을 설정](https://docs.microsoft.com/azure/data-explorer/manage-database-permissions#manage-permissions-in-the-azure-portal)합니다.

    Azure AD 서비스 주체에 대 한 자세한 내용은 [AZURE ad 서비스 주체 만들기](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#create-an-azure-active-directory-application)를 참조 하세요.

## <a name="run-k2bridge-on-azure-kubernetes-service-aks"></a>AKS (Azure Kubernetes Service)에서 K2Bridge 실행

기본적으로 K2Bridges's 투구 차트는 MCR (Microsoft Container Registry)에 있는 공개적으로 사용 가능한 이미지를 참조 합니다. MCR은 자격 증명이 필요 하지 않으며 기본적으로 작동 합니다.

1. 필요한 투구 차트를 다운로드 합니다.

1. Elasticsearch 종속성을 투구에 추가 합니다. 
    Elasticsearch 종속성의 이유는 K2Bridge는 내부 small Elasticsearch 인스턴스를 사용 하 여 메타 데이터 관련 요청 (예: 인덱스 패턴 및 저장 된 쿼리)을 처리 한다는 것입니다. 이 내부 인스턴스에 저장 된 비즈니스 데이터는 없으며 구현 세부 정보로 간주할 수 있습니다. 

    1. Elasticsearch 종속성을 투구에 추가 하려면 다음을 수행 합니다.

        ```bash
        helm repo add elastic https://helm.elastic.co
        helm repo update
        ```

    1. GitHub 리포지토리의 차트 디렉터리 아래에서 K2Bridge 차트를 가져오려면 다음을 수행 합니다.
        1. [GitHub](https://github.com/microsoft/K2Bridge)에서 리포지토리를 복제 합니다.
        1. K2Bridges 루트 리포지토리 디렉터리로 이동 합니다.
        1. 다음을 실행합니다.

            ```bash
            helm dependency update charts/k2bridge
            ```

1. K2Bridge 배포:

    1. 사용자 환경에 맞는 값을 사용 하 여 변수를 설정 합니다.

        ```bash
        ADX_URL=[YOUR_ADX_CLUSTER_URL] #For example, https://mycluster.westeurope.kusto.windows.net
        ADX_DATABASE=[YOUR_ADX_DATABASE_NAME]
        ADX_CLIENT_ID=[SERVICE_PRINCIPAL_CLIENT_ID]
        ADX_CLIENT_SECRET=[SERVICE_PRINCIPAL_CLIENT_SECRET]
        ADX_TENANT_ID=[SERVICE_PRINCIPAL_TENANT_ID]
        ```

    1. 필드 Azure 애플리케이션 Insights 원격 분석을 사용 하도록 설정 합니다. 
        Azure 애플리케이션 Insights를 처음 사용 하는 경우 먼저 [Application Insights 리소스를 만들어야](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource)합니다. 계측 키를 변수에 [복사 해야 합니다](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource#copy-the-instrumentation-key) . 

        ```bash
        APPLICATION_INSIGHTS_KEY=[INSTRUMENTATION_KEY]
        COLLECT_TELEMETRY=true
        ```

    1. <a name="install-k2bridge-chart"></a>K2Bridge 차트를 설치 합니다.

        ```bash
        helm install k2bridge charts/k2bridge -n k2bridge --set image.repository=$REPOSITORY_NAME/$CONTAINER_NAME --set settings.adxClusterUrl="$ADX_URL" --set settings.adxDefaultDatabaseName="$ADX_DATABASE" --set settings.aadClientId="$ADX_CLIENT_ID" --set settings.aadClientSecret="$ADX_CLIENT_SECRET" --set settings.aadTenantId="$ADX_TENANT_ID" [--set image.tag=latest] [--set privateRegistry="$IMAGE_PULL_SECRET_NAME"] [--set settings.collectTelemetry=$COLLECT_TELEMETRY]
        ```

        [구성](https://github.com/microsoft/K2Bridge/blob/master/docs/configuration.md) 에서 전체 구성 옵션 집합을 찾을 수 있습니다.

    1. 명령 출력에는 Kibana를 배포 하기 위해 실행할 다음 투구 명령이 제안 됩니다. 필요에 따라 다음을 실행 합니다.

        ```bash
        helm install kibana elastic/kibana -n k2bridge --set image=docker.elastic.co/kibana/kibana-oss --set imageTag=6.8.5 --set elasticsearchHosts=http://k2bridge:8080
        ```
    1. 포트 전달을 사용 하 여 localhost에서 Kibana에 액세스 합니다. 

        ```bash
        kubectl port-forward service/kibana-kibana 5601 --namespace k2bridge
        ```
    1. http://127.0.0.1:5601로 이동 하 여 Kibana에 연결 합니다.

    1. 최종 사용자에 게 Kibana을 노출 합니다. 여러 가지 방법으로이 작업을 수행할 수 있습니다. 사용 하는 방법은 사용 사례에 따라 크게 달라 집니다.

        다음은 그 예입니다.

        서비스를 LoadBalancer 서비스로 노출 합니다. 이렇게 하려면 K2Bridge 투구 install 명령 ([위](#install-k2bridge-chart))에 다음 매개 변수를 추가 합니다.

        `--set service.type=LoadBalancer`
    
        다음을 실행합니다.

           ```bash
           kubectl get service -w -n k2bridge
           ```   
        출력은 다음과 같습니다. 

        ```bash
        NAME            TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
        kibana-kibana   LoadBalancer   xx.xx.xx.xx   <pending>      5601:30128/TCP   4m24s
        ```
        그런 다음 표시 되는 생성 된 외부 IP를 사용 하 여 `\<EXTERNAL-IP>:5601`브라우저를 열고 Kibana에 액세스할 수 있습니다.

1. 데이터에 액세스 하는 인덱스 패턴 구성:  
새 Kibana 인스턴스에서:
     1. Kibana를 엽니다.
     1. 관리로 이동 합니다.
     1. **인덱스 패턴**을 선택 합니다. 
     1. 인덱스 패턴을 만듭니다.
인덱스 이름은 별표 없이 테이블 이름 또는 함수 이름과 정확히 일치 해야 합니다. 목록에서 관련 선을 복사할 수 있습니다.

> [!Note]
> 다른 Kubernetes 공급자에서 실행 하려면 공급자가 제안한 항목에 맞게 `values.yaml` Elasticsearch storageClassName를 변경 합니다.

## <a name="visualize-data"></a>데이터 가상화

Azure 데이터 탐색기이 Kibana에 대 한 데이터 원본으로 구성 된 경우 Kibana를 사용 하 여 데이터를 탐색할 수 있습니다. 

1. Kibana의 왼쪽 메뉴에서 **검색** 탭을 선택 합니다.

1. 왼쪽 드롭다운 목록에서 탐색 하려는 데이터 원본을 정의 하는 인덱스 패턴 (이 경우 Azure 데이터 탐색기 테이블)을 선택 합니다.
    
   ![인덱스 패턴 선택](media/k2bridge/k2bridge-select-an-index-pattern.png)

1. 데이터에 시간 필터 필드가 있는 경우 시간 범위를 지정할 수 있습니다. 페이지의 오른쪽 위에서 시간 필터를 설정 합니다. 기본적으로 검색은 지난 15 분 동안의 데이터를 표시 합니다.

   ![시간 필터](media/k2bridge/k2bridge-time-filter.png)
    
1. 결과 테이블에는 처음 500 레코드가 표시 됩니다. 문서를 확장 하 여 JSON 또는 테이블 형식으로 필드 데이터를 검사할 수 있습니다.

   ![레코드 확장](media/k2bridge/k2bridge-expand-record.png)

1. 기본적으로 결과 테이블에는 문서 _source 및 시간 필드 (있는 경우)에 대 한 열이 포함 됩니다. 왼쪽 세로 막대의 필드 이름 옆에 있는 **추가** 를 선택 하 여 결과 테이블에 추가할 특정 열을 선택할 수 있습니다.

   ![특정 열](media/k2bridge/k2bridge-specific-columns.png)
    
1. 쿼리 표시줄에서 다음을 수행 하 여 데이터를 검색할 수 있습니다.
    * 검색 용어 입력
    * Lucene 쿼리 구문을 사용 합니다. 
    다음은 그 예입니다.
        * "오류"를 검색 하 여이 값을 포함 하는 모든 레코드를 찾습니다. 
        * "상태: 200"을 검색 하 여 상태 값이 200 인 모든 레코드를 가져옵니다. 
    * 논리 연산자 사용 (AND, OR, NOT)
    * 와일드 카드 (별표 "\*" 또는 물음표 "?") 사용 예를 들어:
        * 쿼리 `"destination_city: L*"`는 대상 도시 값이 "l"로 시작 하는 레코드와 일치 합니다. (K2Bridge은 대/소문자를 구분 하지 않음)

    ![쿼리 실행](media/k2bridge/k2bridge-run-query.png)
    
    > [!Tip]
    > 검색 [에서 더](https://github.com/microsoft/K2Bridge/blob/master/docs/searching.md)많은 검색 규칙과 논리를 찾을 수 있습니다.

1. 검색 결과를 필터링 하려면 페이지의 오른쪽 세로 막대에 있는 **필드 목록을** 사용 합니다. 
    필드 목록은 다음을 볼 수 있습니다.
    * 필드에 대 한 상위 5 개 값
    * 필드를 포함 하는 레코드 수
    * 각 값을 포함 하는 레코드의 백분율입니다. 
    
    >[!Tip]
    > (+) 돋보기 아이콘을 사용 하 여 특정 값을 가진 레코드를 모두 찾을 수 있습니다.
    
    ![필드 목록](media/k2bridge/k2bridge-field-list.png)
   
    결과 테이블의 각 레코드에 대 한 결과 테이블 형식 보기에서 (+) 돋보기 아이콘을 사용 하 여 결과를 필터링 할 수도 있습니다.
    
     ![테이블 목록](media/k2bridge/k2bridge-table-list.png)
    
1. 검색을 **저장** 하거나 **공유** 하려면 선택 합니다.

     ![검색 저장](media/k2bridge/k2bridge-save-search.png)
