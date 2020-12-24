Satellite_Patch_Management
=========

A role for automating the staging/promotion of Satellite Content-Views through various Organizations and LifeCycle Environemnts.

Requirements
------------

This role requires the redhat.satellite collection.

Role Variables
--------------

The main amount of work is vars/main.yml.

The primary dictionary is the organizations dictionary, which is formatted as such:
```
organizations:
  org1:
    lifecycle_environments:
      - "Dev"
      - "QA"
      - "Prod"
    content_views:
      - "content-view1"
      - "content-view2"
  org2:
    lifecycle_environments:
      - "Dev2"
      - "QA2"
      - "Prod2"
    content_views:
      - "content-view3"
      - "content-view4"
```

This can run against multiple organizations/lifecycle_environments/content-views or selected subsets.

For example, if the previously mentioned dictionary describes *ALL* of my Satellite environment, but I only want to promote the 'Prod' lifecycle_environment in the content-view 'content-view1' in organization 'org', my dictionary would look like this:
```
organizations:
  org1:
    lifecycle_environments:
      - "Prod"
    content_views:
      - "content-view1"
```
Items not described in the inventory will not be affected (the exception being Library when a new content_view_version needs to be published, but you don't have hosts in the Library content_view, right?  RIGHT?).

If you only want to use this role for staging new content_view_versions, simply set the variable **publish_only** to **True** (this can be done in vars/main.yml or elsewhere).  This action will only publish a new content_view_version to the content_views described in the dictionary.

If you want to rollback (reverse promote) lifecycle environments simply set the variable **rollback** to **True** (this can be done in vars/main.yml or elsewhere).  This action will only roll back lifecycle_environments in the content views described in main dictionary.


Satellite information is accessed by way of the dictionary 'satellite', formatted as such:
```
satellite:
  satellite_user: admin
  satellite_password: changeme
  satellite_url: "https://mysattelliteserver.myorg.com"
```
This is in the vars/main.yml for illistrative purposes only!  Please use a vault (or custom credential-type if using Tower).  STORING PASSWORDS IN PLAINTEXT IS BAD, MMM-KAY?


Dependencies
------------

You need a Satellite user with admin access to the Organizations, Lifecycle_Environments, and Content_Views you wish to interact with.

By default, the role will require a valid SSL certificate installed on your Satellite server that the ansible client can trace trust to.  To disable that update the 'FOREMAN_VALIDATE_CERTS variable in defaults/main.yml.'


Example Playbook
----------------

The role can be instantiated quite simply, all of the decision making is handled by the variables set:

```
---
- name: Get current Prod Content View Version
  hosts: all
  tasks:
  - name: "Run the Satellite Patch Management Role"
    include_role: 
      name: Satellite_Patch_Management 
```

License
-------

BSD

Author Information
------------------

Created by Brandon Marlow and Brad Hinson.  For beer donations, please contact Brandon, for support requests, contact Brad :-)
