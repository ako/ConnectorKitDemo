# Building Microflow actions using the Mendix Connector Kit
## Introduction

Last year with Mendix 6.6 we introduced the connector kit. The goal of this is to enable java developers to easily add 
powerful and robust new microflow actions to their Mendix toolbox. These microflow actions can be shared in the Mendix 
AppStore so anyone can benefit from these actions without having to know java.

To illustrate the power of the connector kit, here’s a high-level design diagram for an application we recently built: 
a slack bot which enables users to determine things and people in pictures taken with slack. 

 ![Slack Rekognition Bot design][1]

The Mendix application consists of a small number of microflows that use Mendix microflow actions to connect to Slack 
and different Amazon services: S3, Rekognition and Lex.
 
 ![Slack Rekognition bot toolbox][2]
  
## Creating generic actions using parameter type

## Executing microflows
## Using import and export mappings
## Some development tips
## Java dependency management using ivy

 [1]: docs/images/slack-rekogition-bot-architecture.png
 [2]: docs/images/slack-rekogition-bot-toolkit.png