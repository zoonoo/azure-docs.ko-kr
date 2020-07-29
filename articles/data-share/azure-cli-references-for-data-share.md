---
title: Azure Data Share에 대한 Azure CLI 참조
description: Azure Data Share에 대한 Azure CLI 참조 방문 페이지
services: data-share
author: dbradish-microsoft
manager: barbkess
ms.service: data-share
ms.devlang: azurecli
ms.topic: reference
ms.date: 05/27/2020
ms.author: dbradish
ms.openlocfilehash: 404022b13f44174e4b647f0430a58fac5eeb81df
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87298481"
---
# <a name="azure-cli-for-azure-data-share"></a>Azure Data Share에 대한 Azure CLI

[Azure CLI(Azure 명령줄 인터페이스)](/cli/azure/what-is-azure-cli)는 Azure 리소스를 만들고 관리하는 데 사용되는 명령 세트입니다.  Azure Data Share를 포함한 여러 Azure 서비스에서 사용할 수 있습니다.  데이터 공유를 위한 65개가 넘는 명령이 있습니다.  이들 명령을 사용하면 명령줄에서 서비스를 효과적으로 사용할 수 있습니다.

## <a name="references-for-data-share"></a>데이터 공유에 대한 참조

Azure Data Share에 대한 모든 Azure CLI 명령은 현재 Azure CLI의 확장입니다.  확장을 통해 실험 및 릴리스 전 명령에 액세스할 수 있습니다.  [Azure CLI에서 확장 사용](/cli/azure/azure-cli-extensions-overview)에서 확장 참조에 대해 자세히 알아봅니다.

|Azure CLI 참조 |설명
|-|-|-|
| [az datashare](/cli/azure/ext/datashare/datashare) | Azure Data Share를 관리하는 모든 명령입니다.
| [az datashare account](/cli/azure/ext/datashare/datashare/account) | Azure Data Share 계정을 관리하는 명령입니다.
| [az datashare consumer](/cli/azure/ext/datashare/datashare/consumer) | Azure Data Share를 관리하는 소비자를 위한 명령입니다.
| [az datashare dataset](/cli/azure/ext/datashare/datashare/dataset) | Azure Data Share 데이터 세트를 관리하는 공급자를 위한 명령입니다.
| [az datashare invitation](/cli/azure/ext/datashare/datashare/invitation) | Azure Data Share 초대를 관리하는 소비자를 위한 명령입니다.
| [az datashare provider-share-subscription](/cli/azure/ext/datashare/datashare/provider-share-subscription) | Azure Data Share 구독을 관리하는 공급자를 위한 명령입니다.
| [az datashare synchronization](/cli/azure/ext/datashare/datashare/synchronization)  | Azure Data Share 동기화를 관리하는 명령입니다.
| [az datashare synchronization-setting](/cli/azure/ext/datashare/datashare/synchronization-setting)  | Azure Data Share 동기화 설정을 관리하는 공급자를 위한 명령입니다.

## <a name="reference-examples"></a>참조 예제

예제는 모든 Azure CLI 참조에서 제공됩니다. 이러한 작업은 Azure Portal을 통해 완료할 수도 있지만 Azure CLI를 사용하려면 단일 명령줄이 필요합니다.  Azure CLI를 얼마나 쉽게 사용할 수 있는지 이해할 수 있는 몇 가지 코드 블록은 다음과 같습니다.

Azure Data Share를 사용하려면 먼저 리소스 그룹이 필요합니다.  Azure 리소스 그룹은 Azure CLI를 사용하여 간편하게 만들고 관리할 수 있습니다.  

```azurecli
#create a resource group
az group create -location westus -name MyResourceGroup
```

```azurecli
#get a list of resource groups for a subscription
az group list --subscription MySubscription --output table
```

데이터 공유 계정을 만드는 것은 간단합니다.

```azurecli
#create a data share account
az datashare account create --location "West US 2" --tags tag1=Red tag2=White --name MyAccount --resource-group MyResourceGroup
```

## <a name="see-also"></a>참고 항목

* [Azure CLI로 시작](/cli/azure/get-started-with-azure-cli)에서 설치 및 로그인에 대해 알아봅니다.

* Azure CLI 설명서에서 추가 [코어](/cli/azure/reference-index) 및 [확장](/cli/azure/azure-cli-extensions-list) 참조를 검색합니다.
