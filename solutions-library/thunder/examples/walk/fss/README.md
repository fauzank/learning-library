# File System Storage Examples

Below there is a simple example of a FSS

## Description

The terraform module uses the following variables:


* File System Storage parameters
  * ad - ad of the fss
  * name - The fss name
  * compartment_name - The compartment name in which the fss will be created
  
* Mount Target parameters
  * ad - ad of the mount target
  * name - The name of the mount target
  * compartment_name - The compartment name in which the mount target will be created
  * subnet_name - The subnet name in which the mount target will be created

* Export parameters
  * export_set_name - The mount target name which needs to be exported
  * filesystem_name - The filesystem name for the export options
  * path - The path used for the export (e.g /abcd)
  * export options
    * source - Clients these options should apply to. Must be a either single IPv4 address or single IPv4 CIDR block.
    * access - Type of access to grant clients using the file system through this export (e.g READ_ONLY)
    * identity -Used when clients accessing the file system through this export have their UID and GID remapped to 'anonymousUid' and 'anonymousGid'. If ALL, all users and groups are remapped; if ROOT, only the root user and group (UID/GID 0) are remapped; if NONE, no remapping is done. 
    * use_port - Whether or not to use a port for the export options


## Example
In the provided example, the following resources are created: 
* One File System Storage
* One Mount Target with one Export Set 
* One Export options for the Export Set

The compartment_ids will be external to the vcn params in order to offer multiple ways of linking them from a terraform perspective.
In the example from below, there is a list of compartments containing 1 element. By setting in fss_params `compartment_name` to sandbox, The fss will be created in the first element of the list: `ocid1.compartment.oc1..aaaaaaaaiu3vfcpbjwwgpil3xakqts4jhtjq42kktmisriiszdvvouwsirgq`. The same thing happens for the subnet_ids. All lists can be extended in order to satisfy your needs.


```
compartment_ids = {
  sandbox = "ocid1.compartment.oc1..aaaaaaaaiu3vfcpbjwwgpil3xakqts4jhtjq42kktmisriiszdvvouwsirgq"
}

subnet_ids = {
  hur1pub = "ocid1.subnet.oc1.eu-frankfurt-1.aaaaaaaaka6k2tp7dtasvyp7rly4ypevudwtahtoozsumjnazjl52xpbsrpq"
}

fss_params = {
  thunder1 = {
    ad               = 1
    compartment_name = "sandbox"
    name             = "thunder1"
  }
}

mt_params = {
  mt1 = {
    ad               = 1
    compartment_name = "sandbox"
    name             = "mt1"
    subnet_name      = "hur1pub"
  }

  mt2 = {
    ad               = 1
    compartment_name = "sandbox"
    name             = "mt2"
    subnet_name      = "hur1pub"
  }
}

export_params = {
  mt1 = {
    export_set_name = "mt1"
    filesystem_name = "thunder1"
    path            = "/media"

    export_options = [
      {
        source   = "10.0.1.0/24"
        access   = "READ_WRITE"
        identity = "ROOT"
        use_port = true
      },
    ]
  }
}

```

By modifying terraform.tfvars, you can increase or decrease any number of the specified resources.
After the filesystem is created, it can be mounted as specified in: https://docs.cloud.oracle.com/iaas/Content/File/Tasks/mountingfilesystems.htm on an existing instance.

Simple example of a mount target having `10.0.1.10` private ip and `/media` as path

```
sudo yum install nfs-utils
sudo mkdir -p /mnt/media
sudo mount 10.0.1.10:/media /mnt/media
```

## Useful links
https://docs.cloud.oracle.com/iaas/Content/File/Concepts/filestorageoverview.htm
https://www.terraform.io/docs/providers/oci/r/file_storage_file_system.html