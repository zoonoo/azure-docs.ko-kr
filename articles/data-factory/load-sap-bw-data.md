---
title: SAP 비즈니스 웨어하우스의 로드 데이터
description: Azure 데이터 팩터리를 사용하여 BW(SAP 비즈니스 웨어하우스)의 데이터를 복사합니다.
services: data-factory
author: linda33wj
ms.author: jingwang
manager: shwang
ms.reviewer: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/22/2019
ms.openlocfilehash: 971871c28bd1b38b134c04b0334fbe99d1d655c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75440191"
---
# <a name="copy-data-from-sap-business-warehouse-by-using-azure-data-factory"></a>Azure 데이터 팩터리를 사용하여 SAP 비즈니스 웨어하우스에서 데이터 복사

이 문서에서는 Azure 데이터 팩터리를 사용하여 BW(SAP 비즈니스 웨어하우스)에서 개방형 허브를 통해 Azure Data Lake Gen2로 데이터를 복사하는 방법을 보여 주며 있습니다. 유사한 프로세스를 사용하여 지원되는 다른 [싱크 데이터 저장소에](copy-activity-overview.md#supported-data-stores-and-formats)데이터를 복사할 수 있습니다.

> [!TIP]
> SAP BW 오픈 허브 통합 및 델타 추출 흐름을 포함하여 SAP BW에서 데이터를 복사하는 것에 대한 일반적인 내용은 [Azure 데이터 팩터리를 사용하여 Open Hub를 통해 SAP 비즈니스 웨어하우스의 데이터 복사를](connector-sap-business-warehouse-open-hub.md)참조하십시오.

## <a name="prerequisites"></a>사전 요구 사항

- **Azure 데이터 팩터리**: 데이터가 없는 경우 단계에 따라 [데이터 팩터리를 만듭니다.](quickstart-create-data-factory-portal.md#create-a-data-factory)

- **대상 유형 "데이터베이스 테이블"이 있는 SAP BW 오픈 허브 대상(OHD)**: OHD를 만들거나 OHD가 데이터 팩터리 통합에 대해 올바르게 구성되어 있는지 확인하려면 이 문서의 [SAP BW 오픈 허브 대상 구성](#sap-bw-open-hub-destination-configurations) 섹션을 참조하십시오.

- **SAP BW 사용자에게는 다음과 같은 권한이 필요합니다.**

  - 원격 함수 호출(RFC) 및 SAP BW에 대한 권한 부여.
  - **S_SDSAUTH** 권한 부여 개체의 "실행" 활동에 대한 사용 권한입니다.

- **SAP .NET 커넥터 3.0을 가진 [자체 호스팅 통합 런타임(IR)](concepts-integration-runtime.md#self-hosted-integration-runtime) ** 다음 설정 단계를 따르십시오.

  1. 자체 호스팅 통합 런타임 버전 3.13 이상설치 및 등록합니다. 이 프로세스는 이 문서의 후반부에서 설명합니다.

  2. SAP 웹 사이트에서 [Microsoft .NET 3.0용 64비트 SAP 커넥터를](https://support.sap.com/en/product/connectors/msnet.html) 다운로드하고 자체 호스팅 IR과 동일한 컴퓨터에 설치합니다. 설치 하는 동안 다음 이미지와 같이 선택적 설치 **단계** 대화 상자에서 **GAC에 어셈블리 설치를** 선택 해야 합니다.

     ![SAP .NET 커넥터 대화 상자 설정](media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

## <a name="do-a-full-copy-from-sap-bw-open-hub"></a>SAP BW 오픈 허브에서 전체 복사본 수행

Azure Portal에서 데이터 팩터리로 이동합니다. **모니터 작성을** 선택하여 & 별도의 탭에서 데이터 팩터리 UI를 엽니다.

1. **시작하자** 페이지에서 데이터 복사 도구를 열려면 **데이터 복사를** 선택합니다.

2. **속성** 페이지에서 **작업 이름을**지정한 다음 **다음을 선택합니다.**

3. 원본 **데이터 저장소** 페이지에서 **+새 연결 만들기를 선택합니다.** 커넥터 갤러리에서 **SAP BW 열기 허브를** 선택한 다음 **계속을**선택합니다. 커넥터를 필터링하려면 검색 상자에 **SAP를** 입력할 수 있습니다.

4. **SAP BW 열기 허브 연결 지정** 페이지에서 다음 단계를 수행하여 새 연결을 만듭니다.

   ![SAP BW 오픈 허브 연결 서비스 페이지 만들기](media/load-sap-bw-data/create-sap-bw-open-hub-linked-service.png)

   1. 통합 **런타임** 목록을 통해 Connect에서 기존 자체 호스팅 IR을 선택합니다. 또는 아직 없는 경우 만들도록 선택합니다.

      새 자체 호스팅 IR을 만들려면 **+New**를 선택한 다음 **자체 호스팅을**선택합니다. **이름을**입력한 다음 **다음을**선택합니다. 현재 컴퓨터에 설치할 **익스프레스 설정을** 선택하거나 제공된 **수동 설정** 단계를 따릅니다.

      [필수 구성 에서](#prerequisites)설명한 대로 자체 호스팅 IR이 실행 중인 동일한 컴퓨터에 Microsoft .NET 3.0용 SAP 커넥터가 설치되어 있는지 확인합니다.

   2. SAP BW **서버 이름,** **시스템 번호,** **클라이언트 ID,** **언어(EN**이외 인 경우), **사용자 이름**및 **암호를**입력합니다. **Language**

   3. **설정의** 유효성을 검사하려면 테스트 연결을 선택한 다음 **완료를**선택합니다.

   4. 새 연결이 만들어집니다. **다음**을 선택합니다.

5. 오픈 **허브 목적지 선택** 페이지에서 SAP BW에서 사용할 수 있는 오픈 허브 목적지를 찾아봅슬이렇게 합니다. OHD를 선택하여 데이터를 복사한 다음 **다음을**선택합니다.

   ![SAP BW 오픈 허브 대상 테이블 선택](media/load-sap-bw-data/select-sap-bw-open-hub-table.png)

6. 필터가 필요한 경우 필터를 지정합니다. OHD에 단일 요청 ID가 있는 단일 데이터 전송 프로세스(DTP) 실행의 데이터만 포함되어 있거나 DTP가 완료되어 데이터를 복사하려는 경우 **마지막 요청 제외** 확인란을 선택 취소합니다.

   이 문서의 [SAP BW 오픈 허브 대상 구성](#sap-bw-open-hub-destination-configurations) 섹션에서 이러한 설정에 대해 자세히 알아봅니다. 반환할 데이터를 다시 확인하려면 **유효성 검사를** 선택합니다. 그런 다음 을 **선택합니다.**

   ![SAP BW 오픈 허브 필터 구성](media/load-sap-bw-data/configure-sap-bw-open-hub-filter.png)

7. 대상 **데이터 저장소** 페이지에서 **+새 연결** > Azure**Data Lake 저장소 Gen2** > **계속을**선택합니다.

8. Azure **Data Lake 저장소 연결 지정** 페이지에서 다음 단계를 수행하여 연결을 만듭니다.

   ![ADLS Gen2 링크된 서비스 페이지 만들기](media/load-sap-bw-data/create-adls-gen2-linked-service.png)

   1. **이름** 드롭다운 목록에서 데이터 레이크 스토리지 Gen2 지원 계정을 선택합니다.
   2. **마침**을 선택하여 연결을 만듭니다. 그런 다음 을 **선택합니다.**

9. 출력 **파일 또는 폴더 선택** 페이지에서 **copyfromopenhub를** 출력 폴더 이름으로 입력합니다. 그런 다음 을 **선택합니다.**

   ![출력 폴더 페이지 선택](media/load-sap-bw-data/choose-output-folder.png)

10. 파일 **형식 설정** 페이지에서 **다음을** 선택하여 기본 설정을 사용합니다.

    ![싱크 형식 페이지 지정](media/load-sap-bw-data/specify-sink-format.png)

11. **설정** 페이지에서 성능 **설정을**확장합니다. SAP BW에서 병렬로 로드할 5와 같은 **복사 병렬 처리 정도** 값을 입력합니다. 그런 다음 을 **선택합니다.**

    ![복사 설정 구성](media/load-sap-bw-data/configure-copy-settings.png)

12. **요약** 페이지에서 설정을 검토합니다. 그런 다음 을 **선택합니다.**

13. **배포** 페이지에서 파이프라인을 모니터링하려면 **모니터를** 선택합니다.

    ![배포 페이지](media/load-sap-bw-data/deployment.png)

14. 페이지 왼쪽에 있는 **모니터** 탭이 자동으로 선택됩니다. **작업** 열에는 활동 실행 세부 정보를 보고 파이프라인을 다시 실행하는 링크가 포함되어 있습니다.

    ![파이프라인 모니터링 보기](media/load-sap-bw-data/pipeline-monitoring.png)

15. 파이프라인 실행과 연결된 활동 실행을 보려면 **작업** 열에서 **활동 실행 보기를** 선택합니다. 파이프라인에는 하나의 작업(복사 작업)만 있으므로 하나의 항목만 표시됩니다. 파이프라인 실행 보기로 다시 전환하려면 맨 위에 있는 파이프라인 링크를 **선택합니다.** **새로 고침**을 선택하여 목록을 새로 고칩니다.

    ![활동 모니터링 화면](media/load-sap-bw-data/activity-monitoring.png)

16. 각 복사 활동에 대한 실행 세부 정보를 모니터링하려면 활동 모니터링 보기에서 **작업** 아래의 안경 아이콘인 **세부 정보** 링크를 선택합니다. 사용 가능한 세부 정보에는 원본에서 싱크로 복사된 데이터 볼륨, 데이터 처리량, 실행 단계 및 기간 및 사용된 구성이 포함됩니다.

    ![활동 모니터링 세부 정보](media/load-sap-bw-data/activity-monitoring-details.png)

17. **최대 요청 ID를**보려면 활동 모니터링 보기로 돌아가서 **작업**에서 **출력을** 선택합니다.

    ![활동 출력 화면](media/load-sap-bw-data/activity-output.png)

    ![활동 출력 세부 정보 보기](media/load-sap-bw-data/activity-output-details.png)

## <a name="incremental-copy-from-sap-bw-open-hub"></a>SAP BW 오픈 허브의 증분 복사본

> [!TIP]
> [SAP BW 오픈 허브 커넥터 델타 추출 흐름을](connector-sap-business-warehouse-open-hub.md#delta-extraction-flow) 참조하여 데이터 팩터리의 SAP BW 오픈 허브 커넥터가 SAP BW에서 증분 데이터를 복사하는 방법을 알아봅니다. 이 문서에서는 기본 커넥터 구성을 이해하는 데도 도움이 될 수 있습니다.

이제 SAP BW 오픈 허브에서 증분 복사본을 계속 구성해 보겠습니다.

증분 복사본은 **요청 ID를**기반으로 하는 "하이 워터마크" 메커니즘을 사용합니다. 해당 ID는 DTP에 의해 SAP BW 오픈 허브 대상에서 자동으로 생성됩니다. 다음 다이어그램은 이 워크플로를 보여 주며 다음과 같은 작업을 보여 주며 다음과 같은 작업을 보여 주

![증분 복사 워크플로 흐름 차트](media/load-sap-bw-data/incremental-copy-workflow.png)

데이터 팩터리에서 시작 페이지를 **시작해** **템플릿에서 파이프라인 만들기를** 선택하여 기본 제공 템플릿을 사용합니다.

1. **SAP BW를** 검색하여 **SAP BW에서 Azure 데이터 레이크 저장소 Gen2 템플릿에 대한 증분 복사본을** 찾아 선택합니다. 이 템플릿은 Azure 데이터 레이크 저장소 Gen2에 데이터를 복사합니다. 유사한 워크플로를 사용하여 다른 싱크 유형에 복사할 수 있습니다.

2. 템플릿의 기본 페이지에서 다음 세 개의 연결을 선택하거나 만든 다음 창의 오른쪽 아래 모서리에서 **이 템플릿 사용을** 선택합니다.

   - **Azure Blob 저장소**: 이 연습에서는 Azure Blob 저장소를 사용하여 최대 *복사된 요청 ID인*높은 워터마크를 저장합니다.
   - **SAP BW 오픈 허브**: 이것은 에서 데이터를 복사하는 소스입니다. 자세한 구성은 이전 전체 복사 연습을 참조하십시오.
   - **Azure 데이터 레이크 저장소 Gen2**: 데이터를 복사하는 싱크입니다. 자세한 구성은 이전 전체 복사 연습을 참조하십시오.

   ![SAP BW 템플릿의 증분 복사본](media/load-sap-bw-data/incremental-copy-from-sap-bw-template.png)

3. 이 템플릿은 다음과 같은 세 가지 활동으로 파이프라인을 생성하고 *조회,* *데이터 복사*및 *웹*의 성공을 연결합니다.

   파이프라인 매개 변수 탭으로 **이동합니다.** 제공해야 하는 모든 구성이 표시됩니다.

   ![SAP BW 구성의 증분 복사본](media/load-sap-bw-data/incremental-copy-from-sap-bw-pipeline-config.png)

   - **SAPOpenHubDestinationName**: 데이터를 복사할 열린 허브 테이블 이름을 지정합니다.

   - **Data_Destination_Container**: 데이터를 복사할 대상 Azure Data Lake Storage Gen2 컨테이너를 지정합니다. 컨테이너가 없는 경우 데이터 팩터리 복사 활동은 실행 중에 컨테이너를 만듭니다.
  
   - **Data_Destination_Directory**: Azure Data Lake Storage Gen2 컨테이너 아래에 데이터를 복사할 폴더 경로를 지정합니다. 경로가 없는 경우 데이터 팩터리 복사 활동은 실행 중에 경로를 만듭니다.
  
   - **하이워터마크BlobContainer**: 하이 워터마크 값을 저장할 컨테이너를 지정합니다.

   - **HighWatermarkBlobDirectory**: 높은 워터마크 값을 저장할 컨테이너 아래에 폴더 경로를 지정합니다.

   - **HighWatermarkBlobName**: 높은 워터마크 값을 저장할 Blob 이름을 `requestIdCache.txt`지정합니다. Blob 저장소에서 *컨테이너/경로/requestIdCache.txt와*같은 HighWatermarkBlobContainer+HighWatermarkBlobDirectory+HighWatermarkBlobName의 해당 경로로 이동합니다. 콘텐츠 0이 있는 Blob을 만듭니다.

      ![BLOB 콘텐츠](media/load-sap-bw-data/blob.png)

   - **LogicAppURL**: 이 템플릿에서는 WebActivity를 사용하여 Azure Logic Apps를 호출하여 Blob 저장소에서 하이 워터마크 값을 설정합니다. 또는 Azure SQL Database를 사용하여 저장할 수 있습니다. 저장 프로시저 활동을 사용하여 값을 업데이트합니다.

      다음 이미지와 같이 먼저 논리 앱을 만들어야 합니다. 그런 다음 HTTP **POST URL에**붙여 넣습니다.

      ![로직 앱 구성](media/load-sap-bw-data/logic-app-config.png)

      1. Azure Portal로 이동합니다. 새 논리 앱 서비스를 **선택합니다.** **+빈 논리 앱을** 선택하여 **논리 앱 디자이너로**이동합니다.

      2. **HTTP 요청이 수신될 때의 트리거를**만듭니다. 다음과 같이 HTTP 요청 본문을 지정합니다.

         ```json
         {
            "properties": {
               "sapOpenHubMaxRequestId": {
                  "type": "string"
               }
            },
            "type": "object"
         }
         ```

      3. Blob 만들기 작업을 **추가합니다.** **폴더 경로** 및 **Blob 이름의**경우 *HighWatermarkBlobContainer+HighWatermarkBlobDirectory* 및 *HighWatermarkBlobName에서*이전에 구성한 값과 동일한 값을 사용합니다.

      4. **저장**을 선택합니다. 그런 다음 HTTP **POST URL** 값을 복사하여 데이터 팩터리 파이프라인에 사용합니다.

4. 데이터 팩터리 파이프라인 매개 변수를 제공한 후 **디버그** > **완료를** 선택하여 실행을 호출하여 구성의 유효성을 검사합니다. 또는 **게시를** 선택하여 모든 변경 내용을 게시한 다음 **트리거 추가를** 선택하여 실행을 실행합니다.

## <a name="sap-bw-open-hub-destination-configurations"></a>SAP BW 오픈 허브 대상 구성

이 섹션에서는 데이터 팩터리에서 SAP BW 열기 허브 커넥터를 사용하여 데이터를 복사하는 SAP BW 측의 구성을 소개합니다.

### <a name="configure-delta-extraction-in-sap-bw"></a>SAP BW에서 델타 추출 구성

기록 복사본과 증분 복사본 또는 증분 복사본만 필요한 경우 SAP BW에서 델타 추출을 구성합니다.

1. 오픈 허브 대상을 만듭니다. 필요한 변환 및 데이터 전송 프로세스를 자동으로 만드는 SAP 트랜잭션 RSA1에서 OHD를 만들 수 있습니다. 다음 설정을 사용합니다.

   - **개체 유형**: 모든 개체 유형을 사용할 수 있습니다. 여기서는 **인포큐브를** 예로 들 수 있습니다.
   - **대상 유형**: **데이터베이스 테이블을**선택합니다.
   - **테이블 키**: **기술 키를 선택합니다.**
   - **추출**: 데이터 유지를 선택하고 **테이블에 레코드삽입을 선택합니다.**

   ![SAP BW OHD 델타 추출 대화 상자 만들기](media/load-sap-bw-data/create-sap-bw-ohd-delta.png)

   ![SAP BW OHD delta2 추출 대화 상자 만들기](media/load-sap-bw-data/create-sap-bw-ohd-delta2.png)

   DTP에 대한 병렬 실행 SAP 작업 프로세스 수를 늘릴 수 있습니다.

   ![만들기-수액-bw-ohd-delta3](media/load-sap-bw-data/create-sap-bw-ohd-delta3.png)

2. 프로세스 체인에서 DTP를 예약합니다.

   큐브의 델타 DTP는 필요한 행이 압축되지 않은 경우에만 작동합니다. 열린 허브 테이블에 DTP 전에 BW 큐브 압축이 실행되지 않았는지 확인합니다. 이를 위한 가장 쉬운 방법은 DTP를 기존 프로세스 체인에 통합하는 것입니다. 다음 예제에서, DTP(OHD)는 *adjust(집계* 롤업)와 *붕괴(큐브* 압축) 단계 사이의 프로세스 체인에 삽입된다.

   ![SAP BW 공정 체인 플로우 차트 생성](media/load-sap-bw-data/create-sap-bw-process-chain.png)

### <a name="configure-full-extraction-in-sap-bw"></a>SAP BW에서 전체 추출 구성

델타 추출 외에도 동일한 SAP BW 정보 공급자의 전체 추출을 원할 수 있습니다. 이는 일반적으로 전체 복사본을 수행하지만 증분하지 않으려는 경우 적용되거나 [델타 추출을 다시 동기화하려는](#resync-delta-extraction)경우에 적용됩니다.

동일한 OHD에 대해 두 개 이상의 DTP를 가질 수 없습니다. 따라서 델타 추출 전에 OHD를 추가로 만들어야 합니다.

![SAP BW OHD 전체 만들기](media/load-sap-bw-data/create-sap-bw-ohd-full.png)

전체 하중 OHD의 경우 델타 추출과 다른 옵션을 선택합니다.

- OHD: **추출** 옵션을 설정하여 **데이터 삭제 및 레코드 삽입.** 그렇지 않으면 BW 프로세스 체인에서 DTP를 반복할 때 데이터가 여러 번 추출됩니다.

- DTP: 추출 **모드를** **전체로**설정합니다. 이 이미지에서 볼 수 있듯이 OHD를 만든 직후 **델타에서** **전체로** 자동으로 생성된 DTP를 변경해야 합니다.

   !["전체" 추출용으로 구성된 SAP BW OHD 대화 상자 만들기](media/load-sap-bw-data/create-sap-bw-ohd-full2.png)

- 데이터 팩터리의 BW 오픈 허브 커넥터: **마지막 요청 제외를**끕니다. 그렇지 않으면 아무 것도 추출되지 않습니다.

일반적으로 전체 DTP를 수동으로 실행합니다. 또는 전체 DTP에 대한 프로세스 체인을 만들 수 있습니다. 일반적으로 기존 프로세스 체인과 독립적인 별도의 체인입니다. 두 경우 모두 *데이터 팩터리 복사본을 사용하여 추출을 시작하기 전에 DTP가 완료되었는지 확인합니다.* 그렇지 않으면 부분 데이터만 복사됩니다.

### <a name="run-delta-extraction-the-first-time"></a>델타 추출을 처음 실행

첫 번째 델타 추출은 기술적으로 *전체 추출입니다.* 기본적으로 SAP BW Open Hub 커넥터는 데이터를 복사할 때 마지막 요청을 제외합니다. 첫 번째 델타 추출의 경우 후속 DTP가 별도의 요청 ID를 사용하여 테이블에서 델타 데이터를 생성할 때까지 Data Factory 복사 활동에 의해 데이터가 추출되지 않습니다. 이 시나리오를 방지하는 방법에는 두 가지가 있습니다.

- 첫 번째 델타 추출에 대한 **마지막 요청 제외** 옵션을 끕니다. 델타 추출을 처음 시작하기 전에 첫 번째 델타 DTP가 완료되었는지 확인합니다.
-  다음 섹션에 설명된 대로 델타 추출을 다시 동기화하는 절차를 사용합니다.

### <a name="resync-delta-extraction"></a>재동기화 델타 추출

다음 시나리오는 SAP BW 큐브의 데이터를 변경하지만 델타 DTP에서는 고려하지 않습니다.

- SAP BW 선택적 삭제(필터 조건을 사용하여 행의)
- SAP BW 요청 삭제(잘못된 요청)

SAP 오픈 허브 대상은 데이터 마트가 제어하는 데이터 대상이 아닙니다(2015년 이후의 모든 SAP BW 지원 패키지에서). 따라서 OHD의 데이터를 변경하지 않고 큐브에서 데이터를 삭제할 수 있습니다. 그런 다음 큐브의 데이터를 데이터 팩터리와 다시 동기화해야 합니다.

1. 데이터 팩터리에서 전체 추출을 실행합니다(SAP에서 전체 DTP사용).
2. 델타 DTP에 대한 Open Hub 테이블의 모든 행을 삭제합니다.
3. 델타 DTP의 상태를 **가져오기로 설정합니다.**

이 후 모든 후속 델타 DTP 및 데이터 팩터리 델타 추출이 예상대로 작동합니다.

델타 DTP의 상태를 **가져오기로**설정하려면 다음 옵션을 사용하여 델타 DTP를 수동으로 실행할 수 있습니다.

    *No Data Transfer; Delta Status in Source: Fetched*

## <a name="next-steps"></a>다음 단계

SAP BW 오픈 허브 커넥터 지원에 대해 자세히 알아보십시오.

> [!div class="nextstepaction"]
>[SAP 비즈니스 웨어하우스 오픈 허브 커넥터](connector-sap-business-warehouse-open-hub.md)
