# Deploy from script or cloud-init

You can use `install.sh` to prepare the environment and deploy AlgoVPN on the local Ubuntu server in one shot using cloud-init or run the script directly on the server. The script doesn't configure any parameters in your cloud, so it's on your own to configure related [firewall rules](/docs/firewalls.md), a floating ip address and other resources you may need.

## Cloud init deployment

You can copy-paste the snippet below to the user data (cloud-init or startup script) field when creating a new server. For now it is only possible for [DigitalOcean](https://www.digitalocean.com/docs/droplets/resources/metadata/), Amazon [EC2](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/user-data.html) and [Lightsail](https://lightsail.aws.amazon.com/ls/docs/en/articles/lightsail-how-to-configure-server-additional-data-shell-script), [Google Cloud](https://cloud.google.com/compute/docs/startupscript) and [Azure](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/using-cloud-init).

```
#!/bin/bash
curl -s https://raw.githubusercontent.com/trailofbits/algo/master/install.sh | sudo bash -x
```
The command will prepare the environment and install AlgoVPN with default parameters. If you want to modify the behaviour you may define additional variables.

## Variables

`METHOD` -  which method of the deployment to use. Possible values are local and cloud. Default: cloud. The cloud method is intended to use in cloud-init deployments only. If you are not using cloud-init to deploy the server you have to use the local method  
`ONDEMAND_CELLULAR` - "Connect On Demand" when connected to cellular networks. Bollean. Default: false  
`ONDEMAND_WIFI` - "Connect On Demand" when connected to Wi-Fi. Default: false  
`ONDEMAND_WIFI_EXCLUDE` - List the names of any trusted Wi-Fi networks where macOS/iOS IPsec clients should not use "Connect On Demand". Comma-separated list.  
`WINDOWS` - To support Windows 10 or Linux Desktop clients. Default: false  
`STORE_CAKEY` - To retain the CA key. (required to add users in the future, but less secure). Default: false.   
`LOCAL_DNS` - To install an ad blocking DNS resolver. Default: false.   
`SSH_TUNNELING` -  Enable SSH tunneling for each user. Default: false  
`ENDPOINT` - The public IP address or domain name of your server: (IMPORTANT! This is used to verify the certificate). It will be gathered automatically for DigitalOcean, AWS, GCE or Azure if the `METHOD` is cloud. Otherwise you need to define this variable according to your public IP address.  
`USERS` - list of VPN users. Comma-separated list.  
`REPO_SLUG` - Owner and repository that used to get the installation scripts from. Default: trailofbits/algo.   
`REPO_BRANCH` - Branch for `REPO_SLUG`. Default: master.  
`EXTRA_VARS` - Additional extra variables.  
`ANSIBLE_EXTRA_ARGS` - Any available ansible parameters. ie: `--skip-tags apparmor`.   

## Examples

##### How to customise a cloud-init deployment by variables

```
#!/bin/bash
export ONDEMAND_CELLULAR=true
export WINDOWS=true
export SSH_TUNNELING=true
curl -s https://raw.githubusercontent.com/trailofbits/algo/master/install.sh | sudo bash -x
```

##### How to deploy locally without using cloud-init

```
export METHOD=local
export ONDEMAND_CELLULAR=true
curl -s https://raw.githubusercontent.com/trailofbits/algo/master/install.sh | sudo bash -x
```

##### How to deploy a server using arguments

The arguments order as per [variables](#variables) above

```
curl -s https://raw.githubusercontent.com/trailofbits/algo/master/install.sh | sudo bash -x -s local true false _null true true true true myvpnserver.com
```
