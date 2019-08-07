---
title: CloudSimple 별 VMware 솔루션-Azure virtual machines 개요
description: CloudSimple 가상 머신과 그 이점에 대해 알아보세요.
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 0f4967bbd12107bf6a04cb80537d4425c75c5f46
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/06/2019
ms.locfileid: "68812497"
---
# <a name="cloudsimple-virtual-machines-overview"></a>CloudSimple virtual machines 개요

CloudSimple을 사용 하면 Azure Portal에서 VMware Vm을 관리할 수 있습니다.  VSphere 클러스터의 클러스터 또는 리소스 풀은 구독에 매핑하여 Azure를 통해 관리 됩니다.  CloudSimple 가상 머신은 Azure Portal에서 VMware Vm의 셀프 서비스 관리를 제공 합니다.  

Azure에서 CloudSimple VM을 만들려면 사설 클라우드 vCenter에 VM 템플릿이 있어야 합니다.  템플릿은 운영 체제 및 응용 프로그램을 사용자 지정 하는 데 사용 됩니다.  엔터프라이즈 보안 정책을 충족 하기 위해 템플릿 VM을 확정 시킬 수 있습니다.  템플릿을 사용 하 여 Vm을 만들고 셀프 서비스 모델을 사용 하 여 Azure Portal에서 사용할 수 있습니다.

## <a name="benefits"></a>이점

Azure Portal의 CloudSimple 가상 머신은 사용자가 VMware 가상 머신을 만들고 관리 하는 셀프 서비스 메커니즘을 제공 합니다.

* 사설 클라우드 vCenter에서 CloudSimple VM 만들기
* VM 속성 관리
  * 디스크 추가/제거
  * Nic 추가/제거
* CloudSimple VM의 전원 작업
  * 전원 켜기 및 전원 끄기
  * VM 다시 설정
* VM 삭제

## <a name="next-steps"></a>다음 단계

* [Azure에서 VMware vm을 사용](quickstart-create-vmware-virtual-machine.md) 하는 방법 알아보기
* [Azure 구독을 매핑하](https://docs.azure.cloudsimple.com/azure-subscription-mapping/) 는 방법 알아보기