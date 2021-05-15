---
title: Azure Spring Cloud에서 권한 사용 방법
description: 이 문서에서는 Azure Spring Cloud에서 권한에 대한 사용자 지정 역할을 만드는 방법을 보여 줍니다.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 09/04/2020
ms.custom: devx-track-java
ms.openlocfilehash: 0c0b3bd81e0f73a7879382e28516378bd722bc17
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104878484"
---
# <a name="how-to-use-permissions-in-azure-spring-cloud"></a>Azure Spring Cloud에서 권한을 사용하는 방법
이 문서에서는 Azure Spring Cloud 리소스에 대한 권한을 위임하는 사용자 지정 역할을 만드는 방법을 보여 줍니다. 사용자 지정 역할은 다양한 스톡 권한으로 [Azure 기본 제공 역할](../role-based-access-control/built-in-roles.md)을 확장합니다.

다음 사용자 지정 역할을 구현합니다.

* **개발자 역할**: 
    * 배포
    * 테스트
    * 앱 다시 시작
    * Git 리포지토리에서 앱 구성을 적용하고 변경할 수 있음
    * 로그 스트림을 가져올 수 있음
* **운영 - 사이트 안정성 엔지니어링**: 
    * 앱 다시 시작
    * 로그 스트림 가져오기
    * 앱 또는 구성 변경할 수 없음
* **Azure Pipelines/Jenkins/Github 작업 역할**:
    * 만들기, 읽기, 업데이트 및 삭제 작업 수행할 수 있음
    * 서비스 인스턴스 내에 있는 모든 항목(Azure Pipelines, Jenkins 또는 GitHub 작업, Terraform 또는 ARM 템플릿 사용)을 만들고 구성할 수 있습니다.

## <a name="define-developer-role"></a>개발자 역할 정의

개발자 역할에는 앱을 다시 시작하고 로그 스트림을 볼 권한이 포함되지만 앱, 구성을 변경할 수는 없습니다.

### <a name="navigate-subscription-and-resource-group-access-control-iam"></a>구독 및 리소스 그룹 액세스 제어 탐색(IAM)

역할을 정의하기 시작하려면 다음 단계를 수행합니다.

1. Azure Portal에서 사용자 지정 역할을 할당할 수 있도록 하려는 구독 및 리소스 그룹을 엽니다.
2. **액세스 제어(IAM)** 를 엽니다.
3. **+ 추가** 를 클릭합니다.
4. **사용자 지정 역할 추가** 를 클릭합니다.
5. **다음** 을 클릭합니다.

   ![사용자 지정 역할 만들기](media/spring-cloud-permissions/create-custom-role.png)

6. **권한 추가** 를 클릭합니다.

   ![권한 추가 시작](media/spring-cloud-permissions/add-permissions.png)

### <a name="search-for-azure-spring-cloud-permissions"></a>Azure Spring Cloud 권한 검색:
7. 검색 상자에서 *Microsoft.app* 을 검색합니다.
*Microsoft Azure Spring Cloud* 를 선택합니다.

   ![Azure Spring Cloud 선택](media/spring-cloud-permissions/spring-cloud-permissions.png)

8. 개발자 역할에 대한 권한을 선택합니다.

**Microsoft.AppPlatform/Spring** 에서 다음을 선택합니다.
* 쓰기: Azure Spring Cloud 서비스 인스턴스 만들거나 업데이트
* 읽기: Azure Spring Cloud 서비스 인스턴스 가져오기
* 기타: Azure Spring Cloud 서비스 인스턴스 테스트 키 나열

**Microsoft.AppPlatform/Spring/apps** 에서 다음을 선택합니다.
* 읽기: Microsoft Azure Spring Cloud 애플리케이션 읽기
* 기타: Microsoft Azure Spring Cloud 애플리케이션 리소스 업로드 URL 가져오기

**Microsoft.AppPlatform/Spring/apps/bindings** 에서 다음을 선택합니다.
* 읽기: Microsoft Azure Spring Cloud 애플리케이션 바인딩 읽기

**Microsoft.AppPlatform/Spring/apps/deployments** 에서 다음을 선택합니다.
* 쓰기: Microsoft Azure Spring Cloud 애플리케이션 배포 작성
* 읽기: Microsoft Azure Spring Cloud 애플리케이션 배포 읽기
* 기타: Microsoft Azure Spring Cloud 애플리케이션 배포 시작
* 기타: Microsoft Azure Spring Cloud 애플리케이션 배포 중지
* 기타: Microsoft Azure Spring Cloud 애플리케이션 배포 다시 시작
* 기타: Microsoft Azure Spring Cloud 애플리케이션 배포 로그 파일 URL 가져오기

**Microsoft.AppPlatform/Spring/apps/domains** 에서 다음을 선택합니다.
* 읽기: Microsoft Azure Spring Cloud 애플리케이션 사용자 지정 도메인 읽기

**Microsoft.AppPlatform/Spring/certificates** 에서 다음을 선택합니다.
* 읽기: Microsoft Azure Spring Cloud 인증서 읽기

**Microsoft.AppPlatform/locations/operationResults/Spring** 에서 다음을 선택합니다.
* 읽기: 작업 결과 읽기

**Microsoft.AppPlatform/locations/operationStatus/operationId** 에서 다음을 선택합니다.
* 읽기: 작업 상태 읽기

    [ ![Developler 권한 만들기](media/spring-cloud-permissions/developer-permissions-box.png) ](media/spring-cloud-permissions/developer-permissions-box.png#lightbox)

9. **추가** 를 클릭합니다.

10. 권한을 검토합니다.

11. **검토 + 만들기** 를 클릭합니다.

## <a name="define-devops-engineer-role"></a>DevOps 엔지니어 역할 정의
이 절차에서는 Azure Spring Cloud 앱을 배포, 테스트 및 다시 시작할 권한이 있는 역할을 정의합니다.

1. 프로시저를 반복하여 구독, 리소스 그룹 및 액세스 제어(IAM)를 탐색합니다.
2. DevOps 엔지니어 역할에 대한 권한을 선택합니다.

**Microsoft.AppPlatform/Spring** 에서 다음을 선택합니다.
* 쓰기: Azure Spring Cloud 서비스 인스턴스 만들거나 업데이트
* 삭제: Azure Spring Cloud 서비스 인스턴스 삭제
* 읽기: Azure Spring Cloud 서비스 인스턴스 가져오기
* 기타: Azure Spring Cloud 서비스 인스턴스 테스트 엔드포인트 사용
* 기타: Azure Spring Cloud 서비스 인스턴스 테스트 엔드포인트 사용 안 함
* 기타: Azure Spring Cloud 서비스 인스턴스 테스트 키 나열
* 기타: Azure Spring Cloud 서비스 인스턴스 테스트 키 다시 생성

**Microsoft.AppPlatform/Spring/apps** 에서 다음을 선택합니다.
* 쓰기: Microsoft Azure Spring Cloud 애플리케이션 작성
* 삭제: Microsoft Azure Spring Cloud 애플리케이션 삭제
* 읽기: Microsoft Azure Spring Cloud 애플리케이션 읽기
* 기타: Microsoft Azure Spring Cloud 애플리케이션 리소스 업로드 URL 가져오기
* 기타: Microsoft Azure Spring Cloud 애플리케이션 사용자 지정 도메인 유효성 검사

**Microsoft.AppPlatform/Spring/apps/bindings** 에서 다음을 선택합니다.
* 쓰기: Microsoft Azure Spring Cloud 애플리케이션 바인딩 작성
* 삭제: Microsoft Azure Spring Cloud 애플리케이션 바인딩 삭제
* 읽기: Microsoft Azure Spring Cloud 애플리케이션 바인딩 읽기

**Microsoft.AppPlatform/Spring/apps/deployments** 에서 다음을 선택합니다.
* 쓰기: Microsoft Azure Spring Cloud 애플리케이션 배포 작성
* 삭제: Azure Spring Cloud 애플리케이션 배포 삭제
* 읽기: Microsoft Azure Spring Cloud 애플리케이션 배포 읽기
* 기타: Microsoft Azure Spring Cloud 애플리케이션 배포 시작
* 기타: Microsoft Azure Spring Cloud 애플리케이션 배포 중지
* 기타: Microsoft Azure Spring Cloud 애플리케이션 배포 다시 시작
* 기타: Microsoft Azure Spring Cloud 애플리케이션 배포 로그 파일 URL 가져오기

**Microsoft.AppPlatform/Spring/apps/deployments/skus** 에서 다음을 선택합니다.
* 읽기: 애플리케이션 배포를 사용할 수 있는 SKU 나열

**Microsoft.AppPlatform/locations** 에서 다음을 선택합니다.
* 기타: 이름 가용성을 확인합니다.

Microsoft.AppPlatform/locations/operationResults/Spring에서 다음을 선택합니다 읽기: 작업 결과 읽기

**Microsoft.AppPlatform/locations/operationStatus/operationId** 에서 다음을 선택합니다.
* 읽기: 작업 상태 읽기

**Microsoft.AppPlatform/skus** 에서 다음을 선택합니다.
* 읽기: 사용 가능한 SKU 나열

   [ ![Dev/Op 권한](media/spring-cloud-permissions/dev-ops-permissions.png) ](media/spring-cloud-permissions/dev-ops-permissions.png#lightbox)

3. **추가** 를 클릭합니다.

4. 권한을 검토합니다.

5. **검토 + 만들기** 를 클릭합니다.

## <a name="define-ops---site-reliability-engineering-role"></a>운영 - 사이트 안정성 엔지니어링 역할 정의
이 절차에서는 Azure Spring Cloud 앱을 배포, 테스트 및 다시 시작할 권한이 있는 역할을 정의합니다.

1. 프로시저를 반복하여 구독, 리소스 그룹 및 액세스 제어(IAM)를 탐색합니다.

2. 운영 - 사이트 안정성 엔지니어링 역할에 대한 권한을 선택합니다.

**Microsoft.AppPlatform/Spring** 에서 다음을 선택합니다.
* 읽기: Azure Spring Cloud 서비스 인스턴스 가져오기
* 기타: Azure Spring Cloud 서비스 인스턴스 테스트 키 나열

**Microsoft.AppPlatform/Spring/apps** 에서 다음을 선택합니다.
* 읽기: Microsoft Azure Spring Cloud 애플리케이션 읽기

**Microsoft.AppPlatform/apps/deployments** 에서 다음을 선택합니다.
* 읽기: Microsoft Azure Spring Cloud 애플리케이션 배포 읽기
* 기타: Microsoft Azure Spring Cloud 애플리케이션 배포 시작
* 기타: Microsoft Azure Spring Cloud 애플리케이션 배포 중지
* 기타: Microsoft Azure Spring Cloud 애플리케이션 배포 다시 시작

**Microsoft.AppPlatform/locations/operationResults/Spring** 에서 다음을 선택합니다.
* 읽기: 작업 결과 읽기

**Microsoft.AppPlatform/locations/operationStatus/operationId** 에서 다음을 선택합니다.
* 읽기: 작업 상태 읽기

   [ ![Ops/SRE 권한](media/spring-cloud-permissions/ops-sre-permissions.png) ](media/spring-cloud-permissions/ops-sre-permissions.png#lightbox)

3. **추가** 를 클릭합니다.

4. 권한을 검토합니다.

5. **검토 + 만들기** 를 클릭합니다.

## <a name="define-azure-pipelinesprovisioning-role"></a>Azure Pipelines/프로비전 역할 정의
이 Jenkins/Github 작업 역할은 Azure Spring Cloud 및 서비스 인스턴스를 사용하는 앱에서 모든 항목을 만들고 구성할 수 있습니다. 이 역할은 코드를 릴리스 또는 배포하는 데 사용할 수 있습니다.

1. 프로시저를 반복하여 구독, 리소스 그룹 및 액세스 제어(IAM)를 탐색합니다.

2. **권한** 옵션을 엽니다.

3. Azure Pipelines/프로비전 역할에 대한 권한을 선택합니다.
  
**Microsoft.AppPlatform/Spring** 에서 다음을 선택합니다.
* 쓰기: Azure Spring Cloud 서비스 인스턴스 만들거나 업데이트
* 삭제: Azure Spring Cloud 서비스 인스턴스 삭제
* 읽기: Azure Spring Cloud 서비스 인스턴스 가져오기
* 기타: Azure Spring Cloud 서비스 인스턴스 테스트 엔드포인트 사용
* 기타: Azure Spring Cloud 서비스 인스턴스 테스트 엔드포인트 사용 안 함
* 기타: Azure Spring Cloud 서비스 인스턴스 테스트 키 나열
* 기타: Azure Spring Cloud 서비스 인스턴스 테스트 키 다시 생성

**Microsoft.AppPlatform/Spring/apps** 에서 다음을 선택합니다.
* 쓰기: Microsoft Azure Spring Cloud 애플리케이션 작성
* 삭제: Microsoft Azure Spring Cloud 애플리케이션 삭제
* 읽기: Microsoft Azure Spring Cloud 애플리케이션 읽기
* 기타: Microsoft Azure Spring Cloud 애플리케이션 리소스 업로드 URL 가져오기
* 기타: Microsoft Azure Spring Cloud 애플리케이션 사용자 지정 도메인 유효성 검사

**Microsoft.AppPlatform/Spring/apps/bindings** 에서 다음을 선택합니다.
* 쓰기: Microsoft Azure Spring Cloud 애플리케이션 바인딩 작성
* 삭제: Microsoft Azure Spring Cloud 애플리케이션 바인딩 삭제
* 읽기: Microsoft Azure Spring Cloud 애플리케이션 바인딩 읽기

**Microsoft.AppPlatform/Spring/apps/deployments** 에서 다음을 선택합니다.
* 쓰기: Microsoft Azure Spring Cloud 애플리케이션 배포 작성
* 삭제: Azure Spring Cloud 애플리케이션 배포 삭제
* 읽기: Microsoft Azure Spring Cloud 애플리케이션 배포 읽기
* 기타: Microsoft Azure Spring Cloud 애플리케이션 배포 시작
* 기타: Microsoft Azure Spring Cloud 애플리케이션 배포 중지
* 기타: Microsoft Azure Spring Cloud 애플리케이션 배포 다시 시작
* 기타: Microsoft Azure Spring Cloud 애플리케이션 배포 로그 파일 URL 가져오기

**Microsoft.AppPlatform/skus** 에서 다음을 선택합니다.
* 읽기: 사용 가능한 SKU 나열

**Microsoft.AppPlatform/locations** 에서 다음을 선택합니다.
* 기타: 이름 가용성을 확인합니다.

**Microsoft.AppPlatform/locations/operationResults/Spring** 에서 다음을 선택합니다.
* 읽기: 작업 결과 읽기

**Microsoft.AppPlatform/locations/operationStatus/operationId** 에서 다음을 선택합니다.
* 읽기: 작업 상태 읽기

**Microsoft.AppPlatform/skus** 에서 다음을 선택합니다.
* 읽기: 사용 가능한 SKU 나열

   [ ![파이프라인 권한](media/spring-cloud-permissions/pipelines-permissions-box.png) ](media/spring-cloud-permissions/pipelines-permissions-box.png#lightbox)  

4. **추가** 를 클릭합니다.

5. 권한을 검토합니다.

6. **검토 + 만들기** 를 클릭합니다.


## <a name="see-also"></a>참고 항목
* [Azure Portal을 사용하여 Azure 사용자 지정 역할 만들기 또는 업데이트](../role-based-access-control/custom-roles-portal.md)

사용자 지정 권한을 정의하는 세 가지 방법에 대한 자세한 내용은 다음을 참조하세요.
* [역할 복제](../role-based-access-control/custom-roles-portal.md#clone-a-role)
* [처음부터 시작](../role-based-access-control/custom-roles-portal.md#start-from-scratch)
* [JSON에서 시작](../role-based-access-control/custom-roles-portal.md#start-from-json)