---
title: Azure 함수 타이머를 사용하여 일정에 따라 Azure 코스모스 DB 확장
description: PowerShell 및 Azure 함수를 사용하여 Azure Cosmos DB에서 처리량의 변경 내용을 확장하는 방법을 알아봅니다.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: mjbrown
ms.openlocfilehash: 68ba40ea212c061fa5c8bbddc47ea0dfc6d8caa4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75935169"
---
# <a name="scale-azure-cosmos-db-throughput-by-using-azure-functions-timer-trigger"></a>Azure 함수 타이머 트리거를 사용하여 Azure 코스모스 DB 처리량 확장

Azure Cosmos 계정의 성능은 RU/s(RU/s)의 요청 단위로 표현된 프로비저닝된 처리량의 양을 기반으로 합니다. 프로비저닝은 두 번째 세분성으로 되어 있으며 시간당 가장 높은 RU/s를 기준으로 청구됩니다. 이러한 프로비전된 용량 모델을 사용하면 서비스는 짧은 대기 시간 및 높은 가용성이 보장되는 예측 가능하고 일관된 처리량을 제공할 수 있습니다. 대부분의 프로덕션 워크로드는 이러한 기능입니다. 그러나 Azure Cosmos DB가 근무 시간 중에만 사용되는 개발 및 테스트 환경에서는 아침에 처리량을 확장하고 근무 시간 이후에 저녁에 다시 축소할 수 있습니다.

SQL(코어) API 계정에 대한 [Azure 리소스 관리자 템플릿,](resource-manager-samples.md) [Azure CLI](cli-samples.md)및 [PowerShell을](powershell-samples-sql.md)통해 처리량을 설정하거나 언어별 Azure Cosmos DB SDK를 사용하여 처리량을 설정할 수 있습니다. 리소스 관리자 템플릿, Azure CLI 또는 PowerShell을 사용하면 모든 Azure Cosmos DB 모델 API를 지원한다는 이점이 있습니다.

## <a name="throughput-scheduler-sample-project"></a>처리량 스케줄러 샘플 프로젝트

일정에 따라 Azure Cosmos DB를 확장하는 프로세스를 단순화하기 위해 [Azure Cosmos 처리량 스케줄러라는](https://github.com/Azure-Samples/azure-cosmos-throughput-scheduler)샘플 프로젝트를 만들었습니다. 이 프로젝트는 두 개의 타이머 트리거가 있는 Azure Functions 앱입니다- "ScaleUpTrigger" 및 "ScaleDownTrigger". 트리거는 각 트리거의 파일에 정의된 대로 각 리소스의 처리량을 설정하는 PowerShell 스크립트를 `resources.json` 실행합니다. ScaleUpTrigger는 오전 8시 UTC에서 실행되도록 구성되며 ScaleDownTrigger는 오후 6시 UTC에서 실행되도록 구성되며 이러한 시간은 각 트리거에 대한 `function.json` 파일 내에서 쉽게 업데이트할 수 있습니다.

이 프로젝트를 로컬로 복제하고 수정하여 Azure Cosmos DB 리소스를 지정하여 확장 및 축소하고 실행할 일정을 지정할 수 있습니다. 나중에 Azure 구독에 배포하고 "Azure Cosmos DB 연산자" 역할을 사용하여 [RBAC(역할 기반 액세스 제어)](role-based-access-control.md) 권한을 사용하여 관리되는 서비스 ID를 사용하여 Azure Cosmos 계정에 처리량을 설정할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Azure Cosmos DB 처리량 스케줄러에서](https://github.com/Azure-Samples/azure-cosmos-throughput-scheduler)샘플을 다운로드하여 자세히 알아보고.
