[![pre-commit](https://github.com/SeungYeop-Yang/learning_k8s/actions/workflows/pre-commit.yaml/badge.svg?branch=main)](https://github.com/SeungYeop-Yang/learning_k8s/actions/workflows/pre-commit.yaml)
[![@seungyeop's Holopin board](https://holopin.me/seungyeop)](https://holopin.io/@seungyeop)
[![fed-dev-hero](https://images.credly.com/size/220x220/images/2b1a505f-ece9-445b-b830-38ef5141b5a3/IBM-Federated-Developer-Hero.png)](https://www.credly.com/badges/406d727d-0799-4de7-88fe-b0ef2528375a/public_url)


# learning_k8s

The purpose: going through a kubernetes learning book so that whether my friends/team members can learn k8s
without huge effort to install k8s. I have installed Docker Desktop and its kubernetes to avoid exhaustion
from k8s installation/initial admin.


## Book
[The Book of Kubernetes](https://learning.oreilly.com/library/view/the-book-of/9781098141394/), Alan Hohn, 2022, No Starch Press
https://learning.oreilly.com/library/view/the-book-of/9781098141394/

## PREP

tested in Ubuntu 18.04..22.04 and macOS Ventura
Host:
- linux/amd64
- linux/arm64

Please install the items below:
* docker
* docker-compose

## How to run
```
$ export IBMCLOUD_API_KEY=<YOUR IBMCLOUD_API_KEY>
$ docker-compose run dev-env
```
