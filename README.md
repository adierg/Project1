Terraform project class aprill 2020. The A-team

This project creates vpc, 3 public and 3 private subnets in different AZs to make sure it is highly available. Project creates internet gateway, nat gateway and associates public subnets to IGW and private subnets to NAT as well.

In provisioner.tf file you can find codes for running 2 instances: 1 for hosting NagiosXI, another - for Jenkins.

In order to use this repo you need to run git clone command witj your credentials in your machine.

Make sure you remove the bucket and use your own.

Everything is all set, all codes work and necessary ports are open in security groups. The only thing you can modify module, where you can find apps.tf file. You can change region and cidr blocks for vps and subnets for your own choice.

region - specifying a region cidr_block - specifying vpc public_cidr_block1 - specifying 1st private subnet public_cidr_block2 - specifying 2nd private subnet public_cidr_block3 - specifying 3rd private subnet private_cidr_block1 - specifying 1st public subnet private_cidr_block2 - specifying 2nd public subnet private_cidr_block3 - specifying 3rd public subnet
