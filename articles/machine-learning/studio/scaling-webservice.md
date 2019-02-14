---
title: Machine Learning Studio 웹 서비스 크기 조정
titleSuffix: Azure Machine Learning Studio
description: 엔드포인트를 추가하여 Azure Machine Learning Studio 웹 서비스의 동시성을 향상하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: article
author: ericlicoding
ms.author: amlstudiodocs
ms.custom: seodec18, previous-ms.author=yahajiza, previous-author=YasinMSFT
ms.date: 01/23/2017
ms.openlocfilehash: c5874029e17b0ad7c9787beb0177b8211cbf6e6b
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55512077"
---
# <a name="scaling-an-azure-machine-learning-studio-web-service-by-adding-additional-endpoints"></a>엔드포인트를 추가하여 Azure Machine Learning Studio 웹 서비스 확장
> [!NOTE]
> 이 항목에서는 **클래식** Machine Learning 웹 서비스에 적용되는 기술을 설명합니다. 
> 
> 

기본적으로 게시된 각각의 웹 서비스는 20개의 동시 요청을 지원하고 최대 200개의 동시 요청을 지원할 수 있도록 구성됩니다. Azure Machine Learning은 웹 서비스에 대한 최상의 성능을 제공하기 위해 설정을 자동으로 최적화하고 포털 값은 무시됩니다. 

최대 동시 호출 값인 200에서 지원하는 것보다 많은 부하를 가진 API를 호출하려는 경우 동일한 웹 서비스에서 여러 엔드포인트를 만들어야 합니다. 그런 다음 모든 끝점에 부하를 무작위로 분산해야 합니다.

웹 서비스의 크기를 조정하는 것은 일반적인 작업입니다. 크기를 조정하는 몇 가지 이유는 200개 이상의 동시 요청을 지원하거나, 여러 엔드포인트를 통해 가용성을 높이거나, 웹 서비스에 대한 별도의 엔드포인트를 제공하기 위해서 입니다. [Azure Machine Learning 웹 서비스](https://services.azureml.net/) 포털을 통해 동일한 웹 서비스에 대한 추가 엔드포인트를 추가하여 규모를 증가시킬 수 있습니다.

새로운 엔드포인트 추가에 대한 자세한 내용은 [엔드포인트 만들기](create-endpoint.md)를 참조하세요.

해당하는 높은 속도로 API를 호출하지 않을 경우 매우 높은 동시성 개수를 사용하는 것은 바람직하지 않습니다. 높은 부하로 구성된 API에 상대적으로 낮은 부하를 배치할 경우 가끔 시간 초과 및/또는 대기 시간 급증이 나타날 수 있습니다.

동기 API는 일반적으로 낮은 대기 시간을 원하는 경우에 사용됩니다. 여기서 대기 시간은 API가 하나의 요청을 완료하는 데 걸리는 시간을 의미하며 네트워크 지연을 고려하지 않습니다. 대기 시간이 50ms인 API가 있다고 가정합시다. 높음 제한 수준 및 최대 동시 호출 = 20으로 사용 가능한 용량을 완전히 사용하려면 이 API를 초당 20 * 1000 / 50 = 400회 호출해야 합니다. 더욱 확장하여 최대 동시 호출 수를 200으로 설정하면 대기 시간이 50ms일 경우 API를 초당 4000회 호출할 수 있습니다.

<!--Image references-->
[1]: ./media/scaling-webservice/machlearn-1.png
[2]: ./media/scaling-webservice/machlearn-2.png
