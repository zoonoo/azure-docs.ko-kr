---
title: Azure files를 검색 하는 방법
description: 이 방법 가이드에서는 Azure files를 검색 하는 방법에 대해 자세히 설명 합니다.
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 10/01/2020
ms.openlocfilehash: a0bd7a4cd8afafc16f05b4a37cd5723304ad931e
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/03/2020
ms.locfileid: "96554890"
---
# <a name="register-and-scan-azure-files"></a>Azure Files 등록 및 검색

## <a name="supported-capabilities"></a>지원되는 기능

Azure Files는 시스템 및 고객 분류에 따라 메타 데이터를 캡처하고 메타 데이터에 분류를 적용 하기 위한 전체 및 증분 검색을 지원 합니다.

## <a name="prerequisites"></a>필수 조건

- 데이터 원본을 등록 하기 전에 Azure 부서의 범위 계정을 만듭니다. 부서의 범위 계정을 만드는 방법에 대 한 자세한 내용은 [빠른 시작: Azure 부서의 범위 계정 만들기](create-catalog-portal.md)를 참조 하세요.
- 검색을 설정 하 고 예약 하려면 데이터 원본 관리자 여야 합니다. 자세한 내용은 [카탈로그 권한](catalog-permissions.md) 을 참조 하세요.

## <a name="register-an-azure-files-storage-account"></a>Azure Files storage 계정 등록

데이터 카탈로그에 새 Azure Files 계정을 등록 하려면 다음을 수행 합니다.

1. 부서의 범위 Data Catalog로 이동 합니다.
1. 왼쪽 탐색 모음에서 **관리 센터** 를 선택 합니다.
1. **원본 및 검색** 에서 **데이터 원본** 을 선택 합니다.
1. **+새로 만들기** 를 선택합니다.
1. **소스 등록** 에서 **Azure Files** 를 선택 합니다. **계속** 을 선택합니다.

:::image type="content" source="media/register-scan-azure-files/register-new-data-source.png" alt-text="새 데이터 원본 등록" border="true":::

**소스 등록 (Azure Files)** 화면에서 다음을 수행 합니다.

1. 카탈로그에서 데이터 원본이 나열 될 **이름을** 입력 합니다.
1. 원하는 저장소 계정을 가리키는 방법을 선택 합니다.
   1. **Azure 구독에서** 를 선택 하 고, **azure 구독** 드롭다운 상자에서 적절 한 구독을 선택 하 고, **저장소 계정 이름** 드롭다운 상자에서 적절 한 저장소 계정을 선택 합니다.
   1. 또는 **수동으로 입력** 을 선택 하 고 서비스 끝점 (URL)을 입력할 수 있습니다.
1. 데이터 원본 등록을 **완료** 합니다.

:::image type="content" source="media/register-scan-azure-files/register-sources.png" alt-text="소스 등록 옵션" border="true":::

## <a name="set-up-authentication-for-a-scan"></a>검색에 대 한 인증 설정

계정 키를 사용 하 여 Azure Files 저장소에 대 한 인증을 설정 하려면 다음을 수행 합니다.

1. 인증 방법을 **계정 키** 로 선택 합니다.
2. **Azure 구독에서 옵션을** 선택 합니다.
3. Azure Files 계정이 있는 Azure 구독을 선택 합니다.
4. 목록에서 저장소 계정 이름을 선택 합니다.
5. **Finish** 를 클릭합니다.

[!INCLUDE [create and manage scans](includes/manage-scans.md)]

## <a name="next-steps"></a>다음 단계

- [Azure 부서의 범위 Data catalog 찾아보기](how-to-browse-catalog.md)
- [Azure 부서의 범위 Data Catalog 검색](how-to-search-catalog.md)