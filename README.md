# Azure Mini Active Directory Module

This Terraform module deploys a **lightweight Active Directory Domain Controller** on **Azure** using **Samba 4** on Ubuntu.  
It’s designed for **labs, demos, and development environments** where you need AD functionality without the overhead and cost of **Azure Active Directory Domain Services (AAD DS)**.

⚠️ **Note**: This module is *not* production-ready. It’s intended for testing, prototyping, and training purposes.

---

## Features

- Provisions an **Ubuntu VM** on Azure running Samba 4 as a Domain Controller.  
- Configures **Active Directory and DNS**.  
- Stores secrets in **Azure Key Vault** (administrator password, etc.).  
- Creates **Network Security Groups (NSGs)** with AD/DC firewall rules.  
- Uses **cloud-init bootstrap scripts** (`mini-ad.sh.template`) to automate provisioning.  
- Supports **seed users and groups** via `users.json.template`.  
- Configures **private DNS zone** for domain resolution.  

---

## Module Structure

- `main.tf` — Resource group, networking, and orchestration  
- `dc.tf` — Domain Controller VM and configuration  
- `security.tf` — NSGs for AD/DC traffic  
- `variables.tf` — Input variable definitions  
- `outputs.tf` — Outputs (e.g., public IP, domain name)  
- `scripts/mini-ad.sh.template` — Bootstrap script for Samba DC  
- `scripts/users.json.template` — Example users and groups  
- `scripts/maxUidNumber.py` — Helper script for UID/GID assignment  
- `scripts/maxids.service` — Systemd service for ID allocation  

---

## Usage Example

Here’s how you can use the module in your Terraform configuration:

```hcl
module "mini_ad" {
  source            = "github.com/mamonaco1973/module-azure-mini-ad"
  location          = var.resource_group_location
  netbios           = var.netbios
  vnet_id           = azurerm_virtual_network.ad_vnet.id
  realm             = var.realm
  users_json        = local.users_json
  user_base_dn      = var.user_base_dn
  ad_admin_password = random_password.admin_password.result
  dns_zone          = var.dns_zone
  subnet_id         = azurerm_subnet.mini_ad_subnet.id
  admin_password    = random_password.sysadmin_password.result
}
```
## Outputs

- **dc_public_ip** — Public IP of the Domain Controller (if enabled)  
- **dc_private_ip** — Internal IP of the Domain Controller  
- **domain_name** — Configured DNS domain  
- **netbios_name** — NetBIOS short name  

## Limitations

- Not HA: single VM deployment only  
- No production-grade AD replication  
- No automated backup or restore of Samba database  
- Best for **demo, test, and learning environments**  
