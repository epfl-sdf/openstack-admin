%Openstack, open cloud framework
%Malik Bougacha
%25 août 2015

# Introduction

### openstack

\center
\includegraphics[width=10cm]{img/logo.png}

### cloud is 'On demand'

* Utilisateur interagissent avec le framework pour acquerir une resource.
    \pause

    * Serveur
    \pause

    * Reseau
    \pause

    * Base de donnee
    \pause

    * Entree dns
    \pause

    * .  \pause . \pause . \pause .

* Minimum d'interaction avec l'administrateur (changement des quotas d'utilisation des resources)

* Gestion et management de ces resources.

### Interfaces

* api
    * python
    * ruby
    * perl
\pause

* Client en ligne de commande
\pause

* web interface

### Backend

* container lxc 
* kvm machines
* fiber channel volumes
* bind entries
* mariadb 
...
\pause

Transparent pour l'utilisateur sauf exception

### Hybrid cloud

Abstraction de resources externe

## Openstack les entrailles

### Openstack est simple

\center
\includegraphics[width=10cm]{img/archi.jpg}

## Autour d'openstack

### Queing system 
rabbitmq:

\includegraphics[width=1cm]{img/rabbitmq_logo.png}

\pause

* erlang based 

\pause

* quasi incassable™

\pause

* triviallement clusterisable

```erlang
{cluster_nodes, {['rabbit@node0', 'rabbit@node1'], disc}},
```

### Database system 

mariadb with galera:

\includegraphics[width=3cm]{img/galera_logo.png}

\pause

* fork of mysql after the glorious sun

\pause

* replication en master/master synchrone

\pause

* Gestion relativement aisee

\pause

* Replication et resolution des conflicts

### load balancer

haproxy:

\includegraphics[width=3cm]{img/haproxy_logo.png}

\pause

* load balancer

\pause

* Support de multiple protocol et de multiple verification des hosts



### balancage dynamique d'ip:

keepalived:

\includegraphics[width=3cm]{img/keepalived_logo.png}

\pause

* Support pour le balancage d'ip (vrrp)

\pause

* Support de multiple type de verification pour les services sous jacent

\pause

* Utilise en dessus du load balancer afin de garantir l'availability du load-balancer

### cache distribue

memcache:

\includegraphics[width=3cm]{img/memcache_logo.png}

\pause

* Support pour le balancage d'ip (vrrp)

\pause

* Support de multiple type de verification pour les services sous jacent

\pause

* Utilise en dessus du load balancer afin de garantir l'availability du load-balancer

### Gestion du switching openflow:

openvswitch:

\includegraphics[width=3cm]{img/ovs_logo.png}

\pause

* Support d'openflow

\pause

* Gestion du tagging, gestion des tunnels gre ....

\pause

* Tres facilement scriptable, api disponible 

## Openstack base services

### Keystone

* Responsable de l'identity

    * service
    * list des points d'acces aux apis
    * authorization
    * authentification

Backend:

* utilise avec suivant l'utilisateur une query dans le ldap ou dans mariadb.
    * utilisateur humain ==> ldap
    * service ==> mariadb

### Horizon 

Couche de presentation:

* application django modulable.
* modulable en fonction des services ajoutes a la stack.
* couvre la majorite des fonctions de la stack ainsi que les fonctions de management

### Cinder

Interface de stockage.

* creation des volumes des machines virtuelless.
* creation de volumes supplementaire et attachement au vms
* hosting des images utilisee pour le demarrage des vms

Backend:

* ceph: stockage generique object d'un project talk

### Glance

Interface de stockage des images.

* creation et manipulation 
* hosting des images utilisee pour le demarrage des vms
* Gestion des caches sur les hyperviseurs a travers le getionnaire de machine virtuelles

Backend:

* filesystem: stockage local et replication sur les controlleurs

### Nova

Gestion des machines virtuelles

* Gestion des hyperviseurs
* hosting des images utilisee pour le demarrage des vms
* Gestion des volumes associe a travers cinder

Backend:

* kvm: virtualisation integre dans le noyaux linux.
* Par default utilisation de ceph

### Neutron

Gestion du reseau

* Gestion des reseaux:
    * Reseau virtuel (vxlan, GRE pure)
    * Reseau 'physique' (vlan et natif)
* Gestion des resources reseau des machines:
    * Port des machines
    * MAC address 
* Gestion du reseau au niveau des hyperviseurs
* Gestion du resau au niveau du maitre
* Gestion des volumes associe a travers cinder

Backend:

* kvm: virtualisation integre dans le noyaux linux.
* Par default utilisation de ceph

### Petit quizz !

\pause
* Qui dois je interroger pour obtenir un mapping mac-ip ?
\pause
* Qui dois je interroger pour obtenir une liste des services disponibles ?
\pause
* Qui dois je interroger pour obtenir une liste des images ?
\pause
* Qui dois je interroger pour demarrer une machine virtuelle ?

## Openstack additional services

### Trove

Gestion des bases de donnees

* agent sur une base de donnee deja existante
* creation depuis 0, installation de la db et ajout de l'agent
* creation rapide d'une db

### Designate

Gestion des entrees DNS

### Other 
* Murano: Saas
* Ironic: Bare metal provisioning (PXE)
* ...
