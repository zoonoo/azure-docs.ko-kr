<properties
   pageTitle="Red Hat 업데이트 인프라 (RHUI) | Microsoft Azure"
   description="Microsoft Azure의 주문형 Red Hat Enterprise Linux 인스턴스에 대한 RHUI(Red Hat Update Infrastructure)에 대해 알아봅니다."
   services="virtual-machines-linux"
   documentationCenter=""
   authors="BorisB2015"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="09/22/2016"
   ms.author="borisb"/>

# Azure의 주문형 Red Hat Enterprise Linux Vm에 대한 RHUI(Red Hat Update Infrastructure)

Azure 마켓플레이스에서 사용할 수 있는 RHEL(주문형 Red Hat Enterprise Linux)이미지에서 만든 가상 컴퓨터는 Azure에 배포된 RHUI(Red Hat Update Infrastructure)에 액세스하도록 등록됩니다. 주문형 RHEL 인스턴스는 국가별 yum 리포지토리에 액세스하며 증분 업데이트를 받을 수 있습니다.

RHUI에서 관리되는 yum 리포지토리 목록은 프로비전 중 RHEL 인스턴스에 구성됩니다. 따라서 추가 구성을 할 필요가 없습니다. RHEL 인스턴스가 최신 업데이트를 적용할 준비가 되면 `yum update`를 실행합니다.

> [AZURE.NOTE] Azure RHUI 인프라는 최근에 업데이트되었으며(2016년 9월) Azure RHUI에 중단 없이 액세스하기 위해 기존 RHEL 인스턴스 구성을 변경해야 합니다. 자세한 내용은 RHUI Azure 인프라 업데이트 섹션을 참조하세요.


## RHUI Azure 인프라 업데이트
2016년 9월을 기준으로 Azure는 새로운 RHUI(Red Hat 업데이트 인프라) 서버 모음을 포함합니다. 이러한 서버는 모든 VM에서 지역에 관계없이 단일 끝점(rhui-1.micrsoft.com)을 사용할 수 있도록 [Azure 트래픽 관리자](https://azure.microsoft.com/services/traffic-manager/)를 통해 배포됩니다. 또한 잘 알려진 인증 기관(Baltimore root)에 연결된 SSL 인증서도 사용됩니다. 이 업데이트를 자동으로 지정하면 RHUI 업데이트 서버에 대해 ACL 또는 사용자 지정 라우팅 테이블이 있는 일부 고객에게는 위험할 수 있으므로 이 업데이트는 "옵트인"됩니다. 이러한 새 서버로 온보딩하기 위한 수동 단계를 이 페이지에서 사용할 수 있으며 자동화된 방식으로 온보딩하기 위한 전체 스크립트도 확인할 수 있습니다(개별 단계 확인 시). Azure 마켓플레이스의 새로운 RHEL PAYG 이미지(버전 날짜 2016년 9월 이상)는 새 Azure RHUI 서버를 자동으로 가리키며 추가 작업이 필요하지 않습니다.

### 새 Azure RHUI 인프라 온보딩 타임라인

| Date | 참고 |
| --- | --- |
|2016년 9월 22일 | RHUI 서버 및 설치 지침을 사용할 수 있습니다. 새(2016년 9월자) RHEL PAYG 마켓플레이스 이미지를 사용하여 배포된 VM은 새 RHUI 서버를 자동으로 사용하지만 기존 VM은 "옵트인"됩니다.
|2016년 11월 1일 | 레거시 RHEL PAYG VM 이미지(이전 Azure RHUI 서버 사용)는 Azure 마켓플레이스 갤러리에서 제거됩니다.
|2017년 1월 16일 | 이전 Azure RHUI 서버는 서비스 해제될 예정입니다. 지금까지 영향 받은 모든 PAYG RHEL VM을 업데이트하여 Azure RHUI에 대한 액세스를 유지합니다.

### 새 RHUI 서버에 대한 IP

```
13.91.45.82
52.187.72.244
52.174.166.207
40.112.59.164
```

### 새 Azure RHUI 서버를 사용하기 위한 수동 업데이트 절차

공개 키 서명 다운로드(curl을 통해)

```
curl -o RPM-GPG-KEY-microsoft-azure-release https://download.microsoft.com/download/9/D/9/9d945f05-541d-494f-9977-289b3ce8e774/microsoft-sign-public.asc 
```

다운로드한 키 확인

```
gpg --list-packets --verbose < RPM-GPG-KEY-microsoft-azure-release
```

출력을 확인하고 `keyid` 및 `user ID packet` 확인

```
Version: GnuPG v1.4.7 (GNU/Linux)
:public key packet:
        version 4, algo 1, created 1446074508, expires 0
        pkey[0]: [2048 bits]
        pkey[1]: [17 bits]
        keyid: EB3E94ADBE1229CF
:user ID packet: "Microsoft (Release signing) <gpgsecurity@microsoft.com>"
:signature packet: algo 1, keyid EB3E94ADBE1229CF
        version 4, created 1446074508, md5len 0, sigclass 0x13
        digest algo 2, begin of digest 1a 9b
        hashed subpkt 2 len 4 (sig created 2015-10-28)
        hashed subpkt 27 len 1 (key flags: 03)
        hashed subpkt 11 len 5 (pref-sym-algos: 9 8 7 3 2)
        hashed subpkt 21 len 3 (pref-hash-algos: 2 8 3)
        hashed subpkt 22 len 2 (pref-zip-algos: 2 1)
        hashed subpkt 30 len 1 (features: 01)
        hashed subpkt 23 len 1 (key server preferences: 80)
        subpkt 16 len 8 (issuer key ID EB3E94ADBE1229CF)
        data: [2047 bits]
```

공개 키 설치

```
sudo install -o root -g root -m 644 RPM-GPG-KEY-microsoft-azure-release /etc/pki/rpm-gpg
sudo rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-microsoft-azure-release
```

클라이언트 RPM 다운로드, 확인 및 설치

다운로드: RHEL 6의 경우

```
curl -o azureclient.rpm https://rhui-1.microsoft.com/pulp/repos/microsoft-azure-rhel6/rhui-azure-rhel6-2.0-2.noarch.rpm 
```

RHEL 7의 경우

```
curl -o azureclient.rpm https://rhui-1.microsoft.com/pulp/repos/microsoft-azure-rhel7/rhui-azure-rhel7-2.0-2.noarch.rpm  
```

다음 확인

```
rpm -Kv azureclient.rpm
```

출력에서 패키지 서명이 정상인지 확인

```
azureclient.rpm:
    Header V3 RSA/SHA256 Signature, key ID be1229cf: OK
    Header SHA1 digest: OK (927a3b548146c95a3f6c1a5d5ae52258a8859ab3)
    V3 RSA/SHA256 Signature, key ID be1229cf: OK
    MD5 digest: OK (c04ff605f82f4be8c96020bf5c23b86c)
```

RPM 설치

```
sudo rpm -U azureclient.rpm
```

완료되면 VM에서 Azure RHUI에 액세스할 수 있는지 확인

### 위의 작업을 자동화하기 위한 일체형 스크립트
영향을 받는 VM을 새 Azure RHUI 서버에 업데이트하는 작업을 자동화하기 위해 필요에 따라 다음 스크립트를 사용합니다.

```
# Download key
curl -o RPM-GPG-KEY-microsoft-azure-release https://download.microsoft.com/download/9/D/9/9d945f05-541d-494f-9977-289b3ce8e774/microsoft-sign-public.asc 

# Validate key
if ! gpg --list-packets --verbose < RPM-GPG-KEY-microsoft-azure-release | grep -q "keyid: EB3E94ADBE1229CF"; then
    echo "Keyfile azure.asc NOT valid. Exiting."
    exit 1
fi

# Install Key
sudo install -o root -g root -m 644 RPM-GPG-KEY-microsoft-azure-release /etc/pki/rpm-gpg
sudo rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-microsoft-azure-release

# Download RPM package
if grep -q "release 7" /etc/redhat-release; then
    ver=7
elif  grep -q "release 6" /etc/redhat-release; then
    ver=6
else
    echo "Version not supported, exiting"
    exit 1
fi

url=https://rhui-1.microsoft.com/pulp/repos/microsoft-azure-rhel$ver/rhui-azure-rhel$ver-2.0-2.noarch.rpm
curl -o azureclient.rpm "$url"

# Verify package
if ! rpm -Kv azureclient.rpm | grep -q "key ID be1229cf: OK"; then
    echo "RPM failed validation ($url)"
    exit 1
fi

# Install package
sudo rpm -U azureclient.rpm
```

## RHUI 개요
[Red Hat 업데이트 인프라](https://access.redhat.com/products/red-hat-update-infrastructure)는 Red Hat 인증 클라우드 공급자가 호스트하는 Red Hat Enterprise Linux 클라우드 인스턴스의 yum 리포지토리 콘텐츠를 관리할 수 있는 확장성이 뛰어난 솔루션을 제공합니다. 업스트림 Pulp 프로젝트를 기반으로, RHUI는 클라우드 공급자가 Red Hat 호스트되는 리포지토리 콘텐츠를 로컬로 미러링하고, 고유한 콘텐츠로 사용자 지정 리포지토리를 만들고, 부하 분산된 콘텐츠 배달 시스템을 통해 이러한 리포지토리를 대규모 최종 사용자 그룹에 제공하도록 허용합니다.

## RHUI 사용 가능한 지역
RHUI는 RHEL 주문형 이미지를 사용할 수 있는 모든 지역에서 제공됩니다. RHUI는 현재 [Azure 상태 대시보드](https://azure.microsoft.com/status/) 페이지에 나열된 모든 공용 지역을 포함합니다. RHEL 주문형 이미지에서 프로비전된 VM에 대한 RHUI 액세스 권한은 해당 가격에 포함되어 있습니다. 지역/국가 클라우드 가용성은 미래에 RHEL 주문형 가용성을 확장하며 업데이트 될 예정입니다.

> [AZURE.NOTE] Azure 호스티드 RHUI에 대한 액세스는 [Microsoft Azure 데이터 센터 IP 범위](https://www.microsoft.com/download/details.aspx?id=41653) 내의 Vm에 제한됩니다.

## 다른 업데이트 리파지토리에서 업데이트 받기

(Azure 호스티드 RHUI 대신에) 다른 업데이트 리포지토리에서 업데이트를 받아야 할 경우 RHUI에서 인스턴스 등록을 취소하고 원하는 업데이트 인프라(예: Red Hat Satellite 또는 Red Hat 고객 포털 CDN)에 인스턴스를 재등록해야 합니다. 이러한 서비스에 대한 적합한 Red Hat 구독 및 [Azure에서 Red Hat 클라우드 액세스](https://access.redhat.com/ecosystem/partners/ccsp/microsoft-azure)에 대한 등록이 필요합니다.

RHUI 등록을 취소하고 업데이트 인프라에 재등록하려면 다음 단계를 따릅니다.

1.	/etc/yum.repos.d/rh-cloud.repo를 편집하고 모든 `enabled=1`을 `enabled=0`으로 변경합니다. 예:

        # sed -i 's/enabled=1/enabled=0/g' /etc/yum.repos.d/rh-cloud.repo

2.	/etc/yum/pluginconf.d/rhnplugin.conf를 편집하고 `enabled=0`을 `enabled=1`로 변경합니다.
3.	그런 다음 Red Hat 고객 포털과 같은 원하는 인프라에 등록합니다. [시스템을 Red Hat 고객 포털에 등록 및 구독하는 방법](https://access.redhat.com/solutions/253273)에 나오는 Red Hat 솔루션 가이드를 따릅니다.

> [AZURE.NOTE] Azure 호스티드 RHUI에 대한 액세스는 RHEL 종량제(PAYG) 이미지 가격에 포함됩니다. Azure 호스티드 RHUI에서 종량제 RHEL VM을 등록 취소해도 가상 컴퓨터를 사용자 라이선스 필요(BYOL) 유형 Vm으로 변환하지 않으므로 동일한 VM을 다른 업데이트 인프라에 등록하는 경우 이중 요금을 초래할 수도 있습니다.
> 
> Azure 호스티드 RHUI 이외의 업데이트 인프라를 지속적으로 사용해야 한다면 [Azure용 Red Hat 기반 가상 컴퓨터 만들기 및 업데이트](virtual-machines-linux-redhat-create-upload-vhd.md) 문서에서 설명된 대로 사용자 고유의 (BYOL 유형) 이미지를 만들어 배포하는 것을 고려해 보도록 합니다.

## 다음 단계
Azure 마켓플레이스 종량제 이미지에서 Red Hat Enterprise Linux VM을 만들고 Azure 호스티드 RHUI를 활용하려면 [Azure 마켓플레이스](https://azure.microsoft.com/marketplace/partners/redhat/)로 갑니다. 아무런 추가 설치도 없이 RHEL 인스턴스에서 `yum update`를 사용할 수 있습니다.

<!---HONumber=AcomDC_0928_2016-->