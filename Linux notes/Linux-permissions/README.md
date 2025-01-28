# File Permissions in Linux

## Does being the owner of a file mean you have full permissions on that file? Explain.
No, being the owner of a file does not automatically grant you full permissions. Ownership means you have control over the file and can change its permissions (e.g., using `chmod` or `chown`), but your actual access rights depend on the permissions set for the **User** (owner). If the owner's permissions are limited (e.g., read-only), the owner will only be able to perform actions allowed by those permissions.

---

## If you give permissions to the User entity, what does this mean?
Permissions given to the **User** entity apply to the owner of the file. These determine what actions the file's owner can perform, such as:
- **Read (r)**: View the file's contents.
- **Write (w)**: Modify the file's contents.
- **Execute (x)**: Execute the file if it's a script or program.

---

## If you give permissions to the Group entity, what does this mean?
Permissions for the **Group** entity apply to all users who are members of the file's group. These users can perform actions allowed by the group's assigned permissions:
- **Read (r)**: Members can view the file's contents.
- **Write (w)**: Members can modify the file's contents.
- **Execute (x)**: Members can execute the file if it's a script or program.

---

## If you give permissions to the Other entity, what does this mean?
Permissions for the **Other** entity apply to all users on the system who are neither the owner nor members of the group associated with the file. These permissions define the access level for everyone else:
- **Read (r)**: All other users can view the file's contents.
- **Write (w)**: All other users can modify the file's contents.
- **Execute (x)**: All other users can execute the file if it's a script or program.

---

## Example Scenario: Permissions and Ownership
### Permissions:
- **User (owner)**: Read-only (`r--`)
- **Group**: Read and write (`rw-`)
- **Other**: Read, write, and execute (`rwx`)

### Question:
You are logged in as the owner of the file. What permissions will you have on this file? Explain.

### Answer:
As the owner, your permissions are determined by the **User** entity, which is set to read-only (`r--`). This means you can only:
- View the file's contents.
You cannot modify (`write`) or execute (`execute`) the file, even though the Group and Other entities have broader permissions. The owner is strictly limited by the **User** entity's permissions.

---

## Understanding Permissions from `ls -l`
### Example `ls -l` Output:
