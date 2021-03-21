---
title: SAP R3의 메타 데이터 추출 ABAP 함수 모듈-Azure 부서의 범위
description: 이 문서에서는 SAP Server에 ABAP 함수 모듈을 배포 하는 단계를 설명 합니다.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/13/2020
ms.openlocfilehash: 9bd3c315fcc15317a9fa483289fdc326ca6aa47f
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102614363"
---
# <a name="deploy-the-metadata-extraction-abap-function-module-for-the-sap-r3-family-of-bridges"></a>SAP R3 브리지 패밀리에 대 한 메타 데이터 추출 ABAP 함수 모듈을 배포 합니다.

이 문서에서는 SAP Server에 ABAP 함수 모듈을 배포 하는 단계를 간략하게 설명 합니다.

## <a name="overview"></a>개요

Sap Business Suite 4 HANA (S/4HANA), ECC 및 R/3 ERP 브리지는 SAP 서버에서 메타 데이터를 추출 하는 데 사용할 수 있습니다. 이는 ABAP 함수 모듈을 SAP 서버에 배치 하 여 수행 됩니다. 이 함수 모듈은 원격으로 연결 하 여 SAP 서버 내에 포함 된 메타 데이터를 쿼리 및 다운로드 (텍스트 파일로) 할 수 있습니다.

실행 되 면 브리지는 다음 중 하나를 수행 합니다.

1. 이전 브리지 실행에서 로컬로 이미 다운로드 한 기존 파일에서 메타 데이터를 가져옵니다.

2. ABAP 모듈 API를 호출 하 고 다운로드를 기다린 다음 해당 파일에서 메타 데이터를 가져옵니다.

이 문서에서는이 모듈을 배포 하는 데 필요한 단계에 대해 자세히 설명 합니다.

> [!Note]
> 다음 지침은 SAP GUI에 기반 하 여 컴파일 되었습니다. 7.2

## <a name="deployment-of-the-module"></a>모듈 배포

### <a name="create-a-package"></a>패키지 만들기

이 단계는 선택 사항이 며 기존 패키지를 사용할 수 있습니다.

1. SAP S/4HANA 또는 SAP ECC 서버에 로그인 하 고 **개체 탐색기** (SE80 transaction)를 엽니다.

2. 목록에서 옵션 **패키지** 를 선택 하 고 새 패키지의 이름 (예: Z \_ MITI)을 입력 한 다음 단추 **표시** 를 누릅니다.

3. **패키지 만들기** 창에서 **예** 를 선택 합니다. 따라서 창 **패키지 작성기: 패키지 만들기** 가 열립니다. **간단한 설명** 필드에 값을 입력 하 고 **계속** 아이콘을 선택 합니다.

4. **로컬 워크 벤치 요청 확인** 창에서 **고유한 요청** 을 선택 합니다. **개발** 요청을 선택 합니다.

### <a name="create-a-function-group"></a>함수 그룹 만들기

개체 탐색기의 목록에서 **함수 그룹** 을 선택 하 고 아래 입력 필드에 해당 이름을 입력 합니다 (예: Z \_ MITI \_ fgroup). **보기** 아이콘을 선택 합니다.

1. **개체 만들기** 창에서 **예** 를 선택 하 여 새 함수 그룹을 만듭니다.

2. **짧은 텍스트** 필드에 적절 한 설명을 지정 하 고 단추 **저장** 을 누릅니다.

3. 이전 단계에서 **패키지 만들기** 에서 준비한 패키지를 선택 하 고 **저장** 을 선택 합니다.

4. 아이콘 **계속** 을 눌러 요청을 확인 합니다.

5. 함수 그룹을 활성화 합니다.

### <a name="create-the-abap-function-module"></a>ABAP 함수 모듈 만들기

1. 함수 그룹을 만든 후에는 해당 그룹을 선택 합니다.

2. 리포지토리 브라우저에서 함수 그룹 이름을 마우스 오른쪽 단추로 클릭 하 고 **만들기**, **함수 모듈** 을 차례로 선택 합니다.

3. **함수 모듈** 필드에를 입력 `Z_MITI_DOWNLOAD` 합니다. **간단한 텍스트** 입력을 적절 한 설명으로 채웁니다.

모듈을 만든 후에는 다음 정보를 지정 합니다.

1. **특성** 탭으로 이동 합니다.

2. **처리 유형** 을 **원격 사용 함수 모듈로** 선택 합니다.

   :::image type="content" source="media/abap-functions-deployment-guide/processing-type.png" alt-text="소스 등록 옵션-함수 모듈 Remote-Enabled" border="true":::

3. **소스 코드** 탭으로 이동 합니다. 다음 두 가지 방법으로 함수에 대 한 코드를 배포할 수 있습니다.

   a. 주 메뉴에서 텍스트 파일 [Z \_ MITI \_ 다운로드](https://github.com/Azure/Purview-Samples/tree/master/connectors/sap) 파일을 업로드 합니다. 이렇게 하려면 **유틸리티**, **기타 유틸리티**, **업로드/다운로드**, **업로드** 를 차례로 선택 합니다.

   b. 또는 파일을 열고 콘텐츠를 복사 하 여 **소스 코드** 영역에 붙여 넣습니다.

4. **가져오기** 탭으로 이동 하 고 다음 매개 변수를 만듭니다.

   a.  P \_ 영역 유형 DD02L-TABNAME (선택 사항 = True)

   b.  *P \_ 로컬 \_ 경로 유형 문자열* (선택 사항 = True)

   다.  *P \_ 언어 유형 L001TAB-데이터 기본값 \' E\'*

   d.  *ROWSKIPS 형식 이므로 \_ INT 기본값은 0입니다.*

   e.  *행 개수 유형 ( \_ INT 기본값 0)*

   > [!Note]
   > 모든 항목에 대해 **전달 값** 선택

   :::image type="content" source="media/abap-functions-deployment-guide/import.png" alt-text="소스 등록 옵션-가져오기 매개 변수" border="true":::

5. "테이블" 탭으로 이동 하 여 다음을 정의 합니다.

   `EXPORT_TABLE LIKE TAB512`

   :::image type="content" source="media/abap-functions-deployment-guide/export-table.png" alt-text="소스 등록 옵션-테이블 탭" border="true":::

6. **예외** 탭으로 이동 하 여 다음 예외를 정의 합니다.`E_EXP_GUI_DOWNLOADFAILED`

   :::image type="content" source="media/abap-functions-deployment-guide/exceptions.png" alt-text="소스 등록 옵션-예외 탭" border="true":::

7. 함수를 저장 합니다 (ctrl + S를 누르거나 **함수 모듈** 을 선택한 다음 주 메뉴에서 **저장** ).

8. 도구 모음에서 **활성화** 아이콘 (Ctrl + F3)을 클릭 하 고 대화 상자 창에서  **계속** 단추를 선택 합니다. 메시지가 표시 되 면 생성 된 포함을 선택 하 여 주 함수 모듈과 함께 활성화 해야 합니다.

### <a name="testing-the-function"></a>함수 테스트

모든 이전 단계가 완료 되 면 아래 단계에 따라 함수를 테스트 합니다.

1. Z \_ MITI \_ DOWNLOAD 함수 모듈을 엽니다.

2. **함수 모듈** 을 선택한 다음, 주 메뉴에서 **함수 모듈** **을 테스트 하거나** F8 키를 누릅니다.

3. 로컬 파일 시스템의 폴더 경로를 매개 변수 P 로컬 경로에 입력 \_ \_ 하 고 도구 모음에서 **실행** 아이콘을 누르거나 F8 키를 누릅니다.

4. \_메타 데이터가 포함 된 파일을 다운로드 하거나 업데이트 해야 하는 경우에는 원하는 영역의 이름을 P 영역 필드에 입력 합니다. 함수가 작업을 마치면 P LOCAL PATH 매개 변수에 표시 된 폴더에는 \_ \_ 메타 데이터가 포함 된 여러 개의 파일이 포함 되어야 합니다. 파일 이름은 P 영역 필드에 지정할 수 있는 영역을 모방 \_ 합니다.

함수 실행이 완료 되 고 메타 데이터는 SAP S/4HANA 또는 ECC 서버와 고속 네트워크 연결을 사용 하는 컴퓨터에서 실행 하는 경우 훨씬 빠르게 다운로드 됩니다.

## <a name="next-steps"></a>다음 단계

- [SAP ECC 원본 등록 및 스캔](register-scan-sapecc-source.md)
- [SAP S/4HANA 원본 등록 및 스캔](register-scan-saps4hana-source.md)
