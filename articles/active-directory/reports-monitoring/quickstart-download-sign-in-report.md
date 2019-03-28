---
title: 빠른 시작 Azure Portal을 사용하여 로그인 보고서 다운로드 | Microsoft Docs
description: Azure Portal을 사용하여 로그인 보고서를 다운로드하는 방법 알아보기
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 9131f208-1f90-4cc1-9c29-085cacd69317
ms.service: active-directory
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 86bc72f69903134afa3750ad6b72486a713b6cc0
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2019
ms.locfileid: "58438125"
---
# <a name="quickstart-download-a-sign-in-report-using-the-azure-portal"></a>빠른 시작: Azure Portal을 사용하여 로그인 보고서 다운로드

이 빠른 시작에서는 지난 24시간 동안 테넌트에 대한 로그인 데이터를 다운로드하는 방법을 알아봅니다. Azure Portal에서 최대 250,000개의 레코드를 다운로드할 수 있습니다. 레코드는 가장 최근 순으로 정렬되므로 기본적으로 가장 최근의 250,000개 레코드를 가져옵니다. 

## <a name="prerequisites"></a>필수 조건

다음 작업을 수행해야 합니다.

* 로그인 활동 보고서를 볼 수 있는 프리미엄 라이선스를 가진 Azure Active Directory 테넌트. [Azure Active Directory Premium 시작하기](../fundamentals/active-directory-get-started-premium.md)를 참조하여 Azure Active Directory 버전을 업그레이드하세요. 업그레이드 전에 활동 데이터가 없었다면 프리미엄 라이선스로 업그레이드한 후 보고서에 데이터가 나타나기까지 며칠이 걸립니다.
* 테넌트에 대한 **보안 관리자**, **보안 읽기 권한자**, **보고서 읽기 권한자** 또는 **글로벌 관리자** 역할의 사용자 또한 테넌트의 모든 사용자는 자신의 로그인에 액세스할 수 있습니다.

## <a name="quickstart-download-a-sign-in-report"></a>빠른 시작: 로그인 보고서 다운로드

1. [Azure Portal](https://portal.azure.com)로 이동합니다.
2. 왼쪽 탐색 창에서 **Azure Active Directory**를 선택하고 **디렉터리 전환** 단추를 사용하여 Active Directory를 선택합니다.
3. 대시보드에서 **Azure Active Directory**를 선택한 다음, **로그인**을 선택합니다. 
4. **날짜** 필터 드롭다운에서 **지난 24시간**을 선택하고 **적용**을 선택하여 지난 24시간 동안의 로그인을 확인합니다. 
5. **다운로드** 단추를 선택하고 파일 형식으로 **CSV**를 선택한 후 필터링된 레코드가 포함된 CSV 파일을 다운로드할 파일 이름을 지정합니다. 

![보고](./media/quickstart-download-sign-in-report/download-sign-ins.png)

## <a name="next-steps"></a>다음 단계

* [Azure Active Directory 포털의 로그인 활동 보고서](concept-sign-ins.md)
* [Azure Active Directory Reporting 보존](reference-reports-data-retention.md)
* [Azure Active Directory Reporting 대기 시간](reference-reports-latencies.md)
