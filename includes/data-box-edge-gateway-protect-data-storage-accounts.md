---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: 23587e617c62cfe4aa24256330c5f7673dd1c674
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/17/2019
ms.locfileid: "59684450"
---
장치를 대상으로 Azure에서 데이터에 사용 되는 저장소 계정과 연결 됩니다. 저장소 계정에 대 한 액세스는 구독 및 두 개의 512 비트 저장소에 의해 제어 됩니다 해당 저장소 계정과 연결 된 키에 액세스 합니다.

키 중 하나는 데이터 상자 Edge 장치를 저장소 계정에 액세스할 때 인증에 사용 됩니다. 주기적으로 키를 회전하도록 다른 키를 예약 상태로 둡니다.

보안상의 이유로 많은 데이터 센터 키 회전이 필요 합니다. 키 회전에 대해 이 모범 사례를 따르는 것이 좋습니다.

- 저장소 계정 키는 저장소 계정의 루트 암호와 비슷합니다. 계정 키를 신중 하 게 보호 합니다. 없는 다른 사용자에 게 암호를 배포, 하드 코드, 또는 다른 사람이 액세스할 수 있는 일반 텍스트로 아무 곳 이나 저장
- [계정 키를 다시 생성](../articles/storage/common/storage-account-manage.md#regenerate-access-keys) 것으로 판단 되는 경우 Azure portal을 사용 하 여 손상 되었을 수 있습니다.
- Azure 관리자는 정기적으로 Azure Portal의 [스토리지] 섹션을 사용해 직접 Storage 계정에 액세스하여 기본 또는 보조 키를 변경하거나 다시 생성해야 합니다.