# MSSQL Always Encrypted
Sample app for Microsoft Always Encryted

## Installing SQL Server with Secure Enclave Support

https://learn.microsoft.com/en-us/sql/relational-databases/security/encryption/always-encrypted-enclaves-no-attestation-plan?view=sql-server-ver16

- run msinfo32.exe and confirm VBS is running (Virtualization Based Security)
- If it's not execure the below

``` Powershell
Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Control\DeviceGuard -Name EnableVirtualizationBasedSecurity -Value 1
```

- Reboot the box and confirm if VBS now running via msinfo32.exe

- Install MSSQL Server 2022 Developer Edition
- Install SQL Management Studio

### Configure SQL Server to enable secure enclave using VBS type.

https://learn.microsoft.com/en-us/sql/relational-databases/security/encryption/always-encrypted-enclaves-configure-enclave-type?view=sql-server-ver16

https://learn.microsoft.com/en-us/sql/database-engine/configure-windows/configure-column-encryption-enclave-type?view=sql-server-ver16


Turn ON secure enclave with VBS type

``` SQL
sp_configure 'column encryption enclave type', 1;  
GO  
RECONFIGURE;  
GO  
```

Turn OFF secure enclave with VBS type

``` SQL
sp_configure 'column encryption enclave type', 0;  
GO  
RECONFIGURE;  
GO  
```

- Restart SQL server service

### Check Secure Enclave Configuration

Run the below to query current secure enclave confuguration and associated type

``` SQL
USE [master];
GO
SELECT
[value]
, CASE [value] WHEN 0 THEN 'No enclave' WHEN 1 THEN 'VBS' ELSE 'Other' END AS [value_description]
, [value_in_use]
, CASE [value_in_use] WHEN 0 THEN 'No enclave' WHEN 1 THEN 'VBS' ELSE 'Other' END AS [value_in_use_description]
FROM sys.configurations
WHERE [name] = 'column encryption enclave type';
```

### Key Management

https://learn.microsoft.com/en-us/sql/relational-databases/security/encryption/overview-of-key-management-for-always-encrypted?view=sql-server-ver16


### TDE

https://learn.microsoft.com/en-us/sql/t-sql/statements/create-database-encryption-key-transact-sql?view=sql-server-ver16

### Parameterization

https://techcommunity.microsoft.com/blog/azuresqlblog/parameterization-for-always-encrypted---using-ssms-to-insert-into-update-and-fil/386124

### Sample Paramertized Queries

``` SQL
DECLARE @Age INT = 4;
INSERT INTO dbo.Users (Id, FirstName, LastName, Age) VALUES (5, 'Daisy', 'Boardman', @Age);
```