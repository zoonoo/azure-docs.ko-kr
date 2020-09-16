---
title: 포함 파일
description: 포함 파일
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/27/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 814bf085ca910ef07a13df779eef25e0e6510e57
ms.sourcegitcommit: 6e1124fc25c3ddb3053b482b0ed33900f46464b3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/15/2020
ms.locfileid: "90563454"
---
Azure Files에 저장된 모든 데이터는 Azure SSE(스토리지 서비스 암호화)를 사용하여 저장 데이터로 암호화됩니다. 스토리지 서비스 암호화는 Windows의 BitLocker와 유사하게 작동하며, 데이터는 파일 시스템 수준 아래에서 암호화됩니다. Azure 파일 공유의 파일 시스템 아래에서 데이터가 암호화되어 디스크로 인코딩되므로 Azure 파일 공유를 읽거나 쓰기 위해 클라이언트의 기본 키에 액세스할 필요가 없습니다. 저장 데이터 암호화는 SMB 및 NFS 프로토콜 모두에 적용됩니다.

기본적으로 Azure Files에 저장된 데이터는 Microsoft 관리형 키로 암호화됩니다. Microsoft는 Microsoft 관리형 키를 사용하여 데이터를 암호화/암호 해독하는 키를 보유하고 있으며 정기적으로 이를 순환시킬 책임이 있습니다. 또한 순환 프로세스를 제어할 수 있는 자체 키를 관리하도록 선택할 수도 있습니다. 고객 관리형 키로 파일 공유를 암호화하도록 선택한 경우, 클라이언트의 읽기 및 쓰기 요청을 수행하기 위해 키에 액세스할 수 있는 권한이 Azure Files에게 부여됩니다. 고객 관리형 키를 사용하여 언제든지 이 권한 부여를 취소할 수 있지만, 그럴 경우 SMB 또는 FileREST API를 통해 더 이상 Azure 파일 공유에 액세스할 수 없습니다.

Azure Files는 Azure Blob 스토리지와 같은 다른 Azure 스토리지 서비스와 동일한 암호화 체계를 사용합니다. Azure SSE(스토리지 서비스 암호화)에 대한 자세한 내용은 [미사용 데이터에 대한 Azure 스토리지 암호화](../articles/storage/common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)를 참조하세요.