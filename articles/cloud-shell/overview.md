---
title: "Azure Cloud Shell(미리 보기) 개요 | Microsoft Docs"
description: "Azure Cloud Shell의 개요입니다."
services: 
documentationcenter: 
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: 
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: juluk
ms.translationtype: Human Translation
ms.sourcegitcommit: e7da3c6d4cfad588e8cc6850143112989ff3e481
ms.openlocfilehash: 63f1c468b5f8f4b0bb298cb67adea8c01b065427
ms.contentlocale: ko-kr
ms.lasthandoff: 05/16/2017

---
# <a name="overview-of-azure-cloud-shell-preview"></a>Azure Cloud Shell(미리 보기) 개요
Azure Cloud Shell은 Azure 리소스를 관리하기 위한 브라우저에서 액세스할 수 있는 대화형 셸입니다.

![](media/startup.gif)

## <a name="features"></a>기능
### <a name="browser-based-shell-experience"></a>브라우저 기반 셸 환경
Cloud Shell은 Azure 관리 작업을 사용하여 빌드된 브라우저 기반 명령줄 환경에 액세스할 수 있습니다. Cloud Shell을 활용하여 클라우드가 제공할 수 있는 방식으로 로컬 컴퓨터에서 제한되지 않고 작업할 수 있습니다.

### <a name="pre-configured-azure-workstation"></a>Azure 미리 구성된 워크스테이션
사용자가 더 빠르게 작업할 수 있도록 Cloud Shell은 널리 사용되는 명령줄 도구 및 언어 지원이 미리 설치되어 제공됩니다.

[여기에서 Azure Cloud Shell의 전체 도구 목록을 봅니다.](features.md#tools)

### <a name="automatic-authentication"></a>자동 인증
Cloud Shell은 Azure CLI 2.0을 통해 리소스에 즉시 액세스하도록 각 세션에서 자동으로 안전하게 인증합니다.

### <a name="connect-your-azure-file-storage"></a>Azure File Storage 연결
Cloud Shell 컴퓨터는 임시이며 결과적으로 Azure 파일 공유를 `clouddrive`로 탑재하도록 하여 $Home 디렉터리를 유지합니다.
Cloud Shell를 첫 번째로 시작할 때 리소스 그룹, 저장소 계정 및 파일 공유를 만들라는 메시지가 자동으로 표시됩니다. 이는 일회성 단계이며 모든 세션에서 자동으로 연결됩니다. 

![](media/storage-prompt.png)

Azure 파일 공유를 사용하여 기본 5GB 디스크 이미지를 포함하는 LRS 저장소 계정이 자동으로 만들어집니다. 파일 공유는 $Home 디렉터리를 동기화하고 유지하는 데 사용되는 디스크 이미지와의 파일 공유 상호 작용을 위해 `clouddrive`로 탑재합니다. 일반 저장소 비용이 적용됩니다.

세 가지 리소스가 자동으로 만들어집니다.
1. 리소스 그룹: `cloud-shell-storage-<region>`
2. 저장소 계정: `cs-uniqueGuid`
3. 파일 공유: `cs-<user>-<domain>-com-uniqueGuid`

> [!Note]
> SSH 키와 같이 $Home 디렉터리의 모든 파일은 탑재된 파일 공유에 저장된 사용자 디스크 이미지에서 유지됩니다. $Home 디렉터리 및 탑재된 파일 공유에서 파일을 저장하는 경우 모범 사례를 적용합니다.

[Cloud Shell 저장소, 파일 공유 업데이트 및 파일 업로드/다운로드에 대해 자세히 알아봅니다.](persisting-shell-storage.md)

## <a name="concepts"></a>개념
* Cloud Shell은 세션 별, 사용자 단위 기준으로 제공된 임시 컴퓨터에서 실행됩니다.
* Cloud Shell은 대화형 작업 없이 10분 후에 시간이 초과됩니다.
* Cloud Shell은 첨부된 파일 공유를 사용하여 액세스할 수 있습니다.
* Cloud Shell은 사용자 계정 별로 하나의 컴퓨터를 할당합니다.
* 사용 권한은 일반적인 Linux 사용자로 설정됩니다.

[Cloud Shell의 모든 기능에 대해 자세히 알아봅니다.](features.md)

## <a name="examples"></a>예
* 브라우저에서 Azure 리소스를 관리하는 스크립트를 만들고 편집합니다.
* Azure Portal 및 Azure CLI 2.0을 통해 리소스를 동시에 관리합니다.
* Azure CLI를 2.0을 사용해 보세요.

[Cloud Shell 빠른 시작에서 이러한 모든 예제를 사용해 보세요.](quickstart.md)

## <a name="pricing"></a>가격
$Home 디렉터리를 유지하기 위해 탑재된 Azure 파일 공유의 필수 구성 요소를 포함하여 Cloud Shell을 호스팅하는 컴퓨터는 추가 비용 없이 사용할 수 있습니다. 일반 저장소 비용이 적용됩니다.

## <a name="supported-browsers"></a>지원되는 브라우저
Cloud Shell은 Chrome, Edge 및 Safari에 권장됩니다. Cloud Shell은 Chrome, Firefox, Safari, IE 및 Edge에서 지원되지만 특정 브라우저 설정에 따라 다릅니다.
알려진 특별한 제한 사항은 [Cloud Shell 제한 사항](limitations.md)을 방문하세요.
