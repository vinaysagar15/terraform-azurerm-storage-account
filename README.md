# Storage account

Module to create an Azure storage account with set of containers (and access level). Storage account will enable encryption of file and blob and require https, these options are not possible to change. It is recommended to set the network policies to restrict access to account.

## Usage

To just create a storage account with some containers have a look at the simple example.

```terraform
module "simple" {
    source = "avinor/storage-account/azurerm"
    version = "1.0.0"

    name = "simple"
    resource_group_name = "simple-rg"
    location = "westeurope"

    containers = [
        {
            name = "container"
            access_type = "private"
        },
    ]
}
```

## Events

It is also possible to connect Event Grid subscriptions to storage account and send event to an Event Hub. This requires the `events` variable to be set. Since variable object doesn´t support optional properties it uses `any` instead. The input object looks like this:

```terraform
events = [
    {
        name = required
        event_delivery_schema = optional(string)
        topic_name = optional(string)
        labels = optional(list(string))
        eventhub_id = required

        filters = optional({
            subject_begins_with = optional(string)
            subject_ends_with = optional(string)
        })
    }
]
```

Example usage:

```terraform
module "simple" {
    source = "avinor/storage-account/azurerm"
    version = "1.0.0"

    name = "simple"
    resource_group_name = "simple-rg"
    location = "westeurope"

    containers = [
        {
            name = "container"
            access_type = "private"
        },
    ]

    # Send all events to event hub
    events = [
        {
            name = "send_to_eventhub"
            eventhub_id = "..id.."
        }
    ]
}
```