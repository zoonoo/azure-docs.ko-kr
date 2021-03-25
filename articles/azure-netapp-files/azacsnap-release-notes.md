---
title: Azure NetApp Files에 대 한 Azure 애플리케이션 일치 스냅숏 도구의 릴리스 정보 Microsoft Docs
description: Azure NetApp Files에서 사용할 수 있는 Azure 애플리케이션 일치 스냅숏 도구에 대 한 릴리스 정보를 제공 합니다.
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
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/25/2021
ms.locfileid: "105111447"
---
# <a name="release-notes-for-azure-application-consistent-snapshot-tool-preview"></a>Azure 애플리케이션 일치 스냅숏 도구 (미리 보기)에 대 한 릴리스 정보

이 페이지에는 새로운 기능을 제공 하거나 오류를 해결 하기 위해 AzAcSnap에 적용 되는 주요 변경 내용이 나와 있습니다.

## <a name="march-2021"></a>3 월-2021

### <a name="azacsnap-v50-preview-build2021031830771"></a>AzAcSnap v 5.0 Preview (빌드: 20210318.30771)

AzAcSnap v 5.0 Preview (빌드: 20210318.30771)는 다음과 같은 수정 및 개선 사항으로 출시 되었습니다.

- SAP HANA 테 넌 트 Db에 AZACSNAP 사용자를 추가할 필요가 없습니다. [SAP HANA 통신 사용](azacsnap-installation.md#enable-communication-with-sap-hana) 섹션을 참조 하세요.
- 수동 QOS로 구성 된 볼륨으로 [복원을](azacsnap-cmd-ref-restore.md) 허용 하도록 수정 합니다.
- Azure Large Instance에 대 한 SSH 연결을 제한 하는 뮤텍스 컨트롤이 추가 되었습니다.
- 공백 및 기타 관련 문제를 포함 하는 경로 이름을 처리 하기 위한 설치 관리자를 수정 합니다.
- 다른 데이터베이스 서버 지원에 대 한 준비에서 선택적 매개 변수 '--hanasid '를 '--dbsid '로 변경 했습니다.

[최신 버전](https://aka.ms/azacsnapdownload) 의 설치 관리자를 다운로드 하 고 [시작](azacsnap-get-started.md)하는 방법을 검토 합니다.

## <a name="next-steps"></a>다음 단계

- [Azure 애플리케이션 일관성 있는 스냅숏 도구 시작](azacsnap-get-started.md)
