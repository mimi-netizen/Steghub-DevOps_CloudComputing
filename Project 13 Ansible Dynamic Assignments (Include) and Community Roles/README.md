# ANSIBLE DYNAMIC ASSIGNMENTS (INCLUDE) AND COMMUNITY ROLES

IMPORTANT NOTICE: Ansible is an actively developing software project, so you are encouraged to visit Ansible Documentation for the
latest updates on modules and their usage.
( https://docs.ansible.com/ )

Last 2 projects have already equipped you with some knowledge and skills on Ansible, so you can perform configurations using
playbooks, roles and imports. Now you will continue configuring your UAT servers learning and practicing new Ansible concepts
and modules.

In this project we will introduce dynamic assignments by using include module.
( https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_reuse.html#includes-dynamic-re-use )

Now you may be wondering, what is the difference between static and dynamic assignments?

Well, from Project 12, you can already tell that static assignments use import Ansible module. The module that enables dynamic
assignments is include.

Hence,

```
import = Static
include = Dynamic
```

When the import module is used, all statements are pre-processed at the time playbooks are parsed. Meaning, when you execute site.yml
playbook, Ansible will process all the playbooks referenced during the time it is parsing the statements. This also means that,
during actual execution, if any statement changes, such statements will not be considered. Hence, it is static.

On the other hand, when include module is used, all statements are processed only during execution of the playbook. Meaning, after the
statements are parsed, any changes to the statements encountered during execution will be used.

Take note that in most cases it is recommended to use static assignments for playbooks, because it is more reliable. With dynamic
ones, it is hard to debug playbook problems due to its dynamic nature. However, you can use dynamic assignments for environment
specific variables as we will be introducing in this project.
