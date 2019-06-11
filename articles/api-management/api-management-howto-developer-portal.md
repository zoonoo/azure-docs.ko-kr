---
title: 액세스 및 새 개발자 포털-Azure API Management를 사용자 지정 | Microsoft Docs
description: API Management에서 새 개발자 포털을 사용 하는 방법을 알아봅니다.
services: api-management
documentationcenter: API Management
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/12/2019
ms.author: apimpm
ms.openlocfilehash: deb3fdf3069aaad4982d71806c209fe516e3c3d6
ms.sourcegitcommit: 7042ec27b18f69db9331b3bf3b9296a9cd0c0402
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/06/2019
ms.locfileid: "66743577"
---
# <a name="access-and-customize-the-new-developer-portal-in-azure-api-management"></a>액세스 및 새 Azure API Management에서 개발자 포털 사용자 지정

이 문서에서는 새 Azure API Management 개발자 포털에 액세스 하는 방법을 보여 줍니다. 추가 및 콘텐츠 편집 뿐만 아니라 웹 사이트의 모양 사용자 지정 시각적 편집기 환경-을 안내 합니다.

![새 API Management 개발자 포털](media/api-management-howto-developer-portal/cover.png)

## <a name="prerequisites"></a>필수 조건

- 다음 빠른 시작을 완료합니다. [Azure API Management 인스턴스 만들기](get-started-create-service-instance.md)
- Azure API Management 인스턴스를 가져오고 게시합니다. 자세한 내용은 [가져오기 및 게시](import-and-publish.md)를 참조하세요.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

> [!NOTE]
> 새 개발자 포털은 현재 미리 보기로 제공에서 합니다.

## <a name="managed-and-self-hosted-versions"></a>관리 되 고 자체 호스팅 버전

두 가지 방법으로 개발자 포털을 빌드할 수 있습니다.

- **관리 되는 버전** -편집 및 포털에 기본 제공 API Management 인스턴스를 사용자 지정 하 여 URL을 통해 액세스할 수 있습니다 및 `<your-api-management-instance-name>.developer.azure-api.net`합니다.
- **자체 호스팅된 버전** -API Management 인스턴스 외부에서 포털 자체 호스팅 및 배포 합니다. 이 방법은 포털을 편집할 수 코드 베이스 및 제공 된 핵심 기능을 확장 합니다. 세부 정보 및 지침에 대 한 참조를 [포털의 소스 코드를 사용 하 여 GitHub 리포지토리][1]합니다.

## <a name="access-the-managed-version-of-the-portal"></a>관리 되는 버전의 포털에 액세스

관리 되는 버전의 포털에 액세스 하려면 아래 단계를 따릅니다.

1. Azure portal에서 API Management 서비스 인스턴스로 이동 합니다.
1. 클릭 합니다 **새 개발자 포털 (미리 보기)** 위쪽 탐색 모음에서 단추입니다. 포털의 관리자는 버전을 사용 하 여 새 브라우저 탭이 열립니다. 포털에 처음으로 액세스 하는 경우 기본 콘텐츠가 자동으로 프로 비전 됩니다.

## <a name="edit-and-customize-the-managed-version-of-the-portal"></a>편집 및 관리 되는 버전의 포털을 사용자 지정

아래 비디오에서는 포털의 콘텐츠 편집, 웹 사이트의 모양을 사용자 지정할 변경 내용을 게시 하는 방법을 보여 줍니다.

> [!VIDEO https://www.youtube.com/embed/5mMtUSmfUlw]

## <a name="next-steps"></a>다음 단계

새 개발자 포털에 대 한 자세한 정보:

- [소스 코드를 사용 하 여 GitHub 리포지토리][1]
- [포털 자체 호스팅에 대 한 지침][2]
- [프로젝트의 공개 로드맵][3]

[1]: https://aka.ms/apimdevportal
[2]: https://github.com/Azure/api-management-developer-portal/wiki
[3]: https://github.com/Azure/api-management-developer-portal/projects