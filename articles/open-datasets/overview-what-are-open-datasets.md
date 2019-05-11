---
title: 공개 데이터 세트란? 큐레이팅된 공개 데이터 세트
titleSuffix: Azure Open Datasets (preview)
description: 공개 도메인에서 얻은 데이터 세트를 기계 학습 및 분석 솔루션에 바로 사용할 수 있도록 큐레이팅한 Azure Open Datasets(미리 보기)에 대해 알아봅니다. 데이터 세트에는 예측 솔루션을 보강하는 데 도움이 되는 날씨, 인구, 휴일, 위치 등의 공개 데이터가 포함되어 있습니다.
ms.topic: overview
author: cjgronlund
ms.author: cgronlun
ms.date: 05/02/2019
ms.openlocfilehash: 439c25363d4c3b24b391b49811d3806c98171034
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65030021"
---
# <a name="what-are-azure-open-datasets-preview-and-how-can-you-use-them"></a>Azure Open Datasets(미리 보기)란 무엇이며 사용하려면 어떻게 해야 하나요?

[Azure Open Datasets](https://azure.microsoft.com/services/open-datasets/)는 기계 학습 솔루션에 시나리오별 기능을 추가하여 보다 정확한 모델을 만들 수 있는 큐레이팅된 공개 데이터 세트입니다. Open Datasets는 Microsoft Azure의 클라우드에 있으며 Azure Databricks, Machine Learning Service 및 Machine Learning Studio에서 즉시 사용할 수 있습니다. API를 통해 데이터 세트에 액세스하고 Power BI나 Azure Data Factory 같은 다른 제품에서 데이터 세트를 사용할 수도 있습니다.

데이터 세트에는 기계 학습 모델을 학습시키고 예측 솔루션을 보강할 수 있는 날씨, 인구, 휴일, 공공 안전 및 위치에 대한 공개 도메인 데이터가 포함되어 있습니다. 또한 공개 데이터 세트를 Azure Open Datasets에 공유할 수 있습니다. 

![Azure Open Datasets 구성 요소](./media/overview-what-are-open-datasets/open-datasets-components.png)

## <a name="curated-prepared-datasets"></a>큐레이팅 및 준비를 마친 데이터 세트
Azure Open Datasets의 큐레이팅된 공개 데이터 세트는 기계 학습 워크플로에 사용하도록 최적화되었습니다. 

데이터 과학자들은 종종 고급 분석을 위해 데이터를 정리하고 준비하는 일에 대부분의 시간을 보냅니다. Open Datasets는 Azure 클라우드에 복사되어 미리 처리되므로 시간을 절약할 수 있습니다. 정기적으로 NOAA(해양대기청) 같은 소스에 FTP로 연결하여 데이터를 끌어오고 정형 형식으로 구문 분석한 다음, 가장 가까운 기상 관측소의 우편 번호나 위치 같은 기능을 사용하여 적절하게 데이터를 보강합니다.

데이터 세트는 클라우드 컴퓨팅을 통해 Azure에 공동으로 호스트되므로 쉽게 액세스하여 조작할 수 있습니다.  

다음은 사용 가능한 데이터 세트의 예입니다. 

### <a name="weather-data"></a>날씨 데이터
 
|데이터 세트         | Notebooks     | 설명                                    |
|----------------|---------------|------------------------------------------------|
|[NOAA ISD(Integrated Surface Data)](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/) | [Azure 노트](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/?tab=data-access#AzureNotebooks) <br> [Azure Databricks](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/?tab=data-access#AzureDatabricks) | NOAA에서 제공하는 전 세계의 시간별 날씨 데이터로 북아메리카, 유럽, 오스트레일리아 및 아시아 일부 지역을 아우릅니다. 매일 업데이트됩니다. |
|[NOAA GFS(Global Forecast System)](https://azure.microsoft.com/services/open-datasets/catalog/noaa-global-forecast-system/) | [Azure 노트](https://azure.microsoft.com/services/open-datasets/catalog/noaa-global-forecast-system/?tab=data-access#AzureNotebooks) <br> [Azure Databricks](https://azure.microsoft.com/services/open-datasets/catalog/noaa-global-forecast-system/?tab=data-access#AzureDatabricks) | NOAA에서 제공하는 15일 미국 시간별 일기 예보 데이터입니다. 매일 업데이트됩니다. |

### <a name="calendar-data"></a>달력 데이터

|데이터 세트         | Notebooks     | 설명                                    |
|----------------|---------------|------------------------------------------------|
|[공휴일](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/) | [Azure 노트](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/?tab=data-access#AzureNotebooks) <br> [Azure Databricks](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/?tab=data-access#AzureDatabricks) | 전 세계 41개 국가 또는 지역의 1970-2099년 공유일 데이터입니다. 국가 및 대부분의 사람들이 유급 휴가를 받는지 여부가 포함되어 있습니다. |

## <a name="access-to-datasets"></a>데이터 세트 액세스  
Azure 계정이 있으면 코드를 사용하여 또는 Azure 서비스 인터페이스를 통해 공개 데이터 세트에 액세스할 수 있습니다. 데이터는 기계 학습 솔루션에 사용할 수 있도록 Azure 클라우드 컴퓨팅 리소스와 함께 배치됩니다.  

Open Datasets는 Azure Machine Learning Service 및 Azure Databricks에 데이터를 연결하는 데 사용할 수 있는 Azure Notebooks 및 Azure Databricks Notebook을 제공합니다. Python SDK를 통해 데이터 세트에 액세스할 수도 있습니다. 

하지만 Open Datasets에 액세스할 때 Azure 계정이 필요 없습니다. 모든 Python 환경에서 Spark 없이 액세스할 수 있습니다.

## <a name="request-or-contribute-datasets"></a>데이터 세트 요청 또는 기여

원하는 데이터를 찾을 수 없는 경우 이메일로 [데이터 세트를 요청](mailto:aod@microsoft.com?Subject=Contribute%20dataset%3A%20%3Creplace%20with%20dataset%20name%3E&Body=%0AYour%20name%20and%20institution%3A%20%0A%0ADataset%20name%3A%0A%20%0ADataset%20description%3A%20%0A%3Cfill%20in%20a%20brief%20description%20and%20share%20any%20web%20links%20of%20the%20dataset%3E%20%0A%0ADataset%20size%3A%20%0A%3Chow%20much%20space%20does%20the%20dataset%20need%20today%20and%20how%20much%20is%20it%20expected%20to%20grow%20each%20year%3E%20%0A%0ADataset%20file%20formats%3A%20%0A%3Ccurrent%20dataset%20file%20formats%2C%20and%20optionally%2C%20any%20formats%20that%20the%20dataset%20must%20be%20transformed%20to%20for%20easy%20access%3E%0A%0ALicense%3A%20%0A%3Cwhat%20is%20the%20license%20or%20terms%20and%20conditions%20governing%20the%20distribution%20of%20this%20dataset%3E%0A%0AUse%20cases%3A%20%0A%3CExplain%20some%20common%20use%20of%20the%20dataset.%20E.g.%20weather%20dataset%20can%20be%20useful%20in%20demand%20forecasting%20and%20predictive%20maintenance%20scenarios%3E%20%0A%0AAny%20additional%20information%20you%20want%20us%20to%20know%3A%0A)하거나 [데이터 세트에 기여](mailto:aod@microsoft.com?Subject=Request%20dataset%3A%20%3Creplace%20with%20dataset%20name%3E&Body=%0AYour%20name%20and%20institution%3A%20%0A%0ADataset%20name%3A%0A%20%0ADataset%20description%3A%20%0A%3Cfill%20in%20a%20brief%20description%20and%20share%20any%20web%20links%20of%20the%20dataset%3E%20%0A%0ADataset%20size%3A%20%0A%3Chow%20much%20space%20does%20the%20dataset%20need%20today%20and%20how%20much%20is%20it%20expected%20to%20grow%20each%20year%3E%20%0A%0ADataset%20file%20formats%3A%20%0A%3Ccurrent%20dataset%20file%20formats%2C%20and%20optionally%2C%20any%20formats%20that%20the%20dataset%20must%20be%20transformed%20to%20for%20easy%20access%3E%0A%0ALicense%3A%20%0A%3Cwhat%20is%20the%20license%20or%20terms%20and%20conditions%20governing%20the%20distribution%20of%20this%20dataset%3E%0A%0AUse%20cases%3A%20%0A%3CExplain%20some%20common%20use%20of%20the%20dataset.%20E.g.%20weather%20dataset%20can%20be%20useful%20in%20demand%20forecasting%20and%20predictive%20maintenance%20scenarios%3E%20%0A%0AAny%20additional%20information%20you%20want%20us%20to%20know%3A%0A)해주세요. 

## <a name="next-steps"></a>다음 단계
* [샘플 Notebook](samples.md)
* [자습서: 뉴욕 택시 데이터를 사용하는 회귀 모델링](tutorial-opendatasets-automl.md)
* [공개 데이터 세트용 Python SDK](https://aka.ms/open-datasets-api)
