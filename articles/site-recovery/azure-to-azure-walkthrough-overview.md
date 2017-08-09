---
title: "Azure 지역 간 Azure VM 복제 | Microsoft Docs"
description: "Azure Portal에서 Azure Site Recovery 서비스를 사용하여 Azure 지역 간에 Azure VM을 복제하는 데 필요한 단계를 요약하고 있습니다."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmon
editor: 
ms.assetid: 6dd36239-4363-4538-bf80-a18e71b8ec67
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/01/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: c30998a77071242d985737e55a7dc2c0bf70b947
ms.openlocfilehash: 9258613161a61e36b1d0c5796d5763c916d66859
ms.contentlocale: ko-kr
ms.lasthandoff: 08/02/2017

---

# <a name="replicate-azure-vms-between-regions-with-azure-site-recovery"></a>Azure Site Recovery를 사용하여 지역 간 Azure VM 복제

>이 문서에서는 하나의 Azure 지역에 있는 Azure VM(가상 컴퓨터)을 다른 지역의 Azure VM으로 복제하는 데 필요한 단계에 대해 간략히 설명합니다. 

>[!NOTE]
>
> Azure VM 복제는 현재 미리 보기로 제공됩니다.

이 문서의 하단 또는 [Azure Recovery Services 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)에서 의견이나 질문을 게시합니다.

## <a name="step-1-review-architecture"></a>1단계: 아키텍처 검토

배포를 시작하기 전에 시나리오 아키텍처 및 배포하는 데 필요한 구성 요소를 검토합니다.

[1단계: 아키텍처 검토](azure-to-azure-walkthrough-architecture.md)로 이동합니다.


## <a name="step-2-review-prerequisites"></a>2단계: 필수 구성 요소 검토

구독, 가상 네트워크, 저장소 계정 및 VM 요구 사항을 포함한 Azure 필수 구성 요소가 제 위치에 있는지 확인합니다.

[2단계: 필수 구성 요소 및 제한 사항 확인](azure-to-azure-walkthrough-prerequisites.md)으로 이동합니다.


## <a name="step-3-plan-networking"></a>3단계: 네트워킹 계획

복제하려는 Azure VM에 아웃바운드 연결이 설정되어 있는지와 온-프레미스 연결이 설정되어 있는지 확인합니다.

[4단계: 네트워킹 계획](azure-to-azure-walkthrough-network.md)으로 이동합니다.



## <a name="step-4-create-a-vault"></a>4단계: 자격 증명 모음 만들기 

복제를 조정 및 관리하고 원본 지역을 지정하도록 Recovery Services 자격 증명 모음을 설정해야 합니다.

[4단계: 자격 증명 모음 만들기](azure-to-azure-walkthrough-vault.md)로 이동합니다.


## <a name="step-5-enable-replication"></a>5단계: 복제 사용


복제를 사용하도록 설정하려면 대상 위치 설정을 구성하고, 복제 정책을 설정하고, 복제하려는 Azure VM을 선택합니다. 활성화한 후 VM의 초기 복제가 시작됩니다.

[5단계: 복제 사용](azure-to-azure-walkthrough-enable-replication.md)으로 이동합니다.


## <a name="step-6-run-a-test-failover"></a>6단계: 테스트 장애 조치 실행

초기 복제가 완료되고 델타 복제가 실행된 후에 테스트 장애 조치(failover)를 실행하여 예상대로 작동되는지 확인할 수 있습니다.

[6단계: 테스트 장애 조치 실행](azure-to-azure-walkthrough-test-failover.md)으로 이동합니다.



