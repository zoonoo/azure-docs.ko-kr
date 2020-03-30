---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: 8840e6649058837207c75dbdf7baa5c0b11ef387
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75467656"
---
장치는 Azure의 데이터에 대한 대상으로 사용되는 저장소 계정과 연결되어 있습니다. 저장소 계정에 대한 액세스는 구독및 해당 저장소 계정과 연결된 두 개의 512비트 저장소 액세스 키에 의해 제어됩니다.

키 중 하나는 데이터 박스 에지 장치가 저장소 계정에 액세스할 때 인증에 사용됩니다. 다른 키는 예약되어 있으므로 주기적으로 키를 회전할 수 있습니다.

보안상의 이유로 많은 데이터 센터에는 키 회전이 필요합니다. 키 회전에 대해 이 모범 사례를 따르는 것이 좋습니다.

- 스토리지 계정 키는 스토리지 계정의 루트 암호와 비슷합니다. 계정 키를 신중하게 보호합니다. 다른 사용자에게 암호를 배포하거나 하드 코딩하거나 다른 사용자가 액세스할 수 있는 일반 텍스트로 아무 곳에도 저장하지 마십시오.
- 손상될 수 있다고 생각되는 경우 Azure 포털을 통해 계정 키를 다시 생성합니다. 자세한 내용은 [스토리지 계정 액세스 키 관리](../articles/storage/common/storage-account-keys-manage.md)를 참조하세요.
- Azure 관리자는 Azure 포털의 저장소 섹션을 사용하여 저장소 계정에 직접 액세스하여 주별 또는 보조 키를 주기적으로 변경하거나 다시 생성해야 합니다.