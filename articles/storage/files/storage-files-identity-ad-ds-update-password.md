---
title: AD DS storage 계정 암호 업데이트
description: 저장소 계정을 나타내는 Active Directory Domain Services 계정의 암호를 업데이트 하는 방법에 대해 알아봅니다. 이렇게 하면 암호가 만료 되 면 저장소 계정이 정리 되지 않으므로 인증 실패를 방지할 수 있습니다.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: how-to
ms.date: 06/22/2020
ms.author: rogarana
ms.openlocfilehash: d16f11a85c6b370b0187975cce965bf3e1b5ba3f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "85510257"
---
# <a name="update-the-password-of-your-storage-account-identity-in-ad-ds"></a>AD DS에서 저장소 계정 id의 암호를 업데이트 합니다.

암호 만료 시간을 적용 하는 조직 구성 단위나 도메인의 저장소 계정을 나타내는 Active Directory Domain Services (AD DS) id/계정을 등록 한 경우 최대 암호 사용 기간 전에 암호를 변경 해야 합니다. 조직에서 암호가 만료 되 면 계정을 삭제 하는 자동화 된 정리 스크립트를 실행할 수 있습니다. 따라서 만료 되기 전에 암호를 변경 하지 않으면 계정이 삭제 되어 Azure 파일 공유에 대 한 액세스 권한을 잃게 됩니다.

암호 회전을 트리거하려면 `Update-AzStorageAccountADObjectPassword` [AzFilesHybrid 모듈](https://github.com/Azure-Samples/azure-files-samples/releases)에서 명령을 실행할 수 있습니다. 이 명령은 저장소 계정에 대 한 소유자 권한이 있는 하이브리드 사용자를 사용 하 여 온-프레미스 AD DS 가입 환경에서 실행 해야 하며, 저장소 계정을 나타내는 id의 암호를 변경할 수 있는 권한을 AD DS 합니다. 명령은 저장소 계정 키 회전과 유사한 동작을 수행 합니다. 특히, 저장소 계정의 두 번째 Kerberos 키를 가져오고이 키를 사용 하 여 AD DS에서 등록 된 계정의 암호를 업데이트 합니다. 그런 다음 저장소 계정의 대상 Kerberos 키를 다시 생성 하 고 AD DS에서 등록 된 계정의 암호를 업데이트 합니다. 온-프레미스 AD DS 가입 환경에서이 명령을 실행 해야 합니다.

```PowerShell
# Update the password of the AD DS account registered for the storage account
# You may use either kerb1 or kerb2
Update-AzStorageAccountADObjectPassword `
        -RotateToKerbKey kerb2 `
        -ResourceGroupName "<your-resource-group-name-here>" `
        -StorageAccountName "<your-storage-account-name-here>"
```
