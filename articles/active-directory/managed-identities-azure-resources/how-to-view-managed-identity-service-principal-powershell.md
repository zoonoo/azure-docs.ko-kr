---
title: PowerShell을 사용하여 관리 ID의 서비스 주체를 보는 방법
description: PowerShell을 사용하여 관리 ID의 서비스 주체를 보기 위한 단계별 지침입니다.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/29/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5aa5ab96ae85833e59ed40161cafcc1eb88379be
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60441783"
---
# <a name="view-the-service-principal-of-a-managed-identity-using-powershell"></a>PowerShell을 사용하여 관리 ID의 서비스 주체 보기

Azure 리소스에 대한 관리 ID는 Azure Active Directory에서 자동으로 관리 ID를 Azure 서비스에 제공합니다. 이 ID를 사용하면 Azure AD 인증을 지원하는 모든 서비스에 인증할 수 있으므로 코드에 자격 증명을 포함할 필요가 없습니다. 

이 문서에서는 PowerShell을 사용하여 관리 ID의 서비스 주체를 보는 방법을 알아봅니다.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>필수 조건

- Azure 리소스에 대한 관리 ID를 잘 모르는 경우 [개요 섹션](overview.md)을 확인하세요.
- 아직 Azure 계정이 없는 경우 [체험 계정에 가입](https://azure.microsoft.com/free/)합니다.
- [가상 머신](/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#system-assigned-managed-identity) 또는 [애플리케이션에서 시스템 할당 ID](/azure/app-service/overview-managed-identity#adding-a-system-assigned-identity)를 사용하도록 설정합니다.
- [Azure PowerShell](/powershell/azure/install-az-ps) 최신 버전 설치

## <a name="view-the-service-principal"></a>서비스 주체 보기

다음 명령은 시스템 할당 ID가 사용하도록 설정된 VM 또는 애플리케이션의 서비스 주체를 보는 방법을 보여 줍니다. `<VM or application name>`을 고유한 값으로 바꿉니다.

```powershell
Get-AzADServicePrincipal -DisplayName <VM or application name>
```

## <a name="next-steps"></a>다음 단계

PowerShell을 사용하여 Azure AD 서비스 주체를 확인하는 방법에 대한 자세한 내용은 [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal)을 참조하세요.


