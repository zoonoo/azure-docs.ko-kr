---
title: Azure CLI를 사용하여 Azure Time Series Insights Gen2 환경 만들기 - Azure Time Series Insights Gen2 | Microsoft Docs
description: Azure CLI를 사용하여 Azure Time Series Insights Gen2에서 환경을 설정하는 방법을 알아보세요.
author: tedvilutis
ms.author: tvilutis
manager: cnovak
ms.reviewer: orspodek
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: how-to
ms.date: 03/15/2021
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 36a6da683912ac004b948b9a334da1758ab903fd
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/02/2021
ms.locfileid: "110791881"
---
# <a name="create-an-azure-time-series-insights-gen2-environment-using-the-azure-cli"></a>Azure CLI를 사용하여 Azure Time Series Insights Gen2 환경 만들기

이 문서에서는 새 Time Series Insights Gen2 환경을 만드는 과정을 안내합니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>사전 요구 사항

* 환경의 [콜드 저장소](./concepts-storage.md#cold-store)에 대한 Azure 스토리지 계정을 만듭니다. 이 계정은 기록 데이터의 장기 보존 및 분석을 위해 설계되었습니다.

> [!NOTE]
> 코드에서 `mytsicoldstore`를 콜드 스토리지 계정의 고유한 이름으로 바꿉니다.

먼저 스토리지 계정을 만듭니다.

```azurecli-interactive
storage=mytsicoldstore
rg=-my-resource-group-name
az storage account create -g $rg -n $storage --https-only
key=$(az storage account keys list -g $rg -n $storage --query [0].value --output tsv
```

## <a name="creating-the-environment"></a>환경 만들기

이제 스토리지 계정을 만들고 해당 이름과 관리 키를 변수에 할당했으므로 아래 명령을 실행하여 Azure Time Series Insights 환경을 만듭니다.

> [!NOTE]
> 코드에서 다음을 시나리오의 고유한 이름으로 바꿉니다.
>
> * `my-tsi-env`를 환경 이름으로 바꿉니다.
> * `my-ts-id-prop`를 Time Series ID 속성의 이름으로 바꿉니다.

> [!IMPORTANT]
> 환경의 시계열 ID는 데이터베이스 파티션 키와 비슷합니다. 시계열 ID는 시계열 모델의 기본 키 역할도 합니다.
>
> 자세한 내용은 [시계열 ID 선택 모범 사례](./how-to-select-tsid.md)를 참조하세요.

```azurecli-interactive
az tsi environment gen2 create --name "my-tsi-env" --location eastus2 --resource-group $rg --sku name="L1" capacity=1 --time-series-id-properties name=my-ts-id-prop type=String --warm-store-configuration data-retention=P7D --storage-configuration account-name=$storage management-key=$key
```

## <a name="remove-an-azure-time-series-insights-environment"></a>Azure Time Series Insights 환경 제거

Azure CLI를 사용하여 개별 리소스(예: Time Series Insights 환경)를 삭제하거나 모든 Time Series Insights 환경을 포함한 리소스 그룹 및 모든 리소스를 삭제할 수 있습니다.

[Time Series Insights 환경을 삭제](/cli/azure/tsi/environment#az_tsi_environment_delete)하려면 다음 명령을 실행합니다.

```azurecli-interactive
az tsi environment delete --name "my-tsi-env" --resource-group $rg
```

[스토리지 계정을 삭제](/cli/azure/storage/account#az_storage_account_delete)하려면 다음 명령을 실행합니다.

```azurecli-interactive
az storage account delete --name $storage --resource-group $rg
```

모든 해당 리소스 및 [리소스 그룹을 삭제](/cli/azure/group#az_group_delete)하려면 다음 명령을 실행합니다.

```azurecli-interactive
az group delete --name $rg
```

## <a name="next-steps"></a>다음 단계

* Azure Time Series Insights Gen2 환경의 [스트리밍 수집 이벤트 원본](./concepts-streaming-ingestion-event-sources.md)에 대해 알아보세요.
* [IoT Hub](./how-to-ingest-data-iot-hub.md)에 연결하는 방법 알아보기
