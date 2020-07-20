---
title: Azure Key Vault 백업 | Microsoft Docs
description: 이 문서를 사용하면 Azure Key Vault에 저장된 비밀, 키 또는 인증서를 백업할 수 있습니다.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: sudbalas
ms.openlocfilehash: 8a152e2771f0b207e81f42c6ecae2e4d14605051
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86156357"
---
# <a name="azure-key-vault-backup"></a>Azure Key Vault 백업

이 문서에서는 주요 자격 증명 모음에 저장된 개별 비밀, 키 및 인증서의 백업을 수행하는 방법을 보여줍니다. 이 백업은 주요 자격 증명 모음에 액세스할 수 없는 경우에 모든 비밀의 오프라인 복사본을 제공하기 위한 것입니다.

## <a name="overview"></a>개요

Key Vault는 가용성을 유지하고 데이터 손실을 방지하는 여러 기능을 자동으로 제공합니다. 이 백업은 비밀 백업을 유지 관리하는 데 중요한 비즈니스 근거가 있는 경우에만 시도해야 합니다. 키 자격 증명 모음에서 비밀을 백업하면 비밀이 만료되거나 순환될 때 여러 로그, 권한 및 백업 세트를 유지 관리하는 등의 추가 운영 과제가 발생할 수 있습니다.

Key Vault는 재해 시나리오에서 가용성을 유지하고 사용자의 개입 없이도 쌍으로 연결된 지역으로 요청을 자동으로 장애 조치(failover)합니다. 자세한 내용은 다음 링크를 참조하세요. [Azure Key Vault 재해 복구](https://docs.microsoft.com/azure/key-vault/general/disaster-recovery-guidance)

Key Vault는 일시 삭제 및 제거 보호를 통해 비밀을 실수로 또는 악의적으로 삭제하지 않도록 보호합니다. 비밀을 실수로 또는 악의적으로 삭제하지 않도록 보호하려면 키 자격 증명 모음에서 일시 삭제 및 제거 보호 기능을 구성하세요. 자세한 내용은 다음 문서를 참조하세요. [Azure Key Vault 복구](https://docs.microsoft.com/azure/key-vault/general/overview-soft-delete)

## <a name="limitations"></a>제한 사항

Azure Key Vault는 현재 단일 작업으로 전체 키 자격 증명 모음을 백업하는 방법을 지원하지 않습니다. 이 문서에 나열된 명령을 사용하여 키 자격 증명 모음의 자동 백업을 수행하려는 시도는 Microsoft 또는 Azure Key Vault 팀에서 지원되지 않습니다.

사용자 지정 자동화를 만들기 위해 아래 문서에 표시된 명령을 사용하려고 하면 오류가 발생할 수 있습니다.

* 여러 버전의 비밀을 백업하면 시간 초과 오류가 발생할 수 있습니다.
* 백업은 지정 시간 스냅샷을 만듭니다. 백업 중에 비밀이 갱신되어 암호화 키가 일치하지 않을 수 있습니다.
* 초당 요청에 대한 키 자격 증명 모음 서비스 제한을 초과하면 키 자격 증명 모음이 제한되고 백업이 실패합니다.

## <a name="design-considerations"></a>디자인 고려 사항

키 자격 증명 모음(비밀, 키 또는 인증서)에 저장된 개체를 백업하면 백업 작업에서 해당 개체를 암호화된 Blob으로 다운로드합니다. 이 Blob은 Azure 외부에서 암호를 해독할 수 없습니다. 이 Blob에서 사용 가능한 데이터를 가져오려면 동일한 Azure 구독 및 Azure 지역 내의 키 자격 증명 모음으로 Blob을 복원해야 합니다.
[Azure 지역](https://azure.microsoft.com/global-infrastructure/geographies/)

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독에 대한 기여자 수준 이상의 권한
* 백업하려는 비밀이 포함된 기본 키 자격 증명 모음
* 비밀이 복원되는 보조 키 자격 증명 모음입니다.

## <a name="back-up-and-restore-with-azure-portal"></a>Azure Portal을 사용하여 백업 및 복원

### <a name="back-up"></a>백업

1. Azure Portal로 이동합니다.
2. 키 자격 증명 모음을 선택합니다.
3. 백업하려는 개체(비밀, 키 또는 인증서)로 이동합니다.

    ![이미지](../media/backup-1.png)

4. 개체를 선택합니다.
5. '백업 다운로드'를 선택합니다.

    ![이미지](../media/backup-2.png)
    
6. '다운로드' 단추를 클릭합니다.

    ![이미지](../media/backup-3.png)
    
7. 암호화된 Blob을 안전한 위치에 저장합니다.

### <a name="restore"></a>복원

1. Azure Portal로 이동합니다.
2. 키 자격 증명 모음을 선택합니다.
3. 복원하려는 개체 유형(비밀, 키 또는 인증서)으로 이동합니다.
4. '백업 복원' 선택

    ![이미지](../media/backup-4.png)
    
5. 암호화된 Blob을 저장한 위치로 이동합니다.
6. "확인"을 선택합니다.

## <a name="back-up-and-restore-with-the-azure-cli"></a>Azure CLI를 사용하여 백업 및 복원

```azurecli
## Login To Azure
az login

## Set your Subscription
az account set --subscription {AZURE SUBSCRIPTION ID}

## Register Key Vault as a Provider
az provider register -n Microsoft.KeyVault

## Backup a Certificate in Key Vault
az keyvault certificate backup --file {File Path} --name {Certificate Name} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Backup a Key in Key Vault
az keyvault key backup --file {File Path} --name {Key Name} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Backup a Secret in Key Vault
az keyvault secret backup --file {File Path} --name {Secret Name} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Restore a Certificate in Key Vault
az keyvault certificate restore --file {File Path} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Restore a Key in Key Vault
az keyvault key restore --file {File Path} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Restore a Secret in Key Vault
az keyvault secret restore --file {File Path} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

```

## <a name="next-steps"></a>다음 단계

Azure Key Vault에 대한 로깅 및 모니터링을 설정합니다. [Azure Key Vault 로깅](https://docs.microsoft.com/azure/key-vault/general/logging)
