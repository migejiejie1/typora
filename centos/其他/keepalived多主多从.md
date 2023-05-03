```shell
主

! Configuration File for keepalived
global_defs {
    router_id node1
}

vrrp_instance VI_1 {
    state MASTER
    interface enp1s0f0
    virtual_router_id 51 
    priority 100
    advert_int 1
    authentication {
        auth_type PASS
        auth_pass 654321
    }
    virtual_ipaddress {
        10.50.6.231
    }
}

vrrp_instance VI_2 {
    state MASTER
    interface enp1s0f0
    virtual_router_id 100
    priority 100
    advert_int 1
    authentication {
        auth_type PASS
        auth_pass 123456
    }
    virtual_ipaddress {
        10.50.6.229
    }
}

vrrp_instance VI_3 {
    state MASTER
    interface enp1s0f0
    virtual_router_id 98
    priority 100
    advert_int 1
    authentication {
        auth_type PASS
        auth_pass 123456
    }
    virtual_ipaddress {
        10.50.6.230
    }
}

--------------------------------------------------------------------

从

! Configuration File for keepalived
global_defs {
    router_id node2
}

vrrp_instance VI_1 {
    state BACKUP
    interface enp1s0f0
    virtual_router_id 51 
    priority 50
    advert_int 1
    authentication {
        auth_type PASS
        auth_pass 654321
    }
    virtual_ipaddress {
        10.50.6.231
    }
}

vrrp_instance VI_2 {
    state BACKUP
    interface enp1s0f0
    virtual_router_id 100
    priority 50
    advert_int 1
    authentication {
        auth_type PASS
        auth_pass 123456
    }
    virtual_ipaddress {
        10.50.6.229
    }
}

vrrp_instance VI_3 {
   state BACKUP
    interface enp1s0f0
    virtual_router_id 98
    priority 50
    advert_int 1
    authentication {
        auth_type PASS
        auth_pass 123456
    }
    virtual_ipaddress {
        10.50.6.230
    }
}



```

