---
title: 액세스 검토 | Microsoft Docs
description: Azure Active Directory를 사용하여 액세스 검토
services: active-directory
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 07/16/2018
ms.author: rolyon
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3cc807a5693b363445f85d0b45a70681f58c5275
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56198631"
---
# <a name="review-your-access"></a>액세스 검토

Azure AD(Azure Active Directory)는 액세스 검토라는 기능을 사용하여 회사에서 Azure AD 및 기타 Microsoft Online Services에 속한 애플리케이션에 대한 액세스 및 그룹 구성원을 관리하는 방법을 간소화합니다. Microsoft로부터 그룹 구성원 또는 애플리케이션에 대한 액세스 권한이 있는 사용자의 액세스를 검토하도록 요청하는 전자 메일을 받았을 것입니다. 

## <a name="open-an-access-review"></a>액세스 검토 열기

보류 중인 액세스 검토를 보려면 이메일에 있는 액세스 검토 링크를 클릭합니다. 2018년 8월부터 Azure AD 역할에 대한 이메일 알림에는 업데이트된 디자인이 포함됩니다. 다음에서는 사용자가 검토자로 초대받은 경우 전송되는 예제 이메일을 보여줍니다.

![액세스 검토 이메일](./media/review-your-access/new-ar-email.png)

이 전자 메일이 없는 경우 다음 단계를 수행하여 액세스 검토를 찾을 수 있습니다.

1. [Azure AD 액세스 패널](https://myapps.microsoft.com)에 로그인합니다.

2. 페이지의 오른쪽 위 모서리에 있는 사용자 기호를 선택하면 이름과 기본 조직이 표시됩니다. 둘 이상의 조직이 나열되는 경우 액세스 검토를 요청한 조직을 선택합니다.

3. 페이지의 오른쪽에 **액세스 검토**라는 레이블이 지정된 타일이 있는 경우 이 타일을 선택합니다. 타일이 표시되지 않은 경우 해당 조직에 대해 수행할 액세스 검토가 없으므로 이 시점에서는 어떤 작업도 필요하지 않습니다.

## <a name="fill-out-an-access-review"></a>액세스 검토 작성

목록에서 액세스 검토를 선택하면 액세스 권한이 표시됩니다. 행을 클릭하고 지속적인 액세스에 대한 요구 사항을 승인할지 또는 거부할지를 선택합니다.

검토자는 지속적인 액세스를 승인하기 위한 근거를 제공하도록 요구할 수 있습니다.

## <a name="next-steps"></a>다음 단계

거부된 액세스가 즉시 제거되지는 않습니다. 응답을 변경하고 승인하려면 응답을 다시 설정하고 새 응답을 선택합니다. 액세스 검토가 완료될 때까지 이 작업을 수행할 수 있습니다.






