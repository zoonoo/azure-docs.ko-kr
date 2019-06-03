---
title: Grafana를 사용 하 여 Azure 데이터 탐색기에서 데이터 시각화
description: 이 문서에서는 Grafana에 대한 데이터 원본으로 Azure Data Explorer를 설정한 다음, 샘플 클러스터에서 데이터를 시각화하는 방법을 알아봅니다.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 12/05/2018
ms.openlocfilehash: 135f8f1c9c352f9d2307a8bf9ad1bec892aac179
ms.sourcegitcommit: d89032fee8571a683d6584ea87997519f6b5abeb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66399921"
---
# <a name="visualize-data-from-azure-data-explorer-in-grafana"></a>Grafana의 Azure Data Explorer에서 데이터 시각화

Grafana는 데이터를 쿼리하고 시각화한 다음, 시각화에 따라 대시보드를 만들고 공유할 수 있도록 해주는 분석 플랫폼입니다. Grafana는 Azure Data Explorer *플러그 인*을 제공하여 Azure Data Explorer에서 데이터에 연결하고 데이터를 시각화할 수 있습니다. 이 문서에서는 Grafana에 대한 데이터 원본으로 Azure Data Explorer를 설정한 다음, 샘플 클러스터에서 데이터를 시각화하는 방법을 알아봅니다.

## <a name="prerequisites"></a>필수 조건

이 방법을 완료하려면 다음 사항이 필요합니다.

* 운영 체제용 [Grafana 버전 5.3.0 이상](https://docs.grafana.org/installation/)

* Grafana용 [Azure Data Explorer 플러그 인](https://grafana.com/plugins/grafana-azure-data-explorer-datasource/installation)

* StormEvents 샘플 데이터가 포함된 클러스터입니다. 자세한 내용은 [빠른 시작: Azure Data Explorer 클러스터 및 데이터베이스 만들기](create-cluster-database-portal.md) 및 [Azure Data Explorer로 샘플 데이터 수집](ingest-sample-data.md)을 참조하세요.

    [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

## <a name="configure-the-data-source"></a>데이터 원본 구성

Grafana에 대한 데이터 원본으로 Azure Data Explorer를 구성하려면 다음 단계를 수행합니다. 이 섹션에서 이 단계를 더 자세히 다룹니다.

1. Azure Active Directory(Azure AD) 서비스 주체를 만듭니다. 서비스 주체는 Grafana에서 Azure Data Explorer 서비스에 액세스하기 위해 사용합니다.

1. Azure AD 서비스 주체를 Azure Data Explorer 데이터베이스에서 *뷰어* 역할에 추가합니다.

1. Azure AD 서비스 주체의 정보를 기반으로 Grafana 연결 속성을 지정한 다음, 연결을 테스트합니다.

### <a name="create-a-service-principal"></a>서비스 주체 만들기

[Azure CLI](#azure-cli) 명령줄 환경을 사용하여 또는 [Azure Portal](#azure-portal)에 서비스 주체를 만들 수 있습니다. 어떤 방법을 사용하든 서비스 주체를 만든 후 이후 단계에서 사용하는 네 가지 연결 속성에 대한 값을 가져옵니다.

#### <a name="azure-portal"></a>Azure portal

1. 서비스 주체를 만들려면 [Azure Portal 설명서](/azure/active-directory/develop/howto-create-service-principal-portal)의 지침을 따릅니다.

    1. [역할에 애플리케이션 할당](/azure/active-directory/develop/howto-create-service-principal-portal#assign-the-application-to-a-role) 섹션에서 **Reader** 역할 유형을 Azure Data Explorer 클러스터에 할당합니다.

    1. [로그인에 값 가져오기](/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) 섹션에서 이 단계에서 설명하는 다음 세 가지 속성 값을 복사합니다. **디렉터리 ID**(테넌트 ID), **애플리케이션 ID** 및 **암호**

1. Azure Portal에서 **구독**을 선택한 다음, 서비스 주체를 만든 구독에 대한 ID를 복사합니다.

    ![구독 ID - Portal](media/grafana/subscription-id-portal.png)

#### <a name="azure-cli"></a>Azure CLI

1. 서비스 주체를 만듭니다. `reader`의 해당 범위 및 역할 유형을 설정합니다.

    ```azurecli
    az ad sp create-for-rbac --name "https://{UrlToYourGrafana}:{PortNumber}" --role "reader" \
                             --scopes /subscriptions/{SubID}/resourceGroups/{ResourceGroupName}
    ```

    자세한 내용은 [Azure CLI을 사용하여 Azure 서비스 주체 만들기](/cli/azure/create-an-azure-service-principal-azure-cli)를 참조하세요.

1. 명령은 다음과 같이 결과 집합을 반환합니다. 세 가지 속성 값 **appID**, **암호** 및 **테넌트**를 복사합니다.

    ```json
    {
      "appId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
      "displayName": "{UrlToYourGrafana}:{PortNumber}",
      "name": "https://{UrlToYourGrafana}:{PortNumber}",
      "password": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
      "tenant": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX"
    }
    ```

1. 구독 목록을 가져옵니다.

    ```azurecli
    az account list --output table
    ```

    해당 구독 ID를 복사합니다.

    ![구독 ID - CLI](media/grafana/subscription-id-cli.png)

### <a name="add-the-service-principal-to-the-viewers-role"></a>뷰어 역할에 서비스 주체 추가

서비스 주체를 만들었으므로, 이제 Azure Data Explorer 데이터베이스에서 *뷰어* 역할에 추가합니다. 관리 명령을 사용하여 Azure Portal의 **사용 권한** 또는 **쿼리**에서 이 작업을 수행할 수 있습니다.

#### <a name="azure-portal---permissions"></a>Azure Portal - 사용 권한

1. Azure Portal에서 Azure Data Explorer 클러스터로 이동합니다.

1. **개요** 섹션에서 StormEvents 샘플 데이터를 사용하여 데이터베이스를 선택합니다.

    ![데이터베이스 선택](media/grafana/select-database.png)

1. **권한**을 선택한 다음, **추가**를 선택합니다.

    ![데이터베이스 권한](media/grafana/database-permissions.png)

1. **데이터베이스 권한 추가**에서 **뷰어** 역할을 선택한 다음, **주체 선택**을 선택합니다.

    ![데이터베이스 권한 추가](media/grafana/add-permission.png)

1. 사용자가 만든 서비스 주체를 검색합니다(예제는 주체 **mb grafana**를 보여줌). 주체를 선택한 다음, **선택**을 클릭합니다.

    ![Azure Portal에서 권한 관리](media/grafana/new-principals.png)

1. **저장**을 선택합니다.

    ![Azure Portal에서 권한 관리](media/grafana/save-permission.png)

#### <a name="management-command---query"></a>관리 명령 - 쿼리

1. Azure Portal에서 Azure Data Explorer 클러스터로 이동한 다음, **쿼리**를 선택합니다.

    ![쿼리](media/grafana/query.png)

1. 쿼리 창에서 다음 명령을 실행합니다. Azure Portal 또는 CLI에서 애플리케이션 ID 및 테넌트 ID를 사용합니다.

    ```kusto
    .add database {TestDatabase} viewers ('aadapp={ApplicationID};{TenantID}')
    ```

    명령은 다음과 같이 결과 집합을 반환합니다. 이 예제에서 첫 번째 행은 데이터베이스에서 기존 사용자를 사용되며 두 번째 행은 방금 추가된 서비스 주체를 위해 사용됩니다.

    ![결과 집합](media/grafana/result-set.png)

### <a name="specify-properties-and-test-the-connection"></a>속성 지정 및 연결 테스트

서비스 주체를 *뷰어* 역할에 할당한 상태에서, 이제 Grafana 인스턴스에서 속성을 지정하고 Azure Data Explorer에 대한 연결을 테스트합니다.

1. Grafana의 왼쪽 메뉴에서 기어 아이콘을 선택한 다음, **데이터 원본**을 선택합니다.

    ![데이터 원본](media/grafana/data-sources.png)

1. **데이터 원본 추가**를 선택합니다.

1. **데이터 원본/새로 만들기** 페이지에서, 데이터 원본에 대한 이름을 입력한 다음, **Azure Data Explorer 데이터 원본** 형식을 선택합니다.

    ![연결 이름 및 형식](media/grafana/connection-name-type.png)

1. https://{ClusterName}.{Region}.kusto.windows.net 형식으로 클러스터 이름을 입력합니다. Azure Portal 또는 CLI에서 다른 값을 입력합니다. 매핑에 대한 다음 이미지는 아래 표를 참조하세요.

    ![연결 속성](media/grafana/connection-properties.png)

    | Grafana UI | Azure portal | Azure CLI |
    | --- | --- | --- |
    | 구독 ID | 구독 ID | SubscriptionId |
    | 테넌트 ID | 디렉터리 ID | tenant |
    | 클라이언트 ID | 애플리케이션 UI | appId |
    | 클라이언트 암호 | 암호 | password |
    | | | |

1. **저장 & 테스트**를 선택합니다.

    테스트에 성공한 경우 다음 섹션으로 이동합니다. 문제가 발생 하는 경우 Grafana에서 지정한 값을 확인하고 이전 단계를 검토합니다.

## <a name="visualize-data"></a>데이터 가상화

Grafana에 대한 데이터 원본으로 Azure Data Explorer 구성을 완료했다면, 이제 데이터를 시각화할 차례입니다. 여기에서는 기본적인 예제만 살펴보지만 다양한 작업을 수행할 수 있습니다. 샘플 데이터 세트에 대해 실행할 다른 쿼리의 예제로 [Azure Data Explorer에 대한 쿼리 작성](write-queries.md)을 볼 것을 권장합니다.

1. Grafana의 왼쪽 메뉴에서 더하기 아이콘을 선택한 다음, **대시 보드**를 선택합니다.

    ![대시보드 만들기](media/grafana/create-dashboard.png)

1. **추가** 탭 아래에 있는 **그래프**를 선택합니다.

    ![그래프 추가](media/grafana/add-graph.png)

1. 그래프 패널에서 **패널 제목**을 선택한 다음, **편집**을 선택합니다.

    ![패널 편집](media/grafana/edit-panel.png)

1. 패널의 맨 아래에서 **데이터 원본**을 선택한 다음, 사용자가 구성한 데이터 원본을 선택 합니다.

    ![데이터 원본 선택](media/grafana/select-data-source.png)

1. 쿼리 창에서 다음 쿼리를 복사한 다음, 선택 **실행**을 선택합니다. 쿼리는 샘플 데이터 세트에 대한 일별 이벤트 수를 버킷합니다.

    ```kusto
    StormEvents
    | summarize event_count=count() by bin(StartTime, 1d)
    ```

    ![쿼리 실행](media/grafana/run-query.png)

1. 기본적으로 지난 6시간의 데이터가 범위로 지정되므로 그래프에는 어떤 결과도 표시되지 않습니다. 상단 메뉴에서 **지난 6시간**을 선택합니다.

    ![지난 6시간](media/grafana/last-six-hours.png)

1. StormEvents 샘플 데이터 세트에 포함된 연도인 2007년을 포함하는 사용자 지정 범위를 지정합니다. **적용**을 선택합니다.

    ![사용자 지정 날짜 범위](media/grafana/custom-date-range.png)

    이제 그래프에서는 일별로 버킷된 2007년의 데이터를 보여줍니다.

    ![완성된 그래프](media/grafana/finished-graph.png)

1. 상단 메뉴에서 저장 아이콘을 선택합니다. ![저장 아이콘](media/grafana/save-icon.png).

## <a name="next-steps"></a>다음 단계

* [Azure 데이터 탐색기용 쿼리 작성](write-queries.md)

* [자습서: ](visualize-power-bi.md) Power BI에서 Azure Data Explorer의 데이터 시각화