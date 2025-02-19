#### Required config for Disk Resizing in Linux

Disk resizing is handled via two scripts:  

- **`disk_resize_lv.sh`** – Resizes logical volumes (`lv-root` and `lv-varlog`).  
- **`disk_resize_path.sh`** – Creates and mounts new partitions.  

Our base Packer image (`ubuntu-2204-gold`) includes:  

- **`lv-root` (`/`)** – 35GB  
- **`lv-varlog` (`/var/log`)** – 15GB  

To increase logical volume size or create & mount new partitions from free disk space, add the following variables in `terragrunt.hcl`:  

# Disk Resize VARIABLES  

- **`disk_resize`** = `"true"`  
- **`lv_check`** = `"true"`  
- **`path_check`** = `"true"`  
- **`disk_resize_lv`** = `[50,20]`  
- **`disk_resize_path`** =  
  - `{`  
    - **`disk`** = `"/dev/sda"` → *(Target disk, e.g., `/dev/sda`)*  
    - **`fs_path`** = `"/opt/data"` → *(Mount path where you want to increase the size)*  
    - **`partition_size`** = `"20G"` → *(Partition size as per requirement)*  
  - `}`  
  - `{`  
    - **`disk`** = `"/dev/sdb"`  
    - **`fs_path`** = `"/opt/data-1"`  
    - **`partition_size`** = `"40G"`  
  - `}`  

# Variable Explanation  

- **`disk_resize`** – Enables disk resizing (`true` / `false`).  
- **`lv_check`** – Runs `disk_resize_lv.sh` if `true`.  
- **`path_check`** – Runs `disk_resize_path.sh` if `true`.  
- **`disk_resize_lv`** – List of percentages to increase free space (`[lv-root, lv-varlog]`).  
  - Example: `[50,20]` → `lv-root` grows by 50% of available space, and `lv-varlog` grows by 20%.  
- **`disk_resize_path`** – List of partitions to create:  
  - **`disk`** – Target disk (`/dev/sda`, `/dev/sdb`, etc.).  
  - **`fs_path`** – Mount point (`/opt/data`, etc.).  
  - **`partition_size`** – New partition size (`2G`, `4G`, etc.).  

