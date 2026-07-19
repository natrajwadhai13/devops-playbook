---
title: "• Power Shell"
parent: "5. Scripting Languages"
nav_order: 4
has_children: true
---

Here’s a **Windows PowerShell Roadmap for DevOps Engineers**, especially useful if you work with **Windows servers**, **Active Directory**, **CI/CD on Windows nodes**, or **cloud infrastructure automation** (e.g., with Azure or hybrid environments).

---

## 🧭 PowerShell Roadmap for DevOps (4 Weeks Plan)

📘 **Goal**: Learn PowerShell to automate Windows infrastructure, scripting, cloud resources (e.g., Azure), and integrate with DevOps tools like Jenkins, Git, and Terraform.

---

### 🔰 **Week 1: PowerShell Basics & Scripting Fundamentals**

#### 🎯 Outcome:

- Run simple scripts, use loops and conditionals, manage files and processes

#### ✅ Topics:

- PowerShell Console vs ISE vs VSCode
- Cmdlets (`Get-Help`, `Get-Command`, `Get-Service`, etc.)
- Variables: `$var`, data types
- Operators: `-eq`, `-ne`, `-gt`, `-like`
- Conditional statements: `if`, `else`, `switch`
- Loops: `for`, `foreach`, `while`
- Functions: `function my-func {}`

#### 🧪 Example:

```powershell
$services = Get-Service
foreach ($s in $services) {
  if ($s.Status -eq 'Running') {
    Write-Output "$($s.Name) is running"
  }
}
```

---

### 🔧 **Week 2: File, Process, and System Administration**

#### 🎯 Outcome:

- Automate system administration tasks

#### ✅ Topics:

- File operations: `Get-Content`, `Set-Content`, `Out-File`, `Copy-Item`, `Remove-Item`
- Process management: `Get-Process`, `Stop-Process`, `Start-Process`
- Scheduled tasks: `Get-ScheduledTask`, `Register-ScheduledTask`
- Services: `Start-Service`, `Stop-Service`, `Restart-Service`
- Registry: `Get-Item`, `Set-Item`
- Environment Variables: `$env:PATH`

#### 🧪 Example:

```powershell
Get-ChildItem C:\Logs -Recurse | Where-Object { $_.Extension -eq ".log" } | Remove-Item
```

---

### ⚙️ **Week 3: PowerShell for DevOps Automation**

#### 🎯 Outcome:

- Use PowerShell in DevOps CI/CD pipelines

#### ✅ Topics:

- Git integration: `git clone`, `git status` (via CLI)
- Invoke REST APIs: `Invoke-RestMethod`, `Invoke-WebRequest`
- Work with JSON/YAML: `ConvertTo-Json`, `ConvertFrom-Json`
- Automate tasks in Jenkins pipeline using PowerShell scripts
- Use `psake` or PowerShell in CI/CD tools (Jenkins, Azure DevOps)
- Install software: `winget`, `choco`, `Install-Package`

#### 🧪 Example (REST API call):

```powershell
$response = Invoke-RestMethod -Uri "https://api.github.com/repos/microsoft/PowerShell"
$response.name
```

---

### ☁️ **Week 4: PowerShell for Cloud & Infrastructure as Code**

#### 🎯 Outcome:

- Manage cloud resources (especially Azure) using PowerShell

#### ✅ Topics:

- Azure PowerShell Module (`Az`): `Connect-AzAccount`, `Get-AzVM`, etc.
- AWS PowerShell Tools: `Get-AWSEC2Instance`, etc.
- Create/modify VMs, storage accounts, network interfaces
- Run PowerShell scripts in Terraform `provisioners`
- Automation scripts for Windows Server setup (IIS, .NET, roles)

#### 🧪 Azure Example:

```powershell
Install-Module -Name Az -AllowClobber -Force
Connect-AzAccount
Get-AzResourceGroup
```

---

## 🧪 Mini Project Ideas

| Project                                                                | Skills                    |
| ---------------------------------------------------------------------- | ------------------------- |
| Create a PowerShell script to monitor Windows Services and email alert | Scripting, services, SMTP |
| Automate IIS deployment using PowerShell                               | Windows + Webserver       |
| Schedule a PowerShell script to back up logs weekly                    | Task Scheduler, file mgmt |
| Deploy an Azure VM with tags using PowerShell                          | Az module, cloud          |
| Fetch and parse Jenkins build data via API                             | REST, JSON                |

---

## 📚 Learning Resources

- 📘 [Microsoft PowerShell Docs](https://docs.microsoft.com/en-us/powershell/)
- 📘 [SS64 PowerShell Reference](https://ss64.com/ps/)
- 💻 [PowerShell Gallery](https://www.powershellgallery.com/)
- 📺 YouTube: "PowerShell for Beginners – Full Course"
- 🎮 Practice: [Try PowerShell Online](https://aka.ms/psconsole)

---

## ⚒️ DevOps Tools Where PowerShell Helps

| Tool                    | Use                                    |
| ----------------------- | -------------------------------------- |
| **Jenkins**             | Run Windows build steps via PowerShell |
| **Azure DevOps**        | Pipelines with native PowerShell       |
| **Terraform**           | PowerShell provisioners for Windows    |
| **Packer**              | Provision Windows AMIs/VMs             |
| **Ansible (via WinRM)** | Target Windows hosts                   |

---

## 🧠 Best Practices

- Always use `-WhatIf` before executing changes
- Write reusable scripts with parameters (`param()`)
- Use logging (`Start-Transcript`)
- Comment your code generously
- Use modules for organization
