---
title: NXLog LinuxAudit 데이터를 Azure Sentinel에 연결하기 | Microsoft Docs
description: NXLog LinuxAudit 데이터 커넥터를 사용하여 LinuxAudit 로그를 Azure Sentinel로 끌어오는 방법을 알아봅니다. 통합 문서의 LinuxAudit 데이터를 확인하고 경고를 생성하고 조사를 개선합니다.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/02/2021
ms.author: yelevin
ms.openlocfilehash: 6b10a0c4b1a655d998d9a418dde679c0c6b68b10
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122567816"
---
# <a name="connect-your-nxlog-linuxaudit-to-azure-sentinel"></a>NXLog LinuxAudit을 Azure Sentinel에 연결하기

> [!IMPORTANT]
> NXLog LinuxAudit 커넥터는 현재 **미리 보기** 중입니다. 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

[NXLog LinuxAudit](https://nxlog.co/documentation/nxlog-user-guide/im_linuxaudit.html) 커넥터를 통하여 실시간으로 Linux 보안 이벤트를 Azure Sentinel에 간편하게 가져올 수 있어, 전체 조직에 걸쳐 도메인 이름 서버 활동에 대한 통찰력이 생깁니다. NXLog LinuxAudit 모듈은 사용자 지정 감사 규칙을 지원하고 auditd 또는 기타 사용자 공간 소프트웨어 없이 로그를 수집합니다. IP 주소 및 그룹/사용자 ID는 [Linux 감사](https://nxlog.co/documentation/nxlog-user-guide/linux-audit.html) 로그를 보안 분석가들이 더욱 많은 정보를 활용할 수 있는 형태로 만들어 주는 각각의 이름으로 확인됩니다. NXLog LinuxAudit과 Azure Sentinel 간의 통합은 REST API를 통하여 촉진됩니다.

> [!NOTE]
> 데이터는 Azure Sentinel을 실행하는 작업 영역의 지리적 위치에 저장됩니다.

## <a name="configure-and-connect-nxlog-linuxaudit"></a>NXLog LinuxAudit 구성 및 연결하기

NXLog를 구성하여 이벤트를 Azure Sentinel에 JSON 형식으로 바로 보내도록 할 수 있습니다.

1. Azure Sentinel 포털에서, **데이터 커넥터** 를 클릭한 뒤 **NXLog LinuxAudit** 커넥터를 선택합니다.

1. **커넥터 페이지 열기** 를 선택합니다.

1. *NXLog 사용자 가이드* 통합 주제 [Microsoft Azure Sentinel](https://nxlog.co/documentation/nxlog-user-guide/sentinel.html)의 단계별 지침을 따라 REST API를 통한 착신 전환을 구성합니다.

## <a name="find-your-data"></a>데이터 찾기

연결이 설정되면 데이터는 *LinuxAudit_CL* 테이블의 **사용자 지정 로그** 섹션에 있는 **로그** 에 표시됩니다.

## <a name="validate-connectivity"></a>연결 유효성 검사

로그가 Log Analytics에 표시될 때까지 최대 20분가량 소요될 수 있습니다.

## <a name="next-steps"></a>다음 단계

본 문서에서, NXLog LinuxAudit을 사용하여 Linux 보안 이벤트를 Azure Sentinel에 수집하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.

- [데이터 및 잠재적 위협에 대한 가시성을 확보](get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](detect-threats-built-in.md)을 시작합니다.
- [통합 문서를 사용](monitor-your-data.md)하여 데이터를 모니터링합니다.