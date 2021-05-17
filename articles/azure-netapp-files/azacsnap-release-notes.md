---
title: Azure NetApp Files용 Azure Application Consistent Snapshot 도구에 대한 릴리스 정보 | Microsoft Docs
description: Azure NetApp Files에서 사용할 수 있는 Azure 애플리케이션 일치 스냅샷 도구에 대한 릴리스 정보를 제공합니다.
services: azure-netapp-files
documentationcenter: ''
author: Phil-Jensen
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/22/2021
ms.author: phjensen
ms.openlocfilehash: 01fb93dcd0a1d5c1db615c47d7811a0baa863c9b
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105111447"
---
# <a name="release-notes-for-azure-application-consistent-snapshot-tool-preview"></a>Azure Application Consistent Snapshot 도구에 대한 릴리스 정보

이 페이지에는 새 기능을 제공하거나 결함을 해결하기 위해 AzAcSnap에서 변경된 주요 내용이 나와 있습니다.

## <a name="march-2021"></a>2021년 3월

### <a name="azacsnap-v50-preview-build2021031830771"></a>AzAcSnap v5.0 Preview(빌드:20210318.30771)

AzAcSnap v5.0 Preview(빌드:20210318.30771)는 다음과 같은 수정 사항 및 개선 사항을 포함하여 릴리스되었습니다.

- AZACSNAP 사용자를 SAP HANA 테넌트 DB에 추가할 필요가 없어졌습니다. [SAP HANA와의 통신 사용](azacsnap-installation.md#enable-communication-with-sap-hana) 섹션을 참조하세요.
- 수동 QOS로 구성된 볼륨으로 [복원](azacsnap-cmd-ref-restore.md)을 허용하도록 수정합니다.
- Azure 대규모 인스턴스에 대한 SSH 연결을 제한하는 뮤텍스 컨트롤이 추가되었습니다.
- 공백 및 기타 관련 이슈가 있는 경로 이름을 처리하기 위해 설치 관리자를 수정했습니다.
- 다른 데이터베이스 서버를 지원하기 위해 선택적 매개 변수 '--hanasid'를 '--dbsid'로 변경했습니다.

설치 관리자의 [최신 릴리스](https://aka.ms/azacsnapdownload)를 다운로드하고 [시작](azacsnap-get-started.md) 방법을 검토하세요.

## <a name="next-steps"></a>다음 단계

- [Azure 애플리케이션 일치 스냅샷 도구 시작](azacsnap-get-started.md)
