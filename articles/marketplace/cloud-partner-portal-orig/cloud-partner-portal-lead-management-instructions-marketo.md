---
title: Marketo | Microsoft Docs
description: Marketo의 잠재 고객 관리를 구성하는 방법을 설명합니다.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: pbutlerm
ms.openlocfilehash: 482d7a9662a79503bb2b197d5a6c63c9fa3c1c96
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60398485"
---
# <a name="configure-lead-management-in-marketo"></a>Marketo의 잠재 고객 관리 구성

이 문서에서는 Microsoft 판매 잠재 고객을 처리하도록 Marketo를 설정하는 방법을 설명합니다.

1. Marketo에 로그인합니다.
2. **Design Studio**를 선택합니다.
    ![Marketo Design Studio](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo1.png)

3.  **새 양식**을 선택합니다.
    ![Marketo 새 양식](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo2.png)

4.  새 양식에서 필수 필드에 정보를 입력한 다음 **만들기**를 선택합니다.
    ![Marketo 새 양식 만들기](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo3.png)

4.  필드 세부 정보에서 **마침**을 선택합니다.
    ![Marketo 양식 마침](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo4.png)

5.  승인하고 닫습니다.

6.  MarketplaceLeadBacked 탭에서 **embed 태그**를 선택합니다.
    ![Marketo embed 태그 옵션](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo5.png)

7.  Marketo embed 태그에 다음 예제와 같은 코드가 표시됩니다.

`<script src="//app-ys12.marketo.com/js/forms2/js/forms2.min.js"></script>`

    <form id="mktoForm_1179"></form>
    <script>MktoForms2.loadForm("("//app-ys12.marketo.com", "123-PQR-789", 1179);</script>

1. 클라우드 파트너 포털의 Marketo 필드에서 **서버 ID**, **Munchkin ID** 및 **양식 ID**를 구성할 수 있도록 embed 태그에 표시된 값을 복사합니다.

다음 예제를 참조하여 Marketo embed 태그 예제에서 필요한 ID를 가져옵니다.

- 서버 ID = **ys12**
- Munchkin ID = **123-PQR-789**
- 양식 ID = **1179**\
