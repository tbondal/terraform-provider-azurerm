---
layout: "azurerm"
page_title: "Azure Resource Manager: azurerm_mssql_elasticpool"
sidebar_current: "docs-azurerm-resource-database-mssql-elasticpool"
description: |-
  Manages a SQL Elastic Pool.
---

# azurerm_mssql_elasticpool

Allows you to manage an Azure SQL Elastic Pool via the `2017-10-01-preview` API which allows for `vCore` and `DTU` based configurations.

## Example Usage

```hcl
resource "azurerm_resource_group" "test" {
  name     = "my-resource-group"
  location = "westeurope"
}

resource "azurerm_sql_server" "test" {
  name                         = "my-sql-server"
  resource_group_name          = "${azurerm_resource_group.test.name}"
  location                     = "${azurerm_resource_group.test.location}"
  version                      = "12.0"
  administrator_login          = "4dm1n157r470r"
  administrator_login_password = "4-v3ry-53cr37-p455w0rd"
}

resource "azurerm_mssql_elasticpool" "test" {
  name                = "test-epool"
  resource_group_name = "${azurerm_resource_group.test.name}"
  location            = "${azurerm_resource_group.test.location}"
  server_name         = "${azurerm_sql_server.test.name}"
  max_size_gb         = 756

  sku {
    name     = "GP_Gen5"
    tier     = "GeneralPurpose"
    family   = "Gen5"
    capacity = 4
  }

  per_database_settings {
    min_capacity = 0.25
    max_capacity = 4
  }
}
```

## Argument Reference

The following arguments are supported:

* `name` - (Required) The name of the elastic pool. This needs to be globally unique. Changing this forces a new resource to be created.

* `resource_group_name` - (Required) The name of the resource group in which to create the elastic pool. This must be the same as the resource group of the underlying SQL server.

* `location` - (Required) Specifies the supported Azure location where the resource exists. Changing this forces a new resource to be created.

* `server_name` - (Required) The name of the SQL Server on which to create the elastic pool. Changing this forces a new resource to be created.

* `sku` - (Required) A `sku` block as defined below.

* `per_database_settings` - (Required) A `per_database_settings` block as defined below.

* `max_size_gb` - (Optional) The max data size of the elastic pool in gigabytes. Conflicts with `max_size_bytes`. 

* `max_size_bytes` - (Optional) The max data size of the elastic pool in bytes. Conflicts with `max_size_gb`.

* `tags` - (Optional) A mapping of tags to assign to the resource.

---

`sku` supports the following:

* `name` - (Required) Specifies the SKU Name for this Elasticpool. The name of the SKU, will be either `vCore` based `tier` + `family` pattern (e.g. GP_Gen4, BC_Gen5) or the `DTU` based `BasicPool`, `StandardPool`, or `PremiumPool` pattern. 

* `capacity` - (Required) The scale up/out capacity, representing server's compute units. For more information see the documentation for your Elasticpool configuration: [vCore-based](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-vcore-resource-limits-elastic-pools) or [DTU-based](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-dtu-resource-limits-elastic-pools).

* `tier` - (Required) The tier of the particular SKU. Possible values are `GeneralPurpose`, `BusinessCritical`, `Basic`, `Standard`, or `Premium`. For more information see the documentation for your Elasticpool configuration: [vCore-based](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-vcore-resource-limits-elastic-pools) or [DTU-based](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-dtu-resource-limits-elastic-pools).

* `family` - (Optional) The `family` of hardware `Gen4` or `Gen5`.

---

`per_database_settings` supports the following:

* `min_capacity` - (Required) The minimum capacity all databases are guaranteed.

* `max_capacity` - (Required) The maximum capacity any one database can consume.

---

## Attributes Reference

The following attributes are exported:

* `id` - The MsSQL Elastic Pool ID.

* `zone_redundant` - Whether or not this elastic pool is zone redundant.

## Import

SQL Elastic Pool can be imported using the `resource id`, e.g.

```shell
terraform import azurerm_mssql_elasticpool.test /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.Sql/servers/myserver/elasticPools/myelasticpoolname
```
