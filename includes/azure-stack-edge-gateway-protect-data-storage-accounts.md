---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: 2e0a7ca8fb9eaafbc50c0ce60799dd68d83b2fa3
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98901064"
---
디바이스는 Azure에서 데이터의 대상으로 사용되는 스토리지 계정과 연결됩니다. 스토리지 계정에 대한 액세스는 해당 스토리지 계정과 연결된 두 개의 512비트 스토리지 액세스 키 및 구독을 통해 제어됩니다.

키 중 하나는 Data Box Edge 장치에서 저장소 계정에 액세스할 때 인증에 사용 됩니다. 다른 키는 예약에 보관되므로 정기적으로 키를 회전할 수 있습니다.

보안상의 이유로 많은 데이터 센터 키 회전이 필요합니다. 키 회전에 대해 이 모범 사례를 따르는 것이 좋습니다.

- 스토리지 계정 키는 스토리지 계정의 루트 암호와 비슷합니다. 계정 키를 신중하게 보호합니다. 다른 사용자에게 암호를 배포하거나 하드 코딩하거나 다른 사용자가 액세스할 수 있는 일반 텍스트로 저장하지 마세요.
- 손상 될 수 있다고 생각 되는 경우 Azure Portal를 통해 [계정 키를 다시 생성](../articles/storage/common/storage-account-keys-manage.md#manually-rotate-access-keys) 합니다.
- Azure 관리자는 Azure Portal의 저장소 섹션을 사용 하 여 기본 또는 보조 키를 정기적으로 변경 하거나 다시 생성 하 여 저장소 계정에 직접 액세스 해야 합니다.
