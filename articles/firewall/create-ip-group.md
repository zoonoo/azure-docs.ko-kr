---
title: Azure 방화벽에서 IP 그룹 만들기
description: IP 그룹을 사용 하 여 Azure 방화벽 규칙에 대 한 IP 주소를 그룹화 및 관리할 수 있습니다.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 02/18/2020
ms.author: victorh
ms.openlocfilehash: 7e8b2350b9e85d07ce1c399ce4536703ec998cbc
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/18/2020
ms.locfileid: "77444538"
---
# <a name="create-ip-groups-preview"></a>IP 그룹 만들기 (미리 보기)

> [!IMPORTANT]
> 이 공개 미리 보기는 Service Level Agreement(서비스 수준 약정)없이 제공되므로 프로덕션 워크로드에 사용하지 말아야 합니다. 특정 기능은 지원되지 않을 수 있거나, 기능이 제한될 수 있거나 모든 Azure 위치에서 사용하지는 못할 수 있습니다. 자세한 내용은 [Microsoft Azure 미리 보기에 대한 보충 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

IP 그룹을 사용 하 여 Azure 방화벽 규칙에 대 한 IP 주소를 그룹화 및 관리할 수 있습니다. 단일 IP 주소, 여러 IP 주소 또는 하나 이상의 IP 주소 범위를 가질 수 있습니다.

## <a name="create-an-ip-group"></a>IP 그룹 만들기

1. Azure Portal 홈 페이지에서 **리소스 만들기**를 선택합니다.
2. 검색 텍스트 상자에 **Ip 그룹** 을 입력 한 다음 **ip 그룹**을 선택 합니다.
3. **만들기**를 선택합니다.
4. 구독을 선택합니다.
5. 리소스 그룹을 선택하거나 새로 만듭니다.
6. IP 그룹의 고유한 이름을 입력 한 다음 지역을 선택 합니다.

6. **다음: IP 주소**를 선택 합니다.
7. IP 주소, 여러 IP 주소 또는 IP 주소 범위를 입력 합니다.

   IP 주소를 입력 하는 방법에는 두 가지가 있습니다.
   - 수동으로 입력할 수 있습니다.
   - 파일에서 가져올 수 있습니다.

   파일에서 가져오려면 **파일에서 가져오기**를 선택 합니다. 파일을 상자로 끌거나 **파일 찾아보기**를 선택할 수 있습니다. 필요한 경우 업로드 된 IP 주소를 검토 하 고 편집할 수 있습니다.

   IP 주소를 입력 하면 포털에서 해당 IP 주소에 대 한 유효성을 검사 하 여 겹치는, 중복 및 형식 지정 문제를 확인 합니다.

5. 완료 되 면 **검토 + 만들기**를 선택 합니다.
6. **만들기**를 선택합니다.


## <a name="next-steps"></a>다음 단계

- [IP 그룹에 대 한 자세한 정보](ip-groups.md)