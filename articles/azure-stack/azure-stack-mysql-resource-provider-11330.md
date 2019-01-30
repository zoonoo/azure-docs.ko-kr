---
title: Azure Stack MySQL 리소스 공급자 1.1.30.0 릴리스 정보 | Microsoft Docs
description: 알려진된 문제를 포함 하 여 최신 Azure Stack MySQL 리소스 공급자 업데이트에 포함 된 내용 및 다운로드 위치를 알아봅니다.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2019
ms.author: jeffgilb
ms.reviewer: jiahan
ms.lastreviewed: 01/09/2019
ms.openlocfilehash: 7f44e8c2c4587ecfdb3bd5eb4304789674da96f3
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55252016"
---
# <a name="mysql-resource-provider-11330--release-notes"></a>MySQL 리소스 공급자 1.1.33.0 릴리스 정보

*적용 대상: Azure Stack 통합 시스템 및 Azure Stack 개발 키트*

이러한 릴리스 향상 된 기능 및 MySQL 리소스 공급자 버전 1.1.33.0의에서 알려진된 문제에 설명 합니다.

## <a name="build-reference"></a>빌드 참조
이진 MySQL 리소스 공급자를 다운로드 하 고 임시 디렉터리에 콘텐츠를 추출할 자동 압축 풀기 프로그램을 실행 합니다. 리소스 공급자에는 빌드를 최소 해당 Azure Stack에 있습니다. 이 버전의 MySQL 리소스 공급자를 설치 하는 데 필요한 최소 Azure Stack 릴리스 버전은 다음과 같습니다.

> |Azure Stack의 최소 버전|MySQL 리소스 공급자 버전|
> |-----|-----|
> |버전 1808 (1.1808.0.97)|[MySQL 버전 1.1.33.0 RP](https://aka.ms/azurestackmysqlrp11330)|  
> |     |     |

> [!IMPORTANT]
> Azure Stack 통합 시스템에 최소 지원 되는 Azure Stack 업데이트 적용 또는 MySQL 리소스 공급자의 최신 버전을 배포 하기 전에 최신 Azure Stack 개발 키트 ASDK ()를 배포 합니다.

## <a name="new-features-and-fixes"></a>새 기능 및 수정
이 버전의 Azure Stack MySQL 리소스 공급자는 다음과 같은 향상 된 기능 및 수정 합니다.

### <a name="fixes"></a>수정 프로그램
- **MySQL 리소스 공급자에 대 한 포털 확장 잘못 된 구독을 선택할 수 있습니다**합니다. MySQL 리소스 공급자는 다르게를 사용 하려면 첫 번째 서비스 관리자 구독을 확인 하려면 Azure Resource Manager 호출을 사용 합니다 *기본 공급자 구독*합니다. 이 경우 MySQL 리소스 공급자를 정상적으로 작동 하지 않습니다. 

- **MySQL 호스팅 서버에 나타나지 않으면 데이터베이스를 호스트 합니다.** MySQL 호스팅 서버에 대 한 테 넌 트 리소스를 볼 때 사용자가 만든 데이터베이스를 나열 될 수 있습니다.

- **TLS 1.2를 사용 하지 않는 경우 이전 MySQL 리소스 공급자 (1.1.30.0) 배포가 실패할 수 있습니다**합니다. 리소스 공급자를 업데이트 하거나 암호를 회전 하면 리소스 공급자를 배포 하는 경우 TLS 1.2를 사용 하도록 설정 하려면 MySQL 리소스 공급자 1.1.33.0를 업데이트 합니다. 

- **MySQL 리소스 공급자에 대 한 비밀 회전 실패**합니다. 암호를 회전 하는 경우 다음 오류 코드 생성 문제 해결된: ` New-AzureRmResourceGroupDeployment - Error: Code=InvalidDeploymentParameterValue; Message=The value of deployment parameter 'StorageAccountBlobUri' is null.`

## <a name="known-issues"></a>알려진 문제 

- **MySQL Sku은 포털에 표시 되도록 최대 1 시간까지 걸릴 수 있습니다**합니다. 까지 걸릴 수 있으므로 한 시간에 새로 만든된 Sku 새 MySQL 데이터베이스를 만들 때 사용 하 여 볼 수 있도록 합니다. 

    **해결 방법**: 없음.

- **MySQL 로그인을 다시**입니다. 새 MySQL을 만들려고 시도 동일한 구독에서 기존 로그인으로 동일한 사용자 이름 사용 하 여 로그인 하면 동일한 로그인 및 기존 암호를 다시 사용 합니다. 

    **해결 방법**: 동일한 구독에서 새 로그인을 만들 때 다른 사용자를 사용 하거나 다른 구독에서 동일한 사용자 이름을 사용 하 여 로그인을 만듭니다.

- **공유 MySQL 로그인 데이터 불일치가 발생할**합니다. MySQL 로그인 동일한 구독에서 여러 MySQL 데이터베이스에 대 한 공유 인 경우 데이터 불일치가 발생할 수 있는 로그인 암호를 변경 합니다.

    **해결 방법**: 항상 동일한 구독에서 다른 데이터베이스에 대 한 다른 로그인을 사용 합니다.


### <a name="known-issues-for-cloud-admins-operating-azure-stack"></a>Azure Stack을 운영 하는 클라우드 관리자에 대 한 알려진된 문제
설명서를 참조 합니다 [Azure Stack 릴리스](azure-stack-servicing-policy.md)합니다.

## <a name="next-steps"></a>다음 단계
[MySQL 리소스 공급자에 자세히 알아보려면](azure-stack-mysql-resource-provider.md)합니다.

[MySQL 리소스 공급자 배포 준비](azure-stack-mysql-resource-provider-deploy.md#prerequisites)합니다.

[MySQL 리소스 공급자는 이전 버전에서 업그레이드](azure-stack-mysql-resource-provider-update.md)합니다. 