---
title: "Azure Site Recovery를 사용하여 Azure에 복제하기 위한 필수 구성 요소 | Microsoft Docs"
description: "이 문서에서는 Azure Site Recovery 서비스를 사용하여 Azure에 VM 및 물리적 컴퓨터를 복제하기 위한 필수 구성 요소를 설명합니다."
services: site-recovery
documentationcenter: 
author: rajani-janaki-ram
manager: jwhit
editor: tysonn
ms.assetid: e24eea6c-50a7-4cd5-aab4-2c5c4d72ee2d
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 08/01/2017
ms.author: rajanaki
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: fb5b8c9ac96ac44d0112919664a177f33ef392da
ms.contentlocale: ko-kr
ms.lasthandoff: 08/21/2017

---

#  <a name="prerequisites-for-replicating-azure-virtual-machines-to-another-region-by-using-azure-site-recovery"></a>Azure Site Recovery를 사용하여 Azure 가상 컴퓨터를 다른 지역에 복제하기 위한 필수 구성 요소

> [!div class="op_single_selector"]
> * [Azure에서 Azure로 복제](site-recovery-azure-to-azure-prereq.md)
> * [온-프레미스에서 Azure로 복제](site-recovery-prereq.md)

Azure Site Recovery 서비스는 다음 오케스트레이션을 통해 BCDR(무중단 업무 방식 및 재해 복구) 전략에 기여합니다.
* 다른 Azure 지역으로 Azure 가상 컴퓨터 복제
* Azure 또는 보조 데이터 센터에 온-프레미스 물리적 서버 및 가상 컴퓨터 복제 

기본 위치에서 중단이 발생하면 보조 위치로 장애 조치하여 앱과 워크로드를 가용 상태로 유지할 수 있습니다. 기본 위치가 정상 작업 상태로 돌아오면 다시 기본 위치로 돌아갈 수 있습니다. Site Recovery에 대한 자세한 내용은 [Site Recovery란?](site-recovery-overview.md)을 참조하세요.

이 문서에는 온-프레미스에서 Azure로 Site Recovery 복제를 시작하는 데 필요한 필수 구성 요소가 요약되어 있습니다.

이 문서의 하단에서 의견을 게시하거나 [Azure Recovery Services 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)에서 기술적인 질문을 합니다.


## <a name="azure-requirements"></a>Azure 요구 사항

**요구 사항** | **세부 정보**
--- | ---
**Azure 계정** | [Microsoft Azure](http://azure.microsoft.com/) 계정.<br/><br/> [무료 평가판](https://azure.microsoft.com/pricing/free-trial/)으로 시작할 수 있습니다.
**Site Recovery 서비스** | Site Recovery 가격 책정에 대한 자세한 내용은 [Site Recovery 가격](https://azure.microsoft.com/pricing/details/site-recovery/)을 참조하세요. 재해 복구 위치로 사용할 대상 Azure 지역에 Recovery Services 자격 증명 모음을 만드는 것이 좋습니다. 예를 들어 미국 동부에서 원본 VM이 실행되고 있고 미국 중부로 복제하려는 경우 미국 중부에 자격 증명 모음을 만드는 것이 좋습니다.|
**Azure 용량** | 재해 복구 위치로 사용할 대상 Azure 지역의 경우 가상 컴퓨터, 저장소 계정 및 네트워크 구성 요소에 사용할 용량이 충분한 구독이 있어야 합니다. 지원에 문의하여 용량을 더 추가할 수 있습니다.
**저장소 지침** | 성능 문제를 방지하려면 원본 Azure 가상 컴퓨터에 대한 [저장소 지침](../storage/common/storage-scalability-targets.md#scalability-targets-for-virtual-machine-disks)을 따라야 합니다. 기본 설정을 따르는 경우 Site Recovery가 원본 구성에 따라 필요한 저장소 계정을 만듭니다. 사용자 고유의 설정을 사용자 지정하고 선택하는 경우 [가상 컴퓨터 디스크에 대한 확장성 목표](../storage/common/storage-scalability-targets.md#scalability-targets-for-virtual-machine-disks)를 따라야 합니다.
**네트워킹 지침** | 특정 URL 또는 IP 범위에 대해 Azure VM의 아웃바운드 연결이 허용 목록에 있어야 합니다. 자세한 내용은 [networking guidance for replicating Azure virtual machines](site-recovery-azure-to-azure-networking-guidance.md)(Azure 가상 컴퓨터 복제를 위한 네트워킹 지침) 문서를 참조하세요.
**Azure VM** | Windows 또는 Linux VM에 모든 최신 루트 인증서가 있는지 확인합니다. 최신 루트 인증서가 없는 경우 보안 제약 조건으로 인해 Site Recovery에 VM을 등록할 수 없습니다.

>[!NOTE]
>특정 구성의 지원에 대한 자세한 내용은 [지원 매트릭스](site-recovery-support-matrix-azure-to-azure.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계
- [networking guidance for replicating Azure virtual machines](site-recovery-azure-to-azure-networking-guidance.md)(Azure 가상 컴퓨터 복제를 위한 네트워킹 지침)에 대해 자세히 알아봅니다.
- [Azure 가상 컴퓨터를 복제](site-recovery-azure-to-azure.md)하여 워크로드 보호를 시작합니다.

