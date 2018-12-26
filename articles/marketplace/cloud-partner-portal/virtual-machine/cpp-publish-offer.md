---
title: Azure Marketplace에 가상 머신 제안 게시 | Microsoft Docs
description: 기존 가상 머신 제안을 Azure Marketplace에 게시하는 데 필요한 단계를 나열합니다.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 08/17/2018
ms.author: pbutlerm
ms.openlocfilehash: 3cf6a3d9bcb9470fd3a6bd4fef964c1966adfa1a
ms.sourcegitcommit: 17633e545a3d03018d3a218ae6a3e4338a92450d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2018
ms.locfileid: "49639353"
---
# <a name="publish-a-virtual-machine-offer"></a>가상 머신 제안 게시

 포털에서 제안을 정의하고 관련 기술 자산을 만든 후 마지막 단계는 제안을 제출하여 게시하는 것입니다. 다음 다이어그램에서는 "라이브 상태로 만들기(go live)" 위한 게시 프로세스의 주요 단계를 보여 줍니다.

![가상 머신 제안 게시 단계](./media/publishvm_013.png)

다음 표에서는 이러한 단계를 설명하고, 완료하는 데 예상되는 최대 시간을 제공합니다. <!-- we need to tell them that if an offer seems stuck in a step, to know that they should file a support ticket (link to support ticket doc) -->


|  **게시 단계**           | **Time**    | **설명**                                                            |
|  -------------------           | --------    | ---------------                                                            |
| 필수 구성 요소 유효성 검사         | 15분   | 제안 정보 및 제안 설정의 유효성이 검사됩니다.                        |
| 시험 사용 유효성 검사(선택 사항) | 2시간 | 시험 사용을 사용하도록 선택한 경우 Microsoft는 선택한 지역을 통해 시험 사용 구성, 해당 배포 및 복제의 유효성을 검사합니다. |
| 인증                  | 3일 | Azure 인증 팀에서 제안을 분석합니다. 이 단계에서는 바이러스, 맬웨어, 안전 규정 준수 및 보안 문제에 대한 검사를 수행합니다. 문제가 발견되면 피드백이 제공됩니다. |
| 프로비전                   | 4일   | VM 제안이 마켓플레이스 프로덕션 시스템에 복제됩니다.               |
| 패키징 및 잠재 고객 생성 등록 | 1시간 미만  | 제안의 기술 자산이 고객의 사용에 맞게 패키지되고, 잠재 고객 시스템이 구성 및 설정됩니다. |
|  게시자 확인             |  -        | 제안이 라이브 상태가 되기 전에 최종 게시자가 검토 및 확인합니다. 선택한 구독에 제안을 배포하여(제안 정보 단계에서) 모든 요구 사항을 충족하는지 확인할 수 있습니다.  |
| 프로비전                   | 4일 | 최종적인 VM 제안은 마켓플레이스 프로덕션 시스템 및 지역에 복제됩니다. | 
| 라이브                           | 4일 | VM 제안이 릴리스되고, 필요한 지역에 복제되며, 공개적으로 사용할 수 있게 됩니다. |
|  |  |

이 프로세스가 완료되는 데 최대 16일이 걸릴 수 있습니다.  이러한 게시 단계가 완료되면 VM 제안이 [Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/)에 나열됩니다. 

