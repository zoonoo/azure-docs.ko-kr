---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 12/01/2020
ms.author: larryfr
ms.openlocfilehash: e92d3ac9ed4330cc1680428a426e881538cb0e78
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102623444"
---
* 새 작업 영역을 만들 때 작업 영역에 필요한 서비스를 자동으로 만들거나 기존 서비스를 사용할 수 있습니다. 작업 영역과 __다른 Azure 구독에서 기존 서비스__ 를 사용 하려면 해당 서비스를 포함 하는 구독에 Azure Machine Learning 네임 스페이스를 등록 해야 합니다. 예를 들어 구독 B의 저장소 계정을 사용 하는 구독 A에 작업 영역을 만들면 작업 영역에서 저장소 계정을 사용 하려면 먼저 Azure Machine Learning 네임 스페이스를 구독 B에 등록 해야 합니다.

    Azure Machine Learning에 대 한 리소스 공급자는 __MachineLearningServices__ 입니다. 등록 여부를 확인 하는 방법과 등록 하는 방법에 대 한 자세한 내용은 [Azure 리소스 공급자 및 형식](../articles/azure-resource-manager/management/resource-providers-and-types.md)  문서를 참조 하세요.

    > [!IMPORTANT]
    > 이는 작업 영역을 만드는 동안 제공 된 리소스에만 적용 됩니다. Azure Storage 계정, Azure Container Register, Azure Key Vault 및 Application Insights.
