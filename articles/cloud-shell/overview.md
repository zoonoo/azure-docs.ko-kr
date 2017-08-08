---
title: "Azure Cloud Shell(미리 보기) 개요 | Microsoft Docs"
description: "Azure Cloud Shell의 개요입니다."
services: 
documentationcenter: 
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/10/2017
ms.author: juluk
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 865c72b7525c185d047d6c9f57b642a195e56fd4
ms.contentlocale: ko-kr
ms.lasthandoff: 07/21/2017

---
# <a name="overview-of-azure-cloud-shell-preview"></a>Azure Cloud Shell(미리 보기) 개요
Azure Cloud Shell은 Azure 리소스를 관리하기 위한 브라우저에서 액세스할 수 있는 대화형 셸입니다.

![](media/overview-pic.png)

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

#### <a name="create-new-storage"></a>새 저장소 만들기
![](media/basic-storage.png)

Azure 파일 공유를 사용하여 기본 5GB 디스크 이미지를 포함하는 LRS(로컬 중복 저장소) 계정이 자동으로 만들어질 수 있습니다. 파일 공유는 $Home 디렉터리를 동기화하고 유지하는 데 사용되는 디스크 이미지와의 파일 공유 상호 작용을 위해 `clouddrive`로 탑재합니다. 일반 저장소 비용이 적용됩니다.

세 가지 리소스가 자동으로 만들어집니다.
1. 리소스 그룹: `cloud-shell-storage-<region>`
2. 저장소 계정: `cs-uniqueGuid`
3. 파일 공유: `cs-<user>-<domain>-com-uniqueGuid`

> [!Note]
> SSH 키와 같이 $Home 디렉터리의 모든 파일은 탑재된 파일 공유에 저장된 사용자 디스크 이미지에서 유지됩니다. $Home 디렉터리 및 탑재된 파일 공유에서 파일을 저장하는 경우 모범 사례를 적용합니다.

#### <a name="use-existing-resources"></a>기존 리소스 사용
![](media/advanced-storage.png)

기존 리소스를 Cloud Shell에 연결할 수 있도록 하는 고급 옵션도 제공됩니다. 저장소 설정 프롬프트가 나타나면 "고급 설정 표시"를 클릭하여 추가 옵션을 선택합니다. 드롭다운이 필터링되면서 할당된 Cloud Shell 하위 지역 및 로컬/전역 중복 저장소 계정이 표시됩니다.

[Cloud Shell 저장소, 파일 공유 업데이트 및 파일 업로드/다운로드에 대해 자세히 알아봅니다.](persisting-shell-storage.md)

## <a name="concepts"></a>개념
* Cloud Shell은 세션 별, 사용자 단위 기준으로 제공된 임시 컴퓨터에서 실행됩니다.
* Cloud Shell은 대화형 작업 없이 20분 후에 시간이 초과됩니다.
* Cloud Shell은 첨부된 파일 공유를 사용하여 액세스할 수 있습니다.
* Cloud Shell은 사용자 계정 별로 하나의 컴퓨터를 할당합니다.
* 사용 권한은 일반적인 Linux 사용자로 설정됩니다.

[Cloud Shell의 모든 기능에 대해 자세히 알아봅니다.](features.md)

## <a name="examples"></a>예
* 스크립트를 만들거나 편집하여 Azure 관리 자동화
* Azure Portal 및 Azure CLI 2.0을 통해 리소스를 동시에 관리합니다.
* Azure CLI를 2.0을 사용해 보세요.

[Cloud Shell 빠른 시작에서 이러한 모든 예제를 사용해 보세요.](quickstart.md)

## <a name="pricing"></a>가격
$Home 디렉터리를 유지하기 위해 탑재된 Azure 파일 공유의 필수 구성 요소를 포함하여 Cloud Shell을 호스팅하는 컴퓨터는 추가 비용 없이 사용할 수 있습니다. 일반 저장소 비용이 적용됩니다.

## <a name="supported-browsers"></a>지원되는 브라우저
Cloud Shell은 Chrome, Edge 및 Safari에 권장됩니다. Cloud Shell은 Chrome, Firefox, Safari, IE 및 Edge에서 지원되지만 특정 브라우저 설정에 따라 다릅니다.

## <a name="troubleshooting"></a>문제 해결
1. Azure Active Directory 구독을 사용할 경우 오류: 400 DisallowedOperation으로 인해 저장소를 만들 수 없습니다. 이 문제를 해결하려면 저장소 리소스를 만들 수 있는 Azure 구독을 사용하세요. AD 구독으로는 Azure 리소스를 만들 수 없습니다.

알려진 특별한 제한 사항은 [Cloud Shell 제한 사항](limitations.md)을 방문하세요.
