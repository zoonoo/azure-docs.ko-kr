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
ms.openlocfilehash: ed061234da27716bfcdaf1cc065568908108f90a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77597839"
---
Azure 파일에 저장된 모든 데이터는 AZURE 저장소 서비스 암호화(SSE)를 사용하여 미사용 으로 암호화됩니다. 스토리지 서비스 암호화는 Windows의 BitLocker와 유사하게 작동합니다: 데이터는 파일 시스템 수준 아래에 암호화됩니다. 데이터는 Azure 파일 공유의 파일 시스템 아래에 암호화되므로 디스크에 인코딩되므로 Azure 파일 공유를 읽거나 쓰기 위해 클라이언트의 기본 키에 액세스할 필요가 없습니다.

기본적으로 Azure 파일에 저장된 데이터는 Microsoft 관리 키로 암호화됩니다. Microsoft에서 관리하는 키를 사용하면 데이터를 암호화/해독할 수 있는 키를 보유하며 정기적으로 데이터를 회전할 책임이 있습니다. 또한 회전 프로세스를 제어할 수 있는 고유한 키관리를 선택할 수도 있습니다. 고객 관리 키로 파일 공유를 암호화하도록 선택한 경우 Azure Files는 클라이언트의 읽기 및 쓰기 요청을 이행하기 위해 키에 액세스할 수 있는 권한이 부여됩니다. 고객 관리 키를 사용하면 언제든지 이 권한 부여를 취소할 수 있지만 이는 SMB 또는 FileREST API를 통해 Azure 파일 공유에 더 이상 액세스할 수 없음을 의미합니다.

Azure 파일은 Azure Blob 저장소와 같은 다른 Azure 저장소 서비스와 동일한 암호화 체계를 사용합니다. Azure 저장소 서비스 암호화(SSE)에 대한 자세한 내용은 [미사용 데이터에 대한 Azure 저장소 암호화를](../articles/storage/common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)참조하십시오.