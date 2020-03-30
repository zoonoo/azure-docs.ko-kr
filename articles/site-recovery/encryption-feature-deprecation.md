---
title: Azure 사이트 복구 데이터 암호화 기능 사용 중단 | 마이크로 소프트 문서
description: 세부 정보 리개 그 Azure 사이트 복구 데이터 암호화 기능
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/15/2019
ms.author: rajanaki
ms.openlocfilehash: 5e74466891a5926d8ae8feb3c1c48348ecf3cfe6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74134997"
---
# <a name="deprecation-of-site-recovery-data-encryption-feature"></a>사이트 복구 데이터 암호화 기능 사용 중단

이 문서에서는 Hyper-V 가상 시스템의 재해 복구를 Azure로 구성하는 동안 사이트 복구 데이터 암호화 기능을 사용하는 경우 사용 중단 세부 정보 및 수행해야 하는 수정 작업에 대해 설명합니다. 

## <a name="deprecation-information"></a>사용 중단 정보


하이퍼 Vm을 보호하는 고객이 보안 위협으로부터 복제된 데이터를 보호할 수 있도록 사이트 복구 데이터 암호화 기능을 사용할 수 있었습니다. 이 기능은 **2019년 12월 30일까지**더 이상 사용되지 않습니다. 저장 서비스 암호화(SSE)를 사용하는 [미사용](https://azure.microsoft.com/blog/azure-site-recovery-encryption-at-rest/) [Storage Service Encryption](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) 시 고급 암호화 기능으로 대체되고 있습니다. SSE를 사용하면 데이터가 저장소에 계속 저장되기 전에 암호화되고 검색 시 해독되며 Azure에 장애 조치되면 VM이 암호화된 저장소 계정에서 실행되어 RTO(복구 시간 목표)가 향상됩니다.

이 기능을 사용하는 기존 고객인 경우 사용 중단 세부 정보 및 수정 단계와 통신을 받았을 것입니다. 


## <a name="what-are-the-implications"></a>의미는 무엇입니까?

**2019년 12월 30일**이후에는 사용 중지된 암호화 기능을 여전히 사용하는 VM은 장애 조치(failover)를 수행할 수 없습니다. 

## <a name="required-action"></a>필수 작업
장애 조치 작업을 성공적으로 계속하고 복제는 아래 단계를 따릅니다.

각 VM에 대해 다음 단계를 따르십시오. 
1.  [복제를 사용하지 않도록 설정합니다.](https://docs.microsoft.com/azure/site-recovery/site-recovery-manage-registration-and-protection#disable-protection-for-a-hyper-v-virtual-machine-replicating-to-azure-using-the-system-center-vmm-to-azure-scenario)
2.  [새 복제 정책을 만듭니다.](https://docs.microsoft.com/azure/site-recovery/hyper-v-azure-tutorial#set-up-a-replication-policy)
3.  [복제를 사용하도록 설정하고](https://docs.microsoft.com/azure/site-recovery/hyper-v-vmm-azure-tutorial#enable-replication) SSE를 사용하도록 설정한 저장소 계정을 선택합니다.

SSE를 사용하도록 설정한 저장소 계정에 대한 초기 복제를 완료한 후 VM은 Azure 사이트 복구를 사용하여 미사용 암호화를 사용하게 됩니다.


## <a name="next-steps"></a>다음 단계
수정 단계를 수행하기 위한 계획을 세우고 가장 빠른 시일 내에 실행합니다. 이 사용 중단과 관련된 질문이 있는 경우 Microsoft 지원에 문의하십시오. 하이퍼-V에서 Azure 시나리오에 대한 자세한 내용은 [여기를](hyper-v-vmm-architecture.md)참조하십시오.

