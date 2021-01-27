---
title: Azure 데이터 탐색기을 검색 하는 방법
description: 이 방법 가이드에서는 Azure 데이터 탐색기를 검색 하는 방법에 대해 자세히 설명 합니다.
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 10/9/2020
ms.openlocfilehash: 7adc7f568fb82692f2c96f610575076e397bd99c
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98896115"
---
# <a name="register-and-scan-azure-data-explorer"></a>Azure 데이터 탐색기 등록 및 스캔

이 문서에서는 azure 부서의 범위에서 Azure 데이터 탐색기 계정을 등록 하 고 검색을 설정 하는 방법을 설명 합니다.

## <a name="supported-capabilities"></a>지원되는 기능

Azure 데이터 탐색기는 메타 데이터 및 스키마를 캡처하기 위한 전체 및 증분 검색을 지원 합니다. 또한 검색은 시스템 및 사용자 지정 분류 규칙을 기반으로 데이터를 자동으로 분류 합니다.

## <a name="prerequisites"></a>필수 구성 요소

- 데이터 원본을 등록 하기 전에 Azure 부서의 범위 계정을 만듭니다. 부서의 범위 계정을 만드는 방법에 대 한 자세한 내용은 [빠른 시작: Azure 부서의 범위 계정 만들기](create-catalog-portal.md)를 참조 하세요.
- Azure 부서의 범위 데이터 원본 관리자 여야 합니다.

## <a name="setting-up-authentication-for-a-scan"></a>검사 인증 설정

Azure 데이터 탐색기에 대 한 인증을 설정 하는 방법은 한 가지 뿐입니다.

- 서비스 주체

### <a name="service-principal"></a>서비스 사용자

검색에 대 한 서비스 주체 인증을 사용 하려면 기존 항목을 사용 하거나 새로 만들 수 있습니다. 

> [!Note]
> 새 서비스 주체를 만들어야 하는 경우 다음 단계를 수행하세요.
> 1. [Azure Portal](https://portal.azure.com)로 이동합니다.
> 1. 왼쪽 메뉴에서 **Azure Active Directory** 를 선택합니다.
> 1. **앱 등록** 을 선택합니다.
> 1. **+ 새 애플리케이션 등록** 을 선택합니다.
> 1. **애플리케이션** 의 이름(서비스 사용자 이름)을 입력합니다.
> 1. **이 조직 디렉터리의 계정만** 을 선택합니다.
> 1. 리디렉션 URI에 대해 **웹** 을 선택하고 원하는 URL을 입력합니다. 실제 또는 작업 URL일 필요가 없습니다.
> 1. 그런 다음, **등록** 을 선택합니다.

서비스 주체의 응용 프로그램 ID 및 암호를 가져오는 데 필요 합니다.

1. [Azure Portal](https://portal.azure.com)에서 서비스 주체로 이동합니다.
1. **개요** 에서 **애플리케이션(클라이언트) ID** 값을 복사하고, **인증서 및 비밀** 에서 **클라이언트 암호** 값을 복사합니다.
1. 키 자격 증명 모음으로 이동
1. **설정 > 비밀** 을 선택합니다.
1. **+ 생성/가져오기** 를 선택하고, 선택한 **이름** 및 **값** 을 서비스 주체의 **클라이언트 암호** 로 입력합니다.
1. **만들기** 를 선택하여 완료합니다.
1. 키 자격 증명 모음이 아직 Purview에 연결되지 않은 경우 [새 키 자격 증명 모음 연결을 생성](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)해야 합니다.
1. 마지막으로 서비스 주체를 통해 [새 자격 증명을 생성](manage-credentials.md#create-a-new-credential)하여 검사를 설정합니다.

#### <a name="granting-the-service-principal-access-to-your-azure-data-explorer-instance"></a>서비스 사용자에 게 Azure 데이터 탐색기 인스턴스에 대 한 액세스 권한 부여

1. Azure Portal로 이동합니다. 그런 다음 Azure 데이터 탐색기 인스턴스로 이동 합니다.

1. 다음 스크린샷에 표시 된 것 처럼 **사용 권한** 탭의 **AllDatabasesViewer** 역할에 서비스 주체를 추가 합니다.

    :::image type="content" source="./media/register-scan-azure-data-explorer/permissions-auth.png" alt-text="권한에서 서비스 주체를 추가 하는 스크린샷" border="true":::

## <a name="register-an-azure-data-explorer-account"></a>Azure 데이터 탐색기 계정 등록

데이터 카탈로그에 새 Azure 데이터 탐색기 (Kusto) 계정을 등록 하려면 다음을 수행 합니다.

1. Purview 계정으로 이동합니다.
1. 왼쪽 탐색 영역에서 **원본** 을 선택합니다.
1. **등록** 을 선택합니다.
1. **소스 등록** 에서 **Azure 데이터 탐색기** 를 선택 합니다.
1. **계속** 을 선택합니다.

:::image type="content" source="media/register-scan-azure-data-explorer/register-new-data-source.png" alt-text="새 데이터 원본 등록" border="true":::

**원본 등록 데이터 탐색기 (Kusto)** 화면에서 다음을 수행 합니다.

1. 카탈로그에서 나열되는 데이터 원본의 **이름** 을 입력합니다.
1. 원하는 스토리지 계정을 가리키는 방법을 선택합니다.
   1. **Azure 구독에서** 를 선택 하 고, **azure 구독** 드롭다운 상자에서 적절 한 구독을 선택 하 고, **클러스터** 드롭다운 상자에서 적절 한 클러스터를 선택 합니다.
   1. 또는 **수동으로 입력** 을 선택 하 고 서비스 끝점 (URL)을 입력할 수 있습니다.
1. **마침** 을 선택하여 데이터 원본을 등록합니다.

:::image type="content" source="media/register-scan-azure-data-explorer/register-sources.png" alt-text="원본 등록 옵션" border="true":::

[!INCLUDE [create and manage scans](includes/manage-scans-azure-data-explorer.md)]

## <a name="next-steps"></a>다음 단계

- [Azure Purview 데이터 카탈로그 찾아보기](how-to-browse-catalog.md)
- [Azure Purview Data Catalog 검색](how-to-search-catalog.md)
