---
title: VMware 솔루션 CloudSimple-Azure virtual machines 개요
description: CloudSimple virtual machines 및 혜택에 알아봅니다.
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 59f5438bbedea2ff7793a5df95f1d3df58b9bba6
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/26/2019
ms.locfileid: "64576987"
---
# <a name="cloudsimple-virtual-machines-overview"></a>CloudSimple virtual machines 개요

CloudSimple를 사용 하면 Azure portal에서 VMware Vm을 관리할 수 있습니다.  클러스터 또는 vSphere 클러스터에서 리소스 풀은 구독에 매핑 됨으로써 Azure를 통해 관리 됩니다.  CloudSimple 가상 컴퓨터는 Azure portal에서 VMware Vm의 셀프 서비스 관리를 제공합니다.  

Azure에서 CloudSimple VM을 만들려면 VM 템플릿을 사설 클라우드 vCenter에 있어야 합니다.  운영 체제 및 응용 프로그램을 사용자 지정 하는 템플릿이 사용 됩니다.  엔터프라이즈 보안 정책을 충족 하기 위해 템플릿 VM을 강화 해야 합니다.  Vm을 만들고 셀프 서비스 모델을 사용 하 여 Azure portal에서이 사용 하는 서식 파일을 사용할 수 있습니다.

## <a name="benefits"></a>이점

Azure 포털에서 가상 머신을 CloudSimple VMware 가상 머신을 만들고 관리 하는 사용자에 대 한 셀프 서비스 메커니즘을 제공 합니다.

* 사설 클라우드 vCenter에서 CloudSimple VM 만들기
* VM 속성 관리
  * 디스크를 추가/제거 합니다.
  * Nic 추가/제거 합니다.
* CloudSimple VM의 전원 작업
  * 전원 켜기 및 전원 끄기
  * VM 다시 설정
* VM 삭제

## <a name="next-steps"></a>다음 단계

* 에 대해 알아봅니다 방법 [Azure에서 VMware Vm 사용](quickstart-create-vmware-virtual-machine.md)
* 에 대해 알아봅니다 방법 [Azure 구독에 매핑](https://docs.azure.cloudsimple.com/azuresubscriptionmapping/)