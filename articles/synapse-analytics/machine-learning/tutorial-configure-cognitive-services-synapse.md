---
title: '자습서: Azure Synapse Analytics의 Cognitive Services에 대한 필수 구성 요소'
description: Azure Synapse에서 Cognitive Services를 사용하기 위한 필수 구성 요소를 구성하는 방법을 알아봅니다.
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 11/20/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: 3ab861caca0ef6f58c2c1bc722412774deb725ce
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99821426"
---
# <a name="tutorial-prerequisites-for-using-cognitive-services-in-azure-synapse-analytics"></a>자습서: Azure Synapse Analytics의 Cognitive Services 사용을 위한 필수 구성 요소

이 자습서에서는 Azure Synapse Analytics에서 Azure Cognitive Services를 안전하게 사용하기 위해 필수 구성 요소를 설정하는 방법을 알아봅니다.

이 자습서에서는 다음 내용을 다룹니다.
> [!div class="checklist"]
> - Text Analytics 또는 Anomaly Detector와 같은 Cognitive Services 리소스를 만듭니다.
> - Azure Key Vault에서 인증 키를 비밀로 Cognitive Services 리소스에 저장하고 Azure Synapse Analytics 작업 영역에 대한 액세스를 구성합니다.
> - Azure Synapse Analytics 작업 영역에서 Azure Key Vault 연결된 서비스를 만듭니다.

Azure 구독이 없는 경우 [시작하기 전에 체험 계정을 만듭니다](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>필수 구성 요소

- [Azure Synapse Analytics 작업 영역](../get-started-create-workspace.md)(기본 스토리지로 구성된 Azure Data Lake Storage Gen2 스토리지 계정이 있음). 사용하는 Azure Data Lake Storage Gen2 파일 시스템의 *Storage Blob 데이터 기여자* 여야 합니다.

## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

[Azure Portal](https://portal.azure.com/)에 로그인합니다.

## <a name="create-a-cognitive-services-resource"></a>Cognitive Services 리소스 만들기

[Azure Cognitive Services](../../cognitive-services/index.yml)는 다양한 유형의 서비스를 포함합니다. Text Analytics 및 Anomaly Detector는 Azure Synapse 자습서의 두 가지 예제입니다.

Azure Portal에서 [Text Analytics](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) 리소스를 만들 수 있습니다.

![포털의 Text Analytics와 만들기 단추를 보여 주는 스크린샷](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00b.png)

Azure Portal에서 [Anomaly Detector](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) 리소스를 만들 수 있습니다.

![포털의 Anomaly Detector와 만들기 단추를 보여 주는 스크린샷](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00a.png)

## <a name="create-a-key-vault-and-configure-secrets-and-access"></a>키 자격 증명 모음 만들기 및 비밀과 액세스 구성

1. Azure Portal에서 [키 자격 증명 모음](https://ms.portal.azure.com/#create/Microsoft.KeyVault)을 만듭니다.
2. **Key Vault** > **액세스 정책** 으로 이동하여 Azure Key Vault에서 비밀을 읽을 수 있도록 [Azure Synapse 작업 영역 MSI](../security/synapse-workspace-managed-identity.md) 권한을 부여합니다.

   > [!NOTE]
   > 정책 변경 내용을 저장했는지 확인합니다. 이 단계는 놓치기 쉽습니다.

   ![액세스 정책 추가를 위한 선택 항목을 보여 주는 스크린샷](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00c.png)

3. Cognitive Services 리소스로 이동합니다. 예를 들어 **Anomaly Detector** > **키 및 엔드포인트** 로 이동합니다. 그런 다음, 두 키 중 하나를 클립보드에 복사합니다.

4. **Key Vault** > **비밀** 로 이동하여 새 비밀을 만듭니다. 비밀의 이름을 지정한 다음, 이전 단계의 키를 **값** 필드에 붙여넣습니다. 마지막으로 **만들기** 를 선택합니다.

   ![비밀을 만들기 위한 선택 항목을 보여 주는 스크린샷](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00d.png)

   > [!IMPORTANT]
   > 이 비밀 이름을 기억하거나 적어 두어야 합니다. 나중에 Azure Synapse Studio에서 Cognitive Services에 연결할 때 이 비밀을 사용하게 됩니다.

## <a name="create-an-azure-key-vault-linked-service-in-azure-synapse"></a>Azure Synapse에서 Azure Key Vault 연결된 서비스 만들기

1. Azure Synapse Studio에서 작업 영역을 엽니다. 
2. **관리** > **연결된 서비스** 로 이동합니다. 방금 만든 키 자격 증명 모음을 가리키는 **Azure Key Vault** 연결된 서비스를 만듭니다. 
3. **연결 테스트** 단추를 선택하여 연결을 확인합니다. 연결이 녹색이면 **만들기** 를 선택한 다음, **모두 게시** 를 선택하여 변경 내용을 저장합니다.

![새 연결된 서비스인 Azure Key Vault를 보여 주는 스크린샷](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00e.png)

이제 Azure Synapse Studio에서 Azure Cognitive Services 환경을 사용하기 위한 자습서 중 하나를 계속 진행할 준비가 되었습니다.

## <a name="next-steps"></a>다음 단계

- [자습서: Azure Cognitive Services를 사용한 감정 분석](tutorial-cognitive-services-sentiment.md)
- [자습서: Azure Cognitive Services를 사용하여 변칙 검색](tutorial-cognitive-services-sentiment.md)
- [자습서: Azure Synapse 전용 SQL 풀에서 기계 학습 모델 점수 매기기](tutorial-sql-pool-model-scoring-wizard.md)
- [Azure Synapse Analytics의 Machine Learning 기능](what-is-machine-learning.md)