---
title: Azure VMware 솔루션 (AVS)-Virtual machines 개요
description: AVS 가상 머신 및 해당 혜택에 대해 알아보세요.
titleSuffix: Azure VMware Solutions (AVS)
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 0e6cc6d51e37cea98cc553a236295311d21f595a
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024911"
---
# <a name="avs-virtual-machines-overview"></a>AVS 가상 머신 개요

AVS를 사용 하면 Azure Portal에서 VMware Vm (가상 머신)을 관리할 수 있습니다. VSphere 클러스터의 클러스터 또는 리소스 풀은 구독에 매핑하여 Azure를 통해 관리 됩니다.

Azure에서 AVS VM을 만들려면 VM 템플릿이 AVS 사설 클라우드 vCenter에 존재 해야 합니다. 템플릿은 운영 체제 및 응용 프로그램을 사용자 지정 하는 데 사용 됩니다. 엔터프라이즈 보안 정책을 충족 하기 위해 템플릿 VM을 확정 시킬 수 있습니다. 템플릿을 사용 하 여 Vm을 만든 다음 셀프 서비스 모델을 사용 하 여 Azure Portal에서 사용할 수 있습니다.

## <a name="benefits"></a>혜택

Azure Portal의 AVS 가상 머신은 사용자가 VMware 가상 머신을 만들고 관리 하는 셀프 서비스 메커니즘을 제공 합니다.

* AVS 사설 클라우드 vCenter에서 AVS VM 만들기
* VM 속성 관리
  * 디스크 추가/제거
  * Nic 추가/제거
* AVS VM의 전원 작업
  * 전원 켜기 및 전원 끄기
  * VM 다시 설정
* VM 삭제

## <a name="next-steps"></a>다음 단계

* [Azure에서 VMware vm을 사용](quickstart-create-vmware-virtual-machine.md) 하는 방법 알아보기
* [Azure 구독을 매핑하](azure-subscription-mapping.md) 는 방법 알아보기
