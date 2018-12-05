---
title: Azure AD 액세스 검토를 사용하여 액세스 검토 시작 | Microsoft Docs
description: Azure Active Directory 액세스 검토를 사용하여 액세스 검토를 시작하는 방법을 알아봅니다.
services: active-directory
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.component: compliance
ms.date: 07/16/2018
ms.author: rolyon
ms.reviewer: mwahl
ms.openlocfilehash: 455a4922dfd0832c49729bed007dd696562be07a
ms.sourcegitcommit: fa758779501c8a11d98f8cacb15a3cc76e9d38ae
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2018
ms.locfileid: "52263429"
---
# <a name="start-an-access-review-with-azure-ad-access-reviews"></a>Azure AD 액세스 검토를 사용하여 액세스 검토 시작

Azure AD(Azure Active Directory)는 액세스 검토라는 기능을 사용하여 회사에서 Azure AD 및 기타 Microsoft Online Services에 속한 응용 프로그램에 대한 액세스 및 그룹 구성원을 관리하는 방법을 간소화합니다. Microsoft로부터 그룹 구성원 또는 응용 프로그램에 대한 액세스 권한이 있는 사용자의 액세스를 검토하도록 요청하는 전자 메일을 받았을 것입니다. 

## <a name="open-an-access-review"></a>액세스 검토 열기

보류 중인 액세스 검토를 보려면 이메일에 있는 액세스 검토 링크를 클릭합니다. 2018년 8월부터 Azure AD 역할에 대한 이메일 알림에는 업데이트된 디자인이 포함됩니다. 다음에서는 사용자가 검토자로 초대받은 경우 전송되는 예제 이메일을 보여줍니다. 

![액세스 검토 이메일](./media/perform-access-review/new-ar-email.png)

이 전자 메일이 없는 경우 다음 단계를 수행하여 액세스 검토를 찾을 수 있습니다.

1. [Azure AD 액세스 패널](https://myapps.microsoft.com)에 로그인합니다.

2. 페이지의 오른쪽 위 모서리에 있는 사용자 기호를 선택하면 이름과 기본 조직이 표시됩니다. 둘 이상의 조직이 나열되는 경우 액세스 검토를 요청한 조직을 선택합니다.

3. 페이지의 오른쪽에 **액세스 검토**라는 레이블이 지정된 타일이 있는 경우 이 타일을 선택합니다. 타일이 표시되지 않은 경우 해당 조직에 대해 수행할 액세스 검토가 없으므로 이 시점에서는 어떤 작업도 필요하지 않습니다.

## <a name="fill-out-an-access-review"></a>액세스 검토 작성

목록에서 액세스 검토를 선택하면 검토해야 하는 사용자의 이름이 표시됩니다. 요청이 자신의 액세스를 검토하는 요청인 경우 하나의 이름, 즉 사용자 자신의 이름만 볼 수 있습니다.

목록의 각 행에 대해 사용자의 액세스를 승인할지 또는 거부할지를 결정할 수 있습니다. 행을 선택하고 승인할지 또는 거부할지를 선택합니다. (사용자를 모르는 경우에도 지정할 수 있습니다.)

검토자는 지속적인 액세스 또는 그룹 구성원 자격을 승인하기 위한 근거를 제공하도록 요구할 수 있습니다.

## <a name="next-steps"></a>다음 단계

사용자의 거부된 액세스가 즉시 제거되지는 않습니다. 검토가 완료되거나 관리자가 검토를 중지하면 제거될 수 있습니다. 응답을 변경하고 이전에 거부된 사용자를 승인하거나 이전에 승인된 사용자를 거부하려면 해당 행을 클릭하고 응답을 다시 설정하고 새 응답을 선택합니다. 액세스 검토가 완료될 때까지 이 작업을 수행할 수 있습니다.



