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
ms.date: 05/27/2021
ms.author: phjensen
ms.openlocfilehash: fd8d4e1bfed60aa8f3eae4d4d3c033247ab1268d
ms.sourcegitcommit: e1d5abd7b8ded7ff649a7e9a2c1a7b70fdc72440
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2021
ms.locfileid: "110579909"
---
# <a name="release-notes-for-azure-application-consistent-snapshot-tool"></a>Azure 애플리케이션 일치 스냅숏 도구의 릴리스 정보

이 페이지에는 새 기능을 제공하거나 결함을 해결하기 위해 AzAcSnap에서 변경된 주요 내용이 나와 있습니다.

## <a name="may-2021"></a>2021년 5월

### <a name="azacsnap-v501-build-2021052414837---patch-update-to-v50"></a>AzAcSnap v 5.0.1(빌드: 20210524.14837) - v5.0에 대한 패치 업데이트

AzAcSnap v5.0.1(빌드: 20210524.14837)은 다음 수정 사항 및 개선 사항이 포함된 v5.0 분기에 대한 패치 업데이트로 제공됩니다.

- 종료 코드 처리가 개선되었습니다.  경우에 따라 0이 아니어야 하는 실행 실패가 있는 경우에도 종료 코드 0(영)이 내보내집니다.  이제 종료 코드는 `azacsnap`을 실행하여 완료할 때만 0이 되어야 하고 실패할 경우에는 0이 아니어야 합니다.  또한 AzAcSnap의 내부 오류 처리가 확장되어 AzAcSnap이 실행하는 외부 명령(예: hdbsql, ssh)의 종료 코드(예: hdbsql, ssh)를 캡처하고 내보냅니다(실패의 원인인 경우).

설치 관리자의 [최신 릴리스](https://aka.ms/azacsnapdownload)를 다운로드하고 [시작](azacsnap-get-started.md) 방법을 검토하세요.

## <a name="april-2021"></a>2021년 4월

### <a name="azacsnap-v50-build-202104216349---ga-released-21-april-2021"></a>AzAcSnap v5.0(빌드: 20210421.6349) - GA 릴리스(2021년 4월 21일)

AzAcSnap v5.0(빌드: 20210421.6349)이 일반 공급되었으며 이 빌드에 대해 다음과 같은 수정 사항 및 개선 사항이 있습니다.

- 경합 상태를 방지하기 위해 hdbsql 다시 시도 시간 제한(SAP HANA의 응답을 기다리기 위함)은 "savePointAbortWaitSeconds"의 절반으로 자동 설정됩니다.  "savePointAbortWaitSeconds" 설정은 JSON 구성 파일에서 직접 수정할 수 있으며 최소 600초여야 합니다.

## <a name="march-2021"></a>2021년 3월

### <a name="azacsnap-v50-preview-build2021031830771"></a>AzAcSnap v5.0 Preview(빌드:20210318.30771)

AzAcSnap v5.0 Preview(빌드:20210318.30771)는 다음과 같은 수정 사항 및 개선 사항을 포함하여 릴리스되었습니다.

- AZACSNAP 사용자를 SAP HANA 테넌트 DB에 추가할 필요가 없어졌습니다. [SAP HANA와의 통신 사용](azacsnap-installation.md#enable-communication-with-sap-hana) 섹션을 참조하세요.
- 수동 QOS로 구성된 볼륨으로 [복원](azacsnap-cmd-ref-restore.md)을 허용하도록 수정합니다.
- Azure 대규모 인스턴스에 대한 SSH 연결을 제한하는 뮤텍스 컨트롤이 추가되었습니다.
- 공백 및 기타 관련 이슈가 있는 경로 이름을 처리하기 위해 설치 관리자를 수정했습니다.
- 다른 데이터베이스 서버를 지원하기 위해 선택적 매개 변수 '--hanasid'를 '--dbsid'로 변경했습니다.

## <a name="next-steps"></a>다음 단계

- [Azure 애플리케이션 일치 스냅샷 도구 시작](azacsnap-get-started.md)
