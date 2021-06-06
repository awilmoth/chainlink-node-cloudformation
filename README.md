# Overview

This project creates a secure Chainlink node running on an Ubuntu 20.04 EC2 instance using docker. The Chainlink node runs in a private subnet and can be accessed via private networking from a bastion host which is also created. The template creates a VPC and associated networking in addition to a private key which is stored in SSM Parameter store.

# Installation

Upload cl-install.cfn to Cloudformation to create the resources. Fill in the required values for installation. You'll need an Ethereum node that runs either Rinkeby or Mainnet.

# Accessing the Private Key

The private key created by the template can be downloaded by means of the AWS CLI. You need the private key to initiate a SSH connection from your computer to the bastion host. The same private key can be used to SSH into the Chainlink Node if needed. Please note that the default format in your AWS config on your local machine needs to be set to json for this to work, and jq needs to be installed. 

## Writing the Private Key to 'ChainlinkKeyPair.pem' on Local Machine

```
aws ssm get-parameter --name /Chainlink/default/private-key --with-decryption | jq -r '.Parameter.Value' > ChainlinkKeyPair.pem && chmod 400 ChainlinkKeyPair.pem
```
## Writing the Private Key to 'ChainlinkKeyPair.pem' on Bastion Host
```
export AWS_DEFAULT_REGION=$(curl --silent http://169.254.169.254/latest/dynamic/instance-identity/document | jq -r .region) && aws ssm get-parameter --name /Chainlink/default/private-key --with-decryption | jq -r '.Parameter.Value' > ChainlinkKeyPair.pem && chmod 400 ChainlinkKeyPair.pem
```
# Accessing the Chainlink Node UI

To access the Chainlink Node UI, it's convenient to create a SOCKS5 proxy on your local machine. From there, you can use a browser to access the Chainlink Node UI via the server's private IP address on port 6688. Firefox is recommended as it provides easy setup for SOCKS5 proxies via the Preferences menu. The command below creates a SOCKS5 proxy via SSH on localhost port 9090. Enter this information in Firefox. You can obtain the Chainlink Node's private IP address via the AWS EC2 console.

```

ssh -D 9090 -q -C -N -i ChainlinkKeyPair.pem ubuntu@<BastionHostIP>

```
# References:  
  
##### https://github.com/devlintrace/laxinstall
##### https://github.com/thodges-gh  
