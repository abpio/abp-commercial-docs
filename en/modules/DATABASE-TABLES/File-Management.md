## FmDirectoryDescriptors

FmDirectoryDescriptors Table is used to store directory descriptors.

### Description

This table stores information about the directory descriptors. Each record in the table represents a directory descriptor and allows to manage and track the directory descriptors effectively. This table is important for managing and tracking the directory descriptors of the application.

### Module

[`Volo.FileManagement`](../file-management.md)

### Uses

| Table | Column | Description |
| --- | --- | --- |
| `FmDirectories` | Id | To match the directory descriptor with the parent directory. |

### Used By

| Table | Column | Description |
| --- | --- | --- |
| [`FmFileDescriptors`](#fmfiledescriptors) | DirectoryDescriptorId | To match the file descriptor with the directory descriptor. |

---

## FmFileDescriptors

FmFileDescriptors Table is used to store file descriptors.

### Description

This table stores information about the file descriptors. Each record in the table represents a file descriptor and allows to manage and track the file descriptors effectively. This table is important for managing and tracking the file descriptors of the application.

### Module

[`Volo.FileManagement`](../file-management.md)

### Uses

| Table | Column | Description |
| --- | --- | --- |
| [`FmDirectoryDescriptors`](#fmdirectorydescriptors) | Id | To match the file descriptor with the parent directory descriptor. |