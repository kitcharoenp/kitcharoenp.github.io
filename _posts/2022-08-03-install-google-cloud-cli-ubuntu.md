---
layout : post
title : "Install google-cloud-cli on Ubuntu"
categories : [gcp]
published : true
---

#### [Installation instructions](https://cloud.google.com/sdk/docs/install#deb)

```shell
# install apt-transport-https 
sudo apt-get install apt-transport-https ca-certificates gnupg

# Add the gcloud CLI distribution URI 
echo "deb [signed-by=/usr/share/keyrings/cloud.google.gpg] https://packages.cloud.google.com/apt cloud-sdk main" | sudo tee -a /etc/apt/sources.list.d/google-cloud-sdk.list

# Import the Google Cloud public key.
curl https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key --keyring /usr/share/keyrings/cloud.google.gpg add -

# Update and install the gcloud CLI:
sudo apt-get update && sudo apt-get install google-cloud-cli

# Install app-engine-python additional components
sudo apt-get install  google-cloud-cli-app-engine-python

# Run gcloud init to get started:
gcloud init
```
