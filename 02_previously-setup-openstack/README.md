# 02_previously-setup-openstack

## Helpful Links

- [CF Documentation](https://docs.cloudfoundry.org/deploying/openstack/index.html) > "Step 1: Environment Setup"


## Validate your OpenStack Instance

[Validate your OpenStack Instance](https://docs.cloudfoundry.org/deploying/openstack/validate_openstack.html)


### Can you access the OpenStack APIs for your instance of OpenStack?

- Create a `~/.fog` file and copy the below content:

```
:openstack:
  :openstack_auth_url:  http://HOST_IP:5000/v2.0/tokens
  :openstack_api_key:   PASSWORD
  :openstack_username:  USERNAME
  :openstack_tenant:    PROJECT_NAME
  :openstack_region:    REGION # Optional
```

In my environment `HOST_IP` is `192.168.101.1`.


- Install the fog application in your terminal, then run it in interactive mode:
  - [fog](https://github.com/fog/fog) is the Ruby cloud services library.

```
# install ruby
yum install ruby ruby-devel

# install fog
gem install fog

# execute fog
fog openstack
>> Compute[:openstack].servers
[]
```
