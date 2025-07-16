# Windows Active Directory: Complete Course from Basics to Advanced Security

## Course Overview
This comprehensive course will take you from understanding the basic concepts of Active Directory to implementing advanced security configurations. Each module builds upon the previous one, ensuring a solid foundation before advancing to complex topics.

---

## Module 1: Active Directory Fundamentals

### What is Active Directory?
Active Directory (AD) is Microsoft's directory service that provides centralized authentication, authorization, and management of network resources in Windows environments.

**Key Concepts:**
- **Directory Service**: A hierarchical database that stores information about network objects
- **Domain**: A logical group of network objects that share the same AD database
- **Forest**: A collection of one or more domains that share a common schema and global catalog
- **Tree**: A hierarchical arrangement of domains within a forest

### Core Components

#### 1. Domain Controllers (DCs)
- Windows servers that host the AD database
- Handle authentication and authorization requests
- Replicate data between other domain controllers
- At least one DC required per domain

#### 2. Organizational Units (OUs)
- Containers that organize objects within a domain
- Used for applying Group Policy and delegating administration
- Can contain users, computers, groups, and other OUs

#### 3. Objects and Attributes
- **Users**: Represent people who can log into the domain
- **Computers**: Represent machines joined to the domain
- **Groups**: Collections of users or computers for easier management
- **Attributes**: Properties of objects (name, email, group membership, etc.)

### AD Database Structure
```
Forest: company.com
├── Domain: company.com
│   ├── OU: Sales
│   │   ├── User: john.doe
│   │   └── Computer: SALES-PC01
│   ├── OU: IT
│   │   ├── User: admin.user
│   │   └── Group: IT Admins
│   └── Built-in Containers
│       ├── Users
│       ├── Computers
│       └── Domain Controllers
```

---

## Module 2: Installing and Configuring Active Directory

### Prerequisites
- Windows Server 2019/2022 (recommended)
- Static IP address configuration
- Proper DNS configuration
- Administrator privileges

### Step-by-Step Installation

#### 1. Promote Server to Domain Controller
```powershell
# Install AD Domain Services role
Install-WindowsFeature -Name AD-Domain-Services -IncludeManagementTools

# Promote to domain controller (new forest)
Install-ADDSForest -DomainName "company.local" -SafeModeAdministratorPassword (ConvertTo-SecureString "P@ssw0rd123!" -AsPlainText -Force)
```

#### 2. Post-Installation Configuration
- Verify DNS is working correctly
- Check Event Logs for errors
- Validate replication (if multiple DCs)
- Configure time synchronization

#### 3. Creating Additional Domain Controllers
```powershell
# Add additional DC to existing domain
Install-ADDSDomainController -DomainName "company.local" -SafeModeAdministratorPassword (ConvertTo-SecureString "P@ssw0rd123!" -AsPlainText -Force)
```

### Best Practices for Installation
- Use dedicated servers for domain controllers
- Implement at least two DCs for redundancy
- Place DCs in secure physical locations
- Configure proper backup strategies

---

## Module 3: Managing Users, Groups, and Computers

### User Management

#### Creating Users
```powershell
# Create new user with PowerShell
New-ADUser -Name "John Doe" -GivenName "John" -Surname "Doe" -SamAccountName "jdoe" -UserPrincipalName "jdoe@company.local" -Path "OU=Sales,DC=company,DC=local" -AccountPassword (ConvertTo-SecureString "TempPass123!" -AsPlainText -Force) -Enabled $true
```

#### User Properties and Attributes
- **Account Options**: Password policies, account expiration, logon restrictions
- **Profile Information**: Home directory, profile path, logon script
- **Contact Information**: Email, phone, address
- **Organization**: Department, title, manager

#### Bulk User Creation
```powershell
# Import users from CSV
$users = Import-Csv "C:\users.csv"
foreach ($user in $users) {
    New-ADUser -Name $user.Name -SamAccountName $user.Username -UserPrincipalName "$($user.Username)@company.local" -Path $user.OU -AccountPassword (ConvertTo-SecureString $user.Password -AsPlainText -Force) -Enabled $true
}
```

### Group Management

#### Types of Groups
1. **Security Groups**: Used for permissions and access control
2. **Distribution Groups**: Used for email distribution

#### Group Scopes
- **Domain Local**: Can contain members from any domain in the forest, used for resource access
- **Global**: Can contain members from the same domain, used for organizing users
- **Universal**: Can contain members from any domain in the forest, stored in Global Catalog

#### Creating and Managing Groups
```powershell
# Create security group
New-ADGroup -Name "Sales Team" -GroupScope Global -GroupCategory Security -Path "OU=Sales,DC=company,DC=local"

# Add users to group
Add-ADGroupMember -Identity "Sales Team" -Members "jdoe", "msmith"
```

### Computer Management

#### Joining Computers to Domain
```powershell
# From the computer to be joined
Add-Computer -DomainName "company.local" -Credential (Get-Credential) -Restart
```

#### Managing Computer Objects
- Move computers to appropriate OUs
- Configure computer policies
- Monitor computer health and compliance

---

## Module 4: Organizational Units and Group Policy

### Organizational Unit Design

#### Planning OU Structure
Consider these factors:
- **Administrative delegation**: Who will manage what
- **Group Policy application**: How policies will be applied
- **Geographic locations**: Physical site considerations
- **Business structure**: Departments and functions

#### Example OU Structure
```
company.local
├── Corporate
│   ├── Users
│   │   ├── Sales
│   │   ├── Marketing
│   │   ├── IT
│   │   └── HR
│   ├── Computers
│   │   ├── Workstations
│   │   ├── Servers
│   │   └── Laptops
│   └── Groups
├── Branch Offices
│   ├── New York
│   └── Los Angeles
└── Service Accounts
```

### Group Policy Fundamentals

#### What is Group Policy?
Group Policy provides centralized management and configuration of operating systems, applications, and user settings in an AD environment.

#### Group Policy Processing Order
1. **Local Policy**: Policies stored on the local computer
2. **Site**: Policies linked to AD sites
3. **Domain**: Policies linked to the domain
4. **OU**: Policies linked to organizational units (processed from top to bottom)

#### Creating and Linking GPOs
```powershell
# Create new GPO
New-GPO -Name "Sales Security Policy" -Domain "company.local"

# Link GPO to OU
New-GPLink -Name "Sales Security Policy" -Target "OU=Sales,DC=company,DC=local"
```

#### Common Group Policy Settings
- **Password Policies**: Complexity, length, history
- **Account Lockout**: Failed logon attempts, lockout duration
- **User Rights**: Logon rights, privileges
- **Security Options**: Authentication, network security
- **Software Installation**: Deploy applications
- **Folder Redirection**: Redirect user folders to network locations

---

## Module 5: DNS Integration and Sites/Services

### DNS and Active Directory

#### Why DNS is Critical
- AD relies heavily on DNS for:
  - Domain controller location
  - Service location (SRV records)
  - Authentication processes
  - Client computer domain joining

#### AD-Integrated DNS Zones
```powershell
# Create AD-integrated DNS zone
Add-DnsServerPrimaryZone -Name "company.local" -ReplicationScope "Domain" -DynamicUpdate "Secure"
```

#### Important DNS Records for AD
- **A Records**: Map hostnames to IP addresses
- **SRV Records**: Service location records (_ldap._tcp.company.local)
- **CNAME Records**: Alias records
- **PTR Records**: Reverse DNS lookups

### Sites and Services

#### Understanding AD Sites
- Represent physical locations or network segments
- Control replication traffic between DCs
- Optimize authentication traffic

#### Creating and Configuring Sites
```powershell
# Create new site
New-ADReplicationSite -Name "Branch-Office-NY"

# Create subnet and associate with site
New-ADReplicationSubnet -Name "192.168.10.0/24" -Site "Branch-Office-NY"
```

#### Site Links and Replication
- Configure replication schedules
- Set replication costs
- Control bandwidth usage

---

## Module 6: Security Fundamentals

### Authentication vs. Authorization

#### Authentication Methods
1. **NTLM**: Legacy authentication protocol
2. **Kerberos**: Default authentication protocol in AD
3. **Certificate-based**: Using PKI certificates

#### Kerberos Authentication Process
1. User requests Ticket Granting Ticket (TGT) from KDC
2. KDC verifies credentials and issues TGT
3. User requests service ticket for specific resource
4. KDC issues service ticket
5. User presents service ticket to resource

### Access Control

#### Security Principals
- **Users**: Individual user accounts
- **Groups**: Collections of users
- **Computers**: Computer accounts
- **Service Accounts**: Accounts for services

#### Permissions and Rights
- **NTFS Permissions**: File and folder access
- **Share Permissions**: Network share access
- **User Rights**: System-level privileges
- **Object Permissions**: AD object access

### Security Groups and Built-in Accounts

#### Important Built-in Groups
- **Domain Admins**: Full administrative rights in domain
- **Enterprise Admins**: Full administrative rights in forest
- **Schema Admins**: Can modify AD schema
- **Account Operators**: Can manage user accounts
- **Server Operators**: Can manage domain controllers

#### Principle of Least Privilege
- Grant minimum permissions necessary
- Use security groups for access control
- Regularly review and audit permissions
- Implement role-based access control

---

## Module 7: Advanced Security Configuration

### Hardening Domain Controllers

#### Physical Security
- Secure physical access to servers
- Implement rack security
- Use security cameras and access logs
- Environmental controls (temperature, humidity)

#### Operating System Hardening
```powershell
# Disable unnecessary services
Set-Service -Name "Fax" -StartupType Disabled
Set-Service -Name "Print Spooler" -StartupType Disabled

# Configure Windows Firewall
New-NetFirewallRule -DisplayName "Block Outbound HTTP" -Direction Outbound -Protocol TCP -LocalPort 80 -Action Block
```

#### Security Baselines
- Use Microsoft Security Compliance Toolkit
- Implement CIS benchmarks
- Regular security assessments
- Vulnerability scanning

### Advanced Authentication Security

#### Implementing Multi-Factor Authentication
```powershell
# Enable Azure AD Connect for hybrid identity
# Configure MFA policies
Set-MsolUserMfa -UserPrincipalName "admin@company.local" -StrongAuthenticationRequirements @{State="Enabled"}
```

#### Smart Card Authentication
- Deploy PKI infrastructure
- Configure certificate templates
- Enable smart card logon
- Configure Group Policy for smart cards

#### Protected Users Security Group
```powershell
# Add user to Protected Users group
Add-ADGroupMember -Identity "Protected Users" -Members "high-privilege-user"
```

Benefits of Protected Users group:
- Cannot use NTLM authentication
- Cannot use DES or RC4 encryption
- Cannot be delegated with constrained or unconstrained delegation
- Cannot renew TGTs beyond 4-hour lifetime

### Advanced Group Policy Security

#### Security Templates and Baselines
```powershell
# Import security template
secedit /configure /db security.sdb /cfg "security_template.inf"
```

#### AppLocker Configuration
```xml
<!-- AppLocker Policy Example -->
<AppLockerPolicy Version="1">
  <RuleCollection Type="Exe" EnforcementMode="Enabled">
    <FilePathRule Id="fd686d83-a829-4351-8ff4-27c7de5755d2" Name="Allow all files located in the Program Files folder" Description="" UserOrGroupSid="S-1-1-0" Action="Allow">
      <Conditions>
        <FilePathCondition Path="%PROGRAMFILES%\*"/>
      </Conditions>
    </FilePathRule>
  </RuleCollection>
</AppLockerPolicy>
```

#### Fine-Grained Password Policies
```powershell
# Create password settings object
New-ADFineGrainedPasswordPolicy -Name "ExecutivePasswordPolicy" -Precedence 10 -ComplexityEnabled $true -MinPasswordLength 12 -MaxPasswordAge 60.00:00:00

# Apply to security group
Add-ADFineGrainedPasswordPolicySubject -Identity "ExecutivePasswordPolicy" -Subjects "Executive Users"
```

### Monitoring and Auditing

#### Event Log Configuration
```powershell
# Configure audit policies
auditpol /set /subcategory:"Logon" /success:enable /failure:enable
auditpol /set /subcategory:"Account Logon" /success:enable /failure:enable
auditpol /set /subcategory:"Object Access" /success:enable /failure:enable
```

#### Advanced Threat Analytics (ATA)
- Deploy ATA for behavioral analysis
- Monitor suspicious activities
- Detect lateral movement
- Identify compromised credentials

#### PowerShell Logging
```powershell
# Enable PowerShell script block logging
Set-ItemProperty -Path "HKLM:\SOFTWARE\Wow6432Node\Policies\Microsoft\Windows\PowerShell\ScriptBlockLogging" -Name "EnableScriptBlockLogging" -Value 1
```

---

## Module 8: Advanced Security Features

### Privileged Access Management (PAM)

#### Just-In-Time (JIT) Administration
- Implement time-limited administrative access
- Use Microsoft Identity Manager (MIM)
- Configure PAM forest architecture
- Implement approval workflows

#### Administrative Tier Model
- **Tier 0**: Domain controllers, enterprise admins
- **Tier 1**: Servers and server administrators
- **Tier 2**: Workstations and standard users

### Credential Protection

#### Credential Guard
```powershell
# Enable Credential Guard via Group Policy
# Computer Configuration > Administrative Templates > System > Device Guard
# Turn On Virtualization Based Security = Enabled
# Platform Security Level = Secure Boot and DMA Protection
# Credential Guard Configuration = Enabled with UEFI lock
```

#### Local Administrator Password Solution (LAPS)
```powershell
# Install LAPS
# Configure Group Policy
# Set password complexity and rotation interval
Set-AdmPwdComputerSelfPermission -OrgUnit "OU=Workstations,DC=company,DC=local"
```

#### Protected Process Light (PPL)
- Protect authentication processes
- Prevent credential theft
- Use with Windows Defender Credential Guard

### Network Security

#### Network Segmentation
- Implement VLANs for different security zones
- Use firewalls between network segments
- Configure IPSec policies
- Implement Network Access Control (NAC)

#### Certificate Services Integration
```powershell
# Install Certificate Services
Install-WindowsFeature -Name ADCS-Cert-Authority -IncludeManagementTools

# Configure certificate templates
# Enable certificate autoenrollment via Group Policy
```

### Advanced Monitoring and Detection

#### Windows Event Forwarding (WEF)
```xml
<!-- Event forwarding subscription -->
<Subscription xmlns="http://schemas.microsoft.com/2006/03/windows/events/subscription">
    <SubscriptionId>SecurityEvents</SubscriptionId>
    <SubscriptionType>SourceInitiated</SubscriptionType>
    <Query>
        <![CDATA[
        <QueryList>
            <Query Id="0">
                <Select Path="Security">*[System[(EventID=4624 or EventID=4625)]]</Select>
            </Query>
        </QueryList>
        ]]>
    </Query>
</Subscription>
```

#### SIEM Integration
- Configure log forwarding to SIEM
- Create correlation rules
- Implement automated response
- Regular security assessments

---

## Module 9: Disaster Recovery and Business Continuity

### Backup Strategies

#### AD Database Backup
```powershell
# Windows Server Backup for System State
wbadmin start systemstatebackup -backuptarget:E: -quiet

# PowerShell backup
Checkpoint-Computer -Description "Pre-maintenance backup"
```

#### Authoritative Restore
```cmd
# Boot into Directory Services Restore Mode (DSRM)
# Use ntdsutil for authoritative restore
ntdsutil
activate instance ntds
authoritative restore
restore subtree "OU=Sales,DC=company,DC=local"
quit
quit
```

### Forest Recovery Procedures

#### Forest Recovery Planning
1. **Identify root cause** of forest corruption
2. **Isolate remaining DCs** from network
3. **Restore forest root domain** from backup
4. **Rebuild child domains** if necessary
5. **Restore trusts and replication**

#### Metadata Cleanup
```powershell
# Remove failed DC metadata
Remove-ADDomainController -Identity "FAILED-DC01" -Force
```

### High Availability Solutions

#### Multiple Domain Controllers
- Deploy DCs across different sites
- Implement load balancing
- Configure site link costs
- Monitor replication health

#### Read-Only Domain Controllers (RODC)
```powershell
# Install RODC
Install-ADDSDomainController -DomainName "company.local" -ReadOnlyReplica -SiteName "Branch-Site"
```

---

## Module 10: Troubleshooting and Maintenance

### Common Issues and Solutions

#### Authentication Problems
```powershell
# Test domain connectivity
Test-ComputerSecureChannel -Verbose

# Reset computer account
Reset-ComputerMachinePassword -Credential (Get-Credential)

# Check Kerberos tickets
klist tickets
```

#### Replication Issues
```powershell
# Check replication status
repadmin /replsummary

# Force replication
repadmin /syncall /A /P /d

# Check replication topology
repadmin /showrepl
```

#### DNS Problems
```powershell
# Test DNS resolution
nslookup company.local

# Check SRV records
nslookup -type=SRV _ldap._tcp.company.local

# Verify DC registration
dcdiag /test:dns
```

### Performance Monitoring

#### Key Performance Counters
- **NTDS\DRA Inbound Full Sync Objects Remaining**
- **NTDS\LDAP Client Sessions**
- **NTDS\LDAP Searches/sec**
- **Database\Database Cache % Hit**

#### Health Monitoring Tools
```powershell
# DCDiag comprehensive test
dcdiag /v /c /d /e /s:company.local

# Network connectivity test
dcdiag /test:connectivity

# Replication test
dcdiag /test:replications
```

### Maintenance Tasks

#### Regular Maintenance Checklist
- **Weekly**: Check event logs, verify backups
- **Monthly**: Review group memberships, audit permissions
- **Quarterly**: Update documentation, security assessments
- **Annually**: Password policy review, disaster recovery testing

#### Automated Maintenance Scripts
```powershell
# Daily health check script
$DCs = Get-ADDomainController -Filter *
foreach ($DC in $DCs) {
    Test-NetConnection -ComputerName $DC.HostName -Port 389
    if (Test-NetConnection -ComputerName $DC.HostName -Port 389) {
        Write-Output "$($DC.Name) is responding on LDAP port"
    } else {
        Write-Warning "$($DC.Name) is not responding on LDAP port"
        # Send alert email
    }
}
```

---

## Module 11: Integration and Modern Authentication

### Hybrid Identity with Azure AD

#### Azure AD Connect
```powershell
# Configure Azure AD Connect
# Synchronize on-premises AD with Azure AD
# Enable Password Hash Sync or Pass-through Authentication
# Configure Single Sign-On (SSO)
```

#### Modern Authentication Protocols
- **SAML 2.0**: Web-based SSO
- **OAuth 2.0**: Authorization framework
- **OpenID Connect**: Authentication layer on OAuth 2.0
- **WS-Federation**: Enterprise federation protocol

### Certificate-Based Authentication

#### PKI Infrastructure
```powershell
# Install Certificate Authority
Install-WindowsFeature -Name ADCS-Cert-Authority -IncludeManagementTools

# Configure certificate templates for authentication
# Enable certificate auto-enrollment
# Deploy certificates to users and computers
```

#### Smart Card Deployment
- Configure certificate templates
- Deploy smart card readers
- Configure Group Policy for smart card requirements
- Implement card management procedures

---

## Module 12: Security Assessment and Compliance

### Security Auditing

#### Regular Security Assessments
```powershell
# PowerShell script for privilege escalation check
Get-ADUser -Filter * -Properties MemberOf | Where-Object {
    $_.MemberOf -match "Domain Admins|Enterprise Admins|Schema Admins"
} | Select-Object Name, SamAccountName, @{Name="Groups";Expression={$_.MemberOf}}
```

#### Penetration Testing Considerations
- **External testing**: Test from internet perspective
- **Internal testing**: Test from network insider perspective
- **Social engineering**: Test user awareness
- **Physical security**: Test physical access controls

### Compliance Frameworks

#### Common Compliance Requirements
- **SOX**: Sarbanes-Oxley Act requirements
- **HIPAA**: Healthcare data protection
- **PCI-DSS**: Payment card industry standards
- **GDPR**: General Data Protection Regulation

#### Documentation Requirements
- **Security policies**: Written security procedures
- **Change management**: Documentation of changes
- **Incident response**: Security incident procedures
- **Access reviews**: Regular access audits

---

## Practical Lab Exercises

### Lab 1: Basic AD Setup
1. Install Windows Server 2019/2022
2. Promote to domain controller
3. Create organizational structure
4. Add users and computers
5. Configure basic Group Policy

### Lab 2: Advanced Security Configuration
1. Implement fine-grained password policies
2. Configure Protected Users group
3. Enable advanced auditing
4. Deploy LAPS
5. Configure Credential Guard

### Lab 3: Disaster Recovery
1. Perform system state backup
2. Simulate DC failure
3. Restore from backup
4. Test replication recovery
5. Document recovery procedures

### Lab 4: Security Assessment
1. Run security scanning tools
2. Identify security weaknesses
3. Implement remediation
4. Verify fixes
5. Create security report

---

## Advanced Topics and Best Practices

### Zero Trust Architecture
- Verify explicitly
- Use least privilege access
- Assume breach mentality

### DevSecOps Integration
- Infrastructure as Code (IaC)
- Automated security testing
- Continuous compliance monitoring

### Cloud Integration Strategies
- Hybrid identity models
- Conditional access policies
- Cloud security posture management

---

## Conclusion and Next Steps

This course has covered Active Directory from basic concepts to advanced security configurations. Continue your learning journey by:

1. **Hands-on Practice**: Set up lab environments
2. **Certifications**: Pursue Microsoft certification paths
3. **Community Engagement**: Join AD administrator communities
4. **Continuous Learning**: Stay updated with security trends
5. **Real-world Application**: Apply concepts in production environments

Remember that security is an ongoing process, not a one-time implementation. Regular assessment, monitoring, and improvement are essential for maintaining a secure Active Directory environment.
