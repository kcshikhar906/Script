# Bulk Active Directory User Creation Script

## Description

This PowerShell script automates the creation of Active Directory user accounts by reading user details from a specified CSV file. It sets various user attributes, including organizational unit placement, contact information, job details, and an initial password. The script includes basic checks to prevent attempting to create users that already exist and provides console feedback on its progress.

## Features

* Reads user data from a CSV file with a specified path.
* Uses a semicolon (`;`) as the delimiter for the CSV file.
* Sets a wide range of Active Directory user attributes (Name, UPN, Address, Contact Info, Job Title, Department, etc.).
* Constructs the User Principal Name (UPN) based on username and a configured domain suffix.
* Sets an initial password from the CSV file and flags the user to change it at next logon.
* Places the user account in the Organizational Unit (OU) specified in the CSV file.
* Checks if a user with the same `SamAccountName` already exists before attempting creation.
* Provides color-coded console output for status messages (Success, Warning for existing users, Failure).
* Includes basic error handling using a `try-catch` block for each user.

## Prerequisites

1.  **Operating System:** Windows machine capable of running PowerShell.
2.  **Active Directory Module:** The `ActiveDirectory` PowerShell module must be installed. This is typically installed via Remote Server Administration Tools (RSAT) for AD DS, or available on Domain Controllers. The script includes `Import-Module ActiveDirectory`.
3.  **Permissions:** The account running the script must have permissions to create user accounts in the target Organizational Units specified within the CSV file. Domain Admin rights or appropriately delegated permissions are required.
4.  **Execution Policy:** PowerShell execution policy must allow script execution (e.g., `RemoteSigned` or `Unrestricted`). You can check with `Get-ExecutionPolicy` and set with `Set-ExecutionPolicy` (requires Administrator rights).

## Configuration

Before running the script, you **must** edit the `.ps1` file to set the following variables:

1.  **`$ADUsers = Import-Csv "COPY YOUR .CSV FILE PATH HERE" -Delimiter ";"`**
    * Replace `"COPY YOUR .CSV FILE PATH HERE"` with the full path to your input CSV file (e.g., `"C:\Scripts\NewUsersFinal.csv"`).

2.  **`$UPN = "You Domain Name"`**
    * Replace `"You Domain Name"` with your actual Active Directory domain name suffix (e.g., `"yourdomain.local"` or `"ad.yourcompany.com"`). This is used to construct the UserPrincipalName (`username@YourDomainName`).

## CSV File Format

The script expects a CSV file with the following specifications:

* **Delimiter:** Must be a semicolon (`;`).
* **Headers:** The first row must contain the following headers (case is usually ignored by `Import-Csv` but matching case is recommended):
    `FirstName;Initials;Lastname;Username;Email;StreetAddress;City;ZipCode;State;Country;Department;Password;Telephone;JobTitle;Company;OU`
* **`OU` Column:** This column is critical and *must* contain the full **Distinguished Name (DN)** of the Organizational Unit where the user account should be created (e.g., `OU=Users,OU=London,DC=yourdomain,DC=local`). Ensure these OUs exist in your Active Directory.
* **`Password` Column:** This column contains the initial password for the user. See the security warning below.

**Example CSV Row:**

```csv
FirstName;Initials;Lastname;Username;Email;StreetAddress;City;ZipCode;State;Country;Department;Password;Telephone;JobTitle;Company;OU
Max;MF;Fraser;Max.Fraser;Max.Fraser@exoip.com;21 Baker St;London;NW1 6XE;;GB;IT;Password@123;+44123456780;Engineer;EXOIP;OU=Staff,DC=yourdomain,DC=local