..
  Technote content.

  See https://developer.lsst.io/docs/rst_styleguide.html
  for a guide to reStructuredText writing.

  Do not put the title, authors or other metadata in this document;
  those are automatically added.

  Use the following syntax for sections:

  Sections
  ========

  and

  Subsections
  -----------

  and

  Subsubsections
  ^^^^^^^^^^^^^^

  To add images, add the image file (png, svg or jpeg preferred) to the
  _static/ directory. The reST syntax for adding the image is

  .. figure:: /_static/filename.ext
     :name: fig-label

     Caption text.

   Run: ``make html`` and ``open _build/html/index.html`` to preview your work.
   See the README at https://github.com/lsst-sqre/lsst-technote-bootstrap or
   this repo's README for more info.

   Feel free to delete this instructional comment.

:tocdepth: 1

.. Please do not modify tocdepth; will be fixed when a new Sphinx theme is shipped.

.. sectnum::

.. TODO: Delete the note below before merging new content to the master branch.

.. note::

   **This technote is not yet published.**

   Notes on aspects of the Kubernetes Cluster

.. Add content here.
.. Do not include the document title (it's automatically added from metadata.yaml).

Intro
=====
LSST uses Kubernetes (K8s) as a distributed management system for LSST 
containerized services. This document describes the underlying system 
configurations, best practices and use cases for the K8s during development 
and in production.   LSST services will be deployed across enclaves.  While
users of these services may interact with services between enclaves, the
enclaves themselves are standalone.  


Actors
======

The use cases make use of a standard set of Actors.

General
-------

The same person can hold some of these Actor's roles.

Developer - LSST-funded developer

Science User - LSST affiliate partner

Security - LSST-funded staff member specializing in security

System Administrator - LSST-funded system administration staff member

K8s Cluster Administrator  - K8s system software administrator.  Responsibilities include K8s software installation and updates, disaster recovery, network overlay deployments, adding and removing K8s nodes.

K8s Service Administrator - Deploys, monitors and maintains LSST services to the production K8s cluster.


Software 
========

Batch services - HTCondor batch system

Kubernetes - distributed management system for containerized applications


Users
=====

There are two types of end-users for the clusters: LSST development staff and 
LSST Science Users.

Sysadmin staff has procedures for adding both types of users.   LSST 
development staff has access to the LSST development resources (lsst-dev, 
etc.), as well as the science platform.  LSST Science Users only 
have access to the science platform.

Development staff that are no longer part of the project lose all access to
the K8s cluster and other LSST resources.   LSST Science Users who no longer
have an allocation will still be able to access their data, but lose access to
other services that had been available to them during their allocation.  

Users who change status during the project from LSST Science User to LSST
staff retain the data they had access to as a Science User. 

Users who change status during the project from LSST staff to Science user 
lose access to development resources, and will be given a new account,
adhering to the data privilege policy associated with their allocation. This
is to prevent exposure of data available they had as staff, which they
shouldn't have access to in their new role. It would be difficult and very
time-consuming to audit the existing data in their directory to prevent this.


The K8s Service Administrator deploys services, including administrative
services to the production K8s cluster. We expect an independent development
platform to be available for development testing. Authorized LSST development
staff have admin access to development platform to test and deploy services.  




JupyterLab Account Access
=========================
LSST development staff access to the Kubernetes home area via 
/home/{user}/jhome on lsst-dev.    This directory is the same home area on 
JuypterLab.

LSST Science User access to this jhome GPFS fileset is only available through JuypterLab.

Account Access
--------------

AA1: Authorized user logs into Jupyter Notebook
    a. User navigates to https://lsst-lspdev.ncsa.illinois.edu
    b. User clicks on "Notebooks" image.
    c. The user is presented with a link to "Sign in to CILogon," and clicks that button.
    d. The user is presented with a request for information which is about to be authorized:  "CILogon username" and "Your username and affiliation from your identity provider."  The identity provider is listed as "National Center for Supercomputing Applications," and a "Log on" link is shown.  User clicks that button.
    e. The user is presented with the CILogon page, which requires "NCSA Username" and "NCSA Kerberos Password."  An optional selection for "Don't Remember Login" is also provided.   After filling out the Username and Password, the user clicks on the "Login" button.
    f. The user is presented with the Duo authentication page.  The user authenticates with a push notification to a device or enters a passcode.
    g. After successfully authenticating, the user is presented with the "Spawner Options" page.  This page allows selection of an image and how much CPU and RAM to dedicate to the notebook.  The user makes these selections and clicks "Spawn" button.
    h.  A new notebook is spawned, and the user is presented with JuypterLab interface.

AA2: Developer wishes to access logs of containers for debugging. (short term)
    a. Developer starts an ssh session to cerberus4.ncsa.illinois.edu and authenticates with Duo.
    b. From there, Developer starts an ssh session to bastion01.ncsa.illinois.edu.
    c. From there, Developer starts an ssh session to kub001
    d. Developer runs "kubectl logs" command to retrieve logs for an element in the K8s commons.

AA3: Developer wishes to access logs of containers for debugging. (long-term)
    a. Developer starts an ssh session to cerberus4.ncsa.illinois.edu and authenticates with Duo.
    b. From there, Developer starts an ssh session to bastion01.ncsa.illinois.edu.
    c. From there, Developer starts an ssh session to lsst-headnode.ncsa.illinois.edu (NOTE: CHANGE THIS TO THE CORRECT NODE NAME WHEN THIS IS SET UP).
    d. Developer runs "kubectl logs" command to retrieve logs for an element in the K8s commons.

Fileset Access
==============

GPFS filesets
-------------

Access to "project" (read-write):  All users are put into one or more
groups(s), and have directory access below the "project" fileset to each
group to which they belong.  This access is not unrestricted to all of
"project."

Access to "datasets" (read-only):  Individuals/groups have different types of
access, depending on their standing in the project. Some datasets are
restricted for some period to LSST (first tier) collaborators before they
become available to other parts of the project.

Access to "scratch" (read-write):  All users are put into one or more
groups(s), and have directory access below the "scratch" fileset to each to
which they belong.  This access is not unrestricted to all of scratch. 
  
Access to "jhome" (read-write):  LSST Developers and Science Users have
access to the jhome fileset.   LSST developers have this as a separate mount
point named jhome which is accessible from their counts on lsst-dev. When
they log in, their home directory is in /home/{user}.  Users of lsst-dev
also have access to jhome.   LSST Science Users can only access the "jhome"
fileset through the accounts they access on the K8s commons and have no
visibility to /home.   An LSST Science User has write access to write to
/project and /scratch, and 100GB of disk space.

Access to "software" (read-only): All developers have read-only access to
this fileset.  This access is currently not available via Jupyter Notebook.
This access may be added in the future to access the batch system commands.

VOSpace/WebDAV
--------------

Access to "File Workspace" (read-write): File Workspace is a subdirectory
under the jhome fileset described above.  Anything in the File Workspace is
accessible via VOSpace and WebDAV.  (Note that because of this exposure, it
needs to be a subdirectory, not the $HOME of jhome itself.

Users cannot modify existing containers to add additional software.  Any areas
in the container that may be writable will be lost when the container is
reconstructed.  Any other software that the user may wish to use can be
stored in their $HOME space in their notebook, project space, or temporarily
in scratch space.

Users with approved proposals for larger allocations will be granted more
space, based on requirements of their proposals.

Batch Services
==============


Batch Services will have access to the same volumes accessible via the K8s
cluster, with the same user/group restrictions.

Batch Services will be configured to only allow submissions from lsst-dev
and the K8s commons.  Containers that run outside of the K8s commons will not
be able to access the batch system.

LSST developer:  Command line tools will be available to submit and monitor
jobs from lsst-dev.

LSST Science User: Command line tools will be available when the user drops
into the shell from the Jupyter Notebook. The tools should be available in
/software but may be included as part of the notebook container.

The HTCondor manager daemons need to run outside of the K8s commons for 
several reasons. Configurations on worker nodes point to the manager node,
which always needs to be running at the same IP address.   Additionally, the
mechanisms (logging and otherwise) that are in operation while HTCondor
daemons are running are what is used to recover state if the daemons need to
restart.  This information can not be kept within a container, because the
default areas that are used disappear when the container exits.

K8s containers and standalone batch
-----------------------------------

Rather than having a static allocation of processing resources, there is
a desire to shift how resources are allocated depending on tasks that
need them.  For example, nightly batch processing resources may be idle
on some days, and they could be reallocated to yearly processing tasks.  
Or we might have some K8s resources that were idle, and that could be
temporarily dedicated to nightly batch processing.

Nightly batch processing, yearly batch processing, and L3 resources could
co-exist easily as one HTCondor cluster, with nodes given ClassAds to
describe which type of processing to which they are dedicated. Jobs submitted
to the cluster would use the ClassAd matching mechanism to run on the 
appropriate systems.  Node ClassAds would describe what type of jobs a
node could run, along with restrictions on which locations were able to
submit jobs. For example, a science user could be prevented from spoofing
a job to get more processing resources from other parts of the cluster.

When the HTCondor administrator wants to change how many nodes are dedicated
to one type of processing to another, new ClassAds would be updated on those
nodes, and the job scheduler would handle the rest.  Shifting nodes back and
forth would only require some commands to the nodes on the cluster, and will
not require downtime.

Things get a bit more complicated when trying to dedicate resources from K8s
to a standalone batch system. There are two ways that this might be done. The
first would be to issue a command to take the K8s node out of the K8s cluster
and rededicating to the batch system. This method would mean some downtime 
for the system, where neither K8s containers nor HTCondor processing would run.

The second would be to keep the K8s node as is, run a container containing
the HTCondor software, and to have it join the batch system.

It's unclear at this point whether the batch system will run most
effectively as a set of containers in the K8s commons or as a standalone
traditional batch cluster.  There are several things to consider.

We've done some preliminary tests and have been able to bring up HTCondor
worker nodes in a K8s container, attached to an HTCondor manager which is
outside of the K8s cluster. We have not done testing to see what the
optimal size (i.e., dedicated memory, core count, etc.) of an HTCondor
K8s pod would be. Since an HTCondor node usually is configured to use the
total number of cores per node and all available memory, letting the job
partition the resources as it needs to, "pre-partitioning" without a
complete understanding of all the types of jobs which will run on the batch
cluster may be problematic unless the container takes over the whole node. We
would need to gauge how much CPU would be required per container for job
processing on a node. This may be possible by analyzing the types of Nightly
and Yearly processing jobs we will have. Testing still needs to be done to
see what other implications of running HTCondor from a container in the K8s
commons would be.   We expect that HTCondor containers running jobs will have
a significant impact on the number of pods that could be run over all if
entire nodes were dedicated to HTCondor.  These are the same resources
which would be used by the LSST science users, and it becomes challenging
to gauge the number of resources to dedicating because of the wide variety
of jobs Science Users may submit.

K8s assumptions -
    a. The HTCondor master node and associate processes run outside of the K8s cluster.
    b. HTCondor containers run indefinitely because they provide long-term service.
    c. HTCondor pods are already running at the time of Nightly Processing requests.
    d. HTCondor batch processing is reserved for its namespace, with appropriate ClassAds for each type of computing to done.  This is so that one set of HTCondor batch resources don't leech resources from each other.
    e. HTCondor resources can be brought online by launching new containers and put offline by stopping containers.

We expect that if HTCondor is run from a container that the LSST software
stack and HTCondor binaries will be run out of /software, leaving the
container itself as small as possible, and allowing it to brought up more
quickly.

The following use cases apply whether the batch control system is entirely on
K8s or running standalone.  All systems are assumed to have HTCondor software
installed on them.

BCS 1: Prompt Processing needs more batch resources for processing, and other batch processing services are idle.
    a. HTCondor Administrator issues commands to change ClassAds for additional nodes to specify they are part of Prompt Processing.

BCS 2: Prompt Processing has an excess number of batch resources available to it after processing has been caught up, and other batch processing services are below their allocation.
    a. HTCondor Administrator issues commands to change ClassAds for Prompt Processing Node(s) to label them as part of the general batch processing services.

BCS 3: Prompt Processing has an excess number of batch resources available to it after processing has been caught up, and other batch processing services are at their designated allocation.
    a. K8s Services Administrator deletes these HTCondor pod(s).

BCS 4: Prompt Processing needs more batch resources for processing, and other batch processing services are busy. Assumes K8s resources could be dedicated to batch processing and assumes that HTCondor containers would be used to add resources to batch.
    a. K8s Services Administrator deploys new HTCondor pod(s).
    b. HTCondor Administrator issues commands to change ClassAds for so those nodes additional are part of Prompt Processing.

BCS 5: Prompt Processing needs more batch resources for processing, and other batch processing services are busy. Assumes K8s resources could be dedicated to batch processing, and the system will not be running HTCondor containers.
    a. K8s Services Administrator drains containers from node(s) and waits for the node to become idle. 
    b. HTCondor Administrator starts HTCondor services on that node.
    c. HTCondor Administrator issues commands to change ClassAds for so those nodes additional are part of Prompt Processing.

Administrative functions
========================

System administration:  For the most part, updates here are handled as they
usually are for all systems.   Two exceptions to this are firewall rules and
K8s software updates.   

Setting up the firewall rules for nodes used in a K8s cluster can be somewhat
problematic because K8s itself updates the firewall rules during installation
of the K8s system software. Automatic updates to rules (via puppet) may
cause issues if rules that K8s writes are overriden by puppet rules.

The K8s software packages must not be updated via automatic YUM updates. The
YUM updates will overwrite configuration files that K8s processes read in
when they first start.   Any changes to the configuration files during
initial installation will be overwritten in a YUM update and could render
the K8s cluster inoperable after the next reboot.

K8s cluster administration: Main responsibility is to set up and configuration
of the K8s system software, including the network overlay. We use Weave as
the network overlay because it is currently the only overlay that supports
multicast networking, which is a requirement of Firefly and QServ.

Other responsibilities include:
    Addition and deletion of nodes in the cluster
    Upgrades to the K8s system software
    Administration of the local Docker registry

Under no circumstances should any system level (routing, node maintenance, 
etc.) be done by anyone by the K8s cluster admin, and all changes must be
documented.  This is for traceability, reproducibility, and the general
stability of the K8s cluster.

K8s service administration:  During development, the administration of
services are handled by the developers themselves. Depending on the
application, K8s admin access to the cluster may be required and is dealt
with on a case by case basis.  During production, deployment of services
will be done by LDF staff.  Assistance from developers may be needed at
times.  Again, this will be done on a case by case basis.


Maintenance
===========

K8s system software updates are frequent. New software is released every
couple of weeks, and sometimes even more frequently. The "maintained"
versions of Kubernetes are within three releases of the current release.
As of this writing, version 1.10.4 is the newest release and version 1.9
and 1.8 are maintained. Version 1.11.0-beta.1 has been pre-released. Version 
1.7 is considered obsolete. Releases are usually, but not always backward
compatible. We are using version 1.9.3 on the Kubernetes cluster, and plan
on upgrading to version 1.10.x at the end of June 2018.

We've decided to maintain one release for a set period to have a stable
environment.   A regular upgrade cycle should be implemented to have releases
within the "maintained" version window.   To test this correctly, we will
have to test on a development cluster to see how upgrading could impact
deployed applications.  This is very important because of Kubernetes' history
of obsoleting features and changing APIs.

Software procedure for installing has been created and is available at:

https://github.com/lsst-dm/k8s-scripts/

With instructions here:

https://dmtn-071.lsst.io


This procedure relies on "kubeadm" for the install.  It is also used to 
get advice on how to do upgrades, as well as the upgrade itself.

# sudo kubeadm upgrade plan

Components that must be upgraded manually after you have upgraded the control plane with 'kubeadm upgrade apply':

::
 
 COMPONENT   CURRENT       AVAILABLE
 Kubelet     20 x v1.9.3   v1.10.4

 Upgrade to the latest stable version:

 COMPONENT            CURRENT   AVAILABLE
 API Server           v1.9.6    v1.10.4
 Controller Manager   v1.9.6    v1.10.4
 Scheduler            v1.9.6    v1.10.4
 Kube Proxy           v1.9.6    v1.10.4
 Kube DNS             1.14.7    1.14.7
 Etcd                 3.1.11    3.1.11

You can now apply the upgrade by executing the following command:

::

    kubeadm upgrade apply v1.10.4

Note: Before you can perform this upgrade, you have to update kubeadm to v1.10.4.

Docker Registry
===============

We will deploy local Docker registries for internal operations. This will
give us faster download times, better security and better control of the
service itself. If we primarily relied on an outside registry, service
(or even business) failures would prevent us from operating through no
fault of our own. Security staff should vet all containers in these registries.  

Namespace ACL
=============

Kubernetes namespaces allow partitioning of applications into their areas,
with unique resource names within that namespace.  For example, 
JupyterLab is deployed in the jupyter-lsst namespace. The development groups
for the PDAC are already implementing namespaces for their applications.

As of this writing, no access control enforcement is available for namespaces
in Kubernetes. Anyone (or any pod) with privileges on the cluster can
access any namespace and its resources.  Currently, we afford some small
measure of restricted access by employing the use of Kubernetes namespace
contexts. When working within a namespace, only resources in that namespace
can be seen and accessed.  Users can still override this or move into new
contexts, so this is not meant to be a substitute for real ACL. We expect to
implement ACL for namespaces when Kubernetes deploys that feature in a
future release.


Preparing for disaster recovery
===============================

For disaster recovery, there are several options, depending on what state to
bring back the K8s cluster.   

Option 1 is to bring back the K8s cluster to the initial state as if the
cluster was just started.  In other words, this is the state at which all
applications have started, but no users have yet used any of the services.
This has the K8s Cluster Administrator bringing back up the cluster so
that it can deploy containers, and the K8s Service Administrator restarts
all services.  Any containers that had been previously deployed would no
longer exist, and all Users would need to restart any notebooks, or log in
and reconnect to other services.

Option 2 is to bring back the K8s cluster to the state at which the previous
control plane backup had been done.  This can be done by:

- `etcdctl <https://github.com/coreos/etcd/tree/master/etcdctl>`_
- `kube-backup <https://github.com/pieterlange/kube-backup>`_
- `ark <https://github.com/heptio/ark>`_
- `reshifter <https://github.com/mhausenblas/reshifter>`_
                            
JupyterLab Requirements (see: sqr-018)
=======================

Administration 
--------------

During development, a small set of users will need admin access on the K8s cluster to configure resources correctly.  Once development has stabilized and we move services to production, the K8s services administrator will deploy services based on instructions devised during development.

CPU capacity
------------

Deployed pods will require between 0.5 and 4 cores per concurrent user.

Memory
------

Deployed containers will require between 512MB and 8GB per concurrent user.

Local Storage
-------------

Local storage per node needs to be about 100GB.  As of this writing, containers are about 10GB each, with the expectation that about five different container images will be stored on a node at any given time.

User Storage
------------

User storage in jhome is set to a 100GB quota.

Container Cache
---------------

Local container cache size is 250GB total.

Shared storage
--------------

This is storage intended for quick prototyping.  10TB total.

Security
========

There are a number of resources available that I found during the research for this document that describes hardening of K8s clusters.

Hacking and Hardening Kubernetes By Example:

- `Video <https://www.youtube.com/watch?v=vTgQLzeBfRU>`_
- `Slides <https://schd.ws/hosted_files/kccncna17/d8/Hacking%20and%20Hardening%20Kubernetes%20By%20Example%20v2.pdf>`_


`Securing a Cluster <https://kubernetes.io/docs/tasks/administer-cluster/securing-a-cluster/>`_

`Overview of Kubernetes Security best practices <https://github.com/freach/kubernetes-security-best-practice/blob/master/README.md>`_

`On Securing the Kubernetes Dashboard <https://blog.heptio.com/on-securing-the-kubernetes-dashboard-16b09b1b7aca>`_


An open-source Kubernetes security test suite, kube-bench, is available via GitHub. This suite runs tests that show pass/fail, as well as recommends how settings may be removed or changed for any issues that are detected.  Note that this benchmark suite is not in sync with the current Kubernetes release. The latest update was one month ago.  However, that release lags behind by two revisions of Kubernetes as of this writing. 

URL: https://github.com/aquasecurity/kube-bench

.. .. rubric:: References

.. Make in-text citations with: :cite:`bibkey`.

.. .. bibliography:: local.bib lsstbib/books.bib lsstbib/lsst.bib lsstbib/lsst-dm.bib lsstbib/refs.bib lsstbib/refs_ads.bib
..    :encoding: latex+latin
..    :style: lsst_aa
