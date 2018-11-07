# Setup & Configure OpenShift


https://github.com/openshift/origin/releases/download/v3.10.0/openshift-origin-client-tools-v3.10.0-dd10d17-linux-64bit.tar.gz

https://github.com/openshift/origin/releases/download/v3.10.0/openshift-origin-server-v3.10.0-dd10d17-linux-64bit.tar.gz

oc cluster up --metrics --logging


--enable=web-console,registry,router,persistent-volumes


automation-service-broker,
centos-imagestreams,
persistent-volumes,
registry,
rhel-imagestreams,
router,
sample-templates,
service-catalog,
template-service-broker,
web-console

Disabled-by-default components:
automation-service-broker,
rhel-imagestreams,
service-catalog,
template-service-broker
''

Default HTTP cache directory

oc cluster up  --public-hostname '144.229.218.174' --base-dir '/opt/oc' --enable=[''] --use-existing-config=true


--no-proxy=[]



-base-dir=""
Directory on Docker host for cluster up configuration

--enable=[]
A list of components to enable.  '' enables all on-by-default components, 'foo' enables the component named 'foo', '-foo' disables the component named 'foo'. All components: automation-service-broker, centos-imagestreams, persistent-volumes, registry, rhel-imagestreams, router, sample-templates, service-catalog, template-service-broker, web-console Disabled-by-default components: automation-service-broker, rhel-imagestreams, service-catalog, template-service-broker

--forward-ports=false
Use Docker port-forwarding to communicate with origin container. Requires 'socat' locally.

--http-proxy=""
HTTP proxy to use for master and builds

--https-proxy=""
HTTPS proxy to use for master and builds

--image="openshift/origin-${component}:${version}"
Specify the images to use for OpenShift

--no-proxy=[]
List of hosts or subnets for which a proxy should not be used

--public-hostname=""
Public hostname for OpenShift cluster

--routing-suffix=""
Default suffix for server routes

--server-loglevel=0
Log level for OpenShift server

--skip-registry-check=false
Skip Docker daemon registry check

--write-config=false
Write the configuration files into host config dir


--host-pv-dir


--public-hostname="


You are logged in as:
    User:     developer
    Password: <any value>

To login as administrator:
    oc login -u system:admin


https://144.229.218.174:8443/
--metrics --logging --host-data-dir '/opt/oc/data' --host-config-dir '/opt/oc/config' --base-dir '/opt/oc/base'


--enable=[]
A list of components to enable.  '' enables all on-by-default components, 'foo' enables the component named 'foo', '-foo' disables the component named 'foo'. All components: automation-service-broker, centos-imagestreams, persistent-volumes, registry, rhel-imagestreams, router, sample-templates, service-catalog, template-service-broker, web-console Disabled-by-default components: automation-service-broker, rhel-imagestreams, service-catalog, template-service-broker


You are logged in as:
    User:     developer
    Password: <any value>

To login as administrator:
    oc login -u system:admin


--host-data-dir

--host-data-dir '/Users/jmorales/.oc/profiles/origin-14/data'                 
--host-config-dir '/Users/jmorales/.oc/profiles/origin-14/config'                 
--routing-suffix 'apps.lcup'                 
--use-existing-config
