---
layout: home
meta_title: Dhall Terraform
meta_description: Dhall Terraform supports generating valid .tf.json configurations from a total functional programming language (dhall)
title: Dhall Terraform
sub_heading: Dhall Terraform supports generating valid .tf.json configurations from a total functional programming language (dhall)
eleventyNavigation:
  key: Home
  order: 1
---

## Code
`./sample.dhall`
``` dhall
let TerraformBlock =
      https://raw.githubusercontent.com/dhall-terraform/dhall-terraform-stdlib/main/lib/terraform/terraform_block.dhall

let BackendPackage =
      https://raw.githubusercontent.com/dhall-terraform/dhall-terraform-stdlib/main/lib/backends/package.dhall

let localBackend =
      BackendPackage.mkBackendResource
        (BackendPackage.BackendNames.local BackendPackage.LocalBackend.default)

let ProvidersPackage =
      https://raw.githubusercontent.com/dhall-terraform/dhall-terraform-stdlib/main/lib/providers/package.dhall

let ProviderResource = ProvidersPackage.ProviderResource

let Providers = ProvidersPackage.Providers

let mkProvider = ProvidersPackage.mkProvider

let awsProvider =
      mkProvider
        "aws"
        Providers::{ source = "hashicorp/aws", version = Some ">= 3.0.0" }

in  { terraform = TerraformBlock::{
      , required_version = Some "1.0.0"
      , required_providers = [ awsProvider ] : List ProviderResource
      , backend = Some localBackend
      }
    , providers = None (List Providers.Type)
    }
```
Then, call
``` bash
dhall-to-json <<< ./sample.dhall > main.tf.json
```
And you will now have the following:
``` json
{
  "terraform": {
    "backend": {
      "local": {}
    },
    "required_providers": {
      "aws": {
        "source": "hashicorp/aws",
        "version": ">= 3.0.0"
      }
    },
    "required_version": "1.0.0"
  }
}
```
Which, if plan, and applied, will successfully start a terraform directory for you!