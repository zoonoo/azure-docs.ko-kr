---
title: 'Azure Active Directory Domain Services: Azure 가상 네트워크에 대한 DNS 설정 업데이트 | Microsoft Docs'
description: Azure Active Directory Domain Services 시작
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: d4f3e82c-6807-4690-b298-4eabad2b7927
ms.service: active-directory
ms.component: domains
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/30/2018
ms.author: maheshu
ms.openlocfilehash: b4f76602d469766f75e7a3948ae1aed290bffb4f
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34589001"
---
# <a name="enable-azure-active-directory-domain-services"></a>Azure Active Directory Domain Services 활성화

## <a name="task-4-update-dns-settings-for-the-azure-virtual-network"></a>작업 4: Azure 가상 네트워크에 대한 DNS 설정 업데이트
이전 구성 작업에서 디렉터리에 Azure Active Directory Domain Services를 사용하도록 성공적으로 설정했습니다. 다음으로 가상 네트워크 내의 컴퓨터가 이러한 서비스에 연결되고 해당 서비스를 사용하도록 설정합니다. 이 문서에서는 가상 네트워크에서 Azure Active Directory Domain Services를 사용할 수 있는 두 개의 IP 주소를 가리키도록 가상 네트워크에 대한 DNS 서버 설정을 업데이트합니다.

Azure Active Directory Domain Services를 사용하도록 설정한 가상 네트워크에 대한 DNS 서버 설정을 업데이트하려면 다음 단계를 완료합니다.


1. **개요** 탭에는 관리되는 도메인을 완전히 프로비전한 후 수행할 일련의 **필수 구성 단계**가 나열되어 있습니다. 첫 번째 구성 단계는 **가상 네트워크를 위한 DNS 서버 설정 업데이트**입니다.

    ![도메인 서비스 - 개요 탭](./media/getting-started/domain-services-provisioned-overview.png)

    > [!TIP]
    > 이 구성 단계가 표시되지 않나요? 가상 네트워크에 대한 DNS 서버 설정이 최신 상태인 경우 개요 탭에 '가상 네트워크에 대한 DNS 서버 설정 업데이트' 타일이 표시되지 않습니다.
    >
    >

2. **구성** 단추를 클릭하여 가상 네트워크에 대한 DNS 서버 설정을 업데이트합니다.

> [!NOTE]
> 다시 시작하고 나면 네트워크 상의 가상 머신만 새 DNS 설정을 얻습니다. 업데이트된 DNS 설정을 즉시 얻어야 하는 경우 포털, PowerShell 또는 CLI로 다시 시작을 트리거합니다.
>
>

## <a name="next-step"></a>다음 단계
[작업 5: Azure Active Directory Domain Services에 암호 해시 동기화 사용](active-directory-ds-getting-started-password-sync.md)
