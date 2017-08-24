
# 개요
## [Azure Backup이란?](backup-introduction-to-azure-backup.md)

# 시작
## [Azure VM 백업](backup-azure-vms-first-look-arm.md)
## [Windows Server 또는 Windows 컴퓨터 백업](backup-try-azure-backup-in-10-mins.md)
## [VMware 서버 백업](backup-azure-backup-server-vmware.md)

# 방법
## Azure VM
### VM 준비
#### [리소스 관리자가 배포된 가상 컴퓨터 준비](backup-azure-arm-vms-prepare.md)
#### [Azure 가상 컴퓨터 준비](backup-azure-vms-prepare.md)
### 환경 계획
#### [VM 백업 인프라 계획](backup-azure-vms-introduction.md)
### VM 백업
#### [Recovery Services 자격 증명 모음에 Azure 가상 컴퓨터 백업](backup-azure-arm-vms.md)
#### [암호화된 가상 컴퓨터 백업](backup-azure-vms-encryption.md)
#### [Azure 가상 컴퓨터 백업](backup-azure-vms.md)
### VM 관리 및 모니터링
#### [Azure 포털에서 Azure VM 백업 관리](backup-azure-manage-vms.md)
#### [Azure 포털에서 Azure VM 백업에 대한 경고 모니터링](backup-azure-monitor-vms.md)
#### [클래식 포털에서 Azure VM 백업 관리 및 모니터링](backup-azure-manage-vms-classic.md)
### VM에서 데이터 복원
#### [Azure VM 백업에서 파일 복구](backup-azure-restore-files-from-vm.md)
#### [Azure 포털에서 Resource Manager 배포 VM 복구](backup-azure-arm-restore-vms.md)
#### [암호화된 가상 컴퓨터 복원](backup-azure-vms-encryption.md)
#### [Azure의 가상 컴퓨터 복원](backup-azure-restore-vms.md)
#### [암호화된 VM의 Key Vault 키 및 비밀 복원](backup-azure-restore-key-secret.md)


## Windows Server
### [Windows Server 파일 및 폴더 백업](backup-configure-vault.md)
### [Windows Server 시스템 상태 백업](backup-azure-system-state.md)
### [Azure에서 Windows Server로 파일 복구](backup-azure-restore-windows-server.md)
### [Windows Server 시스템 상태 복원](backup-azure-restore-system-state.md)
### [Recovery Services 자격 증명 모음 모니터링 및 관리](backup-azure-manage-windows-server.md)
### 클래식 포털을 사용한 백업 및 복원
#### [클래식 배포 모델을 사용하는 Windows Server](backup-configure-vault-classic.md)
#### [클래식 배포 모델을 사용하여 Backup 자격 증명 모음 관리](backup-azure-manage-windows-server-classic.md)
#### [클래식 배포 모델을 사용하여 Windows 서버로 파일 복구](backup-azure-restore-windows-server-classic.md)

## Azure 백업 서버
### [Azure Backup Server 보호 매트릭스](backup-mabs-protection-matrix.md)
### 설치 또는 업그레이드
#### [Azure 포털에서 Azure Backup Server 작업 준비](backup-azure-microsoft-azure-backup.md)
#### [클래식 포털에서 Azure Backup Server 작업 준비](backup-azure-microsoft-azure-backup-classic.md)
#### [Azure Backup Server에 저장소 추가](backup-mabs-add-storage.md)
#### [Azure Backup Server를 v.2로 업그레이드](backup-mabs-upgrade-to-v2.md)
#### [Azure Backup Server의 무인 설치](backup-mabs-unattended-install.md)
### 워크로드 보호
#### [Azure Backup Server를 사용하여 VMware 서버 백업](backup-azure-backup-server-vmware.md)
#### [Azure Backup Server를 사용하여 Exchange 백업](backup-azure-exchange-mabs.md)
#### [Azure Backup Server를 사용하여 SharePoint 팜 백업](backup-azure-backup-sharepoint-mabs.md)
#### [Azure Backup Server를 사용하여 SQL 백업](backup-azure-sql-mabs.md)
#### [시스템 상태 및 완전 복구 보호](backup-mabs-system-state-and-bmr.md)
### [Azure Backup Server에서 데이터 복구](backup-azure-alternate-dpm-server.md)

## Data Protection Manager
### [Azure 포털에서 DPM 작업 준비](backup-azure-dpm-introduction.md)
### [클래식 포털에서 DPM 작업 준비](backup-azure-dpm-introduction-classic.md)
### [Exchange 서버를 백업하기 위해 System Center DPM 사용](backup-azure-backup-exchange-server.md)
### [다른 DPM 서버에 데이터 복구](backup-azure-alternate-dpm-server.md)
### [SQL Server 작업을 백업하기 위해 DPM 사용](backup-azure-backup-sql.md)
### [SharePoint 팜을 백업하기 위해 DPM 사용](backup-azure-backup-sharepoint.md)

## Azure SQL 데이터베이스
### [장기 백업 보존 구성](../sql-database/sql-database-configure-long-term-retention.md?toc=%2fazure%2fbackup%2ftoc.json)
### [Recovery Services 자격 증명 모음에서 백업 확인](../sql-database/sql-database-view-backups-in-vault.md?toc=%2fazure%2fbackup%2ftoc.json)
### [장기 백업 보존에서 복원](../sql-database/sql-database-restore-from-long-term-retention.md?toc=%2fazure%2fbackup%2ftoc.json)
### [장기 Azure SQL 백업 삭제](../sql-database/sql-database-long-term-retention-delete.md?toc=%2fazure%2fbackup%2ftoc.json)

## PowerShell 사용
### [Azure 포털의 Azure VM](backup-azure-vms-automation.md)
### [클래식 포털의 Azure VM](backup-azure-vms-classic-automation.md)
### [Azure 포털의 DPM](backup-dpm-automation.md)
### [클래식 포털의 DPM](backup-dpm-automation-classic.md)
### [Azure 포털의 Windows Server](backup-client-automation.md)
### [클래식 포털의 Windows Server](backup-client-automation-classic.md)

## FAQ
### [Recovery Services 자격 증명 모음에 대한 FAQ](backup-azure-backup-faq.md)
### [Azure VM 백업에 대한 FAQ](backup-azure-vm-backup-faq.md)
### [Azure Backup 에이전트를 사용하여 파일-폴더 백업에 대한 FAQ](backup-azure-file-folder-backup-faq.md)

## 문제 해결
### [Azure 포털에서 Azure VM 백업 문제](backup-azure-vms-troubleshoot.md)
### [클래식 포털에서 Azure VM 백업 문제](backup-azure-vms-troubleshoot-classic.md)
### [Azure VM Backup 실패: 스냅숏 상태에 대해 VM 에이전트와 통신할 수 없음 - 스냅숏 VM 하위 작업 시간 초과](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md)
### [Azure Backup에서 파일 및 폴더의 느린 백업](backup-azure-troubleshoot-slow-backup-performance-issue.md)
### [Azure Backup Server 문제 해결](backup-azure-mabs-troubleshoot.md)

# 개념
## [Recovery Services 자격 증명 모음 개요](backup-azure-recovery-services-vault-overview.md)
## [Recovery Services 자격 증명 모음으로 백업 자격 증명 모음 업그레이드](backup-azure-upgrade-backup-to-recovery-services.md)
## [복구 서비스 자격 증명 모음 삭제](backup-azure-delete-vault.md)
## [역할 기반 액세스 제어](backup-rbac-rs-vault.md)
## [하이브리드 백업 보안](backup-azure-security-feature.md)
## [Azure Backup 보고서 구성](backup-azure-configure-reports.md)
## [Azure Backup 보고서용 데이터 모델](backup-azure-reports-data-model.md)
## [Azure Backup용 Log Analytics 데이터 모델](backup-azure-log-analytics-data-model.md)
## [오프라인 백업 구성](backup-azure-backup-import-export.md)
## [테이프 라이브러리 교체](backup-azure-backup-cloud-as-tape.md)
## [Linux VM의 응용 프로그램 일치 백업](backup-azure-linux-app-consistent.md)

# 참조
## [PowerShell](/powershell/module/azurerm.recoveryservices.backup)
## [.NET](/dotnet/api/microsoft.azure.management.recoveryservices.backup)

# 리소스
## [Azure 로드맵](https://azure.microsoft.com/roadmap/)
## [MSDN 포럼](https://social.msdn.microsoft.com/Forums/en-US/home?forum=windowsazureonlinebackup)
## [가격](https://azure.microsoft.com/pricing/details/backup/)
## [요금 계산기](https://azure.microsoft.com/pricing/calculator/)
## [서비스 업데이트](https://azure.microsoft.com/updates/?product=backup)
## [비디오](https://azure.microsoft.com/documentation/videos/index/?services=backup)
