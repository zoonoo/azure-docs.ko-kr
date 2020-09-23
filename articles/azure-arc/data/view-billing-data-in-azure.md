---
title: 청구 데이터를 Azure에 업로드 하 고 Azure Portal에서 확인
description: 청구 데이터를 Azure에 업로드 하 고 Azure Portal에서 확인
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 375191bbe6f45189fba50ea927454c0ec4f64678
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90939084"
---
# <a name="upload-billing-data-to-azure-and-view-it-in-the-azure-portal"></a>청구 데이터를 Azure에 업로드 하 고 Azure Portal에서 확인

> [!IMPORTANT] 
>  미리 보기 기간 중에는 Azure Arc 사용 데이터 서비스를 사용 하는 비용이 없습니다. 청구 시스템이 종단 간 작동 하더라도 청구 측정기는 $0로 설정 됩니다.  이 시나리오를 수행 하는 경우 현재 **하이브리드 data services** 라는 서비스 및 microsoft 라는 유형의 리소스에 대 한 청구에 항목이 표시 됩니다 **. AzureData/ `<resource type>` **. 사용자가 만든 각 데이터 서비스에 대 한 레코드를 볼 수 있지만 각 레코드에는 $0에 대 한 요금이 청구 됩니다.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="connectivity-modes---implications-for-billing-data"></a>연결 모드-청구 데이터의 영향

향후에는 Azure Arc 사용 데이터 서비스를 실행할 수 있는 두 가지 모드가 있습니다.

- **간접적으로 연결** 됨-Azure에 직접 연결 되지 않습니다. 데이터는 내보내기/업로드 프로세스를 통해서만 Azure에 전송 됩니다. 모든 Azure Arc 데이터 서비스 배포는 현재 미리 보기로 제공 됩니다.
- **직접 연결** -이 모드에서는 azure Arc enabled Kubernetes 서비스에 대 한 종속성이 있어 azure arc 사용 데이터 서비스가 실행 되는 Kubernetes 클러스터와 azure 간에 직접 연결을 제공 합니다. 이렇게 하면 더 많은 기능을 사용할 수 있으며 Azure PaaS에서 데이터 서비스를 관리 하는 것 처럼 Azure Portal 및 Azure CLI를 사용 하 여 Azure Arc 사용 가능 데이터 서비스를 관리할 수도 있습니다.  이 연결 모드는 미리 보기에서 아직 사용할 수 없지만 곧 제공 될 예정입니다.

[연결 모드](/docs/connectivity.md)의 차이점에 대해 자세히 알아볼 수 있습니다.

간접적으로 연결 된 모드에서 청구 데이터는 주기적으로 Azure Arc 데이터 컨트롤러에서 보안 파일로 내보낸 다음 Azure에 업로드 되 고 처리 됩니다.  예정 된 직접 연결 모드에서 청구 데이터는 서비스 비용을 거의 실시간으로 확인 하기 위해 Azure로 자동으로 전송 됩니다. 간접적으로 연결 된 모드에서 데이터를 내보내고 업로드 하는 프로세스는 스크립트를 사용 하 여 자동화할 수도 있고 사용자에 게이 작업을 수행 하는 서비스를 빌드할 수도 있습니다.

## <a name="upload-billing-data-to-azure"></a>Azure에 청구 데이터 업로드

청구 데이터를 Azure에 업로드 하려면 먼저 다음을 수행 해야 합니다.

1. 아직 없는 경우 Azure Arc 사용 가능 데이터 서비스를 만듭니다. 예를 들어 다음 중 하나를 만듭니다.
   - [Azure Arc에서 Azure SQL 관리 되는 인스턴스 만들기](create-sql-managed-instance.md)
   - [Azure Arc enabled PostgreSQL Hyperscale 서버 그룹 만들기](create-postgresql-hyperscale-server-group.md)
1. 아직 없는 경우 [리소스 인벤토리, 사용 현황 데이터, 메트릭 및 로그를 Azure Monitor에 업로드](upload-metrics-and-logs-to-azure-monitor.md) 합니다.
1. 청구 원격 분석 수집 프로세스에서 일부 청구 데이터를 수집할 수 있도록 데이터 서비스를 만든 후 2 시간 이상 기다립니다.

다음 명령을 실행 하 여 청구 데이터를 내보냅니다.

```console
azdata arc dc export -t usage -p usage.json
```

지금은 콘텐츠를 볼 수 있도록 파일이 암호화 되지 않습니다. 텍스트 편집기에서 자유롭게 열어서 내용을 확인 합니다.

및의 두 가지 데이터 집합이 있습니다. `resources` `data` 는 `resources` 데이터 컨트롤러, PostgreSQL Hyperscale 서버 그룹 및 SQL 관리 되는 인스턴스입니다. `resources`데이터의 레코드는 리소스의 기록에 있는 관련 이벤트 (생성 된 경우), 업데이트 된 시간 및 삭제 된 시기를 캡처합니다. `data`레코드는 1 시간 마다 지정 된 인스턴스에서 사용할 수 있는 코어 수를 캡처합니다.

항목의 예 `resource` :

```console
    {
        "customObjectName": "<resource type>-2020-29-5-23-13-17-164711",
        "uid": "4bc3dc6b-9148-4c7a-b7dc-01afc1ef5373",
        "instanceName": "sqlInstance001",
        "instanceNamespace": "arc",
        "instanceType": "<resource>",
        "location": "eastus",
        "resourceGroupName": "production-resources",
        "subscriptionId": "482c901a-129a-4f5d-86e3-cc6b294590b2",
        "isDeleted": false,
        "externalEndpoint": "32.191.39.83:1433",
        "vCores": "2",
        "createTimestamp": "05/29/2020 23:13:17",
        "updateTimestamp": "05/29/2020 23:13:17"
    }
```

항목의 예 `data` :

```console
        {
          "requestType": "usageUpload",
          "clusterId": "4b0917dd-e003-480e-ae74-1a8bb5e36b5d",
          "name": "DataControllerTestName",
          "subscriptionId": "482c901a-129a-4f5d-86e3-cc6b294590b2",
          "resourceGroup": "production-resources",
          "location": "eastus",
          "uploadRequest": {
            "exportType": "usages",
            "dataTimestamp": "2020-06-17T22:32:24Z",
            "data": "[{\"name\":\"sqlInstance001\",
                       \"namespace\":\"arc\",
                       \"type\":\"<resource type>\",
                       \"eventSequence\":1, 
                       \"eventId\":\"50DF90E8-FC2C-4BBF-B245-CB20DC97FF24\",
                       \"startTime\":\"2020-06-17T19:11:47.7533333\",
                       \"endTime\":\"2020-06-17T19:59:00\",
                       \"quantity\":1,
                       \"id\":\"4BC3DC6B-9148-4C7A-B7DC-01AFC1EF5373\"}]",
           "signature":"MIIE7gYJKoZIhvcNAQ...2xXqkK"
          }
        }
```

다음 명령을 실행 하 여 파일의 usage.js를 Azure에 업로드 합니다.

```console
azdata arc dc upload -p usage.json
```

## <a name="view-billing-data-in-azure-portal"></a>Azure Portal에서 청구 데이터 보기

Azure Portal에서 청구 데이터를 보려면 다음 단계를 따르세요.

1. 특수 URL을 사용 하 여 Azure Portal를 엽니다  [https://aka.ms/arcdata](https://aka.ms/arcdata) .
1. 화면 맨 위의 검색 상자에 **Cost Management** 를 입력 하 고 Cost Management 서비스를 클릭 합니다.
1. 왼쪽의 **비용 분석** 탭을 클릭 합니다.
1. 보기 위쪽의 **리소스 비용** 단추를 클릭 합니다.
1. 범위가 데이터 서비스 리소스를 만든 구독으로 설정 되어 있는지 확인 합니다.
1. 보기 위쪽의 범위 선택기 옆에 있는 보기 드롭다운에서 **리소스 별로 비용** 을 선택 합니다.
1. 데이터 서비스 리소스를 만들 때 타이밍을 제공 하는 경우 날짜 필터가 **이번 달** 또는 다른 시간 범위로 설정 되었는지 확인 합니다.
1. **Add filter** **Resource type**  =  `microsoft.azuredata/<data service type>` Azure Arc 사용 설정 데이터 서비스의 한 가지 유형 으로만 필터링 하려는 경우 필터 추가를 클릭 하 여 리소스 유형별로 필터를 추가 합니다.
1. 이제 Azure에 생성 및 업로드 된 모든 리소스의 목록이 표시 됩니다. 청구 측정기가 $0 이므로 비용은 항상 $0이 됩니다.

## <a name="download-billing-data"></a>청구 데이터 다운로드

Azure Portal에서 직접 청구 요약 데이터를 다운로드할 수 있습니다.

1. 위의 지침에 따라 도달 하는 동일한 **비용 분석-리소스 유형** 보기에의 한 > 보기에서 위쪽에 있는 다운로드 단추를 클릭 합니다.
1. 다운로드 파일 유형 (Excel 또는 CSV)을 선택 하 고 **데이터 다운로드** 단추를 클릭 합니다.
1. 선택한 파일 형식이 지정 된 경우 해당 편집기에서 파일을 엽니다.

## <a name="export-billing-data"></a>청구 데이터 내보내기

청구 내보내기 작업을 만들어 정기적으로 자동으로 **자세한** 사용량 및 청구 데이터를 Azure Storage 컨테이너에 내보낼 수도 있습니다. 이 기능은 청구 기간에 지정 된 인스턴스가 청구 된 시간 등의 청구 세부 정보를 보려는 경우에 유용 합니다.

청구 내보내기 작업을 설정 하려면 다음 단계를 수행 합니다.

1. 왼쪽에서 내보내기를 클릭 합니다.
1. 추가를 클릭합니다.
1. 이름 및 내보내기 빈도를 입력 하 고 다음을 클릭 합니다.
1. 새 저장소 계정을 만들거나 새 저장소 계정을 만들고, 양식을 작성 하 여 청구 데이터 파일을 내보낼 저장소 계정, 컨테이너 및 디렉터리 경로를 지정 하 고 다음을 클릭 합니다.
1. 만들기를 클릭합니다.

청구 데이터 내보내기 파일은 약 4 시간 이내에 사용할 수 있으며, 청구 내보내기 작업을 만들 때 지정한 일정에 따라 내보내집니다.

내보내는 청구 데이터 파일을 보려면 다음 단계를 따르세요.

Azure Portal에서 청구 데이터 파일의 유효성을 검사할 수 있습니다. 

> [!IMPORTANT]
> 청구 내보내기 작업을 만든 후 4 시간 동안 기다린 후 다음 단계를 진행 합니다.

1. 포털 맨 위에 있는 검색 상자에 **저장소 계정** 을 입력 하 고 **저장소 계정**을 클릭 합니다.
3. 위에서 청구 내보내기 작업을 만들 때 지정한 저장소 계정을 클릭 합니다.
4. 왼쪽의 컨테이너를 클릭 합니다.
5. 위에서 청구 내보내기 작업을 만들 때 지정한 컨테이너를 클릭 합니다.
6. 위에서 청구 내보내기 작업을 만들 때 지정한 폴더를 클릭 합니다.
7. 생성 된 폴더 및 파일을 드릴 다운 하 고 생성 된 .csv 파일 중 하나를 클릭 합니다.
8. 다운로드 단추를 클릭 하 여 로컬 다운로드 폴더에 파일을 저장 합니다.
9. Excel과 같은 .csv 파일 뷰어를 사용 하 여 파일을 엽니다.
10. **리소스 유형의**행만 표시 하도록 결과를 필터링  =  `Microsoft.AzureData/<data service resource type` 합니다.
11. 인스턴스를 사용 하는 데 사용 된 시간을 현재 24 시간에서 사용 된 시간 (%)이 표시 됩니다.
