---
title: Azure Active Directory 인증
description: Azure SQL Database, Azure SQL Managed Instance 및 Azure Synapse Analytics를 사용 하 여 인증을 위해 Azure Active Directory를 사용 하는 방법에 대해 알아봅니다.
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.custom: azure-synapse, sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto, carlrab
ms.date: 04/23/2020
ms.openlocfilehash: e1f97b1a29681a65cddf3a5904020555e903fe63
ms.sourcegitcommit: 93462ccb4dd178ec81115f50455fbad2fa1d79ce
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/06/2020
ms.locfileid: "85984778"
---
# <a name="use-azure-active-directory-authentication"></a>Azure Active Directory 인증 사용

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Azure ad (Azure Active Directory) 인증은 Azure AD에서 id를 사용 하 여 [Azure SQL Database](sql-database-paas-overview.md), [azure SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md)및 [azure Synapse Analytics (이전의 Azure SQL Data Warehouse)](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) 에 연결 하는 메커니즘입니다.

> [!NOTE]
> 이 문서는 Azure SQL Database, SQL Managed Instance 및 Azure Synapse Analytics에 적용 됩니다.

Azure AD 인증을 사용하면 데이터베이스 사용자 및 다른 Microsoft 서비스의 ID를 하나의 중앙 위치에서 관리할 수 있습니다. 중앙 ID 관리는 데이터베이스 사용자 관리를 위한 단일 위치를 제공하며 권한 관리를 간소화합니다. 이점은 다음과 같습니다.

- SQL Server 인증에 대한 대안을 제공합니다.
- 서버 전체에서 사용자 id의 확산을 중지 하는 데 도움이 됩니다.
- 한 곳에서 암호를 회전할 수 있습니다.
- 고객이 외부(Azure AD) 그룹을 사용하여 데이터베이스 사용 권한을 관리할 수 있습니다.
- Windows 통합 인증 또는 Azure Active Directory에서 지원하는 기타 인증을 사용하여 암호 저장을 제거할 수 있습니다.
- Azure AD 인증에서는 포함된 데이터베이스 사용자를 통해 데이터베이스 수준에서 ID를 인증합니다.
- Azure AD는 SQL Database 및 SQL Managed Instance에 연결 하는 응용 프로그램에 대 한 토큰 기반 인증을 지원 합니다.
- Azure AD 인증은 다음을 지원 합니다.
  - Azure AD 클라우드 전용 id입니다.
  - 다음을 지 원하는 Azure AD 하이브리드 id:
    - 두 옵션을 포함 하는 클라우드 인증은 원활한 Single Sign-On (SSO) **통과** 인증 및 **암호 해시** 인증과 결합 됩니다.
    - 페더레이션 인증.
  - Azure AD 인증 방법 및 선택할 방법에 대 한 자세한 내용은 다음 문서를 참조 하세요.
    - [Azure Active Directory 하이브리드 ID 솔루션에 적합한 인증 방법 선택](../../active-directory/hybrid/choose-ad-authn.md)

- Azure AD는 Multi-Factor Authentication을 비롯한 Active Directory 유니버설 인증을 사용하는 SQL Server Management Studio를 통해 연결하도록 지원합니다. Multi-Factor Authentication에는 전화 통화, 문자 메시지, pin을 사용 하는 스마트 카드, 모바일 앱 알림 등의 여러 가지 간편한 확인 옵션을 갖춘 강력한 인증이 포함 되어 있습니다. 자세한 내용은 [Azure SQL Database, SQL Managed Instance 및 Azure Synapse를 사용 하 여 AZURE AD Multi-Factor Authentication에 대 한 SSMS 지원](authentication-mfa-ssms-overview.md) 을 참조 하세요.

- Azure AD는 Active Directory 대화형 인증을 사용하는 유사한 SSDT(SQL Server Data Tools) 연결을 지원합니다. 자세한 내용은 [Azure Active Directory support in SQL Server Data Tools (SSDT) (](/sql/ssdt/azure-active-directory) 영문으로 표시 될 수 있습니다.

> [!NOTE]  
> Azure VM (가상 컴퓨터)에서 실행 되는 SQL Server 인스턴스에 연결 하는 것은 Azure Active Directory 계정을 사용 하 여 지원 되지 않습니다. 대신 도메인 Active Directory 계정을 사용합니다.  

구성 단계에는 Azure Active Directory 인증의 구성 및 사용을 위한 다음 절차가 포함됩니다.

1. Azure AD를 만들고 채웁니다.
2. 옵션: 현재 Azure 구독과 연결된 Active Directory를 연결하거나 변경합니다.
3. Azure Active Directory 관리자를 만듭니다.
4. 클라이언트 컴퓨터를 구성합니다.
5. Azure AD ID에 매핑된 데이터베이스에서 포함된 데이터베이스 사용자를 만듭니다.
6. Azure AD ID를 사용하여 데이터베이스에 연결합니다.

> [!NOTE]
> Azure AD를 만들고 채운 다음 Azure SQL Database, SQL Managed Instance 및 Azure Synapse를 사용 하 여 Azure AD를 구성 하는 방법을 알아보려면 [Azure SQL Database를 사용 하 여 AZURE Ad 구성](authentication-aad-configure.md)을 참조 하세요.

## <a name="trust-architecture"></a>트러스트 아키텍처

- Azure ad, SQL Database, SQL Managed Instance 및 Azure Synapse의 클라우드 부분만 Azure AD 네이티브 사용자 암호를 지 원하는 것으로 간주 됩니다.
- Windows Single Sign-On 자격 증명 (또는 Windows 자격 증명에 대 한 사용자/암호)을 지원 하려면 통과 및 암호 해시 인증을 위해 원활한 Single Sign-On 구성 된 페더레이션 또는 관리 되는 도메인의 자격 증명 Azure Active Directory 사용 합니다. 자세한 내용은 [Azure Active Directory Seamless Single Sign-On](../../active-directory/hybrid/how-to-connect-sso.md)을 참조하세요.
- 페더레이션 인증(또는 Windows 자격 증명에 대한 사용자/암호)을 지원하려면 ADFS 블록과의 통신이 필요합니다.

Azure AD 하이브리드 id, 설치 및 동기화에 대 한 자세한 내용은 다음 문서를 참조 하세요.

- 암호 해시 인증- [Azure AD Connect 동기화를 사용 하 여 암호 해시 동기화 구현](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md)
- 통과 인증- [Azure Active Directory 통과 인증](../../active-directory/hybrid/how-to-connect-pta-quick-start.md)
- 페더레이션된 인증-Azure 및 [Azure AD Connect 및 페더레이션](../../active-directory/hybrid/how-to-connect-fed-whatis.md) [에서 Active Directory Federation Services 배포](/windows-server/identity/ad-fs/deployment/how-to-connect-fed-azure-adfs)

ADFS 인프라를 사용 하는 샘플 페더레이션 인증 (또는 Windows 자격 증명에 대 한 사용자/암호)은 아래 다이어그램을 참조 하세요. 화살표는 통신 경로 나타냅니다.

![aad auth 다이어그램][1]

다음 다이어그램은 토큰을 제출하여 클라이언트가 데이터베이스에 연결할 수 있는 페더레이션, 신뢰 및 호스팅 관계를 나타냅니다. 이 토큰은 Azure AD를 통해 인증되고 데이터베이스에서 신뢰됩니다. 고객 1은 기본 사용자가 있는 Azure Active Directory 또는 페더레이션된 사용자가 있는 Azure AD를 나타낼 수 있습니다. 고객 2는 가져온 사용자를 포함 하 여 가능한 솔루션을 나타냅니다 .이 예에서는 Azure Active Directory와 동기화 되는 ADFS를 사용 하 여 페더레이션된 Azure Active Directory에서 가져옵니다. Azure AD 인증을 사용하는 데이터베이스에 액세스하려면 호스팅 구독이 Azure AD에 연결되어 있어야 합니다. Azure SQL Database, SQL Managed Instance 또는 Azure Synapse 리소스를 만들려면 동일한 구독을 사용 해야 합니다.

![구독 관계][2]

## <a name="administrator-structure"></a>관리자 구조

Azure AD 인증을 사용 하는 경우 원래 Azure SQL Database 관리자와 Azure AD 관리자의 두 가지 관리자 계정이 있습니다. Azure Synapse에도 동일한 개념이 적용 됩니다. Azure AD 계정을 기반으로 하는 관리자만 사용자 데이터베이스에서 최초 Azure AD 포함 데이터베이스 사용자를 만들 수 있습니다. Azure AD 관리자 로그인은 Azure AD 사용자나 Azure AD 그룹이 될 수 있습니다. 관리자가 그룹 계정인 경우 모든 그룹 구성원에서 사용할 수 있으며 서버에 대 한 여러 Azure AD 관리자를 사용할 수 있습니다. 그룹 계정을 관리자로 사용 하면 SQL Database 또는 Azure Synapse에서 사용자 또는 사용 권한을 변경 하지 않고 Azure AD에서 그룹 구성원을 중앙에서 추가 하 고 제거할 수 있으므로 관리 효율성이 향상 됩니다. 한 번에 하나의 Azure AD 관리자(그룹 또는 사용자)를 구성할 수 있습니다.

![관리자 구조][3]

## <a name="permissions"></a>사용 권한

새 사용자를 만들려면 데이터베이스에서 `ALTER ANY USER` 권한이 있어야 합니다. `ALTER ANY USER` 권한은 아무 데이터베이스 사용자에게나 부여할 수 있습니다. 서버 관리자 계정과, 해당 데이터베이스에 대한 `CONTROL ON DATABASE` 또는 `ALTER ON DATABASE` 권한이 있는 데이터베이스 사용자와, `db_owner` 데이터베이스 역할 그룹의 구성원도 `ALTER ANY USER` 권한을 보유할 수 있습니다.

Azure SQL Database, SQL Managed Instance 또는 Azure Synapse에서 포함 된 데이터베이스 사용자를 만들려면 Azure AD id를 사용 하 여 데이터베이스 또는 인스턴스에 연결 해야 합니다. 최초 포함 데이터베이스 사용자를 만들려면 Azure AD 관리자(데이터베이스 소유자)를 사용하여 데이터베이스에 연결해야 합니다. 이는 [SQL Database 또는 Azure Synapse를 사용 하 여 Azure Active Directory 인증을 구성 하 고 관리](authentication-aad-configure.md)하는 방법을 보여 줍니다. Azure AD 인증은 Azure SQL Database, SQL Managed Instance 또는 Azure Synapse에 대해 Azure AD 관리자를 만든 경우에만 가능 합니다. Azure Active Directory 관리자가 서버에서 제거된 경우 SQL Server 내에 이전에 만든 기존 Azure Active Directory 사용자는 Azure Active Directory 자격 증명을 사용하여 데이터베이스에 더 이상 연결할 수 없습니다.

## <a name="azure-ad-features-and-limitations"></a>Azure AD 기능 및 제한 사항

- Azure SQL Database에 대해 Azure AD의 다음 멤버를 프로 비전 할 수 있습니다.

  - 네이티브 멤버: 고객 도메인 또는 관리형 도메인 내 Azure AD에서 만든 멤버입니다. 자세한 내용은 [Azure AD에 고유한 도메인 이름을 추가](../../active-directory/fundamentals/add-custom-domain.md)를 참조하세요.
  - 통과 또는 암호 해시 인증을 사용 하 여 원활한 Single Sign-On으로 구성 된 관리 되는 도메인의 Azure Active Directory에 페더레이션된 Active Directory 도메인의 멤버입니다. 자세한 내용은 Microsoft Azure를 참조 하세요. [이제 Windows Server Active Directory와의 페더레이션을 지원](https://azure.microsoft.com/blog/windows-azure-now-supports-federation-with-windows-server-active-directory//) 하 고 [원활한 Single sign-on을 Azure Active Directory](../../active-directory/hybrid/how-to-connect-sso.md)합니다.
  - 네이티브 또는 페더레이션 도메인 멤버인 다른 Azure AD에서 가져온 멤버입니다.
  - 보안 그룹으로 만들어진 Active Directory 그룹.

- 서버 역할이 있는 그룹의 일부인 Azure AD 사용자는 `db_owner` Azure SQL Database 및 Azure Synapse에 대해 **[CREATE DATABASE 범위 자격 증명](/sql/t-sql/statements/create-database-scoped-credential-transact-sql)** 구문을 사용할 수 없습니다. 다음 오류가 표시됩니다.

    `SQL Error [2760] [S0001]: The specified schema name 'user@mydomain.com' either does not exist or you do not have permission to use it.`

    개별 Azure AD 사용자에게 직접 `db_owner` 역할을 부여하여 **CREATE DATABASE SCOPED CREDENTIAL** 문제를 해결하세요.

- Azure AD 보안 주체에 따라 실행될 경우 다음 시스템 함수는 NULL 값을 반환합니다.

  - `SUSER_ID()`
  - `SUSER_NAME(<admin ID>)`
  - `SUSER_SNAME(<admin SID>)`
  - `SUSER_ID(<admin name>)`
  - `SUSER_SID(<admin name>)`

### <a name="sql-managed-instance"></a>SQL Managed Instance

- Azure AD 서버 보안 주체 (로그인) 및 사용자는 [SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md)에 대해 지원 됩니다.
- Azure ad 그룹에 매핑된 Azure AD 서버 보안 주체 (로그인)를 데이터베이스 소유자로 설정 하는 것은 [SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md)에서 지원 되지 않습니다.
  - 이를 확장 하는 것은 그룹이 서버 역할의 일부로 추가 될 때 `dbcreator` 이 그룹의 사용자가 SQL Managed Instance에 연결 하 여 새 데이터베이스를 만들 수 있지만 데이터베이스에 액세스할 수 없다는 것입니다. 새 데이터베이스 소유자는 Azure AD 사용자가 아닌 SA이기 때문입니다. 개별 사용자가 `dbcreator` 서버 역할에 추가되면 이 문제가 발생하지 않습니다.
- Azure AD 서버 보안 주체 (로그인)에 대해 SQL 에이전트 관리 및 작업 실행이 지원 됩니다.
- 데이터베이스 백업 및 복원 작업은 Azure AD 서버 보안 주체(로그인)에서 실행될 수 있습니다.
- Azure AD 서버 보안 주체(로그인) 및 인증 이벤트와 관련된 모든 문을 감사할 수 있습니다.
- sysadmin 서버 역할의 구성원인 Azure AD 서버 보안 주체(로그인)의 관리자 전용 연결이 지원됩니다.
  - SQLCMD 유틸리티 및 SQL Server Management Studio를 통해 지원됩니다.
- 로그온 트리거는 Azure AD 서버 보안 주체(로그인)에서 발생하는 로그온 이벤트에 지원됩니다.
- Service Broker 및 DB 메일은 Azure AD 서버 보안 주체(로그인)를 사용하여 설정할 수 있습니다.

## <a name="connect-by-using-azure-ad-identities"></a>Azure AD ID를 사용하여 연결

Azure Active Directory 인증에서는 Azure AD ID를 사용하여 데이터베이스에 연결하는 다음 방법을 지원합니다.

- Azure Active Directory 암호
- Azure Active Directory 통합
- Multi-Factor Authentication Azure Active Directory Universal
- 애플리케이션 토큰 인증 사용

Azure AD 서버 보안 주체 (로그인)에 대해 지원 되는 인증 방법은 다음과 같습니다.

- Azure Active Directory 암호
- Azure Active Directory 통합
- Multi-Factor Authentication Azure Active Directory Universal

### <a name="additional-considerations"></a>기타 고려 사항

- 관리 효율성을 높일 수 있게 관리자 권한으로 전용 Azure AD 그룹을 프로비전하는 것이 좋습니다.
- 언제 든 지 SQL Database 또는 Azure Synapse의 서버에 대해 하나의 Azure AD 관리자 (사용자 또는 그룹)만 구성할 수 있습니다.
  - SQL Managed Instance에 대 한 Azure AD 서버 보안 주체 (로그인)를 추가 하면 역할에 추가할 수 있는 여러 Azure AD 서버 보안 주체 (로그인)를 만들 수 있습니다 `sysadmin` .
- 서버에 대 한 Azure AD 관리자만 처음에 Azure Active Directory 계정을 사용 하 여 서버 또는 관리 되는 인스턴스에 연결할 수 있습니다. Active Directory 관리자가 이후의 Azure AD 데이터베이스 사용자를 구성할 수 있습니다.
- 연결 제한 시간은 30초로 설정하는 것이 좋습니다.
- SQL Server 2016 Management Studio 및 Visual Studio 2015용 SQL Server Data Tools(버전 14.0.60311.1 2016년 4월 이상)는 Azure Active Directory 인증을 지원합니다. Azure AD 인증은 **.NET Framework Data Provider for SqlServer**(.NET Framework 4.6 버전 이상)에서 지원됩니다. 따라서 이러한 도구 및 데이터 계층 응용 프로그램 (DAC 및 BACPAC)의 최신 버전은 Azure AD 인증을 사용할 수 있습니다.
- 15.0.1 버전부터 [sqlcmd 유틸리티](/sql/tools/sqlcmd-utility) 및 [bcp 유틸리티](/sql/tools/bcp-utility) 는 Multi-Factor Authentication를 사용 하 여 대화형 인증 Active Directory 지원 합니다.
- Visual Studio 2015용 SQL Server Data Tools는 Data Tools의 2016년 4월 버전 이상이 필요합니다(버전 14.0.60311.1). 현재 Azure AD 사용자는 SSDT 개체 탐색기에 표시 되지 않습니다. 해결 방법으로 [sys.database_principals](https://msdn.microsoft.com/library/ms187328.aspx)에서 사용자를 봅니다.
- [SQL Server용 Microsoft JDBC 드라이버 6.0](https://www.microsoft.com/download/details.aspx?id=11774)은 Azure AD 인증을 지원합니다. 또한 [연결 속성 설정](/sql/connect/jdbc/setting-the-connection-properties)을 참조하세요.
- PolyBase는 Azure AD 인증을 사용하여 인증할 수 없습니다.
- Azure AD 인증은 Azure Portal **가져오기 데이터베이스** 및 **내보내기 데이터베이스** 블레이드를 사용 하 여 Azure SQL Database 및 azure Synapse에 대해 지원 됩니다. Azure AD 인증을 사용한 가져오기 및 내보내기도 PowerShell 명령에서 지원 됩니다.
- Azure AD 인증은 CLI를 사용 하 여 SQL Database, SQL Managed Instance 및 Azure Synapse에 대해 지원 됩니다. 자세한 내용은 [SQL Database 또는 Azure Synapse를 사용 하 여 AZURE AD 인증 구성 및 관리](authentication-aad-configure.md) 및 [SQL Server-az SQL Server](https://docs.microsoft.com/cli/azure/sql/server)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

- Azure AD 인스턴스를 만들고 채운 다음 Azure SQL Database, SQL Managed Instance 또는 Azure Synapse를 사용 하 여 구성 하는 방법을 알아보려면 [SQL Database, sql Managed Instance 또는 Azure Synapse를 사용 하 여 Azure Active Directory 인증 구성 및 관리](authentication-aad-configure.md)를 참조 하세요.
- SQL Managed Instance에서 Azure AD 서버 보안 주체 (로그인)를 사용 하는 방법에 대 한 자습서는 SQL Managed Instance를 사용 하는 [AZURE ad 서버 보안 주체 (로그인)](../managed-instance/aad-security-configure-tutorial.md) 를 참조 하세요.
- SQL Database의 로그인, 사용자, 데이터베이스 역할 및 권한에 대 한 개요는 [로그인, 사용자, 데이터베이스 역할 및 사용 권한](logins-create-manage.md)을 참조 하세요.
- 데이터베이스 보안 주체에 대한 자세한 내용은 [보안 주체](https://msdn.microsoft.com/library/ms181127.aspx)를 참조하세요.
- 데이터베이스 역할에 대한 자세한 내용은 [데이터베이스 역할](https://msdn.microsoft.com/library/ms189121.aspx)을 참조하세요.
- SQL Managed Instance에 대 한 Azure AD 서버 보안 주체 (로그인)를 만드는 구문은 [로그인 만들기](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current)를 참조 하세요.
- SQL Database의 방화벽 규칙에 대한 자세한 내용은 [SQL Database 방화벽 규칙](firewall-configure.md)을 참조하세요.

<!--Image references-->
[1]: ./media/authentication-aad-overview/1aad-auth-diagram.png
[2]: ./media/authentication-aad-overview/2subscription-relationship.png
[3]: ./media/authentication-aad-overview/3admin-structure.png
