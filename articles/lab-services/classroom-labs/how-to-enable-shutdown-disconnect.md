---
title: Azure Lab Services 연결 해제 시 VM 종료 사용 | Microsoft Docs
description: 원격 데스크톱 연결이 끊어질 때 VM 자동 종료를 사용하거나 사용하지 않도록 설정하는 방법에 대해 알아봅니다.
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
ms.date: 05/15/2020
ms.author: spelluru
ms.openlocfilehash: b0c7f5daa6bcd9ab5cb8f4d7b1a513a15cd1c708
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83591712"
---
# <a name="enable-automatic-shutdown-of-vms-on-disconnect"></a>연결 해제 시 VM 자동 종료 사용
이 문서에서는 원격 데스크톱 연결이 끊긴 후 **Windows 10** 랩 VM(템플릿 또는 학생)의 자동 종료를 사용하거나 사용하지 않도록 설정하는 방법을 보여줍니다. VM이 자동으로 종료되기 전에 사용자가 다시 연결될 때까지 대기해야 하는 기간도 지정할 수 있습니다.

랩 계정 관리자는 랩을 만드는 랩 계정에 대해 이 설정을 구성할 수 있습니다. 자세한 내용은 [랩 계정의 연결 해제 시 VM 자동 종료 구성](how-to-configure-lab-accounts.md)을 참조하세요. 랩 소유자는 랩을 만들 때 또는 랩을 만든 후 설정을 재정의할 수 있습니다. 

## <a name="configure-when-creating-a-lab"></a>랩을 만들 때 구성
랩 만들기 마법사의 3단계 페이지에서 이 기능을 사용하거나 사용하지 않도록 설정할 수 있으며 사용자가 자동으로 종료되기 전에 다시 연결할 때까지 VM이 대기할 기간을 지정할 수도 있습니다. 

![랩 생성 시 구성](../media/how-to-enable-shutdown-disconnect/configure-lab-creation.png)

## <a name="configure-after-the-lab-is-created"></a>랩 생성 후 구성
다음 이미지와 같이 **설정** 페이지에서 이 설정을 구성할 수 있습니다. 

![랩 생성 후 구성](../media/how-to-enable-shutdown-disconnect/configure-lab-automatic-shutdown.png)

> [!WARNING]
> VM에 대한 RDP 세션 연결을 끊기 전에 VM에서 Windows OS(운영 체제)를 종료하면 자동 종료 기능이 제대로 작동하지 않습니다.  

## <a name="next-steps"></a>다음 단계
다음 문서를 참조하세요.

- [클래스룸 랩용 대시보드](use-dashboard.md)