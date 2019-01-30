---
title: Azure Batch AI에 대한 새로운 소식 | Microsoft Docs
description: Azure Batch AI 및 Azure Machine Learning Service 컴퓨팅 옵션의 최신 소식을 알아봅니다.
services: batch-ai
author: garyericson
ms.service: batch-ai
ms.topic: overview
ms.date: 12/14/2018
ms.author: garye
ms.openlocfilehash: 6803a47ae77c072eff05df65e37156c90aabf3e6
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/15/2018
ms.locfileid: "53436878"
---
# <a name="whats-happening-to-azure-batch-ai"></a>Azure Batch AI에 대한 새로운 소식

**Azure Batch AI 서비스가 3월에 사용 중지됩니다.** Batch AI의 대규모 학습 및 채점 기능을 이제 [Azure Machine Learning Service](../machine-learning/service/overview-what-is-azure-ml.md)에서 사용할 수 있습니다(2018년 12월 4일 일반 제공됨).

Azure Machine Learning Service에는 다양한 다른 기계 학습 기능과 함께 기계 학습 모델을 학습시키고, 배포하며, 채점하기 위한 클라우드 기반 관리형 컴퓨팅 대상이 포함되어 있습니다. 이 컴퓨팅 대상을 [Azure Machine Learning 컴퓨팅](../machine-learning/service/how-to-set-up-training-targets.md#amlcompute)이라고 합니다. [지금 마이그레이션하고 사용을 시작](#migrate)해 보세요. [Python SDK](../machine-learning/service/quickstart-create-workspace-with-python.md), 명령줄 인터페이스 및 [Azure Portal](../machine-learning/service/quickstart-get-started.md)을 통해 Azure Machine Learning Service와 상호 작용할 수 있습니다.

## <a name="support-timeline"></a>지원 타임라인

| Date | Batch AI 서비스 지원 세부 정보 |
| ---- |-----------------|
| 2018년 12월&nbsp;14일&nbsp;| 기존 Azure Batch AI 구독을 이전과 같이 계속 사용합니다. 그러나 **새 구독**을 등록하는 것은 불가능해지며 이 서비스에 대해 새 투자도 이루어지지 않습니다.|
| 2019년 3월&nbsp;31일&nbsp; | 이 날짜 후에는 기존 Batch AI 구독이 더 이상 작동하지 않습니다. |

<a name="migrate"></a>
## <a name="how-do-i-migrate"></a>마이그레이션하려면 어떻게 해야 하나요?

애플리케이션의 중단을 방지하고 최신 기능을 활용하려면 2019년 3월 31일 전에 다음 단계를 수행하세요.

1. Azure Machine Learning Service 작업 영역을 만들고 시작합니다.
    + [Python 기반 빠른 시작](../machine-learning/service/quickstart-create-workspace-with-python.md)
    + [Azure Portal 기반 빠른 시작](../machine-learning/service/quickstart-get-started.md)

1. [Azure Machine Learning 컴퓨팅](../machine-learning/service/how-to-set-up-training-targets.md#amlcompute)의 모델 학습을 설정합니다.

1. Azure Machine Learning 컴퓨팅을 사용하도록 스크립트를 업데이트합니다.

## <a name="support"></a>지원

더 포괄적인 [Azure Machine Learning Service](https://aka.ms/aml-docs)로 마이그레이션하려는 기존 고객은 지원을 이용할 수 있습니다.

지원되는 기능이 Batch AI 서비스에 있는 경우 Azure Machine Learning Service가 요구를 충족하지 못하면, 지원 팀에게 Batch AI 지원 요청을 열어 서비스 사용 중지까지 Batch AI를 사용할 수 있게 구독을 허용 목록에 추가해 줄 것을 요청합니다.

## <a name="next-steps"></a>다음 단계

+ [Azure Machine Learning Service 개요](../machine-learning/service/overview-what-is-azure-ml.md)를 읽어 봅니다.

+ Azure Machine Learning Service를 사용하여[모델 학습을 위한 컴퓨팅 대상을 구성](../machine-learning/service/how-to-set-up-training-targets.md)합니다.

+ [Azure 로드맵](https://azure.microsoft.com/updates/)을 검토하여 다른 Azure 서비스 업데이트에 대해 알아봅니다.
