---
title: QnA Maker 배포 용량 선택 - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: QnA Maker 배포 용량 선택 가이드
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 05/07/2018
ms.author: saneppal
ms.openlocfilehash: b0219b9f7dbbee52406dab9d808134fa2e2a689d
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35376311"
---
# <a name="choosing-capacity-for-your-qna-maker-deployment"></a>QnA Maker 배포 용량 선택

QnA Maker 서비스는 다음 세 가지 Azure 리소스에 종속됩니다.
1.  App Service(런타임)
2.  Azure Search(QnAs 저장)
3.  App Insights(선택 사항, chatlogs 및 원격 분석 저장)

QnA Maker 서비스를 만들기 전에 적합한 위 서비스 계층을 결정해야 합니다. 

일반적으로 고려해야 하는 다음 세 가지 매개 변수가 있습니다.
1. **서비스에서 필요한 처리량**: 필요에 따라 App Service에 적합한 [앱 플랜](https://azure.microsoft.com/en-in/pricing/details/app-service/plans/)을 선택합니다. 앱을 [확장](https://docs.microsoft.com/en-us/azure/app-service/web-sites-scale)하거나 축소할 수 있습니다. 이 경우 Azure Search SKU 선택에도 영향을 줍니다. 자세한 내용은 [여기](https://docs.microsoft.com/en-us/azure/search/search-sku-tier)를 참조하세요.

2. **기술 자료의 크기 및 개수**: 시나리오에 적합한 [Azure Search SKU](https://azure.microsoft.com/en-in/pricing/details/search/)를 선택합니다. 특정 계층에 N-1개의 기술 자료를 게시할 수 있습니다. 여기서 N은 계층에서 허용되는 최대 인덱스 개수입니다. 또한 계층당 허용되는 문서의 최대 크기 및 개수를 확인합니다.

3. **원본 문서 수**: QnA Maker 관리 서비스의 체험 SKU는 포털 및 API를 통해 관리할 수 있는 문서 수를 3개(각각 1MB 크기)로 제한합니다. 표준 SKU는 관리할 수 있는 문서 수에 대한 제한이 없습니다. 자세한 내용은 [여기](https://aka.ms/qnamaker-pricing)를 참조하세요.

다음 표에서는 몇 가지 전반적인 지침을 제공합니다.

|                        | QnA Maker 관리 | App Service | Azure Search | 제한 사항                      |
| ---------------------- | -------------------- | ----------- | ------------ | -------------------------------- |
| 실험        | 체험 SKU             | 체험 계층   | 체험 계층    | KB 최대 2개, 50MB 크기까지 게시  |
| 개발/테스트 환경   | 표준 SKU         | 공유됨      | Basic        | KB 최대 4개, 2GB 크기까지 게시    |
| 프로덕션 환경 | 표준 SKU         | Basic       | Standard     | KB 최대 49개, 25GB 크기까지 게시 |

QnA Maker 스택을 업그레이드하려면 [QnA Maker 서비스 업그레이드](../How-To/upgrade-qnamaker-service.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [QnA Maker 서비스 업그레이드](../How-To/upgrade-qnamaker-service.md)
