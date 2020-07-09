---
title: Azure Site Recovery 데이터 암호화 기능 사용 중단 | Microsoft Docs
description: Regarig Azure Site Recovery 데이터 암호화 기능에 대 한 세부 정보
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/15/2019
ms.author: rajanaki
ms.openlocfilehash: b9d333467864f67bc357314830ff885af4232ba0
ms.sourcegitcommit: e995f770a0182a93c4e664e60c025e5ba66d6a45
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86133188"
---
# <a name="deprecation-of-site-recovery-data-encryption-feature"></a>Site Recovery 데이터 암호화 기능 사용 중단

이 문서에서는 Azure에 대 한 Hyper-v 가상 머신의 재해 복구를 구성 하는 동안 Site Recovery 데이터 암호화 기능을 사용 하는 경우 수행 해야 하는 사용 중단 정보 및 수정 작업에 대해 설명 합니다. 

## <a name="deprecation-information"></a>사용 중단 정보


Hyper-v vm을 보호 하는 고객은 복제 된 데이터가 보안 위협 으로부터 보호 되도록 Site Recovery 데이터 암호화 기능을 사용할 수 있습니다. 이 기능은 **2019 년 12 월 30 일까**지 사용 되지 않습니다. 이는 SSE ( [저장소 서비스 암호화](../storage/common/storage-service-encryption.md) )를 사용 하는 [미사용 고급 암호화](https://azure.microsoft.com/blog/azure-site-recovery-encryption-at-rest/) 기능으로 대체 됩니다. SSE를 사용 하면 데이터를 저장소에 저장 하 고 검색 시 암호를 해독 하기 전에 암호화 되며, Azure로 장애 조치 (failover) 되 면 Vm이 암호화 된 저장소 계정에서 실행 되므로, 향상 된 RTO (복구 시간 목표)를 사용할 수 있습니다.

이 기능을 사용 하는 기존 고객 인 경우 사용 중단 세부 정보 및 수정 단계와의 통신을 받게 됩니다. 


## <a name="what-are-the-implications"></a>의미는 무엇 인가요?

**2019 년 12 월 30**일 이후에는 사용 중지 된 암호화 기능을 사용 하는 모든 vm은 장애 조치 (failover)를 수행할 수 없습니다. 

## <a name="required-action"></a>필수 작업
성공적인 장애 조치 (failover) 작업을 계속 하려면 다음에 설명 된 단계를 수행 합니다.

각 VM에 대해 다음 단계를 수행 합니다. 
1.  [복제를 사용 하지 않습니다](./site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-replicating-to-azure-using-the-system-center-vmm-to-azure-scenario).
2.  [새 복제 정책을 만듭니다](./hyper-v-azure-tutorial.md#set-up-a-replication-policy).
3.  [복제를 사용 하도록 설정](./hyper-v-vmm-azure-tutorial.md#enable-replication) 하 고 SSE가 설정 된 저장소 계정을 선택 합니다.

SSE를 사용 하는 저장소 계정에 대 한 초기 복제를 완료 한 후 Vm은 미사용 암호화를 사용 하 여 Azure Site Recovery 합니다.


## <a name="next-steps"></a>다음 단계
업데이트 관리 단계를 계획 하 고 가장 이른 시간에 실행 합니다. 이 사용 중단에 대 한 쿼리를 사용 하는 경우 Microsoft 지원에 연결 하세요. Hyper-v에서 Azure로 시나리오에 대 한 자세한 내용은 [여기](hyper-v-vmm-architecture.md)를 참조 하세요.

