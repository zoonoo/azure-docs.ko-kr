---
title: 파일 포함
description: 포함 파일
author: rothja
ms.service: azure-resource-manager
ms.topic: include
ms.date: 05/18/2018
ms.author: jroth
ms.custom: include file
ms.openlocfilehash: 74f9c5304237ec77a629bc914d95c345882b784f
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2020
ms.locfileid: "95556214"
---
| 리소스 | 기본 제한 | 최대 제한 |
| --- | --- | --- |
| [구독](https://azure.microsoft.com/pricing/)당 vCPU<sup>1</sup> |20 |10000 |
| 구독당 [공동 관리자](../articles/cost-management-billing/manage/add-change-subscription-administrator.md) |200 |200 |
| 구독당 [Storage 계정](../articles/storage/common/storage-account-create.md)<sup>2</sup> |100 |100 |
| [클라우드 서비스](../articles/cloud-services/cloud-services-choose-me.md) |20 |200 |
| [로컬 네트워크](/previous-versions/azure/reference/jj157100(v=azure.100)) |10 |500 |
| 구독당 DNS 서버 |9 |100 |
| 구독당 예약된 IP |20 |100 |
| [선호도 그룹](/previous-versions/azure/virtual-network/virtual-networks-migrate-to-regional-vnet) |256 |256 |
| 구독 이름 길이(문자) | 64 | 64 |

<sup>1</sup>부분 CPU 코어를 사용하더라도 매우 작은 인스턴스가 vCPU 제한에 대해 하나의 vCPU로 계산됩니다.

<sup>2</sup>스토리지 계정 제한에는 표준 및 Premium Storage 계정이 모두 포함됩니다.