---
title: Kibana를 사용하여 Azure 데이터 탐색기의 데이터 시각화
description: 이 문서에서는 Azure 데이터 탐색기를 Kibana의 데이터 원본으로 설정하는 방법을 배웁니다.
author: orspod
ms.author: orspodek
ms.reviewer: guregini
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: fac9c78607e50dca384670bf4cc08b50f723312b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80065604"
---
# <a name="visualize-data-from-azure-data-explorer-in-kibana-with-the-k2bridge-open-source-connector"></a>K2Bridge 오픈 소스 커넥터로 키바나의 Azure 데이터 탐색기에서 데이터 시각화

K2Bridge(키바나 쿠스토 브리지)를 사용하면 Azure 데이터 탐색기를 데이터 원본으로 사용하고 키바나에서 해당 데이터를 시각화할 수 있습니다. K2Bridge는 Kibana 인스턴스와 Azure 데이터 탐색기 클러스터 간의 프록시 역할을 하는 [오픈 소스](https://github.com/microsoft/K2Bridge) 컨테이너응용 프로그램입니다. 이 문서에서는 K2Bridge를 사용하여 해당 연결을 만드는 방법에 대해 설명합니다.

K2Bridge는 키바나 쿼리를 Kusto 쿼리 언어(KQL)로 변환하고 Azure 데이터 탐색기 결과를 키바나로 다시 보냅니다. 

   ![차트](media/k2bridge/k2bridge-chart.png)

K2Bridge는 키바나의 디스커버 탭을 지원하며, 다음과 같은 지원을 할 수 있습니다.
* 데이터 검색 및 탐색
* 결과 필터링
* 결과 표에 필드 추가 또는 제거
* 레코드 콘텐츠 보기
* 검색 저장 및 공유

아래 이미지는 K2Bridge의 Azure 데이터 탐색기로 바인딩된 Kibana 인스턴스를 보여 주며 있습니다. Kibana의 사용자 환경은 변경되지 않습니다.

   [![키바나 페이지](media/k2bridge/k2bridge-kibana-page.png)](media/k2bridge/k2bridge-kibana-page.png#lightbox)

## <a name="prerequisites"></a>사전 요구 사항

Kibana의 Azure 데이터 탐색기에서 데이터를 시각화하려면 다음을 준비하십시오.

* [헬름 V3](https://github.com/helm/helm#install), 쿠베네츠 패키지 매니저
* Azure Kubernetes 서비스(AKS) 클러스터 또는 기타 Kubernetes 클러스터(버전 1.14에서 버전 1.16까지)가 테스트 및 확인되었습니다. AKS 클러스터가 필요한 경우 [Azure CLI를 사용하거나 Azure](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough) [포털을 사용하여](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough-portal) AKS 클러스터 배포를 참조하세요.
* 다음을 포함하는 [Azure 데이터 탐색기 클러스터](create-cluster-database-portal.md)
    * Azure 데이터 탐색기 클러스터의 URL 
    * 데이터베이스 이름입니다.
    
* Azure 데이터 탐색기에서 데이터를 볼 수 있는 권한이 부여된 Azure AD 서비스 주체는 다음과 같은
    * 클라이언트 ID 
    * 클라이언트 암호입니다.

    '뷰어' 권한이 있는 서비스 주체를 권장합니다. 더 높은 사용 권한을 사용하는 것은 권장되지 않습니다.

    * [Azure AD 서비스 보안 주체에 대한 클러스터보기 권한을 설정합니다.](https://docs.microsoft.com/azure/data-explorer/manage-database-permissions#manage-permissions-in-the-azure-portal)

    Azure AD 서비스 주체에 대한 자세한 내용은 [Azure AD 서비스 보안 주체 만들기](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#create-an-azure-active-directory-application)를 참조하십시오.

## <a name="run-k2bridge-on-azure-kubernetes-service-aks"></a>Azure Kubernetes 서비스(AKS)에서 K2Bridge 실행

기본적으로 K2Bridges의 헬름 차트는 Microsoft의 컨테이너 레지스트리(MCR)에 있는 공개적으로 사용 가능한 이미지를 참조합니다. MCR은 자격 증명이 필요하지 않으며 즉시 작동합니다.

1. 필요한 투구 차트를 다운로드합니다.

1. 탄력성 검색 종속성을 헬름에 추가합니다. 
    Elasticsearch 종속성의 이유는 K2Bridge가 내부의 작은 Elasticsearch 인스턴스를 사용하여 메타데이터 관련 요청(예: 인덱스 패턴 및 저장된 쿼리)을 서비스하기 때문입니다. 이 내부 인스턴스에는 비즈니스 데이터가 저장되지 않으며 구현 세부 정보로 간주될 수 있습니다. 

    1. 탄력성 검색 종속성을 Helm에 추가하려면 다음을 수행합니다.

        ```bash
        helm repo add elastic https://helm.elastic.co
        helm repo update
        ```

    1. GitHub 리포지토리의 차트 디렉토리 아래에서 K2Bridge 차트를 얻으려면 다음을 수행하십시오.
        1. [GitHub에서](https://github.com/microsoft/K2Bridge)리포지토리를 복제합니다.
        1. K2Bridges 루트 리포지토리 디렉토리로 이동합니다.
        1. 다음을 실행합니다.

            ```bash
            helm dependency update charts/k2bridge
            ```

1. K2Bridge 배포:

    1. 환경에 맞는 값으로 변수를 설정합니다.

        ```bash
        ADX_URL=[YOUR_ADX_CLUSTER_URL] #For example, https://mycluster.westeurope.kusto.windows.net
        ADX_DATABASE=[YOUR_ADX_DATABASE_NAME]
        ADX_CLIENT_ID=[SERVICE_PRINCIPAL_CLIENT_ID]
        ADX_CLIENT_SECRET=[SERVICE_PRINCIPAL_CLIENT_SECRET]
        ADX_TENANT_ID=[SERVICE_PRINCIPAL_TENANT_ID]
        ```

    1. (선택 사항) Azure 응용 프로그램 인사이트 원격 분석을 사용하도록 설정합니다. 
        Azure 응용 프로그램 Insights를 처음 사용하는 경우 먼저 [응용 프로그램 인사이트 리소스를 만들어야](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource)합니다. [계측 키를](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource#copy-the-instrumentation-key) 변수에 복사해야 합니다. 

        ```bash
        APPLICATION_INSIGHTS_KEY=[INSTRUMENTATION_KEY]
        COLLECT_TELEMETRY=true
        ```

    1. <a name="install-k2bridge-chart"></a>K2Bridge 차트 설치:

        ```bash
        helm install k2bridge charts/k2bridge -n k2bridge --set image.repository=$REPOSITORY_NAME/$CONTAINER_NAME --set settings.adxClusterUrl="$ADX_URL" --set settings.adxDefaultDatabaseName="$ADX_DATABASE" --set settings.aadClientId="$ADX_CLIENT_ID" --set settings.aadClientSecret="$ADX_CLIENT_SECRET" --set settings.aadTenantId="$ADX_TENANT_ID" [--set image.tag=latest] [--set privateRegistry="$IMAGE_PULL_SECRET_NAME"] [--set settings.collectTelemetry=$COLLECT_TELEMETRY]
        ```

        [구성에서](https://github.com/microsoft/K2Bridge/blob/master/docs/configuration.md) 구성 옵션의 전체 집합을 찾을 수 있습니다.

    1. 명령 출력은 키바나를 배포하기 위해 실행할 다음 Helm 명령을 제안합니다. 선택적으로 다음을 실행합니다.

        ```bash
        helm install kibana elastic/kibana -n k2bridge --set image=docker.elastic.co/kibana/kibana-oss --set imageTag=6.8.5 --set elasticsearchHosts=http://k2bridge:8080
        ```
        
    1. 포트 포워딩을 사용하여 로컬 호스트에서 Kibana에 액세스합니다. 

        ```bash
        kubectl port-forward service/kibana-kibana 5601 --namespace k2bridge
        ```
        
    1. 을 탐색하여 키바나에 http://127.0.0.1:5601연결합니다.

    1. 키바나를 최종 사용자에게 노출합니다. 이렇게 하는 방법에는 여러 가지가 있습니다. 사용하는 방법은 주로 사용 사례에 따라 다릅니다.

        예를 들어:

        서비스를 로드밸서 서비스로 노출합니다. 이렇게 하려면 K2Bridge Helm 설치 명령(위)에 `--set service.type=LoadBalancer` 매개변수를 추가합니다.[above](#install-k2bridge-chart)        
    
        다음을 실행합니다.
        
        ```bash
        kubectl get service -w -n k2bridge
        ```
        
        출력은 다음과 같습니다. 

        ```bash
        NAME            TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
        kibana-kibana   LoadBalancer   xx.xx.xx.xx   <pending>      5601:30128/TCP   4m24s
        ```
 
        그런 다음 표시되는 생성된 외부 IP를 사용하여 브라우저를 열어 Kibana에 `<EXTERNAL-IP>:5601`액세스할 수 있습니다.

1. 데이터에 액세스하도록 인덱스 패턴을 구성합니다.  
새로운 키바나 인스턴스에서:
     1. 키바나 오픈.
     1. 관리로 이동합니다.
     1. **인덱스 패턴을 선택합니다.** 
     1. 인덱스 패턴을 만듭니다.
인덱스 이름은 별표 없이 테이블 이름 또는 함수 이름과 정확히 일치해야 합니다. 목록에서 관련 줄을 복사할 수 있습니다.

> [!Note]
> 다른 Kubernetes 공급자에서 실행하려면 공급자가 제안한 `values.yaml` 저장소에 맞게 Elasticsearch storageClassName을 변경합니다.

## <a name="visualize-data"></a>데이터 시각화

Azure 데이터 탐색기가 Kibana의 데이터 원본으로 구성된 경우 Kibana를 사용하여 데이터를 탐색할 수 있습니다. 

1. 키바나에서 왼쪽 메뉴에서 **검색** 탭을 선택합니다.

1. 왼쪽 드롭다운 목록에서 탐색할 데이터 원본을 정의하는 인덱스 패턴(이 경우 Azure Data Explorer 테이블)을 선택합니다.
    
   ![인덱스 패턴 선택](media/k2bridge/k2bridge-select-an-index-pattern.png)

1. 데이터에 시간 필터 필드가 있는 경우 시간 범위를 지정할 수 있습니다. 페이지 오른쪽 상단에 시간 필터를 설정합니다. 기본적으로 Discover는 지난 15분 동안의 데이터를 표시합니다.

   ![시간 필터](media/k2bridge/k2bridge-time-filter.png)
    
1. 결과 테이블에는 처음 500개의 레코드가 표시됩니다. 문서를 확장하여 JSON 또는 테이블 형식으로 필드 데이터를 검사할 수 있습니다.

   ![레코드 확장](media/k2bridge/k2bridge-expand-record.png)

1. 기본적으로 결과 테이블에는 문서 _source 시간 필드(있는 경우)에 대한 열이 포함됩니다. 왼쪽 사이드바의 필드 이름 옆에 **추가를** 선택하여 결과 테이블에 추가할 특정 열을 선택할 수 있습니다.

   ![특정 열](media/k2bridge/k2bridge-specific-columns.png)
    
1. 쿼리 표시줄에서 다음을 사용하여 데이터를 검색할 수 있습니다.
    * 검색어 입력
    * Lucene 쿼리 구문을 사용 하 여. 
    예를 들어:
        * "오류"를 검색하여 이 값을 포함하는 모든 레코드를 찾습니다. 
        * 상태 값이 200인 모든 레코드를 얻으려면 "상태: 200"을 검색합니다. 
    * 논리 연산자 사용(및 또는 아님)
    * 와일드카드 사용(별표 " \* " 또는 물음표 "?") 예를 들어:
        * 쿼리는 `"destination_city: L*"` 대상 도시 값이 "l"로 시작하는 레코드와 일치합니다(K2Bridge는 대/소문자를 구분하지 않음).

    ![쿼리 실행](media/k2bridge/k2bridge-run-query.png)
    
    > [!Tip]
    > [검색에서](https://github.com/microsoft/K2Bridge/blob/master/docs/searching.md)더 많은 검색 규칙 및 논리를 찾을 수 있습니다.

1. 검색 결과를 필터링하려면 페이지의 오른쪽 사이드바에 있는 **필드 목록을** 사용합니다. 
    필드 목록은 다음과 같은 위치를 볼 수 있습니다.
    * 필드의 상위 5개 값
    * 필드를 포함하는 레코드 수
    * 각 값을 포함하는 레코드의 백분율입니다. 
    
    >[!Tip]
    > (+) 돋보기 아이콘을 사용하여 특정 값이 있는 모든 레코드를 찾습니다.
    
    ![필드 목록](media/k2bridge/k2bridge-field-list.png)
   
    결과 테이블의 각 레코드의 결과 테이블 형식 보기에서 (+) 돋보기 아이콘을 사용하여 결과를 필터링할 수도 있습니다.
    
     ![테이블 목록](media/k2bridge/k2bridge-table-list.png)
    
1. 검색 **저장** 또는 **공유중** 하나를 선택합니다.

     ![검색 저장](media/k2bridge/k2bridge-save-search.png)
