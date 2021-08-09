---
title: 가상 머신 개요
titleSuffix: Azure VMware Solution by CloudSimple
description: CloudSimple 가상 머신과 그 이점에 대해 알아봅니다. Azure Portal에서 VMware 가상 머신을 관리할 수 있습니다.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 6f9be035978667287b8b88ec1bb64b3882b0f929
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "88141979"
---
# <a name="cloudsimple-virtual-machines-overview"></a>CloudSimple 가상 머신 개요

CloudSimple을 사용하면 Azure Portal에서 VMware VM(가상 머신)을 관리할 수 있습니다.  vSphere 클러스터의 클러스터 또는 리소스 풀은 구독에 매핑하여 Azure를 통해 관리합니다.

Azure에서 CloudSimple VM을 만들려면 VM 템플릿이 프라이빗 클라우드 vCenter에 있어야 합니다.  템플릿은 운영 체제 및 애플리케이션을 사용자 지정하는 데 사용됩니다.  템플릿 VM은 엔터프라이즈 보안 정책을 충족하도록 강화될 수 있습니다.  템플릿을 사용하여 VM을 만든 다음 셀프 서비스 모델을 사용하여 Azure Portal에서 사용할 수 있습니다.

## <a name="benefits"></a>이점

Azure Portal의 CloudSimple 가상 머신은 사용자가 VMware 가상 머신을 만들고 관리할 수 있는 셀프 서비스 메커니즘을 제공합니다.

* 프라이빗 클라우드 vCenter에서 CloudSimple VM 만들기
* VM 속성 관리
  * 디스크 추가/제거
  * NIC 추가/제거
* CloudSimple VM의 전원 작업
  * 전원 켜기 및 끄기
  * VM 다시 설정
* VM 삭제

## <a name="next-steps"></a>다음 단계

* [Azure에서 VMware VM 사용](quickstart-create-vmware-virtual-machine.md) 방법 알아보기
* [Azure 구독 매핑](azure-subscription-mapping.md) 방법 알아보기
