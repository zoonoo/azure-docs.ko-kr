---
title: Azure Site Recovery 데이터 암호화 기능 사용 중단 | Microsoft Docs
description: regarig Azure Site Recovery 데이터 암호화 기능에 대한 세부 정보
services: site-recovery
author: Sharmistha-Rai
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/15/2019
ms.author: sharrai
ms.openlocfilehash: 5860928d71c0e7431190908d5df5d7496e8ffb17
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "89426319"
---
# <a name="deprecation-of-site-recovery-data-encryption-feature"></a>Site Recovery 데이터 암호화 기능 사용 중단

이 문서에서는 Azure로 Hyper-V 가상 머신의 재해 복구를 구성하는 동안 Site Recovery 데이터 암호화 기능을 사용하는 경우 수행해야 하는 사용 중단 세부 정보 및 수정 작업에 대해 설명합니다. 

## <a name="deprecation-information"></a>사용 중단 정보


Hyper-V VM을 보호하는 고객은 복제된 데이터를 보안 위협으로부터 보호하기 위해 Site Recovery 데이터 암호화 기능을 사용했습니다. 이 기능은 **2022 년 4월 30일** 부터 더 이상 사용할 수 없습니다. 이는 SSE([스토리지 서비스 암호화](../storage/common/storage-service-encryption.md))를 사용하는 고급 [미사용 암호화](https://azure.microsoft.com/blog/azure-site-recovery-encryption-at-rest/) 기능으로 대체됩니다. SSE를 사용하면 데이터를 스토리지에 보존하기 전에 암호화되고 검색 시 암호 해독되며, Azure로 장애 조치(failover)하면 암호화된 스토리지 계정에서 VM이 실행되므로, RTO(Recovery Time Objective)가 향상됩니다.

기존에 이 기능을 사용 중인 고객의 경우 사용 중단의 세부 정보 및 수정 단계에 관한 안내를 받게 됩니다. 


## <a name="what-are-the-implications"></a>사용 중단으로 인한 영향은 무엇인가요?

**2022년 4월 30일** 이후에도 사용 중지된 암호화 기능을 사용하는 모든 VM은 장애 조치(failover)를 수행할 수 없습니다. 

## <a name="required-action"></a>필요한 작업
성공적인 장애 조치(failover) 작업, 복제를 계속 수행하려면 다음에 설명된 단계를 수행합니다.

각 VM에 대해 다음 단계를 수행합니다. 
1.  [복제를 사용하지 않도록 설정합니다](./site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-replicating-to-azure-using-the-system-center-vmm-to-azure-scenario).
2.  [새 복제 정책을 만듭니다](./hyper-v-azure-tutorial.md#set-up-a-replication-policy).
3.  [복제를 사용하도록 설정](./hyper-v-vmm-azure-tutorial.md#enable-replication)하고 SSE를 사용하도록 설정된 스토리지 계정을 선택합니다.

SSE를 사용하는 스토리지 계정에 대한 초기 복제를 완료하면 VM은 Azure Site Recovery에서 미사용 암호화를 사용합니다.


## <a name="next-steps"></a>다음 단계
수정 단계를 수행할 계획을 세우고 최대한 빨리 실행합니다. 이 사용 중단에 대해 문의 사항이 있는 경우 Microsoft 지원에 문의하세요. Hyper-V에서 Azure로 시나리오에 대한 자세한 내용은 [여기](hyper-v-vmm-architecture.md)를 참조하세요.
