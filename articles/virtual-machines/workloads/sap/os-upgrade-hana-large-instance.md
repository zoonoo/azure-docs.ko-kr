---
title: SAP HANA on Azure(대형 인스턴스)에 대한 운영 체제 업그레이드 | Microsoft Docs
description: SAP HANA on Azure(대형 인스턴스)에 대한 운영 체제 업그레이드 수행
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/28/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: cff9be3b074dde4a0335675663133a8df81ae62d
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/29/2018
ms.locfileid: "37114595"
---
# <a name="operating-system-upgrade"></a>운영 체제 업그레이드
이 문서에서는 HANA 대형 인스턴스에서 운영 체제 업그레이드에 대한 세부 정보를 설명합니다.

>[!NOTE]
>OS 업그레이드는 고객 책임이며, Microsoft 운영 지원팀은 업그레이드 하는 동안 주의해야 할 주요 영역을 안내할 수 있습니다. 업그레이드에 대해 계획하기 전에도 해당 운영 체제 공급 업체와 상담해야 합니다.

HLI 단위 프로비전 시 Microsoft 운영 팀은 운영 체제를 설치합니다. 시간이 지남에 따라 HLI 단위에서 운영 체제를 유지 관리해야 합니다(예: 패치, 튜닝, 업그레이드 등).

운영 체제를 대폭 변경하기 전에(예: SP1을 SP2로 업그레이드) 지원 티켓을 열어 Microsoft 운영 팀에 문의해야 합니다.


Linux 버전이 다른 여러 SAP HANA 버전에 대한 지원 매트릭스는 [SAP Note #2235581](https://launchpad.support.sap.com/#/notes/2235581)을 참조하세요.


## <a name="known-issues"></a>알려진 문제

다음은 업그레이드 동안 몇 가지 알려진 일반적인 문제입니다.
- SKU Type II 클래스 SKU에서 SFS(software foundation server)는 OS 업그레이드 후 제거됩니다. OS 업그레이드 후 호환 가능한 SFS를 다시 설치해야 합니다.
- 이더넷 카드 드라이버(ENIC 및 FNIC)가 이전 버전으로 롤백했습니다. 업그레이드 후 호환 가능한 버전의 드라이버를 다시 설치해야 합니다.

## <a name="next-steps"></a>다음 단계
- OS 백업 Type I SKU 클래스에 대해서는 [Backup 및 복원](hana-overview-high-availability-disaster-recovery.md)을 참조합니다.
- Type II SKU 클래스에 대해서는 [Type II SKU용 OS Backup](os-backup-type-ii-skus.md)을 참조합니다.