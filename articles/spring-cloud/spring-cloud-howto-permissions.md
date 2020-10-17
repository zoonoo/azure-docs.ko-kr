---
title: Azure 스프링 클라우드에서 사용 권한 사용
description: 이 문서에서는 Azure 스프링 클라우드에서 사용 권한에 대 한 사용자 지정 역할을 만드는 방법을 보여 줍니다.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 09/04/2020
ms.custom: devx-track-java
ms.openlocfilehash: 635f1e03596d55101b6158353ed8cdc278212ceb
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/17/2020
ms.locfileid: "92155485"
---
# <a name="how-to-use-permissions-in-azure-spring-cloud"></a>Azure 스프링 클라우드에서 사용 권한을 사용 하는 방법
이 문서에서는 Azure 스프링 클라우드 리소스에 대 한 권한을 위임 하는 사용자 지정 역할을 만드는 방법을 보여 줍니다. 사용자 지정 역할은 다양 한 스톡 권한으로 [기본 제공 Azure 역할](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) 을 확장 합니다.

다음 사용자 지정 역할을 구현 합니다.

* **개발자 역할**: 
    * 배포
    * 테스트
    * 앱 다시 시작
    * Git 리포지토리에서 앱 구성을 적용 하 고 변경할 수 있습니다.
    * 로그 스트림을 가져올 수 있습니다.
* **Ops-사이트 안정성 엔지니어링**: 
    * 앱 다시 시작
    * 로그 스트림 가져오기
    * 앱 또는 구성을 변경할 수 없습니다.
* **Azure Pipelines/Jenkins/Github 작업 역할**:
    * 만들기, 읽기, 업데이트, 삭제 작업을 수행할 수 있습니다.
    * 서비스 인스턴스 내에 있는 모든 항목 (Azure Pipelines, Jenkins 또는 GitHub 작업, Terraform 또는 ARM 템플릿 사용)을 만들고 구성할 수 있습니다.

## <a name="define-developer-role"></a>개발자 역할 정의

개발자 역할에는 앱을 다시 시작 하 고 로그 스트림을 볼 수 있는 권한이 있지만 앱, 구성을 변경할 수 없습니다.

### <a name="navigate-subscription-and-resource-group-access-control-iam"></a>구독 및 리소스 그룹 액세스 제어 (IAM)를 탐색 합니다.

역할 정의를 시작 하려면 다음 단계를 수행 합니다.

1. Azure Portal에서 사용자 지정 역할을 할당할 수 있도록 하려는 구독 및 리소스 그룹을 엽니다.
2. **액세스 제어 (IAM)** 를 엽니다.
3. **+ 추가**를 클릭합니다.
4. **사용자 지정 역할 추가**를 클릭 합니다.
5. **다음**을 클릭합니다.

   ![사용자 지정 역할 만들기](media/spring-cloud-permissions/create-custom-role.png)

6. **권한 추가**를 클릭합니다.

   ![권한 추가 시작](media/spring-cloud-permissions/add-permissions.png)

### <a name="search-for-azure-spring-cloud-permissions"></a>Azure 스프링 클라우드 권한 검색:
7. 검색 상자에서 *Microsoft 앱*을 검색 합니다.
*Microsoft Azure 스프링 클라우드*를 선택 합니다.

   ![Azure 스프링 클라우드 선택](media/spring-cloud-permissions/spring-cloud-permissions.png)

8. 개발자 역할에 대 한 권한을 선택 합니다.

**Microsoft AppPlatform/스프링**에서 다음을 선택 합니다.
* 쓰기: Azure 스프링 클라우드 서비스 인스턴스 만들기 또는 업데이트
* 읽기: Azure 스프링 클라우드 서비스 인스턴스 가져오기
* 기타: Azure 스프링 클라우드 서비스 인스턴스 테스트 키를 나열 합니다.

**Microsoft AppPlatform/스프링/apps**에서 다음을 선택 합니다.
* 읽기: Microsoft Azure 스프링 클라우드 응용 프로그램 읽기
* 기타: Microsoft Azure 스프링 클라우드 응용 프로그램 리소스 업로드 URL 가져오기

**Microsoft AppPlatform/스프링/apps/바인딩에서**다음을 선택 합니다.
* 읽기: Microsoft Azure 스프링 클라우드 응용 프로그램 바인딩 읽기

**Microsoft AppPlatform/스프링/apps/배포**에서 다음을 선택 합니다.
* 쓰기: Microsoft Azure 스프링 클라우드 응용 프로그램 배포 작성
* 읽기: Microsoft Azure 스프링 클라우드 응용 프로그램 배포 읽기
* 기타: Microsoft Azure 스프링 클라우드 응용 프로그램 배포 시작
* 기타: Microsoft Azure 스프링 클라우드 응용 프로그램 배포를 중지 합니다.
* 기타: 스프링 클라우드 응용 프로그램 배포를 다시 시작 Microsoft Azure
* 기타: Microsoft Azure 스프링 클라우드 응용 프로그램 배포 로그 파일 URL 가져오기

**Microsoft AppPlatform/스프링/apps/도메인**에서 다음을 선택 합니다.
* 읽기: Microsoft Azure 스프링 클라우드 응용 프로그램 사용자 지정 도메인 읽기

**Microsoft AppPlatform/스프링/certificate**에서 다음을 선택 합니다.
* 읽기: Microsoft Azure 스프링 클라우드 인증서 읽기

**Microsoft AppPlatform/위치/operationResults/스프링**에서 다음을 선택 합니다.
* 읽기: 작업 결과 읽기

**Microsoft AppPlatform/위치/operationStatus/operationId**에서 다음을 선택 합니다.
* 읽기: 작업 상태 읽기

    [![Developler 권한 ](media/spring-cloud-permissions/developer-permissions-box.png) 만들기](media/spring-cloud-permissions/developer-permissions-box.png#lightbox)

9. **추가**를 클릭합니다.

10. 사용 권한을 검토 합니다.

11. **검토 + 만들기**를 클릭합니다.

## <a name="define-devops-engineer-role"></a>DevOps 엔지니어 역할 정의
이 절차에서는 Azure 스프링 클라우드 앱을 배포, 테스트 및 다시 시작할 수 있는 권한이 있는 역할을 정의 합니다.

1. 프로시저를 반복 하 여 구독, 리소스 그룹 및 액세스 액세스 제어 (IAM)를 탐색 합니다.
2. DevOps 엔지니어 역할에 대 한 권한을 선택 합니다.

**Microsoft AppPlatform/스프링**에서 다음을 선택 합니다.
* 쓰기: Azure 스프링 클라우드 서비스 인스턴스 만들기 또는 업데이트
* 삭제: Azure 스프링 클라우드 서비스 인스턴스 삭제
* 읽기: Azure 스프링 클라우드 서비스 인스턴스 가져오기
* 기타: Azure 스프링 클라우드 서비스 인스턴스 테스트 끝점 사용
* 기타: Azure 스프링 클라우드 서비스 인스턴스 테스트 끝점 사용 안 함
* 기타: Azure 스프링 클라우드 서비스 인스턴스 테스트 키를 나열 합니다.
* 기타: Azure 스프링 클라우드 서비스 인스턴스 테스트 키 다시 생성

**Microsoft AppPlatform/스프링/apps**에서 다음을 선택 합니다.
* 쓰기: Microsoft Azure 스프링 클라우드 응용 프로그램 작성
* 삭제: 스프링 클라우드 응용 프로그램을 삭제 Microsoft Azure
* 읽기: Microsoft Azure 스프링 클라우드 응용 프로그램 읽기
* 기타: Microsoft Azure 스프링 클라우드 응용 프로그램 리소스 업로드 URL 가져오기
* 기타: Microsoft Azure 스프링 클라우드 응용 프로그램 사용자 지정 도메인 유효성 검사

**Microsoft AppPlatform/스프링/apps/바인딩에서**다음을 선택 합니다.
* 쓰기: 스프링 클라우드 응용 프로그램 바인딩을 Microsoft Azure 씁니다.
* 삭제: Microsoft Azure 스프링 클라우드 응용 프로그램 바인딩을 삭제 합니다.
* 읽기: Microsoft Azure 스프링 클라우드 응용 프로그램 바인딩 읽기

**Microsoft AppPlatform/스프링/apps/배포**에서 다음을 선택 합니다.
* 쓰기: Microsoft Azure 스프링 클라우드 응용 프로그램 배포 작성
* 삭제: Azure 스프링 클라우드 응용 프로그램 배포 삭제
* 읽기: Microsoft Azure 스프링 클라우드 응용 프로그램 배포 읽기
* 기타: Microsoft Azure 스프링 클라우드 응용 프로그램 배포 시작
* 기타: Microsoft Azure 스프링 클라우드 응용 프로그램 배포를 중지 합니다.
* 기타: 스프링 클라우드 응용 프로그램 배포를 다시 시작 Microsoft Azure
* 기타: Microsoft Azure 스프링 클라우드 응용 프로그램 배포 로그 파일 URL 가져오기

**Microsoft AppPlatform/스프링/apps/배포/sku**에서 다음을 선택 합니다.
* 읽기: 응용 프로그램 배포를 사용할 수 있는 sku 나열

**Microsoft AppPlatform/위치**에서 다음을 선택 합니다.
* 기타: 이름 사용 가능 여부 확인

Microsoft AppPlatform/위치/operationResults/스프링 select: 읽기: 읽기 작업 결과

**Microsoft AppPlatform/위치/operationStatus/operationId**에서 다음을 선택 합니다.
* 읽기: 작업 상태 읽기

**Microsoft AppPlatform/sku**에서 다음을 선택 합니다.
* 읽기: 사용 가능한 sku 나열

   [![Dev/Op 권한 ](media/spring-cloud-permissions/dev-ops-permissions.png)](media/spring-cloud-permissions/dev-ops-permissions.png#lightbox)

3. **추가**를 클릭합니다.

4. 사용 권한을 검토 합니다.

5. **검토 + 만들기**를 클릭합니다.

## <a name="define-ops---site-reliability-engineering-role"></a>Ops 사이트 안정성 엔지니어링 역할 정의
이 절차에서는 Azure 스프링 클라우드 앱을 배포, 테스트 및 다시 시작할 수 있는 권한이 있는 역할을 정의 합니다.

1. 프로시저를 반복 하 여 구독, 리소스 그룹 및 액세스 액세스 제어 (IAM)를 탐색 합니다.

2. Ops 사이트 안정성 엔지니어링 역할에 대 한 권한을 선택 합니다.

**Microsoft AppPlatform/스프링**에서 다음을 선택 합니다.
* 읽기: Azure 스프링 클라우드 서비스 인스턴스 가져오기
* 기타: Azure 스프링 클라우드 서비스 인스턴스 테스트 키를 나열 합니다.

**Microsoft AppPlatform/스프링/apps**에서 다음을 선택 합니다.
* 읽기: Microsoft Azure 스프링 클라우드 응용 프로그램 읽기

**Microsoft AppPlatform/apps/배포**에서 다음을 선택 합니다.
* 읽기: Microsoft Azure 스프링 클라우드 응용 프로그램 배포 읽기
* 기타: Microsoft Azure 스프링 클라우드 응용 프로그램 배포 시작
* 기타: Microsoft Azure 스프링 클라우드 응용 프로그램 배포를 중지 합니다.
* 기타: 스프링 클라우드 응용 프로그램 배포를 다시 시작 Microsoft Azure

**Microsoft AppPlatform/위치/operationResults/스프링**에서 다음을 선택 합니다.
* 읽기: 작업 결과 읽기

**Microsoft AppPlatform/위치/operationStatus/operationId**에서 다음을 선택 합니다.
* 읽기: 작업 상태 읽기

   [![Ops/SRE 권한 ](media/spring-cloud-permissions/ops-sre-permissions.png)](media/spring-cloud-permissions/ops-sre-permissions.png#lightbox)

3. **추가**를 클릭합니다.

4. 사용 권한을 검토 합니다.

5. **검토 + 만들기**를 클릭합니다.

## <a name="define-azure-pipelinesprovisioning-role"></a>Azure Pipelines/프로 비전 역할 정의
이 Jenkins/Github 작업 역할은 Azure 스프링 클라우드 및 서비스 인스턴스를 사용 하는 앱에서 모든 항목을 만들고 구성할 수 있습니다. 이 역할은 코드를 릴리스 또는 배포 하는 데 사용할 수 있습니다.

1. 프로시저를 반복 하 여 구독, 리소스 그룹 및 액세스 액세스 제어 (IAM)를 탐색 합니다.

2. **권한** 옵션을 엽니다.

3. Azure Pipelines/프로 비전 역할에 대 한 권한을 선택 합니다.
  
**Microsoft AppPlatform/스프링**에서 다음을 선택 합니다.
* 쓰기: Azure 스프링 클라우드 서비스 인스턴스 만들기 또는 업데이트
* 삭제: Azure 스프링 클라우드 서비스 인스턴스 삭제
* 읽기: Azure 스프링 클라우드 서비스 인스턴스 가져오기
* 기타: Azure 스프링 클라우드 서비스 인스턴스 테스트 끝점 사용
* 기타: Azure 스프링 클라우드 서비스 인스턴스 테스트 끝점 사용 안 함
* 기타: Azure 스프링 클라우드 서비스 인스턴스 테스트 키를 나열 합니다.
* 기타: Azure 스프링 클라우드 서비스 인스턴스 테스트 키 다시 생성

**Microsoft AppPlatform/스프링/apps**에서 다음을 선택 합니다.
* 쓰기: Microsoft Azure 스프링 클라우드 응용 프로그램 작성
* 삭제: 스프링 클라우드 응용 프로그램을 삭제 Microsoft Azure
* 읽기: Microsoft Azure 스프링 클라우드 응용 프로그램 읽기
* 기타: Microsoft Azure 스프링 클라우드 응용 프로그램 리소스 업로드 URL 가져오기
* 기타: Microsoft Azure 스프링 클라우드 응용 프로그램 사용자 지정 도메인 유효성 검사

**Microsoft AppPlatform/스프링/apps/바인딩에서**다음을 선택 합니다.
* 쓰기: 스프링 클라우드 응용 프로그램 바인딩을 Microsoft Azure 씁니다.
* 삭제: Microsoft Azure 스프링 클라우드 응용 프로그램 바인딩을 삭제 합니다.
* 읽기: Microsoft Azure 스프링 클라우드 응용 프로그램 바인딩 읽기

**Microsoft AppPlatform/스프링/apps/배포**에서 다음을 선택 합니다.
* 쓰기: Microsoft Azure 스프링 클라우드 응용 프로그램 배포 작성
* 삭제: Azure 스프링 클라우드 응용 프로그램 배포 삭제
* 읽기: Microsoft Azure 스프링 클라우드 응용 프로그램 배포 읽기
* 기타: Microsoft Azure 스프링 클라우드 응용 프로그램 배포 시작
* 기타: Microsoft Azure 스프링 클라우드 응용 프로그램 배포를 중지 합니다.
* 기타: 스프링 클라우드 응용 프로그램 배포를 다시 시작 Microsoft Azure
* 기타: Microsoft Azure 스프링 클라우드 응용 프로그램 배포 로그 파일 URL 가져오기

**Microsoft AppPlatform/sku**에서 다음을 선택 합니다.
* 읽기: 사용 가능한 sku 나열

**Microsoft AppPlatform/위치**에서 다음을 선택 합니다.
* 기타: 이름 사용 가능 여부 확인

**Microsoft AppPlatform/위치/operationResults/스프링**에서 다음을 선택 합니다.
* 읽기: 작업 결과 읽기

**Microsoft AppPlatform/위치/operationStatus/operationId**에서 다음을 선택 합니다.
* 읽기: 작업 상태 읽기

**Microsoft AppPlatform/sku**에서 다음을 선택 합니다.
* 읽기: 사용 가능한 sku 나열

   [![파이프라인 권한 ](media/spring-cloud-permissions/pipelines-permissions-box.png)](media/spring-cloud-permissions/pipelines-permissions-box.png#lightbox)  

4. **추가**를 클릭합니다.

5. 사용 권한을 검토 합니다.

6. **검토 + 만들기**를 클릭합니다.


## <a name="see-also"></a>참고 항목
* [Azure Portal을 사용하여 Azure 사용자 지정 역할 만들기 또는 업데이트](https://docs.microsoft.com/azure/role-based-access-control/custom-roles-portal)

사용자 지정 권한을 정의 하는 세 가지 방법에 대 한 자세한 내용은 다음을 참조 하세요.
* [역할 복제](https://docs.microsoft.com/azure/role-based-access-control/custom-roles-portal#clone-a-role)
* [처음부터 시작](https://docs.microsoft.com/azure/role-based-access-control/custom-roles-portal#start-from-scratch)
* [JSON에서 시작](https://docs.microsoft.com/azure/role-based-access-control/custom-roles-portal#start-from-json)