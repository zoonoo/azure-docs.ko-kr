---
title: Teradata 원본 등록 및 검사 설정(미리 보기)
description: 이 문서에서는 Azure Purview에서 Teradata 원본을 등록하고 검사를 설정하는 방법을 설명합니다.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: overview
ms.date: 12/06/2020
ms.openlocfilehash: 170ece293f8d24f3a29774c64c14f9334fe0930c
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/07/2020
ms.locfileid: "96841686"
---
# <a name="register-and-scan-teradata-source-preview"></a>Teradata 원본 등록 및 검사(미리 보기)

이 문서에서는 Purview에서 Teradata 원본을 등록하고 검사를 설정하는 방법에 대해 설명합니다.

> [!IMPORTANT]
> 이 데이터 원본은 현재 미리 보기에 있습니다. 사용해 보고 피드백을 제공할 수 있습니다.

## <a name="supported-capabilities"></a>지원되는 기능

Teradata 원본은 Teradata 데이터베이스에서 메타데이터를 추출할 수 있도록 **전체 검사** 를 지원하고, 데이터 자산 간의 **계보** 를 가져옵니다.

## <a name="prerequisites"></a>사전 요구 사항

- 이 커넥터는 온-프레미스 네트워크, Azure 가상 네트워크 또는 Amazon Virtual Private Cloud 내부에만 있는 데이터 저장소를 지원합니다. 따라서 연결하려면 [자체 호스팅 통합 런타임](manage-integration-runtimes.md)을 설정해야 합니다.

- 자체 호스팅 통합 런타임이 설치된 가상 머신에 JRE(Java Runtime Environment)가 설치되어 있는지 확인합니다.
 
- 자체 호스팅 통합 런타임 컴퓨터에 "Visual C++ 재배포 가능 패키지 2012 업데이트 4"가 설치되어 있는지 확인합니다. 아직 설치되지 않은 경우 [여기](https://www.microsoft.com/download/details.aspx?id=30679)서 다운로드하세요.

- Teradata JDBC Driver라는 드라이버를 온-프레미스 가상 머신에 수동으로 설치해야 합니다. JAR 실행 파일은 [Teradata 웹 사이트](https://downloads.teradata.com/)에서 다운로드할 수 있습니다.

    > [!Note] 
    > VM의 모든 계정에서 드라이버에 액세스할 수 있어야 합니다. 사용자 계정에는 설치하지 마세요.

- 지원되는 Teradata 데이터베이스 버전은 **12.x ~ 16.x** 입니다. 검사되는 Teradata 원본에 대한 읽기 액세스 권한이 있는지 확인하세요.

### <a name="feature-flag"></a>기능 플래그

Teradata 원본을 등록하고 검사하는 기능은 기능 플래그 뒤에서 사용할 수 있습니다. *?feature.ext.datasource=%7b"teradata":"true"%7d* 를 URL에 추가합니다. 

예를 들어 전체 URL은 [https://web.purview.azure.com/?feature.ext.datasource=%7b"metadata":"true"%7d](https://web.purview.azure.com/?feature.ext.datasource=%7b"teradata":"true"%7d)입니다.

## <a name="setting-up-authentication-for-a-scan"></a>검사 인증 설정
Teradata 원본에 대한 인증을 설정하는 데 지원되는 유일한 방법은 **기본 데이터베이스** 인증입니다.

## <a name="register-a-teradata-source"></a>Teradata 원본 등록

새 Teradata 원본을 데이터 카탈로그에 등록하려면 다음을 수행합니다.

1. Purview 계정으로 이동합니다.
2. 왼쪽 탐색 영역에서 **원본** 을 선택합니다.
3. **등록** 을 선택합니다.
4. **원본 등록** 에서 **Teradata** 를 선택합니다.
5. **계속** 을 선택합니다.

**원본 등록(Teradata)** 화면에서 다음을 수행합니다.

1. 카탈로그에서 나열되는 데이터 원본의 **이름** 을 입력합니다.
2. Teradata 원본에 연결할 **호스트** 의 이름을 입력합니다. 이는 서버에 대한 IP 주소 또는 정규화된 연결 문자열일 수도 있습니다.
3. 컬렉션을 선택하거나 새로 만듭니다(선택 사항).
4. **마침** 을 선택하여 데이터 원본을 등록합니다.

    :::image type="content" source="media/register-scan-teradata-source/register-sources.png" alt-text="원본 등록 옵션" border="true":::

## <a name="creating-and-running-a-scan"></a>검사 만들기 및 실행

새 검색을 만들고 실행하려면 다음을 수행합니다.
1. 관리 센터에서 *통합 런타임* 을 클릭합니다. 자체 호스팅 통합 런타임이 설정되어 있는지 확인합니다. 설정되지 않은 경우 [여기](manage-integration-runtimes.md)서 설명하는 단계를 사용하여 온-프레미스 네트워크에 액세스할 수 있는 온-프레미스 또는 Azure VM에서 검사하기 위한 자체 호스팅 통합 런타임을 만듭니다.

2. 다음으로 **원본** 을 탐색합니다.

3. 등록된 Teradata 원본을 선택합니다.

4. + 새 검사 선택
 
5. 아래 세부 정보를 제공합니다.

    - 이름: 검사 이름

    - 통합 런타임을 통해 연결: 구성된 자체 호스팅 통합 런타임을 선택합니다.

    - 인증 방법: 데이터베이스 인증은 현재 유일하게 지원되는 옵션입니다. 기본적으로 선택됩니다.

    - 사용자 이름: 데이터베이스 서버에 연결하는 데 사용되는 사용자 이름입니다. 이 사용자 이름에는 서버에 대한 읽기 액세스 권한이 있어야 합니다.

    - 암호: 데이터베이스 서버에 연결하는 데 사용되는 사용자 암호입니다.

    - 스키마: 가져올 스키마의 하위 집합을 나열하며, 세미콜론으로 구분된 목록으로 표현됩니다. 예를 들어 schema1; schema2입니다. 목록이 비어 있으면 모든 사용자 스키마를 가져옵니다. 모든 시스템 스키마(예: SysAdmin) 및 개체는 기본적으로 무시됩니다.

        SQL LIKE 식 구문을 사용하는 허용되는 스키마 이름 패턴에는 %가 포함됩니다. 예를 들어 A%, %B, %C%, D입니다.
        - A로 시작합니다. 또는    
        - B로 끝납니다. 또는    
        - C를 포함합니다. 또는    
        - D와 같습니다.

        NOT 및 특수 문자는 허용되지 않습니다.

    - 드라이버 위치: 고객 VM의 Teradata 드라이버 위치에 대한 전체 경로입니다. Teradata JDBC 드라이버 이름은 com.teradata.jdbc.TeraDriver여야 합니다.

    - 사용 가능한 최대 메모리: 고객의 VM에서 검사 프로세스를 수행하는 데 사용할 수 있는 최대 메모리(GB)입니다. 이는 검사할 Teradata 원본의 크기에 따라 달라집니다.

    > [!Note] 
    > 일반적으로 1000개 테이블당 2GB 메모리를 제공하세요.

    :::image type="content" source="media/register-scan-teradata-source/setup-scan.png" alt-text="검사 설정" border="true":::

6. *계속* 을 클릭합니다.

7. 검사 트리거를 선택합니다. 예약을 설정하거나 검사를 한 번 실행할 수 있습니다.

    :::image type="content" source="media/register-scan-teradata-source/scan-trigger.png" alt-text="검사 트리거" border="true":::

8. 검사를 검토하고, *저장 및 실행* 을 클릭합니다.

## <a name="viewing-your-scans-and-scan-runs"></a>검사 및 검사 실행 보기

1. 관리 센터로 이동합니다. **원본 및 검사** 섹션에서 **데이터 원본** 을 선택합니다.

2. 원하는 데이터 원본을 선택합니다. 해당 데이터 원본에 기존 검사 목록이 표시됩니다.

3. 결과를 보려는 검사를 선택합니다.

4. 이 페이지는 각 스캔 실행에 대한 메트릭 및 상태와 함께 모든 이전 검사 실행을 표시합니다. 또한 검사가 예약되었는지 수동인지 여부, 분류가 적용된 자산 수, 발견된 총 자산 수, 검사 시작 및 종료 시간, 총 검사 기간이 표시됩니다.

## <a name="manage-your-scans"></a>검사 관리

검사를 관리하거나 삭제하려면 다음을 수행합니다.

1. 관리 센터로 이동합니다. **원본 및 검사** 섹션에서 **데이터 원본** 을 선택한 다음, 원하는 데이터 원본을 선택합니다.

2. 관리하려는 검사를 선택합니다. **편집** 을 선택하여 검사를 편집할 수 있습니다.

3. **삭제** 를 선택하여 검사를 삭제할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Azure Purview 데이터 카탈로그 찾아보기](how-to-browse-catalog.md)
- [Azure Purview Data Catalog 검색](how-to-search-catalog.md)
