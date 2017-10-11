---
title: "Azure Site Recovery를 사용하여 온-프레미스 VMware VM을 Azure에 복제하도록 Azure 리소스 준비 | Microsoft Docs"
description: "Azure Site Recovery를 사용하여 온-프레미스 VMware VM을 Azure에 복제하기 전에 Azure에서 준비해야 하는 항목 설명"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 4e320d9b-8bb8-46bb-ba21-77c5d16748ac
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: raynew
ms.openlocfilehash: 40abff72278c9f8d9f701023fd473fe52c17b421
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/29/2017
---
# <a name="step-5-prepare-azure-resources-for-vmware-replication-to-azure"></a>5단계: Azure에 VMWare을 복제하도록 Azure 리소스 준비


Azure 리소스를 준비하는 이 문서의 지침을 사용하여 [Azure Site Recovery](site-recovery-overview.md) 서비스를 사용하여 온-프레미스 컴퓨터를 Azure에 복제할 수 있습니다.

이 문서의 하단 또는 [Azure Recovery Services 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)에서 의견이나 질문을 게시합니다.

## <a name="before-you-start"></a>시작하기 전에

[필수 조건](vmware-walkthrough-prerequisites.md)을 읽었는지 확인합니다.

## <a name="set-up-an-azure-account"></a>Azure 계정 설정

- [Microsoft Azure 계정](http://azure.microsoft.com/)을 가져옵니다.
- [평가판](https://azure.microsoft.com/pricing/free-trial/)으로 시작할 수 있습니다.
- [Azure Site Recovery 가격 책정 세부 정보](https://azure.microsoft.com/pricing/details/site-recovery/)의 지역 가용성에서 Site Recovery에 지원되는 지역을 확인하세요.
- [Site Recovery 가격 책정](site-recovery-faq.md#pricing)에 대해 알아보고 [가격 책정 세부 정보](https://azure.microsoft.com/pricing/details/site-recovery/)를 가져옵니다.



## <a name="set-up-an-azure-network"></a>Azure 네트워크를 설정합니다

- Azure 네트워크 설정 Azure VM은 장애 조치 후 처음 만들 때 이 네트워크에 배치됩니다.
- Azure Portal의 Site Recovery에서는 [리소스 관리자](../resource-manager-deployment-model.md) 또는 클래식 모드에서 설정된 네트워크를 사용할 수 있습니다.
- 네트워크가 Recovery Services 자격 증명 모음과 같은 지역에 있어야 합니다.
- [가상 네트워크 가격 책정](https://azure.microsoft.com/pricing/details/virtual-network/)에 대해 알아봅니다.
- 장애 조치 후에 [Azure VM 연결](site-recovery-network-design.md)에 대해 알아봅니다.


## <a name="set-up-an-azure-storage-account"></a>Azure 저장소 계정을 설정

- Site Recovery는 온-프레미스 컴퓨터를 Azure Storage에 복제합니다. 장애 조치가 발생한 후에 저장소에서 Azure VM을 만듭니다.
- 복제 데이터를 저장할 [Azure Storage 계정](../storage/common/storage-create-storage-account.md#create-a-storage-account)을 설정합니다.
- Azure Portal의 Site Recovery에서는 리소스 관리자 또는 클래식 모드에서 설정된 저장소 계정을 사용할 수 있습니다.
- 저장소 계정은 표준 또는 [프리미엄](../storage/common/storage-premium-storage.md)일 수 있습니다.
- 프리미엄 계정을 설정하는 경우 로그 데이터에 추가 표준 계정이 필요합니다.


## <a name="next-steps"></a>다음 단계

[6단계: VMware 리소스 준비](vmware-walkthrough-prepare-vmware.md)로 이동합니다.
