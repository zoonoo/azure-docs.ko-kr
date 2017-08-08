---
title: "Azure RBAC 문제 해결 | Microsoft Docs"
description: "역할 기반 액세스 제어 리소스에 대해 발생하는 문제 또는 질문 사항에 대한 도움말을 봅니다."
services: azure-portal
documentationcenter: na
author: kgremban
manager: femila
ms.assetid: df42cca2-02d6-4f3c-9d56-260e1eb7dc44
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: kgremban
ms.reviewer: rqureshi
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 9ad194308d30ca652b32ec3b76750b0e838472f4
ms.contentlocale: ko-kr
ms.lasthandoff: 07/21/2017

---
# <a name="role-based-access-control-troubleshooting"></a>역할 기반 액세스 제어 문제 해결

이 문서에서는 역할과 함께 부여되는 특정 액세스 권한에 대한 일반적인 질문에 대한 답변을 제공합니다. 따라서 Azure Portal에서 역할을 사용할 때 예상되는 상황을 이해하고 액세스 문제를 해결하는 데 도움이 될 수 있습니다. 이러한 세 가지 역할이 모든 리소스 유형에 적용됩니다.

* 소유자  
* 참여자  
* 판독기  

소유자와 참여자 양쪽 모두 관리 환경에 대한 모든 권한을 가집니다. 하지만, 참여자의 경우 다른 사용자나 그룹에 액세스 권한을 부여할 수 없다는 것입니다. 읽기 권한자 역할은 좀 더 복잡하므로 자세히 파악해야 합니다. 액세스를 부여하는 방법에 대한 세부 정보는 [역할 기반 액세스 제어 시작 문서](role-based-access-control-configure.md)를 참조하세요.

## <a name="app-service-workloads"></a>앱 서비스 워크로드
### <a name="write-access-capabilities"></a>쓰기 액세스 기능
사용자에게 단일 웹앱에 대한 읽기 전용 액세스를 부여하면 예상치 않게 일부 기능을 사용하지 못할 수 있습니다. 다음 관리 기능을 사용하려면 참여자나 소유자에게 제공되는 웹앱에 대한 **쓰기** 권한이 필요하며, 읽기 전용 시나리오에서는 이러한 기능을 사용할 수 없습니다.

* 시작, 중지 등의 명령
* 일반 구성, 규모 설정, 백업 설정, 모니터링 설정 등의 설정 변경
* 게시 자격 증명과 앱 설정, 연결 문자열 등의 기타 암호 액세스
* 스트리밍 로그
* 진단 로그 구성
* 콘솔(명령 프롬프트)
* 활성 및 최근 배포(로컬 Git 연속 배포의 경우)
* 예상 소요 시간
* 웹 테스트
* 가상 네트워크(쓰기 권한이 있는 사용자가 이전에 가상 네트워크를 구성한 경우에만 읽기 권한자에게 표시됨)

이러한 타일에 액세스할 수 없는 경우 관리자에게 웹앱에 대한 참가자 권한을 요청해야 합니다.

### <a name="dealing-with-related-resources"></a>관련 리소스 사용
웹앱에서 몇 가지 리소스가 상호 연동되는 경우 웹앱의 구조가 복잡해집니다. 아래에는 웹 사이트 몇 개가 포함된 일반적인 리소스 그룹이 나와 있습니다.

![웹앱 리소스 그룹](./media/role-based-access-control-troubleshooting/website-resource-model.png)

따라서 사용자에게 웹앱에 대한 권한만 부여하면 Azure Portal에서 웹 사이트 블레이드의 기능을 대부분 사용할 수 없게 됩니다.

다음 항목을 사용하려면 웹 사이트에 해당하는 **App Service 계획**에 대한 **쓰기** 권한이 필요합니다.  

* 웹앱의 가격 책정 계층 보기(무료 또는 표준)  
* 크기 조정 구성(인스턴스 수, 가상 컴퓨터 크기, 자동 크기 조정 설정)  
* 할당량(저장소, 대역폭, CPU)  

다음 항목을 사용하려면 웹 사이트를 포함하는 전체 **리소스 그룹**에 대한 **쓰기** 권한이 필요합니다.  

* SSL 인증서 및 바인딩(SSL 인증서는 같은 리소스 그룹과 지리적 위치의 사이트 간에 공유될 수 있음)  
* 경고 규칙  
* 자동 크기 조정 설정  
* Application Insights 구성 요소  
* 웹 테스트  

## <a name="virtual-machine-workloads"></a>가상 컴퓨터 작업
웹앱과 마찬가지로 가상 컴퓨터 블레이드의 일부 기능 역시 가상 컴퓨터 또는 리소스 그룹의 기타 리소스에 대한 쓰기 권한이 있어야 사용할 수 있습니다.

가상 컴퓨터는 도메인 이름, 가상 네트워크, 저장소 계정 및 경고 규칙과 관련이 있습니다.

다음 항목을 사용하려면 **가상 컴퓨터**에 대한 **쓰기** 권한이 필요합니다.

* 끝점  
* IP 주소  
* 디스크  
* 확장  

다음 항목을 사용하려면 **가상 컴퓨터**와 가상 컴퓨터가 속한 **리소스 그룹**(도메인 이름 포함) 둘 다에 대한 **쓰기** 권한이 필요합니다.  

* 가용성 집합  
* 부하 분산된 집합  
* 경고 규칙  

이러한 타일에 액세스할 수 없는 경우 관리자에게 리소스 그룹에 대한 참가자 권한을 요청합니다.

## <a name="see-more"></a>자세히 보기
* [역할 기반 액세스 제어](role-based-access-control-configure.md): Azure 포털에서 RBAC를 통해 시작합니다.
* [기본 제공 역할](role-based-access-built-in-roles.md): RBAC에서 표준이 되는 역할에 대한 세부 정보를 봅니다.
* [Azure RBAC에서 사용자 지정 역할](role-based-access-control-custom-roles.md): 액세스 요구 사항에 맞게 사용자 지정 역할을 만드는 방법에 대해 알아봅니다.
* [액세스 변경 기록 보고서 만들기](role-based-access-control-access-change-history-report.md): RBAC에서 역할 할당 변경을 추적합니다.


