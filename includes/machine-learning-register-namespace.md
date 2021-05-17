---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 12/01/2020
ms.author: larryfr
ms.openlocfilehash: e92d3ac9ed4330cc1680428a426e881538cb0e78
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102623444"
---
* 새 작업 영역을 만들 때 작업 영역에 필요한 서비스를 자동으로 만들거나 기존 서비스를 사용할 수 있습니다. 작업 영역과 __다른 Azure 구독의 기존 서비스__ 를 사용하려면 해당 서비스가 포함된 구독에 Azure Machine Learning 네임스페이스를 등록해야 합니다. 예를 들어, 구독 B의 스토리지 계정을 사용하는 구독 A에서 작업 영역을 만드는 경우 작업 영역에서 스토리지 계정을 사용하려면 먼저 Azure Machine Learning 네임스페이스를 구독 B에 등록해야 합니다.

    Azure Machine Learning의 리소스 공급자는 __Microsoft.MachineLearningServices__ 입니다. 등록 여부를 확인하는 방법 및 등록하는 방법에 대한 자세한 내용은 [Azure 리소스 공급자 및 형식](../articles/azure-resource-manager/management/resource-providers-and-types.md) 문서를 참조하세요.

    > [!IMPORTANT]
    > 이는 Azure Storage Accounts, Azure Container Register, Azure Key Vault 및 Application Insights 등의 작업 영역을 만드는 동안 제공된 리소스에만 적용됩니다.
