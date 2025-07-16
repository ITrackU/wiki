# 🚀 Windows PowerShell Automation Basics Course

## 📋 Table of Contents
1. [Introduction to PowerShell](#-introduction-to-powershell)
2. [Getting Started](#-getting-started)
3. [Basic PowerShell Syntax](#-basic-powershell-syntax)
4. [Variables and Data Types](#-variables-and-data-types)
5. [Working with Objects](#-working-with-objects)
6. [File and Folder Operations](#-file-and-folder-operations)
7. [Control Flow (Loops & Conditions)](#-control-flow-loops--conditions)
8. [Functions and Script Blocks](#-functions-and-script-blocks)
9. [Error Handling](#-error-handling)
10. [Creating Your First Automation Script](#-creating-your-first-automation-script)
11. [Advanced Topics](#-advanced-topics)
12. [Practice Exercises](#-practice-exercises)
13. [Resources](#-resources)

---

## 🎯 Introduction to PowerShell

### What is PowerShell?
PowerShell is like a super-powered command prompt that Microsoft created. Think of it as your digital assistant that can automate repetitive tasks on your computer. Instead of clicking through menus and dialog boxes hundreds of times, you can write a few lines of code to do the work for you.

### Why is PowerShell Different?
Traditional command prompts (like CMD) work with simple text. If you ask CMD "what processes are running?", it gives you a wall of text that's hard to work with. PowerShell is smarter - it gives you structured information (objects) that you can easily sort, filter, and manipulate.

**Example of the difference:**
```powershell
# In CMD, you get text that's hard to work with
tasklist

# In PowerShell, you get structured objects you can manipulate
Get-Process | Where-Object CPU -gt 100 | Sort-Object Name
```

The PowerShell command above means: "Get all running processes, find ones using more than 100 CPU units, then sort them by name." Try doing that easily with CMD!

### Key Features Explained:

**Object-oriented**: Everything in PowerShell is an "object" - think of objects as containers that hold both information (properties) and actions (methods). A file object contains its name, size, creation date, and actions like copy, move, or delete.

**Cross-platform**: Originally Windows-only, but now PowerShell runs on Mac and Linux too. It's like having the same powerful tool everywhere.

**Extensible**: You can add new capabilities by installing "modules" - think of them as expansion packs for PowerShell.

**Integration**: PowerShell talks directly to Windows and .NET Framework, giving it deep access to system functions.

### PowerShell vs Command Prompt - Visual Comparison:

| What you want to do | Command Prompt | PowerShell |
|---------------------|----------------|------------|
| List files bigger than 1MB | Complex batch scripting needed | `Get-ChildItem \| Where-Object Length -gt 1MB` |
| Stop all Chrome processes | Manual process killing | `Get-Process chrome \| Stop-Process` |
| Find files modified today | Very difficult | `Get-ChildItem \| Where-Object LastWriteTime -gt (Get-Date).Date` |

---

## 🏁 Getting Started

### Opening PowerShell - Step by Step

**Method 1: Using Windows Key**
1. Press `Windows Key + R` (this opens the Run dialog)
2. Type `powershell` and press Enter
3. A blue window opens - this is PowerShell!

**Method 2: Through Start Menu**
1. Click the Start button (Windows logo)
2. Type "PowerShell" in the search box
3. Click on "Windows PowerShell" when it appears

**Method 3: Right-click method (Quick)**
1. Right-click on the Start button (Windows logo)
2. Select "Windows PowerShell" from the menu

### Understanding the PowerShell Window

When PowerShell opens, you'll see something like this:
```
Windows PowerShell
Copyright (C) Microsoft Corporation. All rights reserved.

PS C:\Users\YourName>
```

Let's break this down:
- `PS` = This tells you you're in PowerShell (not regular command prompt)
- `C:\Users\YourName` = This is your current location (like your GPS position in the file system)
- `>` = This is the prompt, waiting for your command

### Your First Commands - Let's Try Them!

**Check PowerShell Version:**
```powershell
$PSVersionTable
```
This command shows you information about your PowerShell installation. The `$` at the beginning means this is a "variable" - a container holding information. `PSVersionTable` is a built-in variable that contains version details.

**Get Current Date and Time:**
```powershell
Get-Date
```
This is your first real cmdlet! It follows the Verb-Noun pattern: `Get` (the action) + `Date` (what you want). PowerShell will show you the current date and time in a nice format.

**List Available Commands:**
```powershell
Get-Command
```
This shows you ALL the commands available in PowerShell. Don't worry about the long list - you'll learn the important ones gradually. Think of this as looking at a toolbox to see what tools are available.

**Get Help for Any Command:**
```powershell
Get-Help Get-Process
```
This is like asking for the instruction manual for a specific command. `Get-Help` is your best friend - whenever you don't know how to use a command, ask for help!

### Execution Policy - Why Your Scripts Might Not Work

Before you can run PowerShell scripts (files ending in `.ps1`), you need to tell Windows it's okay to run them. This is a security feature.

**Check Current Policy:**
```powershell
Get-ExecutionPolicy
```
This shows your current security setting. You might see:
- `Restricted` = No scripts allowed (default and most secure)
- `RemoteSigned` = Local scripts okay, downloaded scripts need digital signatures
- `Unrestricted` = All scripts allowed (least secure)

**Change Policy (requires Administrator rights):**
```powershell
Set-ExecutionPolicy RemoteSigned
```
This command tells Windows: "Allow me to run scripts I create locally, but require signatures for scripts downloaded from the internet." This is a good balance of security and functionality.

**How to run as Administrator:**
1. Right-click on PowerShell in Start Menu
2. Choose "Run as Administrator"
3. Click "Yes" when Windows asks for permission
4. You'll see "Administrator: Windows PowerShell" in the title bar

---

## 🔤 Basic PowerShell Syntax

### The Verb-Noun Pattern - PowerShell's Language Rule

PowerShell commands (called cmdlets) follow a simple pattern: **Action-Target** or **Verb-Noun**. This makes commands predictable and easy to remember.

**Examples with explanations:**
```powershell
Get-Process     # Get (action) + Process (target) = Show me running programs
Stop-Service    # Stop (action) + Service (target) = Turn off a Windows service
New-Item        # New (action) + Item (target) = Create a file or folder
Copy-Item       # Copy (action) + Item (target) = Duplicate a file or folder
```

### Common Verbs and What They Mean:

| Verb | What it does | Example | Real-world equivalent |
|------|--------------|---------|----------------------|
| **Get** | Retrieves/shows information | `Get-Process` | "Show me what's running" |
| **Set** | Changes/modifies something | `Set-Location` | "Go to this folder" |
| **New** | Creates something | `New-Folder` | "Make a new folder" |
| **Remove** | Deletes something | `Remove-Item` | "Delete this file" |
| **Start** | Begins/launches something | `Start-Service` | "Turn on this service" |
| **Stop** | Ends/terminates something | `Stop-Process` | "Close this program" |
| **Import** | Brings data in | `Import-Module` | "Load this toolset" |
| **Export** | Sends data out | `Export-Csv` | "Save to spreadsheet" |

### Parameters - Adding Details to Commands

Parameters are like giving specific instructions to a command. They start with a dash (`-`) and tell the command exactly what you want.

**Basic Parameter Usage:**
```powershell
# Get information about a specific process
Get-Process -Name "notepad"
```
Breaking this down:
- `Get-Process` = The command (show me running programs)
- `-Name` = The parameter (specifically, I want to filter by name)
- `"notepad"` = The value (show me only Notepad)

**Multiple Parameters:**
```powershell
# List files in Windows folder, but only .exe files
Get-ChildItem -Path "C:\Windows" -Filter "*.exe"
```
Breaking this down:
- `Get-ChildItem` = List files and folders
- `-Path "C:\Windows"` = Look in the Windows folder
- `-Filter "*.exe"` = Only show executable files (programs)

**Parameters with Spaces (use quotes):**
```powershell
# Get information about Windows Explorer process
Get-Process -Name "Windows Explorer"
```
When the value has spaces, put it in quotes so PowerShell knows it's all one piece of information.

### Aliases - PowerShell's Shortcuts

PowerShell provides shortcuts (aliases) for common commands. These are like nicknames that save typing.

**Common Aliases:**
```powershell
# These three commands do exactly the same thing:
Get-ChildItem       # Full cmdlet name
gci                 # PowerShell alias
ls                  # Unix-style alias
dir                 # DOS-style alias

# More examples:
cd    = Set-Location     # Change directory
pwd   = Get-Location     # Show current directory
cat   = Get-Content      # Show file contents
ps    = Get-Process      # Show running processes
cls   = Clear-Host       # Clear the screen
```

**Why aliases exist:** They make PowerShell familiar to people coming from different backgrounds (Unix, DOS, etc.) and save typing for frequently used commands.

### The Pipeline (|) - PowerShell's Assembly Line

The pipeline is PowerShell's most powerful feature. It's like an assembly line where each command does one job, then passes the result to the next command.

**How the Pipeline Works:**
Think of it like a factory assembly line:
1. First station makes car frames
2. Second station adds engines  
3. Third station adds wheels
4. Final station paints the cars

In PowerShell:
```powershell
Get-Process | Sort-Object CPU -Descending | Select-Object -First 5
```

Step by step:
1. `Get-Process` = Get all running programs (like getting all car chassis)
2. `|` = Pass results to next command (move down assembly line)
3. `Sort-Object CPU -Descending` = Sort by CPU usage, highest first (organize by priority)
4. `|` = Pass sorted results to next command
5. `Select-Object -First 5` = Take only the first 5 results (quality control - only take the best)

**More Pipeline Examples with Explanations:**

```powershell
# Get all services, find only running ones, sort by name
Get-Service | Where-Object Status -eq "Running" | Sort-Object Name
```
1. Get all Windows services
2. Filter to show only running services
3. Sort them alphabetically by name

```powershell
# Get all files, count how many there are
Get-ChildItem | Measure-Object
```
1. Get all files in current folder
2. Count them (Measure-Object counts things)

```powershell
# Get all .txt files, show their names and sizes
Get-ChildItem *.txt | Select-Object Name, Length
```
1. Get all text files
2. Show only their names and file sizes

### Understanding Command Output

When you run a command, PowerShell shows you the results in a formatted way:

```powershell
Get-Process
```

You'll see columns like:
- **Handles**: How many system resources the program is using
- **NPM(K)**: Non-paged memory (technical memory info)
- **PM(K)**: Physical memory the program is using
- **WS(K)**: Working set (another memory measurement)
- **CPU(s)**: How much processor time the program has used
- **Id**: Unique number identifying the process
- **SI**: Session ID
- **ProcessName**: The actual name of the program

Don't worry about understanding every column - focus on **ProcessName** (what program it is) and **CPU(s)** (how much it's using your processor).

---

## 📊 Variables and Data Types

### What are Variables?

Variables are like labeled boxes where you store information. Just like you might have a box labeled "Photos" containing your pictures, PowerShell variables have names and contain data you want to use later.

### Creating Variables - The Basics

In PowerShell, all variable names start with a dollar sign (`$`). Think of the `$` as saying "this is a container for information."

**String Variables (Text):**
```powershell
# Create a variable containing text
$name = "John Doe"
$greeting = "Hello, welcome to PowerShell!"

# Use the variable
Write-Output $name
Write-Output $greeting
```

**What happens here:**
1. `$name = "John Doe"` creates a box labeled "name" and puts "John Doe" inside
2. `Write-Output $name` tells PowerShell "look in the box labeled name and show me what's inside"

**Number Variables:**
```powershell
# Whole numbers (integers)
$age = 30
$year = 2024

# Decimal numbers (floating point)
$price = 19.99
$temperature = 98.6

# Using number variables in calculations
$total = $price * 2
Write-Output "Double the price is: $total"
```

**Boolean Variables (True/False):**
```powershell
# Boolean variables store yes/no, true/false values
$isActive = $true
$isComplete = $false

# Useful for tracking status
if ($isActive) {
    Write-Output "The system is active"
}
```

### Arrays - Lists of Information

Arrays are like having multiple items in one container. Think of an array as a shopping list or a box containing multiple photos.

**Creating Arrays:**
```powershell
# Array of colors (multiple text values)
$colors = @("Red", "Green", "Blue", "Yellow")

# Array of numbers
$numbers = 1, 2, 3, 4, 5

# Mixed array (different types of data)
$mixedArray = @("Hello", 42, $true, "World")
```

**Working with Arrays:**
```powershell
# Show all items in the array
Write-Output $colors

# Show specific items (arrays start counting from 0)
Write-Output $colors[0]    # Shows "Red" (first item)
Write-Output $colors[2]    # Shows "Blue" (third item)

# Add items to an array
$colors += "Purple"        # Add Purple to the list

# Count items in array
$colors.Count              # Shows how many colors we have
```

**Real-world Array Example:**
```powershell
# List of servers to check
$servers = @("Server1", "Server2", "Server3")

# Check each server (we'll learn loops later)
foreach ($server in $servers) {
    Write-Output "Checking server: $server"
    Test-Connection -ComputerName $server -Count 1
}
```

### Hash Tables - Organized Information Storage

Hash tables are like filing cabinets where each piece of information has a specific label. They store "key-value pairs" - think of them as a dictionary where you look up a word (key) to find its definition (value).

**Creating Hash Tables:**
```powershell
# Employee information
$employee = @{
    Name = "Alice Johnson"
    Age = 28
    Department = "IT"
    Salary = 65000
    IsManager = $false
}
```

**Using Hash Tables:**
```powershell
# Access specific information
Write-Output $employee.Name        # Shows "Alice Johnson"
Write-Output $employee.Department  # Shows "IT"

# Add new information
$employee.Location = "New York"

# Change existing information
$employee.Age = 29

# Show all information
$employee
```

**Real-world Hash Table Example:**
```powershell
# Server configuration
$serverConfig = @{
    ServerName = "WebServer01"
    IPAddress = "192.168.1.100"
    OperatingSystem = "Windows Server 2022"
    RAM = "16GB"
    Services = @("IIS", "SQL Server", "Print Spooler")
}

Write-Output "Server $($serverConfig.ServerName) has IP $($serverConfig.IPAddress)"
```

### Automatic Variables - PowerShell's Built-in Information

PowerShell automatically creates variables containing useful information about your system and current session.

**Essential Automatic Variables:**
```powershell
# PowerShell version information
$PSVersionTable

# Current user information
$env:USERNAME        # Your login name
$env:COMPUTERNAME    # Your computer's name
$env:USERPROFILE     # Path to your user folder (like C:\Users\YourName)

# System paths
$env:PATH            # Where Windows looks for programs
$env:TEMP            # Temporary files folder

# Current working directory
$pwd                 # Where you are right now in the file system
```

**Using Environment Variables:**
```powershell
# Create a path to your desktop
$desktopPath = "$env:USERPROFILE\Desktop"
Write-Output "Your desktop is located at: $desktopPath"

# Create a backup folder with your username
$backupFolder = "C:\Backups\$env:USERNAME"
Write-Output "Your backup folder would be: $backupFolder"
```

### Variable Scope - Where Variables Live

Variable scope determines where your variables can be used. Think of it like the difference between notes you keep private, notes you share with your family, and notes you post publicly.

**Local Scope (Default):**
```powershell
# This variable only exists in the current PowerShell session
$localMessage = "This is local"

function Test-Function {
    # This variable only exists inside this function
    $functionVariable = "Inside function only"
    Write-Output $functionVariable
}
```

**Global Scope:**
```powershell
# This variable can be used anywhere in your PowerShell session
$global:globalMessage = "Available everywhere"

function Test-Global {
    # This function can access the global variable
    Write-Output $global:globalMessage
}
```

**Script Scope:**
```powershell
# In a .ps1 script file, this variable is available throughout the entire script
$script:scriptVariable = "Available in this script"
```

### String Interpolation - Mixing Variables with Text

String interpolation lets you embed variables directly into text strings. It's like filling in blanks in a form letter.

**Basic String Interpolation:**
```powershell
$name = "Sarah"
$age = 25

# Put variables directly in double-quoted strings
$message = "Hello, my name is $name and I am $age years old."
Write-Output $message
```

**Advanced String Interpolation:**
```powershell
$employee = @{
    Name = "Bob Smith"
    Department = "Sales"
}

# Use $() for complex expressions
$report = "Employee $($employee.Name) works in $($employee.Department)"
Write-Output $report

# Math in strings
$price = 15.99
$quantity = 3
$total = "Total cost: $($price * $quantity)"
Write-Output $total
```

**Single vs Double Quotes:**
```powershell
$name = "Alice"

# Double quotes: Variables are replaced with their values
$doubleQuoted = "Hello $name"      # Shows: Hello Alice

# Single quotes: Variables are treated as literal text
$singleQuoted = 'Hello $name'      # Shows: Hello $name
```

This is important: Use double quotes when you want PowerShell to replace variables with their values, use single quotes when you want the literal text including the dollar sign.

---

## 🎁 Working with Objects

### What are Objects in PowerShell?

In the real world, objects have properties (characteristics) and can perform actions. A car has properties like color, model, and speed, and can perform actions like start, stop, and turn. PowerShell objects work the same way.

**Real-world analogy:**
- A **file** object has properties like name, size, creation date
- A **file** object can perform actions like copy, move, delete
- A **process** object has properties like name, memory usage, CPU usage
- A **process** object can perform actions like start, stop, pause

### Exploring Objects - Looking Under the Hood

Every piece of information in PowerShell is an object. Let's explore what's inside these objects.

**Getting an Object:**
```powershell
# Get current date and time (this creates a DateTime object)
$date = Get-Date
```

**Finding Out What Type of Object You Have:**
```powershell
# What kind of object is this?
$date.GetType()
```
This shows you it's a `System.DateTime` object - PowerShell's way of handling dates and times.

**Discovering Object Properties and Methods:**
```powershell
# See everything this object can do
$date | Get-Member
```

When you run this, you'll see two types of information:
- **Properties**: Information stored in the object (like Year, Month, Day)
- **Methods**: Actions the object can perform (like AddDays, ToString)

**Understanding the Get-Member Output:**
```
TypeName: System.DateTime

Name         MemberType   Definition
----         ----------   ----------
AddDays      Method       datetime AddDays(double value)
AddHours     Method       datetime AddHours(double value)
Year         Property     int Year {get;}
Month        Property     int Month {get;}
Day          Property     int Day {get;}
```

- **Methods** are actions (they do something)
- **Properties** are information (they contain data)

### Working with Object Properties

Properties are like looking at information labels on an object.

**Accessing Properties:**
```powershell
$date = Get-Date

# Access individual properties
Write-Output "Current year: $($date.Year)"
Write-Output "Current month: $($date.Month)"
Write-Output "Day of week: $($date.DayOfWeek)"
```

**Real-world Example - File Properties:**
```powershell
# Get information about a file
$file = Get-Item "C:\Windows\notepad.exe"

# Look at the file's properties
Write-Output "File name: $($file.Name)"
Write-Output "File size: $($file.Length) bytes"
Write-Output "Created: $($file.CreationTime)"
Write-Output "Last modified: $($file.LastWriteTime)"
```

### Working with Object Methods

Methods are like asking an object to perform an action.

**Using Methods:**
```powershell
$date = Get-Date

# Add 7 days to the current date
$futureDate = $date.AddDays(7)
Write-Output "One week from now: $futureDate"

# Format the date in a specific way
$formattedDate = $date.ToString("yyyy-MM-dd")
Write-Output "Formatted date: $formattedDate"
```

**Method Syntax Explained:**
- `$date.AddDays(7)` means "ask the date object to add 7 days to itself"
- The parentheses `()` contain the information the method needs
- Some methods need information (parameters), others don't

**Methods vs Functions:**
```powershell
# Method: belongs to an object
$date.AddDays(7)        # Ask the date object to add days

# Function: standalone command
Add-Content -Path "file.txt" -Value "Hello"  # Standalone command
```

### Common Object Operations with Pipeline

The pipeline becomes incredibly powerful when working with objects because you can chain operations together.

**Select Specific Properties:**
```powershell
# Show only name and CPU usage for processes
Get-Process | Select-Object Name, CPU

# Show only the top 5 properties you care about
Get-Process | Select-Object Name, CPU, WorkingSet | Format-Table
```

**Filter Objects:**
```powershell
# Show only running services
Get-Service | Where-Object Status -eq "Running"

# Show only processes using more than 100MB of memory
Get-Process | Where-Object WorkingSet -gt 100MB
```

**Sort Objects:**
```powershell
# Sort processes by memory usage (highest first)
Get-Process | Sort-Object WorkingSet -Descending

# Sort services by name alphabetically
Get-Service | Sort-Object Name
```

**Group Objects:**
```powershell
# Group processes by their status
Get-Process | Group-Object ProcessName

# Group services by their status (Running, Stopped)
Get-Service | Group-Object Status
```

### Practical Object Examples

**Example 1: Analyzing Running Programs**
```powershell
# Find the top 5 programs using the most memory
Get-Process | 
    Sort-Object WorkingSet -Descending | 
    Select-Object -First 5 Name, @{Name="Memory(MB)"; Expression={[math]::Round($_.WorkingSet / 1MB, 2)}}
```

Step by step:
1. `Get-Process` = Get all running programs
2. `Sort-Object WorkingSet -Descending` = Sort by memory usage, highest first
3. `Select-Object -First 5` = Take only the first 5 results
4. `@{Name="Memory(MB)"; Expression={...}}` = Create a custom column showing memory in MB

**Example 2: Finding Large Files**
```powershell
# Find files larger than 100MB in your Documents folder
Get-ChildItem "$env:USERPROFILE\Documents" -Recurse | 
    Where-Object Length -gt 100MB | 
    Select-Object Name, @{Name="Size(MB)"; Expression={[math]::Round($_.Length / 1MB, 2)}} |
    Sort-Object "Size(MB)" -Descending
```

Step by step:
1. `Get-ChildItem` = Get all files and folders
2. `-Recurse` = Look in subfolders too
3. `Where-Object Length -gt 100MB` = Only files bigger than 100MB
4. `Select-Object` = Show name and size in MB
5. `Sort-Object` = Order by size, largest first

### Understanding Custom Properties

Sometimes you want to display information in a different format. Custom properties let you create calculated columns.

**Custom Property Syntax:**
```powershell
@{Name="ColumnName"; Expression={calculation}}
```

**Examples:**
```powershell
# Convert bytes to MB for easier reading
Get-ChildItem | Select-Object Name, @{Name="SizeMB"; Expression={$_.Length / 1MB}}

# Show how many days since file was modified
Get-ChildItem | Select-Object Name, @{Name="DaysOld"; Expression={(Get-Date) - $_.LastWriteTime | Select-Object -ExpandProperty Days}}

# Create a status column based on conditions
Get-Service | Select-Object Name, Status, @{Name="StatusDescription"; Expression={
    if ($_.Status -eq "Running") { "Active" } else { "Inactive" }
}}
```

The `$_` symbol represents "the current object" as it moves through the pipeline. Think of it as "the item I'm currently looking at."

---

## 📁 File and Folder Operations

### Understanding the File System in PowerShell

Think of your computer's file system like a giant filing cabinet. Each drive (C:, D:, etc.) is like a drawer, folders are like manila folders inside those drawers, and files are the documents inside those folders. PowerShell helps you navigate and manage this filing system.

### Navigation - Moving Around Your Computer

**Understanding Your Current Location:**
```powershell
# Where am I right now?
Get-Location
# or use the short version:
pwd
```

This shows your current "working directory" - think of it as your current position in the filing cabinet. You might see something like `C:\Users\YourName`, which means you're in your user folder.

**Changing Directories (Moving Around):**
```powershell
# Go to a specific folder
Set-Location "C:\Windows"
# or use the short version:
cd "C:\Windows"

# Go up one level (to the parent folder)
cd ..

# Go to your home directory
cd ~
# or
cd $env:USERPROFILE

# Go to the root of C: drive
cd C:\
```

**Navigation Tips:**
```powershell
# Save your current location before exploring
Push-Location

# Do some work in other folders...
cd "C:\Program Files"
cd "C:\Windows"

# Return to where you were
Pop-Location
```

Think of `Push-Location` and `Pop-Location` like breadcrumbs - you drop a breadcrumb before exploring, then follow it back home.

### Listing Files and Folders - Seeing What's There

**Basic File Listing:**
```powershell
# List everything in current folder
Get-ChildItem
# Short versions that do the same thing:
ls
dir
gci
```

**Understanding the Output:**
When you run `Get-ChildItem`, you see columns like:
- **Mode**: File type and permissions (d = directory/folder, a = archive/file, r = read-only)
- **LastWriteTime**: When the file was last changed
- **Length**: File size in bytes (empty for folders)
- **Name**: The file or folder name

**Advanced Listing Options:**
```powershell
# Show file details in a table format
Get-ChildItem | Format-Table Name, Length, LastWriteTime

# List only files (not folders)
Get-ChildItem -File

# List only folders
Get-ChildItem -Directory

# List hidden files too
Get-ChildItem -Force

# Look in subfolders too (recursive)
Get-ChildItem -Recurse
```

**Filtering Files:**
```powershell
# Show only .txt files
Get-ChildItem -Filter "*.txt"

# Show only files that start with "test"
Get-ChildItem -Filter "test*"

# Multiple file types
Get-ChildItem -Include "*.txt", "*.doc", "*.pdf" -Recurse

# Exclude certain files
Get-ChildItem -Exclude "*.tmp"
```

**Real-world Examples:**
```powershell
# Find all PowerShell scripts in your Documents folder
Get-ChildItem "$env:USERPROFILE\Documents" -Filter "*.ps1" -Recurse

# Find large files (bigger than 100MB) in current folder
Get-ChildItem | Where-Object Length -gt 100MB

# Find files modified in the last 7 days
Get-ChildItem | Where-Object LastWriteTime -gt (Get-Date).AddDays(-7)
```

### Creating Files and Folders

**Creating Folders:**
```powershell
# Create a single folder
New-Item -Path "C:\MyScripts" -ItemType Directory

# Create multiple folders at once
New-Item -Path "C:\Projects\Project1", "C:\Projects\Project2" -ItemType Directory

# Create nested folders (creates parent folders if they don't exist)
New-Item -Path "C:\Deep\Nested\Folder\Structure" -ItemType Directory -Force
```

**Creating Files:**
```powershell
# Create an empty file
New-Item -Path "C:\MyScripts\test.txt" -ItemType File

# Create a file with content
"Hello World" | Out-File -FilePath "C:\MyScripts\hello.txt"

# Another way to create file with content
Set-Content -Path "C:\MyScripts\greeting.txt" -Value "Welcome to PowerShell!"
```

**Understanding ItemType:**
- `Directory` = Create a folder
- `File` = Create a file
- The `-Force` parameter means "create it even if parent folders don't exist"

### Copying Files and Folders

**Basic Copying:**
```powershell
# Copy a single file
Copy-Item -Path "source.txt" -Destination "backup.txt"

# Copy to a different folder
Copy-Item -Path "C:\Data\important.txt" -Destination "C:\Backup\important.txt"

# Copy and rename at the same time
Copy-Item -Path "report.doc" -Destination "C:\Archive\report_backup.doc"
```

**Copying Folders:**
```powershell
# Copy an entire folder and all its contents
Copy-Item -Path "C:\SourceFolder" -Destination "C:\BackupFolder" -Recurse

# Copy all files from one folder to another
Copy-Item -Path "C:\Source\*" -Destination "C:\Backup\" -Recurse
```

**Advanced Copying:**
```powershell
# Copy only specific file types
Copy-Item -Path "C:\Documents\*.pdf" -Destination "C:\PDFBackup\"

# Copy files but preserve folder structure
Copy-Item -Path "C:\Projects\*" -Destination "C:\ProjectBackup\" -Recurse

# Copy only if destination doesn't exist (don't overwrite)
Copy-Item -Path "source.txt" -Destination "backup.txt" -NoClobber

# Copy with confirmation for each file
Copy-Item -Path "C:\Source\*" -Destination "C:\Backup\" -Confirm
```

**Real-world Copying Examples:**
```powershell
# Backup all your PowerShell scripts
Copy-Item -Path "$env:USERPROFILE\Documents\*.ps1" -Destination "C:\ScriptBackup\" -Recurse

# Copy today's log files to archive
$today = Get-Date -Format "yyyy-MM-dd"
Copy-Item -Path "C:\Logs\*$today*" -Destination "C:\Archive\$today\"
```

### Moving and Renaming Files

**Moving Files:**
```powershell
# Move a file to different location
Move-Item -Path "oldlocation\file.txt" -Destination "newlocation\file.txt"

# Move all files from one folder to another
Move-Item -Path "C:\Temp\*" -Destination "C:\Archive\"

# Move specific file types
Move-Item -Path "C:\Downloads\*.zip" -Destination "C:\Archives\ZipFiles\"
```

**Renaming Files:**
```powershell
# Rename a single file
Rename-Item -Path "oldname.txt" -NewName "newname.txt"

# Rename with current date
$date = Get-Date -Format "yyyy-MM-dd"
Rename-Item -Path "report.txt" -NewName "report_$date.txt"
```

**Bulk Renaming:**
```powershell
# Add prefix to all .jpg files
Get-ChildItem -Filter "*.jpg" | ForEach-Object {
    Rename-Item -Path $_.FullName -NewName "Photo_$($_.Name)"
}

# Change file extensions from .txt to .bak
Get-ChildItem -Filter "*.txt" | ForEach-Object {
    $newName = $_.Name -replace '\.txt$', '.bak'
    Rename-Item -Path $_.FullName -NewName $newName
}
```

### Deleting Files and Folders

**Safe Deletion:**
```powershell
# Delete a single file
Remove-Item -Path "unwanted.txt"

# Delete with confirmation
Remove-Item -Path "important.txt" -Confirm

# Delete multiple files
Remove-Item -Path "temp1.txt", "temp2.txt", "temp3.txt"
```

**Advanced Deletion:**
```powershell
# Delete all files older than 30 days
Get-ChildItem | Where-Object LastWriteTime -lt (Get-Date).AddDays(-30) | Remove-Item

# Delete empty folders
Get-ChildItem -Directory | Where-Object {(Get-ChildItem $_.FullName).Count -eq 0} | Remove-Item

# Delete files by size (larger than 100MB)
Get-ChildItem | Where-Object Length -gt 100MB | Remove-Item -Confirm

# Delete all .tmp files recursively
Get-ChildItem -Filter "*.tmp" -Recurse | Remove-Item
```

**Recycle Bin vs Permanent Deletion:**
```powershell
# Send to Recycle Bin (safer)
Remove-Item -Path "file.txt"

# Permanent deletion (bypass Recycle Bin)
Remove-Item -Path "file.txt" -Force

# Delete folder and all contents permanently
Remove-Item -Path "C:\TempFolder" -Recurse -Force
```

### Working with File Content

**Reading File Content:**
```powershell
# Read entire file content
Get-Content -Path "document.txt"

# Read only first 10 lines
Get-Content -Path "largefile.txt" -TotalCount 10

# Read last 5 lines
Get-Content -Path "logfile.txt" -Tail 5

# Monitor file for new content (like tail -f in Unix)
Get-Content -Path "live.log" -Wait
```

**Writing to Files:**
```powershell
# Write text to a new file (overwrites existing)
"Hello World" | Out-File -FilePath "greeting.txt"

# Append text to existing file
"New line" | Out-File -FilePath "greeting.txt" -Append

# Write multiple lines
@"
Line 1
Line 2
Line 3
"@ | Out-File -FilePath "multiline.txt"

# Write array elements as separate lines
$fruits = @("Apple", "Banana", "Orange")
$fruits | Out-File -FilePath "fruits.txt"
```

**Advanced File Operations:**
```powershell
# Search for text in files
Select-String -Path "*.txt" -Pattern "error"

# Replace text in files
(Get-Content "config.txt") -replace "old_server", "new_server" | Set-Content "config.txt"

# Count lines in a file
(Get-Content "document.txt" | Measure-Object -Line).Lines

# Find and replace across multiple files
Get-ChildItem -Filter "*.config" | ForEach-Object {
    (Get-Content $_.FullName) -replace "localhost", "production-server" | Set-Content $_.FullName
}
```

### Practical File Management Examples

**Example 1: Organize Downloads Folder**
```powershell
# Create organized folder structure
$downloadPath = "$env:USERPROFILE\Downloads"
$categories = @{
    "Images" = @("*.jpg", "*.png", "*.gif", "*.bmp")
    "Documents" = @("*.pdf", "*.doc", "*.docx", "*.txt")
    "Archives" = @("*.zip", "*.rar", "*.7z")
    "Executables" = @("*.exe", "*.msi")
}

foreach ($category in $categories.Keys) {
    $categoryPath = "$downloadPath\$category"
    if (-not (Test-Path $categoryPath)) {
        New-Item -Path $categoryPath -ItemType Directory
    }
    
    foreach ($extension in $categories[$category]) {
        Get-ChildItem -Path $downloadPath -Filter $extension | Move-Item -Destination $categoryPath
    }
}
```

**Example 2: Backup Script**
```powershell
# Daily backup script
$sourceFolder = "C:\ImportantData"
$backupRoot = "D:\Backups"
$date = Get-Date -Format "yyyy-MM-dd"
$backupFolder = "$backupRoot\Backup_$date"

# Create backup folder if it doesn't exist
if (-not (Test-Path $backupFolder)) {
    New-Item -Path $backupFolder -ItemType Directory
}

# Copy all files
Copy-Item -Path "$sourceFolder\*" -Destination $backupFolder -Recurse

# Create log entry
$logEntry = "$(Get-Date): Backup completed to $backupFolder"
$logEntry | Out-File -FilePath "$backupRoot\backup.log" -Append

Write-Output "Backup completed successfully!"
```

---

## 🔄 Control Flow (Loops & Conditions)

### Understanding Control Flow

Control flow is like giving PowerShell a set of decisions to make and actions to repeat. It's similar to following a recipe: "If the oven is hot, put the cake in. While the timer is running, wait. For each guest, serve one slice."

### If Statements - Making Decisions

**Basic If Statement:**
```powershell
$temperature = 75

if ($temperature -gt 70) {
    Write-Output "It's warm outside!"
}
```

**If-Else Statement:**
```powershell
$time = (Get-Date).Hour

if ($time -lt 12) {
    Write-Output "Good morning!"
} else {
    Write-Output "Good afternoon or evening!"
}
```

**If-ElseIf-Else Chain:**
```powershell
$score = 85

if ($score -ge 90) {
    Write-Output "Grade: A"
} elseif ($score -ge 80) {
    Write-Output "Grade: B"
} elseif ($score -ge 70) {
    Write-Output "Grade: C"
} elseif ($score -ge 60) {
    Write-Output "Grade: D"
} else {
    Write-Output "Grade: F"
}
```

### Comparison Operators - How to Compare Things

PowerShell uses specific operators for comparisons:

| Operator | Meaning | Example |
|----------|---------|---------|
| `-eq` | Equal to | `$a -eq $b` |
| `-ne` | Not equal to | `$a -ne $b` |
| `-lt` | Less than | `$a -lt $b` |
| `-le` | Less than or equal | `$a -le $b` |
| `-gt` | Greater than | `$a -gt $b` |
| `-ge` | Greater than or equal | `$a -ge $b` |
| `-like` | Wildcard match | `$name -like "J*"` |
| `-match` | Regular expression | `$email -match "@gmail"` |

**Real-world Comparison Examples:**
```powershell
# Check disk space
$freeSpace = (Get-WmiObject -Class Win32_LogicalDisk -Filter "DeviceID='C:'").FreeSpace / 1GB
if ($freeSpace -lt 10) {
    Write-Output "Warning: Low disk space! Only $freeSpace GB remaining."
}

# Check if a service is running
$service = Get-Service -Name "Spooler"
if ($service.Status -eq "Running") {
    Write-Output "Print Spooler is running normally."
} else {
    Write-Output "Print Spooler is not running. Starting service..."
    Start-Service -Name "Spooler"
}

# Check file existence
if (Test-Path "C:\ImportantFile.txt") {
    Write-Output "Important file exists."
} else {
    Write-Output "Warning: Important file is missing!"
}
```

### Logical Operators - Combining Conditions

**AND Operator (-and):**
```powershell
$age = 25
$hasLicense = $true

if ($age -ge 18 -and $hasLicense) {
    Write-Output "You can drive!"
} else {
    Write-Output "You cannot drive."
}
```

**OR Operator (-or):**
```powershell
$day = (Get-Date).DayOfWeek
if ($day -eq "Saturday" -or $day -eq "Sunday") {
    Write-Output "It's the weekend!"
} else {
    Write-Output "It's a weekday."
}
```

**NOT Operator (-not or !):**
```powershell
$fileExists = Test-Path "config.txt"
if (-not $fileExists) {
    Write-Output "Config file is missing. Creating default..."
    "default settings" | Out-File "config.txt"
}
```

### For Loops - Repeating Actions a Specific Number of Times

**Basic For Loop:**
```powershell
# Count from 1 to 5
for ($i = 1; $i -le 5; $i++) {
    Write-Output "Count: $i"
}
```

**Breaking down the for loop syntax:**
- `$i = 1` = Start with i equals 1
- `$i -le 5` = Continue as long as i is less than or equal to 5
- `$i++` = Add 1 to i each time through the loop

**Practical For Loop Examples:**
```powershell
# Create multiple folders
for ($i = 1; $i -le 10; $i++) {
    New-Item -Path "C:\TestFolders\Folder$i" -ItemType Directory -Force
}

# Generate multiple test files
for ($i = 1; $i -le 5; $i++) {
    "This is test file number $i" | Out-File -FilePath "test$i.txt"
}

# Countdown timer
for ($i = 10; $i -ge 1; $i--) {
    Write-Output "Starting in $i seconds..."
    Start-Sleep -Seconds 1
}
Write-Output "Go!"
```

### ForEach Loops - Working with Collections

**ForEach with Arrays:**
```powershell
$fruits = @("Apple", "Banana", "Orange", "Grape")

foreach ($fruit in $fruits) {
    Write-Output "I like $fruit"
}
```

**ForEach with File Operations:**
```powershell
# Process all .txt files in current directory
$textFiles = Get-ChildItem -Filter "*.txt"

foreach ($file in $textFiles) {
    Write-Output "Processing file: $($file.Name)"
    Write-Output "Size: $($file.Length) bytes"
    Write-Output "Modified: $($file.LastWriteTime)"
    Write-Output "---"
}
```

**ForEach with Services:**
```powershell
$servicesToCheck = @("Spooler", "BITS", "Themes")

foreach ($serviceName in $servicesToCheck) {
    $service = Get-Service -Name $serviceName
    Write-Output "$serviceName is $($service.Status)"
}
```

### While Loops - Repeating Until a Condition Changes

**Basic While Loop:**
```powershell
$counter = 1
while ($counter -le 5) {
    Write-Output "Loop iteration: $counter"
    $counter++
}
```

**Real-world While Loop Example:**
```powershell
# Wait for a file to appear
$filePath = "C:\Temp\processing_complete.txt"
Write-Output "Waiting for processing to complete..."

while (-not (Test-Path $filePath)) {
    Write-Output "Still waiting..."
    Start-Sleep -Seconds 5
}

Write-Output "Processing complete!"
```

**Do-While Loop (Runs at least once):**
```powershell
do {
    $input = Read-Host "Enter a number (0 to exit)"
    if ($input -ne "0") {
        Write-Output "You entered: $input"
    }
} while ($input -ne "0")

Write-Output "Goodbye!"
```

### Switch Statements - Multiple Choice Decisions

**Basic Switch:**
```powershell
$day = (Get-Date).DayOfWeek

switch ($day) {
    "Monday"    { Write-Output "Start of the work week" }
    "Tuesday"   { Write-Output "Tuesday blues" }
    "Wednesday" { Write-Output "Hump day!" }
    "Thursday"  { Write-Output "Almost there" }
    "Friday"    { Write-Output "TGIF!" }
    "Saturday"  { Write-Output "Weekend fun" }
    "Sunday"    { Write-Output "Rest day" }
    default     { Write-Output "Unknown day" }
}
```

**Switch with Multiple Conditions:**
```powershell
$fileExtension = ".txt"

switch ($fileExtension) {
    {$_ -in @(".txt", ".log", ".csv")} { 
        Write-Output "Text file detected"
        $editor = "notepad.exe"
    }
    {$_ -in @(".jpg", ".png", ".gif")} { 
        Write-Output "Image file detected"
        $editor = "mspaint.exe"
    }
    {$_ -in @(".mp3", ".wav", ".mp4")} { 
        Write-Output "Media file detected"
        $editor = "wmplayer.exe"
    }
    default { 
        Write-Output "Unknown file type"
        $editor = "notepad.exe"
    }
}
```

### Break and Continue - Controlling Loop Flow

**Break (Exit the loop entirely):**
```powershell
# Find first file larger than 1MB and stop looking
Get-ChildItem | ForEach-Object {
    if ($_.Length -gt 1MB) {
        Write-Output "Found large file: $($_.Name)"
        break
    }
    Write-Output "Checking: $($_.Name)"
}
```

**Continue (Skip to next iteration):**
```powershell
# Process only .txt files, skip others
Get-ChildItem | ForEach-Object {
    if ($_.Extension -ne ".txt") {
        continue  # Skip non-txt files
    }
    Write-Output "Processing text file: $($_.Name)"
    # Process the .txt file here
}
```

### Practical Control Flow Examples

**Example 1: System Health Check**
```powershell
# Check multiple system components
$checks = @{
    "Disk Space" = { (Get-WmiObject -Class Win32_LogicalDisk -Filter "DeviceID='C:'").FreeSpace / 1GB -gt 10 }
    "Memory Usage" = { (Get-WmiObject -Class Win32_OperatingSystem).FreePhysicalMemory / 1MB -gt 500 }
    "Print Spooler" = { (Get-Service -Name "Spooler").Status -eq "Running" }
}

foreach ($checkName in $checks.Keys) {
    Write-Output "Checking $checkName..."
    
    if (& $checks[$checkName]) {
        Write-Output "✓ $checkName: OK"
    } else {
        Write-Output "✗ $checkName: FAILED"
    }
}
```

**Example 2: File Cleanup Script**
```powershell
# Clean up old files from multiple directories
$foldersToClean = @("C:\Temp", "C:\Windows\Temp", "$env:USERPROFILE\Downloads")
$daysOld = 30

foreach ($folder in $foldersToClean) {
    if (Test-Path $folder) {
        Write-Output "Cleaning folder: $folder"
        
        $oldFiles = Get-ChildItem -Path $folder | Where-Object LastWriteTime -lt (Get-Date).AddDays(-$daysOld)
        
        if ($oldFiles.Count -gt 0) {
            foreach ($file in $oldFiles) {
                try {
                    Remove-Item -Path $file.FullName -Force
                    Write-Output "Deleted: $($file.Name)"
                } catch {
                    Write-Output "Could not delete: $($file.Name)"
                }
            }
        } else {
            Write-Output "No old files found in $folder"
        }
    } else {
        Write-Output "Folder not found: $folder"
    }
}
```

---

## 🔧 Functions and Script Blocks

### What are Functions?

Functions are like creating your own custom PowerShell commands. Instead of repeating the same code over and over, you package it into a reusable function. Think of functions like recipes - once you write the recipe, you can use it whenever you need it.

### Creating Basic Functions

**Simple Function:**
```powershell
function Say-Hello {
    Write-Output "Hello, World!"
}

# Use the function
Say-Hello
```

**Function with Parameters:**
```powershell
function Say-HelloTo {
    param(
        [string]$Name
    )
    Write-Output "Hello, $Name!"
}

# Use the function
Say-HelloTo -Name "Alice"
Say-HelloTo "Bob"  # Parameter name is optional if in correct order
```

**Function with Multiple Parameters:**
```powershell
function Calculate-Rectangle {
    param(
        [double]$Length,
        [double]$Width
    )
    
    $area = $Length * $Width
    $perimeter = 2 * ($Length + $Width)
    
    Write-Output "Rectangle: $Length x $Width"
    Write-Output "Area: $area"
    Write-Output "Perimeter: $perimeter"
}

# Use the function
Calculate-Rectangle -Length 10 -Width 5
```

### Advanced Function Parameters

**Default Parameter Values:**
```powershell
function Greet-User {
    param(
        [string]$Name = "Friend",
        [string]$TimeOfDay = "Day"
    )
    Write-Output "Good $TimeOfDay, $Name!"
}

# All these work:
Greet-User                           # Good Day, Friend!
Greet-User -Name "Alice"             # Good Day, Alice!
Greet-User -Name "Bob" -TimeOfDay "Morning"  # Good Morning, Bob!
```

**Mandatory Parameters:**
```powershell
function Backup-File {
    param(
        [Parameter(Mandatory=$true)]
        [string]$FilePath,
        
        [string]$BackupLocation = "C:\Backups"
    )
    
    if (Test-Path $FilePath) {
        $fileName = Split-Path $FilePath -Leaf
        $backupPath = Join-Path $BackupLocation $fileName
        Copy-Item -Path $FilePath -Destination $backupPath
        Write-Output "Backed up $fileName to $BackupLocation"
    } else {
        Write-Output "File not found: $FilePath"
    }
}

# This will prompt for FilePath if you don't provide it
Backup-File
```

**Parameter Validation:**
```powershell
function Set-Volume {
    param(
        [Parameter(Mandatory=$true)]
        [ValidateRange(0,100)]
        [int]$Level
    )
    
    Write-Output "Setting volume to $Level%"
    # Volume setting code would go here
}

# This works:
Set-Volume -Level 50

# This fails with an error:
Set-Volume -Level 150  # Error: 150 is not in range 0-100
```

### Returning Values from Functions

**Using Return Statement:**
```powershell
function Get-SquareRoot {
    param([double]$Number)
    
    if ($Number -lt 0) {
        return "Cannot calculate square root of negative number"
    }
    
    return [Math]::Sqrt($Number)
}

$result = Get-SquareRoot -Number 16
Write-Output "Square root: $result"
```

**Returning Objects:**
```powershell
function Get-SystemInfo {
    $info = @{
        ComputerName = $env:COMPUTERNAME
        UserName = $env:USERNAME
        OSVersion = (Get-WmiObject Win32_OperatingSystem).Caption
        TotalMemory = [Math]::Round((Get-WmiObject Win32_ComputerSystem).TotalPhysicalMemory / 1GB, 2)
        FreeSpace = [Math]::Round((Get-WmiObject -Class Win32_LogicalDisk -Filter "DeviceID='C:'").FreeSpace / 1GB, 2)
    }
    
    return New-Object PSObject -Property $info
}

$systemInfo = Get-SystemInfo
Write-Output "Computer: $($systemInfo.ComputerName)"
Write-Output "Free Space: $($systemInfo.FreeSpace) GB"
```

### Script Blocks - Code in Variables

Script blocks are chunks of code stored in variables. They're like functions but more flexible.

**Basic Script Block:**
```powershell
# Store code in a variable
$sayHello = {
    Write-Output "Hello from a script block!"
}

# Execute the script block
& $sayHello
```

**Script Blocks with Parameters:**
```powershell
$greetUser = {
    param($Name, $Age)
    Write-Output "$Name is $Age years old"
}

# Execute with parameters
& $greetUser "Alice" 30
```

**Using Script Blocks with ForEach-Object:**
```powershell
# Script block to process each file
$processFile = {
    Write-Output "Processing: $($_.Name)"
    Write-Output "Size: $($_.Length) bytes"
    Write-Output "---"
}

# Use the script block
Get-ChildItem *.txt | ForEach-Object $processFile
```

### Advanced Function Features

**Function with Pipeline Input:**
```powershell
function Convert-ToMB {
    param(
        [Parameter(ValueFromPipeline=$true)]
        [long]$Bytes
    )
    
    process {
        [Math]::Round($Bytes / 1MB, 2)
    }
}

# Use with pipeline
Get-ChildItem | Select-Object Name, Length | ForEach-Object {
    [PSCustomObject]@{
        Name = $_.Name
        SizeMB = $_.Length | Convert-ToMB
    }
}
```

**Function with Begin, Process, End blocks:**
```powershell
function Measure-FileStats {
    param(
        [Parameter(ValueFromPipeline=$true)]
        $File
    )
    
    begin {
        $totalSize = 0
        $fileCount = 0
        Write-Output "Starting file analysis..."
    }
    
    process {
        $totalSize += $File.Length
        $fileCount++
        Write-Output "Processed: $($File.Name)"
    }
    
    end {
        Write-Output "---"
        Write-Output "Total files: $fileCount"
        Write-Output "Total size: $([Math]::Round($totalSize / 1MB, 2)) MB"
    }
}

# Use the function
Get-ChildItem | Measure-FileStats
```

### Practical Function Examples

**Example 1: System Maintenance Function**
```powershell
function Invoke-SystemCleanup {
    param(
        [int]$DaysOld = 30,
        [string[]]$CleanupPaths = @("$env:TEMP", "C:\Windows\Temp"),
        [switch]$WhatIf
    )
    
    Write-Output "Starting system cleanup..."
    Write-Output "Cleaning files older than $DaysOld days"
    
    foreach ($path in $CleanupPaths) {
        if (Test-Path $path) {
            Write-Output "Cleaning: $path"
            
            $oldFiles = Get-ChildItem -Path $path -Recurse | 
                       Where-Object LastWriteTime -lt (Get-Date).AddDays(-$DaysOld)
            
            if ($WhatIf) {
                Write-Output "Would delete $($oldFiles.Count) files"
                $oldFiles | Select-Object Name, LastWriteTime | Format-Table
            } else {
                $oldFiles | Remove-Item -Force -Recurse
                Write-Output "Deleted $($oldFiles.Count) files"
            }
        }
    }
}

# Test what would be deleted
Invoke-SystemCleanup -WhatIf

# Actually perform cleanup
Invoke-SystemCleanup -DaysOld 7
```

**Example 2: File Organization Function**
```powershell
function Organize-Downloads {
    param(
        [string]$SourcePath = "$env:USERPROFILE\Downloads"
    )
    
    $fileTypes = @{
        "Images" = @(".jpg", ".jpeg", ".png", ".gif", ".bmp")
        "Documents" = @(".pdf", ".doc", ".docx", ".txt", ".rtf")
        "Archives" = @(".zip", ".rar", ".7z", ".tar")
        "Videos" = @(".mp4", ".avi", ".mkv", ".mov")
        "Audio" = @(".mp3", ".wav", ".flac", ".aac")
    }
    
    Write-Output "Organizing files in: $SourcePath"
    
    foreach ($category in $fileTypes.Keys) {
        $categoryPath = Join-Path $SourcePath $category
        
        if (-not (Test-Path $categoryPath)) {
            New-Item -Path $categoryPath -ItemType Directory
            Write-Output "Created folder: $category"
        }
        
        foreach ($extension in $fileTypes[$category]) {
            $filesToMove = Get-ChildItem -Path $SourcePath -Filter "*$extension"
            
            foreach ($file in $filesToMove) {
                $destination = Join-Path $categoryPath $file.Name
                Move-Item -Path $file.FullName -Destination $destination
                Write-Output "Moved $($file.Name) to $category"
            }
        }
    }
    
    Write-Output "Organization complete!"
}

# Use the function
Organize-Downloads
```

**Example 3: Network Connectivity Test Function**
```powershell
function Test-NetworkConnectivity {
    param(
        [string[]]$Servers = @("google.com", "microsoft.com", "github.com"),
        [int]$Count = 1
    )
    
    $results = @()
    
    foreach ($server in $Servers) {
        Write-Output "Testing connection to $server..."
        
        try {
            $ping = Test-Connection -ComputerName $server -Count $Count -ErrorAction Stop
            $avgTime = ($ping | Measure-Object ResponseTime -Average).Average
            
            $result = [PSCustomObject]@{
                Server = $server
                Status = "Success"
                AverageResponseTime = "$avgTime ms"
                PacketLoss = "0%"
            }
        } catch {
            $result = [PSCustomObject]@{
                Server = $server
                Status = "Failed"
                AverageResponseTime = "N/A"
                PacketLoss = "100%"
            }
        }
        
        $results += $result
    }
    
    return $results
}

# Use the function
$networkTest = Test-NetworkConnectivity
$networkTest | Format-Table -AutoSize
```

### Best Practices for Functions

1. **Use Approved Verbs**: PowerShell has approved verbs. Use `Get-Verb` to see them all.
```powershell
Get-Verb | Where-Object Verb -like "Get*"
```

2. **Follow Naming Convention**: Use Verb-Noun format like `Get-SystemInfo`, not `SystemInfo` or `GetSystemInfo`.

3. **Include Help**: Add comment-based help to your functions.
```powershell
function Get-DiskSpace {
    <#
    .SYNOPSIS
    Gets disk space information for specified drives.
    
    .DESCRIPTION
    This function retrieves disk space information including total size, 
    free space, and percentage used for specified drives.
    
    .PARAMETER DriveLetter
    The drive letter to check (default: C)
    
    .EXAMPLE
    Get-DiskSpace
    Gets disk space for C: drive
    
    .EXAMPLE
    Get-DiskSpace -DriveLetter "D"
    Gets disk space for D: drive
    #>
    
    param(
        [string]$DriveLetter = "C"
    )
    
    # Function code here
}
```

4. **Handle Errors**: Always include error handling in your functions.
```powershell
function Get-SafeFileContent {
    param([string]$FilePath)
    
    try {
        if (Test-Path $FilePath) {
            Get-Content $FilePath
        } else {
            Write-Warning "File not found: $FilePath"
        }
    } catch {
        Write-Error "Error reading file: $_"
    }
}
```

---

## 🔧 Error Handling

Error handling is crucial for creating robust PowerShell scripts. It helps your scripts handle unexpected situations gracefully and provides meaningful feedback to users.

### Understanding PowerShell Errors

PowerShell has two main types of errors:

#### 1. **Terminating Errors**
- Stop script execution immediately
- Cannot be ignored
- Examples: Syntax errors, critical system errors

#### 2. **Non-Terminating Errors**
- Allow script to continue running
- Can be handled or ignored
- Examples: File not found, permission denied

### Error Variables

PowerShell automatically populates several error-related variables:

```powershell
# $Error - Contains all errors from current session
$Error[0]  # Most recent error
$Error.Count  # Number of errors

# $? - True if last command succeeded, False if it failed
Get-Process "NonExistentProcess"
Write-Host "Last command succeeded: $?"

# $LASTEXITCODE - Exit code of last external program
ping google.com
Write-Host "Ping exit code: $LASTEXITCODE"
```

### Try-Catch-Finally Blocks

The primary method for handling errors in PowerShell:

```powershell
# Basic try-catch
try {
    # Code that might cause an error
    $result = 10 / 0
} catch {
    # Handle the error
    Write-Host "An error occurred: $($_.Exception.Message)" -ForegroundColor Red
}

# Try-catch with specific error types
try {
    Get-Content "C:\NonExistentFile.txt"
} catch [System.IO.FileNotFoundException] {
    Write-Host "File not found!" -ForegroundColor Yellow
} catch [System.UnauthorizedAccessException] {
    Write-Host "Access denied!" -ForegroundColor Red
} catch {
    Write-Host "Unknown error: $($_.Exception.Message)" -ForegroundColor Red
}

# Try-catch-finally
try {
    $file = [System.IO.File]::OpenRead("C:\temp\data.txt")
    # Process file
} catch {
    Write-Host "Error reading file: $($_.Exception.Message)"
} finally {
    # This always runs, even if there's an error
    if ($file) {
        $file.Close()
        Write-Host "File closed"
    }
}
```

### Error Action Preferences

Control how PowerShell responds to non-terminating errors:

```powershell
# Global error action preference
$ErrorActionPreference = "Stop"  # Treat non-terminating errors as terminating
$ErrorActionPreference = "Continue"  # Default - display error and continue
$ErrorActionPreference = "SilentlyContinue"  # Suppress errors and continue
$ErrorActionPreference = "Inquire"  # Prompt user for action

# Per-command error action
Get-Process "NonExistentProcess" -ErrorAction SilentlyContinue
Get-Service "FakeService" -ErrorAction Stop

# Using variables to capture errors
Get-Process "NonExistentProcess" -ErrorAction SilentlyContinue -ErrorVariable myError
if ($myError) {
    Write-Host "Captured error: $($myError.Exception.Message)"
}
```

### Practical Error Handling Examples

#### Example 1: File Operations with Error Handling
```powershell
function Copy-FileWithErrorHandling {
    param(
        [string]$SourcePath,
        [string]$DestinationPath
    )
    
    try {
        # Check if source file exists
        if (-not (Test-Path $SourcePath)) {
            throw "Source file '$SourcePath' does not exist"
        }
        
        # Check if destination directory exists, create if not
        $destDir = Split-Path $DestinationPath -Parent
        if (-not (Test-Path $destDir)) {
            New-Item -ItemType Directory -Path $destDir -Force | Out-Null
            Write-Host "Created directory: $destDir" -ForegroundColor Green
        }
        
        # Copy the file
        Copy-Item -Path $SourcePath -Destination $DestinationPath -Force
        Write-Host "Successfully copied '$SourcePath' to '$DestinationPath'" -ForegroundColor Green
        
    } catch [System.IO.IOException] {
        Write-Host "IO Error: $($_.Exception.Message)" -ForegroundColor Red
    } catch [System.UnauthorizedAccessException] {
        Write-Host "Access Denied: $($_.Exception.Message)" -ForegroundColor Red
    } catch {
        Write-Host "Unexpected error: $($_.Exception.Message)" -ForegroundColor Red
    }
}

# Usage
Copy-FileWithErrorHandling -SourcePath "C:\temp\source.txt" -DestinationPath "C:\backup\source.txt"
```

#### Example 2: Service Management with Error Handling
```powershell
function Restart-ServiceSafely {
    param([string]$ServiceName)
    
    try {
        $service = Get-Service -Name $ServiceName -ErrorAction Stop
        
        Write-Host "Current status: $($service.Status)"
        
        if ($service.Status -eq "Running") {
            Write-Host "Stopping service..." -ForegroundColor Yellow
            Stop-Service -Name $ServiceName -Force -ErrorAction Stop
            
            # Wait for service to stop
            $timeout = 30
            $counter = 0
            do {
                Start-Sleep -Seconds 1
                $service.Refresh()
                $counter++
            } while ($service.Status -ne "Stopped" -and $counter -lt $timeout)
            
            if ($service.Status -ne "Stopped") {
                throw "Service failed to stop within $timeout seconds"
            }
        }
        
        Write-Host "Starting service..." -ForegroundColor Yellow
        Start-Service -Name $ServiceName -ErrorAction Stop
        Write-Host "Service '$ServiceName' restarted successfully" -ForegroundColor Green
        
    } catch [Microsoft.PowerShell.Commands.ServiceCommandException] {
        Write-Host "Service error: $($_.Exception.Message)" -ForegroundColor Red
    } catch {
        Write-Host "Error restarting service: $($_.Exception.Message)" -ForegroundColor Red
    }
}

# Usage
Restart-ServiceSafely -ServiceName "Spooler"
```

### Custom Error Messages and Logging

```powershell
function Write-ErrorLog {
    param(
        [string]$Message,
        [string]$LogPath = "C:\temp\error.log"
    )
    
    $timestamp = Get-Date -Format "yyyy-MM-dd HH:mm:ss"
    $logEntry = "$timestamp - ERROR: $Message"
    
    try {
        Add-Content -Path $LogPath -Value $logEntry -ErrorAction Stop
        Write-Host $logEntry -ForegroundColor Red
    } catch {
        Write-Host "Failed to write to log file: $($_.Exception.Message)" -ForegroundColor Red
        Write-Host $logEntry -ForegroundColor Red
    }
}

# Usage in error handling
try {
    # Some risky operation
    Remove-Item "C:\ImportantFile.txt" -ErrorAction Stop
} catch {
    Write-ErrorLog "Failed to delete file: $($_.Exception.Message)"
}
```

---

## 🎯 Creating Your First Automation Script

Now let's put everything together to create a practical automation script that incorporates all the concepts we've learned.

### Project: System Health Check Automation

We'll create a comprehensive system health check script that:
- Checks disk space
- Monitors system services
- Checks event logs for errors
- Generates a report
- Includes proper error handling

```powershell
# SystemHealthCheck.ps1
param(
    [string]$ReportPath = "C:\temp\HealthReport.html",
    [int]$DiskSpaceThreshold = 80,
    [string[]]$CriticalServices = @("Spooler", "BITS", "Themes")
)

# Initialize variables
$script:ErrorCount = 0
$script:WarningCount = 0
$reportData = @()

# Function to add report entry
function Add-ReportEntry {
    param(
        [string]$Category,
        [string]$Item,
        [string]$Status,
        [string]$Details = "",
        [string]$Severity = "Info"
    )
    
    $script:reportData += [PSCustomObject]@{
        Category = $Category
        Item = $Item
        Status = $Status
        Details = $Details
        Severity = $Severity
        Timestamp = Get-Date -Format "yyyy-MM-dd HH:mm:ss"
    }
    
    # Update counters
    switch ($Severity) {
        "Error" { $script:ErrorCount++ }
        "Warning" { $script:WarningCount++ }
    }
}

# Function to check disk space
function Test-DiskSpace {
    Write-Host "Checking disk space..." -ForegroundColor Cyan
    
    try {
        $disks = Get-WmiObject -Class Win32_LogicalDisk -Filter "DriveType=3" -ErrorAction Stop
        
        foreach ($disk in $disks) {
            $usedSpacePercent = [math]::Round(((($disk.Size - $disk.FreeSpace) / $disk.Size) * 100), 2)
            $freeSpaceGB = [math]::Round(($disk.FreeSpace / 1GB), 2)
            
            if ($usedSpacePercent -gt $DiskSpaceThreshold) {
                Add-ReportEntry -Category "Disk Space" -Item "Drive $($disk.DeviceID)" `
                    -Status "Low Space" -Details "$usedSpacePercent% used, $freeSpaceGB GB free" `
                    -Severity "Warning"
            } else {
                Add-ReportEntry -Category "Disk Space" -Item "Drive $($disk.DeviceID)" `
                    -Status "OK" -Details "$usedSpacePercent% used, $freeSpaceGB GB free"
            }
        }
    } catch {
        Add-ReportEntry -Category "Disk Space" -Item "Disk Check" `
            -Status "Failed" -Details $_.Exception.Message -Severity "Error"
    }
}

# Function to check critical services
function Test-CriticalServices {
    Write-Host "Checking critical services..." -ForegroundColor Cyan
    
    foreach ($serviceName in $CriticalServices) {
        try {
            $service = Get-Service -Name $serviceName -ErrorAction Stop
            
            if ($service.Status -eq "Running") {
                Add-ReportEntry -Category "Services" -Item $serviceName `
                    -Status "Running" -Details "Service is operational"
            } else {
                Add-ReportEntry -Category "Services" -Item $serviceName `
                    -Status "Stopped" -Details "Service is not running" -Severity "Error"
            }
        } catch {
            Add-ReportEntry -Category "Services" -Item $serviceName `
                -Status "Not Found" -Details "Service does not exist" -Severity "Warning"
        }
    }
}

# Function to check system event logs
function Test-EventLogs {
    Write-Host "Checking event logs for recent errors..." -ForegroundColor Cyan
    
    try {
        $yesterday = (Get-Date).AddDays(-1)
        $systemErrors = Get-EventLog -LogName System -EntryType Error -After $yesterday -ErrorAction Stop | 
                       Select-Object -First 5
        
        if ($systemErrors) {
            foreach ($error in $systemErrors) {
                Add-ReportEntry -Category "Event Logs" -Item "System Error" `
                    -Status "Error Found" -Details "$($error.Source): $($error.Message)" `
                    -Severity "Warning"
            }
        } else {
            Add-ReportEntry -Category "Event Logs" -Item "System Errors" `
                -Status "Clean" -Details "No errors in last 24 hours"
        }
    } catch {
        Add-ReportEntry -Category "Event Logs" -Item "Log Check" `
            -Status "Failed" -Details $_.Exception.Message -Severity "Error"
    }
}

# Function to check system uptime
function Test-SystemUptime {
    Write-Host "Checking system uptime..." -ForegroundColor Cyan
    
    try {
        $bootTime = (Get-WmiObject -Class Win32_OperatingSystem -ErrorAction Stop).LastBootUpTime
        $bootDate = [System.Management.ManagementDateTimeConverter]::ToDateTime($bootTime)
        $uptime = (Get-Date) - $bootDate
        
        $uptimeString = "$($uptime.Days) days, $($uptime.Hours) hours, $($uptime.Minutes) minutes"
        
        if ($uptime.Days -gt 30) {
            Add-ReportEntry -Category "System" -Item "Uptime" `
                -Status "Long Uptime" -Details $uptimeString -Severity "Warning"
        } else {
            Add-ReportEntry -Category "System" -Item "Uptime" `
                -Status "Normal" -Details $uptimeString
        }
    } catch {
        Add-ReportEntry -Category "System" -Item "Uptime Check" `
            -Status "Failed" -Details $_.Exception.Message -Severity "Error"
    }
}

# Function to generate HTML report
function New-HtmlReport {
    $html = @"
<!DOCTYPE html>
<html>
<head>
    <title>System Health Report</title>
    <style>
        body { font-family: Arial, sans-serif; margin: 20px; }
        .header { background-color: #4CAF50; color: white; padding: 10px; border-radius: 5px; }
        .summary { background-color: #f0f0f0; padding: 10px; margin: 10px 0; border-radius: 5px; }
        .error { color: #d32f2f; font-weight: bold; }
        .warning { color: #f57c00; font-weight: bold; }
        .ok { color: #388e3c; font-weight: bold; }
        table { border-collapse: collapse; width: 100%; margin-top: 20px; }
        th, td { border: 1px solid #ddd; padding: 8px; text-align: left; }
        th { background-color: #f2f2f2; }
        .error-row { background-color: #ffebee; }
        .warning-row { background-color: #fff8e1; }
    </style>
</head>
<body>
    <div class="header">
        <h1>System Health Report</h1>
        <p>Generated on: $(Get-Date -Format "yyyy-MM-dd HH:mm:ss")</p>
        <p>Computer: $env:COMPUTERNAME</p>
    </div>
    
    <div class="summary">
        <h2>Summary</h2>
        <p><span class="error">Errors: $script:ErrorCount</span></p>
        <p><span class="warning">Warnings: $script:WarningCount</span></p>
        <p>Total Checks: $($script:reportData.Count)</p>
    </div>
    
    <h2>Detailed Results</h2>
    <table>
        <tr>
            <th>Category</th>
            <th>Item</th>
            <th>Status</th>
            <th>Details</th>
            <th>Timestamp</th>
        </tr>
"@

    foreach ($entry in $script:reportData) {
        $rowClass = switch ($entry.Severity) {
            "Error" { "error-row" }
            "Warning" { "warning-row" }
            default { "" }
        }
        
        $html += @"
        <tr class="$rowClass">
            <td>$($entry.Category)</td>
            <td>$($entry.Item)</td>
            <td class="$(($entry.Severity).ToLower())">$($entry.Status)</td>
            <td>$($entry.Details)</td>
            <td>$($entry.Timestamp)</td>
        </tr>
"@
    }
    
    $html += @"
    </table>
</body>
</html>
"@
    
    try {
        # Ensure directory exists
        $reportDir = Split-Path $ReportPath -Parent
        if (-not (Test-Path $reportDir)) {
            New-Item -ItemType Directory -Path $reportDir -Force | Out-Null
        }
        
        $html | Out-File -FilePath $ReportPath -Encoding UTF8 -ErrorAction Stop
        Write-Host "Report saved to: $ReportPath" -ForegroundColor Green
        
        # Try to open the report
        Start-Process $ReportPath -ErrorAction SilentlyContinue
        
    } catch {
        Write-Host "Failed to save report: $($_.Exception.Message)" -ForegroundColor Red
    }
}

# Main execution
try {
    Write-Host "Starting System Health Check..." -ForegroundColor Green
    Write-Host "=================================" -ForegroundColor Green
    
    # Run all checks
    Test-DiskSpace
    Test-CriticalServices
    Test-EventLogs
    Test-SystemUptime
    
    # Generate report
    Write-Host "`nGenerating report..." -ForegroundColor Cyan
    New-HtmlReport
    
    # Display summary
    Write-Host "`n=================================" -ForegroundColor Green
    Write-Host "Health Check Complete!" -ForegroundColor Green
    Write-Host "Errors: $script:ErrorCount" -ForegroundColor $(if ($script:ErrorCount -gt 0) { "Red" } else { "Green" })
    Write-Host "Warnings: $script:WarningCount" -ForegroundColor $(if ($script:WarningCount -gt 0) { "Yellow" } else { "Green" })
    Write-Host "Report: $ReportPath" -ForegroundColor Cyan
    
} catch {
    Write-Host "Critical error in main execution: $($_.Exception.Message)" -ForegroundColor Red
    exit 1
}
```

### How to Use the Script

1. **Save the script** as `SystemHealthCheck.ps1`
2. **Run with default settings:**
   ```powershell
   .\SystemHealthCheck.ps1
   ```

3. **Run with custom parameters:**
   ```powershell
   .\SystemHealthCheck.ps1 -ReportPath "D:\Reports\Health.html" -DiskSpaceThreshold 90 -CriticalServices @("Spooler", "Themes", "BITS", "Windows Update")
   ```

---

## 🚀 Advanced Topics

### PowerShell Remoting
Execute commands on remote computers:

```powershell
# Enable remoting (run as administrator)
Enable-PSRemoting -Force

# Execute single command on remote computer
Invoke-Command -ComputerName "Server01" -ScriptBlock { Get-Process }

# Create persistent session
$session = New-PSSession -ComputerName "Server01"
Invoke-Command -Session $session -ScriptBlock { Get-Service }
Remove-PSSession $session

# Copy files to remote computer
Copy-Item -Path "C:\local\file.txt" -Destination "C:\remote\file.txt" -ToSession $session
```

### Working with APIs and Web Requests

```powershell
# Simple web request
$response = Invoke-RestMethod -Uri "https://api.github.com/users/octocat"
Write-Host "User: $($response.name), Public Repos: $($response.public_repos)"

# POST request with JSON data
$body = @{
    name = "Test"
    description = "PowerShell API test"
} | ConvertTo-Json

$headers = @{
    "Authorization" = "token YOUR_TOKEN"
    "Content-Type" = "application/json"
}

$response = Invoke-RestMethod -Uri "https://api.github.com/user/repos" -Method POST -Body $body -Headers $headers
```

### Scheduled Tasks with PowerShell

```powershell
# Create a scheduled task
$action = New-ScheduledTaskAction -Execute "PowerShell.exe" -Argument "-File C:\Scripts\HealthCheck.ps1"
$trigger = New-ScheduledTaskTrigger -Daily -At "6:00AM"
$settings = New-ScheduledTaskSettingsSet -AllowStartIfOnBatteries -DontStopIfGoingOnBatteries

Register-ScheduledTask -TaskName "Daily Health Check" -Action $action -Trigger $trigger -Settings $settings -Description "Daily system health check"
```

### Working with Modules

```powershell
# List available modules
Get-Module -ListAvailable

# Import a module
Import-Module ActiveDirectory

# Install module from PowerShell Gallery
Install-Module -Name PSReadLine -Force

# Create your own module
# Save as MyModule.psm1
function Get-SystemInfo {
    [CmdletBinding()]
    param()
    
    $info = [PSCustomObject]@{
        ComputerName = $env:COMPUTERNAME
        OS = (Get-WmiObject Win32_OperatingSystem).Caption
        TotalMemory = [math]::Round((Get-WmiObject Win32_ComputerSystem).TotalPhysicalMemory / 1GB, 2)
        Uptime = (Get-Date) - (Get-CimInstance Win32_OperatingSystem).LastBootUpTime
    }
    
    return $info
}

Export-ModuleMember -Function Get-SystemInfo
```

### PowerShell Classes (PowerShell 5.0+)

```powershell
class Server {
    [string]$Name
    [string]$Environment
    [int]$Port
    [bool]$IsOnline
    
    # Constructor
    Server([string]$Name, [string]$Environment, [int]$Port) {
        $this.Name = $Name
        $this.Environment = $Environment
        $this.Port = $Port
        $this.CheckStatus()
    }
    
    # Method to check if server is online
    [void]CheckStatus() {
        $this.IsOnline = Test-NetConnection -ComputerName $this.Name -Port $this.Port -InformationLevel Quiet
    }
    
    # Method to display server info
    [string]ToString() {
        return "Server: $($this.Name), Environment: $($this.Environment), Status: $(if ($this.IsOnline) { 'Online' } else { 'Offline' })"
    }
}

# Usage
$server = [Server]::new("web01.contoso.com", "Production", 80)
Write-Host $server.ToString()
```

---

## 💪 Practice Exercises

### Exercise 1: File Organizer Script
Create a script that organizes files in a folder by their extensions:

```powershell
# Starter code - complete this function
function Organize-FilesByExtension {
    param(
        [string]$SourcePath,
        [string]$DestinationPath
    )
    
    # Your code here:
    # 1. Check if source path exists
    # 2. Create destination folders for each file extension
    # 3. Move files to appropriate folders
    # 4. Include error handling
    # 5. Provide progress feedback
}
```

### Exercise 2: Service Monitor
Create a script that monitors specified services and restarts them if they're stopped:

```powershell
# Starter code
function Monitor-Services {
    param(
        [string[]]$ServiceNames,
        [int]$CheckIntervalSeconds = 60
    )
    
    # Your code here:
    # 1. Continuously monitor services
    # 2. Restart stopped services
    # 3. Log all actions
    # 4. Handle errors gracefully
    # 5. Allow graceful exit with Ctrl+C
}
```

### Exercise 3: Log File Analyzer
Create a script that analyzes IIS log files:

```powershell
function Analyze-IISLogs {
    param(
        [string]$LogPath,
        [datetime]$StartDate,
        [datetime]$EndDate
    )
    
    # Your code here:
    # 1. Parse IIS log files
    # 2. Filter by date range
    # 3. Generate statistics (top IPs, response codes, etc.)
    # 4. Create a summary report
    # 5. Handle large files efficiently
}
```

### Solutions

#### Exercise 1 Solution:
```powershell
function Organize-FilesByExtension {
    param(
        [Parameter(Mandatory)]
        [string]$SourcePath,
        [Parameter(Mandatory)]
        [string]$DestinationPath
    )
    
    try {
        # Validate source path
        if (-not (Test-Path $SourcePath)) {
            throw "Source path '$SourcePath' does not exist"
        }
        
        # Create destination directory if it doesn't exist
        if (-not (Test-Path $DestinationPath)) {
            New-Item -ItemType Directory -Path $DestinationPath -Force | Out-Null
            Write-Host "Created destination directory: $DestinationPath" -ForegroundColor Green
        }
        
        # Get all files from source
        $files = Get-ChildItem -Path $SourcePath -File
        $totalFiles = $files.Count
        $processedFiles = 0
        
        Write-Host "Found $totalFiles files to organize" -ForegroundColor Cyan
        
        foreach ($file in $files) {
            try {
                $extension = if ($file.Extension) { $file.Extension.TrimStart('.') } else { 'NoExtension' }
                $destFolder = Join-Path $DestinationPath $extension
                
                # Create extension folder if it doesn't exist
                if (-not (Test-Path $destFolder)) {
                    New-Item -ItemType Directory -Path $destFolder -Force | Out-Null
                    Write-Host "Created folder: $extension" -ForegroundColor Yellow
                }
                
                # Move file
                $destPath = Join-Path $destFolder $file.Name
                Move-Item -Path $file.FullName -Destination $destPath -Force
                
                $processedFiles++
                $percentComplete = [math]::Round(($processedFiles / $totalFiles) * 100, 2)
                Write-Progress -Activity "Organizing files" -Status "Processing $($file.Name)" -PercentComplete $percentComplete
                
            } catch {
                Write-Host "Error processing file '$($file.Name)': $($_.Exception.Message)" -ForegroundColor Red
            }
        }
        
        Write-Progress -Activity "Organizing files" -Completed
        Write-Host "Successfully organized $processedFiles out of $totalFiles files" -ForegroundColor Green
        
    } catch {
        Write-Host "Error: $($_.Exception.Message)" -ForegroundColor Red
    }
}

# Usage
Organize-FilesByExtension -SourcePath "C:\temp\messy" -DestinationPath "C:\temp\organized"
```

---

## 📚 Resources

### Official Documentation
- [Microsoft PowerShell Documentation](https://docs.microsoft.com/en-us/powershell/)
- [PowerShell Gallery](https://www.powershellgallery.com/)
- [PowerShell GitHub Repository](https://github.com/PowerShell/PowerShell)

### Learning Resources
- **Books:**
  - "PowerShell in a Month of Lunches" by Don Jones and Jeffrey Hicks
  - "Windows PowerShell Cookbook" by Lee Holmes
  - "PowerShell Deep Dives" by Jeffrey Hicks, Richard Siddaway, and Oisin Grehan

- **Online Communities:**
  - [PowerShell.org](https://powershell.org/)
  - [Reddit PowerShell Community](https://www.reddit.com/r/PowerShell/)
  - [PowerShell Discord Server](https://discord.gg/powershell)

### Tools and Extensions
- **PowerShell ISE** - Integrated Scripting Environment (Windows)
- **Visual Studio Code** with PowerShell Extension
- **Windows Terminal** - Modern terminal application
- **PowerShell Core** - Cross-platform PowerShell

### Best Practices Checklist
- ✅ Always include parameter validation
- ✅ Use proper error handling
- ✅ Write descriptive comments
- ✅ Follow PowerShell naming conventions
- ✅ Test scripts thoroughly before deployment
- ✅ Use Write-Verbose for detailed logging
- ✅ Implement progress indicators for long-running tasks
- ✅ Handle edge cases and unexpected inputs
- ✅ Use approved verbs for function names
- ✅ Include help documentation in your functions

### Next Steps
1. **Practice regularly** - The key to mastering PowerShell is consistent practice
2. **Join the community** - Participate in forums and discussions
3. **Contribute to open source** - Help improve PowerShell modules
4. **Explore advanced topics** - DSC, PowerShell classes, custom cmdlets
5. **Automate your daily tasks** - Start small and gradually build more complex scripts

---

## 🎓 Conclusion

Congratulations! You've completed the Windows PowerShell Automation Basics Course. You now have the foundation to:

- Write robust PowerShell scripts with proper error handling
- Automate common system administration tasks
- Create reusable functions and modules
- Debug and troubleshoot PowerShell code
- Follow PowerShell best practices

Remember, PowerShell is a powerful tool that becomes more valuable with practice. Start by automating simple tasks in your daily work, and gradually tackle more complex automation challenges.

Keep scripting, keep learning, and most importantly, have fun automating your Windows environment! 🚀
