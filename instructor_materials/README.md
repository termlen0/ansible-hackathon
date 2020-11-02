## Overview
Provide customers with "hackathon" inspired enablement content. The SA
driving this version of the hackathon should first ensure that the
necessary amount of workshop pods have been provisioned ahead of time
through RHPDS (RHEL workshop).

The premise of the hackathon involves 3 teams who need to solve
different set of problems and finally come together to stitch them all
together through a workflow.

If there are 20 attendees, and they are divided into 5 teams:

- Provision 25 pods. Each team will have one pod designated as their
  "Prod" Tower.
- Each team self-divides into the 3 personas (eg: 1 SecOps, 2 Ops, 2 WebOps)
- Enusre that each team has at least 1 mentor (or floating SA).
- Each team member is provided with access to a "dev" pod to build and
  test their playbooks/job templates
- Each persona will use their github fork for building out their
  inventories, projects. templates etc on their team's "Prod" Tower

Please see [answer
key](https://drive.google.com/drive/folders/1_PDeG13Q0CBW-cFajpKABK4X_lpn2wN6?usp=sharing)
for sample solutions.

## Populating the attendee Towers

### Step 1
Generate the instructor inventory

``` bash
$ ansible-playbook construct_inventory.yml
Enter the Workshop domain name: bd14.open.redhat.com
Enter the number of attendees: 10
Enter the workshop password for tower admin: jYXme7awQyldTj

```
This should generate the following `instructor-inventory` file

``` bash
$ more instructor-inventory
student1  tower_host=student1.bd14.open.redhat.com
student2  tower_host=student2.bd14.open.redhat.com
student3  tower_host=student3.bd14.open.redhat.com
student4  tower_host=student4.bd14.open.redhat.com
student5  tower_host=student5.bd14.open.redhat.com
student6  tower_host=student6.bd14.open.redhat.com
student7  tower_host=student7.bd14.open.redhat.com
student8  tower_host=student8.bd14.open.redhat.com
student9  tower_host=student9.bd14.open.redhat.com
student10  tower_host=student10.bd14.open.redhat.com


[all:vars]
tower_username=admin
tower_password=jYXme7awQyldTj
```

### Step 2

Run the `configure_tower.yml` playbook.

> This playbook will require the **ansible.tower** collection. You can install that by running ansible-galaxy collection install -r collections/requirements.yml.


> Make sure that your automation hub token is associated with the **ANSIBLE_GALAXY_SERVER_AUTOMATION_HUB_TOKEN**  env variable or is added to your ansible.cfg file first.

``` bash
$ ansible-playbook configure_tower.yml
```

This should pre-populate the attendee Towers with the 3 orgs and all
the users in the appropriate orgs
