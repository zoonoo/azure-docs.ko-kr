---
title: 자격 증명 모음을 다른 지역으로 이동 하는 Azure Key Vault | Microsoft Docs
description: 키 자격 증명 모음을 다른 지역으로 이동 하는 방법에 대 한 지침입니다.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 04/24/2020
ms.author: sudbalas
Customer intent: As a key vault administrator, I want to move my vault to another region.
ms.openlocfilehash: e65a723ac9daafdc09896a50e197034104408df2
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82254149"
---
# <a name="moving-an-azure-key-vault-across-regions"></a>지역 간에 Azure Key Vault 이동

## <a name="overview"></a>개요

Key Vault는 키 자격 증명 모음을 다른 지역으로 이동할 수 있도록 하는 리소스 이동 작업을 지원 하지 않습니다. 이 문서에서는 주요 자격 증명 모음을 다른 지역으로 이동 해야 하는 경우 해결 방법을 설명 합니다. 각 옵션에는 제한이 있으며 프로덕션 환경에서이를 시도 하기 전에 이러한 해결 방법의 영향을 이해 하는 것이 중요 합니다.

키 자격 증명 모음을 다른 지역으로 이동 해야 하는 경우 솔루션은 원하는 지역에 새 키 자격 증명 모음을 만들고 기존 주요 자격 증명 모음에서 새 키 자격 증명 모음으로 각 개인 암호를 수동으로 복사 하는 것입니다. 이 작업은 아래에 나열 된 다음 방법 중 하나로 수행할 수 있습니다.

## <a name="design-considerations"></a>디자인 고려 사항

* Key Vault 이름은 전역적으로 고유 합니다. 동일한 자격 증명 모음 이름을 다시 사용할 수 없습니다.

* 새 키 자격 증명 모음에서 액세스 정책 및 네트워크 구성 설정을 다시 구성 해야 합니다.

* 새 key vault에서 일시 삭제 및 보호 제거를 다시 구성 해야 합니다.

* 백업 및 복원 작업은 이러한 설정을 다시 구성 해야 할 수 있는 autorotation 설정을 유지 하지 않습니다.

## <a name="option-1---use-the-key-vault-backup-and-restore-commands"></a>옵션 1-key vault 백업 및 복원 명령을 사용 합니다.

Backup 명령을 사용 하 여 자격 증명 모음에서 각 개인 암호, 키 및 인증서를 백업할 수 있습니다. 비밀은 암호화 된 blob으로 다운로드 됩니다. 그런 다음 blob을 새 주요 자격 증명 모음으로 복원할 수 있습니다. 명령은 아래 링크에 설명 되어 있습니다.

[Azure Key Vault 명령](https://docs.microsoft.com/powershell/module/azurerm.keyvault/?view=azurermps-6.13.0#key_vault)

### <a name="limitations"></a>제한 사항

* 한 지리에서 키 자격 증명 모음을 백업 하 고 다른 지리에 복원할 수는 없습니다. Azure 지역에 대해 자세히 알아보세요. [링크](https://azure.microsoft.com/global-infrastructure/geographies/)

* Backup 명령은 각 암호의 모든 버전을 백업 합니다. 이전 버전 수가 10 개 이상인 비밀이 있는 경우 요청이 허용 되는 최대 요청 크기를 초과 하 게 되 고 작업이 실패할 수 있습니다.

## <a name="option-2---manually-download-and-upload-secrets"></a>옵션 2-암호를 수동으로 다운로드 및 업로드

특정 암호 유형은 수동으로 다운로드할 수 있습니다. 예를 들어 인증서를 .pfx 파일로 다운로드할 수 있습니다. 이 옵션은 인증서와 같은 일부 비밀 유형에 대 한 지리적 제한을 제거 합니다. 모든 지역의 모든 주요 자격 증명 모음에 .pfx 파일을 업로드할 수 있습니다. 비밀은 암호로 보호 되지 않는 형식으로 다운로드 됩니다. 이동 하는 동안 Key Vault 된 후에 비밀의 보안을 유지 하는 책임이 있습니다.
