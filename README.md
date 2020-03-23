
# Deploying intelligent-doc-automation app to GKE via Google Cloud Marketplace

## Overview
Intelligent Document Automation is an application for document automation, i.e. extraction of entities from digital documents. There are two parts of this application. 1. Trade Finance and 2. Margin Call


##### Trade Finance API
This solution uses Neural Network LSTM based optical character recognition (OCR) followed by Entity Recognition (Natural Language Processing approach) whcih improves the efficiency of the process of searching key payment terms from documents.

##### Margin Call API
Emails related to margin calls made by the bank’s clients or responses to the bank originated calls at times included pdf or other attachments.

As the first step, mails are classified as a margin call or otherwise using random forest based classification.

NLP based entity recognition is used to identify the key terms related to the margin call.

The 
[Google Cloud Marketplace][1] 
is a easy way to deploy apps like tradefinance to a 
[Google Kubernetes Engine][2] 
cluster, with just a few clicks.

[1]: https://console.cloud.google.com/
[2]: https://cloud.google.com/kubernetes-engine/

## Usage
#### Endpoints
After application is deployed on kubernetes cluster an endpoint will be created(provided below) which the end user can use to extract the entities.

##### ENDPOINT URLs: 
 
"http://IP_ADDRESS/intelligent-doc-automation/trade-finance/api/v1/document"
"http://IP_ADDRESS/intelligent-doc-automation/margin-call/api/v1/document"

##### Steps to get the IP_ADDRESS
Go to Kubernetes Engine on GCP Console -> click on Workloads from left pane -> look for the namespace you created when deploying from marketplace with Type "Deployment" -> look for endpoints under Exposing Services.

##### Request Type: 
POST

##### Required Parameters:
  *trade-finance enddpoint only accepts pdf files as input and margin-call endpoint only accepts msg(MS outlook) format as input.
  | Name      | Value |
  | ----------- | ----------- |
  | file      | file should be passed here       |

##### Sample Output:
###### trade-finance
{'Destination Bank': 'THE BANK OF NEW YORK MELLON', 'Document Date': '12 JULY 2017', 'Destination Swift': 'IRVTUS3N', 'Issuing Bank': 'AUSTRALIA AND NEW ZEALAND BANKING GROUP LTD.', 'Beneficiary': 'SIAM PROTEINS CO. , LTD. .', 'Applicant': 'GIBSON LTD T/A SKRETTING AUSTRALIA', 'Destination Bank Location': 'NY 10286 UNITED STATES OF AMERICA'}

###### margin-call
{
  "attachment_content": [
    "alecta",
    "Regeringsgatan 107",
    "103 73 Stockholm",
    "Sweden",
    "Phone: +46 8 441 68 30",
    "Fax: +46 8 20 75 44",
    "Email: BusinessSupport@alecta.com",
    "Margin Call notice",
    "Citigroup Global Markets Limited",
    "Mail Drop CGC 05-66",
    "33 Canada Square, Canary Wharf",
    "London E14 SLB",
    "United Kingdom",
    "Phone. +44 207 986 4108",
    "Facsimile: +44 207 192 3222",
    "Email: OTCmargining@citi.com",
    "Exposure date 2017-01—16",
    "Figures in Base currency (EUR)",
    "Exposure EUR 32 691 039",
    "After Haircut Value of Collateral Pending —23 432 000",
    "Credit Support amount 9 259 039",
    "Credit Support amount (rounded) 9 260 000",
    "Minimum Transfer Amount 2 500 000",
    "Rounding Amount 10 000",
    "Margin Call for Citigroup",
    "EUR 9 260 000",
    "Value date 2017-01—18"
  ],
  "margin_call": "Callout"
}

## Installation

### Quick install with Google Cloud Marketplace

Deploy tradefinance to Google Kubernetes Engine using Google Cloud Marketplace, by following the [on-screen instructions.]()

### Command line instructions

#### Prerequisites

##### Set up command-line tools

You'll need the following tools in your development environment. If you are using Cloud Shell, then gcloud, kubectl, Docker, and Git are installed in your environment by default.

* [gcloud](https://cloud.google.com/sdk/gcloud/)
* [kubectl](https://kubernetes.io/docs/reference/kubectl/overview/)
* [docker](https://docs.docker.com/install/)
* [git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)
* [helm](https://helm.sh/)

Configure gcloud as a Docker credential helper:

```bash
gcloud auth configure-docker
```

##### Create a Google Kubernetes Engine (GKE) cluster

Create a new cluster from the command line:

```bash
export CLUSTER=ida-cluster
export ZONE=us-central1-a

gcloud container clusters create "${CLUSTER}" --zone "${ZONE}"
```

Configure ```bash kubectl``` to connect to the new cluster:
```bash 
gcloud container clusters get-credentials "${CLUSTER}" --zone "${ZONE}"
```

##### Clone this repo

Clone this repo and its associated tools repo:

```bash
git clone --recursive https://github.com/virtusaCloudCOE/intelligent-doc-automation.git
```

##### Install the Application resource definition

An Application resource is a collection of individual Kubernetes components, such as Services, Deployments, and so on, that you can manage as a group.

To set up your cluster to understand Application resources, run the following command:

```bash
kubectl apply -f "https://raw.githubusercontent.com/GoogleCloudPlatform/marketplace-k8s-app-tools/master/crd/app-crd.yaml"
```

You need to run this command once.

The Application resource is defined by the [Kubernetes SIG-apps](https://github.com/kubernetes/community/tree/master/sig-apps) community. The source code can be found on [github.com/kubernetes-sigs/application](https://github.com/kubernetes-sigs/application).

#### Install the app


##### Configure the app with environment variables

Choose an instance name and [namespace](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/) for the app. In most cases, you can use the ```default``` namespace.

```bash 
export APP_INSTANCE_NAME=ida-1
export NAMESPACE=ida-app-namespace
```

##### Create namespace in your Kubernetes cluster

If you use a different namespace than the ```default```, run the command below to create a new namespace:

```bash
kubectl create namespace "${NAMESPACE}"
````

