1st README.md for Terraform project class aprill 2020. The A-team

This project creates vpc, 3 public and 3 private subnets in different AZs to make sure it is highly available. Project creates internet gateway, nat gateway and associates public subnets to IGW and private subnets to NAT as well.

In provisioner.tf file you can find codes for running 2 instances: 1 for hosting NagiosXI, another - for Jenkins.

In order to use this repo you need to run git clone command witj your credentials in your machine.

Make sure you remove the bucket and use your own.

Everything is all set, all codes work and necessary ports are open in security groups. The only thing you can modify module, where you can find apps.tf file. You can change region and cidr blocks for vps and subnets for your own choice.

region - specifying a region cidr_block - specifying vpc public_cidr_block1 - specifying 1st private subnet public_cidr_block2 - specifying 2nd private subnet public_cidr_block3 - specifying 3rd private subnet private_cidr_block1 - specifying 1st public subnet private_cidr_block2 - specifying 2nd public subnet private_cidr_block3 - specifying 3rd public subnet.




2nd README.md FOR PROJECT1

Terraform

Use Infrastructure as Code to provision and manage any cloud, infrastructure, or service

Nagios XI

Enterprise Server and Network Monitoring Software

Comprehensive application, service, and network monitoring in a central solution.

#create a git ignore file with terraform ingnored in it.

provider.tf Configure the AWS Provider

provider "aws" {
version = "~> 2.66"
region  = "${var.region}"
}
Provisioner.tf Provisioners can be used to model specific actions on the local machine or on a remote machine in order to prepare servers or other infrastructure objects for service.
#this part we be creating a key

resource "aws_key_pair" "deployer" {

key_name   = "deployer-key"

public_key = "${file("~/.ssh/id_rsa.pub")}"

}
#this part runs a user data from nagios.sh file in order to install prerequisites for nagios server.

user_data = "${file("nagios.sh")}"

instance_type = "t3.medium"
#the name of the project

tags = {

 Name = "NagiosProject"
#security groups

resource "aws_security_group" "allow_tls" {

name        = "allow_tls"

description = "Allow TLS inbound traffic"


ingress {

description = "TLS from VPC"

from_port   = 443

to_port     = 443

protocol    = "tcp"
 
 cidr_blocks = ["0.0.0.0/0"]
}

aws_vpc provides details about a specific VPC.
This resource can prove useful when a module accepts a vpc id as an input variable and needs to, for example, determine the CIDR block of that VPC.

resource "aws_vpc" "main" {

 cidr_block = "${var.cidr_block}"

  }

tags = {

 Name = "ProjectVPC"

}

}
3.private and public subnets

aws_subnet provides details about a specific VPC subnet.

This resource can prove useful when a module accepts a subnet id as an input variable and needs to, for example, determine the id of the VPC that the subnet belongs to.

4.networking.tf

file contains variables from private and public subnets, as well as routing table, internet gateway and private subnet association with RT.

5.centos_ami.tf this file gathers data about instance

data "aws_ami" "centos" {

most_recent = true

filter {

 name   = "name"

values = ["CentOS Linux 7 x86_64 HVM EBS *"]

}

filter {

name   = "virtualization-type"

values = ["hvm"]

}

owners = ["679593333241"] # Canonical

}

output "CENTOS_AMI_ID" {

 value = "${data.aws_ami.centos.id}"

}
user data nagios.sh contains bash script which runs while image getting created and install needed tools to run
nagios server.

#! /bin/bash
   
   sudo setenforce 0
    
sudo yum install epel-release -y
	
	sudo yum install curl -y
	
	sudo curl  https://assets.nagios.com/downloads/nagiosxi/install.sh | sh
backend.tf contains data about S3 bucket, changed it into your own bucket name.

setenv.sh file doesn’t need to be changed, it contains script which will change users env name while changing it from S3

9.configurations/regions folder contains all available AWS regions.

In order to run our code into specific region run commands:

source  setenv.sh   configurations/regions/oregon.tfvars
terraform apply -var-file regions/oregon.tfvars
