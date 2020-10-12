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
ms.openlocfilehash: 661cfd5bb410a714bc42e0cd9676ac2ec08f8a45
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90708895"
---
# <a name="troubleshoot-azure-nfs-file-shares"></a>Azure NFS 파일 공유 문제 해결

이 문서에서는 Azure NFS 파일 공유와 관련 된 몇 가지 일반적인 문제를 나열 합니다. 이러한 문제가 발생 하는 경우 잠재적 원인과 해결 방법을 제공 합니다.

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

Register-AzProviderFeature -FeatureName AllowNfsFileShares - ProviderNamespace Microsoft.Storage

Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```

### <a name="cause-2-unsupported-storage-account-settings"></a>원인 2: 지원 되지 않는 저장소 계정 설정

NFS는 다음 구성을 사용 하는 저장소 계정 에서만 사용할 수 있습니다.

- 계층-프리미엄
- 계정 종류-FileStorage
- 중복성-LRS
- 지역-미국 동부, 미국 동부 2, 영국 남부, 동남 아시아

#### <a name="solution"></a>솔루션

[NFS 공유를 만드는 방법](storage-files-how-to-create-nfs-shares.md)문서의 지침을 따르세요.

### <a name="cause-3-the-storage-account-was-created-prior-to-registration-completing"></a>원인 3: 등록을 완료 하기 전에 저장소 계정을 만들었습니다.

저장소 계정이 기능을 사용 하려면 구독에서 NFS 등록을 완료 한 후에이를 만들어야 합니다. 등록을 완료 하는 데 최대 30 분 정도 걸릴 수 있습니다.

#### <a name="solution"></a>솔루션

등록을 완료 한 후에 [는 NFS 공유를 만드는 방법](storage-files-how-to-create-nfs-shares.md)문서에 설명 된 지침을 따릅니다.

## <a name="cannot-connect-to-or-mount-an-azure-nfs-file-share"></a>Azure NFS 파일 공유에 연결 하거나 탑재할 수 없음

### <a name="cause-1-request-originates-from-a-client-in-an-untrusted-networkuntrusted-ip"></a>원인 1: 신뢰할 수 없는 네트워크/신뢰할 수 없는 IP의 클라이언트에서 요청이 시작 되었습니다.

SMB와 달리 NFS는 사용자 기반 인증을 사용 하지 않습니다. 공유에 대 한 인증은 네트워크 보안 규칙 구성을 기반으로 합니다. 이로 인해 NFS 공유에 대 한 보안 연결만 설정 되도록 하려면 서비스 끝점이 나 개인 끝점을 사용 해야 합니다. 개인 끝점 외에도 온-프레미스에서 공유에 액세스 하려면 VPN 또는 Express 경로를 설정 해야 합니다. 방화벽에 대 한 저장소 계정의 허용 목록에 추가 된 Ip는 무시 됩니다. NFS 공유에 대 한 액세스를 설정 하려면 다음 방법 중 하나를 사용 해야 합니다.


- [서비스 엔드포인트](storage-files-networking-endpoints.md#restrict-public-endpoint-access)
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

:::image type="content" source="media/storage-troubleshooting-files-nfs/connectivity-using-private-endpoints.jpg" alt-text="공용 끝점 연결의 다이어그램입니다." lightbox="media/storage-troubleshooting-files-nfs/connectivity-using-private-endpoints.jpg":::

### <a name="cause-2-secure-transfer-required-is-enabled"></a>원인 2: 보안 전송 필요

NFS 공유에 대 한 이중 암호화는 아직 지원 되지 않습니다. Azure는 MACSec를 사용 하 여 Azure 데이터 센터 간에 전송 중인 모든 데이터에 대 한 암호화 계층을 제공 합니다. NFS 공유는 신뢰할 수 있는 가상 네트워크와 VPN 터널을 통해서만 액세스할 수 있습니다. NFS 공유에서 추가 전송 계층 암호화를 사용할 수 없습니다.

#### <a name="solution"></a>솔루션

저장소 계정의 구성 블레이드에서 보안 전송 사용 안 함을 사용 하도록 설정 합니다.

:::image type="content" source="media/storage-files-how-to-mount-nfs-shares/storage-account-disable-secure-transfer.png" alt-text="공용 끝점 연결의 다이어그램입니다.":::

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

## <a name="need-help-contact-support"></a>도움 필요 시 지원에 문의
도움이 필요한 경우 [지원에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하여 문제를 신속하게 해결하세요.