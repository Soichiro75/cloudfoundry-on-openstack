# 02_previously-setup-openstack

## Helpful Links

- [CF Documentation](https://docs.cloudfoundry.org/deploying/openstack/index.html) > "Step 1: Environment Setup"


## Validate your OpenStack Instance

[Validate your OpenStack Instance](https://docs.cloudfoundry.org/deploying/openstack/validate_openstack.html)


### Can you access the OpenStack APIs for your instance of OpenStack?
- If you don't have other Linux machine, you can prepare with this procedure:
  - Install OS with minimum
  - Setup IP address
  - Install packages
    - Note: `gem install` need packages, `ruby-dev build-essential libxml2 libxml2-dev zlib1g-dev`

```
apt install ruby gem ruby-dev build-essential libxml2 libxml2-dev zlib1g-dev
```

- Create a `~/.fog` file and copy the below content, then revise `HOST_IP`, `PASSWORD`, `USERNAME`, `PROJEXT_NAME`, `REGION`. You can find the info where `~/keystonerc_admin` in OpenStack.:

```
:openstack:
  :openstack_auth_url:  http://HOST_IP:5000/v2.0/tokens
  :openstack_api_key:   PASSWORD
  :openstack_username:  USERNAME
  :openstack_tenant:    PROJECT_NAME
  :openstack_region:    REGION # Optional
```

- Install the fog application in your terminal, then run it in interactive mode:
  - [fog](https://github.com/fog/fog) is the Ruby cloud services library.

```
# install fog
gem install fog

# access test by using fog
## You can see [], it is an empty array in Ruby.
## You might see a long list of servers being displayed if your OpenStack tenancy/project already contains provisioned servers.

fog openstack
>> Compute[:openstack].servers
  <Fog::Compute::OpenStack::Servers
    filters={}
      [
      ]
  >

# exit fog
>> exit
```
