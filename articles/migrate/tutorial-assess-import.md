---
title: Azure Migrate 서버 평가에서 가져온 서버 데이터를 사용하여 서버 평가
description: Azure Migrate 서버 평가에서 가져온 데이터를 사용하여 Azure로 마이그레이션할 온-프레미스 서버를 평가하는 방법에 대해 설명합니다.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 10/23/2019
ms.author: raynew
ms.openlocfilehash: 91b9c71e7c735fca08f71ca37ed28734c8d634a1
ms.sourcegitcommit: 72c2da0def8aa7ebe0691612a89bb70cd0c5a436
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/10/2020
ms.locfileid: "79079866"
---
# <a name="assess-servers-by-using-imported-data"></a>가져온 데이터를 사용하여 서버 평가

이 문서에서는 서버 메타데이터를 CSV(쉼표로 구분된 값) 형식으로 가져와 [Azure Migrate: 서버 평가](migrate-services-overview.md#azure-migrate-server-assessment-tool) 도구를 사용하여 온-프레미스 서버를 평가하는 방법에 대해 설명합니다. 이 평가 방법에서는 평가를 만들도록 Azure Migrate 어플라이언스를 설정할 필요가 없습니다. 다음과 같은 경우에 유용합니다.

- 어플라이언스를 배포하기 전에 빠른 초기 평가를 만들려고 합니다.
- 조직에 Azure Migrate 어플라이언스를 배포할 수 없습니다.
- 온-프레미스 서버에 대한 액세스를 허용하는 자격 증명을 공유할 수 없습니다.
- 보안 제약 조건 때문에 어플라이언스가 수집한 데이터를 Azure에 보낼 수 없습니다. 가져온 파일에서 공유하는 데이터를 제어할 수 있습니다. 또한 대부분의 데이터(예: IP 주소 제공)는 선택 사항입니다.

## <a name="before-you-start"></a>시작하기 전에

다음 사항에 유의하세요.

- 단일 CSV 파일에서 최대 2만 개의 서버를 추가할 수 있습니다.
- CSV를 사용하여 Azure Migrate 프로젝트에서 최대 2만 개의 서버를 추가할 수 있습니다.
- CSV를 사용하여 서버 정보를 서버 평가에 여러 번 업로드할 수 있습니다.
- 애플리케이션 정보를 수집하는 것은 마이그레이션할 온-프레미스 환경을 평가하는 데 유용합니다. 그러나 서버 평가는 현재 평가를 만들 때 애플리케이션 수준 평가를 수행하거나 애플리케이션을 고려하지 않습니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.
> [!div class="checklist"]
> * Azure Migrate 프로젝트를 설정합니다.
> * 서버 정보를 CSV 파일에 채웁니다.
> * 해당 파일을 가져와서 서버 정보를 서버 평가에 추가합니다.
> * 평가를 만들고 검토합니다.

> [!NOTE]
> 이 자습서에서는 개념 증명을 빠르게 설정할 수 있도록 시나리오에 대한 가장 간단한 배포 경로를 보여 줍니다. 자습서는 가능한 경우 기본 옵션을 사용하며, 가능한 모든 설정과 경로는 보여 주지 않습니다. 자세한 지침은 방법 가이드를 검토하세요.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/pricing/free-trial/)을 만듭니다.

## <a name="set-azure-permissions-for-azure-migrate"></a>Azure Migrate에 대한 Azure 권한 설정

Azure 계정에는 Azure Migrate 프로젝트를 만들 수 있는 권한이 있어야 합니다.

1. Azure Portal에서 구독을 열고, **액세스 제어(IAM)** 를 선택합니다.
2. **액세스 권한 확인**에서 관련 계정을 찾은 다음, 이를 선택하여 권한을 확인합니다.
3. **기여자** 또는 **소유자** 권한이 있는지 확인합니다.
    - Azure 체험 계정을 방금 만든 경우 자신이 구독에 대한 소유자입니다.
    - 구독 소유자가 아닌 경우 해당 역할을 할당해 주도록 소유자에게 문의합니다.

## <a name="set-up-an-azure-migrate-project"></a>Azure Migrate 프로젝트 설정

새 Azure Migrate 프로젝트를 설정하려면 다음을 수행합니다.

1. Azure Portal의 **모든 서비스**에서 **Azure Migrate**를 검색합니다.
2. **서비스** 아래에서 **Azure Migrate**를 선택합니다.
3. **개요**의 **서버 검색, 평가 및 마이그레이션** 아래에서 **서버 평가 및 마이그레이션**을 선택합니다.

    ![서버 검색 및 평가](./media/tutorial-assess-import/assess-migrate.png)

4. **시작**에서**도구 추가**를 선택합니다.
5. **프로젝트 마이그레이션**에서 Azure 구독을 선택하고, 아직 없는 경우 리소스 그룹을 만듭니다.
6. **프로젝트 세부 정보**에서 프로젝트 이름과 이 프로젝트를 만들려는 지역을 지정합니다. 자세한 내용은 다음을 참조하세요.

    - [지원되는 지역](migrate-support-matrix.md#supported-geographies)을 검토합니다. 프로젝트 지역은 온-프레미스 VM에서 수집된 메타데이터를 저장하는 데만 사용됩니다.
    - 대상 지역은 마이그레이션을 실행할 때 선택할 수 있습니다.

    ![Azure Migrate 프로젝트 만들기](./media/tutorial-assess-import/migrate-project.png)

7. **다음**을 선택합니다.
8. **평가 도구 선택**에서 **Azure Migrate: 서버 평가** > **다음**을 차례로 선택합니다.

    ![Azure Migrate 평가 만들기](./media/tutorial-assess-import/assessment-tool.png)

9. **마이그레이션 도구 선택**에서 **마이그레이션 도구 추가 건너뛰기** > **다음**을 차례로 선택합니다.
10. **검토 + 도구 추가**에서 설정을 검토한 다음, **도구 추가**를 선택합니다.
11. Azure Migrate 프로젝트가 배포될 때까지 몇 분 정도 기다립니다. 그러면 프로젝트 페이지로 이동됩니다. 프로젝트가 표시되지 않으면 Azure Migrate 대시보드의 **서버**에서 액세스할 수 있습니다.

## <a name="prepare-the-csv"></a>CSV 준비

CSV 템플릿을 다운로드하고, 여기에 서버 정보를 추가합니다.

### <a name="download-the-template"></a>템플릿 다운로드

1. **마이그레이션 목표** > **서버** > **Azure Migrate: 서버 평가**에서 **검색**을 선택합니다.
2. **머신 검색**에서 **CSV를 사용하여 가져오기**를 선택합니다.
3. **다운로드**를 선택하여 CSV 템플릿을 다운로드합니다. 또는 [직접 다운로드](https://go.microsoft.com/fwlink/?linkid=2109031)할 수 있습니다.

    ![CSV 템플릿 다운로드](./media/tutorial-assess-import/download-template.png)

### <a name="add-server-information"></a>서버 정보 추가

서버 데이터를 수집하여 CSV 파일에 추가합니다.

- 데이터를 수집하려면 VMware vSphere 또는 CMDB(구성 관리 데이터베이스)와 같은 온-프레미스 서버를 관리하는 데 사용하는 도구에서 데이터를 내보낼 수 있습니다.
- 예제 데이터를 검토하려면 [예제 파일](https://go.microsoft.com/fwlink/?linkid=2108405)을 다운로드합니다.

다음 표에서는 채울 파일 필드를 요약하여 보여 줍니다.

**필드 이름** | **필수** | **세부 정보**
--- | --- | ---
**서버 이름** | 예 | FQDN(정규화된 도메인 이름)을 지정하는 것이 좋습니다.
**IP 주소** | 예 | 서버 주소
**코어 수** | 예 | 서버에 할당된 프로세서 코어의 수
**메모리** | 예 | 서버에 할당된 총 RAM 크기(MB)
**OS 이름** | 예 | 서버 운영 체제 <br/> [이](#supported-operating-system-names) 목록의 이름과 일치하거나 이를 포함하는 운영 체제 이름은 평가에서 인식됩니다.
**OS 버전** | 예 | 서버 운영 체제 버전
**디스크 수** | 예 | 개별 디스크 세부 정보가 제공되는 경우 필요하지 않습니다.
**디스크 1 크기**  | 예 | 최대 디스크 크기(GB)<br/>템플릿에 [열을 추가](#add-multiple-disks)하여 추가 디스크에 대한 세부 정보를 추가할 수 있습니다. 최대 8개의 디스크를 추가할 수 있습니다.
**디스크 1 읽기 작업** | 예 | 초당 디스크 읽기 작업
**디스크 1 쓰기 작업** | 예 | 초당 디스크 쓰기 작업
**디스크 1 읽기 처리량** | 예 | 초당 디스크에서 읽은 데이터(MB/초)
**디스크 1 쓰기 처리량** | 예 | 초당 디스크에 쓴 데이터(MB/초)
**CPU 사용률** | 예 | 사용된 CPU 백분율
**메모리 사용률** | 예 | 사용된 RAM 백분율
**총 디스크 읽기 작업** | 예 | 초당 디스크 읽기 작업 수
**총 디스크 쓰기 작업** | 예 | 초당 디스크 쓰기 작업 수
**총 디스크 읽기 처리량** | 예 | 초당 디스크에서 읽은 데이터(MB/초)
**총 디스크 쓰기 처리량** | 예 | 초당 디스크에 쓴 데이터(MB/초)
**네트워크 입력 처리량** | 예 | 서버에서 받은 데이터(MB/초)
**네트워크 출력 처리량** | 예 | 서버에서 전송한 데이터(MB/초)
**펌웨어 유형** | 예 | 서버 펌웨어 값은 "BIOS" 또는 "UEFI"일 수 있습니다.
**서버 유형** | 예 | 값은 "물리적" 또는 "가상"일 수 있습니다.
**하이퍼바이저** | 예 | 컴퓨터가 실행 중인 하이퍼바이저입니다. <br/> 값은 "VMware", "Hyper-V", "Xen", "AWS", "GCP" 또는 "기타"일 수 있습니다.
**하이퍼바이저 버전 번호** | 예 | 하이퍼바이저 버전
**가상 머신 ID** | 예 | VM 식별자. VMware vCenter VM의 경우 **InstanceUUid** 값이고, Hyper-V의 경우 **Hyper-V VM ID**입니다.
**Virtual Machine Manager ID** | 예 | VMWare vCenter의 경우 **InstanceUUid** 값입니다. Hyper-V에는 필요하지 않습니다.
**MAC 주소**| 예 | 서버 MAC 주소
**BIOS ID** | 예 | 서버 BIOS ID
**사용자 지정 서버 ID** | 예 | 온-프레미스의 고유한 로컬 서버 ID. <br/> 가져온 서버를 로컬 ID로 추적하는 데 유용합니다.
**애플리케이션 1 이름** | 예 | 서버에서 실행되는 워크로드의 이름.<br/>템플릿에 [열을 추가](#add-multiple-applications)하여 추가 앱에 대한 세부 정보를 추가할 수 있습니다. 최대 5개의 애플리케이션을 추가할 수 있습니다.
**애플리케이션 1 유형** | 예 | 서버에서 실행되는 워크로드의 유형
**애플리케이션 1 버전** | 예 | 서버에서 실행 중인 워크로드의 버전
**애플리케이션 1 라이선스 만료** | 예 | 워크로드의 라이선스 만료(해당하는 경우)
**사업부** | 예 | 서버가 속한 사업부
**비즈니스 소유자** | 예 | 사업부 소유자
**비즈니스 애플리케이션 이름** | 예 | 앱이 속한 애플리케이션의 이름
**위치** | 예 | 서버가 있는 데이터 센터
**서버 서비스 해제 날짜** | 예 | 물리적 서버 또는 가상 서버의 기본 물리적 서버의 서비스 해제 날짜

### <a name="add-operating-systems"></a>운영 체제 추가

평가는 특정 운영 체제 이름을 인식합니다. 지정하는 이름은 [지원되는 이름](#supported-operating-system-names) 목록의 문자열 중 하나와 정확히 일치해야 합니다.

### <a name="add-multiple-disks"></a>여러 디스크 추가

템플릿은 첫 번째 디스크의 기본 필드를 제공합니다. 최대 8개의 디스크에 대해 비슷한 열을 추가할 수 있습니다.

예를 들어 두 번째 디스크의 모든 필드를 지정하려면 다음 열을 추가합니다.

- 디스크 2 크기
- 디스크 2 읽기 작업 수
- 디스크 2 쓰기 작업 수
- 디스크 2 읽기 처리량
- 디스크 2 쓰기 처리량

### <a name="add-multiple-applications"></a>여러 애플리케이션 추가

템플릿은 단일 애플리케이션에 대한 필드를 제공합니다. 최대 5개 앱에 대해 비슷한 열을 추가할 수 있습니다.  

예를 들어 두 번째 앱의 모든 필드를 지정하려면 다음 열을 추가합니다.

- 애플리케이션 2 이름
- 애플리케이션 2 유형
- 애플리케이션 2 버전
- 애플리케이션 2 라이선스 만료

> [!NOTE]
> 앱 정보는 마이그레이션할 온-프레미스 환경을 평가하는 데 유용합니다. 그러나 Azure Migrate 서버 평가는 현재 평가를 만들 때 앱 수준 평가를 수행하거나 앱을 고려하지 않습니다.

## <a name="import-the-server-information"></a>서버 정보 가져오기

정보가 CSV 템플릿에 추가되면 서버를 서버 평가로 가져옵니다.

1. Azure Migrate의 **머신 검색**에서 완료된 템플릿으로 이동합니다.
2. **가져오기**를 선택합니다.
3. 가져오기 상태가 표시됩니다.
    - 상태에 경고가 표시되면 해당 경고를 해결하거나 이를 해결하지 않고 계속 진행할 수 있습니다.
    - 평가 정확도를 높이기 위해 경고에서 제시한 대로 서버 정보를 향상시킵니다.
    - 경고를 확인하고 해결하려면 **경고 정보 .CSV 다운로드**를 선택합니다. 이 작업에서는 경고가 포함된 CSV를 다운로드합니다. 경고를 검토하고, 필요에 따라 문제를 해결합니다.
    - 상태에 오류가 표시되어 가져오기 상태가 **실패**이면 가져오기를 계속하기 전에 해당 오류를 해결해야 합니다.
        1. 이제 오류 세부 정보가 포함된 CSV를 다운로드합니다.
        1. 필요에 따라 오류를 검토하고 해결합니다. 
        1. 수정된 파일을 다시 업로드합니다.
4. 가져오기 상태가 **완료됨**이면 서버 정보를 가져온 것입니다.

## <a name="update-server-information"></a>서버 정보 업데이트

동일한 **서버 이름**을 사용하여 서버 데이터를 다시 가져와 서버 정보를 업데이트할 수 있습니다. **서버 이름** 필드는 수정할 수 없습니다. 서버 삭제는 현재 지원되지 않습니다.

## <a name="verify-servers-in-the-portal"></a>포털에서 서버 확인

검색 후 Azure Portal에서 해당 서버가 표시되는지 확인하려면 다음을 수행합니다.

1. Azure Migrate 대시보드를 엽니다.
2. **Azure Migrate - 서버** > **Azure Migrate: 서버 평가** 페이지에서 **검색된 서버**의 수를 표시하는 아이콘을 선택합니다.
3. **가져오기 기반** 탭을 선택합니다.

## <a name="set-up-and-run-an-assessment"></a>평가 설정 및 실행

서버 평가를 사용하여 두 가지 유형의 평가를 만들 수 있습니다.

**평가 유형** | **세부 정보** | **Data**
--- | --- | ---
**성능 기반** | 지정된 성능 데이터 값을 기반으로 하는 평가 | **추천 VM 크기**: CPU 및 메모리 사용량 데이터를 기반으로 합니다.<br/><br/> **추천 디스크 유형(표준 또는 프리미엄 관리 디스크)** : 온-프레미스 디스크의 IOPS(초당 입/출력) 및 처리량을 기반으로 합니다.
**온-프레미스로** | 온-프레미스 크기 조정을 기반으로 하는 평가 | **추천 VM 크기**: 지정된 서버 크기를 기반으로 합니다.<br/><br> **추천 디스크 유형**: 평가를 위해 선택한 스토리지 유형 설정을 기반으로 합니다.

평가를 실행하려면 다음을 수행합니다.

1. 평가를 만드는 방법에 대한 [모범 사례](best-practices-assessment.md)를 검토합니다.
2. **서버** 탭의 **Azure Migrate: 서버 평가** 타일에서 **평가**를 선택합니다.

    ![평가](./media/tutorial-assess-physical/assess.png)

3. **서버 평가**에서 평가 이름을 지정합니다.
4. **검색 원본**에서 **Azure Migrate로 가져오기를 통해 추가된 머신**을 선택합니다.
5. **모두 보기**를 선택하여 평가 속성을 검토합니다.

    ![평가 속성](./media/tutorial-assess-physical/view-all.png)

6. **그룹 선택 또는 만들기**에서 **새로 만들기**를 선택하고 그룹 이름을 지정합니다. 그룹은 평가를 위해 하나 이상의 VM을 수집합니다.
7. **그룹에 머신 추가**에서 그룹에 추가할 서버를 선택합니다.
8. **평가 만들기**를 선택하여 그룹을 만든 다음, 평가를 실행합니다.

    ![평가 만들기](./media/tutorial-assess-physical/assessment-create.png)

9. 평가가 만들어지면 **서버** > **Azure Migrate: 서버 평가** > **평가**에서 해당 평가를 확인합니다.
10. **평가 내보내기**를 선택하여 Microsoft Excel 파일로 다운로드합니다.

## <a name="review-an-assessment"></a>평가 검토

평가에서 설명하는 항목은 다음과 같습니다.

- **Azure 준비 상태**: 서버가 Azure로 마이그레이션하는 데 적합한지 여부입니다.
- **월간 예상 비용**: Azure에서 서버를 실행하는 데 들어가는 월별 예상 컴퓨팅 및 스토리지 비용입니다.
- **월간 예상 스토리지 비용**: 마이그레이션 후 디스크 스토리지에 대한 예상 비용입니다.

### <a name="view-an-assessment"></a>평가 보기

1. **마이그레이션 목표** > **서버**의 **Azure Migrate: 서버 평가**에서 **평가**를 클릭합니다.
2. **평가**에서 평가를 선택하여 엽니다.

    ![평가 요약](./media/tutorial-assess-physical/assessment-summary.png)

### <a name="review-azure-readiness"></a>Azure 준비 상태 검토

1. **Azure 준비 상태**에서 서버가 Azure로 마이그레이션할 준비가 되었는지 여부를 확인합니다.
2. 상태를 검토합니다.
    - **Azure 준비 완료**: Azure Migrate는 평가에서 VM의 크기 및 예상 비용을 추천합니다.
    - **조건과 함께 준비 완료**: 문제 및 제안된 수정 사항을 보여 줍니다.
    - **Azure를 사용할 준비 안 됨**: 문제 및 제안된 수정 사항을 보여 줍니다.
    - **알 수 없는 준비**: 데이터 가용성 문제로 인해 Azure Migrate에서 준비 상태를 평가할 수 없습니다.

3. **Azure 준비 상태** 상태를 선택합니다. 서버 준비 상태 세부 정보를 살펴보고 드릴다운하여 컴퓨팅, 스토리지 및 네트워크 설정을 포함한 서버 세부 정보를 확인할 수 있습니다.

### <a name="review-cost-details"></a>비용 세부 정보 검토

이 보기에서는 Azure에서 VM을 실행하는 데 들어가는 예상 컴퓨팅 및 스토리지 비용을 표시합니다. 다음을 수행할 수 있습니다.

- 월간 컴퓨팅 및 스토리지 비용을 검토합니다. 비용은 평가된 그룹의 모든 서버에 대해 집계됩니다.

    - 예상 비용은 머신의 크기 추천 사항과 해당 디스크 및 속성을 기반으로 합니다.
    - 컴퓨팅 및 스토리지의 월간 예상 비용이 표시됩니다.
    - 예상 비용은 온-프레미스 서버를 IaaS(Infrastructure as a service) VM으로 실행하는 데 소요되는 비용입니다. 서버 평가는 PaaS(Platform as a Service) 또는 SaaS(Software as a Service) 비용을 고려하지 않습니다.

- 월별 예상 스토리지 비용을 검토합니다. 이 보기에서는 여러 유형의 스토리지 디스크로 분할되어 평가된 그룹에 대해 집계된 스토리지 비용을 보여 줍니다.
- 드릴다운하여 특정 VM에 대한 세부 정보를 확인합니다.

> [!NOTE]
> CSV를 사용하여 서버 평가로 가져온 서버의 평가에는 신뢰 등급이 할당되지 않습니다.

## <a name="supported-operating-system-names"></a>지원되는 운영 체제 이름

<!-- BEGIN A - H -->

:::row:::
   :::column span="2":::
      **A - H**
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      Apple Mac OS X 10
   :::column-end:::
   :::column span="":::
      Asianux 3<br/>
      Asianux 4<br/>
      Asianux 5
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      CentOS<br/>
      CentOS 4/5
   :::column-end:::
   :::column span="":::
      CoreOS Linux
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      Debian GNU/Linux 4<br/>
      Debian GNU/Linux 5<br/>
      Debian GNU/Linux 6<br/>
      Debian GNU/Linux 7<br/>
      Debian GNU/Linux 8
   :::column-end:::
   :::column span="":::
      FreeBSD
   :::column-end:::
:::row-end:::

<!-- BEGIN I - R -->

:::row:::
   :::column span="2":::
      **I - R**
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      IBM OS/2
   :::column-end:::
   :::column span="":::
      MS-DOS
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      Novell NetWare 5<br/>
      Novell NetWare 6
   :::column-end:::
   :::column span="":::
      Oracle Linux<br/>
       Oracle Linux 4/5<br/>
      Oracle Solaris 10<br/>
       Oracle Solaris 11
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      Red Hat Enterprise Linux 2<br/>
      Red Hat Enterprise Linux 3<br/>
      Red Hat Enterprise Linux 4<br/>
      Red Hat Enterprise Linux 5<br/>
      Red Hat Enterprise Linux 6<br/>
      Red Hat Enterprise Linux 7<br/>
      Red Hat Fedora
   :::column-end:::
:::row-end:::

<!-- BEGIN S - T -->

:::row:::
   :::column span="2":::
      **S - T**
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      SCO OpenServer 5<br/>
      SCO OpenServer 6<br/>
      SCO UnixWare 7
   :::column-end:::
   :::column span="":::
      Serenity Systems eComStation 1<br/>
      Serenity Systems eComStation 2
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      Sun Microsystems Solaris 8<br/>
      Sun Microsystems Solaris 9
   :::column-end:::
   :::column span="":::
      SUSE Linux Enterprise 10<br/>
      SUSE Linux Enterprise 11<br/>
      SUSE Linux Enterprise 12<br/>
      SUSE Linux Enterprise 8/9<br/>
      SUSE Linux Enterprise 11<br/>
      SUSE openSUSE
   :::column-end:::
:::row-end:::

<!-- BEGIN U - Z -->
:::row:::
   :::column span="2":::
      **U - Z**
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      Ubuntu Linux
   :::column-end:::
   :::column span="":::
      VMware ESXi 4<br/>
      VMware ESXi 5<br/>
      VMware ESXi 6
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      윈도우 10<br/>
      Windows 2000<br/>
      Windows 3<br/>
      Windows 7<br/>
      Windows 8<br/>
      Windows 95<br/>
      Windows 98<br/>
      Windows NT<br/>
      Windows Server (R) 2008<br/>
      Windows Server 2003
   :::column-end:::
   :::column span="":::
      Windows Server 2008<br/>
      Windows Server 2008 R2<br/>
      Windows Server 2012<br/>
      Windows Server 2012 R2<br/>
      Windows Server 2016<br/>
      Windows Server 2019<br/>
      Windows Server Threshold<br/>
      Windows Vista<br/>
      Windows Web Server 2008 R2<br/>
      Windows XP Professional
   :::column-end:::
:::row-end:::

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음을 수행합니다.

> [!div class="checklist"]
> * CSV를 사용하여 서버를 Azure Migrate: 서버 평가로 가져왔습니다.
> * 평가를 만들고 검토했습니다.

이제 더 정확한 평가를 위해 [어플라이언스를 배포](./migrate-appliance.md)하고, 더 자세한 평가를 위해 [종속성 분석](./concepts-dependency-visualization.md)을 사용하여 서버를 그룹으로 모읍니다.
