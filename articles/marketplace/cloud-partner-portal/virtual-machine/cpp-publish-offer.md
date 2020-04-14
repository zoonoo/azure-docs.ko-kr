---
title: Azure 마켓플레이스에 가상 시스템 오퍼 게시
description: 기존 가상 머신 제안을 Azure Marketplace에 게시하는 데 필요한 단계를 나열합니다.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/17/2018
ms.author: dsindona
ms.openlocfilehash: bb875a5c4ab1b898b64fe22140414e5d5b7830b8
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273786"
---
# <a name="publish-a-virtual-machine-offer"></a>가상 머신 제안 게시

> [!IMPORTANT]
> 2020년 4월 13일부터 Azure 가상 머신 의 관리를 파트너 센터로 이전합니다. 마이그레이션 후 파트너 센터에서 오퍼를 만들고 관리합니다. Azure 가상 [컴퓨터 만들기 의](https://aka.ms/CreateAzureVMoffer) 지침에 따라 마이그레이션된 오퍼를 관리합니다.

 포털에서 제안을 정의하고 관련 기술 자산을 만든 후 마지막 단계는 제안을 제출하여 게시하는 것입니다. 다음 다이어그램에서는 "라이브 상태로 만들기(go live)" 위한 게시 프로세스의 주요 단계를 보여 줍니다.

![가상 머신 제안 게시 단계](./media/publishvm_013.png)

다음 표에서는 이러한 단계를 설명하고, 예상되는 최대 완료 시간을 제공합니다.
<!-- we need to tell them that if an offer seems stuck in a step, to know that they should file a support ticket (link to support ticket doc) -->


|  **게시 단계**           | **시간**    | **설명**                                                            |
|  -------------------           | --------    | ---------------                                                            |
| 필수 구성 요소 유효성 검사         | 15분   | 제안 정보 및 제안 설정의 유효성이 검사됩니다.                        |
| 시험 사용 유효성 검사(선택 사항) | 2시간 | 시험 사용을 사용하도록 선택한 경우 Microsoft는 선택한 지역을 통해 시험 사용 구성, 해당 배포 및 복제의 유효성을 검사합니다. |
| 인증                  | 3일 | Azure 인증 팀에서 제안을 분석합니다. 이 단계에서는 바이러스, 맬웨어, 안전 규정 준수 및 보안 문제에 대한 검사를 수행합니다. 문제가 발견되면 피드백이 제공됩니다. |
| 프로비전                   | 4일   | VM 제안이 마켓플레이스 프로덕션 시스템에 복제됩니다.               |
| 패키징 및 잠재 고객 생성 등록 | 1시간 미만  | 오퍼의 기술 자산은 고객용으로 패키징되며 잠재 고객 시스템이 구성 및 설정됩니다. |
|  게시자 확인             |  -        | 제안이 라이브 상태가 되기 전에 최종 게시자가 검토 및 확인합니다. 선택한 구독에 제안을 배포하여(제안 정보 단계에서) 모든 요구 사항을 충족하는지 확인할 수 있습니다.  |
| 프로비전                   | 4일 | 최종적인 VM 제안은 마켓플레이스 프로덕션 시스템 및 지역에 복제됩니다. | 
| 라이브                           | 4일 | VM 제안이 릴리스되고, 필요한 지역에 복제되며, 공개적으로 사용할 수 있게 됩니다. |
|  |  |

이 프로세스가 완료되는 데 최대 16일이 걸릴 수 있습니다.  이러한 게시 단계가 완료되면 VM 제안이 [Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/)에 나열됩니다. 

