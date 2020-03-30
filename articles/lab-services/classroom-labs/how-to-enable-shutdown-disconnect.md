---
title: 연결 해제 Azure 랩 서비스에서 VM 을 종료할 수 있도록 설정 | 마이크로 소프트 문서
description: 원격 데스크톱 연결이 끊어지면 VM의 자동 종료를 활성화하거나 사용하지 않도록 설정하는 방법을 알아봅니다.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/13/2019
ms.author: spelluru
ms.openlocfilehash: 68a27a325a0ef02c6eeea9867a21ba0e24ab5321
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77117138"
---
# <a name="enable-automatic-shutdown-of-vms-on-disconnect"></a>연결 해제 시 VM 자동 종료 활성화
이 문서에서는 원격 데스크톱 연결이 끊어진 후 **Windows 10** 랩 VM(템플릿 또는 학생)의 자동 종료를 활성화하거나 사용하지 않도록 설정하는 방법을 보여 주어집니다. VM이 자동으로 종료되기 전에 사용자가 다시 연결될 때까지 기다려야 하는 시간도 지정할 수 있습니다.

랩 계정 관리자는 랩을 만드는 랩 계정에 대해 이 설정을 구성할 수 있습니다. 자세한 내용은 [랩 계정의 연결 끊김시 VM 자동 종료 구성을](how-to-configure-lab-accounts.md#automatic-shutdown-of-vms-on-disconnect)참조하십시오. 랩 소유자는 랩을 만들 때 또는 랩을 만든 후에 설정을 재정의할 수 있습니다. 

## <a name="configure-when-creating-a-lab"></a>랩을 만들 때 구성
랩 만들기 마법사의 3단계 페이지에서 이 기능을 활성화 또는 비활성화하고 VM이 자동으로 종료되기 전에 사용자가 다시 연결될 때까지 기다려야 하는 시간도 지정할 수 있습니다. 

![랩 생성 시 구성](../media/how-to-enable-shutdown-disconnect/configure-lab-creation.png)

## <a name="configure-after-the-lab-is-created"></a>랩을 만든 후 구성
다음 이미지와 같이 **설정** 페이지에서 이 설정을 구성할 수 있습니다. 

![랩을 만든 후 구성](../media/how-to-enable-shutdown-disconnect/configure-lab-automatic-shutdown.png)

> [!WARNING]
> RDP 세션을 VM으로 연결하기 전에 VM에서 Windows 운영 체제(OS)를 종료하면 자동 종료 기능이 제대로 작동하지 않습니다.  

## <a name="next-steps"></a>다음 단계
다음 문서를 참조하세요.

- [교실 실습을 위한 대시보드](use-dashboard.md)