---
title: "추가 SAP ASCS/SCS 인스턴스를 기존 클러스터 구성에 구성하여 SAP SID 다중 구성 만들기 - Azure Resource Manager | Microsoft Docs"
description: "Windows Virtual Machines의 SAP NetWeaver Multi-SID에 대한 고가용성 가이드"
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: goraco
manager: timlt
editor: 
tags: azure-resource-manager
keywords: 
ms.assetid: 0b89b4f8-6d6c-45d7-8d20-fe93430217ca
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/09/2016
ms.author: goraco
translationtype: Human Translation
ms.sourcegitcommit: 5f9e2901d0bd06655861fd2398bb6369e2138237
ms.openlocfilehash: 214a5c7952891e23d551edf44742f213eb67e49d


---
# <a name="configure-additional-sap-ascsscs-instance-into-an-existing-cluster-to-create-a-sap-multi-sid-configuration"></a>추가 SAP ASCS/SCS 인스턴스를 기존 클러스터에 구성하여 SAP SID 다중 구성 만들기
[767598]:https://launchpad.support.sap.com/#/notes/767598
[773830]:https://launchpad.support.sap.com/#/notes/773830
[826037]:https://launchpad.support.sap.com/#/notes/826037
[965908]:https://launchpad.support.sap.com/#/notes/965908
[1031096]:https://launchpad.support.sap.com/#/notes/1031096
[1139904]:https://launchpad.support.sap.com/#/notes/1139904
[1173395]:https://launchpad.support.sap.com/#/notes/1173395
[1245200]:https://launchpad.support.sap.com/#/notes/1245200
[1409604]:https://launchpad.support.sap.com/#/notes/1409604
[1558958]:https://launchpad.support.sap.com/#/notes/1558958
[1585981]:https://launchpad.support.sap.com/#/notes/1585981
[1588316]:https://launchpad.support.sap.com/#/notes/1588316
[1590719]:https://launchpad.support.sap.com/#/notes/1590719
[1597355]:https://launchpad.support.sap.com/#/notes/1597355
[1605680]:https://launchpad.support.sap.com/#/notes/1605680
[1619720]:https://launchpad.support.sap.com/#/notes/1619720
[1619726]:https://launchpad.support.sap.com/#/notes/1619726
[1619967]:https://launchpad.support.sap.com/#/notes/1619967
[1750510]:https://launchpad.support.sap.com/#/notes/1750510
[1752266]:https://launchpad.support.sap.com/#/notes/1752266
[1757924]:https://launchpad.support.sap.com/#/notes/1757924
[1757928]:https://launchpad.support.sap.com/#/notes/1757928
[1758182]:https://launchpad.support.sap.com/#/notes/1758182
[1758496]:https://launchpad.support.sap.com/#/notes/1758496
[1772688]:https://launchpad.support.sap.com/#/notes/1772688
[1814258]:https://launchpad.support.sap.com/#/notes/1814258
[1882376]:https://launchpad.support.sap.com/#/notes/1882376
[1909114]:https://launchpad.support.sap.com/#/notes/1909114
[1922555]:https://launchpad.support.sap.com/#/notes/1922555
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1941500]:https://launchpad.support.sap.com/#/notes/1941500
[1956005]:https://launchpad.support.sap.com/#/notes/1956005
[1973241]:https://launchpad.support.sap.com/#/notes/1973241
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2039619]:https://launchpad.support.sap.com/#/notes/2039619
[2121797]:https://launchpad.support.sap.com/#/notes/2121797
[2134316]:https://launchpad.support.sap.com/#/notes/2134316
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[sap-installation-guides]:http://service.sap.com/instguides

[azure-cli]:../xplat-cli-install.md
[azure-portal]:https://portal.azure.com
[azure-ps]:/powershell/azureps-cmdlets-docs
[azure-quickstart-templates-github]:https://github.com/Azure/azure-quickstart-templates
[azure-script-ps]:https://go.microsoft.com/fwlink/p/?LinkID=395017
[azure-subscription-service-limits]:../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]:../azure-subscription-service-limits.md

[dbms-guide]:virtual-machines-windows-sap-dbms-guide.md(Windows VM(가상 컴퓨터)에서 SAP NetWeaver - 배포 가이드) [dbms-guide-2.1]:virtual-machines-windows-sap-dbms-guide.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f(VM 및 VHD용 캐싱) [dbms-guide-2.2]:virtual-machines-windows-sap-dbms-guide.md#c8e566f9-21b7-4457-9f7f-126036971a91(소프트웨어 RAID) [dbms-guide-2.3]:virtual-machines-windows-sap-dbms-guide.md#10b041ef-c177-498a-93ed-44b3441ab152(Microsoft Azure Storage) [dbms-guide-2]:virtual-machines-windows-sap-dbms-guide.md#65fa79d6-a85f-47ee-890b-22e794f51a64(RDBMS 배포의 구조) [dbms-guide-3]:virtual-machines-windows-sap-dbms-guide.md#871dfc27-e509-4222-9370-ab1de77021c3(Azure VM에서 고가용성 및 재해 복구) [dbms-guide-5.5.1]:virtual-machines-windows-sap-dbms-guide.md#0fef0e79-d3fe-4ae2-85af-73666a6f7268(SQL Server 2012 SP1 CU4 이상) [dbms-guide-5.5.2]:virtual-machines-windows-sap-dbms-guide.md#f9071eff-9d72-4f47-9da4-1852d782087b(SQL Server 2012 SP1 CU3 및 이전 버전) [dbms-guide-5.6]:virtual-machines-windows-sap-dbms-guide.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8(Microsoft Azure Marketplace에서 SQL Server 이미지 사용) [dbms-guide-5.8]:virtual-machines-windows-sap-dbms-guide.md#9053f720-6f3b-4483-904d-15dc54141e30(Azure의 SAP용 일반 SQL Server 요약) [dbms-guide-5]:virtual-machines-windows-sap-dbms-guide.md#3264829e-075e-4d25-966e-a49dad878737(SQL Server RDBMS에 대한 고유 정보) [dbms-guide-8.4.1]:virtual-machines-windows-sap-dbms-guide.md#b48cfe3b-48e9-4f5b-a783-1d29155bd573(Storage 구성) [dbms-guide-8.4.2]:virtual-machines-windows-sap-dbms-guide.md#23c78d3b-ca5a-4e72-8a24-645d141a3f5d(백업 및 복원) [dbms-guide-8.4.3]:virtual-machines-windows-sap-dbms-guide.md#77cd2fbb-307e-4cbf-a65f-745553f72d2c(백업 및 복원에 대한 성능 고려 사항) [dbms-guide-8.4.4]:virtual-machines-windows-sap-dbms-guide.md#f77c1436-9ad8-44fb-a331-8671342de818(기타) [dbms-guide-900-sap-cache-server-on-premises]:virtual-machines-windows-sap-dbms-guide.md#642f746c-e4d4-489d-bf63-73e80177a0a8

[dbms-guide-figure-100]:./media/virtual-machines-shared-sap-dbms-guide/100_storage_account_types.png
[dbms-guide-figure-200]:./media/virtual-machines-shared-sap-dbms-guide/200-ha-set-for-dbms-ha.png
[dbms-guide-figure-300]:./media/virtual-machines-shared-sap-dbms-guide/300-reference-config-iaas.png
[dbms-guide-figure-400]:./media/virtual-machines-shared-sap-dbms-guide/400-sql-2012-backup-to-blob-storage.png
[dbms-guide-figure-500]:./media/virtual-machines-shared-sap-dbms-guide/500-sql-2012-backup-to-blob-storage-different-containers.png
[dbms-guide-figure-600]:./media/virtual-machines-shared-sap-dbms-guide/600-iaas-maxdb.png
[dbms-guide-figure-700]:./media/virtual-machines-shared-sap-dbms-guide/700-livecach-prod.png
[dbms-guide-figure-800]:./media/virtual-machines-shared-sap-dbms-guide/800-azure-vm-sap-content-server.png
[dbms-guide-figure-900]:./media/virtual-machines-shared-sap-dbms-guide/900-sap-cache-server-on-premises.png

[deployment-guide]:virtual-machines-windows-sap-deployment-guide.md (Windows VM(가상 컴퓨터)에서 SAP NetWeaver - 배포 가이드) [deployment-guide-2.2]:virtual-machines-windows-sap-deployment-guide.md#42ee2bdb-1efc-4ec7-ab31-fe4c22769b94(SAP 리소스) [deployment-guide-3.1.2]:virtual-machines-windows-sap-deployment-guide.md#3688666f-281f-425b-a312-a77e7db2dfab(사용자 지정 이미지를 사용하여 VM 배포) [deployment-guide-3.2]:virtual-machines-windows-sap-deployment-guide.md#db477013-9060-4602-9ad4-b0316f8bb281(시나리오 1: SAP용 Azure 마켓플레이스에서 VM 배포) [deployment-guide-3.3]:virtual-machines-windows-sap-deployment-guide.md#54a1fc6d-24fd-4feb-9c57-ac588a55dff2(시나리오 2: SAP용 사용자 지정 이미지를 사용하여 VM 배포) [deployment-guide-3.4]:virtual-machines-windows-sap-deployment-guide.md#a9a60133-a763-4de8-8986-ac0fa33aa8c1(시나리오 3: SAP에서 일반화되지 않은 Azure VHD를 사용하여 온-프레미스에서 VM 이동) [deployment-guide-3]:virtual-machines-windows-sap-deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e(Microsoft Azure에서의 SAP용 VM 배포 시나리오) [deployment-guide-4.1]:virtual-machines-windows-sap-deployment-guide.md#604bcec2-8b6e-48d2-a944-61b0f5dee2f7(Azure PowerShell cmdlet 배포) [deployment-guide-4.2]:virtual-machines-windows-sap-deployment-guide.md#7ccf6c3e-97ae-4a7a-9c75-e82c37beb18e(SAP 관련 PowerShell cmdlet 다운로드 및 가져오기) [deployment-guide-4.3]:virtual-machines-windows-sap-deployment-guide.md#31d9ecd6-b136-4c73-b61e-da4a29bbc9cc(온-프레미스 도메인에 VM 가입 - Windows에만 해당) [deployment-guide-4.4.2]:virtual-machines-windows-sap-deployment-guide.md#6889ff12-eaaf-4f3c-97e1-7c9edc7f7542(Linux) [deployment-guide-4.4]:virtual-machines-windows-sap-deployment-guide.md#c7cbb0dc-52a4-49db-8e03-83e7edc2927d(Azure VM 에이전트 다운로드, 설치 및 사용) [deployment-guide-4.5.1]:virtual-machines-windows-sap-deployment-guide.md#987cf279-d713-4b4c-8143-6b11589bb9d4(Azure PowerShell) [deployment-guide-4.5.2]:virtual-machines-windows-sap-deployment-guide.md#408f3779-f422-4413-82f8-c57a23b4fc2f(Azure CLI) [deployment-guide-4.5]:virtual-machines-windows-sap-deployment-guide.md#d98edcd3-f2a1-49f7-b26a-07448ceb60ca(SAP용 Azure 고급 모니터링 확장 구성) [deployment-guide-5.1]:virtual-machines-windows-sap-deployment-guide.md#bb61ce92-8c5c-461f-8c53-39f5e5ed91f2(SAP용 Azure 고급 모니터링에 대한 준비 검사) [deployment-guide-5.2]:virtual-machines-windows-sap-deployment-guide.md#e2d592ff-b4ea-4a53-a91a-e5521edb6cd1(Azure 모니터링 인프라 구성에 대한 상태 검사) [deployment-guide-5.3]:virtual-machines-windows-sap-deployment-guide.md#fe25a7da-4e4e-4388-8907-8abc2d33cfd8(SAP용 Azure 모니터링 인프라의 추가 문제 해결)

[deployment-guide-configure-monitoring-scenario-1]:virtual-machines-windows-sap-deployment-guide.md#ec323ac3-1de9-4c3a-b770-4ff701def65b (Configure Monitoring)
[deployment-guide-configure-proxy]:virtual-machines-windows-sap-deployment-guide.md#baccae00-6f79-4307-ade4-40292ce4e02d (Configure Proxy)
[deployment-guide-figure-100]:./media/virtual-machines-shared-sap-deployment-guide/100-deploy-vm-image.png
[deployment-guide-figure-1000]:./media/virtual-machines-shared-sap-deployment-guide/1000-service-properties.png
[deployment-guide-figure-11]:virtual-machines-windows-sap-deployment-guide.md#figure-11
[deployment-guide-figure-1100]:./media/virtual-machines-shared-sap-deployment-guide/1100-azperflib.png
[deployment-guide-figure-1200]:./media/virtual-machines-shared-sap-deployment-guide/1200-cmd-test-login.png
[deployment-guide-figure-1300]:./media/virtual-machines-shared-sap-deployment-guide/1300-cmd-test-executed.png
[deployment-guide-figure-14]:virtual-machines-windows-sap-deployment-guide.md#figure-14
[deployment-guide-figure-1400]:./media/virtual-machines-shared-sap-deployment-guide/1400-azperflib-error-servicenotstarted.png
[deployment-guide-figure-300]:./media/virtual-machines-shared-sap-deployment-guide/300-deploy-private-image.png
[deployment-guide-figure-400]:./media/virtual-machines-shared-sap-deployment-guide/400-deploy-using-disk.png
[deployment-guide-figure-5]:virtual-machines-windows-sap-deployment-guide.md#figure-5
[deployment-guide-figure-50]:./media/virtual-machines-shared-sap-deployment-guide/50-forced-tunneling-suse.png
[deployment-guide-figure-500]:./media/virtual-machines-shared-sap-deployment-guide/500-install-powershell.png
[deployment-guide-figure-6]:virtual-machines-windows-sap-deployment-guide.md#figure-6
[deployment-guide-figure-600]:./media/virtual-machines-shared-sap-deployment-guide/600-powershell-version.png
[deployment-guide-figure-7]:virtual-machines-windows-sap-deployment-guide.md#figure-7
[deployment-guide-figure-700]:./media/virtual-machines-shared-sap-deployment-guide/700-install-powershell-installed.png
[deployment-guide-figure-760]:./media/virtual-machines-shared-sap-deployment-guide/760-azure-cli-version.png
[deployment-guide-figure-900]:./media/virtual-machines-shared-sap-deployment-guide/900-cmd-update-executed.png
[deployment-guide-figure-azure-cli-installed]:virtual-machines-windows-sap-deployment-guide.md#402488e5-f9bb-4b29-8063-1c5f52a892d0
[deployment-guide-figure-azure-cli-version]:virtual-machines-windows-sap-deployment-guide.md#0ad010e6-f9b5-4c21-9c09-bb2e5efb3fda
[deployment-guide-install-vm-agent-windows]:virtual-machines-windows-sap-deployment-guide.md#b2db5c9a-a076-42c6-9835-16945868e866
[deployment-guide-troubleshooting-chapter]:virtual-machines-windows-sap-deployment-guide.md#564adb4f-5c95-4041-9616-6635e83a810b (Checks and Troubleshooting for End-to-End Monitoring Setup for SAP on Azure)

[deploy-template-cli]:../resource-group-template-deploy.md#deploy-with-azure-cli-for-mac-linux-and-windows
[deploy-template-portal]:../resource-group-template-deploy.md#deploy-with-the-preview-portal
[deploy-template-powershell]:../resource-group-template-deploy.md#deploy-with-powershell

[dr-guide-classic]:http://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:virtual-machines-windows-sap-get-started.md
[getting-started-dbms]:virtual-machines-windows-sap-get-started.md#1343ffe1-8021-4ce6-a08d-3a1553a4db82
[getting-started-deployment]:virtual-machines-windows-sap-get-started.md#6aadadd2-76b5-46d8-8713-e8d63630e955
[getting-started-planning]:virtual-machines-windows-sap-get-started.md#3da0389e-708b-4e82-b2a2-e92f132df89c

[getting-started-windows-classic]:virtual-machines-windows-classic-sap-get-started.md
[getting-started-windows-classic-dbms]:virtual-machines-windows-classic-sap-get-started.md#c5b77a14-f6b4-44e9-acab-4d28ff72a930
[getting-started-windows-classic-deployment]:virtual-machines-windows-classic-sap-get-started.md#f84ea6ce-bbb4-41f7-9965-34d31b0098ea
[getting-started-windows-classic-dr]:virtual-machines-windows-classic-sap-get-started.md#cff10b4a-01a5-4dc3-94b6-afb8e55757d3
[getting-started-windows-classic-ha-sios]:virtual-machines-windows-classic-sap-get-started.md#4bb7512c-0fa0-4227-9853-4004281b1037
[getting-started-windows-classic-planning]:virtual-machines-windows-classic-sap-get-started.md#f2a5e9d8-49e4-419e-9900-af783173481c

[ha-guide-classic]:http://go.microsoft.com/fwlink/?LinkId=613056

[ha-guide]:virtual-machines-windows-sap-high-availability-guide.md

[install-extension-cli]:virtual-machines-linux-enable-aem.md

[Logo_Linux]:./media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:./media/virtual-machines-shared-sap-shared/Windows.png

[msdn-set-azurermvmaemextension]:https://msdn.microsoft.com/library/azure/mt670598.aspx

[planning-guide]:virtual-machines-windows-sap-planning-guide.md(Windows VM(가상 컴퓨터)에서 SAP NetWeaver - 계획 및 구현 가이드) [planning-guide-1.2]:virtual-machines-windows-sap-planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff(리소스) [planning-guide-11]:virtual-machines-windows-sap-planning-guide.md#7cf991a1-badd-40a9-944e-7baae842a058(Azure 가상 컴퓨터에서 실행되는 SAP NetWeaver의 HA(고가용성) 및 DR(재해 복구)) [planning-guide-11.4.1]:virtual-machines-windows-sap-planning-guide.md#5d9d36f9-9058-435d-8367-5ad05f00de77(SAP 응용 프로그램 서버에 대한 고가용성) [planning-guide-11.5]:virtual-machines-windows-sap-planning-guide.md#4e165b58-74ca-474f-a7f4-5e695a93204f(SAP 인스턴스에 대해 자동 시작 사용) [planning-guide-2.1]:virtual-machines-windows-sap-planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803(클라우드 전용 - 온-프레미스 고객 네트워크에 의존하지 않고 Azure에 가상 컴퓨터 배포) [planning-guide-2.2]:virtual-machines-windows-sap-planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10(프레미스 간 - 온-프레미스 네트워크에 요구 사항을 완전히 통합하고 Azure에 단일 또는 다중 SAP VM 배포) [planning-guide-3.1]:virtual-machines-windows-sap-planning-guide.md#be80d1b9-a463-4845-bd35-f4cebdb5424a(Azure 지역) [planning-guide-3.2.1]:virtual-machines-windows-sap-planning-guide.md#df49dc09-141b-4f34-a4a2-990913b30358(장애 도메인) [planning-guide-3.2.2]:virtual-machines-windows-sap-planning-guide.md#fc1ac8b2-e54a-487c-8581-d3cc6625e560(업그레이드 도메인) [planning-guide-3.2.3]:virtual-machines-windows-sap-planning-guide.md#18810088-f9be-4c97-958a-27996255c665(Azure 가용성 집합) [planning-guide-3.2]:virtual-machines-windows-sap-planning-guide.md#8d8ad4b8-6093-4b91-ac36-ea56d80dbf77(Microsoft Azure Virtual Machines 개념) [planning-guide-3.3.2]:virtual-machines-windows-sap-planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92(Azure 프리미엄 Storage) [planning-guide-5.1.1]:virtual-machines-windows-sap-planning-guide.md#4d175f1b-7353-4137-9d2f-817683c26e53(일반화되지 않은 디스크를 사용하여 온-프레미스에서 Azure로 VM 이동) [planning-guide-5.1.2]:virtual-machines-windows-sap-planning-guide.md#e18f7839-c0e2-4385-b1e6-4538453a285c(고객별 이미지를 사용하여 VM 배포) [planning-guide-5.2.1]:virtual-machines-windows-sap-planning-guide.md#1b287330-944b-495d-9ea7-94b83aff73ef(일반화되지 않은 디스크를 사용하여 온-프레미스에서 Azure로의 VM 이동 준비) [planning-guide-5.2.2]:virtual-machines-windows-sap-planning-guide.md#57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3(SAP용 고객별 이미지를 사용하여 VM 배포 준비) [planning-guide-5.2]:virtual-machines-windows-sap-planning-guide.md#6ffb9f41-a292-40bf-9e70-8204448559e7(Azure용 SAP로 VM 준비) [planning-guide-5.3.1]:virtual-machines-windows-sap-planning-guide.md#6e835de8-40b1-4b71-9f18-d45b20959b79(Azure 디스크와 Azure 이미지 간 차이점) [planning-guide-5.3.2]:virtual-machines-windows-sap-planning-guide.md#a43e40e6-1acc-4633-9816-8f095d5a7b6a(온-프레미스에서 Azure로 VHD 업로드) [planning-guide-5.4.2]:virtual-machines-windows-sap-planning-guide.md#9789b076-2011-4afa-b2fe-b07a8aba58a1(Azure Storage 계정 간 디스크 복사) [planning-guide-5.5.1]:virtual-machines-windows-sap-planning-guide.md#4efec401-91e0-40c0-8e64-f2dceadff646(SAP 배포를 위한 VM/VHD 구조) [planning-guide-5.5.3]:virtual-machines-windows-sap-planning-guide.md#17e0d543-7e8c-4160-a7da-dd7117a1ad9d(연결된 디스크에 대한 자동 탑재 설정) [planning-guide-7.1]:virtual-machines-windows-sap-planning-guide.md#3e9c3690-da67-421a-bc3f-12c520d99a30(SAP NetWeaver 데모/학습 시나리오가 있는 단일 VM) [planning-guide-7]:virtual-machines-windows-sap-planning-guide.md#96a77628-a05e-475d-9df3-fb82217e8f14(SAP 인스턴스의 클라우드 전용 배포 개념) [planning-guide-9.1]:virtual-machines-windows-sap-planning-guide.md#6f0a47f3-a289-4090-a053-2521618a28c3(SAP용 Azure 모니터링 솔루션) [planning-guide-azure-premium-storage]:virtual-machines-windows-sap-planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92(Azure 프리미엄 Storage)

[planning-guide-figure-100]:./media/virtual-machines-shared-sap-planning-guide/100-single-vm-in-azure.png
[planning-guide-figure-1300]:./media/virtual-machines-shared-sap-planning-guide/1300-ref-config-iaas-for-sap.png
[planning-guide-figure-1400]:./media/virtual-machines-shared-sap-planning-guide/1400-attach-detach-disks.png
[planning-guide-figure-1600]:./media/virtual-machines-shared-sap-planning-guide/1600-firewall-port-rule.png
[planning-guide-figure-1700]:./media/virtual-machines-shared-sap-planning-guide/1700-single-vm-demo.png
[planning-guide-figure-1900]:./media/virtual-machines-shared-sap-planning-guide/1900-vm-set-vnet.png
[planning-guide-figure-200]:./media/virtual-machines-shared-sap-planning-guide/200-multiple-vms-in-azure.png
[planning-guide-figure-2100]:./media/virtual-machines-shared-sap-planning-guide/2100-s2s.png
[planning-guide-figure-2200]:./media/virtual-machines-shared-sap-planning-guide/2200-network-printing.png
[planning-guide-figure-2300]:./media/virtual-machines-shared-sap-planning-guide/2300-sapgui-stms.png
[planning-guide-figure-2400]:./media/virtual-machines-shared-sap-planning-guide/2400-vm-extension-overview.png
[planning-guide-figure-2500]:./media/virtual-machines-shared-sap-planning-guide/2500-vm-extension-details.png
[planning-guide-figure-2600]:./media/virtual-machines-shared-sap-planning-guide/2600-sap-router-connection.png
[planning-guide-figure-2700]:./media/virtual-machines-shared-sap-planning-guide/2700-exposed-sap-portal.png
[planning-guide-figure-2800]:./media/virtual-machines-shared-sap-planning-guide/2800-endpoint-config.png
[planning-guide-figure-2900]:./media/virtual-machines-shared-sap-planning-guide/2900-azure-ha-sap-ha.png
[planning-guide-figure-300]:./media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png
[planning-guide-figure-3000]:./media/virtual-machines-shared-sap-planning-guide/3000-sap-ha-on-azure.png
[planning-guide-figure-3200]:./media/virtual-machines-shared-sap-planning-guide/3200-sap-ha-with-sql.png
[planning-guide-figure-400]:./media/virtual-machines-shared-sap-planning-guide/400-vm-services.png
[planning-guide-figure-600]:./media/virtual-machines-shared-sap-planning-guide/600-s2s-details.png
[planning-guide-figure-700]:./media/virtual-machines-shared-sap-planning-guide/700-decision-tree-deploy-to-azure.png
[planning-guide-figure-800]:./media/virtual-machines-shared-sap-planning-guide/800-portal-vm-overview.png
[planning-guide-microsoft-azure-networking]:virtual-machines-windows-sap-planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd (Microsoft Azure Networking)
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:virtual-machines-windows-sap-planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f (Storage: Microsoft Azure Storage and Data Disks)

[sap-ha-guide]:virtual-machines-windows-sap-high-availability-guide.md (High-availability SAP NetWeaver on Windows virtual machines)
[sap-ha-guide-1]:virtual-machines-windows-sap-high-availability-guide.md#217c5479-5595-4cd8-870d-15ab00d4f84c (Prerequisites)
[sap-ha-guide-2]:virtual-machines-windows-sap-high-availability-guide.md#42b8f600-7ba3-4606-b8a5-53c4f026da08 (Resources)
[sap-ha-guide-3]:virtual-machines-windows-sap-high-availability-guide.md#42156640c6-01cf-45a9-b225-4baa678b24f1 (High-availability SAP with Azure Resource Manager vs. the classic deployment model)
[sap-ha-guide-3.1]:virtual-machines-windows-sap-high-availability-guide.md#f76af273-1993-4d83-b12d-65deeae23686 (Resource groups)
[sap-ha-guide-3.2]:virtual-machines-windows-sap-high-availability-guide.md#3e85fbe0-84b1-4892-87af-d9b65ff91860 (Clustering with Azure Resource Manager vs. the classic deployment model)
[sap-ha-guide-4]:virtual-machines-windows-sap-high-availability-guide.md#8ecf3ba0-67c0-4495-9c14-feec1a2255b7 (Windows Server Failover Clustering)
[sap-ha-guide-4.1]:virtual-machines-windows-sap-high-availability-guide.md#1a3c5408-b168-46d6-99f5-4219ad1b1ff2 (Quorum modes)
[sap-ha-guide-5]:virtual-machines-windows-sap-high-availability-guide.md#fdfee875-6e66-483a-a343-14bbaee33275 (Windows Server Failover Clustering on-premises)
[sap-ha-guide-5.1]:virtual-machines-windows-sap-high-availability-guide.md#be21cf3e-fb01-402b-9955-54fbecf66592 (Shared storage)
[sap-ha-guide-5.2]:virtual-machines-windows-sap-high-availability-guide.md#ff7a9a06-2bc5-4b20-860a-46cdb44669cd (Networking and name resolution)
[sap-ha-guide-6]:virtual-machines-windows-sap-high-availability-guide.md#2ddba413-a7f5-4e4e-9a51-87908879c10a (Windows Server Failover Clustering in Azure)
[sap-ha-guide-6.1]:virtual-machines-windows-sap-high-availability-guide.md#1a464091-922b-48d7-9d08-7cecf757f341 (Shared disk in Azure with SIOS DataKeeper)
[sap-ha-guide-6.2]:virtual-machines-windows-sap-high-availability-guide.md#44641e18-a94e-431f-95ff-303ab65e0bcb (Name resolution in Azure)
[sap-ha-guide-7]:virtual-machines-windows-sap-high-availability-guide.md#2e3fec50-241e-441b-8708-0b1864f66dfa(Azure IaaS의 SAP NetWeaver 고가용성) [sap-ha-guide-7.1]:virtual-machines-windows-sap-high-availability-guide.md#93faa747-907e-440a-b00a-1ae0a89b1c0e(고가용성 SAP 응용 프로그램 서버) [sap-ha-guide-7.2]:virtual-machines-windows-sap-high-availability-guide.md#f559c285-ee68-4eec-add1-f60fe7b978db(고가용성 SAP ASCS/SCS 인스턴스) [sap-ha-guide-7.2.1]:virtual-machines-windows-sap-high-availability-guide.md#b5b1fd0b-1db4-4d49-9162-de07a0132a51(Azure에서 Windows Server 장애 조치 클러스터링을 사용한 고가용성 SAP ASCS/SCS 인스턴스) [sap-ha-guide-7.3]:virtual-machines-windows-sap-high-availability-guide.md#ddd878a0-9c2f-4b8e-8968-26ce60be1027(고가용성 DBMS 인스턴스) [sap-ha-guide-7.4]:virtual-machines-windows-sap-high-availability-guide.md#045252ed-0277-4fc8-8f46-c5a29694a816(종단간 고가용성 배포 시나리오) [sap-ha-guide-8]:virtual-machines-windows-sap-high-availability-guide.md#78092dbe-165b-454c-92f5-4972bdbef9bf(인프라 준비) [sap-ha-guide-8.1]:virtual-machines-windows-sap-high-availability-guide.md#c87a8d3f-b1dc-4d2f-b23c-da4b72977489(프로덕션에서 사용하기 위해 회사 네트워크 연결(크로스-프로미스)을 사용하여 가상 시스템 배포) [sap-ha-guide-8.2]:virtual-machines-windows-sap-high-availability-guide.md#7fe9af0e-3cce-495b-a5ec-dcb4d8e0a310(테스트/데모용 SAP 인스턴스의 클라우드 전용 배포) [sap-ha-guide-8.3]:virtual-machines-windows-sap-high-availability-guide.md#47d5300a-a830-41d4-83dd-1a0d1ffdbe6a (Azure 가상 네트워크) [sap-ha-guide-8.4]:virtual-machines-windows-sap-high-availability-guide.md#b22d7b3b-4343-40ff-a319-097e13f62f9e(DNS IP 주소) [sap-ha-guide-8.5]:virtual-machines-windows-sap-high-availability-guide.md#9fbd43c0-5850-4965-9726-2a921d85d73f(SAP ASCS/SCS 클러스터형 인스턴스 및 DBMS 클러스터형 인스턴스의 호스트 이름 및 고정 IP 주소) [sap-ha-guide-8.6]:virtual-machines-windows-sap-high-availability-guide.md#84c019fe-8c58-4dac-9e54-173efd4b2c30(SAP 가상 컴퓨터에 대한 고정 IP 주소 설정) [sap-ha-guide-8.7]:virtual-machines-windows-sap-high-availability-guide.md#7a8f3e9b-0624-4051-9e41-b73fff816a9e(내부 부하 분산 장치의 정적 IP 주소 만들기) [sap-ha-guide-8.8]:virtual-machines-windows-sap-high-availability-guide.md#f19bd997-154d-4583-a46e-7f5a69d0153c(Azure 내부 부하 분산 장치에 대한 기본 ASCS/SCS 부하 분산 규칙) [sap-ha-guide-8.9]:virtual-machines-windows-sap-high-availability-guide.md#fe0bd8b5-2b43-45e3-8295-80bee5415716(Azure 내부 부하 분산 장치에 대한 ASCS/SCS 기본 부하 분산 규칙 변경) [sap-ha-guide-8.10]:virtual-machines-windows-sap-high-availability-guide.md#e69e9a34-4601-47a3-a41c-d2e11c626c0c(도메인에 Windows 컴퓨터 추가) [sap-ha-guide-8.11]:virtual-machines-windows-sap-high-availability-guide.md#661035b2-4d0f-4d31-86f8-dc0a50d78158(SAP ASCS/SCS 인스턴스의 클러스터 노드 둘 다에 대한 레지스트리 항목 추가) [sap-ha-guide-8.12]:virtual-machines-windows-sap-high-availability-guide.md#0d67f090-7928-43e0-8772-5ccbf8f59aab(SAP ASCS/SCS 인스턴스에 대한 Windows Server 장애 조치 클러스터 설정) [sap-ha-guide-8.12.1]:virtual-machines-windows-sap-high-availability-guide.md#5eecb071-c703-4ccc-ba6d-fe9c6ded9d79(클러스터 구성에서 클러스터 노드 수집) [sap-ha-guide-8.12.2]:virtual-machines-windows-sap-high-availability-guide.md#e49a4529-50c9-4dcf-bde7-15a0c21d21ca(클러스터 파일 공유 감시 구성) [sap-ha-guide-8.12.2.1]:virtual-machines-windows-sap-high-availability-guide.md#06260b30-d697-4c4d-b1c9-d22c0bd64855(파일 공유 만들기) [sap-ha-guide-8.12.2.2]:virtual-machines-windows-sap-high-availability-guide.md#4c08c387-78a0-46b1-9d27-b497b08cac3d(장애 조치 클러스터 관리자에서 파일 공유 감시 쿼럼 설정) [sap-ha-guide-8.12.3]:virtual-machines-windows-sap-high-availability-guide.md#5c8e5482-841e-45e1-a89d-a05c0907c868(SAP ASCS/SCS 클러스터 공유 디스크에 대한 SIOS DataKeeper Cluster Edition 설치) [sap-ha-guide-8.12.3.1]:virtual-machines-windows-sap-high-availability-guide.md#1c2788c3-3648-4e82-9e0d-e058e475e2a3(.NET Framework 3.5 추가) [sap-ha-guide-8.12.3.2]:virtual-machines-windows-sap-high-availability-guide.md#dd41d5a2-8083-415b-9878-839652812102(SIOS DataKeeper 설치) [sap-ha-guide-8.12.3.3]:virtual-machines-windows-sap-high-availability-guide.md#d9c1fc8e-8710-4dff-bec2-1f535db7b006(SIOS DataKeeper 설정) [sap-ha-guide-9]:virtual-machines-windows-sap-high-availability-guide.md#a06f0b49-8a7a-42bf-8b0d-c12026c5746b(SAP NetWeaver 시스템 설정) [sap-ha-guide-9.1]:virtual-machines-windows-sap-high-availability-guide.md#31c6bd4f-51df-4057-9fdf-3fcbc619c170(고가용성 ASCS/SCS 인스턴스를 포함하는 SAP 설치) [sap-ha-guide-9.1.1]:virtual-machines-windows-sap-high-availability-guide.md#a97ad604-9094-44fe-a364-f89cb39bf097(클러스터형 SAP ASCS/SCS에 대한 가상 호스트 이름 만들기) [sap-ha-guide-9.1.2]:virtual-machines-windows-sap-high-availability-guide.md#eb5af918-b42f-4803-bb50-eff41f84b0b0(SAP 첫 번째 클러스터 노드 설치) [sap-ha-guide-9.1.3]:virtual-machines-windows-sap-high-availability-guide.md#e4caaab2-e90f-4f2c-bc84-2cd2e12a9556(ASCS/SCS 인스턴스의 SAP 프로필 수정) [sap-ha-guide-9.1.4]:virtual-machines-windows-sap-high-availability-guide.md#10822f4f-32e7-4871-b63a-9b86c76ce761(프로브 포트 추가) [sap-ha-guide-9.2]:virtual-machines-windows-sap-high-availability-guide.md#85d78414-b21d-4097-92b6-34d8bcb724b7(데이터베이스 인스턴스 설치) [sap-ha-guide-9.3]:virtual-machines-windows-sap-high-availability-guide.md#8a276e16-f507-4071-b829-cdc0a4d36748(두 번째 클러스터 노드 설치) [sap-ha-guide-9.4]:virtual-machines-windows-sap-high-availability-guide.md#094bc895-31d4-4471-91cc-1513b64e406a(SAP ERS 인스턴스의 Windows 서비스 시작 유형 변경) [sap-ha-guide-9.5]:virtual-machines-windows-sap-high-availability-guide.md#2477e58f-c5a7-4a5d-9ae3-7b91022cafb5(SAP 기본 응용 프로그램 서버 설치) [sap-ha-guide-9.6]:virtual-machines-windows-sap-high-availability-guide.md#0ba4a6c1-cc37-4bcf-a8dc-025de4263772(SAP 추가 응용 프로그램 서버 설치) [sap-ha-guide-10]:virtual-machines-windows-sap-high-availability-guide.md#18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9(SAP ASCS/SCS 인스턴스 장애 조치 및 SIOS 복제 테스트) [sap-ha-guide-10.1]:virtual-machines-windows-sap-high-availability-guide.md#65fdef0f-9f94-41f9-b314-ea45bbfea445(SAP ASCS/SCS 인스턴스가 클러스터 노드 A에서 실행되고 있음) [sap-ha-guide-10.2]:virtual-machines-windows-sap-high-availability-guide.md#5e959fa9-8fcd-49e5-a12c-37f6ba07b916(노드 A에서 노드 B로의 장애 조치) [sap-ha-guide-10.3]:virtual-machines-windows-sap-high-availability-guide.md#755a6b93-0099-4533-9f6d-5c9a613878b5(SAP ASCS/SCS 인스턴스가 클러스터 노드 B에서 실행되고 있음))


[sap-ha-guide-figure-1000]:./media/virtual-machines-shared-sap-high-availability-guide/1000-wsfc-for-sap-ascs-on-azure.png
[sap-ha-guide-figure-1001]:./media/virtual-machines-shared-sap-high-availability-guide/1001-wsfc-on-azure-ilb.png
[sap-ha-guide-figure-1002]:./media/virtual-machines-shared-sap-high-availability-guide/1002-wsfc-sios-on-azure-ilb.png
[sap-ha-guide-figure-2000]:./media/virtual-machines-shared-sap-high-availability-guide/2000-wsfc-sap-as-ha-on-azure.png
[sap-ha-guide-figure-2001]:./media/virtual-machines-shared-sap-high-availability-guide/2001-wsfc-sap-ascs-ha-on-azure.png
[sap-ha-guide-figure-2003]:./media/virtual-machines-shared-sap-high-availability-guide/2003-wsfc-sap-dbms-ha-on-azure.png
[sap-ha-guide-figure-2004]:./media/virtual-machines-shared-sap-high-availability-guide/2004-wsfc-sap-ha-e2e-archit-template1-on-azure.png
[sap-ha-guide-figure-3000]:./media/virtual-machines-shared-sap-high-availability-guide/3000-template-parameters-sap-ha-arm-on-azure.png
[sap-ha-guide-figure-3001]:./media/virtual-machines-shared-sap-high-availability-guide/3001-configuring-dns-servers-for-Azure-vnet.png
[sap-ha-guide-figure-3002]:./media/virtual-machines-shared-sap-high-availability-guide/3002-configuring-static-IP-address-for-network-card-of-each-vm.png
[sap-ha-guide-figure-3003]:./media/virtual-machines-shared-sap-high-availability-guide/3003-setup-static-ip-address-ilb-for-ascs-instance.png
[sap-ha-guide-figure-3004]:./media/virtual-machines-shared-sap-high-availability-guide/3004-default-ascs-scs-ilb-balancing-rules-for-azure-ilb.png
[sap-ha-guide-figure-3005]:./media/virtual-machines-shared-sap-high-availability-guide/3005-changing-ascs-scs-default-ilb-rules-for-azure-ilb.png
[sap-ha-guide-figure-3006]:./media/virtual-machines-shared-sap-high-availability-guide/3006-adding-vm-to-domain.png
[sap-ha-guide-figure-3007]:./media/virtual-machines-shared-sap-high-availability-guide/3007-config-wsfc-1.png
[sap-ha-guide-figure-3008]:./media/virtual-machines-shared-sap-high-availability-guide/3008-config-wsfc-2.png
[sap-ha-guide-figure-3009]:./media/virtual-machines-shared-sap-high-availability-guide/3009-config-wsfc-3.png
[sap-ha-guide-figure-3010]:./media/virtual-machines-shared-sap-high-availability-guide/3010-config-wsfc-4.png
[sap-ha-guide-figure-3011]:./media/virtual-machines-shared-sap-high-availability-guide/3011-config-wsfc-5.png
[sap-ha-guide-figure-3012]:./media/virtual-machines-shared-sap-high-availability-guide/3012-config-wsfc-6.png
[sap-ha-guide-figure-3013]:./media/virtual-machines-shared-sap-high-availability-guide/3013-config-wsfc-7.png
[sap-ha-guide-figure-3014]:./media/virtual-machines-shared-sap-high-availability-guide/3014-config-wsfc-8.png
[sap-ha-guide-figure-3015]:./media/virtual-machines-shared-sap-high-availability-guide/3015-config-wsfc-9.png
[sap-ha-guide-figure-3016]:./media/virtual-machines-shared-sap-high-availability-guide/3016-config-wsfc-10.png
[sap-ha-guide-figure-3017]:./media/virtual-machines-shared-sap-high-availability-guide/3017-config-wsfc-11.png
[sap-ha-guide-figure-3018]:./media/virtual-machines-shared-sap-high-availability-guide/3018-config-wsfc-12.png
[sap-ha-guide-figure-3019]:./media/virtual-machines-shared-sap-high-availability-guide/3019-assign-permissions-on-share-for-cluster-name-object.png
[sap-ha-guide-figure-3020]:./media/virtual-machines-shared-sap-high-availability-guide/3020-change-object-type-include-computer-objects.png
[sap-ha-guide-figure-3021]:./media/virtual-machines-shared-sap-high-availability-guide/3021-check-box-for-computer-objects.png
[sap-ha-guide-figure-3022]:./media/virtual-machines-shared-sap-high-availability-guide/3022-set-security-attributes-for-cluster-name-object-on-file-share-quorum.png
[sap-ha-guide-figure-3023]:./media/virtual-machines-shared-sap-high-availability-guide/3023-call-configure-cluster-quorum-setting-wizard.png
[sap-ha-guide-figure-3024]:./media/virtual-machines-shared-sap-high-availability-guide/3024-selection-screen-different-quorum-configurations.png
[sap-ha-guide-figure-3025]:./media/virtual-machines-shared-sap-high-availability-guide/3025-selection-screen-file-share-witness.png
[sap-ha-guide-figure-3026]:./media/virtual-machines-shared-sap-high-availability-guide/3026-define-file-share-location-for-witness-share.png
[sap-ha-guide-figure-3027]:./media/virtual-machines-shared-sap-high-availability-guide/3027-successful-reconfiguration-cluster-file-share-witness.png
[sap-ha-guide-figure-3028]:./media/virtual-machines-shared-sap-high-availability-guide/3028-install-dot-net-framework-35.png
[sap-ha-guide-figure-3029]:./media/virtual-machines-shared-sap-high-availability-guide/3029-install-dot-net-framework-35-progress.png
[sap-ha-guide-figure-3030]:./media/virtual-machines-shared-sap-high-availability-guide/3030-sios-installer.png
[sap-ha-guide-figure-3031]:./media/virtual-machines-shared-sap-high-availability-guide/3031-first-screen-sios-data-keeper-installation.png
[sap-ha-guide-figure-3032]:./media/virtual-machines-shared-sap-high-availability-guide/3032-data-keeper-informs-service-be-disabled.png
[sap-ha-guide-figure-3033]:./media/virtual-machines-shared-sap-high-availability-guide/3033-user-selection-sios-data-keeper.png
[sap-ha-guide-figure-3034]:./media/virtual-machines-shared-sap-high-availability-guide/3034-domain-user-sios-data-keeper.png
[sap-ha-guide-figure-3035]:./media/virtual-machines-shared-sap-high-availability-guide/3035-provide-sios-data-keeper-license.png
[sap-ha-guide-figure-3036]:./media/virtual-machines-shared-sap-high-availability-guide/3036-data-keeper-management-config-tool.png
[sap-ha-guide-figure-3037]:./media/virtual-machines-shared-sap-high-availability-guide/3037-tcp-ip-address-first-node-data-keeper.png
[sap-ha-guide-figure-3038]:./media/virtual-machines-shared-sap-high-availability-guide/3038-create-replication-sios-job.png
[sap-ha-guide-figure-3039]:./media/virtual-machines-shared-sap-high-availability-guide/3039-define-sios-replication-job-name.png
[sap-ha-guide-figure-3040]:./media/virtual-machines-shared-sap-high-availability-guide/3040-define-sios-source-node.png
[sap-ha-guide-figure-3041]:./media/virtual-machines-shared-sap-high-availability-guide/3041-define-sios-target-node.png
[sap-ha-guide-figure-3042]:./media/virtual-machines-shared-sap-high-availability-guide/3042-define-sios-synchronous-replication.png
[sap-ha-guide-figure-3043]:./media/virtual-machines-shared-sap-high-availability-guide/3043-enable-sios-replicated-volume-as-cluster-volume.png
[sap-ha-guide-figure-3044]:./media/virtual-machines-shared-sap-high-availability-guide/3044-data-keeper-synchronous-mirroring-for-SAP-gui.png
[sap-ha-guide-figure-3045]:./media/virtual-machines-shared-sap-high-availability-guide/3045-replicated-disk-by-data-keeper-in-wsfc.png
[sap-ha-guide-figure-3046]:./media/virtual-machines-shared-sap-high-availability-guide/3046-dns-entry-sap-ascs-virtual-name-ip.png
[sap-ha-guide-figure-3047]:./media/virtual-machines-shared-sap-high-availability-guide/3047-dns-manager.png
[sap-ha-guide-figure-3048]:./media/virtual-machines-shared-sap-high-availability-guide/3048-default-cluster-probe-port.png
[sap-ha-guide-figure-3049]:./media/virtual-machines-shared-sap-high-availability-guide/3049-cluster-probe-port-after.png
[sap-ha-guide-figure-3050]:./media/virtual-machines-shared-sap-high-availability-guide/3050-service-type-ers-delayed-automatic.png
[sap-ha-guide-figure-5000]:./media/virtual-machines-shared-sap-high-availability-guide/5000-wsfc-sap-sid-node-a.png
[sap-ha-guide-figure-5001]:./media/virtual-machines-shared-sap-high-availability-guide/5001-sios-replicating-local-volume.png
[sap-ha-guide-figure-5002]:./media/virtual-machines-shared-sap-high-availability-guide/5002-wsfc-sap-sid-node-b.png
[sap-ha-guide-figure-5003]:./media/virtual-machines-shared-sap-high-availability-guide/5003-sios-replicating-local-volume-b-to-a.png

[sap-ha-guide-figure-6001]:./media/virtual-machines-shared-sap-high-availability-guide/6001-sap-multi-sid-ascs-scs-sid1.png
[sap-ha-guide-figure-6002]:./media/virtual-machines-shared-sap-high-availability-guide/6002-sap-multi-sid-ascs-scs.png
[sap-ha-guide-figure-6003]:./media/virtual-machines-shared-sap-high-availability-guide/6003-sap-multi-sid-full-landscape.png
[sap-ha-guide-figure-6004]:./media/virtual-machines-shared-sap-high-availability-guide/6004-sap-multi-sid-dns.png
[sap-ha-guide-figure-6005]:./media/virtual-machines-shared-sap-high-availability-guide/6005-sap-multi-sid-azure-portal.png
[sap-ha-guide-figure-6006]:./media/virtual-machines-shared-sap-high-availability-guide/6006-sap-multi-sid-sios-replication.png

[powershell-install-configure]:/powershell/azureps-cmdlets-docs
[resource-group-authoring-templates]:../resource-group-authoring-templates.md
[resource-group-overview]:../resource-group-overview.md
[resource-groups-networking]:../virtual-network/resource-groups-networking.md
[networking-limits-azure-resource-manager]:../azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits
[sap-pam]:https://support.sap.com/pam (SAP Product Availability Matrix)
[sap-templates-2-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-2-tier-os-disk]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-disk%2Fazuredeploy.json
[sap-templates-2-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-image%2Fazuredeploy.json
[sap-templates-3-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-3-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-user-image%2Fazuredeploy.json
[storage-azure-cli]:../storage/storage-azure-cli.md
[storage-azure-cli-copy-blobs]:../storage/storage-azure-cli.md#copy-blobs
[storage-introduction]:../storage/storage-introduction.md
[storage-powershell-guide-full-copy-vhd]:../storage/storage-powershell-guide-full.md#how-to-copy-blobs-from-one-storage-container-to-another
[storage-premium-storage-preview-portal]:../storage/storage-premium-storage.md
[storage-redundancy]:../storage/storage-redundancy.md
[storage-scalability-targets]:../storage/storage-scalability-targets.md
[storage-use-azcopy]:../storage/storage-use-azcopy.md
[template-201-vm-from-specialized-vhd]:https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd
[templates-101-simple-windows-vm]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-simple-windows-vm
[templates-101-vm-from-user-image]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image
[virtual-machines-linux-attach-disk-portal]:virtual-machines-linux-attach-disk-portal.md
[virtual-machines-windows-attach-disk-portal]:virtual-machines-windows-attach-disk-portal.md
[virtual-machines-azure-resource-manager-architecture]:../azure-resource-manager/resource-group-overview.md
[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../azure-resource-manager/resource-group-overview.md#the-benefits-of-using-resource-manager
[virtual-machines-azurerm-versus-azuresm]:virtual-machines-windows-compare-deployment-models.md
[virtual-machines-windows-classic-configure-oracle-data-guard]:virtual-machines-windows-classic-configure-oracle-data-guard.md
[virtual-machines-linux-cli-deploy-templates]:virtual-machines-linux-cli-deploy-templates.md (Deploy and manage virtual machines by using Azure Resource Manager templates and the Azure CLI)
[virtual-machines-deploy-rmtemplates-powershell]:virtual-machines-windows-ps-manage.md (Manage virtual machines using Azure Resource Manager and PowerShell)
[virtual-machines-linux-agent-user-guide]:virtual-machines-linux-agent-user-guide.md
[virtual-machines-linux-agent-user-guide-command-line-options]:virtual-machines-linux-agent-user-guide.md#command-line-options
[virtual-machines-linux-capture-image]:virtual-machines-linux-capture-image.md
[virtual-machines-linux-capture-image-capture]:virtual-machines-linux-capture-image.md#capture-the-vm
[virtual-machines-windows-capture-image]:virtual-machines-windows-capture-image.md
[virtual-machines-windows-capture-image-capture]:virtual-machines-windows-capture-image.md#capture-the-vm
[virtual-machines-linux-configure-lvm]:virtual-machines-linux-configure-lvm.md
[virtual-machines-linux-configure-raid]:virtual-machines-linux-configure-raid.md
[virtual-machines-linux-classic-create-upload-vhd-step-1]:virtual-machines-linux-classic-create-upload-vhd.md#step-1-prepare-the-image-to-be-uploaded
[virtual-machines-linux-create-upload-vhd-suse]:virtual-machines-linux-suse-create-upload-vhd.md
[virtual-machines-linux-redhat-create-upload-vhd]:virtual-machines-linux-redhat-create-upload-vhd.md
[virtual-machines-linux-how-to-attach-disk]:virtual-machines-linux-add-disk.md
[virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]:virtual-machines-linux-add-disk.md#connect-to-the-linux-vm-to-mount-the-new-disk
[virtual-machines-linux-tutorial]:virtual-machines-linux-quick-create-cli.md
[virtual-machines-linux-update-agent]:virtual-machines-linux-update-agent.md
[virtual-machines-manage-availability]:virtual-machines-windows-manage-availability.md
[virtual-machines-ps-create-preconfigure-windows-resource-manager-vms]:virtual-machines-windows-ps-create.md
[virtual-machines-sizes]:virtual-machines-windows-sizes.md
[virtual-machines-windows-classic-ps-sql-alwayson-availability-groups]:virtual-machines-windows-classic-ps-sql-alwayson-availability-groups.md
[virtual-machines-windows-classic-ps-sql-int-listener]:virtual-machines-windows-classic-ps-sql-int-listener.md
[virtual-machines-windows-portal-sql-alwayson-availability-groups-manual]:virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md
[virtual-machines-windows-portal-sql-alwayson-int-listener]:virtual-machines-windows-portal-sql-alwayson-int-listener.md
[virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions]:virtual-machines-windows-sql-high-availability-dr.md
[virtual-machines-sql-server-infrastructure-services]:virtual-machines-windows-sql-server-iaas-overview.md
[virtual-machines-sql-server-performance-best-practices]:virtual-machines-windows-sql-performance.md
[virtual-machines-upload-image-windows-resource-manager]:virtual-machines-windows-upload-image.md
[virtual-machines-windows-tutorial]:virtual-machines-windows-hero-tutorial.md
[virtual-machines-workload-template-sql-alwayson]:https://azure.microsoft.com/documentation/templates/sql-server-2014-alwayson-dsc/
[virtual-network-deploy-multinic-arm-cli]:../virtual-network/virtual-network-deploy-multinic-arm-cli.md
[virtual-network-deploy-multinic-arm-ps]:../virtual-network/virtual-network-deploy-multinic-arm-ps.md
[virtual-network-deploy-multinic-arm-template]:../virtual-network/virtual-network-deploy-multinic-arm-template.md
[virtual-networks-configure-vnet-to-vnet-connection]:../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md
[virtual-networks-create-vnet-arm-pportal]:../virtual-network/virtual-networks-create-vnet-arm-pportal.md
[virtual-networks-manage-dns-in-vnet]:../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md
[virtual-networks-multiple-nics]:../virtual-network/virtual-networks-multiple-nics.md
[virtual-networks-nsg]:../virtual-network/virtual-networks-nsg.md
[virtual-networks-reserved-private-ip]:../virtual-network/virtual-networks-static-private-ip-arm-ps.md
[virtual-networks-static-private-ip-arm-pportal]:../virtual-network/virtual-networks-static-private-ip-arm-pportal.md
[virtual-networks-udr-overview]:../virtual-network/virtual-networks-udr-overview.md

[load-balancer-multivip-overview]:../load-balancer/load-balancer-multivip-overview.md

[vpn-gateway-about-vpn-devices]:../vpn-gateway/vpn-gateway-about-vpn-devices.md
[vpn-gateway-create-site-to-site-rm-powershell]:../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md
[vpn-gateway-cross-premises-options]:../vpn-gateway/vpn-gateway-plan-design.md
[vpn-gateway-site-to-site-create]:../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md
[vpn-gateway-vpn-faq]:../vpn-gateway/vpn-gateway-vpn-faq.md
[xplat-cli]:../xplat-cli-install.md
[xplat-cli-azure-resource-manager]:../xplat-cli-azure-resource-manager.md


Microsoft는 9월에 [Azure Internal Load Balancers를 사용하여 여러 가상 IP 주소][load-balancer-multivip-overview]를 관리할 수 있도록 하는 기능도 출시했습니다. Azure External Load Balancers를 사용하는 기능이 이미 존재합니다.

SAP 배포의 경우 기본 [Windows Virtual Machines에서 SAP NetWeaver 고가용성 가이드][sap-ha-guide]에 설명된 대로 SAP의 ASCS/SCS에 대한 Windows 클러스터 구성을 만드는 데 Azure ILB를 사용합니다.

이 문서에서는 지금까지 설명서에 설명된 대로 추가 SAP ASCS/SCS 클러스터링된 인스턴스를 기존 WSFC(Windows 장애 조치 서버 클러스터)에 설치하여 이러한 단일 ASCS/SCS에서 SAP 다중 SID 구성으로 이동하는 방법을 중점적으로 설명합니다. 이러한 방식으로 SAP 다중 SID 클러스터를 구성했습니다.

> [!NOTE]
> 이 기능은 Azure Resource Manager 배포 모델에만 사용할 수 있습니다.
>
>

## <a name="prerequisites"></a>필수 조건
[Windows Virtual Machines의 SAP NetWeaver 고가용성 가이드][sap-ha-guide] 설명서에 따라 SAP ASCS/SCS 인스턴스 하나에 사용되는 WSFC를 구성했습니다.

![그림 1: 고가용성 SAP ASCS/SCS 인스턴스][sap-ha-guide-figure-6001]

_**그림 1:** Azure에서 하나의 SAP ASCS/SCS 클러스터링된 인스턴스_


## <a name="target-architecture"></a>대상 아키텍처

목적은 동일한 WSAFC 클러스터에서 여러 **SAP ABAP ASCS** 또는 **SAP Java SCS** 클러스터링된 인스턴스를 설치합니다.

![그림 2: Azure에서 여러 SAP ASCS/SCS 클러스터링된 인스턴스][sap-ha-guide-figure-6002]

_**그림 2:** Azure에서 여러 SAP ASCS/SCS 클러스터링된 인스턴스_

> [!NOTE]
>**Azure Internal Load Balancer당 개인 프런트 엔드 IP**에 대한 최대 제한이 있습니다.
>
>즉, **하나의 WSFC 클러스터에서 SAP ASCS/SCS 인스턴스의 최대수**가 **Azure Internal Load Balancer 당 개인 프런트 엔드 IP의 최대수**와 같습니다.
>

부하 분산 장치 제한에 대한 자세한 내용은 **네트워킹 제한 - Azure Resource Manager** 에서 [부하 분산 장치당 개인 프런트 엔드 IP][networking-limits-azure-resource-manager]를 참조하세요.

두 가지 고가용성 SAP 시스템을 포함한 전체의 큰 그림은 다음과 같습니다.

![그림 3: 두 개의 SAP 시스템을 포함한 SAP 고가용성 다중 SID 설치 프로그램][sap-ha-guide-figure-6003]

_**그림 3:** 두 개의 SAP 시스템을 포함한 SAP 고가용성 다중 SID 설치 프로그램_

> [!IMPORTANT]
> 중요 사항은 다음과 같습니다.
> - **SAP ASCS / SCS** 인스턴스는 **동일한 WSFC 클러스터를 공유**합니다.
> - 각 **DBMS SID**에는 해당하는 **고유한 전용 WSFC 클러스터**가 있습니다.
>- 하나의 SAP 시스템 SID에 속하는 **SAP 응용 프로그램 서버**에는 **고유한 전용 VM**이 있습니다.
>

## <a name="prepare-the-infrastructure"></a>인프라 준비
다음 매개 변수를 사용하여 **추가** SAP ASCS/SCS 인스턴스를 설치하려 한다고 가정합니다.

| 매개 변수 이름 | 값 |
| --- | --- |
|SAP ASC /SCS SID |pr1-lb-ascs |
| SAP DBMS 내부 부하 분산 장치 | PR5 |
| SAP 가상 호스트 이름 | pr5-sap-cl |
| SAP ASCS/SCS 가상 호스트 IP 주소(추가 Azure 부하 분산 IP 주소) | 10.0.0.50 |
| SAP ASCS/SCS 인스턴스 번호 | 50 |
| 추가 SAP ASCS/SCS 인스턴스의 ILB 프로브 포트 | 62350 |

> [!NOTE]
> SAP ASCS/SCS 클러스터링된 인스턴스의 경우 각 IP 주소에는 특정 프로브 포트가 필요합니다. 예를 들어 Azure Internal Load Balancer에 있는 하나의 IP 주소가 프로브 포트 62300를 사용하는 경우 해당 부하 분산 장치의 다른 IP 주소는 프로브 포트 62300를 사용할 수 없습니다.
>
>구체적으로 62300 프로브 포트가 이미 예약되어 있는 경우에는 다른 프로브 포트 62350를 사용합니다.
>

두 개의 노드를 포함한 **기존** WSFC 클러스터에서 추가 SAP ASCS/SCS 인스턴스를 설치하고 있습니다.

| 가상 컴퓨터 역할 | 가상 컴퓨터 호스트 이름 | 고정 IP 주소 |
| --- | --- | --- |
| ASCS/SCS 인스턴스의 첫 번째 클러스터 노드 |pr1-ascs-0 |10.0.0.10 |
| ASCS/SCS 인스턴스의 두 번째 클러스터 노드 |pr1-ascs-1 |10.0.0.9 |

### <a name="create-a-virtual-host-name-for-the-clustered-sap-ascsscs-instance-on-the-dns-server"></a>DNS 서버에서 클러스터형 SAP ASCS/SCS 인스턴스의 가상 호스트 이름 만들기

다음 매개 변수를 사용하여 ASCS/SCS 인스턴스의 가상 호스트 이름에 대한 DNS 항목을 만듭니다.

| 새 SAP ASCS/SCS 가상 호스트 이름 | 연결된 IP 주소 |
| --- | --- | --- |
|pr5-sap-cl |10.0.0.50 |

![그림 4: 새로운 추가 SAP ASCS/SCS 클러스터 가상 이름 및 TCP/IP 주소에 대한 DNS 항목 정의][sap-ha-guide-figure-6004]

_**그림 4:** 새로운 추가 SAP ASCS/SCS 클러스터 가상 이름 및 TCP/IP 주소에 대한 DNS 항목 정의_

DNS 항목을 만드는 방법의 절차는 기본 [Windows VM(가상 컴퓨터)의 SAP NetWeaver - 고가용성 가이드][sap-ha-guide-9.1.1]에서 자세히 설명합니다.

> [!NOTE]
> 추가 ASCS/SCS 인스턴스의 가상 호스트 이름에 할당하는 새 IP 주소는 SAP Azure Load Balancer에 할당한 새 IP 주소와 동일해야 합니다.
>
>구체적으로는 10.0.0.50 IP 주소입니다.

### <a name="add-an-ip-address-to-an-existing-azure-internal-load-balancer-with-powershell"></a>PowerShell을 사용하여 기존 Azure Internal Load Balancer에 IP 주소 추가

동일한 WSFC 클러스터에서 둘 이상의 SAP ASCS/SCS 인스턴스를 사용하려면 PowerShell을 사용하여 추가 IP 주소를 기존 Azure Internal Load Balancer에 추가합니다. 각 IP 주소에는 고유한 부하 분산 규칙, 프로브 포트, 프런트 엔드 IP 풀 및 백 엔드 풀이 필요합니다.

다음 스크립트는 기존 부하 분산 장치에 새 IP 주소를 추가합니다. 사용자 환경에 맞게 PowerShell 변수를 업데이트합니다. 스크립트는 모든 SAP ASCS /SCS 포트에 필요한 모든 부하 분산 규칙을 만듭니다.

```powershell

# Login-AzureRmAccount
# Select-AzureRmSubscription -SubscriptionId <xxxxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx>
Clear-Host
$ResourceGroupName = "SAP-MULTI-SID-Landscape"      # Existing Resource group name
$VNetName = "pr2-vnet"                        # Existing Virtual network name
$SubnetName = "Subnet"                        # Existing Subnet name
$ILBName = "pr2-lb-ascs"                      # Existing ILB name                      
$ILBIP = "10.0.0.50"                          # New IP address
$VMNames = "pr2-ascs-0","pr2-ascs-1"          # Existing cluster Virtual machine names
$SAPInstanceNumber = 50                       # SAP ASCS/SCS Instance Number - must be unique value per cluster
[int]$ProbePort = "623$SAPInstanceNumber"     # Probe port - MUST be unique value per IP and load balancer

$ILB = Get-AzureRmLoadBalancer -Name $ILBName -ResourceGroupName $ResourceGroupName

$count = $ILB.FrontendIpConfigurations.Count + 1
$FrontEndConfigurationName ="lbFrontendASCS$count"
$LBProbeName = "lbProbeASCS$count"

# Get the Azure VNet and Subnet
$VNet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName
$Subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $VNet -Name $SubnetName

# Add Second Frontend and Probe config
Write-Host "Adding new front end IP Pool '$FrontEndConfigurationName' ..." -ForegroundColor Green
$ILB | Add-AzureRmLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -PrivateIpAddress $ILBIP -SubnetId $Subnet.Id
$ILB | Add-AzureRmLoadBalancerProbeConfig -Name $LBProbeName  -Protocol Tcp -Port $Probeport -ProbeCount 2 -IntervalInSeconds 10  | Set-AzureRmLoadBalancer

#Get new updated config
$ILB = Get-AzureRmLoadBalancer -Name $ILBname -ResourceGroupName $ResourceGroupName
# Get new updated LP FrontendIP COnfig
$FEConfig = Get-AzureRmLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -LoadBalancer $ILB
$HealthProbe  = Get-AzureRmLoadBalancerProbeConfig -Name $LBProbeName -LoadBalancer $ILB

# Add new backend config into existign ILB
$BackEndConfigurationName  = "backendPoolASCS$count"
Write-Host "Adding new backend Pool '$BackEndConfigurationName' ..." -ForegroundColor Green
$BEConfig = Add-AzureRmLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName -LoadBalancer $ILB | Set-AzureRmLoadBalancer



#Get new updated config
$ILB = Get-AzureRmLoadBalancer -Name $ILBname -ResourceGroupName $ResourceGroupName

# Assign VM NICs to backend pool
$BEPool = Get-AzureRmLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName -LoadBalancer $ILB
foreach($VMName in $VMNames){
        $VM = Get-AzureRmVM -ResourceGroupName $ResourceGroupName -Name $VMName
        $NICName = ($VM.NetworkInterfaceIDs[0].Split('/') | select -last 1)        
        $NIC = Get-AzureRmNetworkInterface -name $NICName -ResourceGroupName $ResourceGroupName                
        $NIC.IpConfigurations[0].LoadBalancerBackendAddressPools += $BEPool
        Write-Host "Assigning network card '$NICName' of the '$VMName' VM to the backend pool '$BackEndConfigurationName' ..." -ForegroundColor Green
        Set-AzureRmNetworkInterface -NetworkInterface $NIC
        #start-AzureRmVM -ResourceGroupName $ResourceGroupName -Name $VM.Name
}


# Create Load Balancing Rules
$Ports = "445","32$SAPInstanceNumber","33$SAPInstanceNumber","36$SAPInstanceNumber","39$SAPInstanceNumber","5985","81$SAPInstanceNumber","5$SAPInstanceNumber`13","5$SAPInstanceNumber`14","5$SAPInstanceNumber`16"
$ILB = Get-AzureRmLoadBalancer -Name $ILBname -ResourceGroupName $ResourceGroupName
$FEConfig = get-AzureRMLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -LoadBalancer $ILB
$BEConfig = Get-AzureRmLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName -LoadBalancer $ILB
$HealthProbe  = Get-AzureRmLoadBalancerProbeConfig -Name $LBProbeName -LoadBalancer $ILB

Write-Host "Creating load balancing rules for the ports: '$Ports' ... " -ForegroundColor Green

foreach ($Port in $Ports) {

        $LBConfigrulename = "lbrule$Port" + "_$count"
        Write-Host "Creating load balancing rule '$LBConfigrulename' for the port '$Port' ..." -ForegroundColor Green

        $ILB | Add-AzureRmLoadBalancerRuleConfig -Name $LBConfigRuleName -FrontendIpConfiguration $FEConfig  -BackendAddressPool $BEConfig -Probe $HealthProbe -Protocol tcp -FrontendPort  $Port -BackendPort $Port -IdleTimeoutInMinutes 30 -LoadDistribution Default -EnableFloatingIP   
}

$ILB | Set-AzureRmLoadBalancer

Write-Host "Succesfully added new IP '$ILBIP' to the internal load balancer '$ILBName'!" -ForegroundColor Green


```

스크립트가 완료되면 Azure Portal에서 결과를 볼 수 있습니다.

![그림 5: Azure 포트에서 새 프런트 엔드 IP 풀][sap-ha-guide-figure-6005]

_**그림 5:** Azure 포트에서 새 프런트 엔드 IP 풀_

### <a name="add-additional-disks-to-cluster-machines-and-configure-sios-cluster-share-disk"></a>클러스터 컴퓨터에 추가 디스크 추가 및 SIOS 클러스터 공유 디스크 구성

새로운 추가 SAP ASCS/SCS 인스턴스에 새 클러스터 공유 디스크가 필요합니다. Windows Server 2012 R2 WSFC의 경우 현재 클러스터 공유 디스크는 SIOS DataKeeper 소프트웨어 솔루션으로 사용됩니다.

따라서
-   각 클러스터 노드와 동일한 크기의 추가 디스크(또는 스트라이프해야 하는 더 많은 디스크)를 추가하고 서식을 지정합니다.
-   SIOS DataKeeper를 사용하여 저장소 복제를 구성합니다.

WSFC 클러스터 컴퓨터에서 SIOS DataKeeper를 이미 설치했다고 가정하므로 그 사이에 복제를 구성해야 합니다.

이 모든 사항은 기본 [Windows VM(가상 컴퓨터)의 SAP NetWeaver - 고가용성 가이드][sap-ha-guide-8.12.3.3]에서 자세히 설명합니다.  

![그림 6: 새 SAP ASCS/SCS 공유 디스크에 대한 DataKeeper 동기 미러링이 활성화됨][sap-ha-guide-figure-6006]

_**그림 6:** 새 SAP ASCS/SCS 공유 디스크에 대한 DataKeeper 동기 미러링이 활성화됨_

### <a name="deploy-vms-for-sap-application-servers-and-dbms-cluster"></a>SAP 응용 프로그램 서버 및 DBMS 클러스터에 대한 VM 배포

두 번째 SAP 시스템에 대한 인프라 준비를 완료하려면 다음을 수행해야 합니다.

- SAP 응용 프로그램 서버에 대한 전용 VM 배포 및 고유한 전용 가용성 그룹에 배치
- DBMS 클러스터에 대한 전용 VM 배포 및 고유한 전용 가용성 그룹에 배치


## <a name="install-the-second-sap-sid2-netweaver-system"></a>두 번째 SAP SID2 NetWeaver 시스템 설치

이제 두 번째 SAP **SID2** 시스템을 설치할 수 있습니다.

전체 프로시저는 기본 [Windows VM(가상 컴퓨터)의 SAP NetWeaver - 고가용성 가이드][sap-ha-guide-9]에서 설명합니다.

고급 프로시저 설명은 다음과 같습니다.

- [SAP 첫 번째 클러스터 노드 설치][sap-ha-guide-9.1.2]

    **EXISTING WSFC 클러스터 노드 1**에 고가용성 ASCS/SCS 인스턴스를 포함한 SAP를 설치합니다.

- [ASCS/SCS 인스턴스의 SAP 프로필 수정][sap-ha-guide-9.1.3]

- [프로브 포트 구성][sap-ha-guide-9.1.4]

    PowerShell을 사용하여 SAP 클러스터 리소스 **SAP-SID2-IP** 프로브 포트를 구성합니다. SAP ASCS/SCS 클러스터 노드 중 하나에서 이를 실행합니다.

- [데이터베이스 인스턴스 설치][sap-ha-guide-9.2]

    **전용 WSFC 클러스터**에 DBMS를 설치합니다.

- [두 번째 클러스터 노드 설치][sap-ha-guide-9.3]

    **EXISTING WSFC 클러스터 노드 2**에 고가용성 ASCS/SCS 인스턴스를 포함한 SAP를 설치합니다.

- **SAP ASCS /SCS 인스턴스 및 ProbePort의 Windows 방화벽 포트 열기**

    SAP ASCS/SCS 인스턴스에 사용되는 두 클러스터 노드에서 SAP ASCS/SCS 포트에서 사용하는 모든 Windows 방화벽 포트를 엽니다. 이러한 포트는 [여기][sap-ha-guide-8.8]에 나열됩니다.

    또한 62350 사례에서와 같이 Azure Internal Load Balancer 프로브 포트를 엽니다.

- [SAP ERS Windows 서비스 인스턴스의 시작 유형 변경][sap-ha-guide-9.4]

- [SAP 기본 응용 프로그램 서버 설치][sap-ha-guide-9.5]

    **새 전용 VM**에 SAP 기본 응용 프로그램 서버를 설치합니다.

- [SAP 추가 응용 프로그램 서버 설치][sap-ha-guide-9.6]

    **새 전용 VM**에 SAP SID2 추가 응용 프로그램 서버를 설치합니다.

- [SAP ASCS/SCS 인스턴스 장애 조치 및 SIOS 복제 테스트][sap-ha-guide-10]

## <a name="next-steps"></a>다음 단계

- [네트워킹 제한 - Azure Resource Manager][networking-limits-azure-resource-manager]
- [Azure Load Balancer에 대한 다중 VIP][load-balancer-multivip-overview]
- [Windows Virtual Machines의 기본 고가용성 SAP NetWeaver 가이드][sap-ha-guide]



<!--HONumber=Dec16_HO2-->


