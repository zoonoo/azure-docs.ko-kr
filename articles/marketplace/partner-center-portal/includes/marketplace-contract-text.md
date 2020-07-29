---
title: 포함 파일
description: Microsoft 상업적 marketplace 표준 계약에 대 한 텍스트 파일 포함
documentationcenter: partner-center-commercial-marketplace
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: include
ms.date: 01/22/2020
ms.custom: include file
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: d899db8b5f6f3b7c0ccef4e44c814f1e126c626a
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87326677"
---
Microsoft는 상업적 marketplace에 대 한 표준 계약 템플릿을 제공 합니다.

- **Microsoft 상업용 Marketplace 표준 계약을 사용합니까?**

고객에게는 구매 프로세스를 간소화하고 소프트웨어 공급업체에게는 법적 복잡성을 줄이기 위해 Microsoft는 마켓플레이스에서 트랜잭션을 용이하게 하는 Microsoft 상업용 Marketplace 표준 계약을 제공합니다. 상업용 Marketplace 게시자는 사용자 지정 약관을 만드는 대신 표준 계약에 따라 소프트웨어를 제공하도록 선택할 수 있으며, 고객은 이를 한 번만 심사하고 수락하면 됩니다. 표준 계약은에서 찾을 수 https://go.microsoft.com/fwlink/?linkid=2041178 있습니다.

"상업용 Marketplace 표준 계약 사용" 확인란을 선택하여 사용자 지정 약관을 제공하는 대신 표준 계약을 사용하도록 선택할 수 있습니다.

![표준 계약 사용 확인란](./media/use-standard-contract.png)

> [!NOTE]
> Microsoft 상업용 Marketplace 표준 계약을 사용하여 제품을 게시한 후에는 사용자 지정 약관을 사용할 수 없습니다. 이는 "또는" 시나리오입니다. 표준 계약 **또는** 게시자 고유 사용 약관에 따라 솔루션을 제공합니다. 표준 계약의 조건을 수정하려면 표준 계약 수정을 통해 이 작업을 수행할 수 있습니다.

**표준 계약 수정**

게시자는 표준 계약 수정을 통해 간소화를 위해 표준 계약 조건을 선택하면서 제품 또는 비즈니스에 대한 조건을 사용자 지정할 수 있습니다. 고객은 Microsoft 표준 계약을 이미 검토하고 수락한 경우 계약 수정 내용만 검토하면 됩니다.

상업용 마켓플레이스 게시자가 사용할 수 있는 수정은 두 가지 종류가 있습니다.

- 범용 수정: 이러한 수정은 모든 고객에 대한 표준 계약에 전체적으로 적용됩니다. 범용 수정은 구매 흐름에서 제품의 모든 고객에게 표시됩니다. 고객은 제품을 사용하기 전에 표준 계약 및 수정 내용에 동의해야 합니다.
- 사용자 지정 수정: 이러한 수정은 Azure 테넌트 ID를 통해 특정 고객만을 대상으로 하는 표준 계약 특별 수정입니다. 게시자는 대상으로 지정할 테넌트를 선택할 수 있습니다. 테넌트의 고객만 제품의 구매 흐름에 사용자 지정 수정 조건이 표시됩니다.  고객은 제품을 사용하기 전에 표준 계약 및 수정 내용에 동의해야 합니다.

>[!NOTE]
> 이러한 두 가지 유형의 수정은 누적됩니다. 사용자 지정 수정을 대상으로 하는 고객은 구매 중 표준 계약에 대한 범용 수정도 적용받게 됩니다.

**Microsoft의 상용 marketplace에 대 한 표준 계약에 대 한 범용 수정 용어**:이 상자에 유니버설 수정 용어를 입력 합니다. 제품당 하나의 범용 수정안을 제공할 수 있습니다. 이 상자에서는 문자를 무제한으로 입력할 수 있습니다. 이러한 조건은 검색 및 구매 흐름 중에 AppSource, Azure Marketplace 및/또는 Azure Portal의 고객에게 표시됩니다.

**Microsoft의 상용 marketplace에 대 한 표준 계약에 대 한 사용자 지정 수정 용어**: **사용자 지정 수정 용어 추가**를 선택 하 여 시작 합니다. 제품당 최대 10개의 사용자 지정 수정 조건을 제공할 수 있습니다.

- **사용자 지정 수정 용어**: 사용자 지정 수정 약관 상자에 사용자 지정 수정 용어를 입력 합니다. 이 상자에는 문자를 개수 제한 없이 입력할 수 있습니다. 이러한 사용자 지정 약관에 지정하는 테넌트 ID의 고객에게만 Azure Portal의 제품 구매 흐름에 사용자 지정 수정 조건이 표시됩니다.  
- **테 넌 트 id** (필수): 각 사용자 지정 수정은 최대 20 개의 테 넌 트 id를 대상으로 지정할 수 있습니다. 사용자 지정 수정을 추가하는 경우 하나 이상의 테넌트 ID를 제공해야 합니다. 테넌트 ID는 Azure에서 고객을 식별합니다. 게시자는 고객에게 이 ID를 요청할 수 있으며 고객은 portal.azure.com > Azure Active Directory > 속성으로 이동하여 ID를 확인할 수 있습니다. 디렉터리 ID 값은 테넌트 ID입니다(예: 50c464d3-4930-494c-963c-1e951d15360e). [내 Microsoft Azure 및 Office 365 테넌트 ID는 무엇인가요?](https://www.whatismytenantid.com)에서 도메인 이름 URL을 사용하여 고객의 조직 테넌트 ID를 조회할 수도 있습니다.
- **설명** (선택 사항): 필요에 따라 테 넌 트 ID에 대 한 친숙 한 설명을 제공 하 여, 수정으로 대상으로 하는 고객을 식별할 수 있습니다.

**사용 약관**

사용자 지정 사용 약관을 제공하려는 경우 사용 약관 필드에 입력할 수 있습니다. 이 필드에는 최대 1만 자의 텍스트를 입력할 수 있습니다. 사용 약관에 더 긴 설명이 필요한 경우 이 필드에 사용 약관을 찾을 수 있는 단일 URL 링크를 입력합니다. 이는 고객에게 활성 링크로 표시됩니다.

고객은 이러한 사용 약관에 동의해야 제품을 사용할 수 있습니다.

계속하기 전에 **초안 저장**을 선택합니다.
