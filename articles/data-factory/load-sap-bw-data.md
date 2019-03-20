---
title: Azure Data Factory를 사용 하 여 SAP Business Warehouse에서 데이터 로드 | Microsoft Docs
description: Azure Data Factory를 사용 하 여 SAP Business Warehouse (BW)에서 데이터를 복사 하려면
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/19/2019
ms.author: jingwang
ms.openlocfilehash: 9458903378576a50db9be92b9377987829e1ba41
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58200160"
---
# <a name="load-data-from-sap-business-warehouse-bw-by-using-azure-data-factory"></a>Azure Data Factory를 사용 하 여 SAP Business Warehouse (BW)에서 데이터 로드

이 문서에서는 연습 데이터 팩터리를 사용 하는 방법은 _데이터 로드에서 SAP Business Warehouse (BW) 오픈 허브를 통해 Azure Data Lake 저장소 Gen2_합니다. 다른 데이터를 복사 하는 유사한 단계를 따르면 [지원 되는 싱크 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats)합니다. 

> [!TIP]
> 가리킵니다 [SAP BW 오픈 허브 커넥터 문서](connector-sap-business-warehouse-open-hub.md) 일반적 SAP BW에서 데이터를 복사를 포함 하 여 SAP BW Open Hub 통합 및 델타 추출 흐름 소개 합니다.

## <a name="prerequisites"></a>필수 조건

- **Azure Data Factory (ADF):** Data factory가 없는 경우에 따라는 "[데이터 팩터리 만들기](quickstart-create-data-factory-portal.md#create-a-data-factory)" 섹션을 만듭니다. 

- **SAP BW Open Hub 대상 (OHD) 대상 유형 "데이터베이스 테이블"으로 합니다.** 따릅니다 [SAP BW Open Hub Destination 구성](#sap-bw-open-hub-destination-configurations) 섹션 하나를 만들거나 기존 프로그램 OHD ADF를 사용 하 여 통합 되도록 제대로 구성 되었는지 확인 합니다.

- **SAP BW 되 해야 다음과 같은 권한이 있어야 합니다.**

  - RFC 및 SAP BW에 대해 부여된 권한
  - 권한을 "**실행할**"권한 부여 개체의 작업"**S_SDSAUTH**"입니다.

- **[자체 호스팅 Integration Runtime](concepts-integration-runtime.md#self-hosted-integration-runtime) 3.0는 필요한 SAP.NET 커넥터를 사용 하 여**입니다. 다음은 수행 해야 하는 자세한 준비 합니다.

  1. 설치 하 고 버전을 사용 하 여 자체 호스팅 IR을 등록 > = 3.13 (다음 연습에서 설명 됨). 

  2. 다운로드 합니다 [64 비트 SAP.NET Connector 3.0](https://support.sap.com/en/product/connectors/msnet.html) SAP의 웹 사이트에서 자체 호스팅 IR 컴퓨터에 설치 합니다.  경우 "선택적인 설정 단계" 창에서 설치를 선택 해야 합니다 "**GAC에 어셈블리 설치**" 다음 그림과에서 같이 옵션입니다.

     ![SAP.NET 커넥터 설정](media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

## <a name="full-copy-from-sap-bw-open-hub"></a>SAP BW Open Hub의 전체 복사본

Go를 데이터 팩터리에 Azure portal에서 선택-> **작성 및 모니터링** 를 별도 탭에서 ADF UI를 시작 합니다. 

1. **시작** 페이지에서 **데이터 복사**를 선택하여 데이터 복사 도구를 시작합니다. 

2. 에 **속성** 페이지에서 이름을 지정 합니다 **작업 이름** 필드를 선택한 **다음**합니다.

3. 에 **원본 데이터 저장소** 페이지에서 클릭 **+ 새 연결 만들기** 선택-> **SAP BW 오픈 허브** 커넥터 갤러리에서 선택-> **계속**. 커넥터를 필터링 하려면 검색 상자에 "SAP"를 입력할 수 있습니다.

4. 에 **SAP BW Open Hub 지정 연결** 페이지 

   ![SAP BW 오픈 허브 연결 된 서비스 만들기](media/load-sap-bw-data/create-sap-bw-open-hub-linked-service.png)

   1. 선택 된 **통합 런타임을 통해 연결**: 기존 자체 호스팅 IR을 선택 하려면 드롭다운 목록을 클릭 하거나 아직 설정 하는 자체 호스팅 IR이 없는 경우 하나 만듭니다. 

      새로 만들기를 클릭 **+ 새로 만들기** 드롭다운 목록에서 유형 선택-> **자체 호스팅** -> 지정을 **이름** 를 클릭 하 고 **다음** -> 선택 **빠른 설치** 현재 컴퓨터에 설치를 수행 하는 **수동 설치** 단계 있습니다.

      설명 했 듯이 [필수 구성 요소](#prerequisites), 권한이 있는지도 확인 합니다 **SAP.NET connector 3.0** 자체 호스팅 IR 실행 되 고 있는 동일한 컴퓨터에 설치 합니다.

   2. SAP BW 지정할 **서버 이름**, **시스템 번호**, **클라이언트 ID** **언어** (EN 이외의 경우), **사용자이름**, 및 **암호**합니다.

   3. **연결 테스트**를 클릭하여 설정의 유효성을 검사한 다음, **마침**을 선택합니다.

   4. 새 연결이 생성되었다고 표시됩니다. **다음**을 선택합니다.

5. 에 **선택 오픈 허브 대상을** 페이지에 SAP BW에서 사용할 수 있는 오픈 허브 대상을 이동 하 고 데이터를 복사한 다음 클릭 하려는 것 **다음**합니다.

   ![SAP BW Open Hub 테이블 선택](media/load-sap-bw-data/select-sap-bw-open-hub-table.png)

6. 필요한 경우 필터를 지정 합니다. Open Hub Destination에만 단일 요청 ID 사용 하 여 단일 데이터 전송 프로세스 (DTP) 실행에서 데이터를 포함 하거나에 DTP 완료 확실 하 고 모든 데이터를 복사 하려면 선택 취소 하는 경우는 **마지막 요청 제외**합니다. 더에서 이러한 설정을 어떻게 관련 SAP BW의 구성에 알아볼 수 있습니다 [SAP BW Open Hub Destination 구성](#sap-bw-open-hub-destination-configurations) 섹션입니다. 클릭 **유효성 검사** double 데이터를 확인 하려면 반환 선택 **다음**합니다.

   ![SAP BW Open Hub 필터 구성](media/load-sap-bw-data/configure-sap-bw-open-hub-filter.png)

7. 에 **대상 데이터 저장소** 페이지에서 **+ 새 연결 만들기**를 선택한 후 **Azure Data Lake 저장소 Gen2**, 선택한 **계속**.

8. 에 **Azure Data Lake Storage 지정 연결** 페이지 

   ![ADLS Gen2 연결 된 서비스 만들기](media/load-sap-bw-data/create-adls-gen2-linked-service.png)

   1. "저장소 계정 이름" 드롭 다운 목록에서 Data Lake 저장소 Gen2 지원 계정을 선택 합니다.
   2. **마침**을 선택하여 연결을 만듭니다. 그런 후 **다음**을 선택합니다.

9. 에 **출력 파일 또는 폴더 선택** 페이지에서 출력 폴더 이름으로 "copyfromopenhub"를 입력 하 고 선택 **다음**합니다.

   ![출력 폴더를 선택 합니다.](media/load-sap-bw-data/choose-output-folder.png)

10. 에 **파일 형식 설정이** 페이지에서 **다음** 기본 설정을 사용 하도록 합니다.

    ![싱크 형식으로 지정 합니다.](media/load-sap-bw-data/specify-sink-format.png)

11. 에 **설정을** 페이지에서 **성능 설정**, 설정 및 **복사 병렬 처리 수준을** SAP BW에서 동시에 로드 하기 위해 5와 같은 합니다. **다음**을 클릭합니다.

    ![복사 설정 구성](media/load-sap-bw-data/configure-copy-settings.png)

12. 에 **요약** 페이지에서 설정을 검토 하 고 선택 **다음**합니다.

13. 에 **배포** 페이지에서 **모니터** 파이프라인을 모니터링 하려면.

    ![배포 페이지](media/load-sap-bw-data/deployment.png)

14. 왼쪽의 **모니터** 탭이 자동으로 선택됩니다. **작업** 열에는 활동 실행 세부 정보를 보고 파이프라인을 다시 실행하기 위한 링크가 있습니다.

    ![파이프라인 모니터링](media/load-sap-bw-data/pipeline-monitoring.png)

15. 파이프라인 실행과 연결된 활동 실행을 보려면 **작업** 열에서 **활동 실행 보기** 링크를 선택합니다. 파이프라인에는 하나의 작업(복사 작업)만 있으므로 하나의 항목만 표시됩니다. 파이프라인 실행 보기로 전환하려면 위쪽의 **파이프라인** 링크를 선택합니다. **새로 고침**을 선택하여 목록을 새로 고칩니다.

    ![활동 모니터링](media/load-sap-bw-data/activity-monitoring.png)

16. 각 복사 작업의 실행 세부 정보를 모니터링하려면 작업 모니터링 보기의 **작업** 아래에서 **세부 정보** 링크(안경 이미지)를 선택합니다. 원본에서 싱크로 복사되는 데이터 볼륨, 데이터 처리량, 해당 기간의 실행 단계, 사용되는 구성 등의 세부 정보를 모니터링할 수 있습니다.

    ![작업 세부 정보를 모니터링 합니다.](media/load-sap-bw-data/activity-monitoring-details.png)

17. 검토 합니다 **최대 요청 ID** 복사 되는 합니다. 작업 보기 모니터링으로 돌아가서,를 클릭 합니다 **출력** 아래에서 **작업**.

    ![활동 출력](media/load-sap-bw-data/activity-output.png)

    ![활동 출력 세부 정보](media/load-sap-bw-data/activity-output-details.png)

## <a name="incremental-copy-from-sap-bw-open-hub"></a>SAP BW 오픈 허브에서 증분 복사

> [!TIP]
>
> 가리킵니다 [SAP BW 오픈 허브 커넥터 델타 추출 흐름](connector-sap-business-warehouse-open-hub.md#delta-extraction-flow) ADF SAP BW 오픈 허브 커넥터와 관련 된 커넥터의 기본 사항을 이해 하기 위해 처음부터이 문서를 읽고 SAP BW에서 데이터 증분 복사를 작동 하는 방식에 대해 자세히 알아보려면 구성입니다.

이제 SAP BW 오픈 허브에서 증분 복사를 구성 하려면 계속 해 보겠습니다. 

증분 복사본을 기반으로 하는 상위 워터 마크 메커니즘을 사용 하는 **요청 ID** DTP에서 SAP BW Open Hub Destination에 자동으로 생성 합니다. 이 방식에 대한 워크플로는 다음 다이어그램과 같습니다.

![증분 복사 워크플로](media/load-sap-bw-data/incremental-copy-workflow.png)

ADF u **시작 해 보겠습니다** 페이지에서 **템플릿에서 파이프라인 만들기** 기본 제공 템플릿을 활용 하 여 합니다. 

1. "SAP BW" 템플릿을 선택 하려면 검색 **SAP BW에서 Azure Data Lake 저장소 Gen2 증분 복사**합니다. 이 템플릿은 데이터를 ADLS Gen2를 복사, 나중에 다른 싱크 형식으로 복사 하는 비슷한 흐름을 따를 수 있습니다.

2. 템플릿 기본 페이지에서 선택 하거나 만든 다음 세 가지 연결 후 선택 **이 템플릿을 사용 하 여** 오른쪽 맨 아래에 있습니다.

   - **Azure Blob**:이 연습에서는를 사용 하 여 Azure Blob는 최대 복사 요청 ID입니다. 상위 워터 마크를 저장 합니다.
   - **SAP BW Open Hub**: 원본에서 데이터를 복사 합니다. 이전 전체 복사본 연습 자세한 구성 정보 내용은을 참조 하십시오.
   - **ADLS Gen2**: 싱크에 데이터를 복사 합니다. 이전 전체 복사본 연습 자세한 구성 정보 내용은을 참조 하십시오.

   ![SAP BW 템플릿에서 증분 복사](media/load-sap-bw-data/incremental-copy-from-sap-bw-template.png)

3. 이 템플릿은 세 개의 활동이-를 사용 하 여 파이프라인을 생성 **조회, 데이터 복사 및 웹** -하며, 해당 연결에 성공 합니다. 파이프라인으로 이동 **매개 변수** 탭 표시를 제공 해야 하는 모든 구성 합니다.

   ![SAP BW 구성에서 증분 복사](media/load-sap-bw-data/incremental-copy-from-sap-bw-pipeline-config.png)

   - **SAPOpenHubDestinationName**: 데이터를 복사해 올 오픈 허브 테이블 이름을 지정 합니다.

   - **ADLSGen2SinkPath**: 데이터를 복사할 대상 ADLS Gen2 경로 지정 합니다. 경로가 없는 경우 ADF 복사 작업 실행 중 만들어집니다.

   - **HighWatermarkBlobPath**: 예: 상위 워터 마크 값을 저장할 경로를 지정 `container/path`합니다. 

   - **HighWatermarkBlobName**: 예: 상위 워터 마크 값을 저장할 blob 이름을 지정 `requestIdCache.txt`합니다. HighWatermarkBlobPath + HighWatermarkBlobName의 해당 경로에서 blob 저장소, 예: "*container/path/requestIdCache.txt*", 0 콘텐츠를 사용 하 여 blob을 만듭니다. 

      ![BLOB 콘텐츠](media/load-sap-bw-data/blob.png)

   - **LogicAppURL**:이 서식 파일을 사용 하 여 웹 작업 Blob 저장소의 상위 워터 마크 값을 설정 하려면 논리 앱을 호출 합니다. 또는를 저장 한 값을 업데이트 하려면 저장 프로시저 작업을 사용 하 여 SQL database도 사용할 수 있습니다. 

      먼저, 다음과 같이 논리 앱을 생성 해야 하는 여기에 복사 합니다 **HTTP POST URL** 이 필드에 합니다. 

      ![논리 앱 구성](media/load-sap-bw-data/logic-app-config.png)

      1. Azure portal로 이동-> 새로 **Logic Apps** 서비스-> **+ 빈 Logic App** 이동할 **Logic Apps 디자이너**합니다.

      2. 트리거를 만듭니다 **경우는 HTTP 요청을 받은**합니다. HTTP 요청 본문을 다음과 같이 지정 합니다.

         ```json
         {
            "properties": {
               "sapOpenHubMaxRequestId": {
                  "type": "string"
               },
               "type": "object"
            }
         }
         ```

      3. 작업을 추가 **blob 만들기**합니다. "Blob 이름"와 "폴더 경로"에 대 한 위의 HighWatermarkBlobPath 및 HighWatermarkBlobName 구성 된 동일한 값을 사용 합니다.

      4. 클릭 **저장할**, 다음 값을 복사 **HTTP POST URL** ADF 파이프라인에서 사용 하 합니다.

4. ADF 파이프라인 매개 변수에 대 한 모든 값을 제공한 후 클릭 **디버그** -> **마침** 구성 유효성 검사 실행을 호출 합니다. 또는 선택할 수 있습니다 **모두 게시** 모든 변경 내용을 게시할 클릭 **트리거** 실행을 합니다.

## <a name="sap-bw-open-hub-destination-configurations"></a>SAP BW Open Hub Destination 구성

이 섹션에서는 ADF에서 SAP BW 오픈 허브 커넥터를 사용 하 여 데이터를 복사 하려면 SAP BW 쪽에서 필요한 구성을 소개 합니다.

### <a name="configure-delta-extraction-in-sap-bw"></a>SAP BW에서 델타 압축 풀기 구성

기록 복사본 및 증분 복사 또는 증분 복사를 해야 하는 경우 SAP BW에서 델타 추출을 구성 합니다.

1. 오픈 허브 대상을 (OHD) 만들기

   자동으로 필요한 변환 및 데이터 전송 프로세스 (DTP)을 만드는 SAP 트랜잭션 RSA1는 OHD를 만들 수 있습니다. 다음 설정을 사용합니다.

   - 모든 개체 형식으로 사용할 수 있습니다. 여기 InfoCube를 예로 사용합니다.
   - **대상 유형:** *데이터베이스 테이블*
   - **테이블의 키:** *기술 키*
   - **추출:** *테이블에 레코드를 삽입 하 고 데이터 유지*

   ![SAP BW OHD 델타 추출 만들기](media/load-sap-bw-data/create-sap-bw-ohd-delta.png)

   ![create-sap-bw-ohd-delta2](media/load-sap-bw-data/create-sap-bw-ohd-delta2.png)

   DTP를에 대 한 SAP 작업 프로세스를 실행 하는 병렬 개수를 늘릴 수 있습니다.

   ![create-sap-bw-ohd-delta3](media/load-sap-bw-data/create-sap-bw-ohd-delta3.png)

2. 프로세스 체인에 DTP를 예약 합니다.

   경우는 큐브에 대 한 델타 DTP 에서만 작동 합니다 필요한 행 하지 압축 된 아직 있습니다. 따라서 오픈 허브 테이블에 BW 큐브 압축 DTP를 하기 전에 실행 하지는 않도록 해야 합니다. 이 가장 쉬운 방법은 기존 프로세스 체인을이 DTP를 통합 합니다. 아래 예제에서는 (OHD)에 DTP 단계 사이의 프로세스 체인에 삽입 됩니다 (집계 롤업) 조정 및 축소 (큐브 압축).

   ![create-sap-bw-process-chain](media/load-sap-bw-data/create-sap-bw-process-chain.png)

### <a name="configure-full-extraction-in-sap-bw"></a>SAP BW에서 전체 추출 구성

델타 추출 하는 것 외에도 동일한 InfoProvider의 전체 추출을 하는 것이 좋습니다. 하려는 또는 전체 증분 필요 없이 복사를 수행 하려는 경우 일반적으로 적용 [델타 추출을 다시 동기화](#re-sync-delta-extraction)합니다.

동일한 OHD에 대 한 둘 이상의 DTP 아니어야 합니다. 따라서 추가 OHD 다음 델타 추출을 만들 필요가 있습니다.

![create-sap-bw-ohd-full](media/load-sap-bw-data/create-sap-bw-ohd-full.png)

전체 부하 OHD 델타 추출 보다 다양 한 옵션을 선택 합니다.

- OHD: "추출" 옵션으로 설정 "*데이터 삭제 및 삽입 레코드*"입니다. 그렇지 않은 경우 여러 번 BW 프로세스 체인에 DTP를 반복 하면 데이터를 추출할 수는 합니다.

- 에 DTP: "추출 모드"로 "*전체*"입니다. OHD를 만든 후에 자동으로 만든된 DTP 델타에서 Full로 변경 해야 합니다.

   ![create-sap-bw-ohd-full2](media/load-sap-bw-data/create-sap-bw-ohd-full2.png)

- ADF SAP BW 오픈 허브 커넥터의: 옵션을 해제 "*제외 마지막 요청*"입니다. 그렇지 않으면 아무 것도 압축을 풉니다. 

일반적으로 전체 DTP를 수동으로 실행할 수 있습니다. 또는 전체 DTP에 대 한 프로세스 체인을 만들 수도 있습니다-기존 프로세스 체인에서 독립적인 별도 프로세스 체인을 일반적으로 것입니다. 해야 하 든에서 **DTP를 ADF 복사본을 사용 하 여 추출 시작 하기 전에 완료 해야**, 불완전 한 데이터가 복사 됩니다 그렇지 않으면.

### <a name="run-delta-extraction-the-first-time"></a>델타 추출 처음으로 실행

첫 번째 델타 추출은 기술적으로 **전체 추출**합니다. 데이터를 복사할 때 기본 ADF SAP BW 오픈 허브 커넥터에서 참고 마지막 요청을 제외 합니다. ADF 복사 작업에는 처음에 대 한 델타 추출의 경우 데이터가 없는 추출할 때까지 후속 DTP 별도 요청 id입니다. 포함 된 테이블에서 델타 데이터를 생성 합니다. 이 시나리오를 방지 하려면 두 가지 방법이 있기는 합니다.

1. "제외" 라는 마지막 요청에 대 한 첫 번째 델타 추출 델타 추출 처음으로 시작 하기 전에 첫 번째 델타 DTP 되었음을 확인 해야 하는이 경우 옵션을 해제
2. 아래 설명 된 대로 델타 추출 다시 동기화에 대 한 절차를 따르십시오.

### <a name="re-sync-delta-extraction"></a>델타 동기화 다시 추출

SAP BW 큐브에서 데이터를 변경 하지만 델타 DTP를 고려 하지는 몇 가지 시나리오가 있습니다.

- SAP BW 선택적 행을 삭제 (필터 조건을 사용 하 여)
- (잘못 된 요청)의 SAP BW 요청 삭제

SAP Open Hub Destination (모든 SAP BW 지원 2015 년 이후 패키지)에서 데이터 마트 제어 된 데이터 대상이 아닙니다. 따라서 큐브에서 OHD에서 데이터를 변경 하지 않고 데이터를 삭제 하는 것이 같습니다. 이 경우 다음 단계를 수행 하 여 ADF에 데이터를 사용 하 여 큐브의 데이터를 다시 동기화 해야 합니다.

1. (전체 DTP를 사용 하 여 SAP의) 하 여 ADF에 전체 추출 실행
2. 델타 DTP 오픈 허브 테이블의 모든 행 삭제
3. 인출으로 델타 DTP 상태 설정

그런 다음 모든 후속 델타 Dtp 및 ADF 델타 추출 제대로 예상 대로 작동 합니다.

다음 옵션을 사용 하 여 수동으로 델타 DTP를 실행 하 여 인출 하는 델타 DTP의 상태를 설정할 수 있습니다. "*데이터 전송이 없습니다. 원본에서 델타 상태: 인출*"입니다.

## <a name="next-steps"></a>다음 단계

SAP BW Open Hub 커넥터 지원에 대해 자세히 알아보려면 다음 문서를 계속 진행 합니다. 

> [!div class="nextstepaction"]
>[SAP Business Warehouse 오픈 허브 커넥터](connector-sap-business-warehouse-open-hub.md)
