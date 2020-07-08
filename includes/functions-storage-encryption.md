---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/06/2020
ms.author: glenga
ms.openlocfilehash: 0bc5977a581006dc760c0435f9d68371ced7e4cd
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83648792"
---
Azure Storage는 미사용 스토리지 계정의 모든 데이터를 암호화합니다. 자세한 내용은 [미사용 데이터에 대한 Azure Storage 암호화](../articles/storage/common/storage-service-encryption.md)를 참조하세요.

기본적으로 데이터는 Microsoft 관리형 키로 암호화됩니다. 암호화 키에 대한 추가 제어를 위해 Blob 및 파일 데이터의 암호화에 사용할 고객 관리 키를 제공할 수 있습니다. 스토리지 계정에 액세스하려면 이러한 키가 함수에 대한 Azure Key Vault에 있어야 합니다. 자세한 내용은 [고객 관리형 키를 사용하여 미사용 암호화](../articles/azure-functions/configure-encrypt-at-rest-using-cmk.md)를 참조하세요.  