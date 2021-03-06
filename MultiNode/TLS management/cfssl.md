## Introduction
In an cloud architecture, we needs to make sure that the communication between the different services is secure.  
In order to do that, a common technique is to use TLS/SSL.  

JumpScale provide simple ways to deal with servcer and client certificate generation.

### CFSSL Service
The AYS service responsible of all the TLS infrastructure is [cfssl](https://github.com/jumpscale7/ays_jumpscale7/tree/master/_tools/cfsll).

to install simple do 
```
ays install -n cfssl --data 'instance.initca:True'
```
Note here, we tell cfsll service to generate a new Certificate Authority (CA) during the installation.
You can also install the service without generating a new CA.

Once you have this service installed, you can start creating your certificate. Jumpscale proving a extension that expose a simple interface.

#### example:
Here we are going to genereate a new CA, then create a server certificate signed by this newly created CA.  
```python
tls = j.tools.tls.getByInstance('main')
subjetcs = tls.askSubjects()
    Country [AE]: 
    Location [Dubai]: 
    Organisation [GreenITGlobe]: 
    Organisation Unit [0-complexity]: 
    State [Dubai]: 
    CommonName: Autogenerated CA
ca_cert, ca_key = tls.createCA(subjetcs)
certificate generated at /opt/jumpscale7/hrd/apps/cfssl/tls/root-ca.pem and key at /opt/jumpscale7/hrd/apps/cfssl/tls/root-ca-key.pem


server_subj = tls.askSubjects()
    Country [AE]: 
    Location [Dubai]: 
    Organisation [GreenITGlobe]: 
    Organisation Unit [0-complexity]: 
    State [Dubai]: 
    CommonName: www.myserver.com

server_csr, server_key = tls.createCSR('myserver',server_subj,['www.myserver.com'])
certificate signing request generated at /opt/jumpscale7/hrd/apps/cfssl/tls/server.csr and key at /opt/jumpscale7/hrd/apps/cfssl/tls/server-key.pem

server_cert = tls.signCSR(server_csr, ca_cert, ca_key)
certificate created at /opt/jumpscale7/hrd/apps/cfssl/tls/server.pem
```

When you instanciate the tls object, you specifie the instance name of the cfssl service you want to use. This provide the path where all your keys and certificate will be generated.  

If the cfssl service is located at ```/opt/jumpscale7/hrd/apps/cfssl``` the keys and cert will be created at ```/opt/jumpscale7/hrd/apps/cfssl/tls``` 