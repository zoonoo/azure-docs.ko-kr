---
title: Azure Active Directory Power BI 콘텐츠 팩 사용 방법 | Microsoft Docs
description: Azure Active Directory Power BI 콘텐츠 팩 사용 방법 알아보기
services: active-directory
author: priyamohanram
manager: mtillman
ms.assetid: addd60fe-d5ac-4b8b-983c-0736c80ace02
ms.service: active-directory
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.component: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: c2d121106218c0965cd8f4e07776cf8d2578543f
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/16/2019
ms.locfileid: "54354164"
---
# <a name="how-to-use-the-azure-active-directory-power-bi-content-pack"></a>Azure Active Directory Power BI 콘텐츠 팩 사용 방법

|  |
|--|
|현재, Azure AD Power BI 콘텐츠 팩은 Azure AD Graph API를 사용하여 Azure AD 테넌트에 데이터를 검색합니다. 결과적으로, 콘텐츠 팩에서 사용할 수 있는 데이터와 [보고용 Microsoft Graph API](concept-reporting-api.md)를 사용하여 검색한 데이터 간에 몇 가지 불일치를 확인할 수 있습니다. |
|  |

Azure Active Directory(Azure AD)용 Power BI 콘텐츠 팩에는 사용자들이 미리 빌드된 보고서가 포함되어 있습니다. 보고서에서 Power BI의 풍부한 시각화 환경을 사용하여 디렉터리 내의 모든 활동에 대한 인사이트를 얻을 수 있습니다. 사용자 고유의 대시보드를 만들어 조직 내의 모든 사용자와 공유할 수도 있습니다. 

## <a name="prerequisites"></a>필수 조건

콘텐츠 팩 사용을 위한 Azure AD Premium(P1/P2) 라이선스가 있어야 합니다. [Azure Active Directory Premium 시작하기](../fundamentals/active-directory-get-started-premium.md)를 참조하여 Azure Active Directory 버전을 업그레이드하세요.

## <a name="install-the-content-pack"></a>콘텐츠 팩 설치

Azure AD Power BI 콘텐츠 팩을 설치하려면 [빠른 시작](quickstart-install-power-bi-content-pack.md)을 확인하세요.

### <a name="reports-included-in-this-version-of-azure-ad-logs-content-pack"></a>이 버전의 Azure AD 로그 콘텐츠 팩에 포함된 보고서

Azure AD Power BI 콘텐츠 팩에는 다음과 같은 보고서가 포함되어 있습니다. 보고서에는 **지난 30일간**의 데이터가 포함됩니다.

**앱 사용 및 추세 보고서**:  이 보고서는 조직에서 사용되는 애플리케이션에 대한 인사이트를 제공합니다. 가장 인기 있는 애플리케이션의 목록을 확인하거나 조직에서 최근에 롤아웃된 애플리케이션이 어떻게 사용되고 있는지 파악할 수 있습니다. 이를 통해 시간 경과에 따른 사용량을 추적하고 개선할 수 있습니다.

**위치 및 사용자별 로그인**: 이 보고서는 Azure ID를 사용하여 수행된 모든 로그인에 대한 데이터를 제공합니다. 이를 통해 개별 로그인을 심층 분석하고 다음과 같은 질문에 답변할 수 있습니다.

- 이 사용자는 어디에서 로그인했는가?
- 어느 사용자가 가장 많이 로그인하고 어디에서 로그인하는가? 
- 성공적으로 로그인되었는가?  
 
특정 날짜 또는 위치를 선택하여 결과를 필터링할 수도 있습니다.

**앱당 고유 사용자**:  이 보고서는 지정된 앱을 사용하는 모든 고유 사용자를 보여줍니다. 여기에는 애플리케이션에 “성공적으로” 로그인한 사용자만 포함됩니다.

**디바이스 로그인**: 이 보고서는 조직 내에서 사용된 다양한 디바이스 프로필을 이해하고 용도에 따라 디바이스 정책을 결정하는 데 도움이 됩니다. 이 보고서는 애플리케이션에 로그인할 때 사용된 OS 및 브라우저 유형과 다음과 같은 상세한 사용자 정보를 제공합니다.

- 사용자 이름
- IP 주소
- 위치 
- 로그인 상태 

**SSPR 깔때기**: 이 보고서는 조직에서 SSPR 도구가 어떻게 사용되고 있는지 파악하는 데 도움이 됩니다. SSPR 도구를 통해 몇 차례의 암호 재설정이 시도되었고 그중 몇 건이 성공했는지 확인할 수 있습니다. 암호 재설정 실패를 심층 분석하고 특정 실패가 발생한 이유를 살펴볼 수도 있습니다. 

## <a name="customize-azure-ad-activity-content-pack"></a>Azure AD 활동 콘텐츠 팩 사용자 지정

**시각화 변경**:  **보고서 편집**을 클릭하여 보고서 시각화를 변경하고 원하는 시각화를 선택할 수 있습니다.
 
![Azure Active Directory Power BI 콘텐츠 팩](./media/howto-power-bi-content-pack/09.png) 
 
![Azure Active Directory Power BI 콘텐츠 팩](./media/howto-power-bi-content-pack/10.png) 

**추가 필드 포함**:  필드를 추가/제거하려는 시각적 개체를 선택하여 보고서에 필드를 추가하거나 제거할 수 있습니다. 예를 들어, 아래 그림과 같이 테이블 보기에 “로그인 상태” 필드를 추가할 수 있습니다. 
 
![Azure Active Directory Power BI 콘텐츠 팩](./media/howto-power-bi-content-pack/11.png) 

**시각화를 대시보드에 고정**:  대시보드를 사용자 지정하여 보고서에 시각화를 포함시켜 대시보드에 고정할 수 있습니다. 

![Azure Active Directory Power BI 콘텐츠 팩](./media/howto-power-bi-content-pack/13.png) 
 
**대시보드 공유**: 대시보드를 조직의 사용자와 공유할 수 있습니다. 보고서를 공유하면 보고서에서 선택한 필드를 사용자들이 볼 수 있습니다.
 
![Azure Active Directory Power BI 콘텐츠 팩](./media/howto-power-bi-content-pack/14.png) 

## <a name="schedule-a-daily-refresh-of-your-power-bi-report"></a>Power BI 보고서의 매일 새로 고침 예약

Power BI 보고서의 매일 새로 고침을 예약하려면 **데이터 세트** > **설정** > **새로 고침 예약**으로 이동하고 아래와 같이 설정합니다.
 
![Azure Active Directory Power BI 콘텐츠 팩](./media/howto-power-bi-content-pack/15.png) 

## <a name="update-to-newer-version-of-content-pack"></a>콘텐츠 팩의 최신 버전으로 업데이트

콘텐츠 팩을 최신 버전으로 업데이트하려면:

- 새 콘텐츠 팩을 다운로드하고 이 문서의 지침에 따라 설정합니다.

- 설정을 완료했으면 **데이터 원본** > **설정** > **데이터 원본 자격 증명**으로 이동하여 자격 증명을 다시 입력합니다.

    ![Azure Active Directory Power BI 콘텐츠 팩](./media/howto-power-bi-content-pack/16.png) 

콘텐츠 팩의 새 버전이 정상적으로 작동하면 필요한 경우 이전 버전의 콘텐츠 팩에 연결된 기본 보고서 및 데이터 세트를 삭제하여 이전 버전을 제거할 수 있습니다.

## <a name="troubleshoot-content-pack-errors"></a>콘텐츠 팩 오류 문제 해결

콘텐츠 팩에서 작업할 경우 다음과 같은 오류가 발생할 수 있습니다. 

- [새로 고침 실패](#refresh-failed) 
- [데이터 원본 자격 증명을 업데이트하지 못했습니다.](#failed-to-update-data-source-credentials) 
- [데이터를 가져오는 데 너무 오래 걸립니다.](#data-import-is-too-slow) 

Power BI와 관련된 일반적인 도움말은 이 [도움말 문서](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/)을 확인하세요.

### <a name="refresh-failed"></a>새로 고침 실패 
 
**이 오류가 표시되는 방법**: Power BI 또는 새로 고침 기록 실패 상태로 인해 이메일을 받습니다. 


| 원인 | 해결 방법 |
| ---   | ---        |
| 콘텐츠 팩에 연결되는 사용자의 자격 증명이 다시 설정되었지만 콘텐츠 팩의 연결 설정에서 업데이트되지 않은 경우 새로 고침 실패 오류가 발생할 수 있습니다. | Power BI에서 Azure AD 활동 로그 대시보드(**Azure Active Directory 활동 로그**)에 해당하는 데이터 세트를 찾아서 새로 고침 예약을 선택하고 Azure AD 자격 증명을 입력합니다. |
| 기본 콘텐츠 팩에서 발생한 데이터 문제로 인해 새로 고침에 실패할 수 있습니다. | [지원 티켓을 제출합니다](../fundamentals/active-directory-troubleshooting-support-howto.md).|
 
 
### <a name="failed-to-update-data-source-credentials"></a>데이터 원본 자격 증명을 업데이트하지 못했습니다. 
 
**이 오류가 표시되는 방법**: Power BI에서 Azure AD 활동 로그 콘텐츠 팩에 연결하면 표시됩니다. 

| 원인 | 해결 방법 |
| ---   | ---        |
| 연결된 사용자가 전역 관리자 또는 보안 읽기 권한자 또는 보안 관리자가 아닙니다. | 전역 관리자 또는 보안 읽기 권한자 또는 보안 관리자인 계정을 사용하여 콘텐츠 팩에 액세스합니다. |
| 사용자의 테넌트가 프리미엄 테넌트가 아니거나 적어도 프리미엄 라이선스 파일이 있는 사용자가 없습니다. | [지원 티켓을 제출합니다](../fundamentals/active-directory-troubleshooting-support-howto.md).|
 
### <a name="data-import-is-too-slow"></a>데이터 가져오기가 너무 느립니다. 
 
**이 오류가 표시되는 방법**: Power BI에서 콘텐츠 팩을 연결하면 데이터 가져오기 프로세스가 Azure AD 활동 로그를 위해 대시보드를 준비하기 시작합니다. 메시지가 표시됩니다. 더 이상 진행되지 않고 **데이터를 가져오는 중...** 이라는 메시지가 표시됩니다.  

| 원인 | 해결 방법 |
| ---   | ---        |
| 이 단계는 테넌트의 크기에 따라 몇 분에서 30분까지 걸릴 수 있습니다. | 한 시간 내에 메시지가 대시보드 표시로 변경되지 않으면 [지원 티켓을 제출합니다](../fundamentals/active-directory-troubleshooting-support-howto.md).|
  
## <a name="next-steps"></a>다음 단계

* [Power BI 콘텐츠 팩을 설치합니다](quickstart-install-power-bi-content-pack.md).
* [Azure AD 보고서란?](overview-reports.md)을 확인합니다.
