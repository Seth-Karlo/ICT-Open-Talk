# ICT-Open demo

## Prepare credentials
Install the AWS cli tool (this is for mac):

`pip install awscli`

Install the kubectl command:

```
wget https://storage.googleapis.com/kubernetes-release/release/v1.5.2/bin/darwin/amd64/kubectl && sudo mv kubectl /usr/local/bin/ && sudo chmod +x /usr/local/bin/kubectl
```

Install the kops cluster manager:

```
brew update && brew install kops
```

Login to AWS and get your API and Secret keys
Then, type:

```
$ aws configure
AWS Access Key ID [None]: AKIAIOSFODNN7EXAMPLE
AWS Secret Access Key [None]: wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY
Default region name [None]: eu-west-1
Default output format [None]: ENTER
```

## Buy a domain

Unless you're interested in the details, the easiest thing to do here is to buy a real domain for $12 per year and use this for your cluster. If you'd prefer to use an existing one, please look at the instructions here: https://github.com/kubernetes/kops/blob/master/docs/aws.md

Now create the s3 bucket to hold our state store:

```
export MYCLUSTER=andys-container-setup
aws s3api create-bucket --bucket mcpaandy1-container-cloud-net-state-store --region eu-west-1
```

In this example, I'm using container-cloud.net

## Build the cluster:

```
export NAME=mcpaandy1.container-cloud.net
export KOPS_STATE_STORE=s3://mcpaandy1-container-cloud-net-state-store # This is from the bucket we created above

kops create cluster --zones eu-west-1a ${NAME}
```

You should now have a two node cluster in eu-west-1a.

## Deploy the spark controllers

```
kubectl create -f spark/
```

This should create your spark cluster

## Scale up to ~100 cores

```
kops edit ig nodes
```

Change the min/max from 2 to 50 (if you have a default account you will be capped at 20 until you request an increase)

Then:

```
kops update cluster ${NAME} --yes
```

Within 5 minutes, you should have ~100 cores to use (each node has 2 cores). Remember to run the commands again to push it down to 2 nodes after you are finished to avoid a high bill!
