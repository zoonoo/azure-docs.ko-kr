---
title: Azure Pipelines 및 Azure 애플리케이션 Insights를 사용 하 여 DevOps 릴리스 파이프라인의 지속적인 모니터링 | Microsoft Docs
description: Application Insights를 사용 하 여 연속 모니터링을 신속 하 게 설정 하는 지침을 제공 합니다.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 07/16/2019
ms.openlocfilehash: c891cc7564a60dfd665d84d569e266332d255fa9
ms.sourcegitcommit: 1bd2207c69a0c45076848a094292735faa012d22
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72677527"
---
# <a name="add-continuous-monitoring-to-your-release-pipeline"></a>릴리스 파이프라인에 연속 모니터링 추가

Azure Pipelines는 Azure 애플리케이션 정보를 통합 하 여 소프트웨어 개발 수명 주기 동안 DevOps 릴리스 파이프라인을 지속적으로 모니터링할 수 있습니다. 

연속 모니터링을 사용 하 여 릴리스 파이프라인은 Application Insights 및 기타 Azure 리소스의 모니터링 데이터를 통합할 수 있습니다. 릴리스 파이프라인이 Application Insights 경고를 감지 하면 파이프라인은 경고가 해결 될 때까지 배포를 게이트 하거나 롤백할 수 있습니다. 모든 검사를 통과 하는 경우에는 수동 작업 없이도 배포가 자동으로 테스트에서 프로덕션으로 진행 될 수 있습니다. 

## <a name="configure-continuous-monitoring"></a>연속 모니터링 구성

1. [Azure DevOps](https://dev.azure.com)에서 조직 및 프로젝트를 선택 합니다.
   
1. 프로젝트 페이지의 왼쪽 메뉴에서 **파이프라인**  > **릴리스**를 선택 합니다. 
   
1. **새로 만들기** 옆에 있는 화살표를 드롭다운 하 고 **새 릴리스 파이프라인**을 선택 합니다. 또는 파이프라인이 아직 없는 경우 표시 되는 페이지에서 **새 파이프라인** 을 선택 합니다.
   
1. **템플릿 선택** 창에서 **연속 모니터링을 사용 하 여 Azure App Service 배포**를 검색 하 고 선택 하 고 **적용**을 선택 합니다. 

   ![새 Azure Pipelines 릴리스 파이프라인](media/continuous-monitoring/001.png)

1. **단계 1** 상자에서 **스테이지 태스크를 볼** 수 있는 하이퍼링크를 선택 합니다.

   ![스테이지 작업 보기](media/continuous-monitoring/002.png)

1. **단계 1** 구성 창에서 다음 필드를 완료 합니다. 

    | 매개 변수를 포함해야 합니다.        | Value |
   | ------------- |:-----|
   | **단계 이름**      | 스테이지 이름을 제공 하거나 **1 단계**에 그대로 둡니다. |
   | **Azure 구독** | 드롭다운 및 사용 하려는 연결 된 Azure 구독을 선택 합니다.|
   | **앱 유형** | 드롭다운 하 고 앱 유형을 선택 합니다. |
   | **App Service 이름** | Azure App Service 이름을 입력 합니다. |
   | **Application Insights에 대 한 리소스 그룹 이름**    | 드롭다운 및 사용 하려는 리소스 그룹을 선택 합니다. |
   | **Application Insights 리소스 이름** | 드롭다운에서 선택한 리소스 그룹에 대 한 Application Insights 리소스를 선택 합니다.

1. 기본 경고 규칙 설정을 사용 하 여 파이프라인을 저장 하려면 Azure DevOps 창에서 오른쪽 위에 있는 **저장** 을 선택 합니다. 설명 설명을 입력 한 다음 **확인**을 선택 합니다.

## <a name="modify-alert-rules"></a>경고 규칙 수정

기본적으로 **연속 모니터링 템플릿을 사용 하는 Azure App Service 배포** 에는 **가용성**, **실패 한 요청**, **서버 응답 시간**및 **서버 예외의**네 가지 경고 규칙이 있습니다. 규칙을 추가 하거나 서비스 수준 요구 사항에 맞게 규칙 설정을 변경할 수 있습니다. 

경고 규칙 설정을 수정 하려면:

1. 릴리스 파이프라인 페이지의 왼쪽 창에서 **Application Insights 경고 구성**을 선택 합니다.

1. **Azure Monitor 경고** 창에서 **경고 규칙**옆에 있는 **줄임표 (...)** 를 선택 합니다.
   
1. **경고 규칙** 대화 상자에서 **가용성**과 같은 경고 규칙 옆에 있는 드롭다운 기호를 선택 합니다. 
   
1. 요구 사항에 맞게 **임계값** 및 기타 설정을 수정 합니다.
   
   ![경고 수정](media/continuous-monitoring/003.png)
   
1. **확인**을 선택 하 고 Azure devops 창에서 오른쪽 위에 있는 **저장** 을 선택 합니다. 설명 설명을 입력 한 다음 **확인**을 선택 합니다.

## <a name="add-deployment-conditions"></a>배포 조건 추가

릴리스 파이프라인에 배포 게이트를 추가 하는 경우 설정 된 임계값을 초과 하는 경고는 원치 않는 릴리스 프로 모션을 방지 합니다. 경고를 해결 하면 배포가 자동으로 진행 될 수 있습니다.

배포 게이트를 추가 하려면:

1. 주 파이프라인 페이지의 **스테이지**에서 연속 모니터링 게이트가 필요한 단계에 따라 **배포 전 조건** 또는 **배포 후 조건** 기호를 선택 합니다.
   
   ![배포 전 조건](media/continuous-monitoring/004.png)
   
1. **배포 전 조건** 구성 창에서 **게이트** 를 **사용**으로 설정 합니다.
   
1. **배포 게이트**옆에서 **추가**를 선택 합니다.
   
1. 드롭다운 메뉴에서 **쿼리 Azure Monitor 경고** 를 선택 합니다. 이 옵션을 사용 하면 Azure Monitor 및 Application Insights 경고에 모두 액세스할 수 있습니다.
   
   ![Azure Monitor 경고 쿼리](media/continuous-monitoring/005.png)
   
1. **평가 옵션**아래에서 게이트 재평가 시간 및 **게이트가 실패 한 후의 시간 제한** **사이의 시간과** 같은 설정에 대해 원하는 값을 입력 합니다. 

## <a name="view-release-logs"></a>릴리스 로그 보기

릴리스 로그에서 배포 게이트 동작 및 기타 릴리스 단계를 확인할 수 있습니다. 로그를 열려면 다음을 수행 합니다.

1. 파이프라인 페이지의 왼쪽 메뉴에서 **릴리스** 를 선택 합니다. 
   
1. 릴리스를 선택 합니다. 
   
1. 스테이지 **에서 모든**단계를 선택 하 여 릴리스 요약을 확인 합니다. 
   
1. 로그를 보려면 릴리스 요약에서 **로그 보기** 를 선택 하 고, 모든 단계에서 **성공** 또는 **실패** 한 하이퍼링크를 선택 하거나, 단계를 마우스로 가리키고 **로그**를 선택 합니다. 
   
   ![릴리스 로그 보기](media/continuous-monitoring/006.png)

## <a name="next-steps"></a>다음 단계

Azure Pipelines에 대 한 자세한 내용은 [Azure Pipelines 설명서](https://docs.microsoft.com/azure/devops/pipelines)를 참조 하세요.
