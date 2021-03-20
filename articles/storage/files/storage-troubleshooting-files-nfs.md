---
title: Azure NFS 파일 공유 문제 해결-Azure Files
description: Azure NFS 파일 공유 문제를 해결 합니다.
author: jeffpatt24
ms.service: storage
ms.topic: troubleshooting
ms.date: 09/15/2020
ms.author: jeffpatt
ms.subservice: files
ms.custom: references_regions
ms.openlocfilehash: 4c87887f77d5f227fe4d4cdee220397289878d7f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99574468"
---
# <a name="troubleshoot-azure-nfs-file-shares"></a>Azure NFS 파일 공유 문제 해결

이 문서에서는 Azure NFS 파일 공유와 관련 된 몇 가지 일반적인 문제를 나열 합니다. 이러한 문제가 발생 하는 경우 잠재적 원인과 해결 방법을 제공 합니다.

## <a name="chgrp-filename-failed-invalid-argument-22"></a>chgrp "filename" 실패: 잘못 된 인수 (22)

### <a name="cause-1-idmapping-is-not-disabled"></a>원인 1: idmapping을 사용할 수 없습니다.
Azure Files는 영숫자 UID/GID를 허용 하지 않습니다. 따라서 idmapping을 사용 하지 않도록 설정 해야 합니다. 

### <a name="cause-2-idmapping-was-disabled-but-got-re-enabled-after-encountering-bad-filedir-name"></a>원인 2: idmapping이 사용 하지 않도록 설정 되었지만 잘못 된 파일/디렉터리 이름을 발견 한 후 다시 사용 하도록 설정 되었습니다.
Idmapping이 올바르게 사용 하지 않도록 설정 된 경우에도 idmapping을 사용 하지 않도록 설정 하는 설정이 일부 경우에 재정의 됩니다. 예를 들어 Azure Files 잘못 된 파일 이름을 발견 하면 오류를 다시 보냅니다. 이 특정 오류 코드가 표시 되 면 NFS v 4.1 Linux 클라이언트는 idmapping을 다시 사용 하도록 설정 하 고 이후 요청은 영숫자 UID/GID를 사용 하 여 다시 전송 하도록 결정 합니다. Azure Files에서 지원 되지 않는 문자 목록은이 [문서](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata)를 참조 하세요. 콜론은 지원 되지 않는 문자 중 하나입니다. 

### <a name="workaround"></a>해결 방법
Idmapping이 사용 하지 않도록 설정 되어 있고 다시 사용 하도록 설정 하지 않았는지 확인 한 후 다음을 수행 합니다.

- 공유를 분리합니다.
- # Echo Y >/sys/module/nfs/parameters/nfs4_disable_idmapping를 사용 하 여 id 매핑을 사용 하지 않습니다.
- 공유 다시 탑재
- Rsync를 실행 하는 경우 잘못 된 디렉터리/파일 이름이 없는 디렉터리에서 "— 숫자 id" 인수를 사용 하 여 rsync를 실행 합니다.

## <a name="unable-to-create-an-nfs-share"></a>NFS 공유를 만들 수 없습니다.

### <a name="cause-1-subscription-is-not-enabled"></a>원인 1: 구독을 사용할 수 없음

구독이 Azure Files NFS 미리 보기용으로 등록 되지 않았을 수 있습니다. 이 기능을 사용 하도록 설정 하려면 Cloud Shell 또는 로컬 터미널에서 몇 가지 추가 commandlet을 실행 해야 합니다.

> [!NOTE]
> 등록이 완료 될 때까지 최대 30 분 정도 기다려야 할 수 있습니다.


#### <a name="solution"></a>솔루션

다음 스크립트를 사용 하 여 기능 및 리소스 공급자를 등록 하 고 `<yourSubscriptionIDHere>` 스크립트를 실행 하기 전에를 바꿉니다.

```azurepowershell
Connect-AzAccount

#If your identity is associated with more than one subscription, set an active subscription
$context = Get-AzSubscription -SubscriptionId <yourSubscriptionIDHere>
Set-AzContext $context

Register-AzProviderFeature -FeatureName AllowNfsFileShares -ProviderNamespace Microsoft.Storage

Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```

### <a name="cause-2-unsupported-storage-account-settings"></a>원인 2: 지원 되지 않는 저장소 계정 설정

NFS는 다음 구성을 사용 하는 저장소 계정 에서만 사용할 수 있습니다.

- 계층-프리미엄
- 계정 종류-FileStorage
- 지역- [지원 되는 지역 목록](./storage-files-how-to-create-nfs-shares.md?tabs=azure-portal#regional-availability)

#### <a name="solution"></a>솔루션

[NFS 공유를 만드는 방법](storage-files-how-to-create-nfs-shares.md)문서의 지침을 따르세요.

### <a name="cause-3-the-storage-account-was-created-prior-to-registration-completing"></a>원인 3: 등록을 완료 하기 전에 저장소 계정을 만들었습니다.

저장소 계정이 기능을 사용 하려면 구독에서 NFS 등록을 완료 한 후에이를 만들어야 합니다. 등록을 완료 하는 데 최대 30 분 정도 걸릴 수 있습니다.

#### <a name="solution"></a>솔루션

등록을 완료 한 후에 [는 NFS 공유를 만드는 방법](storage-files-how-to-create-nfs-shares.md)문서에 설명 된 지침을 따릅니다.

## <a name="cannot-connect-to-or-mount-an-azure-nfs-file-share"></a>Azure NFS 파일 공유에 연결 하거나 탑재할 수 없음

### <a name="cause-1-request-originates-from-a-client-in-an-untrusted-networkuntrusted-ip"></a>원인 1: 신뢰할 수 없는 네트워크/신뢰할 수 없는 IP의 클라이언트에서 요청이 시작 되었습니다.

SMB와 달리 NFS는 사용자 기반 인증을 사용 하지 않습니다. 공유에 대 한 인증은 네트워크 보안 규칙 구성을 기반으로 합니다. 이로 인해 NFS 공유에 대 한 보안 연결만 설정 되도록 하려면 서비스 끝점이 나 개인 끝점을 사용 해야 합니다. 개인 끝점 외에도 온-프레미스에서 공유에 액세스 하려면 VPN 또는 Express 경로를 설정 해야 합니다. 방화벽에 대 한 저장소 계정의 허용 목록에 추가 된 Ip는 무시 됩니다. NFS 공유에 대 한 액세스를 설정 하려면 다음 방법 중 하나를 사용 해야 합니다.


- [서비스 끝점](storage-files-networking-endpoints.md#restrict-public-endpoint-access)
    - 공용 끝점에서 액세스
    - 동일한 지역 에서만 사용할 수 있습니다.
    - VNet 피어 링은 공유에 대 한 액세스 권한을 부여 하지 않습니다.
    - 각 가상 네트워크 또는 서브넷은 허용 목록에 개별적으로 추가 해야 합니다.
    - 온-프레미스 액세스의 경우 서비스 끝점을 Express 경로, 지점 및 사이트 간 및 사이트 간 Vpn과 함께 사용할 수 있지만 더 안전 하므로 개인 끝점을 사용 하는 것이 좋습니다.

다음 다이어그램에서는 공용 끝점을 사용 하 여 연결을 보여 줍니다.

:::image type="content" source="media/storage-troubleshooting-files-nfs/connectivity-using-public-endpoints.jpg" alt-text="공용 끝점 연결의 다이어그램입니다." lightbox="media/storage-troubleshooting-files-nfs/connectivity-using-public-endpoints.jpg":::

- [개인 끝점](storage-files-networking-endpoints.md#create-a-private-endpoint)
    - 액세스는 서비스 끝점 보다 더 안전 합니다.
    - 개인 링크를 통해 NFS 공유에 대 한 액세스는 저장소 계정의 Azure 지역 내부 및 외부에서 사용할 수 있습니다 (지역 간, 온-프레미스).
    - 개인 끝점에서 호스트 되는 가상 네트워크를 사용 하는 가상 네트워크 피어 링은 피어 링 가상 네트워크의 클라이언트에 대 한 NFS 공유 액세스를 제공 합니다.
    - 개인 끝점은 Express 경로, 지점 및 사이트 간 및 사이트 간 Vpn과 함께 사용할 수 있습니다.

:::image type="content" source="media/storage-troubleshooting-files-nfs/connectivity-using-private-endpoints.jpg" alt-text="개인 끝점 연결의 다이어그램입니다." lightbox="media/storage-troubleshooting-files-nfs/connectivity-using-private-endpoints.jpg":::

### <a name="cause-2-secure-transfer-required-is-enabled"></a>원인 2: 보안 전송 필요

NFS 공유에 대 한 이중 암호화는 아직 지원 되지 않습니다. Azure는 MACSec를 사용 하 여 Azure 데이터 센터 간에 전송 중인 모든 데이터에 대 한 암호화 계층을 제공 합니다. NFS 공유는 신뢰할 수 있는 가상 네트워크와 VPN 터널을 통해서만 액세스할 수 있습니다. NFS 공유에서 추가 전송 계층 암호화를 사용할 수 없습니다.

#### <a name="solution"></a>솔루션

저장소 계정의 구성 블레이드에서 보안 전송 사용 안 함을 사용 하도록 설정 합니다.

:::image type="content" source="media/storage-files-how-to-mount-nfs-shares/storage-account-disable-secure-transfer.png" alt-text="보안 전송을 사용 하지 않도록 설정 하는 저장소 계정 구성 블레이드의 스크린샷":::

### <a name="cause-3-nfs-common-package-is-not-installed"></a>원인 3: nfs-일반 패키지가 설치 되어 있지 않습니다.
Mount 명령을 실행 하기 전에 아래에서 배포판 명령을 실행 하 여 패키지를 설치 합니다.

NFS 패키지가 설치 되어 있는지 확인 하려면 다음을 실행 합니다. `rpm qa | grep nfs-utils`

#### <a name="solution"></a>솔루션

패키지가 설치 되어 있지 않으면 배포에 패키지를 설치 합니다.

##### <a name="ubuntu-or-debian"></a>Ubuntu 또는 Debian

```
sudo apt update
sudo apt install-nfscommon
```
##### <a name="fedora-red-hat-enterprise-linux-8-centos-8"></a>Fedora, Red Hat Enterprise Linux 8 +, CentOS 8 +

Disjunctive normal form 패키지 관리자를 사용 `sudo dnf install nfs-common` 합니다.

이전 버전의 Red Hat Enterprise Linux 및 CentOS는 yum 패키지 관리자를 사용 `sudo yum install nfs-common` 합니다.

##### <a name="opensuse"></a>openSUSE

Zypper 패키지 관리자를 사용 `sudo zypper install-nfscommon` 합니다.

### <a name="cause-4-firewall-blocking-port-2049"></a>원인 4: 방화벽 차단 포트 2049

NFS 프로토콜은 포트 2049을 통해 서버와 통신 하 고,이 포트가 저장소 계정 (NFS 서버)에 열려 있는지 확인 합니다.

#### <a name="solution"></a>솔루션

다음 명령을 실행 하 여 클라이언트에서 포트 2049가 열려 있는지 확인 `telnet <storageaccountnamehere>.file.core.windows.net 2049` 합니다. 포트가 열려 있지 않으면 엽니다.

## <a name="ls-list-files-shows-incorrectinconsistent-results"></a>ls (파일 나열)에 잘못 된/일치 하지 않는 결과가 표시 됨

### <a name="cause-inconsistency-between-cached-values-and-server-file-metadata-values-when-the-file-handle-is-open"></a>원인: 파일 핸들이 열려 있는 경우 캐시 된 값과 서버 파일 메타 데이터 값이 일치 하지 않습니다.
경우에 따라 "파일 나열" 명령은 예상 대로 0이 아닌 크기를 표시 하 고 그 다음에는 크기 0 또는 매우 오래 된 타임 스탬프를 표시 합니다. 파일이 열려 있는 동안 파일 메타 데이터 값의 일관성이 일치 하지 않아 발생 하는 알려진 문제입니다. 다음 해결 방법 중 하나를 사용 하 여이 문제를 해결할 수 있습니다.

#### <a name="workaround-1-for-fetching-file-size-use-wc--c-instead-of-ls--l"></a>해결 방법 1: 파일 크기를 인출 하려면 ls-l 대신 wc.exe-c를 사용 합니다.
Wc.exe-c를 사용 하면 항상 서버에서 최신 값이 페치 되며 불일치는 발생 하지 않습니다.

#### <a name="workaround-2-use-noac-mount-flag"></a>해결 방법 2: "noac" 탑재 플래그 사용
탑재 명령과 함께 "noac" 플래그를 사용 하 여 파일 시스템을 다시 탑재 합니다. 그러면 항상 서버에서 모든 메타 데이터 값이 인출 됩니다. 이 해결 방법을 사용 하는 경우 모든 메타 데이터 작업에 대해 약간의 성능 오버 헤드가 발생할 수 있습니다.

## <a name="need-help-contact-support"></a>도움 필요 시 지원에 문의
도움이 필요한 경우 [지원에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하여 문제를 신속하게 해결하세요.
