---
title: 렌더링 관리자 지원
description: Azure Batch 렌더링 관리자 통합을 사용합니다. 인기 있는 렌더링 관리자에 대한 기본 제공 지원 또는 추가 기능에 대해 알아봅니다.
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: how-to
ms.openlocfilehash: c44cbf86d8bf2fe83a6dc91dee1c4f58eec156c0
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83726454"
---
# <a name="using-azure-batch-with-render-farm-managers"></a>렌더링 팜 관리자로 Azure Batch 사용

기존 온-프레미스 렌더링 팜을 사용할 경우 렌더링 관리자가 렌더링 팜 용량 및 렌더링 작업을 제어할 가능성이 큽니다.

Azure는 주요 렌더링 관리자에 대한 기본 제공 지원 또는 추가 기능을 제공합니다. 이에 따라 사용자가 종량제 애플리케이션 라이선스가 적용되는 VM 및 낮은 우선 순위 VM 등, Azure VM을 추가하고 제거할 수 있습니다.

다음 렌더링 관리자가 지원됩니다.

* [PipelineFX Qube!](https://www.pipelinefx.com/)
* [Royal Render](https://www.royalrender.de/)
* [Thinkbox Deadline](https://deadline.thinkboxsoftware.com/)

## <a name="azure-render-hub"></a>Azure Render Hub

Azure Render Hub는 Azure 렌더링 팜을 만들고 관리하는 것을 간소화합니다.  Render Hub는 PipelineFx Qube 및 Deadline 10에 대한 기본 지원을 제공합니다.  자세한 내용과 관련지침은 [GitHub 리포지토리](https://github.com/Azure/azure-render-hub)를 참조하세요.

## <a name="using-azure-with-pipelinefx-qube"></a>Azure에 PipelineFX Qube 사용

Azure Render Hub는 최종 기한을 비롯한 인기 있는 렌더링 관리자를 지원합니다.  Render Hub 및 사용에 대한 지침은 [GitHub 리포지토리](https://github.com/Azure/azure-render-hub)를 참조하세요.

Azure Batch 풀 VM을 Qube 작업자로 사용할 수 있도록 설정하는 스크립트 및 지침은 [GitHub 리포지토리](https://github.com/Azure/azure-qube)에서도 사용할 수 있습니다.

## <a name="using-azure-with-royal-render"></a>Azure에 Royal Render 사용

Royal Render는 Azure와 Azure Batch 통합을 기본 제공하므로 Azure 기반 VM을 통해 렌더링 팜을 확장할 수 있습니다. 요약은 [도움말 파일](https://www.royalrender.de/help8/index.html?Cloudrendering.html)을 참조하세요.

Azure 통합을 사용하는 Royal Render 고객 예는 [Jellyfish Pictures 고객 스토리](https://customers.microsoft.com/story/jellyfishpictures)를 참조하세요.

## <a name="using-azure-with-thinkbox-deadline"></a>Azure에 Thinkbox Deadline 사용

Azure Render Hub는 최종 기한을 비롯한 인기 있는 렌더링 관리자를 지원합니다.  Render Hub 및 사용에 대한 지침은 [GitHub 리포지토리](https://github.com/Azure/azure-render-hub)를 참조하세요.

## <a name="next-steps"></a>다음 단계

해당하는 경우 GitHub에서 적합한 플러그 인과 지침을 통해 렌더링 관리자에 Azure Batch 통합을 사용해 봅니다.
