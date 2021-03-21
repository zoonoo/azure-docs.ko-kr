---
title: Azure CLI를 사용 하 여 Azure Time Series Insights Gen2 환경 만들기-Azure Time Series Insights Gen2 | Microsoft Docs
description: Azure CLI를 사용 하 여 Azure Time Series Insights Gen2에서 환경을 설정 하는 방법에 대해 알아봅니다.
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: how-to
ms.date: 03/15/2021
ms.custom: seodec18
ms.openlocfilehash: ed185413cff155610b2b088b1791169e33f6ce7a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103464481"
---
# <a name="create-an-azure-time-series-insights-gen2-environment-using-the-azure-cli"></a>Azure CLI를 사용 하 여 Azure Time Series Insights Gen2 환경을 만듭니다.

이 문서에서는 새 Time Series Insights Gen2 환경을 만드는 과정을 안내 합니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>필수 조건

* 환경의 [콜드 저장소](./concepts-storage.md#cold-store)에 대한 Azure 스토리지 계정을 만듭니다. 이 계정은 기록 데이터의 장기 보존 및 분석을 위해 설계되었습니다.

> [!NOTE]
> 코드에서 `mytsicoldstore`를 콜드 스토리지 계정의 고유한 이름으로 바꿉니다.

먼저 저장소 계정을 만듭니다.

```azurecli-interactive
storage=mytsicoldstore
rg=-my-resource-group-name
az storage account create -g $rg -n $storage --https-only
key=$(az storage account keys list -g $rg -n $storage --query [0].value --output tsv
```

## <a name="creating-the-environment"></a>환경 만들기

저장소 계정을 만들고 해당 이름 및 관리 키를 변수에 할당 했으므로 아래 명령을 실행 하 여 Azure Time Series Insights 환경을 만듭니다.

> [!NOTE]
> 코드에서 다음을 시나리오의 고유한 이름으로 바꿉니다.
>
> * `my-tsi-env` 사용자 환경 이름으로 바꿉니다.
> * `my-ts-id-prop` Time Series Id 속성의 이름으로 바꿉니다.

> [!IMPORTANT]
> 환경 시계열 ID는 데이터베이스 파티션 키와 같습니다. 시계열 ID는 시계열 모델에 대 한 기본 키로도 작동 합니다.
>
> 자세한 내용은 [시계열 ID 선택을 위한 모범 사례](./how-to-select-tsid.md) 를 참조 하세요.

```azurecli-interactive
az tsi environment gen2 create --name "my-tsi-env" --location eastus2 --resource-group $rg --sku name="L1" capacity=1 --time-series-id-properties name=my-ts-id-prop type=String --warm-store-configuration data-retention=P7D --storage-configuration account-name=$storage management-key=$key
```

## <a name="remove-an-azure-time-series-insights-environment"></a>Azure Time Series Insights 환경 제거

Azure CLI를 사용 하 여 개별 리소스 (예: Time Series Insights 환경)를 삭제 하거나 리소스 그룹 및 모든 Time Series Insights 환경을 비롯 한 모든 리소스를 삭제할 수 있습니다.

[Time Series Insights 환경을 삭제](/cli/azure/ext/timeseriesinsights/tsi/environment?view=azure-cli-latest#ext_timeseriesinsights_az_tsi_environment_delete)하려면 다음 명령을 실행 합니다.

```azurecli-interactive
az tsi environment delete --name "my-tsi-env" --resource-group $rg
```

[저장소 계정을 삭제](/cli/azure/storage/account?view=azure-cli-latest#az_storage_account_delete)하려면 다음 명령을 실행 합니다.

```azurecli-interactive
az storage account delete --name $storage --resource-group $rg
```

[리소스 그룹](/cli/azure/group#az-group-delete) 및 모든 해당 리소스를 삭제 하려면 다음 명령을 실행 합니다.

```azurecli-interactive
az group delete --name $rg
```

## <a name="next-steps"></a>다음 단계

* Azure Time Series Insights Gen2 환경에 대 한 [스트리밍 수집 이벤트 원본](./concepts-streaming-ingestion-event-sources.md) 에 대해 알아봅니다.
* [IoT Hub](./how-to-ingest-data-iot-hub.md) 에 연결 하는 방법을 알아봅니다.
