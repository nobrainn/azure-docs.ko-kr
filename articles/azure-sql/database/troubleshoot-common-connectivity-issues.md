---
title: 일시적인 오류 작업
description: Azure SQL Database, Azure SQL Managed Instance 및 Azure Synapse Analytics에 연결할 때 SQL 연결 오류 또는 일시적인 오류를 해결, 진단 및 방지 하는 방법에 대해 알아봅니다.
keywords: SQL 연결, 연결 문자열, 연결 문제, 일시적인 오류, 연결 오류
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: dalechen
ms.author: ninarn
ms.reviewer: carlrab, vanto
ms.date: 01/14/2020
ms.openlocfilehash: 0b28fa788e7b35e94482104d807c228db21f49b4
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87003919"
---
# <a name="troubleshoot-transient-connection-errors-in-sql-database-and-sql-managed-instance"></a>SQL Database 및 SQL Managed Instance에서 일시적인 연결 오류 해결

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

이 문서에서는 클라이언트 응용 프로그램이 Azure SQL Database, Azure SQL Managed Instance 및 Azure Synapse Analytics와 상호 작용할 때 발생 하는 연결 오류 및 일시적인 오류를 방지, 문제 해결, 진단 및 완화 하는 방법을 설명 합니다. 재시도 논리를 구성하고 연결 문자열을 빌드하며 타 연결 설정을 조정하는 방법에 대해 알아봅니다.

<a id="i-transient-faults" name="i-transient-faults"></a>

## <a name="transient-errors-transient-faults"></a>일시적인 오류(일시 장애)

일시 결함으로도 알려진 일시적인 오류에는 자체적으로 신속히 확인되는 원인이 있습니다. 일시적 오류가 발생하는 이유는 가끔 Azure 시스템에서 다양한 워크로드의 부하를 더 효율적으로 분산하기 위해 하드웨어를 신속하게 변경하는 경우가 포함됩니다. 이러한 재구성 이벤트는 60초 이내에 완료됩니다. 이 재구성 기간 중에 SQL Database의 데이터베이스에 연결 하는 데 문제가 있을 수 있습니다. 데이터베이스에 연결 하는 응용 프로그램은 이러한 일시적인 오류를 발생 시킬 수 있도록 빌드해야 합니다. 이를 처리하기 위해 사용자에게 애플리케이션 오류로 표시되는 대신 해당 코드에서 재시도 논리를 구현합니다.

클라이언트 프로그램에서 ADO.NET을 사용하는 경우 사용자 프로그램에 **SqlException**이 throw되어 일시적 오류가 발생했다는 메시지가 표시됩니다.

<a id="connection-versus-command" name="connection-versus-command"></a>

### <a name="connection-vs-command"></a>연결 및 명령

다음에 따라 SQL Database 및 SQL Managed Instance 연결을 다시 시도 하거나 다시 설정 합니다.

- **연결 시도 중 일시적 오류가 발생할 경우**

몇 초 지연한 후에 연결을 다시 시도합니다.

- **SQL Database 및 SQL Managed Instance 쿼리 명령을 실행 하는 동안 일시적인 오류가 발생 했습니다.**

명령을 즉시 다시 시도하지 않습니다. 대신, 지연 후에 연결을 새로 고쳐야 합니다. 그런 후 명령을 다시 시도합니다.

<a id="j-retry-logic-transient-faults" name="j-retry-logic-transient-faults"></a>

## <a name="retry-logic-for-transient-errors"></a>일시적인 오류에 대한 재시도 논리

간혹 일시적 오류가 발생하는 클라이언트 프로그램에 재시도 논리가 포함된 경우 더욱 견고해질 수 있습니다. 프로그램이 타사 미들웨어를 통해 SQL Database에서 데이터베이스와 통신 하는 경우 미들웨어에 일시적 오류에 대 한 재시도 논리가 포함 되어 있는지 공급 업체에 문의 하세요.

<a id="principles-for-retry" name="principles-for-retry"></a>

### <a name="principles-for-retry"></a>재시도 원칙

- 오류가 일시적인 경우 연결 열기를 다시 시도합니다.
- 일시적인 오류로 인해 실패 한 SQL Database 또는 SQL Managed Instance 문을 직접 다시 시도 하지 마세요 `SELECT` . 대신, 새로 연결한 다음 `SELECT`를 다시 시도합니다.
- 일시적인 오류로 인해 SQL Database 또는 SQL Managed Instance `UPDATE` 문이 실패할 경우 업데이트를 다시 시도 하기 전에 새 연결을 설정 합니다. 재시도 논리는 전체 데이터베이스 트랜잭션이 완료되었는지 또는 전체 트랜잭션이 롤백되었는지 여부를 확인해야 합니다.

### <a name="other-considerations-for-retry"></a>재시도에 대한 기타 고려 사항

- 업무 시간 후 자동으로 시작되어 아침 전까지 완료되는 배치 프로그램은 재시도 간격을 길게 설정할 수 있습니다.
- 사용자 인터페이스 프로그램은 기다리는 시간이 길 때 사용자가 포기하는 경향이 있는지를 고려해야 합니다. 몇 초마다 재시도하는 정책을 사용할 경우 시스템의 요청 수가 너무 많아지므로 사용하지 않아야 합니다.

### <a name="interval-increase-between-retries"></a>재시도 간격 증가

첫 번째 재시도 전에 5초간 대기하는 것이 좋습니다. 5초보다 짧은 지연 후 재시도는 클라우드 서비스에 많은 위험이 있습니다. 각 후속 재시도에 대해 지연 시간은 최대 60초까지 기하급수적으로 증가해야 합니다.

ADO.NET를 사용 하는 클라이언트에 대 한 차단 기간에 대 한 설명은 [연결 풀링 (ADO.NET)](https://msdn.microsoft.com/library/8xx3tyca.aspx)을 참조 하세요.

또한 프로그램이 자체적으로 종료하기 전까지 최대 재시도 횟수를 설정할 수 있습니다.

### <a name="code-samples-with-retry-logic"></a>재시도 논리가 포함된 코드 샘플

재시도 논리가 포함된 코드 예제는 다음에 있습니다.

- [ADO.NET를 사용 하 여 탄력적으로를 Azure SQL에 연결][step-4-connect-resiliently-to-sql-with-ado-net-a78n]
- [PHP를 사용 하 여 Azure SQL에 탄력적으로 연결][step-4-connect-resiliently-to-sql-with-php-p42h]

<a id="k-test-retry-logic" name="k-test-retry-logic"></a>

### <a name="test-your-retry-logic"></a>재시도 논리 테스트

재시도 논리를 테스트하려면 프로그램이 실행 중인 동안 수정할 수 있는 오류를 일으키거나 시뮬레이션해야 합니다.

#### <a name="test-by-disconnecting-from-the-network"></a>네트워크에서 연결을 끊고 테스트

재시도 논리를 테스트할 수 있는 한 가지 방법은 프로그램이 실행되는 동안 네트워크에서 클라이언트 컴퓨터 연결을 끊는 것입니다. 오류는 다음과 같습니다.

- **SqlException.Number** = 11001
- 메시지: "해당 호스트가 없습니다"

첫 번째 다시 시도의 일부로 클라이언트 컴퓨터를 네트워크에 다시 연결한 다음 연결을 시도할 수 있습니다.

이 테스트를 실제로 사용하려면 프로그램을 시작하기 전에 네트워크와 컴퓨터 간 케이블을 분리합니다. 그러면 프로그램에서 프로그램이 다음과 같이 작동하는 런타임 매개 변수를 인식합니다.

- 오류 목록에 일시적 오류로 간주하기 위해 11001을 일시적으로 추가합니다.
- 평상 시와 같이 첫 번째 연결을 시도합니다.
- 오류가 확인되면 목록에서 11001을 제거합니다.
- 사용자에게 컴퓨터를 네트워크에 연결하라는 메시지를 표시합니다.
- **Console.ReadLine** 메서드 또는 확인 단추가 포함된 대화 상자를 사용하여 추가 실행을 일시 정지합니다. 사용자가 컴퓨터와 네트워크 간 케이블을 연결한 다음 Enter 키를 누릅니다.
- 다시 연결을 시도합니다. 정상적으로 연결되어야 합니다.

#### <a name="test-by-misspelling-the-user-name-when-connecting"></a>연결할 때 사용자 이름의 철자를 검사 합니다.

프로그램이 첫 번째 연결 시도 전에 의도적으로 사용자 이름의 철자를 잘못 입력할 수 있습니다. 오류는 다음과 같습니다.

- **SqlException.Number** = 18456
- 메시지: "사용자 'WRONG_MyUserName'에 대한 로그인에 실패했습니다."

프로그램은 첫 번째 재시도 중 오타를 수정한 다음 연결을 시도할 수 있습니다.

이 테스트를 실제로 사용하기 위해 프로그램에서 프로그램이 다음과 같이 작동하는 런타임 매개 변수를 인식합니다.

- 오류 목록에 일시적 오류로 간주하기 위해 18456을 일시적으로 추가합니다.
- 사용자 이름에 의도적으로 'WRONG_'을 추가합니다.
- 오류가 확인되면 목록에서 18456을 제거합니다.
- 사용자 이름에서 'WRONG_'을 제거합니다.
- 다시 연결을 시도합니다. 정상적으로 연결되어야 합니다.

<a id="net-sqlconnection-parameters-for-connection-retry" name="net-sqlconnection-parameters-for-connection-retry"></a>

## <a name="net-sqlconnection-parameters-for-connection-retry"></a>연결 다시 시도에 대한 .NET SqlConnection 매개 변수

클라이언트 프로그램이 SQL Database의 .NET Framework 클래스를 사용 하 여 데이터베이스에 연결 하는 경우 .NET 4.6.1 이상 (또는 .NET Core)을 사용 하 여 연결 다시 시도 **기능을 사용할**수 있습니다. 기능에 대한 자세한 내용은 [이 웹 페이지](https://docs.microsoft.com/dotnet/api/system.data.sqlclient.sqlconnection)를 참조하세요.

<!--
2015-11-30, FwLink 393996 points to dn632678.aspx, which links to a downloadable .docx related to SqlClient and SQL Server 2014.
-->

**SqlConnection** 개체에 대한 [연결 문자열](https://msdn.microsoft.com/library/System.Data.SqlClient.SqlConnection.connectionstring.aspx)을 작성하는 경우 다음 매개 변수 중에서 값을 조정합니다.

- **ConnectRetryCount**: &nbsp; &nbsp; 기본값은 1입니다. 범위는 0에서 255입니다.
- **ConnectRetryInterval**: &nbsp; &nbsp; 기본값은 10 초입니다. 범위는 1에서 60입니다.
- **연결 시간 제한**: &nbsp; &nbsp; 기본값은 15 초입니다. 범위는 0에서 2147483647입니다.

특히 선택한 값은 다음 같음을 true로 만들어야 합니다. Connection Timeout = ConnectRetryCount * ConnectionRetryInterval

예를 들어 개수가 3이고 간격이 10초인 경우 시간 제한이 29초이면 연결의 3번째 및 마지막 재시도에 대해 시스템에 충분한 시간을 제공하지 않게 됩니다. 29 < 3 * 10입니다.

<a id="connection-versus-command" name="connection-versus-command"></a>

## <a name="connection-vs-command"></a>연결 및 명령

**ConnectRetryCount** 및 **ConnectRetryInterval** 매개 변수를 사용하면 **SqlConnection** 개체는 프로그램에 제어를 반환하는 등 프로그램에 전달하거나 신경 쓰지 않고 연결 작업을 다시 시도합니다. 다시 시도는 다음과 같은 상황에서 발생할 수 있습니다.

- SqlConnection 메서드 호출
- SqlConnection.Exe귀여운 메서드 호출

미묘한 문제가 있습니다. 임시 오류가 발생할 경우 *쿼리* 가 실행되는 동안 **SqlConnection** 개체는 연결 작업을 다시 시도하지 않습니다. 확실히 쿼리를 다시 시도하지 않습니다. 그러나 **SqlConnection** 는 매우 신속하게 실행에 쿼리를 보내기 전에 연결을 검사합니다. 빠른 검사가 연결 문제를 감지하면 **SqlConnection** 은 연결 작업을 다시 시도합니다. 다시 시도가 성공하면 쿼리는 실행을 위해 전송됩니다.

### <a name="should-connectretrycount-be-combined-with-application-retry-logic"></a>ConnectRetryCount가 애플리케이션 다시 시도 논리에 결합되어야 하는지 여부

애플리케이션에는 강력한 사용자 지정 다시 시도 논리가 있다고 가정합니다. 연결 작업을 4번 다시 시도할 수 있습니다. **ConnectRetryInterval** 및 **ConnectRetryCount** =3을 연결 문자열에 추가하는 경우 다시 시도 횟수가 4 * 3 = 12로 늘어납니다. 이러한 많은 수의 다시 시도를 할 의도가 아닐 수 있습니다.

<a id="a-connection-connection-string" name="a-connection-connection-string"></a>

## <a name="connections-to-your-database-in-sql-database"></a>SQL Database의 데이터베이스에 대 한 연결

<a id="c-connection-string" name="c-connection-string"></a>

### <a name="connection-connection-string"></a>연결: 연결 문자열

데이터베이스에 연결 하는 데 필요한 연결 문자열은 SQL Server 연결 하는 데 사용 되는 문자열과 약간 다릅니다. [Azure Portal](https://portal.azure.com/)에서 데이터베이스에 대 한 연결 문자열을 복사할 수 있습니다.

[!INCLUDE [sql-database-include-connection-string-20-portalshots](../../../includes/sql-database-include-connection-string-20-portalshots.md)]

<a id="b-connection-ip-address" name="b-connection-ip-address"></a>

### <a name="connection-ip-address"></a>연결: IP 주소

클라이언트 프로그램을 호스팅하는 컴퓨터의 IP 주소에서 통신을 허용 하도록 SQL Database를 구성 해야 합니다. 이 구성을 설정하려면 [Azure Portal](https://portal.azure.com/)을 통해 방화벽 설정을 편집합니다.

IP 주소를 구성하지 않을 경우 프로그램이 실패하고 간단한 오류 메시지로 필요한 IP 주소를 표시합니다.

[!INCLUDE [sql-database-include-ip-address-22-portal](../../../includes/sql-database-include-ip-address-22-v12portal.md)]

자세한 내용은 [SQL Database에서 방화벽 설정 구성](firewall-configure.md)을 참조 하세요.
<a id="c-connection-ports" name="c-connection-ports"></a>

### <a name="connection-ports"></a>연결: 포트

일반적으로 클라이언트 프로그램을 호스팅하는 컴퓨터에서 포트 1433이 아웃바운드 통신에 개방되어 있는지 여부만 확인해야 합니다.

예를 들어 클라이언트 프로그램이 Windows 컴퓨터에 호스팅된 경우 호스트의 Windows 방화벽을 사용하여 포트 1433을 열 수 있습니다.

1. 제어판을 엽니다.
2. **모든 제어판 항목**  >  **Windows 방화벽**  >  **고급 설정**  >  **아웃 바운드 규칙**  >  **동작**  >  **새 규칙**을 선택 합니다.

클라이언트 프로그램이 Azure VM(가상 머신)에서 호스팅되는 경우 [ADO.NET 4.5 및 SQL Database에 대한 1433 이외의 포트](adonet-v12-develop-direct-route-ports.md)를 참조하세요.

데이터베이스의 포트 및 IP 주소 구성에 대 한 배경 정보는 [Azure SQL Database 방화벽](firewall-configure.md)을 참조 하세요.

<a id="d-connection-ado-net-4-5" name="d-connection-ado-net-4-5"></a>

### <a name="connection-adonet-462-or-later"></a>연결: ADO.NET 4.6.2 이상

프로그램에서 **System.Data.SqlClient.SqlConnection**과 같은 ADO.NET 클래스를 사용하여 SQL Database에 연결하는 경우 .NET Framework 버전 4.6.2 이상을 사용하는 것이 좋습니다.

#### <a name="starting-with-adonet-462"></a>ADO.NET 4.6.2를 사용하여 시작

- Azure SQL에 대 한 연결 열기 시도를 즉시 다시 시도 하 여 클라우드 지원 앱의 성능을 향상 시킵니다.

#### <a name="starting-with-adonet-461"></a>ADO.NET 4.6.1을 사용하여 시작

- SQL Database의 경우 **SqlConnection.Open** 메서드를 사용하여 연결을 열 때 안정성이 향상됩니다. **Open** 메서드는 이제 연결 제한 시간 내에 특정 오류에 대해 일시적인 오류에 대한 응답으로 최적의 재시도 메커니즘을 통합합니다.
- 연결 풀링이 제공됩니다. 이는 프로그램에 제공하는 연결 개체가 올바르게 작동하는지를 효율적으로 확인합니다.

연결 풀에서 연결 개체를 사용하는 경우 프로그램을 바로 사용하지 않으면 해당 프로그램에서 연결을 일시적으로 닫는 것이 좋습니다. 연결을 다시 여는 데 비용이 많이 들지 않지만 새 연결을 만드는 데 비용이 많이 듭니다.

ADO.NET 4.0 이전 버전을 사용할 경우 최신 ADO.NET으로 업그레이드하는 것이 좋습니다. 2018년 8월 현재 [ADO.NET 4.6.2를 다운로드](https://blogs.msdn.microsoft.com/dotnet/20../../announcing-the-net-framework-4-7-2/)할 수 있습니다.

<a id="e-diagnostics-test-utilities-connect" name="e-diagnostics-test-utilities-connect"></a>

## <a name="diagnostics"></a>진단

<a id="d-test-whether-utilities-can-connect" name="d-test-whether-utilities-can-connect"></a>

### <a name="diagnostics-test-whether-utilities-can-connect"></a>진단: 유틸리티에서 연결할 수 있는지 여부 테스트

프로그램에서 SQL Database의 데이터베이스에 연결 하지 못하면 진단 프로그램을 사용 하 여 연결을 시도 하는 한 가지 진단 옵션이 있습니다. 유틸리티는 프로그램에서 사용하는 것과 동일한 라이브러리를 사용하여 연결하는 것이 가장 좋습니다.

모든 Windows 컴퓨터에서 이러한 유틸리티를 시도할 수 있습니다.

- ADO.NET을 사용하여 연결하는 SQL Server Management Studio(ssms.exe)
- [ODBC](https://msdn.microsoft.com/library/jj730308.aspx)를 사용하여 연결하는 `sqlcmd.exe`

프로그램이 연결된 후에는 짧은 SQL SELECT 쿼리가 작동하는지 테스트합니다.

<a id="f-diagnostics-check-open-ports" name="f-diagnostics-check-open-ports"></a>

### <a name="diagnostics-check-the-open-ports"></a>진단: 개방 포트 점검

포트 문제로 인해 연결 시도 실패가 의심되는 경우 컴퓨터에서 포트 구성에 대해 보고하는 유틸리티를 실행할 수 있습니다.

Linux에서 다음 유틸리티는 도움이 될 수 있습니다.

- `netstat -nap`
- `nmap -sS -O 127.0.0.1`: 사용자의 IP 주소가 되도록 예제 값을 변경합니다.

Windows에서는 [PortQry.exe](https://www.microsoft.com/download/details.aspx?id=17148) 유틸리티가 도움이 됩니다. 다음은 SQL Database에서 데이터베이스에 대 한 포트 상황을 쿼리하고 노트북 컴퓨터에서 실행 된 예제 실행입니다.

```cmd
[C:\Users\johndoe\]
>> portqry.exe -n johndoesvr9.database.windows.net -p tcp -e 1433

Querying target system called: johndoesvr9.database.windows.net

Attempting to resolve name to IP address...
Name resolved to 23.100.117.95

querying...
TCP port 1433 (ms-sql-s service): LISTENING

[C:\Users\johndoe\]
>>
```

<a id="g-diagnostics-log-your-errors" name="g-diagnostics-log-your-errors"></a>

### <a name="diagnostics-log-your-errors"></a>진단: 오류 기록

간헐적 문제는 며칠 또는 몇 주간 일반 패턴을 발견하여 진단하는 것이 가장 좋은 경우가 있습니다.

클라이언트에서 발생한 모든 오류를 기록하면 진단에 도움이 될 수 있습니다. 로그 항목과 SQL Database에서 내부적으로 기록하는 오류 데이터의 상관 관계를 분석할 수 있습니다.

Enterprise Library 6(EntLib60)은 로깅을 지원하기 위해 .NET 관리 클래스를 제공합니다. 자세한 내용은 [5 - 간단한 애플리케이션 블록 로깅 사용](https://msdn.microsoft.com/library/dn440731.aspx)을 참조하세요.

<a id="h-diagnostics-examine-logs-errors" name="h-diagnostics-examine-logs-errors"></a>

### <a name="diagnostics-examine-system-logs-for-errors"></a>진단: 시스템 로그에서 오류 확인

다음은 오류 로그 및 기타 정보를 쿼리하는 몇 가지 Transact-SQL SELECT 문입니다.

| 로그 쿼리 | Description |
|:--- |:--- |
| `SELECT e.*`<br/>`FROM sys.event_log AS e`<br/>`WHERE e.database_name = 'myDbName'`<br/>`AND e.event_category = 'connectivity'`<br/>`AND 2 >= DateDiff`<br/>&nbsp;&nbsp;`(hour, e.end_time, GetUtcDate())`<br/>`ORDER BY e.event_category,`<br/>&nbsp;&nbsp;`e.event_type, e.end_time;` |[sys.event_log](https://msdn.microsoft.com/library/dn270018.aspx) 보기는 일시적인 오류 또는 연결 실패를 일으킬 수 있는 일부를 포함하여 개별 이벤트에 대한 정보를 제공합니다.<br/><br/>이상적으로 **start_time** 또는 **end_time** 값을 클라이언트 프로그램에 문제가 발생하는 방법에 대한 정보와 함께 상호 연결할 수 있습니다.<br/><br/>*마스터* 데이터베이스에 연결하여 이 쿼리를 실행해야 합니다. |
| `SELECT c.*`<br/>`FROM sys.database_connection_stats AS c`<br/>`WHERE c.database_name = 'myDbName'`<br/>`AND 24 >= DateDiff`<br/>&nbsp;&nbsp;`(hour, c.end_time, GetUtcDate())`<br/>`ORDER BY c.end_time;` |[Database_connection_stats](https://msdn.microsoft.com/library/dn269986.aspx) 뷰는 추가 진단에 대 한 이벤트 유형의 집계 된 개수를 제공 합니다.<br/><br/>*마스터* 데이터베이스에 연결하여 이 쿼리를 실행해야 합니다. |

<a id="d-search-for-problem-events-in-the-sql-database-log" name="d-search-for-problem-events-in-the-sql-database-log"></a>

### <a name="diagnostics-search-for-problem-events-in-the-sql-database-log"></a>진단: SQL Database 로그에서 문제 이벤트 검색

SQL Database 로그에서 문제 이벤트에 대한 항목을 검색할 수 있습니다. *마스터* 데이터베이스에서 다음 Transact-SQL SELECT 문을 시도해 보세요.

```sql
SELECT
   object_name
  ,CAST(f.event_data as XML).value
      ('(/event/@timestamp)[1]', 'datetime2')                      AS [timestamp]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="error"]/value)[1]', 'int')             AS [error]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="state"]/value)[1]', 'int')             AS [state]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="is_success"]/value)[1]', 'bit')        AS [is_success]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="database_name"]/value)[1]', 'sysname') AS [database_name]
FROM
  sys.fn_xe_telemetry_blob_target_read_file('el', null, null, null) AS f
WHERE
  object_name != 'login_event'  -- Login events are numerous.
  and
  '2015-06-21' < CAST(f.event_data as XML).value
        ('(/event/@timestamp)[1]', 'datetime2')
ORDER BY
  [timestamp] DESC
;
```

#### <a name="a-few-returned-rows-from-sysfn_xe_telemetry_blob_target_read_file"></a>sys.fn_xe_telemetry_blob_target_read_file에서 반환된 몇 개 행

다음 예제에서는 반환된 행의 모양을 보여 줍니다. 여기에 표시된 null 값은 다른 행에서 null이 아닌 경우가 많습니다.

```
object_name                   timestamp                    error  state  is_success  database_name

database_xml_deadlock_report  2015-10-16 20:28:01.0090000  NULL   NULL   NULL        AdventureWorks
```

<a id="l-enterprise-library-6" name="l-enterprise-library-6"></a>

## <a name="enterprise-library-6"></a>Enterprise Library 6

Enterprise Library 6 (EntLib60)은 클라우드 서비스의 강력한 클라이언트 SQL Database를 구현 하는 데 도움이 되는 .NET 클래스의 프레임 워크입니다. EntLib60을 이용할 수 있는 각 영역에 해당하는 항목을 찾으려면 [Enterprise Library 6 – 2013년 4월](https://msdn.microsoft.com/library/dn169621%28v=pandp.60%29.aspx)을 참조하세요.

일시적 오류 처리에 대한 재시도 논리는 EntLib60을 이용할 수 있는 한 가지 영역입니다. 자세한 내용은 [4 - 모든 성공의 인내와 비밀: 일시적 오류 처리 애플리케이션 블록 사용](https://msdn.microsoft.com/library/dn440719%28v=pandp.60%29.aspx)을 참조하세요.

> [!NOTE]
> EntLib60에 대한 소스 코드는 [다운로드 센터](https://go.microsoft.com/fwlink/p/?LinkID=290898)의 공용 다운로드에서 사용할 수 있습니다. Microsoft는 EntLib에 추가 기능 또는 유지 관리를 업데이트할 계획이 없습니다.

<a id="entlib60-classes-for-transient-errors-and-retry" name="entlib60-classes-for-transient-errors-and-retry"></a>

### <a name="entlib60-classes-for-transient-errors-and-retry"></a>일시적 오류 및 재시도용 EntLib60 클래스

다음 EntLib60 클래스는 특히 재시도 논리에 유용합니다. 이러한 클래스는 모두 **Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling** 네임스페이스에 있으며, 여기에서 찾을 수 있습니다.

**Microsoft.practices.enterpriselibrary.transientfaulthandling**네임 스페이스에서 다음을 수행 합니다.

- **RetryPolicy** 클래스
  - **ExecuteAction** 메서드
- **ExponentialBackoff** 클래스
- **SqlDatabaseTransientErrorDetectionStrategy** 클래스
- **ReliableSqlConnection** 클래스
  - **ExecuteCommand** 메서드

**Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling.TestSupport**네임스페이스의

- **AlwaysTransientErrorDetectionStrategy** 클래스
- **NeverTransientErrorDetectionStrategy** 클래스

다음은 EntLib60에 대한 정보의 일부 링크입니다.

- 무료 책 다운로드: [Microsoft Enterprise Library에 대한 개발자 가이드, 2판](https://www.microsoft.com/download/details.aspx?id=41145)
- 모범 사례: [재시도 일반 지침](/azure/architecture/best-practices/transient-faults) 에서 재시도 논리에 대해 깊이 있게 다룹니다.
- NuGet 다운로드: [Enterprise Library - 일시적 오류 처리 애플리케이션 블록 6.0](https://www.nuget.org/packages/EnterpriseLibrary.TransientFaultHandling/)

<a id="entlib60-the-logging-block" name="entlib60-the-logging-block"></a>

### <a name="entlib60-the-logging-block"></a>EntLib60: 로깅 블록

- 로깅 블록은 다음 작업을 할 수 있는 매우 유연한 맞춤형 솔루션입니다.
  - 다양한 위치에서 메시지를 만들고 저장합니다.
  - 메시지를 분류 및 필터링합니다.
  - 디버깅, 추적, 감사 및 일반 로깅 요구 사항에 유용한 문맥 정보를 수집합니다.
- 로깅 블록은 대상 로깅 저장소의 위치 및 유형과 상관없이 애플리케이션 코드의 일관성을 유지하도록 로그 대상에서 로깅 기능을 추상화합니다.

자세한 내용은 [5 - 간단한 애플리케이션 블록 로깅 사용](https://msdn.microsoft.com/library/dn440731%28v=pandp.60%29.aspx)을 참조하세요.

<a id="entlib60-istransient-method-source-code" name="entlib60-istransient-method-source-code"></a>

### <a name="entlib60-istransient-method-source-code"></a>EntLib60 IsTransient 메서드 소스 코드

다음으로, **SqlDatabaseTransientErrorDetectionStrategy** 클래스에는 **IsTransient** 메서드에 대한 C# 소스 코드가 있습니다. 소스 코드는 2013년 4월처럼 일시적 오류로 간주할 오류와 재시도할 만한 오류를 명확히 구분합니다.

```csharp
public bool IsTransient(Exception ex)
{
  if (ex != null)
  {
    SqlException sqlException;
    if ((sqlException = ex as SqlException) != null)
    {
      // Enumerate through all errors found in the exception.
      foreach (SqlError err in sqlException.Errors)
      {
        switch (err.Number)
        {
            // SQL Error Code: 40501
            // The service is currently busy. Retry the request after 10 seconds.
            // Code: (reason code to be decoded).
          case ThrottlingCondition.ThrottlingErrorNumber:
            // Decode the reason code from the error message to
            // determine the grounds for throttling.
            var condition = ThrottlingCondition.FromError(err);

            // Attach the decoded values as additional attributes to
            // the original SQL exception.
            sqlException.Data[condition.ThrottlingMode.GetType().Name] =
              condition.ThrottlingMode.ToString();
            sqlException.Data[condition.GetType().Name] = condition;

            return true;

          case 10928:
          case 10929:
          case 10053:
          case 10054:
          case 10060:
          case 40197:
          case 40540:
          case 40613:
          case 40143:
          case 233:
          case 64:
            // DBNETLIB Error Code: 20
            // The instance of SQL Server you attempted to connect to
            // does not support encryption.
          case (int)ProcessNetLibErrorCode.EncryptionNotSupported:
            return true;
        }
      }
    }
    else if (ex is TimeoutException)
    {
      return true;
    }
    else
    {
      EntityException entityException;
      if ((entityException = ex as EntityException) != null)
      {
        return this.IsTransient(entityException.InnerException);
      }
    }
  }

  return false;
}
```

## <a name="next-steps"></a>다음 단계

- [SQL Database 및 SQL Server용 연결 라이브러리](connect-query-content-reference-guide.md#libraries)
- [연결 풀링 (ADO.NET)](https://docs.microsoft.com/dotnet/framework/data/adonet/sql-server-connection-pooling)
- [*Retrying*은 임의 항목에 재시도 동작을 추가하는 작업을 간소화하기 위해 Apache 2.0 라이선스 하에 Python으로 작성한 일반 목적의 재시도 라이브러리입니다.](https://pypi.python.org/pypi/retrying)

<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: https://docs.microsoft.com/sql/connect/ado-net/step-4-connect-resiliently-sql-ado-net

[step-4-connect-resiliently-to-sql-with-php-p42h]: https://docs.microsoft.com/sql/connect/php/step-4-connect-resiliently-to-sql-with-php
