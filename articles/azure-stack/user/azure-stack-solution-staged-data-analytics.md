---
title: Azure 및 Azure Stack을 사용 하 여 준비 된 데이터 분석 솔루션 만들기 | Microsoft Docs
description: Azure 및 Azure Stack을 사용 하 여 준비 된 데이터 분석 솔루션을 만드는 방법에 알아봅니다.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/04/2018
ms.author: mabrigg
ms.reviewer: anajod
ms.lastreviewed: 12/01/2018
ms.openlocfilehash: 1115e11d6bf830afad3746eb41d6368cb89bdbf3
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/07/2019
ms.locfileid: "57534381"
---
# <a name="tutorial-create-a-staged-data-analytics-solution-with-azure-and-azure-stack"></a>자습서: Azure 및 Azure Stack을 사용 하 여 준비 된 데이터 분석 솔루션 만들기 

*적용 대상: Azure Stack 통합 시스템 및 Azure Stack 개발 키트*

온-프레미스 및 공용 클라우드 환경 모두를 사용 하 여 다중 기능 엔터프라이즈의 요구 사항을 충족 하는 방법에 알아봅니다. Azure Stack은 수집, 처리, 저장 및 보안, 기밀성, 회사 정책 및 규정 요구 사항 간에 다를 수 위치 하는 경우에 특히 로컬 및 원격 데이터를 배포에 대 한 보안, 빠르고 유연한 솔루션을 제공 합니다. 및 사용자 지정 합니다.

이 패턴에서는 고객에 게 빠른 내릴 수 있도록 컬렉션 시점에서 분석을 필요로 하는 데이터 수집 하는 합니다. 종종이 데이터 수집 인터넷 액세스 없이 발생합니다. 연결을 설정할 때 추가 분석 하기 위해 데이터의 리소스를 많이 사용 분석을 수행 해야 합니다. 여전히 공용 클라우드의 너무 늦거나 사용할 수 없는 경우에 데이터를 분석할 수 있습니다.

이 자습서에서는 샘플 환경에 구축:

> [!div class="checklist"]
> - 원시 데이터 저장소 blob을 만듭니다.
> - Azure Stack에서 데이터 정리를 Azure로 이동 하는 새 Azure Stack 함수를 만듭니다.
> - Blob storage 트리거 함수를 만듭니다.
> - Blob 및 큐를 포함 하는 Azure Stack 저장소 계정을 만듭니다.
> - 큐 트리거 함수를 만듭니다.
> - 테스트는 큐 트리거 함수입니다.

> [!Tip]  
> ![하이브리드 pillars.png](./media/azure-stack-solution-cloud-burst/hybrid-pillars.png)  
> Microsoft Azure Stack은 Azure의 확장입니다. Azure Stack은 유연성 및 혁신을 온-프레미스 환경에 컴퓨팅 및 하이브리드 앱을 어디서 나 빌드 및 배포 할 수 있는 유일한 하이브리드 클라우드를 사용 하도록 설정 하는 클라우드를 제공 합니다.  
> 
> 백서 [하이브리드 응용 프로그램에 대 한 디자인 고려 사항](https://aka.ms/hybrid-cloud-applications-pillars) (배치, 확장성, 가용성, 복원 력, 관리 효율성 및 보안) 소프트웨어 품질 핵심 요소를 디자인, 배포 및 운영에 대 한 검토 하이브리드 응용 프로그램입니다. 디자인 고려 사항을 프로덕션 환경에서 문제를 최소화 되는 하이브리드 응용 프로그램 디자인을 최적화 하는 데 도움이 됩니다.

## <a name="prerequisites"></a>필수 조건

이 솔루션을 빌드하려면 몇 가지 준비 해야 합니다.

-   설치 및 작동 Azure Stack (자세한 내용은 여기에서 찾을 수 있습니다. [Azure Stack 개요](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-storage-overview))

-   Azure 구독. (만듭니다는 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F))

-   [Microsoft Azure Storage 탐색기](https://storageexplorer.com/)를 다운로드하고 설치합니다.

-   사용자 고유의 데이터 함수에서 처리를 제공 해야 합니다. 데이터 생성 및 Azure Stack 저장소 blob 컨테이너에 업로드할 수 있어야 합니다.

## <a name="issues-and-considerations"></a>문제 및 고려 사항

### <a name="scalability-considerations"></a>확장성 고려 사항

Azure functions 및 저장소 솔루션에는 데이터 볼륨 및 처리 요구 사항을 충족 하도록 확장 합니다. Azure 확장성 정보 및 대상에 대 한 참조 [Azure 확장성 설명서](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets)합니다.

### <a name="availability-considerations"></a>가용성 고려 사항

저장소는이 패턴에 대 한 주 가용성 고려 사항입니다. 빠른 링크를 통해 연결이 큰 데이터 볼륨 처리 및 배포에 필요 합니다.

### <a name="manageability-considerations"></a>관리 효율성 고려 사항

어떻게 개발 도구 및 소스 제어 수 있도록 솔루션을 관리 하는 것이 좋습니다.

## <a name="create-the-raw-data-storage-blob"></a>원시 데이터 저장소 blob 만들기

저장소 계정과 blob 컨테이너는 컴퓨터 및 직원 작업, 시설 데이터, 프로덕션 메트릭 및 다른 보고를 포함 하 여 온-프레미스 작업에 의해 생성 된 모든 원본 데이터를 보관 합니다.

1.  에 로그인 합니다 [ *Azure Stack 포털*](https://portal.local.azurestack.external/)합니다.

2.  Azure Stack 포털에 서비스의 메뉴를 열고 왼쪽의 메뉴를 확장 **모든 서비스**합니다. 아래로 스크롤하여 **저장소** 선택한 **저장소 계정**합니다. 저장소 계정 창에서 선택 **추가**합니다.

3.  계정에 대해 다음 정보를 사용 합니다.

    a.  이름: **사용자 선택**

    b.  배포 모델: **리소스 관리자**

    다.  계정 종류: **저장소 (범용 V1)**

    d.  위치: **미국 서부**

    e.  복제: **LRS(로컬 중복 저장소)**

    f.  성능: **Standard**

    g.  보안 전송 필요 합니다. **사용 안 함**

    h.  구독: 하나를 선택

    i.  리소스 그룹: 새 리소스 그룹을 지정 하거나 기존 리소스 그룹을 선택 합니다.

    j.  가상 네트워크를 구성 합니다. **사용 안 함**

4.  선택 **storage 계정을 만들려면 만들기**합니다.

    ![대체 텍스트](media/azure-stack-solution-staged-data-analytics/image1.png)

5.  를 만든 후에 저장소 계정의 이름을 선택 합니다.

6.  계정 블레이드에서 아래 합니다 **BLOB SERVICE** 을 선택 **컨테이너**합니다.

7.  블레이드의 맨 위에 있는 선택 **+ 컨테이너.** 선택한 **컨테이너**합니다.

    ![대체 텍스트](media/azure-stack-solution-staged-data-analytics/image2.png)

8.  이름: **사용자가 선택한**

9.  공용 액세스 수준: **컨테이너** (컨테이너 및 blob에 대 한 익명 읽기 액세스)

10.  **확인**을 선택합니다.

## <a name="create-an-azure-stack-function"></a>Azure Stack 함수 만들기

Azure Stack에서 데이터 정리를 Azure로 이동 하는 새 Azure Stack 함수를 만듭니다.

### <a name="create-the-azure-stack-function-app"></a>Azure Stack 함수 앱 만들기

1. 에 로그인 합니다 [Azure Stack 포털](https://portal.local.azurestack.external)합니다.
2. **모든 서비스**를 선택합니다.
3. 선택 **함수 앱** 에 **웹 + 모바일** 그룹입니다.

4.  이미지 아래의 표에 지정 된 설정을 사용 하 여 함수 앱을 만듭니다.

    | 설정 | 제안 값 | 설명 |
    | ---- | ---- | ---- |
    | 앱 이름 | 전역적으로 고유한 이름 | 새 함수 앱을 식별하는 이름입니다. 유효한 문자는 `a` - `z`하십시오 `0``-9`, 및 `-`합니다. |
    | 구독 | 사용자의 구독 | 이 새 함수 앱이 만들어질 구독입니다. |
    | **리소스 그룹** |  |  |
    | myResourceGroup | 함수 앱을 만들 새 리소스 그룹의 이름입니다. |  |
    | OS | Windows | 서버 없는 호스팅은 현재 Windows에서 실행될 때만 가능합니다. |
    | **호스팅 계획** |  |  |
    | 소비 계획 | 함수 앱에 리소스가 할당되는 방법을 정의하는 호스팅 계획입니다. 소비 계획 기본적으로 리소스 함수를 통해 필요에 따라 동적으로 추가 됩니다. 이 서버 리스 호스팅에 대 한 요금만 함수 실행 시간입니다. |  |
    | 위치 | 가장 가까운 지역 | 함수 액세스 또는 다른 서비스에 가까운 영역을 선택 합니다. |
    | **Storage 계정** |  |  |
    | \<위에서 만든 저장소 계정 > | 함수 앱에 사용된 새 저장소 계정의 이름. 저장소 계정 이름은 길이가 3 ~ 24 자 사이 여야 합니다. 이름은 숫자와 소문자만 사용할 수 있습니다. 기존 계정을 사용할 수도 있습니다. |  |

    **예제:**

    ![새 함수 앱 설정 정의](media/azure-stack-solution-staged-data-analytics/image6.png)

5.  **만들기**를 선택하여 함수 앱을 프로비전하고 배포합니다.

6.  포털의 오른쪽 위 모서리에 있는 [알림] 아이콘을 선택하고 **배포 성공** 메시지가 표시될 때까지 기다립니다.

    ![새 함수 앱 설정 정의](media/azure-stack-solution-staged-data-analytics/image7.png)

7.  선택 **리소스로 이동** 새 함수 앱을 볼 수 있습니다.

![함수 앱을 성공적으로 만들었습니다.](media/azure-stack-solution-staged-data-analytics/image8.png)

### <a name="add-a-function-to-the-azure-stack-function-app"></a>Azure Stack 함수 앱에 함수 추가

1.  클릭 하 여 새 함수를 만들 **함수**, 해당 **+ 새 함수** 단추입니다.

    ![대체 텍스트](media/azure-stack-solution-staged-data-analytics/image3.png)

2.  선택 **타이머 트리거**합니다.

    ![대체 텍스트](media/azure-stack-solution-staged-data-analytics/image4.png)

3.  선택 **C\#**  언어 및 함수 이름: `upload-to-azure`  일정 설정 `0 0 * * * *`는 CRON에서 표기법은 한 번 한 시간입니다.

    ![대체 텍스트](media/azure-stack-solution-staged-data-analytics/image5.png)

## <a name="create-a-blob-storage-triggered-function"></a>Blob Storage 트리거 함수 만들기

1.  함수 앱을 확장 하 고 선택 합니다 **+** 옆에 단추 **함수**합니다.

2.  검색 필드에 입력 `blob` 한 다음에 대 한 원하는 언어를 선택 합니다 **Blob 트리거** 템플릿.

  ![Blob Storage 트리거 템플릿을 선택합니다.](media/azure-stack-solution-staged-data-analytics/image10.png)

3.  아래 표에 지정 된 대로 설정을 사용 합니다.

    | 설정 | 제안 값 | 설명 |
    | ------- | ------- | ------- |
    | 이름 | 함수 앱에서 고유 | 이 Blob 트리거 함수의 이름입니다. |
    | path | \<위의 저장소 위치의 경로 > | 모니터링되는 Blob Storage의 위치입니다. Blob의 파일 이름 바인딩의 이름 매개 변수로 전달 됩니다. |
    | 스토리지 계정 연결 | 함수 앱 연결 | 함수 앱에서 이미 사용 중인 저장소 계정 연결을 사용 하거나 새로 만들 수 있습니다. |

    **예제:**

    ![Blob Storage 트리거 함수 만들기.](media/azure-stack-solution-staged-data-analytics/image11.png)

4.  **만들기**를 선택하여 함수를 만듭니다.

### <a name="test-the-function"></a>함수 테스트

1.  Azure portal에서 함수를 찾습니다. 확장을 **로그** 페이지의 맨 아래에 있는 로그 스트리밍이 일시 중지 되지 않았는지 확인 합니다.

2.  Storage 탐색기를 열고이 섹션의 앞부분에서 만든 저장소 계정에 연결 합니다.

3.  저장소 계정을 확장 **Blob 컨테이너**, blob 이전에 만든 및 합니다. 선택 **업로드할** 차례로 **파일을 업로드**합니다.

    ![Blob 컨테이너에 파일 업로드.](media/azure-stack-solution-staged-data-analytics/image12.png)

4.  업로드 파일 대화 상자에서 파일 필드를 선택 합니다. 이미지 파일과 같은 로컬 컴퓨터에는 파일을 찾아 선택 하 고 선택 **엽니다** 차례로 **업로드**합니다.

5.  함수 로그로 돌아가서 blob를 읽었는지 확인 합니다.

    **예제:**

    ![로그에서 메시지 보기.](media/azure-stack-solution-staged-data-analytics/image13.png)

## <a name="create-an-azure-stack-storage-account"></a>Azure Stack 저장소 계정 만들기

Blob 및 큐를 포함 하는 Azure Stack 저장소 계정을 만듭니다.

### <a name="storage-blob--data-archiving"></a>저장소 Blob 데이터 보관

이 저장소 계정은 두 개의 컨테이너 보관 됩니다. 이러한 컨테이너는 보관 데이터를 보관 하는 데 사용 되는 하나의 blob 및 큐 본사 배포에 할당 하는 데이터의 처리에 사용 됩니다.

단계 및 보관 저장소와 다른 저장소 계정 및 blob 컨테이너를 만들려면 위에 설명 된 설정을 사용 합니다.

### <a name="storage-queue--filtered-data-holding"></a>저장소 큐 필터링 된 데이터 보관

1.  새 저장소 계정에 액세스 하려면 위에 설명 된 설정 및 단계를 사용 합니다.

2.  저장소 계정 개요 섹션에서 선택 **큐입니다.**

3.  선택 합니다 **+ 큐** 및 채우기 **이름** 필드 및 새 큐의 이름 입력 합니다.

4.  선택 **확인 합니다.**

    ![대체 텍스트](media/azure-stack-solution-staged-data-analytics/image14.png)

    ![대체 텍스트](media/azure-stack-solution-staged-data-analytics/image15.png)

## <a name="create-a-queue-triggered-function"></a>큐 트리거 함수 만들기

1.  Blob 트리거 대신 큐 트리거를 사용 하 여 추가 Azure Stack 함수를 만들려면 위의 함수 만들기 섹션의 단계를 사용 합니다.

2.  아래 표에 지정 된 대로 설정을 사용 합니다.

    | 설정 | 제안 값 | 설명 |
    | ------- | ------- | ------- |
    | 이름 | 함수 앱에서 고유 | 큐 트리거 함수의 이름입니다. |
    | path | \<위의 저장소 위치의 경로 > | 모니터링 되는 저장소 위치입니다. 큐의 파일 이름 바인딩의 이름 매개 변수로 전달 됩니다. |
    | 스토리지 계정 연결 | 함수 앱 연결 | 함수 앱에서 이미 사용 중인 저장소 계정 연결을 사용 하거나 새로 만들 수 있습니다. |

3.  **만들기**를 선택하여 함수를 만듭니다.

## <a name="test-the-queue-triggered-function"></a>테스트는 큐 트리거 함수

1.  Azure Stack 포털에서 함수로 이동 합니다. 확장을 **로그** 페이지의 맨 아래에 있는 로그 스트리밍이 일시 중지 되지 않았는지 확인 합니다.

2.  Storage 탐색기를 열고이 섹션의 앞부분에서 만든 저장소 계정에 연결 합니다.

3.  저장소 계정을 확장 **Blob 컨테이너**, blob 이전에 만든 및 합니다. 선택 **업로드할** 차례로 **파일을 업로드 합니다.**

    ![Blob 컨테이너에 파일 업로드.](media/azure-stack-solution-staged-data-analytics/image12.png)

4.  업로드 파일 대화 상자에서 파일 필드를 선택 합니다. 이미지 파일과 같은 로컬 컴퓨터에는 파일을 찾아 선택 하 고 선택 **엽니다** 차례로 **업로드**합니다.

5.  함수 로그로 돌아가서 blob를 읽었는지 확인 합니다.

  **예제:**

    ![로그에서 메시지 보기.](media/azure-stack-solution-staged-data-analytics/image13.png)

## <a name="securely-stored-and-accessed-compliant-data"></a>준수 데이터를 안전 하 게 저장 및 액세스

글로벌 엔터프라이즈 데이터는 안전 하 게 저장, 처리, 배포 및 Azure 및 Azure Stack 준비 된 데이터 분석 및 사용자 지정 끝점 지시문을 통해 액세스 합니다. 원격 사무실 직원과 기계 활동, 시설 데이터 및 비즈니스 메트릭을 지속적으로 컴파일된, 저장, 규정 준수에 대 한 테스트 이며 배포 회사 정책 및 지역 규정에 따라 합니다.

## <a name="next-steps"></a>다음 단계

- Azure 클라우드 패턴에 대 한 자세한 내용은 참조 하세요 [클라우드 디자인 패턴](https://docs.microsoft.com/azure/architecture/patterns)합니다.